---
title: 'Estúdio ML (clássico): Implementar espaços de trabalho com Azure Resource Manager - Azure'
description: Como implementar um espaço de trabalho para o Azure Machine Learning Studio (clássico) usando o modelo de Gestor de Recursos Azure
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18, devx-track-azurepowershell
ms.date: 02/05/2018
ms.openlocfilehash: ee0bee4857d410781561e6f7f6a7eae0509d1138
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "100520039"
---
# <a name="deploy-azure-machine-learning-studio-classic-workspace-using-azure-resource-manager"></a>Implementar O Azure Machine Learning Studio (clássico) Workspace Usando O Gestor de Recursos Azure

**APLICA-SE A:** ![ Aplica-se a. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (clássico) ![ Não se aplica a.](../../../includes/media/aml-applies-to-skus/no.png)[ Aprendizagem de Máquinas Azure](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  

A utilização de um modelo de implementação do Gestor de Recursos Azure poupa-lhe tempo, dando-lhe uma forma escalável de implantar componentes interligados com um mecanismo de validação e retagem. Para configurar o Azure Machine Learning Studio (clássico) Workspaces, por exemplo, é necessário configurar primeiro uma conta de armazenamento Azure e depois implantar o seu espaço de trabalho. Imagine fazer isto manualmente para centenas de espaços de trabalho. Uma alternativa mais fácil é usar um modelo de Gestor de Recursos Azure para implementar um Espaço de Trabalho (clássico) do Estúdio e todas as suas dependências. Este artigo leva-o a este processo passo a passo. Para obter uma excelente visão geral do Azure Resource Manager, consulte [a visão geral do Azure Resource Manager](../../azure-resource-manager/management/overview.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="step-by-step-create-a-machine-learning-workspace"></a>Passo a passo: criar um espaço de trabalho de aprendizagem automática
Criaremos um grupo de recursos Azure, depois implantaremos uma nova conta de armazenamento Azure e um novo Azure Machine Learning Studio (clássico) Workspace usando um modelo de Gestor de Recursos. Assim que a implementação estiver concluída, iremos imprimir informações importantes sobre os espaços de trabalho que foram criados (a chave primária, o espaço de trabalhoID e o URL para o espaço de trabalho).

### <a name="create-an-azure-resource-manager-template"></a>Crie um modelo de gestor de recursos Azure

Um espaço de trabalho de machine learning requer uma conta de armazenamento Azure para armazenar o conjunto de dados ligado a ele.
O modelo a seguir utiliza o nome do grupo de recursos para gerar o nome da conta de armazenamento e o nome do espaço de trabalho.  Também usa o nome da conta de armazenamento como uma propriedade ao criar o espaço de trabalho.

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
Guarde este modelo como mlworkspace.jsno ficheiro em c:\temp\.

### <a name="deploy-the-resource-group-based-on-the-template"></a>Implemente o grupo de recursos, com base no modelo

* Abrir o PowerShell
* Instalar módulos para O Gestor de Recursos Azure e Gestão de Serviços Azure

```powershell
# Install the Azure Resource Manager modules from the PowerShell Gallery (press "A")
Install-Module Az -Scope CurrentUser

# Install the Azure Service Management modules from the PowerShell Gallery (press "A")
Install-Module Azure -Scope CurrentUser
```

   Estes passos descarregam e instalam os módulos necessários para completar os passos restantes. Isto só precisa de ser feito uma vez no ambiente onde está a executar os comandos PowerShell.

* Autenticar para Azure

```powershell
# Authenticate (enter your credentials in the pop-up window)
Connect-AzAccount
```
Este passo tem de ser repetido para cada sessão. Uma vez autenticado, as informações da sua subscrição devem ser apresentadas.

![Conta do Azure](./media/deploy-with-resource-manager-template/azuresubscription.png)

Agora que temos acesso ao Azure, podemos criar o grupo de recursos.

* Criar um grupo de recursos

```powershell
$rg = New-AzResourceGroup -Name "uniquenamerequired523" -Location "South Central US"
$rg
```

Verifique se o grupo de recursos está corretamente aprovisionado. **O Estado de Provisioning** deve ser "Bem sucedido".
O nome do grupo de recursos é usado pelo modelo para gerar o nome da conta de armazenamento. O nome da conta de armazenamento deve ter entre 3 e 24 caracteres de comprimento e utilizar apenas números e letras minúsculas.

![Grupo de Recursos](./media/deploy-with-resource-manager-template/resourcegroupprovisioning.png)

* Utilizando a implementação do grupo de recursos, implemente um novo espaço de trabalho de aprendizagem automática.

```powershell
# Create a Resource Group, TemplateFile is the location of the JSON template.
$rgd = New-AzResourceGroupDeployment -Name "demo" -TemplateFile "C:\temp\mlworkspace.json" -ResourceGroupName $rg.ResourceGroupName
```

Uma vez concluída a implementação, é simples aceder às propriedades do espaço de trabalho que implementou. Por exemplo, pode aceder ao Token da Chave Primária.

```powershell
# Access Azure Machine Learning Studio Workspace Token after its deployment.
$rgd.Outputs.mlWorkspaceToken.Value
```

Outra forma de recuperar fichas do espaço de trabalho existente é usar o comando Invoke-AzResourceAction. Por exemplo, pode listar os símbolos primários e secundários de todos os espaços de trabalho.

```powershell
# List the primary and secondary tokens of all workspaces
Get-AzResource |? { $_.ResourceType -Like "*MachineLearning/workspaces*"} |ForEach-Object { Invoke-AzResourceAction -ResourceId $_.ResourceId -Action listworkspacekeys -Force}
```
Depois de o espaço de trabalho ser a provisionado, também pode automatizar muitas tarefas do Azure Machine Learning Studio (clássico) utilizando o [Módulo PowerShell para o Azure Machine Learning Studio (clássico)](https://aka.ms/amlps).

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [a autoria de Modelos de Gestor de Recursos Azure.](../../azure-resource-manager/templates/template-syntax.md)
* Veja o [Repositório de Modelos Azure Quickstart](https://github.com/Azure/azure-quickstart-templates).
* Veja este vídeo sobre [o Azure Resource Manager](https://channel9.msdn.com/Events/Ignite/2015/C9-39).
* Consulte a ajuda de referência do [modelo do Gestor de Recursos](/azure/templates/microsoft.machinelearning/allversions)

<!--Link references-->