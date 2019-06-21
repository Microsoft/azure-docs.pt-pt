---
title: CI/CD com modelos Azure Resource Manager e Pipelines do Azure
description: Descreve como configurar a integração contínua em Pipelines do Azure ao utilizar projetos de implantação do grupo de recursos do Azure no Visual Studio para implementar modelos do Resource Manager.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: article
ms.date: 06/12/2019
ms.author: tomfitz
ms.openlocfilehash: b70b38904c0373c53c3731dd0442511116d9c4de
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67191463"
---
# <a name="integrate-resource-manager-templates-with-azure-pipelines"></a>Integre modelos do Resource Manager com Pipelines do Azure

O Visual Studio fornece o projeto do grupo de recursos do Azure para criar modelos e implantá-las na sua subscrição do Azure. Pode integrar este projeto com Pipelines do Azure para integração contínua e implementação contínuas (CI/CD).

Existem duas formas de implementar modelos com Pipelines do Azure:

* **Adicionar tarefa executa um script do Azure PowerShell**. Esta opção tem a vantagem de fornecer consistência ao longo do ciclo de vida de desenvolvimento, uma vez que utiliza o mesmo script que está incluído no projeto do Visual Studio (Deploy-AzureResourceGroup.ps1). Os script fases artefatos do seu projeto para uma conta de armazenamento do Resource Manager podem aceder. Artefactos são itens no seu projeto, como modelos ligados, scripts e binários de aplicativo. Em seguida, o script implanta o modelo.

* **Adicionar tarefas ao copiar e implementar tarefas**. Essa opção oferece uma alternativa conveniente para o script de projeto. Configurar duas tarefas no pipeline. Uma tarefa prepara os artefatos e a outra tarefa implanta o modelo.

Este artigo mostra ambas as abordagens.

## <a name="prepare-your-project"></a>Preparar o projeto

Este artigo pressupõe que o projeto do Visual Studio e a organização de DevOps do Azure estão prontos para a criação do pipeline. Os passos seguintes mostram como tornar-se de que está pronto:

* Tem uma organização de DevOps do Azure. Se não tiver uma, [criar uma gratuitamente](/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops). Se sua equipe já tiver uma organização de DevOps do Azure, certifique-se de que for um administrador do projeto de DevOps do Azure que pretende utilizar.

* Configurou uma [ligação do serviço](/azure/devops/pipelines/library/connect-to-azure?view=azure-devops) à sua subscrição do Azure. As tarefas no pipeline são executadas sob a identidade do serviço principal. Para obter passos criar a ligação, veja [criar um projeto de DevOps](resource-manager-tutorial-use-azure-pipelines.md#create-a-devops-project).

* Tem um projeto do Visual Studio que foi criado a partir da **grupo de recursos do Azure** modelo de arranque. Para obter informações sobre como criar esse tipo de projeto, consulte [criar e implementar grupos de recursos do Azure através do Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

* É de seu projeto do Visual Studio [ligado a um projeto de DevOps do Azure](/azure/devops/repos/git/share-your-code-in-git-vs-2017?view=azure-devops).

## <a name="create-pipeline"></a>Criar pipeline

1. Se ainda não adicionou um pipeline anteriormente, tem de criar um novo pipeline. A partir da sua organização de DevOps do Azure, selecione **Pipelines** e **novo pipeline**.

   ![Adicionar novo pipeline](./media/vs-resource-groups-project-devops-pipelines/new-pipeline.png)

1. Especifique onde o seu código está armazenado. A imagem seguinte mostra a selecionar **Azure repositórios Git**.

   ![Selecione o código-fonte](./media/vs-resource-groups-project-devops-pipelines/select-source.png)

1. A partir dessa origem, selecione o repositório que tem o código para o seu projeto.

   ![Selecione o repositório](./media/vs-resource-groups-project-devops-pipelines/select-repo.png)

1. Selecione o tipo de pipeline para criar. Pode selecionar **Starter pipeline**.

   ![Selecione o pipeline](./media/vs-resource-groups-project-devops-pipelines/select-pipeline.png)

Está pronto para adicionar uma tarefa do Azure PowerShell ou o ficheiro de cópia e implementar tarefas.

## <a name="azure-powershell-task"></a>Tarefas do PowerShell do Azure

Esta secção mostra como configurar a implementação contínua com uma única tarefa que executa o script do PowerShell no seu projeto. O ficheiro YAML seguinte cria um [tarefas de Azure PowerShell](/azure/devops/pipelines/tasks/deploy/azure-powershell?view=azure-devops):

```yaml
pool:
  name: Hosted Windows 2019 with VS2019
  demands: azureps

steps:
- task: AzurePowerShell@3
  inputs:
    azureSubscription: 'demo-deploy-sp'
    ScriptPath: 'AzureResourceGroupDemo/Deploy-AzureResourceGroup.ps1'
    ScriptArguments: -ResourceGroupName 'demogroup' -ResourceGroupLocation 'centralus' 
    azurePowerShellVersion: LatestVersion
```

Se definir a tarefa como `AzurePowerShell@3`, o pipeline utiliza comandos do módulo AzureRM, para autenticar a ligação. Por predefinição, o script do PowerShell no projeto do Visual Studio utiliza o módulo AzureRM. Se atualizou o script para utilizar o [módulo Az](/powershell/azure/new-azureps-module-az), defina a tarefa como `AzurePowerShell@4`.

```yaml
steps:
- task: AzurePowerShell@4
```

Para `azureSubscription`, forneça o nome da ligação de serviço que criou.

```yaml
inputs:
    azureSubscription: '<your-connection-name>'
```

Para `scriptPath`, forneça o caminho relativo do ficheiro de pipeline para o script. Pode ver no seu repositório para ver o caminho.

```yaml
ScriptPath: '<your-relative-path>/<script-file-name>.ps1'
```

Se não precisar de artefactos de fase, basta passe o nome e a localização de um grupo de recursos a serem usadas na implantação. O script do Visual Studio cria o grupo de recursos, se ainda não exista.

```yaml
ScriptArguments: -ResourceGroupName '<resource-group-name>' -ResourceGroupLocation '<location>'
```

Se precisar de artefactos de fase a uma conta de armazenamento existente, utilize:

```yaml
ScriptArguments: -ResourceGroupName '<resource-group-name>' -ResourceGroupLocation '<location>' -UploadArtifacts -ArtifactStagingDirectory '$(Build.StagingDirectory)' -StorageAccountName '<your-storage-account>'
```

Agora, o que entenda como criar a tarefa, vamos percorrer os passos para editar o pipeline.

1. Abra o seu pipeline e substitua os conteúdos com sua YAML:

   ```yaml
   pool:
     name: Hosted Windows 2019 with VS2019
     demands: azureps

   steps:
   - task: AzurePowerShell@3
     inputs:
       azureSubscription: 'demo-deploy-sp'
       ScriptPath: 'AzureResourceGroupDemo/Deploy-AzureResourceGroup.ps1'
       ScriptArguments: -ResourceGroupName 'demogroup' -ResourceGroupLocation 'centralus' -UploadArtifacts -ArtifactStagingDirectory '$(Build.StagingDirectory)' -StorageAccountName 'stage3a4176e058d34bb88cc'
       azurePowerShellVersion: LatestVersion
   ```

1. Selecione **Guardar**.

   ![Guardar o pipeline](./media/vs-resource-groups-project-devops-pipelines/save-pipeline.png)

1. Forneça uma mensagem para a consolidação e consolidar diretamente **mestre**.

1. Quando seleciona **guardar**, o pipeline de compilação é executado automaticamente. Voltar ao resumo para seu pipeline de compilação e veja o estado.

   ![Ver resultados](./media/vs-resource-groups-project-devops-pipelines/view-results.png)

Pode selecionar o pipeline para ver detalhes sobre as tarefas atualmente em execução. Quando terminar, verá os resultados para cada passo.

## <a name="copy-and-deploy-tasks"></a>Copie e implementar tarefas

Esta secção mostra como configurar a implementação contínua através de uma duas tarefas para os artefactos de testar e implementar o modelo. 

A seguir mostra YAML a [tarefas de cópia de ficheiros do Azure](/azure/devops/pipelines/tasks/deploy/azure-file-copy?view=azure-devops):

```yaml
- task: AzureFileCopy@3
  displayName: 'Stage files'
  inputs:
    SourcePath: 'AzureResourceGroup1'
    azureSubscription: 'demo-deploy-sp'
    Destination: 'AzureBlob'
    storage: 'stage3a4176e058d34bb88cc'
    ContainerName: 'democontainer'
    outputStorageUri: 'artifactsLocation'
    outputStorageContainerSasToken: 'artifactsLocationSasToken'
    sasTokenTimeOutInMinutes: '240'
```

Existem várias partes desta tarefa revisar para o seu ambiente. O `SourcePath` indica a localização dos artefatos relativo ao ficheiro de pipeline. Neste exemplo, os ficheiros de existir numa pasta chamada `AzureResourceGroup1` qual era o nome do projeto.

```yaml
SourcePath: '<path-to-artifacts>'
```

Para `azureSubscription`, forneça o nome da ligação de serviço que criou.

```yaml
azureSubscription: '<your-connection-name>'
```

Para o nome de armazenamento e um contentor, forneça os nomes da conta de armazenamento e o contentor que pretende utilizar para armazenar os artefactos. A conta de armazenamento tem de existir.

```yaml
storage: '<your-storage-account-name>'
ContainerName: '<container-name>'
```

A seguir mostra YAML a [tarefas de implementação do grupo de recursos do Azure](/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment?view=azure-devops):

```yaml
- task: AzureResourceGroupDeployment@2
  displayName: 'Deploy template'
  inputs:
    azureSubscription: 'demo-deploy-sp'
    resourceGroupName: 'demogroup'
    location: 'centralus'
    templateLocation: 'URL of the file'
    csmFileLink: '$(artifactsLocation)WebSite.json$(artifactsLocationSasToken)'
    csmParametersFileLink: '$(artifactsLocation)WebSite.parameters.json$(artifactsLocationSasToken)'
    overrideParameters: '-_artifactsLocation $(artifactsLocation) -_artifactsLocationSasToken "$(artifactsLocationSasToken)"'
```

Existem várias partes desta tarefa revisar para o seu ambiente. Para `azureSubscription`, forneça o nome da ligação de serviço que criou.

```yaml
azureSubscription: '<your-connection-name>'
```

Para `resourceGroupName` e `location`, forneça o nome e a localização do grupo de recursos que pretende implementar. A tarefa cria o grupo de recursos se não existir.

```yaml
resourceGroupName: '<resource-group-name>'
location: '<location>'
```

As ligações de tarefas de implementação para um modelo com o nome `WebSite.json` e um ficheiro de parâmetros com o nome WebSite.parameters.json. Utilize os nomes dos seus arquivos de modelo e o parâmetro.

Agora, o que entenda como criar as tarefas, vamos percorrer os passos para editar o pipeline.

1. Abra o seu pipeline e substitua os conteúdos com sua YAML:

   ```yaml
   pool:
     name: Hosted Windows 2019 with VS2019

   steps:
   - task: AzureFileCopy@3
     displayName: 'Stage files'
     inputs:
       SourcePath: 'AzureResourceGroup1'
       azureSubscription: 'demo-deploy-sp'
       Destination: 'AzureBlob'
       storage: 'stage3a4176e058d34bb88cc'
       ContainerName: 'democontainer'
       outputStorageUri: 'artifactsLocation'
       outputStorageContainerSasToken: 'artifactsLocationSasToken'
       sasTokenTimeOutInMinutes: '240'
   - task: AzureResourceGroupDeployment@2
     displayName: 'Deploy template'
     inputs:
       azureSubscription: 'demo-deploy-sp'
       resourceGroupName: demogroup
       location: 'centralus'
       templateLocation: 'URL of the file'
       csmFileLink: '$(artifactsLocation)WebSite.json$(artifactsLocationSasToken)'
       csmParametersFileLink: '$(artifactsLocation)WebSite.parameters.json$(artifactsLocationSasToken)'
       overrideParameters: '-_artifactsLocation $(artifactsLocation) -_artifactsLocationSasToken "$(artifactsLocationSasToken)"'
   ```

1. Selecione **Guardar**.

1. Forneça uma mensagem para a consolidação e consolidar diretamente **mestre**.

1. Quando seleciona **guardar**, o pipeline de compilação é executado automaticamente. Voltar ao resumo para seu pipeline de compilação e veja o estado.

   ![Ver resultados](./media/vs-resource-groups-project-devops-pipelines/view-results.png)

Pode selecionar o pipeline para ver detalhes sobre as tarefas atualmente em execução. Quando terminar, verá os resultados para cada passo.

## <a name="next-steps"></a>Passos Seguintes

Para o processo passo a passo sobre a utilização de Pipelines do Azure com modelos do Resource Manager, consulte [Tutorial: Integração contínua de modelos do Azure Resource Manager com Pipelines do Azure](resource-manager-tutorial-use-azure-pipelines.md).
