---
title: Implementar uma aplicação com ci e Azure Pipelines
description: Neste tutorial, aprende-se a configurar uma integração contínua e implementação para uma aplicação de Tecido de Serviço utilizando Pipelines Azure.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc
ms.openlocfilehash: a26cfaca466e01b154c65b27895f3004f6320e5d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91326342"
---
# <a name="tutorial-deploy-an-application-with-cicd-to-a-service-fabric-cluster"></a>Tutorial: Implementar uma aplicação com CI/CD num cluster do Service Fabric

Este tutorial é parte quatro de uma série e descreve como configurar a integração e implementação contínuas para uma aplicação de Tecido de Serviço Azure usando Pipelines Azure.  É necessária uma aplicação do Service Fabric existente. A aplicação criada em [Compilar uma aplicação .NET](service-fabric-tutorial-create-dotnet-app.md) é utilizada como exemplo.

Na terceira parte da série, ficará a saber como:

> [!div class="checklist"]
> * Adicionar controlo de origem ao seu projeto
> * Criar um pipeline de compilação no Azure Pipelines
> * Criar um pipeline de versão no Azure Pipelines
> * Implementar e atualizar uma aplicação automaticamente

Nesta série de tutoriais, ficará a saber como:
> [!div class="checklist"]
> * [Criar uma aplicação .NET do Service Fabric](service-fabric-tutorial-create-dotnet-app.md)
> * [Implementar a aplicação num cluster remoto](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * [Adicionar um ponto final HTTPS a um serviço de front-end ASP.NET Core](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
> * Configurar CI/CD com o Azure Pipelines
> * [Configurar a monitorização e os diagnósticos da aplicação](service-fabric-tutorial-monitoring-aspnet.md)

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial:

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Instale o Visual Studio 2019](https://www.visualstudio.com/) e instale as cargas de trabalho de desenvolvimento do **Azure** e **ASP.NET e desenvolvimento web.**
* [Instalar o Service Fabric SDK](service-fabric-get-started.md)
* Crie um cluster do Windows Service Fabric no Azure, por exemplo, [seguindo este tutorial](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* Criar uma [organização Azure DevOps](/azure/devops/organizations/accounts/create-organization-msa-or-work-student). Isto permite-lhe criar um projeto em Azure DevOps e utilizar a Azure Pipelines.

## <a name="download-the-voting-sample-application"></a>Transferir a aplicação de votação de exemplo

Se não tiver construído a aplicação da amostra de voto na [primeira parte desta série tutorial,](service-fabric-tutorial-create-dotnet-app.md)pode descarregá-la. Numa janela do comando, execute o seguinte comando para clonar o repositório da aplicação de exemplo para o seu computador local.

```git
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## <a name="prepare-a-publish-profile"></a>Preparar um perfil de publicação

Agora que já [criou uma aplicação](service-fabric-tutorial-create-dotnet-app.md) e [implementou a aplicação no Azure](service-fabric-tutorial-deploy-app-to-party-cluster.md), está pronto para configurar a integração contínua.  Em primeiro lugar, prepare um perfil de publicação dentro da sua aplicação para utilização pelo processo de implementação que executa dentro dos Pipelines Azure.  O perfil de publicação deve ser configurado para visar o cluster que criou anteriormente.  Inicie o Visual Studio e abra um projeto de aplicação do Service Fabric existente.  No **Explorador de Soluções**, clique com o botão direito do rato na aplicação e selecione **Publicar...**.

Escolha um perfil de destino no seu projeto de aplicação para utilizar para o fluxo de trabalho de integração contínua, por exemplo, a Cloud.  Especifique o ponto final de ligação do cluster.  Marque a caixa de verificação **Atualizar a Aplicação** para que a aplicação seja atualizada para cada implementação no Azure DevOps.  Clique na hiperligação **Guardar** para guardar as definições do perfil de publicação e, em seguida, clique em **Cancelar** para fechar a caixa de diálogo.

![Perfil push][publish-app-profile]

## <a name="share-your-visual-studio-solution-to-a-new-azure-devops-git-repo"></a>Partilhar a sua solução Visual Studio num novo repositório Git do Azure DevOps

Partilhe os ficheiros de origem da aplicação para um projeto no Azure DevOps, para que possa gerar compilações.

Crie um novo repo Git local para o seu projeto selecionando **Add to Source Control**  ->  **Git** na barra de estado no canto inferior direito do Visual Studio.

Na vista **Push** no **Team Explorer**, selecione o botão **Publicar Repositório Git** em **Push para o Azure DevOps**.

![Screenshot da janela team explorer - sincronização em Estúdio Visual. O botão Publish to Git Repo é destacado em Push to Azure DevOps.][push-git-repo]

Verifique o seu e-mail e selecione a sua conta na lista pendente **Domínio do Azure DevOps**. Introduza o nome do seu repositório e selecione **Publicar repositório**.

![Screenshot das definições push to Azure DevOps com o e-mail, conta, nome do repositório e botão de Repositório de Publicação realçados.][publish-code]

A publicação do repositório cria um novo projeto na sua conta com o mesmo nome que o repositório local. Para criar o repositório num projeto existente, clique em **Avançadas** junto ao nome do **Repositório** e selecione um projeto. Pode ver o código na Web, selecionando **Ver na Web**.

## <a name="configure-continuous-delivery-with-azure-pipelines"></a>Configurar entrega contínua com gasodutos Azure

Um gasoduto Azure Pipelines descreve um fluxo de trabalho que é composto por um conjunto de passos de construção que são executados sequencialmente. Crie um pipeline de compilação que produz um pacote de aplicação do Service Fabric, e outros artefactos, para implementar num cluster do Service Fabric. Saiba mais sobre [Pipelines de compilação do Azure Pipelines](https://www.visualstudio.com/docs/build/define/create). 

Um pipeline de versão do Azure Pipelines descreve um fluxo de trabalho que implementa um pacote de aplicação num cluster. Quando utilizados em conjunto, o pipeline de compilação e o pipeline de versão executam o fluxo de trabalho completo, começando com os ficheiros de origem e terminando com uma aplicação em execução no cluster. Saiba mais sobre [os oleodutos Azure Pipelines.](https://www.visualstudio.com/docs/release/author-release-definition/more-release-definition)

### <a name="create-a-build-pipeline"></a>Criar um pipeline de compilação

Abra um browser e navegue até ao novo projeto em: [https://&lt;myaccount&gt;.visualstudio.com/Voting/Voting%20Team/_git/Voting](https://myaccount.visualstudio.com/Voting/Voting%20Team/_git/Voting).

Selecione o **separador Pipelines** e, em seguida, **Constrói** e, em seguida, clique em **Novo Pipeline**.

![Novo Pipeline][new-pipeline]

Selecione **Azure Repos Git** como fonte, projeto da Equipa **de Voto,** Repositório **de Voto** e ramo **padrão mestre** para construções manuais e programadas.  Em seguida, clique **em Continuar**.

![Selecione repo][select-repo]

Em **Selecione um modelo**, selecione o modelo de **aplicação do Tecido de Serviço Azure** e clique em **Aplicar**.

![Escolher o modelo de compilação][select-build-template]

Em **Tarefas**, insira "Hosted VS2017" como **piscina de Agente**.

![Selecionar tarefas][save-and-queue]

Em **Acionadores**, ative a integração contínua ao selecionar **Ativar a integração contínua**. Dentro **dos filtros Branch,** a **especificação do ramo** não permite **dominar.** Selecione **Guardar e colocar em fila de espera** para iniciar manualmente uma compilação.

![Selecionar acionadores][save-and-queue2]

As compilações também são acionadas após push ou dar entrada. Para verificar o progresso da sua construção, mude para o **separador Builds.**  Assim que verificar se a construção executa com sucesso, defina um gasoduto de libertação que implemente a sua aplicação num cluster.

### <a name="create-a-release-pipeline"></a>Criar um pipeline de versão

Selecione o **separador Pipelines** e, em seguida, **desbloqueia** e, em seguida, **+ Novo gasoduto**.  Em **Selecionar um modelo**, selecione o modelo **Implementação do Azure Service Fabric** na lista e, em seguida, **Aplicar**.

![Escolher o modelo de versão][select-release-template]

Selecione **Tasks** -> **Ambiente 1** e, em seguida, **+Novo** para adicionar uma nova ligação de cluster.

![Adicionar ligação de cluster][add-cluster-connection]

Na vista **Adicionar nova Ligação do Service Fabric**, selecione a autenticação **Baseada em certificado** ou **Azure Active Directory**.  Especifique um nome de ligação "mysftestcluster" e um ponto final de cluster "tcp://mysftestcluster.southcentralus.cloudapp.azure.com:19000" (ou o ponto final do cluster no qual está a implementar).

Para a autenticação baseada em certificados, adicione a impressão digital do **certificado do Servidor** do certificado de servidor utilizado para criar o cluster.  Em **Certificado de cliente**, adicione a codificação base 64 do ficheiro de certificado de cliente. Consulte o pop-up de ajuda sobre esse campo para obter informações sobre como obter essa representação com codificação base 64 do certificado. Além disso, adicione a **Palavra-passe** para o certificado.  Pode utilizar o certificado de servidor ou cluster se não tiver um certificado de cliente separado.

Para as credenciais do Azure Active Directory, adicione o **Thumbprint do certificado de servidor** do certificado de servidor utilizado para criar o cluster e as credenciais que pretende utilizar para ligar ao cluster nos campos **Nome de utilizador** e **Palavra-passe**.

Clique em **Adicionar** para guardar a ligação de cluster.

Em seguida, adicione um artefacto de compilação ao pipeline, para que o pipeline de versão possa encontrar o resultado da compilação. Selecione **Pipeline** e **Artefactos**->**+Adicionar**.  Em **Origem (Definição de compilação)**, selecione o pipeline de compilação que criou anteriormente.  Clique em **Adicionar** para criar o artefacto.

![Adicionar artefacto][add-artifact]

Ative um acionador de implementação contínua para que uma versão seja criada automaticamente quando a compilação for concluída. Clique no ícone de relâmpago no artefacto, ative o acionador e clique em **Guardar** para guardar o pipeline de versão.

![Ativar o acionador][enable-trigger]

Selecione **+**  ->  **Desbloqueie criar um desbloqueio**  ->   para criar manualmente um desbloqueio. Pode monitorizar o progresso da versão no separador **Versões**.

Verifique se a implementação foi concluída com êxito e se a aplicação está em execução no cluster.  Abra um browser e navegue até `http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/`.  Tome nota da versão da aplicação, que neste exemplo é "1.0.0.20170616.3".

## <a name="commit-and-push-changes-trigger-a-release"></a>Consolidar e emitir alterações, acionar uma versão

Para verificar se o pipeline de integração contínua está a funcionar, dê entrada de algumas alterações de código no Azure DevOps.

À medida que escreve o código, as alterações são automaticamente controladas pelo Visual Studio. Para consolidar as alterações ao repositório Git local, selecione o ícone de alterações pendentes (![O ícone de alterações pendentes mostra um lápis e um número.][pending]) na barra de estado na parte inferior direita.

Na vista **Alterações**, no Team Explorer, adicione uma mensagem que descreva a atualização e consolide as alterações.

![Consolidar tudo][changes]

Selecione o ícone da barra de estado de alterações não publicadas (![Alterações não publicadas][unpublished-changes]) ou a vista de Sincronização no Team Explorer. **Selecione Push** para atualizar o seu código em Azure Pipelines.

![Emitir alterações][push]

Empurrar as alterações para a Azure Pipelines ativa automaticamente uma construção.  Quando o pipeline de compilação for concluído com êxito, é criada automaticamente uma versão e a aplicação no cluster começa a ser atualizada.

Para verificar o progresso da compilação, mude para o separador **Compilações** no **Team Explorer** no Visual Studio.  Depois de verificar se a compilação é executada com êxito, defina um pipeline de versão que implementa a aplicação num cluster.

Verifique se a implementação foi concluída com êxito e se a aplicação está em execução no cluster.  Abra um browser e navegue até `http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/`.  Tome nota da versão da aplicação, que neste exemplo é "1.0.0.20170815.3".

![Screenshot da aplicação Voting no Service Fabric Explorer que funciona numa janela do navegador. Destaca-se a versão da aplicação "1.0.0.20170815.3".][sfx1]

## <a name="update-the-application"></a>Atualizar a aplicação

Efetue alterações de código na aplicação.  Guarde e consolide as alterações, seguindo os passos anteriores.

Depois de iniciada a atualização de versão da aplicação, pode ver o progresso da atualização de versão no Service Fabric Explorer:

![Screenshot da aplicação de voto no Service Fabric Explorer. A mensagem de estado "Upgrade", e uma mensagem "Upgrade in Progress" são realçadas.][sfx2]

A atualização de versão da aplicação pode demorar vários minutos. Quando a atualização de versão estiver concluída, a aplicação irá executar a versão seguinte.  Neste exemplo, "1.0.0.20170815.4".

![Screenshot da aplicação Voting no Service Fabric Explorer que funciona numa janela do navegador. Destaca-se a versão atualizada da aplicação "1.0.0.20170815.4".][sfx3]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Adicionar controlo de origem ao seu projeto
> * Criar um pipeline de compilação
> * Criar um pipeline de versão
> * Implementar e atualizar uma aplicação automaticamente

Avance para o tutorial seguinte:
> [!div class="nextstepaction"]
> [Configurar a monitorização e os diagnósticos da aplicação](service-fabric-tutorial-monitoring-aspnet.md)

<!-- Image References -->
[publish-app-profile]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishAppProfile.png
[push-git-repo]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishGitRepo.png
[publish-code]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishCode.png
[new-pipeline]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/NewPipeline.png
[select-repo]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SelectRepo.png
[select-build-template]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SelectBuildTemplate.png
[save-and-queue]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SaveAndQueue.png
[save-and-queue2]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SaveAndQueue2.png
[select-release-template]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SelectReleaseTemplate.png
[set-continuous-integration]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SetContinuousIntegration.png
[add-cluster-connection]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/AddClusterConnection.png
[add-artifact]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/AddArtifact.png
[enable-trigger]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/EnableTrigger.png
[sfx1]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SFX1.png
[sfx2]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SFX2.png
[sfx3]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SFX3.png
[pending]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/Pending.png
[changes]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/Changes.png
[unpublished-changes]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/UnpublishedChanges.png
[push]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/Push.png
[continuous-delivery-with-AzureDevOpsServices]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/VSTS-Dialog.png
[new-service-endpoint]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/NewServiceEndpoint.png
[new-service-endpoint-dialog]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/NewServiceEndpointDialog.png
