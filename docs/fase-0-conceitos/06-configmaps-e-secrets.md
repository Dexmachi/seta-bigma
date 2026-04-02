# Conceito 6: ConfigMaps e Secrets

Uma boa prática de desenvolvimento de software (os "12 Fatores") diz que a **configuração** de uma aplicação deve ser estritamente separada do seu **código**. Você não deve ter URLs de bancos de dados ou chaves de API "hardcoded" na sua imagem de contêiner.

O Kubernetes fornece dois objetos principais para gerenciar esses dados externos: `ConfigMaps` e `Secrets`.

## `ConfigMap`

- **O que é?** Um objeto usado para armazenar dados de configuração não sensíveis em pares de chave-valor.

- **Para que serve?** Para desacoplar a configuração da aplicação. Você pode injetar esses valores em seus Pods em tempo de execução. Isso permite que você use a mesma imagem de contêiner em diferentes ambientes (desenvolvimento, staging, produção) simplesmente apontando-a para um `ConfigMap` diferente em cada ambiente.

- **O que armazenar?** URLs de serviços, flags de feature, variáveis de ambiente, ou até mesmo arquivos de configuração inteiros (como um `nginx.conf`).

**Exemplo de Manifesto:**
```yaml
# configmap-exemplo.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: meu-configmap
data:
  # Configuração como par chave-valor
  database.url: "jdbc:postgresql://db.example.com:5432"
  api.endpoint: "https://api.example.com/v1"

  # Configuração como um arquivo inteiro
  ui.properties: |
    color.background=blue
    font.size=14
```

## `Secret`

- **O que é?** Um objeto muito similar a um `ConfigMap`, mas projetado especificamente para armazenar **dados sensíveis**.

- **Para que serve?** Para armazenar credenciais, tokens, chaves de API, certificados TLS, etc.

- **Qual a diferença para um ConfigMap?**

    1.  **Codificação**: Os valores em um `Secret` são armazenados em `base64` por padrão. **Atenção:** Isso é codificação, **não criptografia**. Qualquer um com acesso ao Secret pode decodificar os valores facilmente. A principal razão para isso é evitar que os segredos sejam acidentalmente expostos em logs ou na tela.

    2.  **Segurança Adicional**: O Kubernetes aplica algumas medidas de segurança extras para `Secrets`. Por exemplo, eles são armazenados em `tmpfs` (um sistema de arquivos em memória) nos nós, em vez de serem escritos em disco, e não são exibidos por padrão em comandos como `kubectl describe`.

    3.  **Criptografia em Repouso**: É possível (e altamente recomendado em produção) configurar o Kubernetes para criptografar os dados dos `Secrets` no seu banco de dados etcd (criptografia em repouso).

**Exemplo de Manifesto:**
```yaml
# secret-exemplo.yaml
apiVersion: v1
kind: Secret
metadata:
  name: meu-secret
type: Opaque  # Tipo padrão para dados arbitrários
data:
  # Os valores DEVEM estar em base64
  # echo -n 'meu-usuario-super-secreto' | base64
  username: bWV1LXVzdWFyaW8tc3VwZXItc2VjcmV0bw==
  # echo -n 'minha-senha-12345' | base64
  password: bWluaGEtc2VuaGEtMTIzNDU=
```

## Como Usar ConfigMaps e Secrets em Pods

Existem duas maneiras principais de consumir esses dados dentro de um Pod:

### 1. Como Variáveis de Ambiente

Esta é a abordagem mais comum. Você pode mapear chaves de um `ConfigMap` ou `Secret` diretamente para variáveis de ambiente no seu contêiner.

```yaml
# pod-usando-config.yaml
spec:
  containers:
  - name: meu-container
    image: minha-app
    env:
      - name: DATABASE_URL
        valueFrom:
          configMapKeyRef:
            name: meu-configmap # Nome do ConfigMap
            key: database.url  # Chave a ser usada
      - name: API_PASSWORD
        valueFrom:
          secretKeyRef:
            name: meu-secret     # Nome do Secret
            key: password      # Chave a ser usada
```

### 2. Como Arquivos em um Volume

Você pode montar um `ConfigMap` ou `Secret` inteiro como um volume. Cada chave no objeto se torna um arquivo no diretório montado. Isso é ideal para injetar arquivos de configuração completos.

```yaml
# pod-montando-config.yaml
spec:
  containers:
  - name: meu-container
    image: minha-app
    volumeMounts:
    - name: config-volume
      mountPath: /etc/config
  volumes:
  - name: config-volume
    configMap:
      name: meu-configmap
```

Neste exemplo, um arquivo chamado `database.url` e outro chamado `ui.properties` apareceriam dentro do diretório `/etc/config` no contêiner.
