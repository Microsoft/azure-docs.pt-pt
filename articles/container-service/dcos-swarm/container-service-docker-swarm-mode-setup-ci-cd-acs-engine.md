---
title: PRETERIDO CI/CD com o mecanismo do serviço de contêiner do Azure e o modo Swarm
description: Usar o mecanismo do serviço de contêiner do Azure com o modo Docker Swarm, um registro de contêiner do Azure e DevOps do Azure para fornecer continuamente um aplicativo .NET Core com vários contêineres
author: diegomrtnzg
ms.service: container-service
ms.topic: conceptual
ms.date: 05/27/2017
ms.author: dimart
ms.custom: mvc
ms.openlocfilehash: 1ec7ece6f5afd1bbd2613ae08af04b82e8a156b2
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76277919"
---
# <a name="deprecated-full-cicd-pipeline-to-deploy-a-multi-container-application-on-azure-container-service-with-acs-engine-and-docker-swarm-mode-using-azure-devops"></a>PRETERIDO Pipeline completo de CI/CD para implantar um aplicativo de vários contêineres no serviço de contêiner do Azure com o mecanismo ACS e o modo Docker Swarm usando o Azure DevOps

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

*Este artigo se baseia no [pipeline de CI/CD completo para implantar um aplicativo de vários contêineres no serviço de contêiner do Azure com o Docker Swarm usando a documentação do DevOps do Azure](container-service-docker-swarm-setup-ci-cd.md)*

Hoje, um dos maiores desafios ao desenvolver aplicativos modernos para a nuvem é a capacidade de fornecer esses aplicativos continuamente. Neste artigo, você aprenderá a implementar um pipeline de CI/CD (integração e implantação contínua) completo usando: 
* Mecanismo do serviço de contêiner do Azure com o modo Docker Swarm
* Azure Container Registry
* Azure DevOps


![Aplicativo de exemplo do Myshop](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/myshop-application.png)

O objetivo é entregar esse aplicativo continuamente em um cluster do modo Docker Swarm, usando o DevOps do Azure. A figura a seguir detalha esse pipeline de entrega contínua:

![Aplicativo de exemplo do Myshop](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/full-ci-cd-pipeline.png)

Aqui está uma breve explicação das etapas:

1. Alterações de código são confirmadas no repositório de código-fonte (aqui, GitHub) 
2. O GitHub dispara uma compilação no Azure DevOps 
3. O Azure DevOps Obtém a versão mais recente das fontes e compila todas as imagens que compõem o aplicativo 
4. O Azure DevOps envia por push cada imagem para um registro do Docker criado usando o serviço de registro de contêiner do Azure 
5. O Azure DevOps dispara uma nova versão 
6. A versão executa alguns comandos usando SSH no nó mestre de cluster do serviço de contêiner do Azure 
7. O modo Docker Swarm no cluster efetua pull da versão mais recente das imagens 
8. A nova versão do aplicativo é implantada usando a pilha do Docker 

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este tutorial, você precisa concluir as seguintes tarefas:

- [Criar um cluster no modo Swarm no serviço de contêiner do Azure com o mecanismo ACS](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acsengine-swarmmode)
- [Ligar ao cluster Swarm no Azure Container Service](../container-service-connect.md)
- [Criar um registro de contêiner do Azure](../../container-registry/container-registry-get-started-portal.md)
- [Ter uma organização do Azure DevOps e um projeto criado](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student)
- [Bifurcar o repositório do GitHub para sua conta do GitHub](https://github.com/jcorioland/MyShop/tree/docker-linux)

>[!NOTE]
> O orchestrator do Docker Swarm no Azure Container Service utiliza o legado autónomo do Swarm. Atualmente, o [modo Swarm](https://docs.docker.com/engine/swarm/) integrado (no Docker 1.12 e superior) não é um orchestrator suportado no Azure Container Service. Por esse motivo, estamos usando o [mecanismo ACS](https://github.com/Azure/acs-engine/blob/master/docs/swarmmode.md), um [modelo de início rápido](https://azure.microsoft.com/resources/templates/101-acsengine-swarmmode/)contribuído pela Comunidade ou uma solução de Docker no [Azure Marketplace](https://azuremarketplace.microsoft.com).
>

## <a name="step-1-configure-your-azure-devops-organization"></a>Etapa 1: configurar sua organização do DevOps do Azure 

Nesta seção, você configurará sua organização DevOps do Azure. Para configurar os pontos de extremidade de Azure DevOps Services, em seu projeto DevOps do Azure, clique no ícone **configurações** na barra de ferramentas e selecione **Serviços**.

![Abrir ponto de extremidade de serviços](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/services-vsts.PNG)

### <a name="connect-azure-devops-and-azure-account"></a>Conectar o Azure DevOps e a conta do Azure

Configure uma conexão entre seu projeto DevOps do Azure e sua conta do Azure.

1. À esquerda, clique em **novo ponto de extremidade de serviço** > **Azure Resource Manager**.
2. Para autorizar o Azure DevOps a trabalhar com sua conta do Azure, selecione sua **assinatura** e clique em **OK**.

    ![Azure DevOps-autorizar o Azure](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-azure.PNG)

### <a name="connect-azure-devops-and-github"></a>Conectar o Azure DevOps e o GitHub

Configure uma conexão entre seu projeto DevOps do Azure e sua conta do GitHub.

1. À esquerda, clique em **novo ponto de extremidade de serviço** > **GitHub**.
2. Para autorizar o Azure DevOps a trabalhar com sua conta do GitHub, clique em **autorizar** e siga o procedimento na janela que é aberta.

    ![Azure DevOps-autorizar o GitHub](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-github.png)

### <a name="connect-azure-devops-to-your-azure-container-service-cluster"></a>Conectar o Azure DevOps ao seu cluster do serviço de contêiner do Azure

As últimas etapas antes de entrar no pipeline de CI/CD são configurar conexões externas com o cluster Docker Swarm no Azure. 

1. Para o cluster Docker Swarm, adicione um ponto de extremidade do tipo **SSH**. Em seguida, insira as informações de conexão SSH do seu cluster Swarm (nó mestre).

    ![DevOps do Azure-SSH](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-ssh.png)

Toda a configuração é feita agora. Nas próximas etapas, você criará o pipeline de CI/CD que cria e implanta o aplicativo no cluster do Docker Swarm. 

## <a name="step-2-create-the-build-pipeline"></a>Etapa 2: criar o pipeline de compilação

Nesta etapa, você configura um pipeline de compilação para seu projeto DevOps do Azure e define o fluxo de trabalho de Build para suas imagens de contêiner

### <a name="initial-pipeline-setup"></a>Configuração inicial do pipeline

1. Para criar um pipeline de compilação, conecte-se ao seu projeto DevOps do Azure e clique em **compilar & versão**. Na seção **definições de compilação** , clique em **+ novo**. 

    ![Azure DevOps-novo pipeline de compilação](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/create-build-vsts.PNG)

2. Selecione o **processo vazio**.

    ![Azure DevOps-novo pipeline de compilação vazio](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/create-empty-build-vsts.PNG)

4. Em seguida, clique na guia **variáveis** e crie duas novas variáveis: **RegistryURL** e **AgentURL**. Cole os valores do registro e do DNS dos agentes de cluster.

    ![DevOps do Azure – configuração de variáveis de compilação](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-variables.png)

5. Na página **definições de compilação** , abra a guia **gatilhos** e configure a compilação para usar a integração contínua com a bifurcação do projeto Myshop que você criou nos pré-requisitos. Em seguida, selecione **alterações em lote**. Certifique-se de selecionar *Docker-Linux* como a **especificação da ramificação**.

    ![DevOps do Azure – configuração do repositório de compilação](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-github-repo-conf.PNG)


6. Por fim, clique na guia **Opções** e configure a fila padrão do agente para versão **prévia hospedada do Linux**.

    ![Azure DevOps-configuração do agente de host](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-agent.png)

### <a name="define-the-build-workflow"></a>Definir o fluxo de trabalho de compilação
As próximas etapas definem o fluxo de trabalho de compilação. Primeiro, você precisa configurar a origem do código. Para fazer isso, selecione **GitHub** e seu **repositório** e **ramificação** (Docker-Linux).

![DevOps do Azure – configurar a origem do código](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-source-code.png)

Há cinco imagens de contêiner para criar para o aplicativo *Myshop* . Cada imagem é criada usando o Dockerfile localizado nas pastas do projeto:

* ProductsApi
* Proxy
* RatingsApi
* RecommendationsApi
* ShopFront

Você precisa de duas etapas do Docker para cada imagem, uma para criar a imagem e outra para enviar a imagem por push no registro de contêiner do Azure. 

1. Para adicionar uma etapa no fluxo de trabalho de compilação, clique em **+ Adicionar etapa de compilação** e selecione **Docker**.

    ![DevOps do Azure – adicionar etapas de compilação](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-add-task.png)

2. Para cada imagem, configure uma etapa que usa o comando `docker build`.

    ![Azure DevOps-Build do Docker](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-docker-build.png)

    Para a operação de compilação, selecione o registro de contêiner do Azure, a ação **criar uma imagem** e o Dockerfile que define cada imagem. Defina o **diretório de trabalho** como o diretório raiz Dockerfile, defina o **nome da imagem**e selecione **incluir marca mais recente**.
    
    O nome da imagem deve estar neste formato: ```$(RegistryURL)/[NAME]:$(Build.BuildId)```. Substitua **[name]** pelo nome da imagem:
    - ```proxy```
    - ```products-api```
    - ```ratings-api```
    - ```recommendations-api```
    - ```shopfront```

3. Para cada imagem, configure uma segunda etapa que usa o comando `docker push`.

    ![Azure DevOps-push do Docker](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-docker-push.png)

    Para a operação de envio em push, selecione o registro de contêiner do Azure, a ação **Enviar uma imagem** , insira o **nome da imagem** criado na etapa anterior e selecione **incluir marca mais recente**.

4. Depois de configurar as etapas de compilação e push para cada uma das cinco imagens, adicione mais três etapas no fluxo de trabalho de compilação.

   ![DevOps do Azure – adicionar tarefa de linha de comando](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-command-task.png)

   1. Uma tarefa de linha de comando que usa um script bash para substituir a ocorrência de *RegistryURL* no arquivo Docker-Compose. yml pela variável RegistryURL. 
    
       ```-c "sed -i 's/RegistryUrl/$(RegistryURL)/g' src/docker-compose-v3.yml"```

       ![Azure DevOps-atualizar arquivo de composição com a URL do registro](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-replace-registry.png)

   2. Uma tarefa de linha de comando que usa um script bash para substituir a ocorrência de *AgentURL* no arquivo Docker-Compose. yml pela variável AgentURL.
  
       ```-c "sed -i 's/AgentUrl/$(AgentURL)/g' src/docker-compose-v3.yml"```

      1. Uma tarefa que descarta o arquivo de composição atualizado como um artefato de compilação para que ele possa ser usado na versão. Consulte a tela a seguir para obter detalhes.

      ![DevOps do Azure – publicar artefato](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-publish.png) 

      ![Azure DevOps-publicar arquivo de composição](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-publish-compose.png) 

5. Clique em **salvar & fila** para testar o pipeline de compilação.

   ![DevOps do Azure – salvar e enfileirar](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-save.png) 

   ![Azure DevOps – nova fila](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-queue.png) 

6. Se a **compilação** estiver correta, você precisará ver esta tela:

   ![Azure DevOps-compilação bem-sucedida](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-succeeded.png) 

## <a name="step-3-create-the-release-pipeline"></a>Etapa 3: criar o pipeline de liberação

O Azure DevOps permite que você [gerencie versões entre ambientes](https://www.visualstudio.com/team-services/release-management/). Você pode habilitar a implantação contínua para garantir que seu aplicativo seja implantado em seus diferentes ambientes (como desenvolvimento, teste, pré-produção e produção) de forma suave. Você pode criar um ambiente que represente o cluster do modo Swarm do serviço de contêiner do Azure.

![Azure DevOps-versão para o ACS](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-acs.png) 

### <a name="initial-release-setup"></a>Configuração da versão inicial

1. Para criar um pipeline de liberação, clique em **versões** >  **+ versão**

2. Para configurar a origem do artefato, clique em **artefatos** > **vincular uma fonte de artefato**. Aqui, vincule esse novo pipeline de lançamento à compilação que você definiu na etapa anterior. Depois disso, o arquivo Docker-Compose. yml está disponível no processo de liberação.

    ![Artefatos de versão DevOps do Azure](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-artefacts.png) 

3. Para configurar o gatilho de versão, clique em **gatilhos** e selecione **implantação contínua**. Defina o gatilho na mesma fonte de artefato. Essa configuração garante que uma nova versão seja iniciada quando a compilação for concluída com êxito.

    ![Gatilhos de versão DevOps do Azure](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-trigger.png) 

4. Para configurar as variáveis de liberação, clique em **variáveis** e selecione **+ variável** para criar três novas variáveis com as informações do registro **: Docker. username**, **Docker. Password**e **Docker. Registry**. Cole os valores do registro e do DNS dos agentes de cluster.

    ![DevOps do Azure – configuração do repositório de compilação](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-variables.png)

    >[!IMPORTANT]
    > Conforme mostrado na tela anterior, clique na caixa de seleção **Bloquear** no Docker. senha. Essa configuração é importante para restringir a senha.
    >

### <a name="define-the-release-workflow"></a>Definir o fluxo de trabalho de liberação

O fluxo de trabalho de liberação é composto de duas tarefas que você adiciona.

1. Configure uma tarefa para copiar com segurança o arquivo de composição para uma pasta de *implantação* no nó mestre do Docker Swarm, usando a conexão SSH configurada anteriormente. Consulte a tela a seguir para obter detalhes.
    
    Pasta de origem: ```$(System.DefaultWorkingDirectory)/MyShop-CI/drop```

    ![Azure DevOps-versão SCP](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-scp.png)

2. Configure uma segunda tarefa para executar um comando bash para executar `docker` e `docker stack deploy` comandos no nó mestre. Consulte a tela a seguir para obter detalhes.

    ```
    docker login -u $(docker.username) -p $(docker.password) $(docker.registry) && export DOCKER_HOST=:2375 && cd deploy && docker stack deploy --compose-file docker-compose-v3.yml myshop --with-registry-auth
    ```

    ![DevOps do Azure – bash de lançamento](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-bash.png)

    O comando executado no mestre usa a CLI do Docker e a CLI do Docker-Compose para realizar as seguintes tarefas:

   - Faça logon no registro de contêiner do Azure (ele usa três variáveis de compilação que são definidas na guia **variáveis** )
   - Defina a variável **DOCKER_HOST** para trabalhar com o ponto de extremidade Swarm (: 2375)
   - Navegue até a pasta de *implantação* que foi criada pela tarefa de cópia segura anterior e que contém o arquivo Docker-Compose. yml 
   - Execute `docker stack deploy` comandos que recebem as novas imagens e criam os contêineres.

     >[!IMPORTANT]
     > Conforme mostrado na tela anterior, deixe a caixa de seleção **falhar em stderr** desmarcada. Essa configuração nos permite concluir o processo de liberação devido a `docker-compose` imprime várias mensagens de diagnóstico, como os contêineres estão parando ou sendo excluídos, na saída de erro padrão. Se você marcar a caixa de seleção, o Azure DevOps relatará que erros ocorreram durante a liberação, mesmo se tudo correr bem.
     >
3. Salve esse novo pipeline de lançamento.

## <a name="step-4-test-the-cicd-pipeline"></a>Etapa 4: testar o pipeline de CI/CD

Agora que você concluiu a configuração, é hora de testar esse novo pipeline de CI/CD. A maneira mais fácil de testá-lo é atualizar o código-fonte e confirmar as alterações em seu repositório GitHub. Alguns segundos depois que você enviar o código por push, você verá uma nova compilação em execução no Azure DevOps. Depois de concluído com êxito, uma nova versão é disparada e implantada a nova versão do aplicativo no cluster do serviço de contêiner do Azure.

## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre CI/CD com o Azure DevOps, consulte o artigo de [documentação Azure pipelines](/azure/devops/pipelines/?view=azure-devops) .
* Para obter mais informações sobre o mecanismo ACS, consulte o [repositório GitHub do mecanismo ACS](https://github.com/Azure/acs-engine).
* Para obter mais informações sobre o modo Docker Swarm, consulte a [visão geral do modo Docker Swarm](https://docs.docker.com/engine/swarm/).
