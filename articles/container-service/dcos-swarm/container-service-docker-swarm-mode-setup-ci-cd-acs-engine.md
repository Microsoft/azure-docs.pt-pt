---
title: (PRETERIDO) CI/CD com o motor do serviço de contentor do Azure e o modo Swarm
description: Utilizar o motor do Azure Container Service com modo Docker Swarm, um Azure Container Registry e DevOps do Azure para fornecer continuamente uma aplicação .NET Core de vários contentor
services: container-service
author: diegomrtnzg
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/27/2017
ms.author: diegomrtnzg
ms.custom: mvc
ms.openlocfilehash: 8aa62e4ed65f8223071786ac165f8343cb6901d5
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58079099"
---
# <a name="deprecated-full-cicd-pipeline-to-deploy-a-multi-container-application-on-azure-container-service-with-acs-engine-and-docker-swarm-mode-using-azure-devops"></a>(PRETERIDO) Pipeline de CI/CD completo para implementar uma aplicação de vários contentor no Azure Container Service com o motor de ACS e o modo Docker Swarm através do Azure DevOps

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

*Este artigo se baseia em [pipeline de CI/CD completo para implementar uma aplicação de vários contentor no Azure Container Service com o Docker Swarm através do Azure DevOps](container-service-docker-swarm-setup-ci-cd.md) documentação*

Hoje em dia, um dos maiores desafios ao desenvolver aplicações modernas para a cloud é a capacidade de fornecer esses aplicativos continuamente. Neste artigo, saiba como implementar um completa de integração e implementação (CI/CD) a utilizar o pipeline: 
* Motor do serviço de contentor do Azure com o modo Docker Swarm
* Registo de Contentores do Azure
* DevOps do Azure

Este artigo se baseia num aplicativo simples, disponível no [GitHub](https://github.com/jcorioland/MyShop/tree/docker-linux), desenvolvidos com o ASP.NET Core. A aplicação é composta por quatro serviços diferentes: APIs e front-end da web de uma de três web:

![Aplicação de exemplo MyShop](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/myshop-application.png)

O objetivo é fornecer esse aplicativo continuamente num cluster do modo Docker Swarm, com o Azure DevOps. A figura a seguir detalha este pipeline de entrega contínua:

![Aplicação de exemplo MyShop](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/full-ci-cd-pipeline.png)

Eis uma breve explicação sobre os passos:

1. Alterações de código são confirmadas para o repositório de código de origem (aqui, GitHub) 
2. GitHub aciona uma compilação em DevOps do Azure 
3. Todas as imagens que compõem o aplicativo de compilações e obtém a versão mais recente de origens de DevOps do Azure 
4. Cada imagem de pushes de DevOps do Azure para um registo de Docker criado com o serviço do Azure Container Registry 
5. DevOps do Azure aciona uma nova versão 
6. A versão executa alguns comandos através de SSH no nó de principal do cluster de serviço de contentor do Azure 
7. Modo docker Swarm no cluster obtém a versão mais recente das imagens 
8. A nova versão da aplicação é implementada com o Docker Stack 

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este tutorial, terá de concluir as seguintes tarefas:

- [Criar um cluster do modo Swarm no Azure Container Service com o motor de ACS](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acsengine-swarmmode)
- [Ligar ao cluster Swarm no Azure Container Service](../container-service-connect.md)
- [Criar um Azure container registry](../../container-registry/container-registry-get-started-portal.md)
- [Ter uma organização de DevOps do Azure e o projeto criado](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student)
- [Bifurcar o repositório do GitHub para a sua conta do GitHub](https://github.com/jcorioland/MyShop/tree/docker-linux)

>[!NOTE]
> O orchestrator do Docker Swarm no Azure Container Service utiliza o legado autónomo do Swarm. Atualmente, o [modo Swarm](https://docs.docker.com/engine/swarm/) integrado (no Docker 1.12 e superior) não é um orchestrator suportado no Azure Container Service. Por esse motivo, estamos usando [motor de ACS](https://github.com/Azure/acs-engine/blob/master/docs/swarmmode.md), uma contribuição pela Comunidade [modelo de início rápido](https://azure.microsoft.com/resources/templates/101-acsengine-swarmmode/), ou uma solução Docker no [Azure Marketplace](https://azuremarketplace.microsoft.com).
>

## <a name="step-1-configure-your-azure-devops-organization"></a>Passo 1: Configurar a sua organização de DevOps do Azure 

Nesta secção, vai configurar sua organização de DevOps do Azure. Para configurar pontos finais de serviços do Azure DevOps, no seu projeto de DevOps do Azure, clique a **configurações** ícone na barra de ferramentas e selecione **serviços**.

![Ponto final dos serviços aberto](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/services-vsts.PNG)

### <a name="connect-azure-devops-and-azure-account"></a>Ligue-se de DevOps do Azure e conta do Azure

Configure uma ligação entre o seu projeto de DevOps do Azure e a sua conta do Azure.

1. No lado esquerdo, clique em **novo ponto final de serviço** > **do Azure Resource Manager**.
2. Para autorizar o Azure DevOps para trabalhar com sua conta do Azure, selecione seu **subscrição** e clique em **OK**.

    ![DevOps do Azure - autorizar o Azure](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-azure.PNG)

### <a name="connect-azure-devops-and-github"></a>Ligue-se de DevOps do Azure e do GitHub

Configure uma ligação entre o seu projeto de DevOps do Azure e a sua conta do GitHub.

1. No lado esquerdo, clique em **novo ponto final de serviço** > **GitHub**.
2. Para autorizar o Azure DevOps para trabalhar com a sua conta do GitHub, clique em **autorizar** e siga o procedimento de janela que é aberta.

    ![Autorizar o Azure DevOps - GitHub](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-github.png)

### <a name="connect-azure-devops-to-your-azure-container-service-cluster"></a>Ligar o Azure DevOps ao cluster do Azure Container Service

São os últimos passos antes de entrar no pipeline de CI/CD para configurar ligações externas ao seu cluster do Docker Swarm no Azure. 

1. Para o cluster Docker Swarm, adicionar um ponto final do tipo **SSH**. Em seguida, introduza as informações de ligação de SSH do seu cluster do Swarm (nó principal).

    ![DevOps do Azure - SSH](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-ssh.png)

Toda a configuração é feita agora. Os passos seguintes, vai criar o pipeline de CI/CD que cria e implementa a aplicação no cluster Docker Swarm. 

## <a name="step-2-create-the-build-pipeline"></a>Passo 2: Criar o pipeline de compilação

Neste passo, configure um pipeline de compilação para o seu projeto de DevOps do Azure e definir o fluxo de trabalho de compilação para as imagens de contentor

### <a name="initial-pipeline-setup"></a>Configuração inicial do pipeline

1. Para criar um pipeline de compilação, ligar ao seu projeto de DevOps do Azure e clique em **compilação e versão**. Na **definições de compilação** secção, clique em **+ novo**. 

    ![Pipeline de compilação de DevOps do Azure - novo](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/create-build-vsts.PNG)

2. Selecione o **vazio processo**.

    ![DevOps do Azure - novo Pipeline de compilação vazio](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/create-empty-build-vsts.PNG)

4. Em seguida, clique a **variáveis** separador e criar duas novas variáveis: **RegistryURL** e **AgentURL**. Cole os valores do seu registo e o DNS de agentes de Cluster.

    ![DevOps do Azure - configuração de variáveis de compilação](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-variables.png)

5. Na **definições de compilação** página, abra o **Acionadores** separador e configurar a compilação para utilizar a integração contínua com o fork do projeto MyShop que criou nos pré-requisitos. Em seguida, selecione **alterações do Batch**. Certifique-se de que seleciona *docker-linux* como o **ramificar especificação**.

    ![DevOps do Azure - configuração do repositório de compilação](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-github-repo-conf.PNG)


6. Por último, clique a **opções** separador e configurar a fila de agente predefinido **alojado pré-visualização do Linux**.

    ![DevOps do Azure - configuração do agente anfitrião](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-agent.png)

### <a name="define-the-build-workflow"></a>Definir o fluxo de trabalho de compilação
Os passos seguintes definem o fluxo de trabalho de compilação. Em primeiro lugar, terá de configurar a origem do código. Para fazê-lo, selecione **GitHub** e a sua **repositório** e **ramo** (docker-linux).

![Configurar o DevOps do Azure - código-fonte](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-source-code.png)

Existem cinco imagens de contentor para criar aplicativos para o *MyShop* aplicação. Cada imagem é criada usando o Dockerfile localizado nas pastas de projeto:

* ProductsApi
* Proxy
* RatingsApi
* RecommendationsApi
* ShopFront

Precisa de dois passos de Docker para cada imagem, um para compilar a imagem e um para enviar a imagem no Azure container registry. 

1. Para adicionar um passo no fluxo de trabalho de compilação, clique em **+ Adicionar passo de compilação** e selecione **Docker**.

    ![DevOps do Azure - adicionar passos de compilação](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-add-task.png)

2. Para cada imagem, configure uma etapa que utiliza o `docker build` comando.

    ![DevOps do Azure - compilação do Docker](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-docker-build.png)

    Para a operação de compilação, selecione o seu registo de contentor do Azure, o **crie uma imagem** ação e o Dockerfile que define cada imagem. Definir o **diretório de trabalho** como o diretório de raiz do Dockerfile, definir o **nome da imagem**e selecione **incluem a etiqueta mais recente**.
    
    O nome da imagem tem de estar no seguinte formato: ```$(RegistryURL)/[NAME]:$(Build.BuildId)```. Substitua **[nome]** com o nome da imagem:
    - ```proxy```
    - ```products-api```
    - ```ratings-api```
    - ```recommendations-api```
    - ```shopfront```

3. Para cada imagem, configure uma segunda etapa, que utiliza o `docker push` comando.

    ![DevOps do Azure – envio do Docker](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-docker-push.png)

    Para a operação de push, selecione o seu registo de contentor do Azure, o **enviar uma imagem** ação, introduza o **nome da imagem** criada no passo anterior e selecione **etiqueta mais recentes incluem**.

4. Depois de configurar os passos de compilação e emissão para cada uma das cinco imagens, adicione três passos mais o fluxo de trabalho de compilação.

   ![DevOps do Azure - adicionar tarefa da linha de comandos](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-command-task.png)

   1. Uma tarefa da linha de comandos que utiliza um script de bash para substituir a *RegistryURL* ocorrência no ficheiro docker-Compose com a variável RegistryURL. 
    
       ```-c "sed -i 's/RegistryUrl/$(RegistryURL)/g' src/docker-compose-v3.yml"```

       ![DevOps do Azure - ficheiro de atualização Compose com o URL de registo](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-replace-registry.png)

   2. Uma tarefa da linha de comandos que utiliza um script de bash para substituir a *AgentURL* ocorrência no ficheiro docker-Compose com a variável AgentURL.
  
       ```-c "sed -i 's/AgentUrl/$(AgentURL)/g' src/docker-compose-v3.yml"```

      1. Uma tarefa que descarta o ficheiro de composição atualizado como um artefacto de compilação para que possa ser utilizada na versão. Veja o ecrã seguinte para obter detalhes.

      ![DevOps do Azure - publicar artefactos](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-publish.png) 

      ![Publicar de DevOps do Azure - ficheiro de composição](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-publish-compose.png) 

5. Clique em **guardar e colocar em fila** para testar o seu pipeline de compilação.

   ![DevOps do Azure - Save e fila](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-save.png) 

   ![DevOps do Azure - nova fila](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-queue.png) 

6. Se o **criar** está correto, tem que ver este ecrã:

   ![DevOps do Azure - compilação bem-sucedida](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-succeeded.png) 

## <a name="step-3-create-the-release-pipeline"></a>Passo 3: Criar o pipeline de lançamento

DevOps do Azure permite-lhe [gerir versões em ambientes](https://www.visualstudio.com/team-services/release-management/). Pode ativar a implementação contínua para se certificar de que a aplicação é implementada nos seus ambientes diferentes (por exemplo, desenvolvimento, teste, pré-produção e produção), de forma uniforme. Pode criar um ambiente que representa o seu cluster do Azure Container Service modo Docker Swarm.

![DevOps do Azure - versão para ACS](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-acs.png) 

### <a name="initial-release-setup"></a>Versão inicial do programa de configuração

1. Para criar um pipeline de lançamento, clique em **versões** > **+ versão**

2. Para configurar a origem de artefacto, clique em **artefactos** > **ligar uma origem de artefacto**. Aqui, ligar a este novo pipeline de lançamento para a compilação que definiu no passo anterior. Depois disso, o ficheiro docker-Compose yml está disponível no processo de liberação.

    ![DevOps do Azure - artefactos de versão](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-artefacts.png) 

3. Para configurar o acionador de versão, clique em **Acionadores** e selecione **implementação contínua**. Defina o gatilho na mesma origem de artefacto. Esta definição garante que uma nova versão é iniciado quando a compilação for concluída com êxito.

    ![DevOps do Azure - Acionadores de versão](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-trigger.png) 

4. Para configurar as variáveis de versão, clique em **variáveis** e selecione **+ variável** para criar três novas variáveis com as informações do registo: **docker.username**, **docker.password**, e **docker.registry**. Cole os valores do seu registo e o DNS de agentes de Cluster.

    ![DevOps do Azure - configuração do repositório de compilação](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-variables.png)

    >[!IMPORTANT]
    > Conforme mostrado no ecrã anterior, clique nas **bloqueio** docker.password de caixa de verificação. Esta definição é importante para restringir a palavra-passe.
    >

### <a name="define-the-release-workflow"></a>Definir o fluxo de trabalho de versão

O fluxo de trabalho de versão é composto por duas tarefas que adicionar.

1. Configurar uma tarefa para copiar de forma segura o ficheiro de composição para um *implementar* pasta do Docker Swarm nó principal, usando a ligação de SSH que configurou anteriormente. Veja o ecrã seguinte para obter detalhes.
    
    Pasta de origem: ```$(System.DefaultWorkingDirectory)/MyShop-CI/drop```

    ![DevOps do Azure - versão SCP](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-scp.png)

2. Configurar uma segunda tarefa para executar um comando bash para ser executado `docker` e `docker stack deploy` comandos no nó principal. Veja o ecrã seguinte para obter detalhes.

    ```
    docker login -u $(docker.username) -p $(docker.password) $(docker.registry) && export DOCKER_HOST=:2375 && cd deploy && docker stack deploy --compose-file docker-compose-v3.yml myshop --with-registry-auth
    ```

    ![DevOps do Azure - versão Bash](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-bash.png)

    O comando foi executado no mestre de utiliza a CLI do Docker e a CLI do Docker-Compose para efetuar as seguintes tarefas:

   - Inicie sessão no Azure container registry (utiliza três variáveis de compilação que são definidas na **variáveis** separador)
   - Definir o **DOCKER_HOST** variável para trabalhar com o ponto final do Swarm (: 2375)
   - Navegue para o *implementar* pasta que foi criada pela tarefa de cópia de segurança anterior e que contém o ficheiro docker-Compose. 
   - Executar `docker stack deploy` comandos que as novas imagens de solicitação e criar os contentores.

     >[!IMPORTANT]
     > Conforme mostrado no ecrã anterior, deixe a **falhar em STDERR** caixa de verificação desmarcada. Esta definição permite-nos concluir o processo de liberação devido a `docker-compose` imprime várias mensagens de diagnóstico, como contentores estão a parar ou a ser eliminado, a saída de erro padrão. Se selecionar a caixa de verificação, Azure DevOps relatórios que ocorreram erros durante o lançamento, mesmo se tudo correr bem.
     >
3. Guarde este novo pipeline de lançamento.

## <a name="step-4-test-the-cicd-pipeline"></a>Passo 4: Testar o pipeline de CI/CD

Agora que já com a configuração, é altura de o testar este novo pipeline de CI/CD. É a maneira mais fácil para testá-lo atualizar o código-fonte e consolidar as alterações no seu repositório do GitHub. Alguns segundos depois de enviar o código, verá uma nova compilação em execução no Azure DevOps. Depois de concluída com êxito, uma nova versão é acionada e implementado a nova versão da aplicação no cluster do Azure Container Service.

## <a name="next-steps"></a>Passos Seguintes

* Para obter mais informações sobre a CI/CD com o DevOps do Azure, consulte a [descrição geral do Azure DevOps Build](https://www.visualstudio.com/docs/build/overview).
* Para obter mais informações sobre o motor de ACS, consulte a [repositório do GitHub do motor de ACS](https://github.com/Azure/acs-engine).
* Para obter mais informações sobre o modo Docker Swarm, consulte a [descrição geral do modo Docker Swarm](https://docs.docker.com/engine/swarm/).
