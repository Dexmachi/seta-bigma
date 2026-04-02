# 🛠️ Pré-requisitos da Missão

Antes de iniciar as fases do desafio, certifique-se de que o seu ambiente está configurado com as ferramentas e acessos necessários.

## 1. Acesso e Permissões
*   **Máquinas Virtuais (VMs):** Garanta o acesso às VMs disponibilizadas para o laboratório.
*   **Usuário de Operação:** É obrigatório operar com as permissões corretas. Após acessar a VM, utilize o comando:
    ```bash
    su - lappis
    ```

## 2. Ferramentas Necessárias

Os seguintes softwares devem estar instalados e configurados no seu ambiente de trabalho (VM) para o sucesso da operação:

*   **Docker (ou similar):** Necessário para a Fase 1 (criar, construir e fazer o push de imagens de contêiner). Ferramentas como Docker CLI ou Buildah são aceitas.
*   **Kind (Kubernetes IN Docker):** Obrigatório para a Fase 2. Será a ferramenta exclusiva para subir o cluster Kubernetes local.
*   **kubectl:** A ferramenta de linha de comando padrão para interagir com a API do cluster Kubernetes. Essencial para aplicar os manifestos e gerenciar os recursos criados.
*   **k9s:** Uma interface de terminal (TUI) poderosa para interagir com os clusters. Vai facilitar imensamente a visualização, navegação e o troubleshooting de Pods, Deployments e PVCs durante a batalha.

## 3. Contas em Serviços Externos
*   **Docker Hub:** Será necessário ter uma conta (da equipe ou de um integrante) para realizar o push e hospedar a imagem pública exigida na Fase 1.