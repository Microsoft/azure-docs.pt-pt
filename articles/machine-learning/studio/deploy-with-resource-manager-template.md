---
title: Implantar o espaço de trabalho Studio (clássico) com Azure Resource Manager
titleSuffix: Azure Machine Learning Studio (classic)
description: Como implantar um espaço de trabalho para Azure Machine Learning Studio (clássico) usando o modelo Azure Resource Manager
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 02/05/2018
ms.openlocfilehash: e157ef3944e6c7e231c78e5bce826ccddb1a59f8
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73493051"
---
# <a name="deploy-azure-machine-learning-studio-classic-workspace-using-azure-resource-manager"></a>Implantar Azure Machine Learning Studio espaço de trabalho (clássico) usando Azure Resource Manager

O uso de um modelo de implantação Azure Resource Manager poupa tempo fornecendo uma maneira escalonável de implantar componentes interconectados com um mecanismo de validação e repetição. Para configurar os espaços de trabalho do Azure Machine Learning Studio (clássico), por exemplo, você precisa primeiro configurar uma conta de armazenamento do Azure e, em seguida, implantar seu espaço de trabalho. Imagine fazer isso manualmente para centenas de espaços de trabalho. Uma alternativa mais fácil é usar um modelo de Azure Resource Manager para implantar um espaço de trabalho do Studio (clássico) e todas as suas dependências. Este artigo orienta você passo a passo deste processo. Para obter uma ótima visão geral do Azure Resource Manager, consulte [Azure Resource Manager visão geral](../../azure-resource-manager/resource-group-overview.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="step-by-step-create-a-machine-learning-workspace"></a>Passo a passo: criar um Workspace do Machine Learning
Criaremos um grupo de recursos do Azure e implantaremos uma nova conta de armazenamento do Azure e um novo espaço de trabalho Azure Machine Learning Studio (clássico) usando um modelo do Resource Manager. Depois que a implantação for concluída, vamos imprimir informações importantes sobre os espaços de trabalho que foram criados (a chave primária, o workspaceid e a URL para o espaço de trabalho).

### <a name="create-an-azure-resource-manager-template"></a>Criar um modelo de Azure Resource Manager

Um Workspace do Machine Learning requer uma conta de armazenamento do Azure para armazenar o conjunto de um vinculado a ele.
O modelo a seguir usa o nome do grupo de recursos para gerar o nome da conta de armazenamento e o nome do espaço de trabalho.  Ele também usa o nome da conta de armazenamento como uma propriedade ao criar o espaço de trabalho.

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "variables": {
        "namePrefix": "[resourceGroup().name]",
        "location": "[resourceGroup().location]",
        "mlVersion": "2016-04-01",
        "stgVersion": "2015-06-15",
        "storageAccountName": "[concat(variables('namePrefix'),'stg')]",
        "mlWorkspaceName": "[concat(variables('namePrefix'),'mlwk')]",
        "mlResourceId": "[resourceId('Microsoft.MachineLearning/workspaces', variables('mlWorkspaceName'))]",
        "stgResourceId": "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "storageAccountType": "Standard_LRS"
    },
    "resources": [
        {
            "apiVersion": "[variables('stgVersion')]",
            "name": "[variables('storageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "[variables('location')]",
            "properties": {
                "accountType": "[variables('storageAccountType')]"
            }
        },
        {
            "apiVersion": "[variables('mlVersion')]",
            "type": "Microsoft.MachineLearning/workspaces",
            "name": "[variables('mlWorkspaceName')]",
            "location": "[variables('location')]",
            "dependsOn": ["[variables('stgResourceId')]"],
            "properties": {
                "UserStorageAccountId": "[variables('stgResourceId')]"
            }
        }
    ],
    "outputs": {
        "mlWorkspaceObject": {"type": "object", "value": "[reference(variables('mlResourceId'), variables('mlVersion'))]"},
        "mlWorkspaceToken": {"type": "string", "value": "[listWorkspaceKeys(variables('mlResourceId'), variables('mlVersion')).primaryToken]"},
        "mlWorkspaceWorkspaceID": {"type": "string", "value": "[reference(variables('mlResourceId'), variables('mlVersion')).WorkspaceId]"},
        "mlWorkspaceWorkspaceLink": {"type": "string", "value": "[concat('https://studio.azureml.net/Home/ViewWorkspace/', reference(variables('mlResourceId'), variables('mlVersion')).WorkspaceId)]"}
    }
}

```
Salve este modelo como arquivo mlworkspace. JSON em c:\Temp\.

### <a name="deploy-the-resource-group-based-on-the-template"></a>Implantar o grupo de recursos, com base no modelo

* Abrir o PowerShell
* Instalar módulos para Azure Resource Manager e gerenciamento de serviços do Azure

```powershell
# Install the Azure Resource Manager modules from the PowerShell Gallery (press “A”)
Install-Module Az -Scope CurrentUser

# Install the Azure Service Management modules from the PowerShell Gallery (press “A”)
Install-Module Azure -Scope CurrentUser
```

   Estas etapas baixam e instalam os módulos necessários para concluir as etapas restantes. Isso só precisa ser feito uma vez no ambiente em que você está executando os comandos do PowerShell.

* Autenticar no Azure

```powershell
# Authenticate (enter your credentials in the pop-up window)
Connect-AzAccount
```
Esta etapa precisa ser repetida para cada sessão. Depois de autenticado, as informações de sua assinatura devem ser exibidas.

![Conta do Azure](./media/deploy-with-resource-manager-template/azuresubscription.png)

Agora que temos acesso ao Azure, podemos criar o grupo de recursos.

* Criar um grupo de recursos

```powershell
$rg = New-AzResourceGroup -Name "uniquenamerequired523" -Location "South Central US"
$rg
```

Verifique se o grupo de recursos está provisionado corretamente. **ProvisioningState** deve ser "Succeeded".
O nome do grupo de recursos é usado pelo modelo para gerar o nome da conta de armazenamento. O nome da conta de armazenamento deve ter entre 3 e 24 caracteres de comprimento e usar apenas números e letras minúsculas.

![Grupo de Recursos](./media/deploy-with-resource-manager-template/resourcegroupprovisioning.png)

* Usando a implantação do grupo de recursos, implante um novo Workspace do Machine Learning.

```powershell
# Create a Resource Group, TemplateFile is the location of the JSON template.
$rgd = New-AzResourceGroupDeployment -Name "demo" -TemplateFile "C:\temp\mlworkspace.json" -ResourceGroupName $rg.ResourceGroupName
```

Após a conclusão da implantação, é simples acessar as propriedades do espaço de trabalho implantado. Por exemplo, você pode acessar o token de chave primária.

```powershell
# Access Azure Machine Learning Studio Workspace Token after its deployment.
$rgd.Outputs.mlWorkspaceToken.Value
```

Outra maneira de recuperar Tokens do espaço de trabalho existente é usar o comando Invoke-AzResourceAction. Por exemplo, você pode listar os tokens primários e secundários de todos os espaços de trabalho.

```powershell
# List the primary and secondary tokens of all workspaces
Get-AzResource |? { $_.ResourceType -Like "*MachineLearning/workspaces*"} |ForEach-Object { Invoke-AzResourceAction -ResourceId $_.ResourceId -Action listworkspacekeys -Force}
```
Depois que o espaço de trabalho for provisionado, você também poderá automatizar muitas tarefas Azure Machine Learning Studio (clássicas) usando o [módulo do PowerShell para Azure Machine Learning Studio (clássico)](https://aka.ms/amlps).

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre a [criação de modelos de Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md).
* Examine o [repositório de modelos de início rápido do Azure](https://github.com/Azure/azure-quickstart-templates).
* Assista a este vídeo sobre [Azure Resource Manager](https://channel9.msdn.com/Events/Ignite/2015/C9-39).
* Consulte a [ajuda de referência do modelo do Resource Manager](https://docs.microsoft.com/azure/templates/microsoft.machinelearning/allversions)

<!--Link references-->
