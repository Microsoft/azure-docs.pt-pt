---
title: Fornecer continuamente atualizações de código de função através do Azure DevOps
description: Saiba como configurar um pipeline de DevOps do Azure para as funções do Azure.
author: ahmedelnably
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: aelnably
ms.custom: ''
ms.openlocfilehash: ce57aae1119261c0545b59a037226fdc12ec115f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67050695"
---
# <a name="continuous-delivery-using-azure-devops"></a>Entrega contínua através do Azure DevOps

Pode implementar automaticamente a função para uma aplicação de função do Azure com [Pipelines do Azure](/azure/devops/pipelines/).
Para definir o seu pipeline, pode usar:

- Ficheiro YAML: Este ficheiro descreve o pipeline, que pode ter uma secção de passos de compilação e uma seção de versão. O ficheiro YAML deve estar no mesmo repositório que a aplicação.

- Modelos: Os modelos são prontos efetuadas tarefas que criarem ou implementar a sua aplicação.

## <a name="yaml-based-pipeline"></a>Com base em YAML pipeline

### <a name="build-your-app"></a>Criar a sua aplicação

Criar a sua aplicação no Azure Pipelines depende da linguagem de programação da sua aplicação.
Cada linguagem tenha passos de compilação específica para criar um artefacto de implementação, o que pode ser utilizado para implementar a sua aplicação de função no Azure.

#### <a name="net"></a>.NET

Pode utilizar o exemplo a seguir para criar o seu ficheiro YAML para criar seu aplicativo .NET.

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

Pode utilizar o exemplo a seguir para criar o seu ficheiro YAML para criar a sua aplicação do JavaScript:

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

Pode utilizar o exemplo a seguir para criar o seu ficheiro YAML para criar a sua aplicação de Python, Python só é suportado para as funções do Azure do Linux:

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

Pode utilizar o exemplo a seguir para criar o seu ficheiro YAML para empacotar a aplicação do PowerShell, PowerShell só é suportado para as funções do Azure do Windows:

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

Dependendo do sistema operacional em hospedagem, precisa incluir o seguinte exemplo YAML no seu ficheiro YAML.

#### <a name="windows-function-app"></a>Função do Windows App

O fragmento seguinte pode ser utilizado para implementar uma aplicação de funções do Windows

```yaml
steps:
- task: AzureFunctionApp@1
  inputs:
    azureSubscription: '<Azure service connection>'
    appType: functionApp
    appName: '<Name of function app>'
```

#### <a name="linux-function-app"></a>Aplicação de função de Linux

O fragmento seguinte pode ser utilizado para implementar uma aplicação de funções do Linux

```yaml
steps:
- task: AzureFunctionApp@1
  inputs:
    azureSubscription: '<Azure service connection>'
    appType: functionAppLinux
    appName: '<Name of function app>'
```

## <a name="template-based-pipeline"></a>Com base no modelo de pipeline

Modelos do Azure DevOps, são predefinidas de grupo de tarefas que criar ou implementar uma aplicação.

### <a name="build-your-app"></a>Criar a sua aplicação

Criar a sua aplicação no Azure Pipelines depende da linguagem de programação da sua aplicação. Cada linguagem tenha passos de compilação específica para criar um artefacto de implementação, o que pode ser utilizado para atualizar a sua aplicação de função no Azure.
Para utilizar os modelos de compilação incorporado, ao criar um novo pipeline de compilação, escolha **utilize o editor clássico** para criar um pipeline com os modelos de designers

![Editor clássico de Pipelines do Azure](media/functions-how-to-azure-devops/classic-editor.png)

Depois de configurar a origem do seu código, procure as funções do Azure, modelos de compilação e escolher o modelo que corresponda ao idioma da sua aplicação.

![As funções do Azure, criar modelos](media/functions-how-to-azure-devops/build-templates.png)

Em alguns casos, os artefactos de compilação têm uma estrutura de pasta específica e poderá ter de verificar a **Prepend nome da pasta raiz para arquivar caminhos** opção.

![Preceder a pasta raiz](media/functions-how-to-azure-devops/prepend-root-folder.png)

#### <a name="javascript-apps"></a>Aplicações de JavaScript

Se a aplicação do JavaScript possui uma dependência em módulos nativos do Windows, terá de atualizar:

- A versão do conjunto de agentes para **Hosted VS2017**

  ![Alterar agente de compilação de SO](media/functions-how-to-azure-devops/change-agent.png)

### <a name="deploy-your-app"></a>Implementar a sua aplicação

Ao criar um novo pipeline de lançamento, procure o modelo de liberação de funções do Azure.

![](media/functions-how-to-azure-devops/release-template.png)

## <a name="creating-an-azure-pipeline-using-the-azure-cli"></a>Criar um Pipeline do Azure com a CLI do Azure

Utilizar o `az functionapp devops-pipeline create` [comando](/cli/azure/functionapp/devops-pipeline#az-functionapp-devops-pipeline-create), será criado um pipeline do Azure para criar e lançar quaisquer alterações de código no seu repositório. O comando irá gerar um novo ficheiro YAML que define o pipeline de compilação e lançamento e confirmá-lo no seu repositório.
Pré-requisitos para este comando dependem da localização do seu código:

- Se seu código no GitHub:

    - Tem de ter **escrever** permissão à sua subscrição.

    - For o administrador de projeto de DevOps do Azure.

    - Tem permissão para criar um GitHub Token de acesso pessoal com permissões suficientes. [Requisitos de permissão de PAT do GitHub.](https://aka.ms/azure-devops-source-repos)

    - Tem permissão para consolidar para o ramo principal no repositório do GitHub para consolidar o ficheiro YAML de gerado automaticamente.

- Se seu código em repositórios do Azure:

    - Tem de ter **escrever** permissão à sua subscrição.

    - For o administrador de projeto de DevOps do Azure.

## <a name="next-steps"></a>Passos Seguintes

+ [Descrição geral das funções do Azure](functions-overview.md)
+ [Descrição geral de DevOps do Azure](/azure/devops/pipelines/)
