# Conceito 8: Ingress

Se um `Service` do tipo `NodePort` ou `LoadBalancer` é como abrir uma porta específica do seu prédio para um apartamento, o **Ingress** é a recepção inteligente e o porteiro do seu cluster. Ele opera na camada 7 (HTTP/S) e permite gerenciar o tráfego externo de forma muito mais sofisticada.

## O que é um Ingress?

Um `Ingress` é um objeto do Kubernetes que define regras para rotear o tráfego HTTP e HTTPS de fora do cluster para os `Services` dentro do cluster.

Por si só, criar um recurso `Ingress` não faz nada. Você precisa de um **Ingress Controller**, que é um Pod especializado que "escuta" os recursos `Ingress` e implementa as regras de roteamento. Pense no Ingress como o "plano de roteamento" e no Ingress Controller como o "roteador" que executa o plano.

Exemplos populares de Ingress Controllers são NGINX, Traefik e HAProxy.

## Por que usar um Ingress em vez de um Service?

`Services` do tipo `NodePort` e `LoadBalancer` operam na camada 4 (TCP/UDP). Eles são ótimos para expor um único serviço, mas se tornam limitados e caros à medida que o número de serviços aumenta.

Imagine que você tem 10 serviços no seu cluster e quer expô-los publicamente. Com `LoadBalancer`, você precisaria de 10 balanceadores de carga, um para cada serviço, o que pode custar caro.

Com um Ingress, você precisa de apenas **um** ponto de entrada (um único Load Balancer para o seu Ingress Controller) e pode usar regras para direcionar o tráfego para dezenas de serviços diferentes.

### Vantagens do Ingress:

-   **Roteamento Baseado em Host**: Direcionar `app1.exemplo.com` para o `Service` da App1 e `app2.exemplo.com` para o `Service` da App2.

-   **Roteamento Baseado em Caminho (Path)**: Direcionar `exemplo.com/ui` para o `Service` do frontend e `exemplo.com/api` para o `Service` do backend.

-   **Terminação TLS/SSL**: O Ingress Controller pode cuidar de toda a complexidade do HTTPS. Ele "termina" a conexão criptografada e encaminha o tráfego em HTTP simples para os seus Pods, que não precisam se preocupar com certificados. Isso centraliza o gerenciamento de certificados em um único lugar.

-   **Ponto de Entrada Único**: Simplifica a exposição de múltiplos serviços.

## Manifesto YAML Básico de um Ingress

Este exemplo mostra como rotear o tráfego baseado no host para dois serviços diferentes.

```yaml
# ingress-exemplo.yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: meu-ingress
  annotations:
    # Anotações específicas do Ingress Controller são usadas para configuração
    # Ex: nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - host: "cofre.minhaequipe.com"
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: vaultwarden-service # Nome do seu Service do Vaultwarden
            port:
              number: 80
  - host: "outra-app.minhaequipe.com"
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: outro-service # Nome de outro Service
            port:
              number: 8080
```

**Analisando o manifesto:**

-   **`apiVersion: networking.k8s.io/v1`**: A API para Ingress.

-   **`metadata.annotations`**: Essencial para Ingress. É aqui que você passa configurações específicas para o controller, como reescrita de URL, limites de taxa, etc.

-   **`spec.rules`**: Uma lista de regras de roteamento.

    -   **`host`**: O nome de domínio ao qual a regra se aplica.

    -   **`http.paths`**: Define os caminhos e para qual `backend` (serviço) o tráfego deve ir.

    -   **`backend.service`**: Aponta para o `Service` e a `port` que receberão o tráfego.

Em resumo, enquanto um `Service` expõe seus Pods na rede, um `Ingress` atua como o controlador de tráfego aéreo para seus serviços, decidindo quem vai para onde com base em regras HTTP claras. É a maneira padrão de expor aplicações web no Kubernetes.
