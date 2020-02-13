---
title: Deploy Studio (clássico) espaço de trabalho com O Gestor de Recursos Azure
titleSuffix: ML Studio (classic) - Azure
description: Como implementar um espaço de trabalho para o Azure Machine Learning Studio (clássico) utilizando o modelo de Gestor de Recursos Azure
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 02/05/2018
ms.openlocfilehash: f05ef9472f11a5025e9856cfb207cc0859f24c3e
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77169356"
---
# <a name="deploy-azure-machine-learning-studio-classic-workspace-using-azure-resource-manager"></a>Implementar o Estúdio de Aprendizagem automática Azure (clássico) Workspace utilizando o Gestor de Recursos Azure

Utilizar um modelo de implementação poupa tempo ao fornecer-lhe uma forma dimensionável para o Azure Resource Manager implantar componentes interconectados com uma validação e mecanismo de repetição. Para configurar o Azure Machine Learning Studio (clássico) Workspaces, por exemplo, é necessário configurar primeiro uma conta de armazenamento Azure e depois implementar o seu espaço de trabalho. Imagine a fazê-lo manualmente para centenas de áreas de trabalho. Uma alternativa mais fácil é usar um modelo de Gestor de Recursos Azure para implantar um Espaço de Trabalho studio (clássico) e todas as suas dependências. Este artigo leva-o ao longo deste processo passo a passo. Para uma excelente visão geral do Gestor de Recursos Azure, consulte a [visão geral do Gestor de Recursos do Azure.](../../azure-resource-manager/management/overview.md)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="step-by-step-create-a-machine-learning-workspace"></a>Passo a passo: criar uma área de trabalho do Machine Learning
Criaremos um grupo de recursos Azure, em seguida, implementaremos uma nova conta de armazenamento Azure e um novo Azure Machine Learning Studio (clássico) Workspace usando um modelo de Gestor de Recursos. Assim que a implementação estiver concluída, iremos irá imprimir informações importantes sobre as áreas de trabalho que foram criados (a chave primária, o workspaceID e o URL para a área de trabalho).

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

* Criar um grupo de recursos:

```powershell
$rg = New-AzResourceGroup -Name "uniquenamerequired523" -Location "South Central US"
$rg
```

Certifique-se de que o grupo de recursos está aprovisionado corretamente. **O Estado deve** ser "Bem sucedido".
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

Outra forma de recuperar fichas do espaço de trabalho existente é usar o comando Invoke-AzResourceAction. Por exemplo, pode listar os tokens primários e secundários de todas as áreas de trabalho.

```powershell
# List the primary and secondary tokens of all workspaces
Get-AzResource |? { $_.ResourceType -Like "*MachineLearning/workspaces*"} |ForEach-Object { Invoke-AzResourceAction -ResourceId $_.ResourceId -Action listworkspacekeys -Force}
```
Depois do espaço de trabalho ser aprovisionado, também pode automatizar muitas tarefas do Azure Machine Learning Studio (clássico) utilizando o [Módulo PowerShell para o Azure Machine Learning Studio (clássico)](https://aka.ms/amlps).

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre a autoria de Modelos de [Gestor de Recursos Azure](../../azure-resource-manager/templates/template-syntax.md).
* Veja o [Repositório de Modelos De Arranque Rápido Azure](https://github.com/Azure/azure-quickstart-templates).
* Veja este vídeo sobre [o Gestor de Recursos Azure.](https://channel9.msdn.com/Events/Ignite/2015/C9-39)
* Consulte a ajuda de referência do [modelo do Gestor](https://docs.microsoft.com/azure/templates/microsoft.machinelearning/allversions) de Recursos

<!--Link references-->
