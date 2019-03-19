---
title: (PRETERIDO) CI/CD com o Azure Container Service e do Swarm
description: Utilizar o Azure Container Service com Docker Swarm, um Azure Container Registry e DevOps do Azure para fornecer continuamente uma aplicação .NET Core de vários contentor
services: container-service
author: jcorioland
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 12/08/2016
ms.author: jucoriol
ms.custom: mvc
ms.openlocfilehash: f28ea3dd2837a241c538057bd118409d4f5b858a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58096270"
---
# <a name="deprecated-full-cicd-pipeline-to-deploy-a-multi-container-application-on-azure-container-service-with-docker-swarm-using-azure-devops-services"></a>(PRETERIDO) Pipeline de CI/CD completo para implementar uma aplicação de vários contentor no Azure Container Service com o Docker Swarm com os serviços do Azure DevOps

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Um dos maiores desafios ao desenvolver aplicações modernas para a cloud é a capacidade de fornecer esses aplicativos continuamente. Neste artigo, saiba como implementar uma integração completa contínua e um pipeline de implementação (CI/CD) com o Azure Container Service com a gestão de Docker Swarm, Azure Container Registry e Pipelines do Azure.

Este artigo se baseia num aplicativo simples, disponível no [GitHub](https://github.com/jcorioland/MyShop/tree/acs-docs), desenvolvidos com o ASP.NET Core. A aplicação é composta por quatro serviços diferentes: APIs e front-end da web de uma de três web:

![Aplicação de exemplo MyShop](./media/container-service-docker-swarm-setup-ci-cd/myshop-application.png)

O objetivo é fornecer esse aplicativo continuamente num cluster Docker Swarm, usando os serviços de DevOps do Azure. A figura a seguir detalha este pipeline de entrega contínua:

![Aplicação de exemplo MyShop](./media/container-service-docker-swarm-setup-ci-cd/full-ci-cd-pipeline.png)

Eis uma breve explicação sobre os passos:

1. Alterações de código são confirmadas para o repositório de código de origem (aqui, GitHub) 
1. GitHub aciona uma compilação nos serviços de DevOps do Azure 
1. Serviços de DevOps do Azure obtém a versão mais recente das origens e baseia-se todas as imagens que compõem a aplicação 
1. Serviços de DevOps do Azure envia por push a cada imagem para um registo de Docker criado com o serviço do Azure Container Registry 
1. Serviços de DevOps do Azure aciona uma nova versão 
1. A versão executa alguns comandos através de SSH no nó de principal do cluster de serviço de contentor do Azure 
1. O docker Swarm no cluster obtém a versão mais recente das imagens 
1. A nova versão da aplicação é implementada com o Docker Compose 

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este tutorial, terá de concluir as seguintes tarefas:

- [Criar um cluster Swarm no Azure Container Service](container-service-deployment.md)
- [Ligar ao cluster Swarm no Azure Container Service](../container-service-connect.md)
- [Criar um Azure container registry](../../container-registry/container-registry-get-started-portal.md)
- [Ter uma organização de serviços de DevOps do Azure e o projeto criado](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student)
- [Bifurcar o repositório do GitHub para a sua conta do GitHub](https://github.com/jcorioland/MyShop/)

[!INCLUDE [container-service-swarm-mode-note](../../../includes/container-service-swarm-mode-note.md)]

Também precisa de uma máquina do Ubuntu (14.04 ou 16.04) com o Docker instalado. Esta máquina é utilizada pelos serviços do Azure DevOps durante os processos de Pipelines do Azure. Uma forma de criar esta máquina é utilizar a imagem disponível na [do Azure Marketplace](https://azure.microsoft.com/marketplace/partners/canonicalandmsopentech/dockeronubuntuserver1404lts/). 

## <a name="step-1-configure-your-azure-devops-services-organization"></a>Passo 1: Configurar a sua organização de serviços do Azure DevOps 

Nesta secção, vai configurar sua organização de serviços de DevOps do Azure.

### <a name="configure-an-azure-devops-services-linux-build-agent"></a>Configurar um agente de compilação do Linux de serviços do Azure DevOps

Para criar imagens do Docker e enviar essas imagens para um registo de contentor do Azure a partir de uma compilação de serviços de DevOps do Azure, terá de registar um agente do Linux. Tem estas opções de instalação:

* [Implementar um agente no Linux](https://www.visualstudio.com/docs/build/admin/agents/v2-linux)

* [Utilizar o Docker para executar o agente dos serviços de DevOps do Azure](https://hub.docker.com/r/microsoft/vsts-agent)

### <a name="install-the-docker-integration-azure-devops-services-extension"></a>Instalar a extensão de serviços de DevOps do Azure de integração do Docker

A Microsoft fornece uma extensão de serviços de DevOps do Azure para trabalhar com o Docker em processos de Pipelines do Azure. Esta extensão está disponível na [Marketplace de serviços do Azure DevOps](https://marketplace.visualstudio.com/items?itemName=ms-vscs-rm.docker). Clique em **instalar** para adicionar esta extensão para a sua organização de serviços de DevOps do Azure:

![Instalar a integração de Docker](./media/container-service-docker-swarm-setup-ci-cd/install-docker-vsts.png)

É-lhe perguntado para ligar a sua organização de serviços de DevOps do Azure com as suas credenciais. 

### <a name="connect-azure-devops-services-and-github"></a>Ligue-se de DevOps do Azure Services e do GitHub

Configure uma ligação entre o seu projeto de serviços de DevOps do Azure e a sua conta do GitHub.

1. No seu projeto de serviços de DevOps do Azure, clique nas **configurações** ícone na barra de ferramentas e selecione **serviços**.

    ![Serviços de DevOps do Azure - ligação externa](./media/container-service-docker-swarm-setup-ci-cd/vsts-services-menu.png)

1. No lado esquerdo, clique em **novo ponto final de serviço** > **GitHub**.

    ![Serviços de DevOps do Azure - GitHub](./media/container-service-docker-swarm-setup-ci-cd/vsts-github.png)

1. Para autorizar os serviços de DevOps do Azure para trabalhar com a sua conta do GitHub, clique em **autorizar** e siga o procedimento de janela que é aberta.

    ![Serviços de DevOps do Azure - autorizar GitHub](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-authorize.png)

### <a name="connect-azure-devops-services-to-your-azure-container-registry-and-azure-container-service-cluster"></a>Ligar a serviços de DevOps do Azure ao seu registo de contentor do Azure e o cluster do Azure Container Service

São os últimos passos antes de entrar no pipeline de CI/CD para configurar ligações externas ao seu registo de contentor e o seu cluster Docker Swarm no Azure. 

1. Na **serviços** definições do seu projeto de serviços de DevOps do Azure, adicionar um ponto final de serviço do tipo **registo Docker**. 

1. No pop-up que se abre, introduza o URL e as credenciais do seu registo de contentor do Azure.

    ![Serviços de DevOps do Azure - registo Docker](./media/container-service-docker-swarm-setup-ci-cd/vsts-registry.png)

1. Para o cluster Docker Swarm, adicionar um ponto final do tipo **SSH**. Em seguida, introduza as informações de ligação de SSH do seu cluster do Swarm.

    ![Azure DevOps serviços - SSH](./media/container-service-docker-swarm-setup-ci-cd/vsts-ssh.png)

Toda a configuração é feita agora. Os passos seguintes, vai criar o pipeline de CI/CD que cria e implementa a aplicação no cluster Docker Swarm. 

## <a name="step-2-create-the-build-pipeline"></a>Passo 2: Criar o pipeline de compilação

Neste passo, configure um pipeline de compilação para o seu projeto de serviços de DevOps do Azure e definir o fluxo de trabalho de compilação para as imagens de contentor

### <a name="initial-pipeline-setup"></a>Configuração inicial do pipeline

1. Para criar um pipeline de compilação, ligar ao seu projeto de serviços de DevOps do Azure e clique em **compilação e versão**. 

1. Na **definições de compilação** secção, clique em **+ novo**. Selecione o **vazio** modelo.

    ![Pipeline de compilação de DevOps do Azure - novo](./media/container-service-docker-swarm-setup-ci-cd/create-build-vsts.png)

1. Configurar a nova compilação com uma origem de repositório do GitHub, verificação **integração contínua**e selecione a fila de agente em que registrou o agente do Linux. Clique em **criar** para criar o pipeline de compilação.

    ![Serviços de DevOps do Azure - criar o Pipeline de compilação](./media/container-service-docker-swarm-setup-ci-cd/vsts-create-build-github.png)

1. Na **definições de compilação** página, primeiro abra o **repositório** separador e configurar a compilação para usar o fork do projeto MyShop que criou nos pré-requisitos. Certifique-se de que seleciona *acs-docs* como o **ramo predefinido**.

    ![Serviços de DevOps do Azure - configuração do repositório de compilação](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-repo-conf.png)

1. Sobre o **Acionadores** separador, configure a compilação para ser acionado após cada consolidação. Selecione **integração contínua** e **alterações do Batch**.

    ![Serviços de DevOps do Azure - configuração de Acionador de compilação](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-trigger-conf.png)

### <a name="define-the-build-workflow"></a>Definir o fluxo de trabalho de compilação
Os passos seguintes definem o fluxo de trabalho de compilação. Existem cinco imagens de contentor para criar aplicativos para o *MyShop* aplicação. Cada imagem é criada usando o Dockerfile localizado nas pastas de projeto:

* ProductsApi
* Proxy
* RatingsApi
* RecommendationsApi
* ShopFront

Tem de adicionar duas etapas de Docker para cada imagem, um para compilar a imagem e um para enviar a imagem no Azure container registry. 

1. Para adicionar um passo no fluxo de trabalho de compilação, clique em **+ Adicionar passo de compilação** e selecione **Docker**.

    ![Serviços de DevOps do Azure - adicionar passos de compilação](./media/container-service-docker-swarm-setup-ci-cd/vsts-build-add-task.png)

1. Para cada imagem, configure uma etapa que utiliza o `docker build` comando.

    ![Serviços de DevOps do Azure - compilação do Docker](./media/container-service-docker-swarm-setup-ci-cd/vsts-docker-build.png)

    Para a operação de compilação, selecione o seu registo de contentor do Azure, o **crie uma imagem** ação e o Dockerfile que define cada imagem. Definir o **contexto de compilação** como o Dockerfile diretório de raiz e definir o **nome da imagem**. 
    
    Conforme mostrado no ecrã anterior, inicie o nome da imagem com o URI do seu registo de contentor do Azure. (Também pode utilizar uma variável de compilação para parametrizar a etiqueta da imagem, como o identificador de compilação neste exemplo.)

1. Para cada imagem, configure uma segunda etapa, que utiliza o `docker push` comando.

    ![Serviços de DevOps do Azure – envio do Docker](./media/container-service-docker-swarm-setup-ci-cd/vsts-docker-push.png)

    Para a operação de push, selecione o seu registo de contentor do Azure, o **enviar uma imagem** ação e introduza o **nome da imagem** criada no passo anterior.

1. Depois de configurar os passos de compilação e emissão para cada uma das cinco imagens, adicione mais duas etapas do fluxo de trabalho de compilação.

    a. Uma tarefa da linha de comandos que utiliza um script de bash para substituir a *BuildNumber* ocorrência no ficheiro docker-Compose com o atual criar ID. Veja o ecrã seguinte para obter detalhes.

    ![Serviços de DevOps do Azure - ficheiro de composição de atualização](./media/container-service-docker-swarm-setup-ci-cd/vsts-build-replace-build-number.png)

    b. Uma tarefa que descarta o ficheiro de composição atualizado como um artefacto de compilação para que possa ser utilizada na versão. Veja o ecrã seguinte para obter detalhes.

    ![Serviços de DevOps do Azure - publicar Compose ficheiro](./media/container-service-docker-swarm-setup-ci-cd/vsts-publish-compose.png) 

1. Clique em **guardar** e nomeie o seu pipeline de compilação.

## <a name="step-3-create-the-release-pipeline"></a>Passo 3: Criar o pipeline de lançamento

Serviços de DevOps do Azure permite-lhe [gerir versões em ambientes](https://www.visualstudio.com/team-services/release-management/). Pode ativar a implementação contínua para se certificar de que a aplicação é implementada nos seus ambientes diferentes (por exemplo, desenvolvimento, teste, pré-produção e produção), de forma uniforme. Pode criar um novo ambiente que representa o seu cluster do Azure Container Service Docker Swarm.

![Serviços de DevOps do Azure - versão para ACS](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-acs.png) 

### <a name="initial-release-setup"></a>Versão inicial do programa de configuração

1. Para criar um pipeline de lançamento, clique em **versões** > **+ versão**

1. Para configurar a origem de artefacto, clique em **artefactos** > **ligar uma origem de artefacto**. Aqui, ligar a este novo pipeline de lançamento para a compilação que definiu no passo anterior. Ao fazer isso, o ficheiro docker-Compose yml está disponível no processo de liberação.

    ![Serviços de DevOps do Azure - artefactos de versão](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-artefacts.png) 

1. Para configurar o acionador de versão, clique em **Acionadores** e selecione **implementação contínua**. Defina o gatilho na mesma origem de artefacto. Esta definição garante que uma nova versão é iniciada assim que a compilação for concluída com êxito.

    ![Serviços de DevOps do Azure - Acionadores de versão](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-trigger.png) 

### <a name="define-the-release-workflow"></a>Definir o fluxo de trabalho de versão

O fluxo de trabalho de versão é composto por duas tarefas que adicionar.

1. Configurar uma tarefa para copiar de forma segura o ficheiro de composição para um *implementar* pasta do Docker Swarm nó principal, usando a ligação de SSH que configurou anteriormente. Veja o ecrã seguinte para obter detalhes.

    ![Serviços de DevOps do Azure - versão SCP](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-scp.png)

1. Configurar uma segunda tarefa para executar um comando bash para ser executado `docker` e `docker-compose` comandos no nó principal. Veja o ecrã seguinte para obter detalhes.

    ![Serviços de DevOps do Azure - versão Bash](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-bash.png)

    O comando é executado no mestre, utilize a CLI do Docker e a CLI do Docker-Compose para efetuar as seguintes tarefas:

   - Início de sessão para o Azure container registry (utiliza três variab'les de compilação que são definidos na **variáveis** separador)
   - Definir o **DOCKER_HOST** variável para trabalhar com o ponto final do Swarm (: 2375)
   - Navegue para o *implementar* pasta que foi criada pela tarefa de cópia de segurança anterior e que contém o ficheiro docker-Compose. 
   - Executar `docker-compose` comandos que extraia as novas imagens, pare os serviços, remova os serviços e criar os contentores.

     >[!IMPORTANT]
     > Conforme mostrado no ecrã anterior, deixe a **falhar em STDERR** caixa de verificação desmarcada. Isso é uma definição importante, porque `docker-compose` imprime várias mensagens de diagnóstico, como contentores estão a parar ou a ser eliminado, a saída de erro padrão. Se selecionar a caixa de verificação, os serviços do Azure DevOps relatórios que ocorreram erros durante o lançamento, mesmo se tudo correr bem.
     >
1. Guarde este novo pipeline de lançamento.


>[!NOTE]
>Esta implementação inclui algum período de indisponibilidade porque estamos a parar os serviços antigos e novo a executar. É possível evitar isto, fazendo uma implementação de "blue-Green".
>

## <a name="step-4-test-the-cicd-pipeline"></a>Passo 4. Testar o pipeline de CI/CD

Agora que já com a configuração, é altura de o testar este novo pipeline de CI/CD. É a maneira mais fácil para testá-lo atualizar o código-fonte e consolidar as alterações no seu repositório do GitHub. Alguns segundos depois de enviar o código, verá uma nova compilação em execução nos serviços de DevOps do Azure. Depois de concluída com êxito, uma nova versão será acionada e irá implementar a nova versão da aplicação no cluster do Azure Container Service.

## <a name="next-steps"></a>Próximos Passos

* Para obter mais informações sobre a CI/CD com os serviços de DevOps do Azure, consulte a [descrição geral de compilação do Azure DevOps Services](https://www.visualstudio.com/docs/build/overview).
