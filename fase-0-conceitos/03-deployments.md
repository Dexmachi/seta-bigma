# Conceito 3: Deployments

Você raramente criará Pods diretamente no Kubernetes. Por quê? Porque um Pod, sozinho, é frágil. Se o nó em que ele está rodando falhar, ou se o processo do contêiner travar, o Pod simplesmente morre e não volta.

É aqui que entra o **Deployment**. Ele é um controlador de alto nível que gerencia o ciclo de vida dos Pods.

## O que um Deployment faz?

Um `Deployment` permite que você descreva o **estado desejado** para sua aplicação. Você diz ao Deployment:

- "Eu quero que **3 réplicas** de um Pod, usando a imagem `nginx:1.21`, estejam sempre rodando no meu cluster."

O Deployment então trabalha constantemente para garantir que a realidade (o estado atual) corresponda ao seu desejo (o estado desejado).

- **Autocorreção (Self-healing)**: Se um Pod gerenciado por um Deployment falha ou é deletado, o Deployment detecta a ausência e cria um novo Pod para substituí-lo.

- **Escalabilidade**: Se você decidir que precisa de 5 réplicas em vez de 3, basta atualizar o campo `replicas` no manifesto do Deployment, e ele criará mais 2 Pods para você. Se você reduzir para 1, ele encerrará os Pods extras.

- **Atualizações de Aplicação**: Deployments gerenciam a atualização da sua aplicação de forma controlada, sem tempo de inatividade.

    - **Rolling Update (Padrão)**: O Deployment substitui gradualmente os Pods antigos (com a versão 1 da sua imagem) por Pods novos (com a versão 2). Ele só continua a atualização se os novos Pods iniciarem com sucesso.

    - **Recreate**: Todos os Pods antigos são encerrados de uma vez antes que os novos sejam criados. Isso causa um breve tempo de inatividade.

## Como funciona? (ReplicaSets)

O `Deployment` não monitora os Pods diretamente. Ele delega essa tarefa a outro objeto chamado **ReplicaSet**.

O fluxo é: `Deployment` -> `ReplicaSet` -> `Pods`

- Você cria um `Deployment`.

- O `Deployment` cria um `ReplicaSet` com base no seu `spec.template` (o template do Pod).

- O `ReplicaSet` é responsável por manter o número correto de `Pods` em execução.

Quando você atualiza a imagem no seu `Deployment`, ele cria um *novo* `ReplicaSet` com a nova versão e gradualmente aumenta a escala do novo ReplicaSet enquanto diminui a do antigo. Isso permite atualizações seguras e a capacidade de reverter para a versão anterior, se necessário.

## Manifesto YAML Básico de um Deployment

```yaml
# deployment-exemplo.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: meu-deployment-nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx-server
  template:
    metadata:
      labels:
        app: nginx-server
    spec:
      containers:
      - name: nginx
        image: nginx:1.21
        ports:
        - containerPort: 80
```

**Analisando o manifesto:**

- **`apiVersion: apps/v1`**: Deployments vivem na API `apps/v1`.

- **`spec.replicas: 3`**: O estado desejado. Queremos 3 cópias do nosso Pod.

- **`spec.selector.matchLabels`**: O seletor do Deployment. Ele precisa saber quais Pods ele deve gerenciar. Ele vai "adotar" todos os Pods que correspondam a esta label.

- **`spec.template`**: Este é o "molde" para os Pods que o Deployment criará.

    - **`metadata.labels`**: As labels que serão aplicadas a cada Pod criado. **Crucial:** Estas labels devem corresponder ao `spec.selector.matchLabels` do Deployment! É assim que ele encontra seus próprios filhos.

    - **`spec`**: A especificação do contêiner, exatamente como vimos no manifesto do Pod.

Em resumo: **Use sempre um `Deployment` (ou outro controlador como um `StatefulSet` ou `DaemonSet`) para gerenciar seus Pods.** Nunca crie Pods órfãos.
