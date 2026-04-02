# Conceito 5: Volumes e Persistência (PV, PVC, StorageClass)

Contêineres são projetados para serem efêmeros. Se um Pod que executa um banco de dados for reiniciado, todos os dados dentro do contêiner serão perdidos. Para aplicações que precisam manter o estado (quase todas as aplicações reais), o Kubernetes fornece um sistema de **Volumes**.

## Volumes

Um `Volume` é, em essência, um diretório que é montado em um ou mais contêineres de um Pod. A principal característica de um `Volume` é que seu ciclo de vida é independente do contêiner. Se o contêiner for reiniciado, o `Volume` permanece, e os dados são preservados.

Existem muitos tipos de volumes (`emptyDir`, `hostPath`, `nfs`, etc.), mas para persistência de dados real, usamos um sistema de abstração mais poderoso.

## A Abstração da Persistência: PV, PVC e StorageClass

O Kubernetes lida com armazenamento da mesma forma que lida com computação: de forma abstrata e desacoplada da infraestrutura.

### `PersistentVolume` (PV)

- **O que é?** Uma "fatia" de armazenamento no cluster que foi provisionada por um administrador.

- **Analogia:** Pense em um `PersistentVolume` como um "disco rígido" que foi plugado no cluster. Ele é um recurso do cluster, assim como um nó. Ele tem um tamanho, um tipo (ex: "disco rápido SSD", "disco lento magnético") e um modo de acesso.

- **Quem gerencia?** Geralmente, o administrador do cluster. Os desenvolvedores não interagem com os PVs diretamente.

### `PersistentVolumeClaim` (PVC)

- **O que é?** Uma **solicitação** de armazenamento feita por um usuário/desenvolvedor.

- **Analogia:** Se o PV é o disco rígido, a `PersistentVolumeClaim` (Reivindicação de Volume Persistente) é o "pedido de compra" que o desenvolvedor faz. "Eu preciso de 10GB de armazenamento que seja rápido e que eu possa ler e escrever".

- **Como funciona?** O Kubernetes tenta satisfazer o PVC encontrando um PV compatível que atenda aos requisitos da solicitação (tamanho, modo de acesso, etc.). Se encontrar um, ele "liga" (binds) o PVC ao PV. A partir desse momento, aquele PV pertence exclusivamente àquele PVC.

O Pod, então, não monta o PV diretamente. Ele monta o **PVC**.

**Fluxo:** `Pod` -> usa um -> `PVC` -> que está ligado a um -> `PV` -> que representa o armazenamento físico.

Isso é poderoso porque desacopla a aplicação (Pod) da infraestrutura de armazenamento (PV). O desenvolvedor só precisa saber o que ele quer (o PVC), não como ou onde o armazenamento é realmente fornecido.

### `StorageClass`

- **O que é?** Um objeto que descreve os "tipos" de armazenamento disponíveis. Define o que significa "disco rápido" ou "disco lento".

- **Provisionamento Dinâmico:** É aqui que a mágica acontece. Em vez de um administrador ter que criar `PersistentVolumes` manualmente, uma `StorageClass` pode ser configurada para **provisionar dinamicamente** um PV sempre que um PVC a solicita.

- **Como funciona?**

    1. O desenvolvedor cria um PVC que especifica o nome de uma `StorageClass`.

    2. A `StorageClass` contém as instruções para o provedor de armazenamento (ex: AWS EBS, GCE Persistent Disk, ou no caso do Kind, um diretório local) criar um novo volume físico.

    3. Um novo `PersistentVolume` é criado automaticamente para representar esse volume físico.

    4. O PVC é ligado a esse novo PV.

O Kind vem com uma `StorageClass` padrão que faz exatamente isso, usando diretórios no seu computador como armazenamento físico. É por isso que no nosso desafio, você só precisa criar o PVC e todo o resto acontece automaticamente!

## Exemplo de Manifesto de PVC

```yaml
# pvc-exemplo.yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: meu-pvc-para-dados
spec:
  # storageClassName: standard  # Opcional se houver uma SC padrão
  accessModes:
    - ReadWriteOnce  # Pode ser montado como R/W por um único nó
  resources:
    requests:
      storage: 5Gi  # Solicitação de 5 Gigabytes de armazenamento
```

Depois de criado, você pode referenciar `meu-pvc-para-dados` no manifesto do seu `Deployment` para montar este volume persistente dentro do seu Pod.
