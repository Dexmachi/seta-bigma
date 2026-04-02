# Fase 3: O Cofre (Deploy & Storage)

Com o cluster de pé, é hora da missão principal: implantar o **Vaultwarden**, um cofre de senhas de código aberto, e garantir que ele seja robusto e resiliente. Isso exigirá a criação de três dos mais importantes objetos do Kubernetes.

## 3.1. O Manifesto de `Deployment`

Um `Deployment` é a sua garantia de que a aplicação estará sempre rodando. Ele diz ao Kubernetes qual imagem de contêiner usar e quantas réplicas dela manter em execução. Se um Pod falhar, o `Deployment` cria outro para substituí-lo.

**Sua Missão:**

- Crie um arquivo `vaultwarden-deployment.yaml`.

- **`spec.template.spec.containers`**: É aqui que você define a imagem a ser usada. A imagem oficial é `vaultwarden/server:latest`.

- **Variáveis de Ambiente**: O Vaultwarden é configurado principalmente por variáveis de ambiente. Uma crucial para começar é a `ADMIN_TOKEN`, que lhe dará acesso à interface de administração. Pesquise na documentação do Vaultwarden por outras variáveis que possam ser úteis.

- **Portas**: Você precisa expor a porta em que a aplicação roda dentro do contêiner (a documentação da imagem dirá qual é).

## 3.2. O Manifesto de `Service`

Um `Deployment` executa os Pods, mas como você acessa eles? Um `Service` expõe um conjunto de Pods (geralmente definidos por um `selector`) como um serviço de rede.

**Sua Missão:**

- Crie um arquivo `vaultwarden-service.yaml`.

- **`spec.type`**: Que tipo de serviço você usará?

    - `ClusterIP`: Expõe o serviço apenas dentro do cluster.

    - `NodePort`: Expõe o serviço em uma porta estática em cada nó do cluster. Isso torna a aplicação acessível de fora do cluster.

    - `LoadBalancer`: Cria um balanceador de carga externo (geralmente em provedores de nuvem).

- Para este desafio, `NodePort` é uma excelente escolha para expor seu serviço externamente de forma simples.

- **`spec.selector`**: Como o `Service` sabe quais Pods pertencem a ele? A combinação de `labels` no `Deployment` e `selectors` no `Service` é a chave.

## 3.3. A Persistência com `PersistentVolumeClaim` (PVC)

Se o Pod do Vaultwarden for reiniciado, todos os dados (senhas, usuários) serão perdidos! Para evitar essa catástrofe, você precisa de armazenamento persistente. No Kubernetes, você *solicita* armazenamento através de um `PersistentVolumeClaim` (PVC).

**Sua Missão:**

- Crie um arquivo `vaultwarden-pvc.yaml`.

- **`spec.accessModes`**: Como o volume deve ser montado? `ReadWriteOnce` (pode ser montado como leitura/escrita por um único nó) é o que você provavelmente quer.

- **`spec.resources.requests.storage`**: Quanto espaço você precisa? Comece com algo razoável, como `1Gi`.

- **Montando o Volume**: Após criar o PVC, você precisa dizer ao seu `Deployment` para usá-lo. Isso é feito em duas etapas no manifesto do `Deployment`:

    1.  Definir o volume na seção `spec.template.spec.volumes`, apontando para o seu PVC.

    2.  Montar esse volume em um caminho específico dentro do contêiner usando `spec.template.spec.containers.volumeMounts`. A documentação do Vaultwarden indicará qual diretório (`/data`) precisa ser persistido.

**Dica de Ouro:** O Kind vem com uma `StorageClass` padrão que provisiona `PersistentVolumes` dinamicamente a partir de diretórios no nó do cluster. Isso significa que você só precisa criar o PVC, e o Kubernetes (via Kind) cuidará de criar o `PersistentVolume` correspondente para você.
