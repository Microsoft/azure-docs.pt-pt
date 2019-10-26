---
title: Fornecer atualizações de código de função continuamente usando o Azure DevOps-Azure Functions
description: Saiba como configurar um pipeline DevOps do Azure que tem como destino Azure Functions.
author: ahmedelnably
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: aelnably
ms.openlocfilehash: 4fb01eac53151799a0def00d13f18619faf437f6
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72927525"
---
# <a name="continuous-delivery-by-using-azure-devops"></a>Entrega contínua usando o Azure DevOps

Você pode implantar automaticamente sua função em um aplicativo Azure Functions usando [Azure pipelines](/azure/devops/pipelines/).

Você tem duas opções para definir seu pipeline:

- **Arquivo YAML**: um arquivo YAML descreve o pipeline. O arquivo pode ter uma seção de etapas de compilação e uma seção de versão. O arquivo YAML deve estar no mesmo repositório que o aplicativo.
- **Modelo**: modelos são tarefas prontas que criam ou implantam seu aplicativo.

## <a name="yaml-based-pipeline"></a>Pipeline baseado em YAML

Para criar um pipeline baseado em YAML, primeiro crie seu aplicativo e, em seguida, implante o aplicativo.

### <a name="build-your-app"></a>Criar a sua aplicação

A forma como você cria seu aplicativo no Azure Pipelines depende da linguagem de programação do seu aplicativo. Cada idioma tem etapas de Build específicas que criam um artefato de implantação. Um artefato de implantação é usado para implantar seu aplicativo de funções no Azure.

#### <a name="net"></a>.NET

Você pode usar o exemplo a seguir para criar um arquivo YAML para compilar um aplicativo .NET:

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

Você pode usar o exemplo a seguir para criar um arquivo YAML para criar um aplicativo JavaScript:

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

Você pode usar o exemplo a seguir para criar um arquivo YAML para criar um aplicativo Python. O Python tem suporte apenas para Azure Functions Linux. O YAML para Python 3,7 pode ser criado substituindo todas as instâncias de 3,6 por 3,7 neste YAML.

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

Você pode usar o exemplo a seguir para criar um arquivo YAML para empacotar um aplicativo PowerShell. O PowerShell só tem suporte para o Windows Azure Functions.

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

### <a name="deploy-your-app"></a>Implantar seu aplicativo

Você deve incluir um dos exemplos de YAML a seguir no arquivo YAML, dependendo do sistema operacional de hospedagem.

#### <a name="windows-function-app"></a>Aplicativo de funções do Windows

Você pode usar o trecho a seguir para implantar um aplicativo de funções do Windows:

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

#### <a name="linux-function-app"></a>Aplicativo de funções do Linux

Você pode usar o trecho a seguir para implantar um aplicativo de funções do Linux:

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

Os modelos no Azure DevOps são grupos predefinidos de tarefas que criam ou implantam um aplicativo.

### <a name="build-your-app"></a>Criar a sua aplicação

A forma como você cria seu aplicativo no Azure Pipelines depende da linguagem de programação do seu aplicativo. Cada idioma tem etapas de Build específicas que criam um artefato de implantação. Um artefato de implantação é usado para atualizar seu aplicativo de funções no Azure.

Para usar modelos de compilação internos, ao criar um novo pipeline de compilação, selecione **usar o editor clássico** para criar um pipeline usando modelos de designer.

![Selecione o editor clássico Azure Pipelines](media/functions-how-to-azure-devops/classic-editor.png)

Depois de configurar a origem do seu código, pesquise Azure Functions modelos de compilação. Selecione o modelo que corresponde ao idioma do aplicativo.

![Selecionar um modelo de compilação Azure Functions](media/functions-how-to-azure-devops/build-templates.png)

Em alguns casos, os artefatos de compilação têm uma estrutura de pastas específica. Talvez seja necessário selecionar a caixa de seleção **nome da pasta raiz para caminhos de arquivo** .

![A opção para preceder o nome da pasta raiz](media/functions-how-to-azure-devops/prepend-root-folder.png)

#### <a name="javascript-apps"></a>Aplicativos JavaScript

Se seu aplicativo JavaScript tiver uma dependência em módulos nativos do Windows, você deverá atualizar a versão do pool do agente para o **VS2017 hospedado**.

![Atualizar a versão do pool de agentes](media/functions-how-to-azure-devops/change-agent.png)

### <a name="deploy-your-app"></a>Implantar seu aplicativo

Ao criar um novo pipeline de lançamento, pesquise o modelo de versão Azure Functions.

![Pesquisar o modelo de versão Azure Functions](media/functions-how-to-azure-devops/release-template.png)

Não há suporte para a implantação em um slot de implantação no modelo de versão.

## <a name="create-a-build-pipeline-by-using-the-azure-cli"></a>Criar um pipeline de compilação usando o CLI do Azure

Para criar um pipeline de compilação no Azure, use o [comando](/cli/azure/functionapp/devops-pipeline#az-functionapp-devops-pipeline-create)`az functionapp devops-pipeline create`. O pipeline de compilação é criado para compilar e liberar quaisquer alterações de código feitas em seu repositório. O comando gera um novo arquivo YAML que define o pipeline de compilação e versão e, em seguida, o confirma para seu repositório. Os pré-requisitos para esse comando dependem do local do seu código.

- Se o seu código estiver no GitHub:

    - Você deve ter permissões de **gravação** para sua assinatura.

    - Você deve ser o administrador do projeto no Azure DevOps.

    - Você deve ter permissões para criar um PAT (token de acesso pessoal) do GitHub que tenha permissões suficientes. Para obter mais informações, consulte [requisitos de permissão do GITHUB Pat.](https://aka.ms/azure-devops-source-repos)

    - Você deve ter permissões para confirmar o Branch mestre em seu repositório do GitHub, para que você possa confirmar o arquivo YAML gerado automaticamente.

- Se seu código estiver em Azure Repos:

    - Você deve ter permissões de **gravação** para sua assinatura.

    - Você deve ser o administrador do projeto no Azure DevOps.

## <a name="next-steps"></a>Passos seguintes

- Examine a [Azure Functions visão geral](functions-overview.md).
- Examine a [visão geral do Azure DevOps](/azure/devops/pipelines/).
