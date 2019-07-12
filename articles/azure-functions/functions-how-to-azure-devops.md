---
title: Fornecer continuamente função atualizações ao código através do Azure DevOps - as funções do Azure
description: Saiba como configurar um pipeline de DevOps do Azure voltado as funções do Azure.
author: ahmedelnably
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: aelnably
ms.openlocfilehash: 0fdad0caa2deef0d7d55b30a85632f72f4ff0ecc
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594465"
---
# <a name="continuous-delivery-by-using-azure-devops"></a>Entrega contínua através do Azure DevOps

Pode implementar automaticamente a função a uma aplicação de funções do Azure, utilizando [Pipelines do Azure](/azure/devops/pipelines/).

Tem duas opções para definir o seu pipeline:

- **Ficheiro YAML**: Um ficheiro YAML descreve o pipeline. O ficheiro pode ter uma secção de passos de compilação e uma seção de versão. O ficheiro YAML deve estar no mesmo repositório que a aplicação.
- **Modelo**: Os modelos são prontas a utilizar tarefas que criarem ou implementar a sua aplicação.

## <a name="yaml-based-pipeline"></a>Com base em YAML pipeline

Para criar um pipeline com base em YAML, primeiro crie a sua aplicação e, em seguida, implementar a aplicação.

### <a name="build-your-app"></a>Criar a sua aplicação

Como criar a sua aplicação no Azure Pipelines depende da linguagem de programação da sua aplicação. Cada linguagem tenha passos de compilação específica que criar um artefacto de implementação. Um artefato de implementação é utilizado para implementar a sua aplicação de função no Azure.

#### <a name="net"></a>.NET

Pode utilizar o exemplo a seguir para criar um ficheiro YAML para criar uma aplicação .NET:

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
    modifyOutputPath: true
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
    name: 'drop'
```

#### <a name="javascript"></a>JavaScript

Pode utilizar o exemplo a seguir para criar um ficheiro YAML para criar uma aplicação do JavaScript:

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
    name: 'drop'
```

#### <a name="python"></a>Python

Pode utilizar o exemplo a seguir para criar um ficheiro YAML para criar uma aplicação de Python. Apenas para as funções do Azure do Linux, o Python seja suportado.

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
    python3.6 -m venv worker_venv
    source worker_venv/bin/activate
    pip3.6 install setuptools
    pip3.6 install -r requirements.txt
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    name: 'drop'
```
#### <a name="powershell"></a>PowerShell

Pode utilizar o exemplo a seguir para criar um ficheiro YAML para empacotar uma aplicação do PowerShell. PowerShell só é suportado para as funções do Azure do Windows.

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
    name: 'drop'
```

### <a name="deploy-your-app"></a>Implementar a sua aplicação

Tem de incluir um dos exemplos de YAML seguintes no ficheiro YAML, dependendo do sistema operacional Host.

#### <a name="windows-function-app"></a>Aplicação de funções do Windows

Pode usar o fragmento seguinte para implementar uma aplicação de funções do Windows:

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

#### <a name="linux-function-app"></a>Aplicação de funções do Linux

Pode utilizar o fragmento seguinte para implementar uma aplicação de funções do Linux:

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

## <a name="template-based-pipeline"></a>Com base no modelo de pipeline

Modelos de DevOps do Azure são grupos predefinidos de tarefas que criarem ou implementar uma aplicação.

### <a name="build-your-app"></a>Criar a sua aplicação

Como criar a sua aplicação no Azure Pipelines depende da linguagem de programação da sua aplicação. Cada linguagem tenha passos de compilação específica que criar um artefacto de implementação. Um artefato de implementação é utilizado para atualizar a sua aplicação de função no Azure.

Para utilizar modelos de compilação incorporada, quando cria um novo pipeline de compilação, selecione **utilize o editor clássico** para criar um pipeline com os modelos de designers.

![Selecione o editor clássico de Pipelines do Azure](media/functions-how-to-azure-devops/classic-editor.png)

Depois de configurar a origem do seu código, procure as funções do Azure crie modelos. Selecione o modelo que corresponda ao idioma da sua aplicação.

![Selecione um modelo de compilação de funções do Azure](media/functions-how-to-azure-devops/build-templates.png)

Em alguns casos, os artefactos de compilação têm uma estrutura de pasta específica. Poderá ter de selecionar o **Prepend nome da pasta raiz para arquivar caminhos** caixa de verificação.

![A opção de preceder o nome da pasta raiz](media/functions-how-to-azure-devops/prepend-root-folder.png)

#### <a name="javascript-apps"></a>Aplicações de JavaScript

Se a sua aplicação do JavaScript tem uma dependência no módulos nativos do Windows, tem de atualizar a versão do conjunto de agente para **Hosted VS2017**.

![Atualizar a versão do conjunto de agente](media/functions-how-to-azure-devops/change-agent.png)

### <a name="deploy-your-app"></a>Implementar a sua aplicação

Quando cria um novo pipeline de lançamento, procure o modelo de liberação de funções do Azure.

![Procure o modelo de versão de funções do Azure](media/functions-how-to-azure-devops/release-template.png)

Implantação num bloco de implementação não é suportada no modelo de liberação.

## <a name="create-a-build-pipeline-by-using-the-azure-cli"></a>Criar um pipeline de compilação com a CLI do Azure

Para criar um pipeline de compilação no Azure, utilize o `az functionapp devops-pipeline create` [comando](/cli/azure/functionapp/devops-pipeline#az-functionapp-devops-pipeline-create). O pipeline de compilação é criado para criar e lançar quaisquer alterações de código que são feitas no seu repositório. O comando gera um novo ficheiro YAML que define o pipeline de compilação e versão e, em seguida, consolide-lo ao seu repositório. As pré-requisitos para este comando dependem da localização do seu código.

- Se seu código no GitHub:

    - Tem de ter **escrever** permissões para a sua subscrição.

    - Tem de ser o administrador de projeto de DevOps do Azure.

    - Tem de ter permissões para criar um token de acesso pessoal do GitHub (PAT) que tem permissões suficientes. Para obter mais informações, consulte [dar um TAPINHA GitHub requisitos de permissão.](https://aka.ms/azure-devops-source-repos)

    - Tem de ter permissões para consolidar para o ramo principal no repositório do GitHub para que pode consolidar o ficheiro YAML de gerado automaticamente.

- Se seu código em repositórios do Azure:

    - Tem de ter **escrever** permissões para a sua subscrição.

    - Tem de ser o administrador de projeto de DevOps do Azure.

## <a name="next-steps"></a>Passos Seguintes

- Reveja os [descrição geral das funções do Azure](functions-overview.md).
- Reveja os [descrição geral de Azure DevOps](/azure/devops/pipelines/).
