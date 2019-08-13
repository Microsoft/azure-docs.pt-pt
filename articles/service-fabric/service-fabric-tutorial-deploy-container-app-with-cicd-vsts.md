---
title: Implementar uma aplicação de contentor com CI/CD num cluster do Azure Service Fabric
description: Neste tutorial, você aprenderá a configurar a integração e a implantação contínuas para um aplicativo de contêiner de Service Fabric do Azure usando o Visual Studio Azure DevOps.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/29/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: b686ceace3679d1541e8f1a74bca7e99b81ba932
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/12/2019
ms.locfileid: "68598904"
---
# <a name="tutorial-deploy-a-container-application-with-cicd-to-a-service-fabric-cluster"></a>Tutorial: Implantar um aplicativo de contêiner com CI/CD em um Cluster Service Fabric

Este tutorial é a parte dois de uma série e descreve como configurar a integração e a implantação contínua para um aplicativo de contêiner de Service Fabric do Azure usando o Visual Studio e o Azure DevOps.  É necessária uma aplicação do Service Fabric existente. A aplicação criada em [Implementar uma aplicação .NET num contentor do Windows no Azure Service Fabric](service-fabric-host-app-in-a-container.md) é utilizada como exemplo.

Na segunda parte da série, saiba como:

> [!div class="checklist"]
> * Adicionar controlo de origem ao seu projeto
> * Criar uma definição de compilação no Visual Studio Team Explorer
> * Criar uma definição de versão no Visual Studio Team Explorer
> * Implementar e atualizar uma aplicação automaticamente

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial:

* Tem um cluster no Azure ou [criar um com este tutorial](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* [Implementar uma aplicação contentorizada no mesmo](service-fabric-host-app-in-a-container.md)

## <a name="prepare-a-publish-profile"></a>Preparar um perfil de publicação

Agora que já [implementou uma aplicação de contentor](service-fabric-host-app-in-a-container.md), está pronto para configurar a integração contínua.  Primeiro, prepare um perfil de publicação na sua aplicação para ser utilizado pelo processo de implementação que é executado no Azure DevOps.  O perfil de publicação deve ser configurado para visar o cluster que criou anteriormente.  Inicie o Visual Studio e abra um projeto de aplicação do Service Fabric existente.  No **Explorador de Soluções**, clique com o botão direito do rato na aplicação e selecione **Publicar...** .

Escolha um perfil de destino no seu projeto de aplicação para utilizar para o fluxo de trabalho de integração contínua, por exemplo, a Cloud.  Especifique o ponto final de ligação do cluster.  Marque a caixa de verificação **Atualizar a Aplicação** para que a aplicação seja atualizada para cada implementação no Azure DevOps.  Clique na hiperligação **Guardar** para guardar as definições do perfil de publicação e, em seguida, clique em **Cancelar** para fechar a caixa de diálogo.

![Perfil push][publish-app-profile]

## <a name="share-your-visual-studio-solution-to-a-new-azure-devops-git-repo"></a>Partilhar a sua solução Visual Studio num novo repositório Git do Azure DevOps

Compartilhe os arquivos de origem do aplicativo para um projeto de equipe no Azure DevOps para que você possa gerar compilações.

Crie um novo repositório Git local para o seu projeto, selecionando **Adicionar ao Controlo de Origem** -> **Git** na barra de estado no canto inferior direito do Visual Studio.

Na vista **Push** no **Team Explorer**, selecione o botão **Publicar Repositório Git** em **Push para o Azure DevOps**.

![Repositório Git de push][push-git-repo]

Verifique seu email e selecione sua organização na lista suspensa **conta** . Talvez você precise configurar uma organização se ainda não tiver uma. Introduza o nome do seu repositório e selecione **Publicar repositório**.

![Repositório Git de push][publish-code]

A publicação do repositório cria um novo projeto de equipa na sua conta com o mesmo nome que o repositório local. Para criar o repositório num projeto de equipa existente, clique em **Avançadas** junto ao nome do **Repositório** e selecione um projeto de equipa. Pode ver o código na Web, selecionando **Ver na Web**.

## <a name="configure-continuous-delivery-with-azure-pipelines"></a>Configurar entrega contínua com Pipelines do Azure

Uma definição de compilação DevOps do Azure descreve um fluxo de trabalho que é composto por um conjunto de etapas de compilação que são executadas sequencialmente. Crie uma definição de compilação que produz um pacote de aplicação do Service Fabric, e outros artefactos, para implementar num cluster do Service Fabric. Saiba mais sobre as [definições de compilação](https://www.visualstudio.com/docs/build/define/create)do Azure DevOps. 

Uma definição da versão DevOps do Azure descreve um fluxo de trabalho que implanta um pacote de aplicativos em um cluster. Quando utilizadas em conjunto, a definição de compilação e a definição de versão executam o fluxo de trabalho completo, começando com os ficheiros de origem e terminando com uma aplicação em execução no cluster. Saiba mais sobre as [definições de versão](https://www.visualstudio.com/docs/release/author-release-definition/more-release-definition)do Azure DevOps.

### <a name="create-a-build-definition"></a>Criar uma definição de compilação

Abra seu novo projeto de equipe navegando https://dev.azure.com até em um navegador da Web e selecionando sua organização, seguido pelo novo projeto. 

Selecione a opção pipelines no painel esquerdo e clique em **novo pipeline**.

>[!NOTE]
>Se não vir o modelo de definição de compilação, certifique-se de que a funcionalidade **Nova experiência de criação do pipeline YAML** está desativada. Esta funcionalidade está configurada na secção **Funcionalidades de Pré-visualização** da sua conta de DevOps.

![Novo Pipeline][new-pipeline]

Selecione **Azure Repos git** como fonte, o nome do projeto de equipe, o repositório do projeto e o Branch **mestre** padrão ou as compilações manuais e agendadas.  Em seguida, clique em **Continuar**.

Em **Selecionar um modelo**, selecione o modelo **Aplicação do Azure Service Fabric com suporte do Docker** e clique em **Aplicar**.

![Escolher o modelo de compilação][select-build-template]

Em **Tarefas**, selecione **Hosted VS2017** como **Conjunto do agente**.

![Selecionar tarefas][task-agent-pool]

Clique em **Etiquetar imagens**.

Em **Tipo de Registo de Contentor**, selecione **Azure Container Registry**. Selecione uma **Subscrição do Azure** e, em seguida, clique em **Autorizar**. Selecione um **Azure Container Registry**.

![Selecionar Etiquetar imagens do Docker][select-tag-images]

Clique em **Enviar imagens**.

Em **Tipo de Registo de Contentor**, selecione **Azure Container Registry**. Selecione uma **Subscrição do Azure** e, em seguida, clique em **Autorizar**. Selecione um **Azure Container Registry**.

![Selecionar Enviar imagens do Docker][select-push-images]

Na guia **gatilhos** , habilite a integração contínua verificando **habilitar integração contínua**. Nos **Filtros de ramos**, clique em **+ Adicionar** e a **Especificação do ramo** será predefinida para **mestre**.

Na caixa de diálogo **Guardar pipeline de compilação e fila**, clique em **Guardar e colocar em fila** para iniciar manualmente uma compilação.

![Selecionar acionadores][save-and-queue]

As compilações também são acionadas após push ou dar entrada. Para verificar o progresso da compilação, mude para o separador **Compilações**.  Depois de verificar se a compilação é executada com êxito, defina uma definição de versão que implementa a aplicação num cluster.

### <a name="create-a-release-definition"></a>Criar uma definição de versão

Selecione a opção pipelines no painel esquerdo, então **libera**e **+ novo pipeline**.  Em **Selecionar um modelo**, selecione o modelo **Implementação do Azure Service Fabric** na lista e, em seguida, **Aplicar**.

![Escolher o modelo de versão][select-release-template]

Selecione **Tarefas**, **Ambiente 1** e, em seguida, **+Novo** para adicionar uma nova ligação de cluster.

![Adicionar ligação de cluster][add-cluster-connection]

Na vista **Adicionar nova Ligação do Service Fabric**, selecione a autenticação **Baseada em certificado** ou **Azure Active Directory**.  Especifique um nome de ligação "mysftestcluster" e um ponto final de cluster "tcp://mysftestcluster.southcentralus.cloudapp.azure.com:19000" (ou o ponto final do cluster no qual está a implementar).

Para a autenticação baseada em certificado, adicione o **Thumbprint do certificado de servidor** do certificado de servidor utilizado para criar o cluster.  Em **Certificado de cliente**, adicione a codificação base 64 do ficheiro de certificado de cliente. Consulte o pop-up de ajuda sobre esse campo para obter informações sobre como obter essa representação com codificação base 64 do certificado. Além disso, adicione a **Palavra-passe** para o certificado.  Pode utilizar o certificado de servidor ou cluster se não tiver um certificado de cliente separado.

Para as credenciais do Azure Active Directory, adicione o **Thumbprint do certificado de servidor** do certificado de servidor utilizado para criar o cluster e as credenciais que pretende utilizar para ligar ao cluster nos campos **Nome de utilizador** e **Palavra-passe**.

Clique em **Adicionar** para guardar a ligação de cluster.



Em Fase do Agente, clique em **Implementar Aplicação do Service Fabric**.
Clique em **Definições do Docker** e, em seguida, clique em **Configurar definições do Docker**. Em **Origem das Credenciais de Registo**, selecione **Ligação do Serviço Azure Resource Manager**. Em seguida, selecione a sua **subscrição do Azure**.

![Libertar agente do pipeline][release-pipeline-agent]

Em seguida, adicione um artefacto de compilação ao pipeline, para a definição de versão possa encontrar o resultado da compilação. Selecione **Pipeline** e **Artefactos**-> **+Adicionar**.  Em **Origem (Definição de compilação)** , selecione a definição de compilação que criou anteriormente.  Clique em **Adicionar** para criar o artefacto.

![Adicionar artefacto][add-artifact]

Ative um acionador de implementação contínua para que uma versão seja criada automaticamente quando a compilação for concluída. Clique no ícone de relâmpago no artefacto, ative o acionador e clique em **Guardar** para guardar a definição de versão.

![Ativar o acionador][enable-trigger]

Selecione **+ Versão** -> **Criar uma Versão** -> **Criar** para criar manualmente uma versão. Pode monitorizar o progresso da versão no separador **Versões**.

Verifique se a implementação foi concluída com êxito e se a aplicação está em execução no cluster.  Abra um browser e navegue para [http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/](http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/).  Tome nota da versão da aplicação, que neste exemplo é "1.0.0.20170616.3".

## <a name="commit-and-push-changes-trigger-a-release"></a>Consolidar e emitir alterações, acionar uma versão

Para verificar se o pipeline de integração contínua está a funcionar, dê entrada de algumas alterações de código no Azure DevOps.

À medida que escreve o código, as alterações são automaticamente controladas pelo Visual Studio. Para consolidar as alterações ao repositório Git local, selecione o ícone de alterações pendentes (![Pendente][pending]) na barra de estado na parte inferior direita.

Na vista **Alterações**, no Team Explorer, adicione uma mensagem que descreva a atualização e consolide as alterações.

![Consolidar tudo][changes]

Selecione o ícone da barra de estado de alterações não publicadas (![Alterações não publicadas][unpublished-changes]) ou a vista de Sincronização no Team Explorer. Selecione **Push** para atualizar o código no Azure DevOps.

![Emitir alterações][push]

A emissão das alterações ao Azure DevOps aciona automaticamente uma compilação.  Quando a definição de compilação for concluída com êxito, uma versão é criada automaticamente e começa a atualizar a aplicação no cluster.

Para verificar o progresso da compilação, mude para o separador **Compilações** no **Team Explorer** no Visual Studio.  Depois de verificar se a compilação é executada com êxito, defina uma definição de versão que implementa a aplicação num cluster.

Verifique se a implementação foi concluída com êxito e se a aplicação está em execução no cluster.  Abra um browser e navegue para [http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/](http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/).  Tome nota da versão da aplicação, que neste exemplo é "1.0.0.20170815.3".

![Service Fabric Explorer][sfx1]

## <a name="update-the-application"></a>Atualizar a aplicação

Efetue alterações de código na aplicação.  Guarde e consolide as alterações, seguindo os passos anteriores.

Depois de iniciada a atualização de versão da aplicação, pode ver o progresso da atualização de versão no Service Fabric Explorer:

![Service Fabric Explorer][sfx2]

A atualização de versão da aplicação pode demorar vários minutos. Quando a atualização de versão estiver concluída, a aplicação irá executar a versão seguinte.  Neste exemplo, "1.0.0.20170815.4".

![Service Fabric Explorer][sfx3]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Adicionar controlo de origem ao seu projeto
> * Criar uma definição de compilação
> * Criar uma definição de versão
> * Implementar e atualizar uma aplicação automaticamente

Na parte seguinte do tutorial, aprenda a configurar a [monitorização para o contentor](service-fabric-tutorial-monitoring-wincontainers.md).

<!-- Image References -->
[publish-app-profile]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/PublishAppProfile.png
[push-git-repo]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/PublishGitRepo.png
[publish-code]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/PublishCode.png
[new-pipeline]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/NewPipeline.png
[select-build-template]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/SelectBuildTemplate.png
[task-agent-pool]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/TaskAgentPool.png
[save-and-queue]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/SaveAndQueue.png
[select-tag-images]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/DockerTagImages.png
[select-push-images]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/DockerPushImages.png
[select-release-template]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/SelectReleaseTemplate.png
[set-continuous-integration]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/SetContinuousIntegration.png
[add-cluster-connection]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/AddClusterConnection.png
[release-pipeline-agent]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/ReleasePipelineAgent.png
[add-artifact]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/AddArtifact.png
[enable-trigger]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/EnableTrigger.png
[sfx1]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/SFX1.png
[sfx2]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/SFX2.png
[sfx3]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/SFX3.png
[pending]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/Pending.png
[changes]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/Changes.png
[unpublished-changes]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/UnpublishedChanges.png
[push]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/Push.png
[continuous-delivery-with-VSTS]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/VSTS-Dialog.png
[new-service-endpoint]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/NewServiceEndpoint.png
[new-service-endpoint-dialog]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/NewServiceEndpointDialog.png
