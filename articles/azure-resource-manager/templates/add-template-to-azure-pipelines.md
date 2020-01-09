---
title: CI/CD com Azure Pipelines e modelos
description: Descreve como configurar a integração contínua no Azure Pipelines usando projetos de implantação do grupo de recursos do Azure no Visual Studio para implantar modelos do Resource Manager.
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: 6f5d4846d32b4880ccd3fbd82f062f57948ac15a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75478269"
---
# <a name="integrate-resource-manager-templates-with-azure-pipelines"></a>Integrar modelos do Resource Manager com o Azure Pipelines

O Visual Studio fornece o projeto do grupo de recursos do Azure para criar modelos e implantá-los em sua assinatura do Azure. Você pode integrar esse projeto com Azure Pipelines para integração contínua e implantação contínua (CI/CD).

Há duas maneiras de implantar modelos com Azure Pipelines:

* **Adicionar tarefa que executa um script de Azure PowerShell**. Essa opção tem a vantagem de fornecer consistência em todo o ciclo de vida de desenvolvimento, pois você usa o mesmo script que está incluído no projeto do Visual Studio (Deploy-azureresourcegroup. ps1). O script prepara os artefatos do seu projeto para uma conta de armazenamento que o Gerenciador de recursos pode acessar. Os artefatos são itens em seu projeto, como modelos vinculados, scripts e binários de aplicativos. Em seguida, o script implanta o modelo.

* **Adicionar tarefas para copiar e implantar tarefas**. Essa opção oferece uma alternativa conveniente ao script do projeto. Você configura duas tarefas no pipeline. Uma tarefa prepara os artefatos e a outra tarefa implanta o modelo.

Este artigo mostra as duas abordagens.

## <a name="prepare-your-project"></a>Preparar seu projeto

Este artigo pressupõe que seu projeto do Visual Studio e a organização do Azure DevOps estejam prontos para criar o pipeline. As etapas a seguir mostram como se certificar de que você está pronto:

* Você tem uma organização de DevOps do Azure. Se você não tiver um, [crie um gratuitamente](/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops). Se sua equipe já tiver uma organização de DevOps do Azure, verifique se você é um administrador do projeto DevOps do Azure que deseja usar.

* Você configurou uma [conexão de serviço](/azure/devops/pipelines/library/connect-to-azure?view=azure-devops) para sua assinatura do Azure. As tarefas no pipeline são executadas sob a identidade da entidade de serviço. Para obter as etapas para criar a conexão, consulte [criar um projeto DevOps](template-tutorial-use-azure-pipelines.md#create-a-devops-project).

* Você tem um projeto do Visual Studio que foi criado no modelo de início do **grupo de recursos do Azure** . Para obter informações sobre como criar esse tipo de projeto, consulte [criando e implantando grupos de recursos do Azure por meio do Visual Studio](create-visual-studio-deployment-project.md).

* Seu projeto do Visual Studio está [conectado a um projeto DevOps do Azure](/azure/devops/repos/git/share-your-code-in-git-vs-2017?view=azure-devops).

## <a name="create-pipeline"></a>Criar pipeline

1. Se você ainda não adicionou um pipeline anteriormente, precisará criar um novo pipeline. Na organização do Azure DevOps, selecione **pipelines** e **novo pipeline**.

   ![Adicionar novo pipeline](./media/add-template-to-azure-pipelines/new-pipeline.png)

1. Especifique onde o código está armazenado. A imagem a seguir mostra a seleção de **Azure Repos git**.

   ![Selecionar fonte de código](./media/add-template-to-azure-pipelines/select-source.png)

1. Nessa fonte, selecione o repositório que tem o código para seu projeto.

   ![Selecionar repositório](./media/add-template-to-azure-pipelines/select-repo.png)

1. Selecione o tipo de pipeline a ser criado. Você pode selecionar **pipeline de início**.

   ![Selecionar pipeline](./media/add-template-to-azure-pipelines/select-pipeline.png)

Você está pronto para adicionar uma tarefa Azure PowerShell ou copiar arquivo e implantar tarefas.

## <a name="azure-powershell-task"></a>Azure PowerShell tarefa

Esta seção mostra como configurar a implantação contínua usando uma única tarefa que executa o script do PowerShell em seu projeto. O seguinte arquivo YAML cria uma [tarefa Azure PowerShell](/azure/devops/pipelines/tasks/deploy/azure-powershell?view=azure-devops):

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

Quando você define a tarefa como `AzurePowerShell@3`, o pipeline usa comandos do módulo AzureRM para autenticar a conexão. Por padrão, o script do PowerShell no projeto do Visual Studio usa o módulo AzureRM. Se você atualizou o script para usar o [módulo AZ](/powershell/azure/new-azureps-module-az), defina a tarefa como `AzurePowerShell@4`.

```yaml
steps:
- task: AzurePowerShell@4
```

Para `azureSubscription`, forneça o nome da conexão de serviço que você criou.

```yaml
inputs:
    azureSubscription: '<your-connection-name>'
```

Para `scriptPath`, forneça o caminho relativo do arquivo de pipeline para o seu script. Você pode procurar no repositório para ver o caminho.

```yaml
ScriptPath: '<your-relative-path>/<script-file-name>.ps1'
```

Se você não precisar preparar artefatos, apenas passe o nome e o local de um grupo de recursos a ser usado para a implantação. O script do Visual Studio criará o grupo de recursos se ele ainda não existir.

```yaml
ScriptArguments: -ResourceGroupName '<resource-group-name>' -ResourceGroupLocation '<location>'
```

Se você precisar preparar artefatos para uma conta de armazenamento existente, use:

```yaml
ScriptArguments: -ResourceGroupName '<resource-group-name>' -ResourceGroupLocation '<location>' -UploadArtifacts -ArtifactStagingDirectory '$(Build.StagingDirectory)' -StorageAccountName '<your-storage-account>'
```

Agora que você entende como criar a tarefa, vamos percorrer as etapas para editar o pipeline.

1. Abra seu pipeline e substitua o conteúdo por seu YAML:

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

   ![Guardar o pipeline](./media/add-template-to-azure-pipelines/save-pipeline.png)

1. Forneça uma mensagem para a confirmação e confirme diretamente no **mestre**.

1. Quando você seleciona **salvar**, o pipeline de compilação é executado automaticamente. Volte para o resumo do pipeline de compilação e veja o status.

   ![Ver resultados](./media/add-template-to-azure-pipelines/view-results.png)

Você pode selecionar o pipeline em execução no momento para ver detalhes sobre as tarefas. Quando ele for concluído, você verá os resultados de cada etapa.

## <a name="copy-and-deploy-tasks"></a>Copiar e implantar tarefas

Esta seção mostra como configurar a implantação contínua usando duas tarefas para preparar os artefatos e implantar o modelo.

O YAML a seguir mostra a [tarefa cópia de arquivo do Azure](/azure/devops/pipelines/tasks/deploy/azure-file-copy?view=azure-devops):

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

Há várias partes dessa tarefa a serem revisadas para o seu ambiente. O `SourcePath` indica o local dos artefatos em relação ao arquivo de pipeline. Neste exemplo, os arquivos existem em uma pasta chamada `AzureResourceGroup1` que era o nome do projeto.

```yaml
SourcePath: '<path-to-artifacts>'
```

Para `azureSubscription`, forneça o nome da conexão de serviço que você criou.

```yaml
azureSubscription: '<your-connection-name>'
```

Para o armazenamento e o nome do contêiner, forneça os nomes da conta de armazenamento e do contêiner que você deseja usar para armazenar os artefatos. A conta de armazenamento deve existir.

```yaml
storage: '<your-storage-account-name>'
ContainerName: '<container-name>'
```

O YAML a seguir mostra a [tarefa de implantação de modelo de Azure Resource Manager](https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureResourceManagerTemplateDeploymentV3/README.md):

```yaml
- task: AzureResourceGroupDeployment@2
  displayName: 'Deploy template'
  inputs:
    deploymentScope: 'Resource Group'
    ConnectedServiceName: 'demo-deploy-sp'
    subscriptionName: '01234567-89AB-CDEF-0123-4567890ABCDEF'
    action: 'Create Or Update Resource Group'
    resourceGroupName: 'demogroup'
    location: 'Central US'
    templateLocation: 'URL of the file'
    csmFileLink: '$(artifactsLocation)WebSite.json$(artifactsLocationSasToken)'
    csmParametersFileLink: '$(artifactsLocation)WebSite.parameters.json$(artifactsLocationSasToken)'
    overrideParameters: '-_artifactsLocation $(artifactsLocation) -_artifactsLocationSasToken "$(artifactsLocationSasToken)"'
    deploymentMode: 'Incremental'
```

Há várias partes dessa tarefa a serem revisadas para o seu ambiente.

- `deploymentScope`: selecione o escopo de implantação nas opções: `Management Group`, `Subscription` e `Resource Group`. Use o **grupo de recursos** nesta passagem. Para saber mais sobre os escopos, consulte [escopos de implantação](deploy-rest.md#deployment-scope).

- `ConnectedServiceName`: forneça o nome da conexão de serviço que você criou.

    ```yaml
    ConnectedServiceName: '<your-connection-name>'
    ```

- `subscriptionName`: forneça a ID da assinatura de destino. Essa propriedade só se aplica ao escopo de implantação do grupo de recursos e ao escopo de implantação da assinatura.

- `resourceGroupName` e `location`: forneça o nome e o local do grupo de recursos no qual você deseja implantar. A tarefa cria o grupo de recursos, caso ele não exista.

    ```yaml
    resourceGroupName: '<resource-group-name>'
    location: '<location>'
    ```

A tarefa de implantação é vinculada a um modelo chamado `WebSite.json` e um arquivo de parâmetros chamado WebSite. Parameters. JSON. Use os nomes dos arquivos de modelo e parâmetro.

Agora que você entende como criar as tarefas, vamos percorrer as etapas para editar o pipeline.

1. Abra seu pipeline e substitua o conteúdo por seu YAML:

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
        deploymentScope: 'Resource Group'
        ConnectedServiceName: 'demo-deploy-sp'
        subscriptionName: '01234567-89AB-CDEF-0123-4567890ABCDEF'
        action: 'Create Or Update Resource Group'
        resourceGroupName: 'demogroup'
        location: 'Central US'
        templateLocation: 'URL of the file'
        csmFileLink: '$(artifactsLocation)WebSite.json$(artifactsLocationSasToken)'
        csmParametersFileLink: '$(artifactsLocation)WebSite.parameters.json$(artifactsLocationSasToken)'
        overrideParameters: '-_artifactsLocation $(artifactsLocation) -_artifactsLocationSasToken "$(artifactsLocationSasToken)"'
        deploymentMode: 'Incremental'
   ```

1. Selecione **Guardar**.

1. Forneça uma mensagem para a confirmação e confirme diretamente no **mestre**.

1. Quando você seleciona **salvar**, o pipeline de compilação é executado automaticamente. Volte para o resumo do pipeline de compilação e veja o status.

   ![Ver resultados](./media/add-template-to-azure-pipelines/view-results.png)

Você pode selecionar o pipeline em execução no momento para ver detalhes sobre as tarefas. Quando ele for concluído, você verá os resultados de cada etapa.

## <a name="next-steps"></a>Passos seguintes

Para obter um processo passo a passo sobre como usar Azure Pipelines com modelos do Resource Manager, consulte [tutorial: integração contínua de modelos de Azure Resource Manager com o Azure pipelines](template-tutorial-use-azure-pipelines.md).
