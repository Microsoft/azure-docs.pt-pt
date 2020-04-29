---
title: CI/CD com oleodutos e modelos Azure
description: Descreve como criar uma integração contínua em Pipelines Azure utilizando projetos de implantação do Grupo de Recursos Azure no Estúdio Visual para implementar modelos de Gestor de Recursos.
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: d8eff1c7efae319106eb8a85af7823a820a0da39
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82084656"
---
# <a name="integrate-arm-templates-with-azure-pipelines"></a>Integrar modelos ARM com oleodutos Azure

O Visual Studio fornece o projeto do Grupo de Recursos Azure para criar modelos de Gestor de Recursos Azure (ARM) e implantá-los para a sua subscrição Azure. Pode integrar este projeto com pipelines Azure para integração contínua e implantação contínua (CI/CD).

Existem duas formas de implementar modelos com pipelines Azure:

* **Adicione a tarefa que executa um script Azure PowerShell**. Esta opção tem a vantagem de proporcionar consistência ao longo do ciclo de vida de desenvolvimento porque você usa o mesmo script que está incluído no projeto Estúdio Visual (Deploy-AzureResourceGroup.ps1). O script encena artefactos do seu projeto para uma conta de armazenamento a que o Gestor de Recursos pode aceder. Os artefactos são itens do seu projeto, tais como modelos ligados, scripts e binários de aplicação. Em seguida, o script implementa o modelo.

* **Adicione tarefas para copiar e implementar tarefas**. Esta opção oferece uma alternativa conveniente ao script do projeto. Configura duas tarefas no oleoduto. Uma tarefa encena os artefactos e a outra tarefa implementa o modelo.

Este artigo mostra ambas as abordagens.

## <a name="prepare-your-project"></a>Prepare o seu projeto

Este artigo assume que o seu projeto Visual Studio e a organização Azure DevOps estão prontos para criar o pipeline. Os seguintes passos mostram como se certificar de que está pronto:

* Tens uma organização Azure DevOps. Se não tiver um, [crie um de graça.](/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops) Se a sua equipa já tem uma organização Azure DevOps, certifique-se de ser administrador do projeto Azure DevOps que pretende utilizar.

* Configurou uma [ligação](/azure/devops/pipelines/library/connect-to-azure?view=azure-devops) de serviço à sua subscrição Azure. As tarefas no gasoduto executam sob a identidade do diretor de serviço. Para passos para criar a ligação, consulte [Criar um projeto DevOps](deployment-tutorial-pipeline.md#create-a-devops-project).

* Você tem um projeto Visual Studio que foi criado a partir do modelo de arranque do **Grupo de Recursos Azure.** Para obter informações sobre a criação desse tipo de projeto, consulte [Criar e implantar grupos](create-visual-studio-deployment-project.md)de recursos Azure através do Visual Studio .

* O seu projeto Visual Studio está [ligado a um projeto Azure DevOps.](/azure/devops/repos/git/share-your-code-in-git-vs-2017?view=azure-devops)

## <a name="create-pipeline"></a>Criar pipeline

1. Se ainda não adicionou um oleoduto anteriormente, precisa de criar um novo oleoduto. Da sua organização Azure DevOps, selecione **Pipelines** e **New pipeline**.

   ![Adicione novo oleoduto](./media/add-template-to-azure-pipelines/new-pipeline.png)

1. Especifique onde o seu código está armazenado. A imagem que se segue mostra a seleção **de Azure Repos Git**.

   ![Selecione fonte de código](./media/add-template-to-azure-pipelines/select-source.png)

1. A partir dessa fonte, selecione o repositório que tem o código para o seu projeto.

   ![Selecione repositório](./media/add-template-to-azure-pipelines/select-repo.png)

1. Selecione o tipo de gasoduto para criar. Pode selecionar **o gasoduto Starter**.

   ![Selecione o gasoduto](./media/add-template-to-azure-pipelines/select-pipeline.png)

Está pronto para adicionar uma tarefa Azure PowerShell ou o ficheiro de cópia e implementar tarefas.

## <a name="azure-powershell-task"></a>Tarefa Azure PowerShell

Esta secção mostra como configurar a implantação contínua utilizando uma única tarefa que executa o script PowerShell no seu projeto. O seguinte ficheiro YAML cria uma [tarefa Azure PowerShell:](/azure/devops/pipelines/tasks/deploy/azure-powershell?view=azure-devops)

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

Quando define a `AzurePowerShell@3`tarefa, o gasoduto utiliza comandos do módulo AzureRM para autenticar a ligação. Por padrão, o script PowerShell no projeto Estúdio Visual utiliza o módulo AzureRM. Se atualizou o seu script para utilizar o módulo `AzurePowerShell@4` [Az,](/powershell/azure/new-azureps-module-az)detete a tarefa para .

```yaml
steps:
- task: AzurePowerShell@4
```

Para `azureSubscription`, fornecer o nome da ligação de serviço que criou.

```yaml
inputs:
    azureSubscription: '<your-connection-name>'
```

Para `scriptPath`, fornecer o caminho relativo desde o ficheiro do gasoduto até ao seu script. Pode olhar no seu repositório para ver o caminho.

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

Agora, que entende como criar a tarefa, vamos percorrer os passos para editar o oleoduto.

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

1. Forneça uma mensagem para o compromisso, e comprometa-se diretamente a **dominar.**

1. Quando selecionar **Save,** o gasoduto de construção é executado automaticamente. Volte ao resumo do seu oleoduto de construção, e observe o estado.

   ![Ver resultados](./media/add-template-to-azure-pipelines/view-results.png)

Pode selecionar o pipeline em execução actualpara ver detalhes sobre as tarefas. Quando termina, vê-se os resultados de cada passo.

## <a name="copy-and-deploy-tasks"></a>Copiar e implementar tarefas

Esta secção mostra como configurar a implantação contínua utilizando duas tarefas para encenar os artefactos e implantar o modelo.

O Seguinte YAML mostra a tarefa de cópia de [ficheiros Azure:](/azure/devops/pipelines/tasks/deploy/azure-file-copy?view=azure-devops)

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

Há várias partes desta tarefa para rever para o seu ambiente. Indica `SourcePath` a localização dos artefactos relativos ao ficheiro do gasoduto. Neste exemplo, os ficheiros existem numa pasta com `AzureResourceGroup1` o nome do nome do projeto.

```yaml
SourcePath: '<path-to-artifacts>'
```

Para `azureSubscription`, fornecer o nome da ligação de serviço que criou.

```yaml
azureSubscription: '<your-connection-name>'
```

Para o nome do armazenamento e do contentor, forneça os nomes da conta de armazenamento e do recipiente que pretende utilizar para armazenar os artefactos. A conta de armazenamento deve existir.

```yaml
storage: '<your-storage-account-name>'
ContainerName: '<container-name>'
```

O Seguinte YAML mostra a tarefa de implantação do modelo do Gestor de [Recursos Azure:](https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureResourceManagerTemplateDeploymentV3/README.md)

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

Há várias partes desta tarefa para rever para o seu ambiente.

- `deploymentScope`: Selecione o âmbito `Management Group`de `Subscription` `Resource Group`implantação a partir das opções: , e . Use **o Grupo de Recursos** nesta caminhada. Para saber mais sobre os âmbitos, consulte [os âmbitos de implantação.](deploy-rest.md#deployment-scope)

- `ConnectedServiceName`: Forneça o nome da ligação de serviço que criou.

    ```yaml
    ConnectedServiceName: '<your-connection-name>'
    ```

- `subscriptionName`: Fornecer o ID de subscrição do alvo. Esta propriedade aplica-se apenas ao âmbito de implementação do Grupo de Recursos e ao âmbito de implementação de subscrições.

- `resourceGroupName`e `location`: fornecer o nome e a localização do grupo de recursos a que pretende implantar. A tarefa cria o grupo de recursos se não existir.

    ```yaml
    resourceGroupName: '<resource-group-name>'
    location: '<location>'
    ```

A tarefa de implementação `WebSite.json` liga-se a um modelo nomeado e a um ficheiro de parâmetros chamado WebSite.parameters.json. Utilize os nomes do seu modelo e ficheiros de parâmetros.

Agora, que você entende como criar as tarefas, vamos passar pelos passos para editar o oleoduto.

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

1. Forneça uma mensagem para o compromisso, e comprometa-se diretamente a **dominar.**

1. Quando selecionar **Save,** o gasoduto de construção é executado automaticamente. Volte ao resumo do seu oleoduto de construção, e observe o estado.

   ![Ver resultados](./media/add-template-to-azure-pipelines/view-results.png)

Pode selecionar o pipeline em execução actualpara ver detalhes sobre as tarefas. Quando termina, vê-se os resultados de cada passo.

## <a name="next-steps"></a>Passos seguintes

Para um processo passo a passo sobre a utilização de Pipelines Azure com modelos ARM, consulte Tutorial: Integração contínua de modelos de Gestor de [Recursos Azure com Pipelines Azure](deployment-tutorial-pipeline.md).
