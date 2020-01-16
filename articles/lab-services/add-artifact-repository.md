---
title: Adicionar um repositório de artefatos ao seu laboratório no Azure DevTest Labs | Microsoft Docs
description: Saiba como adicionar um repositório de artefatos ao seu laboratório no Azure DevTest Labs.
services: devtest-lab
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2019
ms.author: spelluru
ms.openlocfilehash: 28ab6ca9b87bb00cbb7b5e329b7ff08972ba370a
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75979140"
---
# <a name="add-an-artifact-repository-to-your-lab-in-devtest-labs"></a>Adicionar um repositório de artefatos ao seu laboratório no DevTest Labs
O DevTest Labs permite que você especifique um artefato a ser adicionado a uma VM no momento da criação da VM ou após a criação da VM. Esse artefato pode ser uma ferramenta ou um aplicativo que você deseja instalar na VM. Os artefatos são definidos em um arquivo JSON carregado de um repositório do GitHub ou Azure DevOps git.

O [repositório público de artefatos](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts), mantido pelo DevTest Labs, fornece muitas ferramentas comuns para Windows e Linux. Um link para esse repositório é adicionado automaticamente ao seu laboratório. Você pode criar seu próprio repositório de artefatos com ferramentas específicas que não estão disponíveis no repositório público de artefatos. Para saber mais sobre a criação de artefatos personalizados, consulte [criar artefatos personalizados](devtest-lab-artifact-author.md).

Este artigo fornece informações sobre como adicionar seu repositório de artefatos personalizado usando portal do Azure, modelos de gerenciamento de recursos do Azure e Azure PowerShell. Você pode automatizar a adição de um repositório de artefatos a um laboratório escrevendo scripts do PowerShell ou da CLI.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos
Para adicionar um repositório ao seu laboratório, primeiro, obtenha informações de chave do seu repositório. As seções a seguir descrevem como obter as informações necessárias para repositórios hospedados no **GitHub** ou no **Azure DevOps**.

### <a name="get-the-github-repository-clone-url-and-personal-access-token"></a>Obter a URL de clone do repositório do GitHub e o token de acesso pessoal

1. Vá para a home page do repositório GitHub que contém as definições de modelo de artefato ou do Resource Manager.
2. Selecione **Clone or download** (Clonar ou transferir).
3. Para copiar a URL para a área de transferência, selecione o botão **URL de clone https** . Salve a URL para uso posterior.
4. No canto superior direito do GitHub, selecione a imagem do perfil e, em seguida, selecione **configurações**.
5. No menu **configurações pessoais** à esquerda, selecione configurações do **desenvolvedor**.
6. Selecione **tokens de acesso pessoal** no menu à esquerda.
7. Selecione **gerar novo token**.
8. Na página **novo token de acesso pessoal** , em **Descrição do token**, insira uma descrição. Aceite os itens padrão em **selecionar escopos**e, em seguida, selecione **gerar token**.
9. Salve o token gerado. Você usará o token mais tarde.
10. Feche o GitHub.   

### <a name="get-the-azure-repos-clone-url-and-personal-access-token"></a>Obter a URL de clone de Azure Repos e o token de acesso pessoal
1. Vá para a home page da sua coleção de equipe (por exemplo, https://contoso-web-team.visualstudio.com) e, em seguida, selecione seu projeto.
2. No home page do projeto, selecione **código**.
3. Para exibir a URL de clone, na página de **código** do projeto, selecione **clonar**.
4. Salve a URL. Você usará a URL mais tarde.
5. Para criar um token de acesso pessoal, no menu suspenso conta de usuário, selecione **meu perfil**.
6. Na página informações do perfil, selecione **segurança**.
7. Na guia **segurança** , selecione **Adicionar**.
8. Na página **criar um token de acesso pessoal** :
   1. Insira uma **Descrição** para o token.
   2. Na lista **expira em** , selecione **180 dias**.
   3. Na lista **contas** , selecione **todas as contas acessíveis**.
   4. Selecione a opção **todos os escopos** .
   5. Selecione **criar token**.
9. O novo token aparece na lista de **tokens de acesso pessoal** . Selecione **copiar token**e, em seguida, salve o valor do token para uso posterior.
10. Vá para a seção conectar seu laboratório ao repositório.

## <a name="use-azure-portal"></a>Utilizar o portal do Azure
Esta seção fornece etapas para adicionar um repositório de artefatos a um laboratório no portal do Azure.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Selecione **mais serviços**e, em seguida, selecione **DevTest Labs** na lista de serviços.
3. Na lista de laboratórios, selecione seu laboratório.
4. Selecione **configuração e políticas** no menu à esquerda.
5. Selecione **repositórios** na seção **recursos externos** no menu à esquerda.
6. Selecione **+ Adicionar** na barra de ferramentas.

    ![O botão Adicionar repositório](./media/devtest-lab-add-repo/devtestlab-add-repo.png)
5. Na página **repositórios** , especifique as seguintes informações:
   1. **Nome**. Insira um nome para o repositório.
   2. **URL de git clone**. Insira a URL de clone HTTPS do git que você copiou anteriormente do GitHub ou Azure DevOps Services.
   3. **Ramificação**. Para obter suas definições, insira a ramificação.
   4. **Token de acesso pessoal**. Insira o token de acesso pessoal que você obteve anteriormente do GitHub ou Azure DevOps Services.
   5. **Caminhos de pasta**. Insira pelo menos um caminho de pasta relativo à URL de clone que contém suas definições de modelo de artefato ou do Resource Manager. Ao especificar um subdiretório, certifique-se de incluir a barra no caminho da pasta.

        ![Área de repositórios](./media/devtest-lab-add-repo/devtestlab-repo-blade.png)
6. Selecione **Guardar**.

## <a name="use-azure-resource-manager-template"></a>Utilizar o modelo do Azure Resource Manager
Os modelos de gerenciamento de recursos do Azure (Azure Resource Manager) são arquivos JSON que descrevem os recursos no Azure que você deseja criar. Para obter mais informações sobre esses modelos, consulte [criando modelos de Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md).

Esta seção fornece etapas para adicionar um repositório de artefatos a um laboratório usando um modelo de Azure Resource Manager.  O modelo criará o laboratório se ele ainda não existir.

### <a name="template"></a>Modelo
O modelo de exemplo usado neste artigo reúne as seguintes informações por meio de parâmetros. A maioria dos parâmetros tem padrões inteligentes, mas há alguns valores que devem ser especificados. Você deve especificar o nome do laboratório, o URI do repositório de artefatos e o token de segurança para o repositório.

- Nome do laboratório.
- Nome de exibição do repositório de artefatos na interface do usuário do DevTest Labs. O valor padrão é: `Team Repository`.
- URI para o repositório (exemplo: `https://github.com/<myteam>/<nameofrepo>.git` ou `"https://MyProject1.visualstudio.com/DefaultCollection/_git/TeamArtifacts"`.
- Branch no repositório que contém artefatos. O valor padrão é: `master`.
- Nome da pasta que contém artefatos. O valor padrão é: `/Artifacts`.
- Tipo do repositório. Os valores permitidos são `VsoGit` ou `GitHub`.
- Token de acesso para o repositório.

    ```json
    {

        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "labName": {
                "type": "string"
            },
            "artifactRepositoryDisplayName": {
                "type": "string",
                "defaultValue": "Team Repository"
            },
            "artifactRepoUri": {
                "type": "string"
            },
            "artifactRepoBranch": {
                "type": "string",
                "defaultValue": "master"
            },
            "artifactRepoFolder": {
                "type": "string",
                "defaultValue": "/Artifacts"
            },
            "artifactRepoType": {
                "type": "string",
                "allowedValues": ["VsoGit", "GitHub"]
            },
            "artifactRepoSecurityToken": {
                "type": "securestring"
            }
        },
        "variables": {
            "artifactRepositoryName": "[concat('Repo-', uniqueString(subscription().subscriptionId))]"
        },
        "resources": [{
                "apiVersion": "2016-05-15",
                "type": "Microsoft.DevTestLab/labs",
                "name": "[parameters('labName')]",
                "location": "[resourceGroup().location]",
                "resources": [
                    {
                        "apiVersion": "2016-05-15",
                        "name": "[variables('artifactRepositoryName')]",
                        "type": "artifactSources",
                        "dependsOn": [
                            "[resourceId('Microsoft.DevTestLab/labs', parameters('labName'))]"
                        ],
                        "properties": {
                            "uri": "[parameters('artifactRepoUri')]",
                            "folderPath": "[parameters('artifactRepoFolder')]",
                            "branchRef": "[parameters('artifactRepoBranch')]",
                            "displayName": "[parameters('artifactRepositoryDisplayName')]",
                            "securityToken": "[parameters('artifactRepoSecurityToken')]",
                            "sourceType": "[parameters('artifactRepoType')]",
                            "status": "Enabled"
                        }
                    }
                ]
            }
        ]
    }
    ```


### <a name="deploy-the-template"></a>Implementar o modelo
Há algumas maneiras de implantar o modelo no Azure e ter o recurso criado, se ele não existir, ou atualizado, se ele existir. Para obter detalhes, consulte os seguintes artigos:

- [Implementar recursos com modelos do Resource Manager e o Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)
- [Implementar recursos com modelos do Resource Manager e a CLI do Azure](../azure-resource-manager/templates/deploy-cli.md)
- [Implementar recursos com modelos do Resource Manager e o Portal do Azure](../azure-resource-manager/templates/deploy-portal.md)
- [Implementar recursos com modelos do Resource Manager e a API REST do Resource Manager](../azure-resource-manager/templates/deploy-rest.md)

Vamos continuar e ver como implantar o modelo no PowerShell. Os cmdlets usados para implantar o modelo são específicos do contexto, portanto, o locatário atual e a assinatura atual são usados. Use [set-AzContext](/powershell/module/az.accounts/set-azcontext) antes de implantar o modelo, se necessário, para alterar o contexto.

Primeiro, crie um grupo de recursos usando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Se o grupo de recursos que você deseja usar já existir, ignore esta etapa.

```powershell
New-AzResourceGroup -Name MyLabResourceGroup1 -Location westus
```

Em seguida, crie uma implantação para o grupo de recursos usando [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment). Esse cmdlet aplica as alterações de recurso ao Azure. Várias implantações de recursos podem ser feitas em qualquer grupo de recursos específico. Se você estiver implantando várias vezes no mesmo grupo de recursos, verifique se o nome de cada implantação é exclusivo.

```powershell
New-AzResourceGroupDeployment `
    -Name MyLabResourceGroup-Deployment1 `
    -ResourceGroupName MyLabResourceGroup1 `
    -TemplateFile azuredeploy.json `
    -TemplateParameterFile azuredeploy.parameters.json
```

Depois que New-AzResourceGroupDeployment for executado com êxito, o comando produzirá informações importantes como o estado de provisionamento (deve ser bem-sucedido) e quaisquer saídas para o modelo.

## <a name="use-azure-powershell"></a>Utilizar o Azure PowerShell
Esta seção fornece um exemplo de script do PowerShell que pode ser usado para adicionar um repositório de artefatos a um laboratório. Se você não tiver Azure PowerShell, consulte [como instalar e configurar o Azure PowerShell](/powershell/azure/overview?view=azps-1.2.0) para obter instruções detalhadas para instalá-lo.

### <a name="full-script"></a>Script completo
Este é o script completo, incluindo algumas mensagens e comentários detalhados:

**New-DevTestLabArtifactRepository.ps1**:

```powershell

<#

.SYNOPSIS
This script creates a new custom repository and adds it to an existing DevTest Lab.

.PARAMETER LabName
The name of the lab.

.PARAMETER LabResourceGroupName
The name of the resource group that contains the lab.

.PARAMETER ArtifactRepositoryName
Name for the new artifact repository.
Script creates a random name for the respository if it is not specified.

.PARAMETER ArtifactRepositoryDisplayName
Display name for the artifact repository.
This is the name that shows up in the Azure portal (https://portal.azure.com) when viewing all the artifact repositories for a lab.

.PARAMETER RepositoryUri
Uri to the repository.

.PARAMETER RepositoryBranch
Branch in which artifact files can be found. Defaults to 'master'.

.PARAMETER FolderPath
Folder under which artifacts can be found. Defaults to '/Artifacts'

.PARAMETER PersonalAccessToken
Security token for access to GitHub or VSOGit repository.
See https://azure.microsoft.com/documentation/articles/devtest-lab-add-artifact-repo/ for instructions to get personal access token

.PARAMETER SourceType
Whether artifact is VSOGit or GitHub repository.

.EXAMPLE
Set-AzContext -SubscriptionId 11111111-1111-1111-1111-111111111111
.\New-DevTestLabArtifactRepository.ps1 -LabName "mydevtestlab" -LabResourceGroupName "mydtlrg" -ArtifactRepositoryName "MyTeam Repository" -RepositoryUri "https://github.com/<myteam>/<nameofrepo>.git" -PersonalAccessToken "1111...." -SourceType "GitHub"

.NOTES
Script uses the current Az context. To set the context, use the Set-AzContext cmdlet

#>


[CmdletBinding()]
Param(

    [Parameter(Mandatory=$true)]
    $LabName,

    [Parameter(Mandatory=$true)]
    $LabResourceGroupName,
    $ArtifactRepositoryName,
    $ArtifactRepositoryDisplayName  = 'Team Artifact Repository',

    [Parameter(Mandatory=$true)]
    $RepositoryUri,
    $RepositoryBranch = 'master',
    $FolderPath = '/Artifacts',

    [Parameter(Mandatory=$true)]
    $PersonalAccessToken ,

    [Parameter(Mandatory=$true)]
    [ValidateSet('VsoGit', 'GitHub')]
    $SourceType
)


#Set artifact repository internal name,
# if not set by user.

if ($ArtifactRepositoryName -eq $null){
    $ArtifactRepositoryName = "PrivateRepo" + (Get-Random -Maximum 999)
}

# Sign in to Azure
Connect-AzAccount


#Get Lab Resource
$LabResource = Get-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceName $LabName -ResourceGroupName $LabResourceGroupName

Write-Verbose "Lab Name: $($LabResource.Name)"
Write-Verbose "Lab Resource Group Name: $($LabResource.ResourceGroupName)"
Write-Verbose "Lab Resource Location: $($LabResource.Location)"

Write-Verbose "Artifact Repository Internal Name: $ArtifactRepositoryName"

#Prepare properties object for call to New-AzResource
$propertiesObject = @{
    uri = $RepositoryUri;
    folderPath = $FolderPath;
    branchRef = $RepositoryBranch;
    displayName = $ArtifactRepositoryDisplayName;
    securityToken = $PersonalAccessToken;
    sourceType = $SourceType;
    status = 'Enabled'
}

Write-Verbose @"Properties to be passed to New-AzResource:$($propertiesObject | Out-String)"

#Resource will be added to current subscription.
$resourcetype = 'Microsoft.DevTestLab/labs/artifactSources'
$resourceName = $LabName + '/' + $ArtifactRepositoryName
Write-Verbose "Az ResourceType: $resourcetype"
Write-Verbose "Az ResourceName: $resourceName"

Write-Verbose "Creating artifact repository '$ArtifactRepositoryDisplayName'..."
$result = New-AzResource -Location $LabResource.Location -ResourceGroupName $LabResource.ResourceGroupName -properties $propertiesObject -ResourceType $resourcetype -ResourceName $resourceName -ApiVersion 2016-05-15 -Force


#Alternate implementation:
# Use resourceId rather than resourcetype and resourcename parameters.
# Using resourceId allows you to specify the $SubscriptionId rather than using the
# subscription id of Get-AzContext.
#$resourceId = "/subscriptions/$SubscriptionId/resourceGroups/$($LabResource.ResourceGroupName)/providers/Microsoft.DevTestLab/labs/$LabName/artifactSources/$ArtifactRepositoryName"
#$result = New-AzResource -properties $propertiesObject -ResourceId $resourceId -ApiVersion 2016-05-15 -Force


# Check the result
if ($result.Properties.ProvisioningState -eq "Succeeded") {
    Write-Verbose ("Successfully added artifact repository source '$ArtifactRepositoryDisplayName'")
}
else {
    Write-Error ("Error adding artifact repository source '$ArtifactRepositoryDisplayName'")
}

#Return the newly created resource so it may be used in subsequent scripts
return $result
```

### <a name="run-the-powershell-script"></a>Executar o script do PowerShell
O exemplo a seguir mostra como executar o script:

```powershell
Set-AzContext -SubscriptionId <Your Azure subscription ID>

.\New-DevTestLabArtifactRepository.ps1 -LabName "mydevtestlab" -LabResourceGroupName "mydtlrg" -ArtifactRepositoryName "MyTeam Repository" -RepositoryUri "https://github.com/<myteam>/<nameofrepo>.git" -PersonalAccessToken "1111...." -SourceType "GitHub"
```


### <a name="parameters"></a>Parâmetros
O script do PowerShell de exemplo neste artigo usa os seguintes parâmetros:

| Parâmetro | Descrição |
| --------- | ----------- |
| LabName | O nome do laboratório. |
| ArtifactRepositoryName | Nome do novo repositório de artefatos. O script cria um nome aleatório para o repositório se ele não for especificado. |
| ArtifactRepositoryDisplayName | Nome de exibição do repositório de artefatos. Esse é o nome que aparece na portal do Azure (https://portal.azure.com) ao exibir todos os repositórios de artefatos de um laboratório. |
| RepositoryUri | URI para o repositório. Exemplos: `https://github.com/<myteam>/<nameofrepo>.git` ou `"https://MyProject1.visualstudio.com/DefaultCollection/_git/TeamArtifacts"`.|
| RepositoryBranch | Ramificação na qual os arquivos de artefato podem ser encontrados. O padrão é ' Master '. |
| FolderPath | Pasta sob a qual os artefatos podem ser encontrados. O padrão é '/Artifacts ' |
| PersonalAccessToken | Token de segurança para acessar o repositório GitHub ou VSOGit. Consulte a seção pré-requisitos para obter instruções para obter o token de acesso pessoal |
| sourceType | Se o artefato é VSOGit ou repositório GitHub. |

O próprio repositório precisa de um nome interno para identificação, que é diferente do nome de exibição que é visto no portal do Azure. Você não vê o nome interno usando o portal do Azure, mas você o vê ao usar as APIs REST do Azure ou Azure PowerShell. O script fornece um nome, se um não for especificado pelo usuário do nosso script.

```powershell
#Set artifact repository name, if not set by user
if ($ArtifactRepositoryName -eq $null){
    $ArtifactRepositoryName = "PrivateRepo" + (Get-Random -Maximum 999)
}
```

### <a name="powershell-commands-used-in-the-script"></a>Comandos do PowerShell usados no script

| Comando do PowerShell | Notas |
| ------------------ | ----- |
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Esse comando é usado para obter detalhes sobre o laboratório, como seu local. |
| [New-AzResource](/powershell/module/az.resources/new-azresource) | Não há nenhum comando específico para adicionar repositórios de artefatos. O cmdlet [New-AzResource](/powershell/module/az.resources/new-azresource) genérico faz o trabalho. Esse cmdlet precisa do **ResourceId** ou do par **resourceName** e **ResourceType** para saber o tipo de recurso a ser criado. Este script de exemplo usa o par nome do recurso e tipo de recurso. <br/><br/>Observe que você está criando a origem do repositório de artefatos no mesmo local e no mesmo grupo de recursos que o laboratório.|

O script adiciona um novo recurso à assinatura atual. Use [Get-AzContext](/powershell/module/az.accounts/get-azcontext) para ver essas informações. Use [set-AzContext](/powershell/module/az.accounts/set-azcontext) para definir o locatário atual e a assinatura.

A melhor maneira de descobrir o nome do recurso e as informações do tipo de recurso é usar o site [test drive do Azure REST APIs](https://azure.github.io/projects/apis/) . Confira o provedor do [DevTest Labs – 2016-05-15](https://aka.ms/dtlrestapis) para ver as APIs REST disponíveis para o provedor do DevTest Labs. O script Users tem a seguinte ID de recurso.

```powershell
"/subscriptions/$SubscriptionId/resourceGroups/$($LabResource.ResourceGroupName)/providers/Microsoft.DevTestLab/labs/$LabName/artifactSources/$ArtifactRepositoryName"
```

O tipo de recurso é tudo listado após ' Providers ' no URI, exceto para os itens listados entre chaves. O nome do recurso é tudo que é visto entre chaves. Se mais de um item for esperado para o nome do recurso, separe cada item com uma barra como fizemos.

```powershell
$resourcetype = 'Microsoft.DevTestLab/labs/artifactSources'
$resourceName = $LabName + '/' + $ArtifactRepositoryName
```


## <a name="next-steps"></a>Passos seguintes
- [Especificar artefatos obrigatórios para seu laboratório no Azure DevTest Labs](devtest-lab-mandatory-artifacts.md)
- [Criar artefatos personalizados para sua máquina virtual do DevTest Labs](devtest-lab-artifact-author.md)
- [Diagnosticar falhas de artefato no laboratório](devtest-lab-troubleshoot-artifact-failure.md)
