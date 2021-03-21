---
title: Adicione um repositório de artefactos ao seu laboratório em Azure DevTest Labs | Microsoft Docs
description: Aprenda a especificar o seu próprio repositório de artefactos para o seu laboratório em Azure DevTest Labs para armazenar ferramentas indisponíveis no repositório de artefactos públicos.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: da4e345b18a46226853d71bbf66af0487f1a761f
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102502200"
---
# <a name="add-an-artifact-repository-to-your-lab-in-devtest-labs"></a>Adicione um repositório de artefactos ao seu laboratório em Laboratórios DevTest
A DevTest Labs permite especificar um artefacto a ser adicionado a um VM no momento da criação do VM ou após a criação do VM. Este artefacto pode ser uma ferramenta ou uma aplicação que pretende instalar no VM. Os artefactos são definidos num ficheiro JSON carregado de um repositório GitHub ou Azure DevOps Git.

O [repositório de artefactos públicos,](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts)mantido pela DevTest Labs, fornece muitas ferramentas comuns tanto para o Windows como para o Linux. Uma ligação a este repositório é automaticamente adicionada ao seu laboratório. Você pode criar o seu próprio repositório de artefactos com ferramentas específicas que não estão disponíveis no repositório de artefactos públicos. Para aprender a criar artefactos personalizados, consulte [Criar artefactos personalizados.](devtest-lab-artifact-author.md)

Este artigo fornece informações sobre como adicionar o seu repositório de artefactos personalizados usando o portal Azure, modelos de Gestão de Recursos Azure e Azure PowerShell. Você pode automatizar a adição de um repositório de artefactos a um laboratório escrevendo scripts PowerShell ou CLI.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos
Para adicionar um repositório ao seu laboratório, primeiro, obtenha informações chave do seu repositório. As secções seguintes descrevem como obter as informações necessárias para repositórios que estão hospedados no **GitHub** ou **Azure DevOps**.

### <a name="get-the-github-repository-clone-url-and-personal-access-token"></a>Obtenha o URL do clone do repositório GitHub e o token de acesso pessoal

1. Vá à página inicial do repositório GitHub que contém as definições de modelo de artefacto ou gestor de recursos.
2. Selecione **Clone or download** (Clonar ou transferir).
3. Para copiar o URL para a área de transferência, selecione o botão **url de clone HTTPS.** Guarde o URL para utilização posterior.
4. No canto superior direito do GitHub, selecione a imagem de perfil e, em seguida, selecione **Definições**.
5. No menu **de definições pessoais** à esquerda, selecione **Definições de Programação**.
6. Selecione **Fichas de acesso pessoal** no menu esquerdo.
7. **Selecione Gerer novo símbolo**.
8. Na página **de token de acesso pessoal Novo,** sob **descrição de Token,** insira uma descrição. Aceite os itens predefinidos nos **âmbitos Select** e, em seguida, selecione **Gerar Token**.
9. Salve o símbolo gerado. Usas o símbolo mais tarde.
10. Feche o GitHub.   

### <a name="get-the-azure-repos-clone-url-and-personal-access-token"></a>Obtenha o URL clone do Azure Repos e o token de acesso pessoal
1. Vá à página inicial da sua coleção de equipas (por exemplo, `https://contoso-web-team.visualstudio.com` e, em seguida, selecione o seu projeto.
2. Na página inicial do projeto, selecione **Código.**
3. Para ver o URL do clone, na página **código** do projeto, selecione **Clone**.
4. Salve a URL. Use a URL mais tarde.
5. Para criar um token de acesso pessoal, no menu suspenso da conta de utilizador, selecione **O meu perfil**.
6. Na página de informações de perfil, selecione **Security**.
7. No **separador > de Acesso pessoal,** selecione **+ Novo Token**.
8. Na **página Criar um novo token de acesso pessoal:**
   1. Insira um **nome** para o símbolo.
   2. Na lista da **Organização,** selecione **Todas as organizações acessíveis.**
   3. Na lista **de Expiração (UTC),** selecione **90 dias,** ou um período de validade definido personalizado.
   4. Selecione a opção **acesso completo** para Âmbitos.
   5. Selecione **Criar**.
9. O novo símbolo aparece na lista de **Tokens de Acesso Pessoal.** Selecione **Copy Token** e, em seguida, guarde o valor simbólico para utilização posterior.
10. Continue até ao laboratório Ligar o seu laboratório à secção de repositório.

## <a name="use-azure-portal"></a>Utilizar o portal do Azure
Esta secção fornece passos para adicionar um repositório de artefactos a um laboratório no portal Azure.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione **Mais Serviços** e, em seguida, selecione **DevTest Labs** da lista de serviços.
3. Da lista de laboratórios, selecione o seu laboratório.
4. Selecione **Configuração e políticas** no menu à esquerda.
5. Selecione **Repositórios** na secção **de recursos externos** no menu esquerdo.
6. **Selecione + Adicione** na barra de ferramentas.

    ![O botão de repositório adicionar](./media/devtest-lab-add-repo/devtestlab-add-repo.png)
5. Na página **repositórios,** especifique as seguintes informações:
   1. **Nome**. Insira um nome para o repositório.
   2. **Url Git Clone**. Introduza o URL de clone do Git HTTPS que copiou anteriormente dos Serviços GitHub ou Azure DevOps.
   3. **Filial**. Para obter as suas definições, entre no ramo.
   4. **Ficha de Acesso Pessoal**. Insira o sinal de acesso pessoal que obteve anteriormente dos Serviços GitHub ou Azure DevOps.
   5. **Caminhos de pasta**. Introduza pelo menos um caminho de pasta em relação ao URL do clone que contém as definições do seu modelo de artefacto ou gestor de recursos. Quando especificar uma subdiretória, certifique-se de que inclui o corte dianteiro no caminho da pasta.

        ![Área de repositórios](./media/devtest-lab-add-repo/devtestlab-repo-blade.png)
6. Selecione **Guardar**.

## <a name="use-azure-resource-manager-template"></a>Use o modelo de gestor de recursos Azure
Os modelos Azure Resource Management (Azure Resource Manager) são ficheiros JSON que descrevem recursos em Azure que pretende criar. Para obter mais informações sobre estes modelos, consulte os modelos do [Gestor de Recursos Azure.](../azure-resource-manager/templates/template-syntax.md)

Esta secção fornece passos para adicionar um repositório de artefactos a um laboratório usando um modelo de Gestor de Recursos Azure.  O modelo cria o laboratório se já não existe.

### <a name="template"></a>Modelo
O modelo de amostra utilizado neste artigo recolhe as seguintes informações através de parâmetros. A maioria dos parâmetros tem padrão inteligente, mas há alguns valores que devem ser especificados. Tem de especificar o nome do laboratório, URI para o repositório de artefactos, e o símbolo de segurança para o repositório.

- Nome do laboratório.
- Nome de exibição para o repositório de artefactos na interface de utilizador DavTest Labs (UI). O valor predefinido é: `Team Repository` .
- URI ao repositório (Exemplo: `https://github.com/<myteam>/<nameofrepo>.git` ou `"https://MyProject1.visualstudio.com/DefaultCollection/_git/TeamArtifacts"` .
- Ramo no repositório que contém artefactos. O valor predefinido é: `master` .
- Nome da pasta que contém artefactos. O valor predefinido é: `/Artifacts` .
- Tipo de repositório. Os valores permitidos são `VsoGit` ou `GitHub` .
- Ficha de acesso para o repositório.

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
Existem algumas formas de implantar o modelo para Azure e ter o recurso criado, se não existir, ou atualizado, se existir. Para mais detalhes, consulte os seguintes artigos:

- [Implementar recursos com modelos do Resource Manager e o Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)
- [Implementar recursos com modelos do Resource Manager e do CLI do Azure](../azure-resource-manager/templates/deploy-cli.md)
- [Implementar recursos com modelos do Resource Manager e do Portal do Azure](../azure-resource-manager/templates/deploy-portal.md)
- [Implementar recursos com modelos do Resource Manager e API REST do Resource Manager](../azure-resource-manager/templates/deploy-rest.md)

Vamos em frente e ver como implementar o modelo no PowerShell. Os cmdlets utilizados para implantar o modelo são específicos do contexto, pelo que o inquilino atual e a subscrição atual são usados. Utilize [o Set-AzContext](/powershell/module/az.accounts/set-azcontext) antes de implementar o modelo, se necessário, para alterar o contexto.

Em primeiro lugar, criar um grupo de recursos utilizando [o New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Se o grupo de recursos que pretende utilizar já existir, ignore este passo.

```powershell
New-AzResourceGroup -Name MyLabResourceGroup1 -Location westus
```

Em seguida, crie uma implementação para o grupo de recursos utilizando [o New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment). Este cmdlet aplica as alterações de recursos ao Azure. Várias implementações de recursos podem ser feitas a qualquer grupo de recursos. Se estiver a implantar várias vezes para o mesmo grupo de recursos, certifique-se de que o nome de cada implantação é único.

```powershell
New-AzResourceGroupDeployment `
    -Name MyLabResourceGroup-Deployment1 `
    -ResourceGroupName MyLabResourceGroup1 `
    -TemplateFile azuredeploy.json `
    -TemplateParameterFile azuredeploy.parameters.json
```

Após New-AzResourceGroupDeployment executado com sucesso, o comando produz informações importantes como o estado de provisionamento (deve ser bem sucedido) e quaisquer saídas para o modelo.

## <a name="use-azure-powershell"></a>Utilizar o Azure PowerShell
Esta secção fornece-lhe uma amostra do script PowerShell que pode ser usado para adicionar um repositório de artefactos a um laboratório. Se não tiver Azure PowerShell, consulte [Como instalar e configurar a Azure PowerShell](/powershell/azure/) para obter instruções detalhadas para a sua instalação.

### <a name="full-script"></a>Script completo
Aqui está o roteiro completo, incluindo algumas mensagens verbosas e comentários:

**New-DevTestLabArtifactRepository.ps1:**

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
Script creates a random name for the repository if it is not specified.

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
O exemplo a seguir mostra-lhe como executar o script:

```powershell
Set-AzContext -SubscriptionId <Your Azure subscription ID>

.\New-DevTestLabArtifactRepository.ps1 -LabName "mydevtestlab" -LabResourceGroupName "mydtlrg" -ArtifactRepositoryName "MyTeam Repository" -RepositoryUri "https://github.com/<myteam>/<nameofrepo>.git" -PersonalAccessToken "1111...." -SourceType "GitHub"
```


### <a name="parameters"></a>Parâmetros
A amostra do script PowerShell neste artigo requer os seguintes parâmetros:

| Parâmetro | Descrição |
| --------- | ----------- |
| Nome lab | O nome do laboratório. |
| ArtefactoSRepositórioName | Nome para o novo repositório de artefactos. O script cria um nome aleatório para o repositório se não for especificado. |
| ArtefactoRepositoryDisplayName | Nome de exibição para o repositório de artefactos. Este é o nome que aparece no portal Azure ( https://portal.azure.com) ao ver todos os repositórios de artefactos para um laboratório. |
| RepositórioUri | Uri para o repositório. Exemplos: `https://github.com/<myteam>/<nameofrepo>.git` ou `"https://MyProject1.visualstudio.com/DefaultCollection/_git/TeamArtifacts"` .|
| RepositoryBranch | Ramo no qual podem ser encontrados ficheiros de artefactos. Incumprimentos para 'mestre'. |
| PastaPata | Pasta sob a qual os artefactos podem ser encontrados. Predefinições para '/Artefactos' |
| PersonalAccessToken | Ficha de segurança para aceder ao repositório GitHub ou VSOGit. Consulte a secção de pré-requisitos para obter ficha de acesso pessoal |
| SourceType | Quer o artefacto seja vsoGit ou repositório do GitHub. |

O próprio repositório precisa de um nome interno para identificação, o que é diferente do nome de exibição que é visto no portal Azure. Não vê o nome interno usando o portal Azure, mas vê-o quando utiliza APIs Azure REST ou Azure PowerShell. O script fornece um nome, se um não for especificado pelo utilizador do nosso script.

```powershell
#Set artifact repository name, if not set by user
if ($ArtifactRepositoryName -eq $null){
    $ArtifactRepositoryName = "PrivateRepo" + (Get-Random -Maximum 999)
}
```

### <a name="powershell-commands-used-in-the-script"></a>Comandos PowerShell usados no script

| Comando do PowerShell | Notas |
| ------------------ | ----- |
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Este comando é usado para obter detalhes sobre o laboratório, como a sua localização. |
| [New-AzResource](/powershell/module/az.resources/new-azresource) | Não há um comando específico para adicionar repositórios de artefactos. O cmdlet genérico [da New-AzResource](/powershell/module/az.resources/new-azresource) faz o trabalho. Este cmdlet precisa do **resourceId** ou do par **ResourceName** e **ResourceType** para saber o tipo de recurso a criar. Este script de amostra utiliza o nome de recurso e o par do tipo de recurso. <br/><br/>Note que está a criar a fonte de repositório de artefactos no mesmo local e no mesmo grupo de recursos que o laboratório.|

O script adiciona um novo recurso à subscrição atual. Utilize [o Get-AzContext](/powershell/module/az.accounts/get-azcontext) para ver esta informação. Utilize [o Set-AzContext](/powershell/module/az.accounts/set-azcontext) para definir o inquilino e a subscrição atuais.

A melhor maneira de descobrir o nome do recurso e informações do tipo de recurso é utilizar o website [Test Drive Azure REST APIs.](https://azure.github.io/projects/apis/) Consulte o fornecedor [DevTest Labs – 2016-05-15](https://aka.ms/dtlrestapis) para ver as APIs rest disponíveis para o fornecedor DevTest Labs. Os utilizadores do script têm o seguinte ID de recursos.

```powershell
"/subscriptions/$SubscriptionId/resourceGroups/$($LabResource.ResourceGroupName)/providers/Microsoft.DevTestLab/labs/$LabName/artifactSources/$ArtifactRepositoryName"
```

O tipo de recurso é tudo listado após 'fornecedores' no URI, com exceção dos itens listados nos suportes encaracolados. O nome do recurso é tudo o que se vê nos suportes encaracolados. Se for esperado mais de um item para o nome do recurso, separe cada item com um corte como fizemos.

```powershell
$resourcetype = 'Microsoft.DevTestLab/labs/artifactSources'
$resourceName = $LabName + '/' + $ArtifactRepositoryName
```


## <a name="next-steps"></a>Passos seguintes
- [Especifique artefactos obrigatórios para o seu laboratório em Azure DevTest Labs](devtest-lab-mandatory-artifacts.md)
- [Crie artefactos personalizados para a sua máquina virtual DevTest Labs](devtest-lab-artifact-author.md)
- [Diagnosticar falhas de artefactos no laboratório](devtest-lab-troubleshoot-artifact-failure.md)
