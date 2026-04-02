# Conceito 2: Labels e Selectors

Se os Pods, Deployments e Services são os atores no palco do Kubernetes, **Labels (Etiquetas)** e **Selectors (Seletores)** são o roteiro que diz a eles como interagir. Eles são o mecanismo de "cola" que conecta os diferentes componentes.

## Labels (Etiquetas)

- **O que são?** Pares de chave/valor (ex: `app: frontend`) que você anexa aos seus objetos do Kubernetes (Pods, Services, etc.).

- **Para que servem?** Para organizar e identificar recursos de forma flexível. Um objeto pode ter múltiplas labels.

- **São arbitrários?** Sim. Você pode usar as chaves e valores que fizerem sentido para sua organização. Boas práticas comuns incluem:

    - `app`: O nome da aplicação (ex: `app: vaultwarden`).

    - `tier`: A camada da aplicação (ex: `tier: backend`, `tier: database`).

    - `env`: O ambiente (ex: `env: production`, `env: staging`).

**Exemplo em um manifesto de Pod:**

```yaml
# pod-com-labels.yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-com-labels
  labels:
    app: meu-app-web
    tier: frontend
    env: development
spec:
  containers:
  - name: nginx-container
    image: nginx
```

Este Pod agora está "marcado" com três etiquetas que descrevem seu papel no sistema.

## Selectors (Seletores)

- **O que são?** Uma forma de consultar e selecionar objetos do Kubernetes com base em suas labels.
- **Para que servem?** Para criar a conexão lógica entre os objetos. Por exemplo, um `Service` precisa saber quais `Pods` ele deve gerenciar e para quais ele deve enviar tráfego. Ele faz isso usando um seletor.

Existem dois tipos principais de seletores, mas o mais comum é o de **igualdade**:

`selector:`
  `app: meu-app-web`
  `tier: frontend`

Este seletor irá corresponder a **todos** os objetos que tenham **ambas** as labels `app: meu-app-web` E `tier: frontend`.

## A Conexão na Prática

Vamos ver como um `Service` usa um seletor para encontrar Pods.

**O Pod (com sua label):**
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-1
  labels:
    app: meu-servico-backend # <-- A LABEL!
spec:
  containers:
  - name: app-container
    image: minha-app
```

**O Service (com seu seletor):**
```yaml
apiVersion: v1
kind: Service
metadata:
  name: meu-servico-de-backend
spec:
  selector:
    app: meu-servico-backend # <-- O SELECTOR!
  ports:
  - protocol: TCP
    port: 80
    targetPort: 8080
```

**A Mágica:** O `Service` chamado `meu-servico-de-backend` usará seu seletor para procurar por todos os Pods que tenham a etiqueta `app: meu-servico-backend`. Ele encontrará o `pod-1` e automaticamente começará a enviar tráfego de rede para a porta `8080` dele.

Se você criar um `pod-2` com a mesma label, o Service o encontrará e começará a balancear a carga entre os dois Pods. Se você remover a label de um Pod, o Service deixará de enviar tráfego para ele. É um sistema dinâmico e desacoplado, e é um dos conceitos mais poderosos do Kubernetes.
