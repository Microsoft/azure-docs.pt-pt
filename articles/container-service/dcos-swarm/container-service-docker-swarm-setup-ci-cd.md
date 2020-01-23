---
title: PRETERIDO CI/CD com o serviço de contêiner do Azure e o Swarm
description: Usar o serviço de contêiner do Azure com o Docker Swarm, um registro de contêiner do Azure e o Azure DevOps para fornecer continuamente um aplicativo .NET Core com vários contêineres
author: jcorioland
ms.service: container-service
ms.topic: conceptual
ms.date: 12/08/2016
ms.author: jucoriol
ms.custom: mvc
ms.openlocfilehash: 11a6debe735459b617f6f93c3f67a32350dd4623
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76549058"
---
# <a name="deprecated-full-cicd-pipeline-to-deploy-a-multi-container-application-on-azure-container-service-with-docker-swarm-using-azure-devops-services"></a>PRETERIDO Pipeline de CI/CD completo para implantar um aplicativo de vários contêineres no serviço de contêiner do Azure com o Docker Swarm usando o Azure DevOps Services

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Um dos maiores desafios ao desenvolver aplicativos modernos para a nuvem é a capacidade de fornecer esses aplicativos continuamente. Neste artigo, você aprenderá a implementar um pipeline de CI/CD (integração e implantação contínua) completo usando o serviço de contêiner do Azure com o Docker Swarm, o registro de contêiner do Azure e o gerenciamento de Azure Pipelines.


![Aplicativo de exemplo do Myshop](./media/container-service-docker-swarm-setup-ci-cd/myshop-application.png)

O objetivo é entregar esse aplicativo continuamente em um cluster do Docker Swarm, usando Azure DevOps Services. A figura a seguir detalha esse pipeline de entrega contínua:

![Aplicativo de exemplo do Myshop](./media/container-service-docker-swarm-setup-ci-cd/full-ci-cd-pipeline.png)

Aqui está uma breve explicação das etapas:

1. Alterações de código são confirmadas no repositório de código-fonte (aqui, GitHub) 
1. O GitHub dispara uma compilação no Azure DevOps Services 
1. Azure DevOps Services Obtém a versão mais recente das fontes e compila todas as imagens que compõem o aplicativo 
1. Azure DevOps Services envia por push cada imagem para um registro do Docker criado usando o serviço de registro de contêiner do Azure 
1. Azure DevOps Services dispara uma nova versão 
1. A versão executa alguns comandos usando SSH no nó mestre de cluster do serviço de contêiner do Azure 
1. O Docker Swarm no cluster recebe a versão mais recente das imagens 
1. A nova versão do aplicativo é implantada usando Docker Compose 

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este tutorial, você precisa concluir as seguintes tarefas:

- [Criar um cluster Swarm no Azure Container Service](container-service-deployment.md)
- [Ligar ao cluster Swarm no Azure Container Service](../container-service-connect.md)
- [Criar um registro de contêiner do Azure](../../container-registry/container-registry-get-started-portal.md)
- [Ter uma organização Azure DevOps Services e um projeto criados](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student)
- [Bifurcar o repositório do GitHub para sua conta do GitHub](https://github.com/jcorioland/MyShop/)

[!INCLUDE [container-service-swarm-mode-note](../../../includes/container-service-swarm-mode-note.md)]

Você também precisa de um computador Ubuntu (14, 4 ou 16, 4) com o Docker instalado. Esse computador é usado pelo Azure DevOps Services durante os processos de Azure Pipelines. Uma maneira de criar essa máquina é usar a imagem disponível no Azure Marketplace. 

## <a name="step-1-configure-your-azure-devops-services-organization"></a>Etapa 1: configurar sua organização de Azure DevOps Services 

Nesta seção, você configurará sua organização Azure DevOps Services.

### <a name="configure-an-azure-devops-services-linux-build-agent"></a>Configurar um Azure DevOps Services agente de compilação do Linux

Para criar imagens do Docker e enviar por push essas imagens para um registro de contêiner do Azure de uma compilação Azure DevOps Services, você precisa registrar um agente do Linux. Você tem estas opções de instalação:

* [Implantar um agente no Linux](https://www.visualstudio.com/docs/build/admin/agents/v2-linux)

* [Usar o Docker para executar o agente de Azure DevOps Services](https://hub.docker.com/r/microsoft/vsts-agent)

### <a name="install-the-docker-integration-azure-devops-services-extension"></a>Instalar a extensão de Azure DevOps Services de integração do Docker

A Microsoft fornece uma extensão Azure DevOps Services para trabalhar com o Docker em processos Azure Pipelines. Essa extensão está disponível no [Azure DevOps Services Marketplace](https://marketplace.visualstudio.com/items?itemName=ms-vscs-rm.docker). Clique em **instalar** para adicionar essa extensão à sua organização de Azure DevOps Services:

![Instalar a integração do Docker](./media/container-service-docker-swarm-setup-ci-cd/install-docker-vsts.png)

Você será solicitado a se conectar à sua organização do Azure DevOps Services usando suas credenciais. 

### <a name="connect-azure-devops-services-and-github"></a>Conectar Azure DevOps Services e GitHub

Configure uma conexão entre seu projeto Azure DevOps Services e sua conta do GitHub.

1. No projeto Azure DevOps Services, clique no ícone **configurações** na barra de ferramentas e selecione **Serviços**.

    ![Azure DevOps Services-conexão externa](./media/container-service-docker-swarm-setup-ci-cd/vsts-services-menu.png)

1. À esquerda, clique em **novo ponto de extremidade de serviço** > **GitHub**.

    ![Azure DevOps Services-GitHub](./media/container-service-docker-swarm-setup-ci-cd/vsts-github.png)

1. Para autorizar Azure DevOps Services para trabalhar com sua conta do GitHub, clique em **autorizar** e siga o procedimento na janela que é aberta.

    ![Azure DevOps Services-autorizar o GitHub](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-authorize.png)

### <a name="connect-azure-devops-services-to-your-azure-container-registry-and-azure-container-service-cluster"></a>Conectar Azure DevOps Services ao registro de contêiner do Azure e ao cluster do serviço de contêiner do Azure

As últimas etapas antes de entrar no pipeline de CI/CD são configurar conexões externas para o registro de contêiner e o cluster Docker Swarm no Azure. 

1. Nas configurações de **Serviços** do seu projeto Azure DevOps Services, adicione um ponto de extremidade de serviço do tipo **registro do Docker**. 

1. No pop-up que é aberto, insira a URL e as credenciais do registro de contêiner do Azure.

    ![Azure DevOps Services-registro do Docker](./media/container-service-docker-swarm-setup-ci-cd/vsts-registry.png)

1. Para o cluster Docker Swarm, adicione um ponto de extremidade do tipo **SSH**. Em seguida, insira as informações de conexão SSH do cluster Swarm.

    ![Azure DevOps Services-SSH](./media/container-service-docker-swarm-setup-ci-cd/vsts-ssh.png)

Toda a configuração é feita agora. Nas próximas etapas, você criará o pipeline de CI/CD que cria e implanta o aplicativo no cluster do Docker Swarm. 

## <a name="step-2-create-the-build-pipeline"></a>Etapa 2: criar o pipeline de compilação

Nesta etapa, você configura um pipeline de compilação para seu projeto Azure DevOps Services e define o fluxo de trabalho de Build para suas imagens de contêiner

### <a name="initial-pipeline-setup"></a>Configuração inicial do pipeline

1. Para criar um pipeline de compilação, conecte-se ao seu projeto Azure DevOps Services e clique em **compilar & versão**. 

1. Na seção **definições de compilação** , clique em **+ novo**. Selecione o modelo **vazio** .

    ![Azure DevOps-novo pipeline de compilação](./media/container-service-docker-swarm-setup-ci-cd/create-build-vsts.png)

1. Configure a nova compilação com uma fonte de repositório do GitHub, verifique **integração contínua**e selecione a fila do agente em que você registrou o agente do Linux. Clique em **criar** para criar o pipeline de compilação.

    ![Azure DevOps Services-criar pipeline de compilação](./media/container-service-docker-swarm-setup-ci-cd/vsts-create-build-github.png)

1. Na página **definições de compilação** , primeiro abra a guia **repositório** e configure a compilação para usar a bifurcação do projeto Myshop que você criou nos pré-requisitos. Certifique-se de selecionar *ACS-docs* como o **Branch padrão**.

    ![Azure DevOps Services-configuração do repositório de Build](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-repo-conf.png)

1. Na guia **gatilhos** , configure a compilação a ser disparada após cada confirmação. Selecione **integração contínua** e **alterações em lote**.

    ![Azure DevOps Services-configuração do gatilho de compilação](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-trigger-conf.png)

### <a name="define-the-build-workflow"></a>Definir o fluxo de trabalho de compilação
As próximas etapas definem o fluxo de trabalho de compilação. Há cinco imagens de contêiner para criar para o aplicativo *Myshop* . Cada imagem é criada usando o Dockerfile localizado nas pastas do projeto:

* ProductsApi
* Proxy
* RatingsApi
* RecommendationsApi
* ShopFront

Você precisa adicionar duas etapas do Docker para cada imagem, uma para criar a imagem e outra para enviar a imagem por push no registro de contêiner do Azure. 

1. Para adicionar uma etapa no fluxo de trabalho de compilação, clique em **+ Adicionar etapa de compilação** e selecione **Docker**.

    ![Azure DevOps Services-adicionar etapas de compilação](./media/container-service-docker-swarm-setup-ci-cd/vsts-build-add-task.png)

1. Para cada imagem, configure uma etapa que usa o comando `docker build`.

    ![Azure DevOps Services-Build do Docker](./media/container-service-docker-swarm-setup-ci-cd/vsts-docker-build.png)

    Para a operação de compilação, selecione o registro de contêiner do Azure, a ação **criar uma imagem** e o Dockerfile que define cada imagem. Defina o **contexto de compilação** como o diretório raiz Dockerfile e defina o **nome da imagem**. 
    
    Conforme mostrado na tela anterior, inicie o nome da imagem com o URI do seu registro de contêiner do Azure. (Você também pode usar uma variável de compilação para parametrizar a marca da imagem, como o identificador de compilação neste exemplo.)

1. Para cada imagem, configure uma segunda etapa que usa o comando `docker push`.

    ![Azure DevOps Services-push do Docker](./media/container-service-docker-swarm-setup-ci-cd/vsts-docker-push.png)

    Para a operação de envio em push, selecione o registro de contêiner do Azure, a ação **Enviar uma imagem** e insira o **nome da imagem** criado na etapa anterior.

1. Depois de configurar as etapas de compilação e push para cada uma das cinco imagens, adicione mais duas etapas no fluxo de trabalho de compilação.

    a. Uma tarefa de linha de comando que usa um script bash para substituir a ocorrência *BuildNumber* no arquivo Docker-Compose. yml pela ID de compilação atual. Consulte a tela a seguir para obter detalhes.

    ![Azure DevOps Services-arquivo de composição de atualização](./media/container-service-docker-swarm-setup-ci-cd/vsts-build-replace-build-number.png)

    b. Uma tarefa que descarta o arquivo de composição atualizado como um artefato de compilação para que ele possa ser usado na versão. Consulte a tela a seguir para obter detalhes.

    ![Azure DevOps Services-publicar arquivo de composição](./media/container-service-docker-swarm-setup-ci-cd/vsts-publish-compose.png) 

1. Clique em **salvar** e nomeie o pipeline de compilação.

## <a name="step-3-create-the-release-pipeline"></a>Etapa 3: criar o pipeline de liberação

Azure DevOps Services permite que você [gerencie versões entre ambientes](https://www.visualstudio.com/team-services/release-management/). Você pode habilitar a implantação contínua para garantir que seu aplicativo seja implantado em seus diferentes ambientes (como desenvolvimento, teste, pré-produção e produção) de forma suave. Você pode criar um novo ambiente que representa o cluster do Docker Swarm do serviço de contêiner do Azure.

![Azure DevOps Services-versão do ACS](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-acs.png) 

### <a name="initial-release-setup"></a>Configuração da versão inicial

1. Para criar um pipeline de liberação, clique em **versões** >  **+ versão**

1. Para configurar a origem do artefato, clique em **artefatos** > **vincular uma fonte de artefato**. Aqui, vincule esse novo pipeline de lançamento à compilação que você definiu na etapa anterior. Ao fazer isso, o arquivo Docker-Compose. yml está disponível no processo de liberação.

    ![Artefatos de versão Azure DevOps Services](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-artefacts.png) 

1. Para configurar o gatilho de versão, clique em **gatilhos** e selecione **implantação contínua**. Defina o gatilho na mesma fonte de artefato. Essa configuração garante que uma nova versão seja iniciada assim que a compilação for concluída com êxito.

    ![Gatilhos de Azure DevOps Services versão](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-trigger.png) 

### <a name="define-the-release-workflow"></a>Definir o fluxo de trabalho de liberação

O fluxo de trabalho de liberação é composto de duas tarefas que você adiciona.

1. Configure uma tarefa para copiar com segurança o arquivo de composição para uma pasta de *implantação* no nó mestre do Docker Swarm, usando a conexão SSH configurada anteriormente. Consulte a tela a seguir para obter detalhes.

    ![SCP de Azure DevOps Services versão](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-scp.png)

1. Configure uma segunda tarefa para executar um comando bash para executar `docker` e `docker-compose` comandos no nó mestre. Consulte a tela a seguir para obter detalhes.

    ![Bash de Azure DevOps Services de lançamento](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-bash.png)

    O comando executado no mestre usa a CLI do Docker e a CLI do Docker-Compose para realizar as seguintes tarefas:

   - Faça logon no registro de contêiner do Azure (ele usa três variab'les de compilação que são definidas na guia **variáveis** )
   - Defina a variável **DOCKER_HOST** para trabalhar com o ponto de extremidade Swarm (: 2375)
   - Navegue até a pasta de *implantação* que foi criada pela tarefa de cópia segura anterior e que contém o arquivo Docker-Compose. yml 
   - Execute `docker-compose` comandos que extraem as novas imagens, interrompam os serviços, removam os serviços e criem os contêineres.

     >[!IMPORTANT]
     > Conforme mostrado na tela anterior, deixe a caixa de seleção **falhar em stderr** desmarcada. Essa é uma configuração importante, porque `docker-compose` imprime várias mensagens de diagnóstico, como os contêineres estão parando ou sendo excluídos, na saída de erro padrão. Se você marcar a caixa de seleção, o Azure DevOps Services relatará que erros ocorreram durante a liberação, mesmo se tudo correr bem.
     >
1. Salve esse novo pipeline de lançamento.


>[!NOTE]
>Essa implantação inclui algum tempo de inatividade porque estamos interrompendo os serviços antigos e executando o novo. É possível evitar isso fazendo uma implantação azul-verde.
>

## <a name="step-4-test-the-cicd-pipeline"></a>Passo 4. Testar o pipeline de CI/CD

Agora que você concluiu a configuração, é hora de testar esse novo pipeline de CI/CD. A maneira mais fácil de testá-lo é atualizar o código-fonte e confirmar as alterações em seu repositório GitHub. Alguns segundos depois que você enviar o código por push, você verá uma nova compilação em execução no Azure DevOps Services. Depois de concluído com êxito, uma nova versão será disparada e implantará a nova versão do aplicativo no cluster do serviço de contêiner do Azure.

## <a name="next-steps"></a>Próximos Passos

* Para obter mais informações sobre CI/CD com Azure DevOps Services, consulte o artigo [Azure pipelines documentação](/azure/devops/pipelines/?view=azure-devops) .
