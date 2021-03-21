---
title: Atualizar continuamente o código de aplicação da função usando Azure DevOps
description: Saiba como configurar um oleoduto Azure DevOps que visa as Funções Azure.
author: craigshoemaker
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python, devx-track-azurecli
ms.openlocfilehash: a3f423a144738fdaa4462606de6ad4a4e34d6775
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97563420"
---
# <a name="continuous-delivery-by-using-azure-devops"></a>Entrega contínua utilizando Azure DevOps

Pode implantar automaticamente a sua função numa aplicação Azure Functions utilizando [Pipelines Azure](/azure/devops/pipelines/).

Tem duas opções para definir o seu oleoduto:

- **Ficheiro YAML**: Um ficheiro YAML descreve o gasoduto. O ficheiro pode ter uma secção de etapas de construção e uma secção de libertação. O ficheiro YAML deve estar no mesmo repo que a aplicação.
- **Modelo**: Os modelos são tarefas prontas que constroem ou implementam a sua aplicação.

## <a name="yaml-based-pipeline"></a>Gasoduto baseado em YAML

Para criar um pipeline baseado em YAML, primeiro construa a sua app e, em seguida, implemente a aplicação.

### <a name="build-your-app"></a>Criar a sua aplicação

A forma como constrói a sua aplicação em Azure Pipelines depende da linguagem de programação da sua aplicação. Cada idioma tem passos de construção específicos que criam um artefacto de implantação. Um artefacto de implantação é usado para implementar a sua aplicação de função em Azure.

# <a name="c"></a>[C\#](#tab/csharp)

Pode utilizar a seguinte amostra para criar um ficheiro YAML para construir uma aplicação .NET:

```yaml
pool:
      vmImage: 'VS2017-Win2016'
steps:
- script: |
    dotnet restore
    dotnet build --configuration Release
- task: DotNetCoreCLI@2
  inputs:
    command: publish
    arguments: '--configuration Release --output publish_output'
    projects: '*.csproj'
    publishWebProjects: false
    modifyOutputPath: false
    zipAfterPublish: false
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)/publish_output"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Pode utilizar a seguinte amostra para criar um ficheiro YAML para construir uma aplicação JavaScript:

```yaml
pool:
      vmImage: ubuntu-16.04 # Use 'VS2017-Win2016' if you have Windows native +Node modules
steps:
- bash: |
    if [ -f extensions.csproj ]
    then
        dotnet build extensions.csproj --output ./bin
    fi
    npm install 
    npm run build --if-present
    npm prune --production
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

# <a name="python"></a>[Python](#tab/python)

Pode utilizar uma das seguintes amostras para criar um ficheiro YAML para construir uma aplicação para uma versão python específica. Python é suportado apenas para aplicações de função em execução em Linux.

**Versão 3.7**

```yaml
pool:
  vmImage: ubuntu-16.04
steps:
- task: UsePythonVersion@0
  displayName: "Setting python version to 3.7 as required by functions"
  inputs:
    versionSpec: '3.7'
    architecture: 'x64'
- bash: |
    if [ -f extensions.csproj ]
    then
        dotnet build extensions.csproj --output ./bin
    fi
    pip install --target="./.python_packages/lib/site-packages" -r ./requirements.txt
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

**Versão 3.6**

```yaml
pool:
  vmImage: ubuntu-16.04
steps:
- task: UsePythonVersion@0
  displayName: "Setting python version to 3.6 as required by functions"
  inputs:
    versionSpec: '3.6'
    architecture: 'x64'
- bash: |
    if [ -f extensions.csproj ]
    then
        dotnet build extensions.csproj --output ./bin
    fi
    pip install --target="./.python_packages/lib/python3.6/site-packages" -r ./requirements.txt
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Pode utilizar a seguinte amostra para criar um ficheiro YAML para embalar uma aplicação PowerShell. O PowerShell é suportado apenas para funções do Windows Azure.

```yaml
pool:
      vmImage: 'VS2017-Win2016'
steps:
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

---

### <a name="deploy-your-app"></a>Implemente a sua aplicação

Deve incluir uma das seguintes amostras de YAML no seu ficheiro YAML, dependendo do sistema operativo de alojamento.

#### <a name="windows-function-app"></a>Aplicativo de função Windows

Pode utilizar o seguinte corte para implementar uma aplicação de função Windows:

```yaml
steps:
- task: AzureFunctionApp@1
  inputs:
    azureSubscription: '<Azure service connection>'
    appType: functionApp
    appName: '<Name of function app>'
    #Uncomment the next lines to deploy to a deployment slot
    #deployToSlotOrASE: true
    #resourceGroupName: '<Resource Group Name>'
    #slotName: '<Slot name>'
```

#### <a name="linux-function-app"></a>Aplicativo de função Linux

Pode utilizar o seguinte corte para implementar uma aplicação de função Linux:

```yaml
steps:
- task: AzureFunctionApp@1
  inputs:
    azureSubscription: '<Azure service connection>'
    appType: functionAppLinux
    appName: '<Name of function app>'
    #Uncomment the next lines to deploy to a deployment slot
    #Note that deployment slots is not supported for Linux Dynamic SKU
    #deployToSlotOrASE: true
    #resourceGroupName: '<Resource Group Name>'
    #slotName: '<Slot name>'
```

## <a name="template-based-pipeline"></a>Pipeline baseado em modelo

Os modelos em Azure DevOps são grupos pré-finidos de tarefas que constroem ou implementam uma aplicação.

### <a name="build-your-app"></a>Criar a sua aplicação

A forma como constrói a sua aplicação em Azure Pipelines depende da linguagem de programação da sua aplicação. Cada idioma tem passos de construção específicos que criam um artefacto de implantação. Um artefacto de implementação é usado para atualizar a sua aplicação de função em Azure.

Para utilizar modelos de construção incorporados, quando criar um novo oleoduto de construção, selecione **Use o editor clássico** para criar um pipeline usando modelos de design.

![Selecione o editor clássico da Azure Pipelines](media/functions-how-to-azure-devops/classic-editor.png)

Depois de configurar a fonte do seu código, procure modelos de construção de Funções Azure. Selecione o modelo que corresponde ao seu idioma de aplicação.

![Selecione um modelo de construção de funções Azure](media/functions-how-to-azure-devops/build-templates.png)

Em alguns casos, os artefactos de construção têm uma estrutura de pasta específica. Pode ser necessário selecionar o nome da **pasta de raiz Prepend para arquivar a** caixa de verificação dos caminhos.

![A opção de preparar o nome da pasta raiz](media/functions-how-to-azure-devops/prepend-root-folder.png)

#### <a name="javascript-apps"></a>Aplicativos JavaScript

Se a sua aplicação JavaScript tiver uma dependência de módulos nativos do Windows, tem de atualizar a versão do grupo de agentes para **O ANFITRIÃO VS2017**.

![Atualizar a versão da piscina do agente](media/functions-how-to-azure-devops/change-agent.png)

### <a name="deploy-your-app"></a>Implemente a sua aplicação

Quando criar um novo pipeline de lançamento, procure o modelo de libertação de Funções Azure.

![Procure o modelo de libertação de Funções Azure](media/functions-how-to-azure-devops/release-template.png)

A implantação numa ranhura de implantação não é suportada no modelo de libertação.

## <a name="create-a-build-pipeline-by-using-the-azure-cli"></a>Criar um gasoduto de construção utilizando o Azure CLI

Para criar um gasoduto de construção em Azure, utilize o `az functionapp devops-pipeline create` [comando](/cli/azure/functionapp/devops-pipeline#az-functionapp-devops-pipeline-create). O gasoduto de construção é criado para construir e libertar quaisquer alterações de código que sejam feitas no seu repo. O comando gera um novo ficheiro YAML que define o gasoduto de construção e libertação e, em seguida, compromete-o com o seu repo. Os pré-requisitos para este comando dependem da localização do seu código.

- Se o seu código estiver no GitHub:

    - Deve ter permissões de **escrita** para a sua subscrição.

    - Deve ser o administrador do projeto em Azure DevOps.

    - Você deve ter permissões para criar um token de acesso pessoal GitHub (PAT) que tenha permissões suficientes. Para mais informações, consulte [os requisitos de permissão do GitHub PAT.](/azure/devops/pipelines/repos/github#repository-permissions-for-personal-access-token-pat-authentication)

    - Tem de ter permissões para se comprometer com o ramo principal no seu repositório GitHub para que possa cometer o ficheiro YAML autogerido.

- Se o seu código estiver em Azure Repos:

    - Deve ter permissões de **escrita** para a sua subscrição.

    - Deve ser o administrador do projeto em Azure DevOps.

## <a name="next-steps"></a>Passos seguintes

- Reveja a [visão geral das funções Azure](functions-overview.md).
- Reveja a [visão geral do Azure DevOps](/azure/devops/pipelines/).