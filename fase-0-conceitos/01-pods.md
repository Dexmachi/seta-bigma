# Conceito 1: Pods

O **Pod** é a menor unidade de computação que você pode criar e gerenciar no Kubernetes. Ele é o "tijolo" fundamental de qualquer aplicação.

## O que é um Pod?

Pense em um Pod como um invólucro para um ou mais contêineres (geralmente Docker). Um Pod representa uma única instância da sua aplicação.

- **Unidade Atômica**: Todos os contêineres dentro de um mesmo Pod são agendados juntos no mesmo nó (máquina física ou virtual) do cluster.

- **Recursos Compartilhados**: Os contêineres em um Pod compartilham o mesmo ambiente de execução:

    - **Rede**: Eles podem se comunicar uns com os outros usando `localhost`.

    - **Volumes de Armazenamento**: Eles podem compartilhar dados através de volumes montados.

- **Ciclo de Vida Acoplado**: Quando o Pod é iniciado, todos os seus contêineres são iniciados. Quando ele é encerrado, todos são encerrados.

## Por que não usar contêineres diretamente?

O Kubernetes adiciona essa camada de abstração (o Pod) para fornecer um ambiente mais rico e gerenciável. Enquanto 90% das vezes você terá um único contêiner por Pod, o modelo permite padrões avançados, como o "sidecar", onde um contêiner auxiliar (ex: para logging ou proxy) é executado ao lado do contêiner principal da aplicação.

## Manifesto YAML Básico de um Pod

Embora na prática você raramente crie Pods diretamente (você usará `Deployments`, que veremos a seguir), é importante entender sua estrutura.

```yaml
# pod-exemplo.yaml
apiVersion: v1
kind: Pod
metadata:
  name: meu-primeiro-pod
  labels:
    app: webserver
spec:
  containers:
  - name: nginx-container
    image: nginx:1.21
    ports:
    - containerPort: 80
```

**Analisando o manifesto:**

- **`apiVersion: v1`**: Define a versão da API do Kubernetes para este objeto. Pods "core" estão na `v1`.

- **`kind: Pod`**: O tipo de objeto que estamos criando.

- **`metadata`**: Dados sobre o objeto, como seu `name` (nome) e `labels` (etiquetas).

- **`spec`**: A especificação do estado desejado. É aqui que a mágica acontece.

- **`spec.containers`**: Uma lista de um ou mais contêineres que viverão dentro deste Pod.

    - **`name`**: Um nome para o contêiner.

    - **`image`**: A imagem de contêiner a ser usada.

    - **`ports.containerPort`**: A porta que o contêiner expõe. **Importante:** Isso é apenas informativo para o Kubernetes; não expõe a porta para fora do Pod.

Para criar este Pod, você usaria o comando:
`kubectl apply -f pod-exemplo.yaml`

E para ver se ele está rodando:
`kubectl get pods`
