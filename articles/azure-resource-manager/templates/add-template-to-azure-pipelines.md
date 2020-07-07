---
title: CI/CD com gasodutos e modelos Azure
description: Descreve como configurar a integração contínua em Pipelines Azure utilizando projetos de implementação do Grupo de Recursos Azure em Estúdio Visual para implementar modelos de Gestor de Recursos.
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: d8eff1c7efae319106eb8a85af7823a820a0da39
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82084656"
---
# <a name="integrate-arm-templates-with-azure-pipelines"></a>Integrar modelos ARM com Gasodutos Azure

O Visual Studio fornece o projeto Azure Resource Group para criar modelos Azure Resource Manager (ARM) e implantá-los na sua subscrição Azure. Pode integrar este projeto com Pipelines Azure para integração contínua e implantação contínua (CI/CD).

Existem duas formas de implementar modelos com gasodutos Azure:

* **Adicionar tarefa que executa um script Azure PowerShell**. Esta opção tem a vantagem de proporcionar consistência ao longo do ciclo de vida do desenvolvimento porque você usa o mesmo script que está incluído no projeto Visual Studio (Deploy-AzureResourceGroup.ps1). O script encena artefactos do seu projeto para uma conta de armazenamento a que o Gestor de Recursos pode aceder. Os artefactos são itens no seu projeto, tais como modelos ligados, scripts e binários de aplicações. Em seguida, o script implementa o modelo.

* **Adicionar tarefas para copiar e implementar tarefas**. Esta opção oferece uma alternativa conveniente ao script do projeto. Configura duas tarefas no oleoduto. Uma tarefa encena os artefactos e a outra tarefa implementa o modelo.

Este artigo mostra ambas as abordagens.

## <a name="prepare-your-project"></a>Prepare o seu projeto

Este artigo pressupõe que o seu projeto Visual Studio e a organização Azure DevOps estão prontos para criar o pipeline. Os seguintes passos mostram como se certificar de que está pronto:

* Tem uma organização da Azure DevOps. Se não tiver um, [crie um de graça.](/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops) Se a sua equipa já tem uma organização Azure DevOps, certifique-se de que é administrador do projeto Azure DevOps que pretende utilizar.

* Configuraste uma [ligação de serviço](/azure/devops/pipelines/library/connect-to-azure?view=azure-devops) à tua subscrição do Azure. As tarefas no gasoduto executam sob a identidade do diretor de serviço. Para obter medidas para criar a ligação, consulte [Criar um projeto DevOps](deployment-tutorial-pipeline.md#create-a-devops-project).

* Você tem um projeto visual Studio que foi criado a partir do modelo de arranque **do Grupo de Recursos Azure.** Para obter informações sobre a criação desse tipo de projeto, consulte [criar e implementar grupos de recursos Azure através do Visual Studio.](create-visual-studio-deployment-project.md)

* O seu projeto Visual Studio está [ligado a um projeto Azure DevOps.](/azure/devops/repos/git/share-your-code-in-git-vs-2017?view=azure-devops)

## <a name="create-pipeline"></a>Criar pipeline

1. Se ainda não adicionou um oleoduto, tem de criar um novo oleoduto. Da sua organização Azure DevOps, selecione **Pipelines** e **New pipeline**.

   ![Adicione novo oleoduto](./media/add-template-to-azure-pipelines/new-pipeline.png)

1. Especifique onde o seu código está armazenado. A imagem a seguir mostra a seleção de **Azure Repos Git**.

   ![Selecione fonte de código](./media/add-template-to-azure-pipelines/select-source.png)

1. A partir dessa fonte, selecione o repositório que tem o código para o seu projeto.

   ![Selecione repositório](./media/add-template-to-azure-pipelines/select-repo.png)

1. Selecione o tipo de oleoduto para criar. Pode selecionar **o pipeline Starter**.

   ![Selecione o oleoduto](./media/add-template-to-azure-pipelines/select-pipeline.png)

Está pronto para adicionar uma tarefa Azure PowerShell ou o ficheiro de cópia e implementar tarefas.

## <a name="azure-powershell-task"></a>Tarefa Azure PowerShell

Esta secção mostra como configurar a implementação contínua utilizando uma única tarefa que executa o script PowerShell no seu projeto. O seguinte ficheiro YAML cria uma [tarefa Azure PowerShell](/azure/devops/pipelines/tasks/deploy/azure-powershell?view=azure-devops):

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

Quando define a `AzurePowerShell@3` tarefa, o gasoduto utiliza comandos do módulo AzureRM para autenticar a ligação. Por predefinição, o script PowerShell no projeto Visual Studio utiliza o módulo AzureRM. Se atualizou o seu script para utilizar o [módulo Az,](/powershell/azure/new-azureps-module-az)desate a tarefa para `AzurePowerShell@4` .

```yaml
steps:
- task: AzurePowerShell@4
```

Para `azureSubscription` , fornecer o nome da ligação de serviço que criou.

```yaml
inputs:
    azureSubscription: '<your-connection-name>'
```

Para `scriptPath` , fornecer o caminho relativo do ficheiro do pipeline para o seu script. Pode olhar no seu repositório para ver o caminho.

```yaml
ScriptPath: '<your-relative-path>/<script-file-name>.ps1'
```

Se não precisar de encenar artefactos, basta passar o nome e a localização de um grupo de recursos para usar para implantação. O script do Estúdio Visual cria o grupo de recursos se já não existir.

```yaml
ScriptArguments: -ResourceGroupName '<resource-group-name>' -ResourceGroupLocation '<location>'
```

Se precisar de encenar artefactos para uma conta de armazenamento existente, utilize:

```yaml
ScriptArguments: -ResourceGroupName '<resource-group-name>' -ResourceGroupLocation '<location>' -UploadArtifacts -ArtifactStagingDirectory '$(Build.StagingDirectory)' -StorageAccountName '<your-storage-account>'
```

Agora, que entende como criar a tarefa, vamos passar pelos passos para editar o oleoduto.

1. Abra o seu oleoduto e substitua o conteúdo pelo seu YAML:

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

1. Forneça uma mensagem para o compromisso, e comprometa-se diretamente a **dominar**.

1. Quando selecionar **Save,** o gasoduto de construção é executado automaticamente. Volte ao resumo para o seu oleoduto de construção, e observe o estado.

   ![Ver resultados](./media/add-template-to-azure-pipelines/view-results.png)

Pode selecionar o pipeline atualmente em execução para ver detalhes sobre as tarefas. Quando terminar, vê os resultados de cada passo.

## <a name="copy-and-deploy-tasks"></a>Copiar e implementar tarefas

Esta secção mostra como configurar a implementação contínua utilizando duas tarefas para encenar os artefactos e implantar o modelo.

O yaml seguinte mostra a tarefa de [cópia de ficheiro Azure](/azure/devops/pipelines/tasks/deploy/azure-file-copy?view=azure-devops):

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

Existem várias partes desta tarefa para rever para o seu ambiente. `SourcePath`Indica a localização dos artefactos relativos ao ficheiro do gasoduto. Neste exemplo, os ficheiros existem numa pasta chamada `AzureResourceGroup1` que era o nome do projeto.

```yaml
SourcePath: '<path-to-artifacts>'
```

Para `azureSubscription` , fornecer o nome da ligação de serviço que criou.

```yaml
azureSubscription: '<your-connection-name>'
```

Para armazenamento e nome do recipiente, forneça os nomes da conta de armazenamento e do recipiente que pretende utilizar para armazenar os artefactos. A conta de armazenamento deve existir.

```yaml
storage: '<your-storage-account-name>'
ContainerName: '<container-name>'
```

O seguinte YAML mostra a tarefa de implementação do [modelo Azure Resource Manager](https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureResourceManagerTemplateDeploymentV3/README.md):

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

Existem várias partes desta tarefa para rever para o seu ambiente.

- `deploymentScope`: Selecione o âmbito de implementação das opções: `Management Group` e `Subscription` `Resource Group` . Utilize **o Grupo de Recursos** nesta caminhada. Para saber mais sobre os âmbitos, consulte os [âmbitos de implementação](deploy-rest.md#deployment-scope).

- `ConnectedServiceName`: Fornecer o nome da ligação de serviço que criou.

    ```yaml
    ConnectedServiceName: '<your-connection-name>'
    ```

- `subscriptionName`: Fornecer o ID de assinatura alvo. Esta propriedade aplica-se apenas ao âmbito de implementação do Grupo de Recursos e ao âmbito de implementação da subscrição.

- `resourceGroupName`e `location` : fornecer o nome e a localização do grupo de recursos para o quais pretende implementar. A tarefa cria o grupo de recursos se não existir.

    ```yaml
    resourceGroupName: '<resource-group-name>'
    location: '<location>'
    ```

A tarefa de implementação liga-se a um modelo nomeado `WebSite.json` e a um ficheiro de parâmetros nomeado WebSite.parameters.jsligado. Use os nomes do seu modelo e ficheiros de parâmetros.

Agora, que entende como criar as tarefas, vamos passar pelos passos para editar o oleoduto.

1. Abra o seu oleoduto e substitua o conteúdo pelo seu YAML:

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

1. Forneça uma mensagem para o compromisso, e comprometa-se diretamente a **dominar**.

1. Quando selecionar **Save,** o gasoduto de construção é executado automaticamente. Volte ao resumo para o seu oleoduto de construção, e observe o estado.

   ![Ver resultados](./media/add-template-to-azure-pipelines/view-results.png)

Pode selecionar o pipeline atualmente em execução para ver detalhes sobre as tarefas. Quando terminar, vê os resultados de cada passo.

## <a name="next-steps"></a>Passos seguintes

Para o processo passo a passo sobre a utilização de Gasodutos Azure com modelos ARM, consulte [Tutorial: Integração contínua dos modelos do Gestor de Recursos Azure com Gasodutos Azure](deployment-tutorial-pipeline.md).
