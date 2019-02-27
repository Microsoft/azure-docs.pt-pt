---
title: Adicionar um repositório de artefactos para seu laboratório no Azure DevTest Labs | Documentos da Microsoft
description: Saiba como adicionar um repositório de artefactos para seu laboratório no Azure DevTest labs.
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
ms.openlocfilehash: 9a267b48e185e02c1b1217380429453799308bbe
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56886782"
---
# <a name="add-an-artifact-repository-to-your-lab-in-devtest-labs"></a>Adicionar um repositório de artefactos para seu laboratório no DevTest Labs
DevTest Labs permite-lhe especificar um artefacto a ser adicionados a uma VM no momento da criação da VM ou após a VM é criada. Este artefacto pode ser uma ferramenta ou uma aplicação que pretende instalar na VM. Artefactos são definidos num ficheiro JSON carregado de um repositório do GitHub ou o Git do VSTS. 

O [repositório público de artefactos](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts), mantidos pelo DevTest Labs, fornece várias ferramentas comuns para Windows e Linux. Uma ligação para este repositório é automaticamente adicionada ao seu laboratório. Pode criar seu próprio repositório de artefactos com ferramentas específicas que não estão disponíveis no repositório público de artefactos. Para saber mais sobre como criar artefactos personalizados, veja [criar artefactos personalizados](devtest-lab-artifact-author.md).

Este artigo fornece informações sobre como adicionar o seu repositório de artefactos personalizados com o portal do Azure, modelos de gestão de recursos do Azure e o Azure PowerShell. Pode automatizar a adição de um repositório de artefactos a um laboratório com a criação de scripts do PowerShell ou CLI. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos
Para adicionar um repositório ao seu laboratório, primeiro, obtenha informações da chave do seu repositório. As secções seguintes descrevem como obter as informações necessárias para repositórios que estão alojados num **GitHub** ou **do Azure DevOps**.

### <a name="get-the-github-repository-clone-url-and-personal-access-token"></a>Obter o URL de clone do repositório de GitHub e o acesso pessoal token

1. Vá para a home page do repositório do GitHub que contém o artefacto ou definições de modelo do Resource Manager.
2. Selecione **Clone or download** (Clonar ou transferir).
3. Para copiar o URL para a área de transferência, selecione o **url de clone de HTTPS** botão. Guarde o URL para utilização posterior.
4. No canto superior direito do GitHub, selecione a imagem de perfil e, em seguida, selecione **definições**.
5. Na **configurações pessoais** menu à esquerda, selecione **definições de programador**.
6. Selecione **tokens de acesso pessoal** no menu da esquerda.
7. Selecione **gerar novo token**.
8. Sobre o **novo token de acesso pessoal** página, em **Token Descrição**, introduza uma descrição. Aceitar os itens predefinidos sob **selecione os âmbitos**e, em seguida, selecione **gerar Token**.
9. Guarde o token de gerado. Utilizar o token mais tarde.
10. Feche o GitHub.   

### <a name="get-the-azure-repos-clone-url-and-personal-access-token"></a>Obtenha o clone de repositórios do Azure, URL e o token de acesso pessoal
1. Vá para a home page de sua coleção de equipe (por exemplo, https://contoso-web-team.visualstudio.com)e, em seguida, selecione seu projeto.
2. Na home page do projeto, selecione **código**.
3. Para ver o URL do clone, no projeto **código** página, selecione **Clone**.
4. Guarde o URL. Utilize o URL mais tarde.
5. Para criar um token de acesso pessoal, no menu de lista pendente da conta de utilizador, selecione **meu perfil**.
6. Na página de informações do perfil, selecione **segurança**.
7. Sobre o **Security** separador, selecione **Add**.
8. Sobre o **criar um token de acesso pessoal** página:
   1. Introduza um **Descrição** para o token.
   2. Na **expira em** lista, selecione **180 dias**.
   3. Na **contas** lista, selecione **todas as contas acessíveis**.
   4. Selecione o **todos os âmbitos** opção.
   5. Selecione **criar Token**.
9. O novo token é apresentado na **Tokens de acesso pessoal** lista. Selecione **cópia Token**e, em seguida, guarde o valor do token para utilizar mais tarde.
10. Avance para o Connect em seu laboratório para a secção de repositório.

## <a name="use-azure-portal"></a>Utilizar o portal do Azure
Esta secção fornece passos para adicionar um repositório de artefactos a um laboratório no portal do Azure. 

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione **mais serviços**e, em seguida, selecione **DevTest Labs** na lista de serviços.
3. Na lista de laboratórios, selecione o seu laboratório. 
4. Selecione **Konfigurace a zásady** no menu da esquerda.
5. Selecione **repositórios** sob **recursos externos** secção no menu da esquerda.
6. Selecione **+ adicionar** na barra de ferramentas.

    ![Botão de repositório de adicionar](./media/devtest-lab-add-repo/devtestlab-add-repo.png)
5. Sobre o **repositórios** , especifique as seguintes informações:
  1. **Nome**. Introduza um nome para o repositório.
  2. **Url de Clone de Git**. Introduza o URL de clone de Git HTTPS que copiou anteriormente partir do GitHub ou dos serviços de DevOps do Azure.
  3. **Ramo**. Para obter as definições, introduza o ramo.
  4. **Token de acesso pessoal**. Introduza o token de acesso pessoal que obteve anteriormente do GitHub ou dos serviços de DevOps do Azure.
  5. **Caminhos de pastas**. Introduza pelo menos um caminho de pasta relativo para o URL do clone que contém o artefacto ou definições de modelo do Resource Manager. Quando especificar um subdiretório, certifique-se de que incluir a barra no caminho da pasta.

        ![Área de repositórios](./media/devtest-lab-add-repo/devtestlab-repo-blade.png)
6. Selecione **Guardar**.

## <a name="use-azure-resource-manager-template"></a>Utilizar o modelo do Azure Resource Manager
Modelos de gestão de recursos (Azure Resource Manager) do Azure são ficheiros JSON que descrevem os recursos no Azure que pretende criar. Para obter mais informações sobre estes modelos, consulte [modelos Authoring Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

Esta secção fornece passos para adicionar um repositório de artefactos a um laboratório com um modelo Azure Resource Manager.  O modelo cria o laboratório se ainda não exista. 

### <a name="template"></a>Modelo
O modelo de exemplo utilizado neste artigo reúne as seguintes informações através de parâmetros. A maioria dos parâmetros tem predefinições inteligentes, mas há alguns valores que tem de ser especificados. Tem de especificar o nome de laboratório, URI para o repositório de artefactos e o token de segurança para o repositório. 

- Nome de laboratório.
- Nome a apresentar para o repositório de artefactos na interface de utilizador de DevTest Labs (IU). O valor predefinido é: `Team Repository`.
- URI para o repositório (exemplo: `https://github.com/<myteam>/<nameofrepo>.git` ou `"https://MyProject1.visualstudio.com/DefaultCollection/_git/TeamArtifacts"`.
- Ramo no repositório que contém os artefactos. O valor predefinido é: `master`.
- Nome da pasta que contém os artefactos. O valor predefinido é: `/Artifacts`.
- Tipo de repositório. Valores permitidos são `VsoGit` ou `GitHub`.
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
Existem algumas formas de implementar o modelo para o Azure e ter o recurso criado, se não existir ou atualizados, se existir. Para obter detalhes, veja os artigos seguintes:

- [Implementar recursos com modelos do Resource Manager e o Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
- [Implementar recursos com modelos do Resource Manager e a CLI do Azure](../azure-resource-manager/resource-group-template-deploy-cli.md)
- [Implementar recursos com modelos do Resource Manager e o Portal do Azure](../azure-resource-manager/resource-group-template-deploy-portal.md)
- [Implementar recursos com modelos do Resource Manager e a API REST do Resource Manager](../azure-resource-manager/resource-group-template-deploy-rest.md)

Vamos continuar e veja como implementar o modelo no PowerShell. Cmdlets utilizados para implementar o modelo são específicas do contexto, para que o inquilino atual e a subscrição atual são utilizados. Uso [Set-AzContext](/powershell/module/az.profile/set-azcontext) antes de implementar o modelo, se necessário, para alterar o contexto.

Primeiro, crie um grupo de recursos utilizando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Se o grupo de recursos que pretende utilizar já existir, ignore este passo.

```powershell
New-AzResourceGroup -Name MyLabResourceGroup1 -Location westus
```

Em seguida, crie uma implementação para o grupo de recursos utilizando [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment). Este cmdlet aplica as alterações de recursos para o Azure. Várias implementações de recurso podem ser feitas para qualquer grupo de recursos específico. Se estiver a implementar várias vezes para o mesmo grupo de recursos, certifique-se de que o nome de cada implementação é exclusivo.

```powershell
New-AzResourceGroupDeployment `
    -Name MyLabResourceGroup-Deployment1 `
    -ResourceGroupName MyLabResourceGroup1 `
    -TemplateFile azuredeploy.json `
    -TemplateParameterFile azuredeploy.parameters.json
```

Depois de New-AzResourceGroupDeployment executada com êxito, o comando devolve informações importantes, como o estado de aprovisionamento (deve ser concluída com êxito) e saídas para o modelo.
 
## <a name="use-azure-powershell"></a>Utilizar o Azure PowerShell 
Esta seção fornece um script do PowerShell de exemplo que pode ser utilizado para adicionar um repositório de artefactos a um laboratório. Se não tiver o Azure PowerShell, veja [como instalar e configurar o Azure PowerShell](/powershell/azure/overview?view=azps-1.2.0) para obter instruções detalhadas para instalá-lo.

### <a name="full-script"></a>Script completo
Eis o script completo, incluindo algumas mensagens detalhadas e comentários:

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
See https://azure.microsoft.com/en-us/documentation/articles/devtest-lab-add-artifact-repo/ for instructions to get personal access token

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

Write-Verbose @"Properties to be passed to New-AzResource:$($propertiesObject | Out-String)"@

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
O exemplo seguinte mostra como executar o script: 

```powershell
Set-AzContext -SubscriptionId <Your Azure subscription ID>

.\New-DevTestLabArtifactRepository.ps1 -LabName "mydevtestlab" -LabResourceGroupName "mydtlrg" -ArtifactRepositoryName "MyTeam Repository" -RepositoryUri "https://github.com/<myteam>/<nameofrepo>.git" -PersonalAccessToken "1111...." -SourceType "GitHub"
```


### <a name="parameters"></a>Parâmetros
O script do PowerShell de exemplo neste artigo usa os seguintes parâmetros:

| Parâmetro | Descrição | 
| --------- | ----------- | 
| LabName | O nome do laboratório. |
| ArtifactRepositoryName | Nome para o novo repositório de artefactos. O script cria um nome aleatório para o repositório, se não for especificado. |
| ArtifactRepositoryDisplayName | Nome a apresentar para o repositório de artefactos. Este é o nome que aparece no portal do Azure (https://portal.azure.com) ao visualizar todos os repositórios de artefacto para um laboratório. |
| RepositoryUri | URI para o repositório. Exemplos: `https://github.com/<myteam>/<nameofrepo>.git` ou `"https://MyProject1.visualstudio.com/DefaultCollection/_git/TeamArtifacts"`.| 
| RepositoryBranch | Ramo no qual artefacto arquivos podem ser encontrados. Por predefinição 'Mestra'. | 
| FolderPath | Pasta sob a qual os artefactos podem ser encontrados. Por predefinição ' / dos artefactos |
| PersonalAccessToken | Token de segurança para aceder ao repositório do GitHub ou VSOGit. Consulte a secção de pré-requisitos para obter instruções para obter o token de acesso pessoal |
| sourceType | Se o artefacto é repositório VSOGit ou do GitHub. |

O próprio repositório tem um internos de nomes para identificação, que é diferente que o nome a apresentar que é visto no portal do Azure. Não vê o nome interno com o portal do Azure, mas vê-lo ao utilizar APIs REST do Azure ou do Azure PowerShell. O script fornece um nome, se não for especificado pelo utilizador do nosso script.

```powershell
#Set artifact repository name, if not set by user
if ($ArtifactRepositoryName -eq $null){
    $ArtifactRepositoryName = "PrivateRepo" + (Get-Random -Maximum 999)
}
```

### <a name="powershell-commands-used-in-the-script"></a>Comandos do PowerShell utilizados no script

| Comando do PowerShell | Notas |
| ------------------ | ----- |
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Este comando é utilizado para obter detalhes sobre o laboratório, como a localização do mesmo. |
| [New-AzResource](/powershell/module/az.resources/new-azresource) | Não existe nenhum comando específico para adicionar repositórios de artefacto. Genérica [New-AzResource](/powershell/module/az.resources/new-azresource) cmdlet faz o trabalho. Este cmdlet necessita de um a **ResourceId** ou o **ResourceName** e **ResourceType** par saber o tipo de recurso para criar. Este script de exemplo utiliza o nome do recurso e o par de tipo de recurso. <br/><br/>Tenha em atenção que está a criar a origem do repositório de artefactos na mesma localização e sob o mesmo grupo de recursos como o laboratório.|

O script adiciona um novo recurso para a subscrição atual. Uso [Get-AzContext](/powershell/module/az.profile/get-azcontext) para ver estas informações. Uso [Set-AzContext](/powershell/module/az.profile/set-azcontext) para definir o inquilino atual e a subscrição.

A melhor forma de descobrir o nome de recurso e informações de tipo de recurso é utilizar o [APIs de REST do Azure de unidade de teste](https://azure.github.io/projects/apis/) Web site. Veja a [DevTest Labs – 2016 a 05-15](http://aka.ms/dtlrestapis) fornecedor para ver as APIs REST disponível para o fornecedor de DevTest Labs. Os utilizadores de script o seguinte ID de recurso. 

```powershell
"/subscriptions/$SubscriptionId/resourceGroups/$($LabResource.ResourceGroupName)/providers/Microsoft.DevTestLab/labs/$LabName/artifactSources/$ArtifactRepositoryName"
```
 
O tipo de recurso é tudo o que apresentados a seguir "providers" no URI, exceto para itens listados em chaves de saída. O nome do recurso é tudo o que viu os colchetes. Se mais de um item é esperado para o nome do recurso, separe cada item com uma barra, como fizemos. 

```powershell
$resourcetype = 'Microsoft.DevTestLab/labs/artifactSources'
$resourceName = $LabName + '/' + $ArtifactRepositoryName
```


## <a name="next-steps"></a>Passos Seguintes
- [Especifique os artefactos obrigatórios para o laboratório no Azure DevTest Labs](devtest-lab-mandatory-artifacts.md)
- [Criar artefactos personalizados para a máquina virtual do DevTest Labs](devtest-lab-artifact-author.md)
- [Diagnosticar falhas de artefactos no laboratório](devtest-lab-troubleshoot-artifact-failure.md)

