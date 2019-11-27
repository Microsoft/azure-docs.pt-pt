---
title: 'Azure Resource Manager: criar um banco de dados individual'
description: Crie um banco de dados individual no banco de dados SQL do Azure usando o modelo Azure Resource Manager.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: mumian
ms.author: jgao
ms.reviewer: carlrab
ms.date: 06/28/2019
ms.openlocfilehash: 5d090add7bdb2c3ee08f4c186bd57d63f14ab113
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422569"
---
# <a name="quickstart-create-a-single-database-in-azure-sql-database-using-the-azure-resource-manager-template"></a>Início rápido: criar um banco de dados individual no banco de dados SQL do Azure usando o modelo de Azure Resource Manager

Criar um [banco de dados individual](sql-database-single-database.md) é a opção de implantação mais rápida e mais simples para criar um banco de dados no banco de dados SQL do Azure. Este guia de início rápido mostra como criar um banco de dados individual usando o modelo de Azure Resource Manager. Para obter mais informações, consulte a [documentação do Azure Resource Manager](/azure/azure-resource-manager/).

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/).

## <a name="create-a-single-database"></a>Criar um banco de dados individual

Um banco de dados individual tem um conjunto definido de recursos de computação, memória, e/s e armazenamento usando um dos dois [modelos de compra](sql-database-purchase-models.md). Ao criar um banco de dados individual, você também define um [servidor de banco de dados SQL](sql-database-servers.md) para gerenciá-lo e colocá-lo no [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) em uma região especificada.

O arquivo JSON a seguir é o modelo usado neste artigo. O modelo é armazenado no [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/SQLServerAndDatabase/azuredeploy.json). Mais exemplos de modelo de banco de dados SQL do Azure podem ser encontrados em [modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sql&pageNumber=1&sort=Popular).

[!code-json[create-azure-sql-database-server-and-database](~/resourcemanager-templates/SQLServerAndDatabase/azuredeploy.json)]

Selecione **Experimente** no seguinte bloco de código do PowerShell para abrir Azure cloud Shell.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter an Azure location (i.e. centralus)"
$adminUser = Read-Host -Prompt "Enter the SQL server administrator username"
$adminPassword = Read-Host -Prompt "Enter the SQl server administrator password" -AsSecureString

$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile "D:\GitHub\azure-docs-json-samples\SQLServerAndDatabase\azuredeploy.json" -projectName $projectName -adminUser $adminUser -adminPassword $adminPassword

Read-Host -Prompt "Press [ENTER] to continue ..."
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter an Azure location (i.e. centralus)"
$adminUser = Read-Host -Prompt "Enter the SQL server administrator username"
$adminPassword = Read-Host -Prompt "Enter the SQl server administrator password" -AsSecureString

$resourceGroupName = "${projectName}rg"

az group create --location $location --name $resourceGroupName

az group deployment create -g $resourceGroupName --template-uri "D:\GitHub\azure-docs-json-samples\SQLServerAndDatabase\azuredeploy.json" `
    --parameters 'projectName=' + $projectName \
                 'adminUser=' + $adminUser \
                 'adminPassword=' + $adminPassword

Read-Host -Prompt "Press [ENTER] to continue ..."
```

* * *

## <a name="query-the-database"></a>Consultar o banco de dados

Para consultar o banco de dados, consulte [consultar o banco de dados](./sql-database-single-database-get-started.md#query-the-database).

## <a name="clean-up-resources"></a>Limpar recursos

Mantenha este grupo de recursos, servidor de banco de dados e banco de dados individual se desejar ir para as [próximas etapas](#next-steps). As próximas etapas mostram como se conectar e consultar seu banco de dados usando métodos diferentes.

Para excluir o grupo de recursos:

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

* * *

## <a name="next-steps"></a>Passos seguintes

- Crie uma regra de firewall no nível de servidor para se conectar ao banco de dados individual de ferramentas locais ou remotas. Para obter mais informações, consulte [criar uma regra de firewall no nível de servidor](sql-database-server-level-firewall-rule.md).
- Depois de criar uma regra de firewall no nível de servidor, [conecte e consulte](sql-database-connect-query.md) seu banco de dados usando várias ferramentas e idiomas diferentes.
  - [Ligar e consultar com o SQL Server Management Studio](sql-database-connect-query-ssms.md)
  - [Ligar e consultar com o Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- Para criar um banco de dados individual usando CLI do Azure, consulte [CLI do Azure exemplos](sql-database-cli-samples.md).
- Para criar um banco de dados individual usando Azure PowerShell, consulte [Azure PowerShell exemplos](sql-database-powershell-samples.md).
