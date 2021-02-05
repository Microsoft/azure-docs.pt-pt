---
title: CI/CD com gasodutos e modelos Azure
description: Descreve como configurar a integração contínua em Pipelines Azure utilizando modelos de Gestor de Recursos Azure. Mostra como usar um script PowerShell ou copiar ficheiros para um local de paragem e ser implantado a partir daí.
ms.topic: conceptual
ms.date: 02/05/2021
ms.openlocfilehash: ea1ccac00f121bd81fd8b9b1f182b565fc53d214
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594202"
---
# <a name="integrate-arm-templates-with-azure-pipelines"></a>Integrar modelos do Resource Manager com Pipelines do Azure

Pode integrar os modelos Azure Resource Manager (modelos ARM) com Pipelines Azure para integração contínua e implementação contínua (CI/CD). Neste artigo, você aprende duas formas mais avançadas de implementar modelos com Azure Pipelines.

## <a name="select-your-option"></a>Selecione a sua opção

Antes de prosseguir com este artigo, consideremos as diferentes opções para a implementação de um modelo ARM a partir de um oleoduto.

* **Utilize a tarefa de implementação do modelo ARM**. Esta opção é a opção mais fácil. Esta abordagem funciona quando pretende implantar um modelo diretamente a partir de um repositório. Esta opção não é abrangida por este artigo, mas está coberta pela integração tutorial [contínua dos modelos ARM com Azure Pipelines](deployment-tutorial-pipeline.md). Mostra como usar a [tarefa de implementação](https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureResourceManagerTemplateDeploymentV3/README.md) do modelo ARM para implementar um modelo a partir do seu repo GitHub.

* **Adicionar tarefa que executa um script Azure PowerShell**. Esta opção tem a vantagem de proporcionar consistência ao longo do ciclo de vida do desenvolvimento porque pode usar o mesmo script que usou ao realizar testes locais. O seu script implementa o modelo, mas também pode executar outras operações, tais como obter valores para usar como parâmetros. Esta opção é mostrada neste artigo. Ver [tarefa Azure PowerShell](#azure-powershell-task).

   O Visual Studio fornece o [projeto Azure Resource Group](create-visual-studio-deployment-project.md) que inclui um script PowerShell. O script encena artefactos do seu projeto para uma conta de armazenamento a que o Gestor de Recursos pode aceder. Os artefactos são itens no seu projeto, tais como modelos ligados, scripts e binários de aplicações. Se quiser continuar a utilizar o script do projeto, utilize a tarefa de script PowerShell mostrada neste artigo.

* **Adicionar tarefas para copiar e implementar tarefas**. Esta opção oferece uma alternativa conveniente ao script do projeto. Configura duas tarefas no oleoduto. Uma tarefa coloca os artefactos num local acessível. A outra tarefa implementa o modelo a partir desse local. Esta opção é mostrada neste artigo. Consulte [copiar e implementar tarefas.](#copy-and-deploy-tasks)

## <a name="prepare-your-project"></a>Prepare o seu projeto

Este artigo assume que o seu modelo ARM e a organização Azure DevOps estão prontos para criar o pipeline. Os seguintes passos mostram como se certificar de que está pronto:

* Tem uma organização da Azure DevOps. Se não tiver um, [crie um de graça.](/azure/devops/pipelines/get-started/pipelines-sign-up) Se a sua equipa já tem uma organização Azure DevOps, certifique-se de que é administrador do projeto Azure DevOps que pretende utilizar.

* Configuraste uma [ligação de serviço](/azure/devops/pipelines/library/connect-to-azure) à tua subscrição do Azure. As tarefas no gasoduto executam sob a identidade do diretor de serviço. Para obter medidas para criar a ligação, consulte [Criar um projeto DevOps](deployment-tutorial-pipeline.md#create-a-devops-project).

* Você tem um [modelo ARM](quickstart-create-templates-use-visual-studio-code.md) que define a infraestrutura para o seu projeto.

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

## <a name="azure-powershell-task"></a>Tarefa do Azure PowerShell

Esta secção mostra como configurar a implementação contínua utilizando uma única tarefa que executa o script PowerShell no seu projeto. Se precisar de um script PowerShell que implemente um modelo, consulte [Deploy-AzTemplate.ps1](https://github.com/Azure/azure-quickstart-templates/blob/master/Deploy-AzTemplate.ps1) ou [Deploy-AzureResourceGroup.ps1](https://github.com/Azure/azure-quickstart-templates/blob/master/Deploy-AzureResourceGroup.ps1).

O seguinte ficheiro YAML cria uma [tarefa Azure PowerShell](/azure/devops/pipelines/tasks/deploy/azure-powershell):

```yml
trigger:
- master

pool:
  vmImage: 'ubuntu-latest'

steps:
- task: AzurePowerShell@5
  inputs:
    azureSubscription: 'script-connection'
    ScriptType: 'FilePath'
    ScriptPath: './Deploy-AzTemplate.ps1'
    ScriptArguments: -Location 'centralus' -ResourceGroupName 'demogroup' -TemplateFile templates\mainTemplate.json
    azurePowerShellVersion: 'LatestVersion'
```

Quando define a `AzurePowerShell@5` tarefa, o gasoduto utiliza o [módulo Az](/powershell/azure/new-azureps-module-az). Se estiver a utilizar o módulo AzureRM no seu script, desajuste a tarefa para `AzurePowerShell@3` .

```yaml
steps:
- task: AzurePowerShell@3
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

Em `ScriptArguments` , fornecer quaisquer parâmetros necessários pelo seu script. O exemplo a seguir mostra alguns parâmetros para um script, mas terá de personalizar os parâmetros para o seu script.

```yaml
ScriptArguments: -Location 'centralus' -ResourceGroupName 'demogroup' -TemplateFile templates\mainTemplate.json
```

Quando selecionar **Save,** o gasoduto de construção é executado automaticamente. Volte ao resumo para o seu oleoduto de construção, e observe o estado.

![Ver resultados](./media/add-template-to-azure-pipelines/view-results.png)

Pode selecionar o pipeline atualmente em execução para ver detalhes sobre as tarefas. Quando terminar, vê os resultados de cada passo.

## <a name="copy-and-deploy-tasks"></a>Copiar e implementar tarefas

Esta secção mostra como configurar a implementação contínua utilizando duas tarefas. A primeira tarefa coloca os artefactos numa conta de armazenamento e a segunda tarefa implementa o modelo.

Para copiar ficheiros para uma conta de armazenamento, o principal de serviço para a ligação de serviço deve ser atribuído à função de Colaborador de Dados blob de armazenamento ou de armazenamento do Proprietário de Dados blob. Para mais informações, consulte [Começar com a AzCopy.](../../storage/common/storage-use-azcopy-v10.md)

A seguinte YAML mostra a [tarefa de cópia de ficheiro Azure](/azure/devops/pipelines/tasks/deploy/azure-file-copy).

```yml
trigger:
- master

pool:
  vmImage: 'windows-latest'

steps:
- task: AzureFileCopy@4
  inputs:
    SourcePath: 'templates'
    azureSubscription: 'copy-connection'
    Destination: 'AzureBlob'
    storage: 'demostorage'
    ContainerName: 'projecttemplates'
  name: AzureFileCopy
```

Existem várias partes desta tarefa para rever para o seu ambiente. `SourcePath`Indica a localização dos artefactos relativos ao ficheiro do gasoduto.

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

Depois de criar a tarefa do ficheiro de cópia, está pronto para adicionar a tarefa para implementar o modelo encenado.

O seguinte YAML mostra a tarefa de implementação do [modelo Azure Resource Manager](https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureResourceManagerTemplateDeploymentV3/README.md):

```yaml
- task: AzureResourceManagerTemplateDeployment@3
  inputs:
    deploymentScope: 'Resource Group'
    azureResourceManagerConnection: 'copy-connection'
    subscriptionId: '00000000-0000-0000-0000-000000000000'
    action: 'Create Or Update Resource Group'
    resourceGroupName: 'demogroup'
    location: 'West US'
    templateLocation: 'URL of the file'
    csmFileLink: '$(AzureFileCopy.StorageContainerUri)templates/mainTemplate.json$(AzureFileCopy.StorageContainerSasToken)'
    csmParametersFileLink: '$(AzureFileCopy.StorageContainerUri)templates/mainTemplate.parameters.json$(AzureFileCopy.StorageContainerSasToken)'
    deploymentMode: 'Incremental'
    deploymentName: 'deploy1'
```

Há várias partes desta tarefa a rever mais detalhadamente.

- `deploymentScope`: Selecione o âmbito de implementação das opções: `Management Group` `Subscription` , e `Resource Group` . Para saber mais sobre os âmbitos, consulte os [âmbitos de implementação](deploy-rest.md#deployment-scope).

- `azureResourceManagerConnection`: Fornecer o nome da ligação de serviço que criou.

- `subscriptionId`: Fornecer o ID de assinatura alvo. Esta propriedade aplica-se apenas ao âmbito de implementação do Grupo de Recursos e ao âmbito de implementação da subscrição.

- `resourceGroupName` e `location` : fornecer o nome e a localização do grupo de recursos para o quais pretende implementar. A tarefa cria o grupo de recursos se não existir.

   ```yml
   resourceGroupName: '<resource-group-name>'
   location: '<location>'
   ```

- `csmFileLink`: Fornecer o link para o modelo encenado. Ao definir o valor, utilize variáveis devolvidas da tarefa de cópia de ficheiro. O exemplo a seguir liga-se a um modelo nomeado mainTemplate.js. Os **modelos nomeados por pasta estão incluídos** porque onde a tarefa de cópia de ficheiro copiou o ficheiro para. No seu oleoduto, forneça o caminho para o seu modelo e o nome do seu modelo.

   ```yml
   csmFileLink: '$(AzureFileCopy.StorageContainerUri)templates/mainTemplate.json$(AzureFileCopy.StorageContainerSasToken)'
   ```

O seu oleoduto parece:

```yml
trigger:
- master

pool:
  vmImage: 'windows-latest'

steps:
- task: AzureFileCopy@4
  inputs:
    SourcePath: 'templates'
    azureSubscription: 'copy-connection'
    Destination: 'AzureBlob'
    storage: 'demostorage'
    ContainerName: 'projecttemplates'
  name: AzureFileCopy
- task: AzureResourceManagerTemplateDeployment@3
  inputs:
    deploymentScope: 'Resource Group'
    azureResourceManagerConnection: 'copy-connection'
    subscriptionId: '00000000-0000-0000-0000-000000000000'
    action: 'Create Or Update Resource Group'
    resourceGroupName: 'demogroup'
    location: 'West US'
    templateLocation: 'URL of the file'
    csmFileLink: '$(AzureFileCopy.StorageContainerUri)templates/mainTemplate.json$(AzureFileCopy.StorageContainerSasToken)'
    csmParametersFileLink: '$(AzureFileCopy.StorageContainerUri)templates/mainTemplate.parameters.json$(AzureFileCopy.StorageContainerSasToken)'
    deploymentMode: 'Incremental'
    deploymentName: 'deploy1'
```

Quando selecionar **Save,** o gasoduto de construção é executado automaticamente. Volte ao resumo para o seu oleoduto de construção, e observe o estado.

## <a name="next-steps"></a>Passos seguintes

Para aprender a usar modelos ARM com ações do GitHub, consulte [os modelos de Gestor de Recursos do Azure, utilizando as ações do GitHub](deploy-github-actions.md).
