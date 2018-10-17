---
title: Tutorial para implementar uma tarefa do Azure Stream Analytics com CI/CD através dos Serviços de DevOps do Azure
description: Este artigo descreve como implementar uma tarefa do Stream Analytics com CI/CD através dos Serviços de DevOps do Azure.
services: stream-analytics
author: su-jie
ms.author: sujie
manager: kfile
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: tutorial
ms.date: 7/10/2018
ms.openlocfilehash: adacbaf718c5ef293b4ee3fa833083704aa41f5c
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2018
ms.locfileid: "44297947"
---
# <a name="tutorial-deploy-an-azure-stream-analytics-job-with-cicd-using-azure-pipelines"></a>Tutorial: Implementar uma tarefa do Azure Stream Analytics com CI/CD através do Azure Pipelines
Este tutorial descreve como configurar a integração e implementação contínuas para uma tarefa do Azure Stream Analytics com o Azure Pipelines. 

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Adicionar controlo de origem ao seu projeto
> * Criar um pipeline de compilação no Azure Pipelines
> * Criar um pipeline de versão no Azure Pipelines
> * Implementar e atualizar uma aplicação automaticamente

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar, certifique-se de que tem o seguinte:

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Instale o [Visual Studio](stream-analytics-tools-for-visual-studio-install.md) e as cargas de trabalho **Desenvolvimento do Azure** ou **Armazenamento de Dados e Processamento**.
* Crie um [projeto do Stream Analytics no Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-quick-create-vs).
* Crie uma organização do [Azure DevOps](https://visualstudio.microsoft.com/team-services/).

## <a name="configure-nuget-package-dependency"></a>Configurar a dependência do pacote NuGet
Para efetuar uma compilação e implementação automáticas numa máquina arbitrária, tem de utilizar o pacote NuGet `Microsoft.Azure.StreamAnalytics.CICD`. Fornece o MSBuild, execução local e ferramentas de implementação que suportam o processo de integração e implementação contínuas de projetos do Stream Analytics para Visual Studio. Para obter mais informações, veja [Ferramentas de CI/CD do Stream Analytics](stream-analytics-tools-for-visual-studio-cicd.md).

Adicione **packages.config** ao diretório do projeto.

```xml
<?xml version="1.0" encoding="utf-8"?>
<packages>
<package id="Microsoft.Azure.StreamAnalytics.CICD" version="1.0.0" targetFramework="net452" />
</packages>
```

## <a name="share-your-visual-studio-solution-to-a-new-azure-repos-git-repo"></a>Partilhar a sua solução Visual Studio num novo repositório Git do Azure

Partilhe os ficheiros de origem da aplicação para um projeto no Azure DevOps, para que possa gerar compilações.  

1. Crie um novo repositório Git local para o seu projeto ao selecionar **Adicionar ao Controlo de Origem** e **Git** na barra de estado no canto inferior direito do Visual Studio. 

2. Na vista **Sincronização** no **Team Explorer**, selecione o botão **Publicar Repositório Git** em **Push para os Serviços de DevOps do Azure**.

   ![Repositório Git de push](./media/stream-analytics-tools-visual-studio-cicd-vsts/publishgitrepo.png)

3. Verifique o seu e-mail e selecione a sua organização na lista pendente **Domínio dos Serviços de DevOps do Azure**. Introduza o nome do seu repositório e selecione **Publicar repositório**.

   ![Repositório Git de push](./media/stream-analytics-tools-visual-studio-cicd-vsts/publishcode.png)

    A publicação do repositório cria um novo projeto na sua organização com o mesmo nome que o repositório local. Para criar o repositório num projeto existente, clique em **Avançadas** junto ao nome do **Repositório** e selecione um projeto. Pode ver o código no browser ao selecionar **Ver na Web**.
 
## <a name="configure-continuous-delivery-with-azure-devops"></a>Configurar a entrega contínua com o Azure DevOps
Um pipeline de compilação do Azure Pipelines descreve um fluxo de trabalho composto por passos de compilação que são executados sequencialmente. Saiba mais sobre [Pipelines de compilação do Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=vsts&tabs=new-nav). 

Um pipeline de versão do Azure Pipelines descreve um fluxo de trabalho que implementa um pacote de aplicação num cluster. Quando utilizados em conjunto, o pipeline de compilação e o pipeline de versão executam o fluxo de trabalho completo, começando com os ficheiros de origem e terminando com uma aplicação em execução no cluster. Saiba mais sobre [pipelines de versão](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts) do Azure Pipelines.

### <a name="create-a-build-pipeline"></a>Criar um pipeline de compilação
Abra um browser e navegue para o projeto que acabou de criar no [Azure DevOps](https://app.vsaex.visualstudio.com/). 

1. No separador **Compilação e Versão**, selecione **Compilações** e, em seguida **+Novo**.  Selecione **Git dos Serviços de DevOps do Azure** e **Continuar**.
    
    ![Selecionar origem](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-select-source.png)

2. Em **Selecionar um modelo**, clique em **Processo Vazio** para começar com um pipeline vazio.
    
    ![Escolher o modelo de compilação](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-select-template.png)

3. Em **Acionadores**, ative a integração contínua ao selecionar o estado do acionador **Ativar a integração contínua**.  Selecione **Guardar e colocar em fila de espera** para iniciar manualmente uma compilação. 
    
    ![Estado do acionador](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-trigger.png)

4. As compilações também são acionadas após push ou dar entrada. Para verificar o progresso da compilação, mude para o separador **Compilações**.  Depois de verificar se a compilação é executada com êxito, tem de definir um pipeline de versão que implemente a aplicação num cluster. Clique com o botão direito do rato nas reticências junto ao pipeline de compilação e selecione **Editar**.

5.  Em **Tarefas**, introduza "Hosted" como **Fila de agentes**.
    
    ![Selecionar a fila de agentes](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-agent-queue.png) 

6. Em **Fase 1**, clique em **+** e adicione uma tarefa **NuGet**.
    
    ![Adicionar uma tarefa NuGet](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-nuget.png)

7. Expanda **Avançadas** e adicione `$(Build.SourcesDirectory)\packages` ao **Diretório de destino**. Mantenha a predefinição dos restantes valores de configuração do NuGet.

   ![Configurar a tarefa NuGet](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-nuget-config.png)

8. Em **Fase 1**, clique em **+** e adicione uma tarefa **MSBuild**.

   ![Adicionar uma tarefa MSBuild](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-msbuild-task.png)

9. Altere os **Argumentos de MSBuild** para os seguintes:

   ```
   /p:CompilerTaskAssemblyFile="Microsoft.WindowsAzure.StreamAnalytics.Common.CompileService.dll"  /p:ASATargetsFilePath="$(Build.SourcesDirectory)\packages\Microsoft.Azure.StreamAnalytics.CICD.1.0.0\build\StreamAnalytics.targets"
   ```

   ![Configurar a tarefa MSBuild](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-msbuild.png)

10. Em **Fase 1**, clique em **+** e adicione uma tarefa **Implementação do Grupo de Recursos do Azure**. 
    
    ![Adicionar uma tarefa de Implementação do Grupo de Recursos do Azure](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-deploy.png)

11. Expanda **Detalhes do Azure** e preencha a configuração com o seguinte:
    
    |**Definição**  |**Valor sugerido**  |
    |---------|---------|
    |Subscrição  |  Escolha a sua subscrição.   |
    |Ação  |  Criar ou atualizar o grupo de recursos   |
    |Grupo de Recursos  |  Introduza um nome de grupo de recursos.   |
    |Modelo  | [Caminho da solução]\bin\Debug\Deploy\\[Nome do projeto].JobTemplate.json   |
    |Parâmetros do modelo  | [Caminho da solução]\bin\Debug\Deploy\\[Nome do projeto].JobTemplate.parameters.json   |
    |Substituir os parâmetros do modelo  | Escreva os parâmetros do modelo a substituir na caixa de texto. Exemplo, –storageName fabrikam –adminUsername $(vmusername) -adminPassword $(password) –azureKeyVaultName $(fabrikamFibre). Esta propriedade é opcional, mas a compilação resultará em erros se os parâmetros de chave não forem substituídos.    |
    
    ![Definir as propriedades](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-deploy-2.png)

12. Clique em **Guardar e Colocar em Fila** para testar o pipeline de compilação.
    
    ![Definir os parâmetros de substituição](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-save-queue.png)

### <a name="failed-build-process"></a>Processo de compilação falhado
Poderá receber erros para parâmetros de implementação nulos se não substituir os parâmetros do modelo na tarefa **Implementação do Grupo de Recursos do Azure** do pipeline de compilação. Regresse ao pipeline de compilação e substitua os parâmetros nulos para resolver o erro.

   ![Processo de compilação falhado](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-process-failed.png)

### <a name="commit-and-push-changes-to-trigger-a-release"></a>Consolidar e emitir alterações para acionar uma versão
Verifique se o pipeline de integração contínua está a funcionar ao dar entrada de algumas alterações de código no Azure DevOps.    

À medida que escreve o código, as alterações são automaticamente controladas pelo Visual Studio. Para consolidar as alterações ao repositório Git local, selecione o ícone de alterações pendentes na barra de estado, na parte inferior direita.

1. Na vista **Alterações**, no Team Explorer, adicione uma mensagem que descreva a atualização e consolide as alterações.

    ![Consolidar e emitir alterações](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-push-changes.png)

2. Selecione o ícone da barra de estado de alterações não publicadas ou a vista de Sincronização no Team Explorer. Selecione **Push** para atualizar o código no Azure DevOps.

    ![Consolidar e emitir alterações](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-push-changes-2.png)

A emissão das alterações aos Serviços de DevOps do Azure aciona automaticamente uma compilação.  Quando o pipeline de compilação for concluído com êxito, é criada automaticamente uma versão e começa a atualizar a tarefa no cluster.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, elimine o grupo de recursos, a tarefa de transmissão em fluxo e todos os recursos relacionados. A eliminação da tarefa evita a faturação das unidades de transmissão em fluxo consumidas pela tarefa. Se estiver a planear utilizar a tarefa no futuro, pode pará-la e reiniciá-la mais tarde, quando for necessário. Se não quiser continuar a utilizar esta tarefa, elimine todos os recursos criados por este tutorial ao utilizar os seguintes passos:

1. No menu do lado esquerdo do portal do Azure, clique em **Grupos de recursos** e, em seguida, clique no nome de recurso que criou.  
2. Na página do grupo de recursos, clique em **Eliminar**, escreva o nome do recurso a eliminar na caixa de texto e, em seguida, clique em **Eliminar**.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre como utilizar as ferramentas do Azure Stream Analytics para Visual Studio para configurar um processo de integração e implementação contínuas, avance para o artigo de pipeline de CI/CD de configuração:

> [!div class="nextstepaction"]
> [Integrar e desenvolver continuamente as ferramentas do Stream Analytics](stream-analytics-tools-for-visual-studio-cicd.md)
