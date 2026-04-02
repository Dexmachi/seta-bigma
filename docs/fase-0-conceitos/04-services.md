# Conceito 4: Services (ClusterIP, NodePort, LoadBalancer)

Um `Deployment` garante que seus Pods estejam rodando, mas surge um problema: os Pods são efêmeros. Eles podem ser destruídos, recriados, e seus endereços IP internos mudam constantemente. Como outros Pods (ou o mundo externo) podem se conectar a eles de forma confiável?

A resposta é o **Service**. Um `Service` é um objeto do Kubernetes que fornece um ponto de entrada de rede estável para um conjunto de Pods.

## Como um Service funciona?

1.  Um `Service` recebe um endereço IP virtual estável (chamado de `ClusterIP`) que não muda.

2.  Ele usa um **seletor de labels** para encontrar continuamente todos os Pods que deve abranger.

3.  Quando o tráfego chega ao IP do `Service`, ele o encaminha e balanceia a carga para um dos Pods saudáveis que correspondem ao seletor.

Isso significa que outras partes da sua aplicação só precisam saber o nome do `Service`, que é resolvido para o `ClusterIP` estável pelo DNS interno do Kubernetes. Eles não precisam se preocupar com os IPs dos Pods individuais.

## Tipos de Service

O tipo de um `Service` determina como ele é exposto. Os três tipos principais são definidos no campo `spec.type`.

### 1. `ClusterIP` (Padrão)

- **O que faz?**: Expõe o `Service` em um endereço IP interno, acessível **apenas de dentro do cluster**.

- **Quando usar?**: Este é o tipo padrão e o mais comum. Use-o para comunicação entre serviços internos (ex: seu frontend precisa se comunicar com seu backend). Você não quer expor seu banco de dados diretamente para a internet, então você usaria um `ClusterIP` para ele.

**Exemplo YAML:**
```yaml
apiVersion: v1
kind: Service
metadata:
  name: meu-servico-interno
spec:
  type: ClusterIP # Este é o padrão, então esta linha é opcional
  selector:
    app: meu-backend
  ports:
  - protocol: TCP
    port: 80       # A porta em que o Service escuta
    targetPort: 8080 # A porta para a qual o tráfego é enviado nos Pods
```

### 2. `NodePort`

- **O que faz?**: Além de fazer tudo que um `ClusterIP` faz, ele também expõe o `Service` em uma porta estática (a `NodePort`) em **cada nó** do cluster.

- **Como funciona?**: O Kubernetes aloca uma porta de um intervalo padrão (geralmente 30000-32767). O tráfego para `[IP de qualquer nó]:[NodePort]` é então encaminhado para o `Service` e, finalmente, para os Pods.

- **Quando usar?**: Para expor rapidamente um serviço para o mundo exterior durante o desenvolvimento ou para aplicações que não precisam de um balanceador de carga sofisticado. É perfeito para o nosso desafio com o Kind.

**Exemplo YAML:**
```yaml
apiVersion: v1
kind: Service
metadata:
  name: meu-servico-externo
spec:
  type: NodePort
  selector:
    app: meu-frontend
  ports:
  - protocol: TCP
    port: 80
    targetPort: 8080
    # nodePort: 30080 # Opcional: Você pode especificar uma porta ou deixar o K8s escolher uma.
```

### 3. `LoadBalancer`

- **O que faz?**: Além de fazer tudo que um `NodePort` faz, ele também solicita a criação de um **balanceador de carga externo** do provedor de nuvem (AWS, GCP, Azure, etc.) e o configura para enviar tráfego para o `NodePort` do `Service` em todos os nós.

- **Como funciona?**: Este tipo de `Service` é dependente da infraestrutura subjacente. Se você estiver rodando em um provedor de nuvem, ele provisionará um Classic Load Balancer (AWS), Network Load Balancer (GCP), etc.

- **Quando usar?**: Este é o método padrão para expor um serviço à internet em um ambiente de produção na nuvem. Ele fornece um único endereço IP externo e estável.

- **E no Kind?** O Kind não tem um provedor de nuvem, mas alguns controladores (como o MetalLB) podem ser instalados para simular essa funcionalidade em ambientes locais. Sem eles, um `Service` do tipo `LoadBalancer` permanecerá com o status "pending".

**Exemplo YAML:**
```yaml
apiVersion: v1
kind: Service
metadata:
  name: meu-servico-de-producao
spec:
  type: LoadBalancer
  selector:
    app: minha-api
  ports:
  - protocol: TCP
    port: 80
    targetPort: 9000
```
