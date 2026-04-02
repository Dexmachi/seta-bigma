# 🌩️ Operação Kubernetes: Equipe Seta vs Equipe Bigma

Aqui quem fala é o líder da operação, Dexmachina. Bem-vindos ao campo de batalha.

O objetivo de vocês nesta tarde é ir do absoluto zero no Kubernetes (*"from zero to hero"*) até colocar um cofre de senhas funcional e resiliente de pé. Vocês foram divididos em duas equipes. O conhecimento de uma equipe completa a outra, mas no fim do dia, apenas uma sairá inteira.

**Lembrete de acesso:** Nas VMs disponibilizadas, garantam que estão operando com as permissões corretas utilizando `ssh lappis@<seu_ip>` e `su -`.

---

## 📚 Fase 0: Conceitos Fundamentais (Leitura Recomendada)

Antes de mergulhar na prática, é crucial entender os blocos de construção do Kubernetes. Esta seção serve como seu glossário e base de conhecimento. (NÃO LEIA ISSO SE VOCÊ NÃO PRECISAR, USE APENAS QUANDO FOR NECESSÁRIO DURANTE AS FASES PRÁTICAS)

*   [**01 - Pods**](./fase-0-conceitos/01-pods.md): A menor unidade de computação do Kubernetes.

*   [**02 - Labels e Selectors**](./fase-0-conceitos/02-labels-e-selectors.md): O sistema de "etiquetas" que conecta tudo.

*   [**03 - Deployments**](./fase-0-conceitos/03-deployments.md): Como garantir que sua aplicação esteja sempre rodando.

*   [**04 - Services**](./fase-0-conceitos/04-services.md): Como expor sua aplicação para o mundo e outros serviços.

*   [**05 - Volumes e PVCs**](./fase-0-conceitos/05-volumes-e-pvc.md): Como garantir que seus dados não desapareçam.

*   [**06 - ConfigMaps e Secrets**](./fase-0-conceitos/06-configmaps-e-secrets.md): Como gerenciar configurações e segredos.

*   [**07 - Namespaces**](./fase-0-conceitos/07-namespaces.md): Como organizar o cluster em "fatias" virtuais.

*   [**08 - Ingress**](./fase-0-conceitos/08-ingress.md): O porteiro inteligente para seus serviços.

---

## 🎯 Objetivos da Missão (O Caminho)

Sua jornada é dividida em etapas. Não tentem pular os degraus. A documentação detalhada para cada fase guiará vocês pela escuridão.

### Fase 1: O Aquecimento (Docker)
*   [Guia da Fase 1](./fase-1-docker/01-docker.md): Criar uma imagem Docker básica rodando uma aplicação absurdamente simples (um script de *"hello world"* em qualquer linguagem serve).
*   Fazer o push dessa imagem para um repositório público no **Docker Hub**. *(Dica: Usem qualquer ferramenta que preferirem: Docker CLI, Buildah, etc).*

### Fase 2: A Fundação (Kubernetes)
*   [Guia da Fase 2](./fase-2-fundacao/01-cluster-kind.md): Subir um cluster Kubernetes local dentro da VM utilizando a ferramenta **Kind** (E APENAS KIND).

### Fase 3: O Cofre (Deploy & Storage)
*   [Guia da Fase 3](./fase-3-cofre/01-manifestos.md): Criar um manifesto de `Deployment` e um de `Service` para subir uma instância do **Vaultwarden**.
    *   *Aviso de sanidade:* Usem a imagem oficial do Vaultwarden no Docker Hub. Pelo amor de Deus, não tentem buildar o Vaultwarden do zero no cluster, ou a VM não vai aguentar.
*   Configurar um **PVC (Persistent Volume Claim)** atrelado ao Deployment. Se o Pod for deletado pelo K9s, as senhas não podem sumir no limbo.

### Fase 4: A Glória (Features Extras)
*   [Guia da Fase 4](./fase-4-gloria/01-smtp-tls.md): Adicionar features relevantes ao Vaultwarden para torná-lo pronto para produção (ex: configurar um servidor SMTP para envio de e-mails, TLS/HTTPS, etc).

---

## ✅ Definition of Done (Critérios de Aceite)

A missão só acaba quando os seguintes artefatos forem entregues e validados por mim:

*   [ ] A imagem do *"hello world"* com o nome da sua equipe está pública e acessível no Docker Hub.
*   [ ] O cluster Kubernetes está rodando e saudável localmente via Kind.
*   [ ] Um arquivo **kubeconfig** válido foi entregue como relatório da missão. *Atenção técnica:* Este arquivo deve permitir o acesso ao cluster de fora da VM (lembrem-se de investigar como expor a API do Kind para a rede local).
*   [ ] O Vaultwarden está rodando, com os dados persistidos (PVC), e acessível externamente via um `Service` (NodePort ou LoadBalancer).

---

## 🏆 Regras de Engajamento e Pontuação

O tempo está correndo e a guerra de pontos define o destino de vocês:

1.  **Velocidade de Execução:** A primeira equipe a bater todos os critérios do DOD recebe **5 pontos**. A segunda recebe **3 pontos**.
2.  **Features Adicionais:** A cada feature extra funcional implementada no Vaultwarden, a equipe ganha **2 pontos extras**.
3.  **A Punição do TLS:** Um cofre sem segurança é um alvo fácil. Ao fim da atividade, caso a equipe *não* tenha implementado um sistema de TLS (HTTPS) funcional, **todos os pontos extras conquistados serão cortados pela metade** (arredondados para baixo). O próprio TLS conta como uma feature e adiciona 2 pontos antes do corte.
4.  **A Punição do PVC:** Se o cofre não tiver um PVC funcional, a equipe perde **4 pontos**. Se o PVC estiver presente mas não for configurado corretamente (ex: não persiste os dados), a penalidade é de **3 pontos**.
5.  **O Julgamento Final:** A equipe que somar mais pontos rouba a parte do nome que falta da outra. A vencedora se funde e se torna a suprema **Equipe Sigma**. A perdedora é rebaixada para **Equipe Betinha**.
6.  **A Regra de Ouro:** Não sobra nada pros betinhas. A Equipe Sigma ganha um prêmio surpresa ao final do laboratório.

Boa sorte.
