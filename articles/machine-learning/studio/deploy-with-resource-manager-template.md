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
ms.openlocfilehash: 1ce147ac6c9b21176c738c21114c331b56bf667f
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84118471"
---
# <a name="deploy-azure-machine-learning-studio-classic-workspace-using-azure-resource-manager"></a>Implementar o Estúdio de Aprendizagem automática Azure (clássico) Workspace utilizando o Gestor de Recursos Azure

A utilização de um modelo de implementação do Gestor de Recursos Azure poupa-lhe tempo, dando-lhe uma forma escalável de implantar componentes interligados com um mecanismo de validação e retry. Para configurar o Azure Machine Learning Studio (clássico) Workspaces, por exemplo, é necessário configurar primeiro uma conta de armazenamento Azure e depois implementar o seu espaço de trabalho. Imagine fazer isto manualmente para centenas de espaços de trabalho. Uma alternativa mais fácil é usar um modelo de Gestor de Recursos Azure para implantar um Espaço de Trabalho studio (clássico) e todas as suas dependências. Este artigo leva-o a este processo passo a passo. Para uma excelente visão geral do Gestor de Recursos Azure, consulte a [visão geral do Gestor de Recursos do Azure.](../../azure-resource-manager/management/overview.md)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="step-by-step-create-a-machine-learning-workspace"></a>Passo a passo: criar um espaço de trabalho de aprendizagem automática
Criaremos um grupo de recursos Azure, em seguida, implementaremos uma nova conta de armazenamento Azure e um novo Azure Machine Learning Studio (clássico) Workspace usando um modelo de Gestor de Recursos. Assim que a implementação estiver concluída, imprimiremos informações importantes sobre os espaços de trabalho que foram criados (a chave primária, o espaço de trabalho ID e o URL para o espaço de trabalho).

### <a name="create-an-azure-resource-manager-template"></a>Criar um modelo de Gestor de Recursos Azure

Um espaço de trabalho de aprendizagem automática requer uma conta de armazenamento Azure para armazenar o conjunto de dados ligado ao mesmo.
O seguinte modelo usa o nome do grupo de recursos para gerar o nome da conta de armazenamento e o nome do espaço de trabalho.  Também usa o nome da conta de armazenamento como uma propriedade ao criar o espaço de trabalho.

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
Guarde este modelo como ficheiro mlworkspace.json sob c:\temp\.

### <a name="deploy-the-resource-group-based-on-the-template"></a>Implementar o grupo de recursos, com base no modelo

* Abrir o PowerShell
* Instale módulos para O Gestor de Recursos Azure e Gestão de Serviços Azure

```powershell
# Install the Azure Resource Manager modules from the PowerShell Gallery (press "A")
Install-Module Az -Scope CurrentUser

# Install the Azure Service Management modules from the PowerShell Gallery (press "A")
Install-Module Azure -Scope CurrentUser
```

   Estes passos descarregam e instalam os módulos necessários para completar os passos restantes. Isto só precisa de ser feito uma vez no ambiente onde está a executar os comandos PowerShell.

* Autenticar a Azure

```powershell
# Authenticate (enter your credentials in the pop-up window)
Connect-AzAccount
```
Este passo tem de ser repetido para cada sessão. Uma vez autenticados, as informações de subscrição devem ser apresentadas.

![Conta do Azure](./media/deploy-with-resource-manager-template/azuresubscription.png)

Agora que temos acesso ao Azure, podemos criar o grupo de recursos.

* Criar um grupo de recursos

```powershell
$rg = New-AzResourceGroup -Name "uniquenamerequired523" -Location "South Central US"
$rg
```

Verifique se o grupo de recursos está corretamente aprovisionado. **O Estado deve** ser "Bem sucedido".
O nome do grupo de recursos é usado pelo modelo para gerar o nome da conta de armazenamento. O nome da conta de armazenamento deve ter entre 3 e 24 caracteres de comprimento e utilizar números e letras minúsculas apenas.

![Grupo de Recursos](./media/deploy-with-resource-manager-template/resourcegroupprovisioning.png)

* Utilizando a implantação do grupo de recursos, implemente um novo espaço de trabalho de aprendizagem automática.

```powershell
# Create a Resource Group, TemplateFile is the location of the JSON template.
$rgd = New-AzResourceGroupDeployment -Name "demo" -TemplateFile "C:\temp\mlworkspace.json" -ResourceGroupName $rg.ResourceGroupName
```

Uma vez concluída a implementação, é simples aceder às propriedades do espaço de trabalho que implementou. Por exemplo, pode aceder ao Token chave primária.

```powershell
# Access Azure Machine Learning Studio Workspace Token after its deployment.
$rgd.Outputs.mlWorkspaceToken.Value
```

Outra forma de recuperar fichas do espaço de trabalho existente é usar o comando Invoke-AzResourceAction. Por exemplo, pode listar os símbolos primários e secundários de todos os espaços de trabalho.

```powershell
# List the primary and secondary tokens of all workspaces
Get-AzResource |? { $_.ResourceType -Like "*MachineLearning/workspaces*"} |ForEach-Object { Invoke-AzResourceAction -ResourceId $_.ResourceId -Action listworkspacekeys -Force}
```
Depois do espaço de trabalho ser aprovisionado, também pode automatizar muitas tarefas do Azure Machine Learning Studio (clássico) utilizando o [Módulo PowerShell para o Azure Machine Learning Studio (clássico)](https://aka.ms/amlps).

## <a name="next-steps"></a>Próximos passos

* Saiba mais sobre a autoria de Modelos de [Gestor de Recursos Azure](../../azure-resource-manager/templates/template-syntax.md).
* Veja o [Repositório de Modelos De Arranque Rápido Azure](https://github.com/Azure/azure-quickstart-templates).
* Veja este vídeo sobre [o Gestor de Recursos Azure.](https://channel9.msdn.com/Events/Ignite/2015/C9-39)
* Consulte a ajuda de referência do [modelo do Gestor](https://docs.microsoft.com/azure/templates/microsoft.machinelearning/allversions) de Recursos

<!--Link references-->
