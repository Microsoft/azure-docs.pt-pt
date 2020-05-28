---
title: 'Gestor de Recursos Azure: Criar uma única base de dados'
description: Crie uma única base de dados na Base de Dados Azure SQL utilizando o modelo do Gestor de Recursos Azure.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: subject-armqs sqldbrb=1
ms.devlang: ''
ms.topic: quickstart
author: mumian
ms.author: jgao
ms.reviewer: carlrab
ms.date: 06/28/2019
ms.openlocfilehash: 03d598e0c0756cd304248ab19ab28a25836c57ab
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84054336"
---
# <a name="quickstart-create-a-single-database-in-azure-sql-database-using-the-azure-resource-manager-template"></a>Quickstart: Criar uma única base de dados na Base de Dados Azure SQL utilizando o modelo do Gestor de Recursos Azure
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

A criação de uma [única base de dados](single-database-overview.md) é a opção mais rápida e simples para criar uma base de dados na Base de Dados Azure SQL. Este quickstart mostra-lhe como criar uma única base de dados usando o modelo do Gestor de Recursos Azure.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Se não tiver uma subscrição Azure, [crie uma conta gratuita.](https://azure.microsoft.com/free/)

## <a name="prerequisites"></a>Pré-requisitos

Nenhum

## <a name="create-a-single-database"></a>Criar uma única base de dados

Uma única base de dados tem um conjunto definido de dados, memória, IO e recursos de armazenamento utilizando um dos dois [modelos de compra.](purchasing-models.md) Quando cria uma única base de dados, também define um [servidor](logical-servers.md) para geri-lo e colocá-lo dentro do [grupo de recursos Azure](../../active-directory-b2c/overview.md) numa região especificada.

### <a name="review-the-template"></a>Reveja o modelo

O modelo utilizado neste quickstart é de [modelos Azure Quickstart](https://azure.microsoft.com/resources/templates/101-sql-logical-server/).

:::code language="json" source="~/quickstart-templates/101-sql-logical-server/azuredeploy.json" range="1-163" highlight="63-132":::

Estes recursos são definidos no modelo:

- [**Microsoft.Sql/servidores**](/azure/templates/microsoft.sql/servers)
- [**Microsoft.Sql/servidores/firewallRules**](/azure/templates/microsoft.sql/servers/firewallrules)
- [**Microsoft.Sql/servidores/securityAlertPolicies**](/azure/templates/microsoft.sql/servers/securityalertpolicies)
- [**Microsoft.Sql/servidores/vulnerabilidadeS**](/azure/templates/microsoft.sql/servers/vulnerabilityassessments)
- [**Microsoft.Sql/servidores/políticas de ligação**](/azure/templates/microsoft.sql/servers/connectionpolicies)
- [**Microsoft.Storage/storageAccounts**](/azure/templates/microsoft.storage/storageaccounts)
- [**Microsoft.Armazenamento/armazenamentoContas/fornecedores/tarefas**](/azure/templates/microsoft.authorization/roleassignments)

Mais amostras de modelo de base de dados Azure SQL podem ser encontradas em [modelos de quickstart azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sql&pageNumber=1&sort=Popular).

### <a name="deploy-the-template"></a>Implementar o modelo

Selecione **Experimente** a partir do seguinte bloco de código PowerShell para abrir a Azure Cloud Shell.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter an Azure location (i.e. centralus)"
$adminUser = Read-Host -Prompt "Enter the SQL server administrator username"
$adminPassword = Read-Host -Prompt "Enter the SQl server administrator password" -AsSecureString

$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-sql-logical-server/azuredeploy.json" -administratorLogin $adminUser -administratorLoginPassword $adminPassword

Read-Host -Prompt "Press [ENTER] to continue ..."
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter an Azure location (i.e. centralus)"
$adminUser = Read-Host -Prompt "Enter the SQL server administrator username"
$adminPassword = Read-Host -Prompt "Enter the SQl server administrator password" -AsSecureString

$resourceGroupName = "${projectName}rg"

az group create --location $location --name $resourceGroupName

az group deployment create -g $resourceGroupName --template-uri "D:\GitHub\azure-docs-json-samples\SQLServerAndDatabase\azuredeploy.json" `
    --parameters 'projectName=' + $projectName \
                 'administratorLogin=' + $adminUser \
                 'administratorLoginPassword=' + $adminPassword

Read-Host -Prompt "Press [ENTER] to continue ..."
```

* * *

## <a name="validate-the-deployment"></a>Validar a implementação

Para consultar a base de dados, consulte [a base de dados](single-database-create-quickstart.md#query-the-database).

## <a name="clean-up-resources"></a>Limpar recursos

Mantenha este grupo de recursos, servidor e base de dados única se quiser ir aos [próximos passos](#next-steps). Os próximos passos mostram-lhe como ligar e consultar a sua base de dados utilizando métodos diferentes.

Para eliminar o grupo de recursos:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

* * *

## <a name="next-steps"></a>Próximos passos

- Crie uma regra de firewall ao nível do servidor para se ligar à base de dados única a partir de instalações ou ferramentas remotas. Para mais informações, consulte [Criar uma regra de firewall ao nível do servidor](firewall-create-server-level-portal-quickstart.md).
- Depois de criar uma regra de firewall ao nível do servidor, [conecte e consulta](connect-query-content-reference-guide.md) a sua base de dados utilizando várias ferramentas e idiomas diferentes.
  - [Ligar e consultar com o SQL Server Management Studio](connect-query-ssms.md)
  - [Ligar e consultar com o Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- Para criar uma única base de dados utilizando o Azure CLI, consulte [as amostras do Azure CLI](az-cli-script-samples-content-guide.md).
- Para criar uma única base de dados utilizando o Azure PowerShell, consulte [as amostras do Azure PowerShell](powershell-script-content-guide.md).
- Para aprender a criar modelos de Gestor de Recursos, consulte [Criar o seu primeiro modelo](../../azure-resource-manager/templates/template-tutorial-create-first-template.md).
