---
title: Implantar o espaço de trabalho Studio (clássico) com Azure Resource Manager
titleSuffix: ML Studio (classic) - Azure
description: Como implantar um espaço de trabalho para Azure Machine Learning Studio (clássico) usando o modelo Azure Resource Manager
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 02/05/2018
ms.openlocfilehash: ff9c123c0fecd26aba6ecbbda4a915d928b99f2f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75454726"
---
# <a name="deploy-azure-machine-learning-studio-classic-workspace-using-azure-resource-manager"></a>Implantar Azure Machine Learning Studio espaço de trabalho (clássico) usando Azure Resource Manager

Utilizar um modelo de implementação poupa tempo ao fornecer-lhe uma forma dimensionável para o Azure Resource Manager implantar componentes interconectados com uma validação e mecanismo de repetição. Para configurar os espaços de trabalho do Azure Machine Learning Studio (clássico), por exemplo, você precisa primeiro configurar uma conta de armazenamento do Azure e, em seguida, implantar seu espaço de trabalho. Imagine a fazê-lo manualmente para centenas de áreas de trabalho. Uma alternativa mais fácil é usar um modelo de Azure Resource Manager para implantar um espaço de trabalho do Studio (clássico) e todas as suas dependências. Este artigo leva-o ao longo deste processo passo a passo. Para uma excelente descrição geral do Azure Resource Manager, consulte [descrição geral do Azure Resource Manager](../../azure-resource-manager/management/overview.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="step-by-step-create-a-machine-learning-workspace"></a>Passo a passo: criar uma área de trabalho do Machine Learning
Criaremos um grupo de recursos do Azure e implantaremos uma nova conta de armazenamento do Azure e um novo espaço de trabalho Azure Machine Learning Studio (clássico) usando um modelo do Resource Manager. Assim que a implementação estiver concluída, iremos irá imprimir informações importantes sobre as áreas de trabalho que foram criados (a chave primária, o workspaceID e o URL para a área de trabalho).

### <a name="create-an-azure-resource-manager-template"></a>Criar um modelo Azure Resource Manager

Uma área de trabalho do Machine Learning requer uma conta de armazenamento do Azure para armazenar o conjunto de dados ligado ao mesmo.
O modelo seguinte utiliza o nome do grupo de recursos para gerar o nome de conta de armazenamento e o nome de área de trabalho.  Ele também usa o nome da conta de armazenamento como uma propriedade ao criar a área de trabalho.

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
Guarde este modelo como ficheiro mlworkspace.json em c:\temp\.

### <a name="deploy-the-resource-group-based-on-the-template"></a>Implementar o grupo de recursos, com base no modelo

* Abrir o PowerShell
* Instalar módulos para o Azure Resource Manager e gestão de serviço do Azure

```powershell
# Install the Azure Resource Manager modules from the PowerShell Gallery (press “A”)
Install-Module Az -Scope CurrentUser

# Install the Azure Service Management modules from the PowerShell Gallery (press “A”)
Install-Module Azure -Scope CurrentUser
```

   Estes passos transferirem e instalar os módulos necessários para concluir os passos restantes. Só precisa ser feito uma vez no ambiente em que está a executar os comandos do PowerShell.

* Autenticar para o Azure

```powershell
# Authenticate (enter your credentials in the pop-up window)
Connect-AzAccount
```
Este passo tem de ser repetido para cada sessão. Uma vez autenticado, deverá ser apresentadas informações da sua subscrição.

![Conta do Azure](./media/deploy-with-resource-manager-template/azuresubscription.png)

Agora que temos acesso ao Azure, podemos criar o grupo de recursos.

* Criar um grupo de recursos

```powershell
$rg = New-AzResourceGroup -Name "uniquenamerequired523" -Location "South Central US"
$rg
```

Certifique-se de que o grupo de recursos está aprovisionado corretamente. **ProvisioningState** deve ser "com êxito."
O nome do grupo de recursos é utilizado pelo modelo para gerar o nome de conta de armazenamento. O nome da conta de armazenamento tem de ter entre 3 e 24 carateres de comprimento e utilizar números e letras minúsculas apenas.

![Grupo de Recursos](./media/deploy-with-resource-manager-template/resourcegroupprovisioning.png)

* Ao utilizar a implementação do grupo de recursos, implemente uma nova área de trabalho do Machine Learning.

```powershell
# Create a Resource Group, TemplateFile is the location of the JSON template.
$rgd = New-AzResourceGroupDeployment -Name "demo" -TemplateFile "C:\temp\mlworkspace.json" -ResourceGroupName $rg.ResourceGroupName
```

Depois de concluída a implementação, é simples para propriedades de acesso da área de trabalho que implementou. Por exemplo, pode acessar o Token de chave primária.

```powershell
# Access Azure Machine Learning Studio Workspace Token after its deployment.
$rgd.Outputs.mlWorkspaceToken.Value
```

Outra maneira de recuperar Tokens do espaço de trabalho existente é usar o comando Invoke-AzResourceAction. Por exemplo, pode listar os tokens primários e secundários de todas as áreas de trabalho.

```powershell
# List the primary and secondary tokens of all workspaces
Get-AzResource |? { $_.ResourceType -Like "*MachineLearning/workspaces*"} |ForEach-Object { Invoke-AzResourceAction -ResourceId $_.ResourceId -Action listworkspacekeys -Force}
```
Depois que o espaço de trabalho for provisionado, você também poderá automatizar muitas tarefas Azure Machine Learning Studio (clássicas) usando o [módulo do PowerShell para Azure Machine Learning Studio (clássico)](https://aka.ms/amlps).

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [criar modelos do Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md).
* Veja a [repositório de modelos de início rápido do Azure](https://github.com/Azure/azure-quickstart-templates).
* Assista a este vídeo sobre [do Azure Resource Manager](https://channel9.msdn.com/Events/Ignite/2015/C9-39).
* Consulte a [ajuda de referência do modelo do Resource Manager](https://docs.microsoft.com/azure/templates/microsoft.machinelearning/allversions)

<!--Link references-->
