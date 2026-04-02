# Conceito 7: Namespaces

À medida que um cluster Kubernetes cresce, com mais equipes e projetos compartilhando os mesmos recursos, a organização se torna um desafio. Como evitar que a Equipe A delete acidentalmente um `Service` da Equipe B porque ambos tinham o mesmo nome?

A solução é o **Namespace**.

## O que é um Namespace?

- **Escopo para Nomes**: Um `Namespace` fornece um escopo para os nomes dos recursos. O nome de um recurso precisa ser único *dentro de um namespace*, mas não entre namespaces diferentes. Isso significa que você pode ter um `Service` chamado `meu-banco-de-dados` no namespace `equipe-a` e outro `Service` com o mesmo nome no namespace `equipe-b`.

- **Cluster Virtual**: Você pode pensar em um `Namespace` como um "cluster virtual" dentro do seu cluster físico. Ele permite particionar o cluster em subgrupos.

- **Controle de Acesso e Recursos**: Namespaces são um pilar fundamental para a administração de clusters multi-usuário. Eles permitem:

    - **RBAC (Role-Based Access Control)**: Definir permissões de forma que um usuário só possa ver e modificar os recursos em seu próprio namespace.

    - **Resource Quotas**: Limitar a quantidade de recursos (CPU, memória, armazenamento) que um namespace pode consumir no total.

## Namespaces Padrão

Quando você cria um cluster Kubernetes, ele vem com alguns namespaces pré-criados:

- **`default`**: O namespace onde seus recursos são criados se você não especificar um. Para projetos pequenos ou de desenvolvimento, muitas pessoas trabalham aqui, mas em ambientes maiores, seu uso é desencorajado.

- **`kube-system`**: Onde os componentes do próprio Kubernetes (o cérebro do cluster) são executados, como o `etcd`, o `scheduler`, e o `api-server`. Não toque em nada aqui, a menos que você saiba exatamente o que está fazendo.

- **`kube-public`**: Um namespace especial que é legível por todos os usuários (incluindo os não autenticados). Geralmente usado para expor informações do cluster que precisam ser públicas.

- **`kube-node-lease`**: Contém objetos de lease que ajudam a determinar a saúde dos nós.

## Como Trabalhar com Namespaces

**1. Criando um Namespace:**

É um objeto simples.
```yaml
# namespace-equipe-a.yaml
apiVersion: v1
kind: Namespace
metadata:
  name: equipe-a
```
E então: `kubectl apply -f namespace-equipe-a.yaml`

**2. Criando Recursos em um Namespace:**

Você usa o campo `metadata.namespace` no seu manifesto.

```yaml
# pod-no-namespace.yaml
apiVersion: v1
kind: Pod
metadata:
  name: meu-pod
  namespace: equipe-a # <-- AQUI!
spec:
  ...
```

**3. Visualizando Recursos em um Namespace:**

A maioria dos comandos `kubectl` opera no namespace `default` por padrão. Para especificar outro, use a flag `--namespace` (ou `-n`).

- `kubectl get pods --namespace equipe-a`

- `kubectl get pods -n kube-system`

**4. Configurando seu Contexto para um Namespace:**

É tedioso digitar `-n` toda vez. Você pode alterar o namespace padrão para o seu contexto atual:

`kubectl config set-context --current --namespace=equipe-a`

Agora, todos os seus comandos `kubectl` serão executados no namespace `equipe-a` por padrão. Para voltar:

`kubectl config set-context --current --namespace=default`

## Comunicação Entre Namespaces

Um Pod no `namespace-a` pode se comunicar com um `Service` no `namespace-b`? Sim!

O DNS interno do Kubernetes cria registros para os serviços no formato:
`<nome-do-servico>.<nome-do-namespace>.svc.cluster.local`

Então, se um Pod no namespace `equipe-a` quer se conectar ao `Service` `meu-banco-de-dados` no namespace `equipe-b`, ele pode usar o endereço completo:
`meu-banco-de-dados.equipe-b`

Se ambos estivessem no mesmo namespace, apenas `meu-banco-de-dados` seria suficiente.
