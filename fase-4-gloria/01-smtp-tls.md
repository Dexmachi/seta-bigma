# Fase 4: A Glória (Features Extras)

Seu cofre está no ar, funcional e persistente. Isso já é uma grande vitória. Mas para a **Equipe Sigma**, "funcional" é apenas o ponto de partida. "Pronto para produção" é o objetivo. Nesta fase, você adicionará funcionalidades que transformam um serviço de laboratório em algo robusto.

Cada feature implementada com sucesso vale pontos preciosos.

## 4.1. Configurando um Servidor SMTP

Um cofre de senhas que não pode enviar e-mails para convidar novos usuários ou recuperar contas é apenas um banco de dados glorificado. Configurar um servidor SMTP é essencial.

**Sua Missão:**

- Investigue a documentação do Vaultwarden para descobrir quais **variáveis de ambiente** são usadas para configurar os detalhes do SMTP (host, porta, usuário, senha, etc.).

- Você pode usar serviços como o **SendGrid** ou até mesmo uma conta do **Gmail** (com "senhas de app") para obter um servidor SMTP funcional.

- **Como atualizar?** Você precisará editar seu `Deployment` para adicionar essas novas variáveis de ambiente. O `kubectl` é seu amigo. O comando `kubectl apply -f seu-manifesto.yaml` é inteligente o suficiente para aplicar apenas as mudanças, sem derrubar o que já está funcionando.

## 4.2. TLS/HTTPS: A Punição e a Glória

A regra é clara: um cofre sem TLS não é um cofre, é um alvo. Implementar HTTPS não é apenas uma feature extra, é uma necessidade de segurança fundamental.

Esta é, de longe, a tarefa mais complexa e a que mais diferencia uma equipe júnior de uma sênior. Existem alguns caminhos para a vitória:

### Caminho A: O Padrão de Mercado (Ingress + Cert-Manager)

Esta é a forma como a maioria das aplicações em produção no Kubernetes lida com TLS.

1.  **Ingress Controller**: Um Ingress Controller (como o popular **NGINX Ingress**) atua como um proxy reverso dentro do cluster. Ele direciona o tráfego externo para os `Services` internos com base em regras (ex: `host` ou `path`). Instale um no seu cluster.

2.  **Cert-Manager**: Este é um "operador" do Kubernetes que automatiza a criação e renovação de certificados TLS. Ele pode se conectar a autoridades certificadoras como a **Let's Encrypt** para obter certificados gratuitos e válidos.

3.  **A Mágica**: Você cria um recurso do tipo `Ingress` que aponta para o seu `Service` do Vaultwarden e adiciona anotações especiais que dizem ao `cert-manager` para obter um certificado para aquele host.

**Prós:** Extremamente robusto, escalável e automatizado.
**Contras:** Requer a instalação e configuração de dois componentes complexos.

### Caminho B: O Modo Direto (TLS no Vaultwarden)

O próprio Vaultwarden (via seu servidor web Rocket) pode ser configurado para servir tráfego em HTTPS diretamente.

**Sua Missão:**

1.  Obtenha um certificado TLS. Para um teste local, você pode gerar um **certificado autoassinado** usando `openssl`.

2.  Crie um `Secret` do Kubernetes do tipo `kubernetes.io/tls` para armazenar seus arquivos de certificado e chave privada (`tls.crt`, `tls.key`).

3.  Monte este `Secret` como um volume dentro do seu Pod do Vaultwarden.

4.  Configure as variáveis de ambiente do Vaultwarden para habilitar o HTTPS e apontar para os caminhos dos arquivos de certificado e chave que você montou.

**Prós:** Mais simples, menos componentes para instalar.
**Contras:** Certificados autoassinados geram alertas no navegador. A renovação manual é impraticável para produção.

A escolha da estratégia é sua. Ambas as soluções, se bem implementadas, cumprem o requisito. Ao seguir o caminho A, você recebe 2 pontos a mais (duh, você subiu 2 aplicações extras no clusters), mas recomendo o caminho B. Alertas de certificados autoassinados são aceitáveis para um ambiente de laboratório, e a simplicidade é uma virtude. O importante é que o TLS esteja funcionando, garantindo que as comunicações com o cofre sejam seguras.
