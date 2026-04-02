# Fase 1: O Aquecimento (Docker)

Nesta fase, o objetivo é simples: criar um "cartão de visitas" digital para a sua equipe. Uma imagem Docker que, ao ser executada, simplesmente diz "Olá, mundo!" em nome da equipe. Isso validará suas habilidades básicas com a tecnologia de contêineres que sustenta todo o universo Kubernetes.

## 1.1. O `Dockerfile`: A Receita do Bolo

Toda imagem Docker começa com um `Dockerfile`. Pense nele como uma receita que descreve, passo a passo, como montar seu ambiente.

- **`FROM`**: Toda receita precisa de uma base. Qual imagem você usará como ponto de partida? Para um simples script, imagens como `alpine`, `busybox` ou `ubuntu` são excelentes escolhas por serem leves.

- **`RUN`**: Comandos que você executa para preparar o ambiente. Instalar pacotes, criar diretórios, etc.

- **`COPY` ou `ADD`**: Como você coloca seu código (o script "hello world") para dentro da imagem?

- **`CMD` ou `ENTRYPOINT`**: O comando final. O que a imagem deve executar quando um contêiner for criado a partir dela?

**Sua Missão:** Crie um `Dockerfile` que monte uma imagem contendo um script (pode ser em Bash, Python, Node.js, etc.) que imprima uma mensagem de "hello world" da sua equipe.

## 1.2. Construindo e Batizando a Imagem

Com a receita em mãos, é hora de ir para a cozinha.

- **`docker build`**: O comando para construir a imagem a partir do seu `Dockerfile`.

  - **Dica:** Use a tag `-t` para dar um nome à sua imagem. O padrão de nomenclatura para o Docker Hub é `seu-usuario/nome-da-imagem:versao`. Por exemplo: `equipeseta/hello-world:v1`.

## 1.3. Publicando seu Trabalho

De que adianta uma obra de arte se ela não pode ser vista? É hora de publicá-la no Docker Hub para que o mundo (e eu) possa vê-la.

1.  **`docker login`**: Autentique-se no Docker Hub. Suas credenciais serão solicitadas.

2.  **`docker push`**: Envie sua imagem (já nomeada no formato correto) para o repositório.

**Critério de Sucesso:** Ao final desta fase, qualquer pessoa com acesso à internet deve ser capaz de executar `docker run seu-usuario/nome-da-imagem:versao` e ver a mensagem da sua equipe.
