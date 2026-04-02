# Fase 2: A Fundação (Kubernetes com Kind)

Com sua imagem Docker orgulhosamente publicada, é hora de construir o campo de batalha. O Kubernetes é complexo, mas a ferramenta **Kind (Kubernetes in Docker)** permite criar um cluster funcional em minutos, rodando dentro de contêineres Docker na sua própria VM.

## 2.1. Instalando o Kind

Se o Kind ainda não estiver disponível na sua VM, você precisará instalá-lo. A documentação oficial do Kind é sua melhor amiga. Pesquise por "install kind" e siga as instruções para o seu ambiente Linux.

## 2.2. Criando o Cluster Básico

A maneira mais simples de subir um cluster é com um único comando:

```bash
kind create cluster
```

Este comando cria um cluster com o nome padrão `kind` e um único nó que funciona como *control-plane* e *worker* (ignorem essa nomenclatura, pra vocês que são iniciantes, ela não importa). Para esta operação, é mais do que suficiente.

**Desafio:** Após executar o comando, como você pode verificar se o cluster está realmente no ar e se seus "nós" (contêineres Docker) estão rodando? Que comandos `docker` e `kubectl` você usaria?

## 2.3. O Kubeconfig e o Acesso Remoto (Objetivo Crítico)

Um dos seus objetivos é entregar um arquivo `kubeconfig` que permita acesso ao cluster de *fora* da VM. Por padrão, o Kind configura o `kubeconfig` para ser acessado apenas de `localhost` (dentro da VM). Isso não é suficiente para nós.

Você precisará instruir o Kind a criar um cluster que possa ser acessado pela rede.

**Sua Missão:**

1.  Investigue como criar um cluster Kind usando um **arquivo de configuração YAML**.

2.  Dentro desse arquivo de configuração, qual parâmetro permite controlar o endereço em que a API do Kubernetes (o `apiServer`) escuta as conexões?

3.  **Dica:** O endereço `0.0.0.0` é um endereço especial em redes que significa "escutar em todas as interfaces de rede disponíveis".

4.  Você precisará deletar o cluster anterior (`kind delete cluster`) e recriá-lo com o arquivo de configuração.

O comando para criar um cluster com um arquivo de configuração se parece com:

```bash
kind create cluster --config seu-arquivo-de-config.yaml
```

**Critério de Sucesso:** O arquivo `~/.kube/config` gerado na VM, após uma pequena modificação no endereço do servidor (trocando `0.0.0.0` pelo IP da VM), deve ser capaz de gerenciar o cluster a partir de outra máquina na mesma rede.
