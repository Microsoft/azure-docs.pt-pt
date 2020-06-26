---
title: 'Gestor de Recursos Azure: Criar uma única base de dados'
description: Crie uma única base de dados na Base de Dados Azure SQL utilizando o modelo Azure Resource Manager.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: subject-armqs sqldbrb=1
ms.devlang: ''
ms.topic: quickstart
author: mumian
ms.author: jgao
ms.reviewer: carlrab
ms.date: 06/24/2020
ms.openlocfilehash: 2975b98306b7019022799d5ba69b9d7af5797a2b
ms.sourcegitcommit: bf8c447dada2b4c8af017ba7ca8bfd80f943d508
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/25/2020
ms.locfileid: "85368044"
---
# <a name="quickstart-create-a-single-database-in-azure-sql-database-using-the-azure-resource-manager-template"></a>Quickstart: Criar uma única base de dados na Base de Dados Azure SQL utilizando o modelo do Gestor de Recursos Azure

A criação de uma [única base de dados](single-database-overview.md) é a opção mais rápida e simples para criar uma base de dados na Base de Dados Azure SQL. Este quickstart mostra-lhe como criar uma única base de dados utilizando o modelo Azure Resource Manager.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Se o seu ambiente satisfaça os pré-requisitos e estiver familiarizado com a utilização de modelos ARM, selecione o botão Implementar para Azul. O modelo será aberto no portal Azure.

[![Implementar no Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sql-database%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/).

## <a name="review-the-template"></a>Rever o modelo

Uma única base de dados tem um conjunto definido de computação, memória, IO e recursos de armazenamento utilizando um de dois [modelos de compra.](purchasing-models.md) Quando cria uma única base de dados, também define um [servidor](logical-servers.md) para geri-lo e colocá-lo dentro do [grupo de recursos Azure](../../active-directory-b2c/overview.md) numa região especificada.

O modelo utilizado neste início rápido pertence aos [modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-sql-logical-server/).

:::code language="json" source="~/quickstart-templates/101-sql-database/azuredeploy.json" range="1-67" highlight="41-65":::

Estes recursos são definidos no modelo:

- [**Microsoft.Sql/servidores**](/azure/templates/microsoft.sql/servers)
- [**Microsoft.Sql/servidores/bases de dados**](/azure/templates/microsoft.sql/servers/databases)

Mais amostras de modelo de base de dados Azure SQL podem ser encontradas em [modelos de arranque rápido Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sql&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Implementar o modelo

Selecione **Experimente-o** a partir do seguinte bloco de código PowerShell para abrir a Azure Cloud Shell.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter an Azure location (i.e. centralus)"
$adminUser = Read-Host -Prompt "Enter the SQL server administrator username"
$adminPassword = Read-Host -Prompt "Enter the SQl server administrator password" -AsSecureString

$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-sql-database/azuredeploy.json" -administratorLogin $adminUser -administratorLoginPassword $adminPassword

Read-Host -Prompt "Press [ENTER] to continue ..."
```

## <a name="validate-the-deployment"></a>Validar a implementação

Para consultar a base de dados, consulte [consultar a base de dados.](single-database-create-quickstart.md#query-the-database)

## <a name="clean-up-resources"></a>Limpar recursos

Mantenha este grupo de recursos, servidor e base de dados única se quiser ir aos [próximos passos](#next-steps). Os próximos passos mostram-lhe como conectar e consultar a sua base de dados utilizando diferentes métodos.

Para eliminar o grupo de recursos:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>Passos seguintes

- Crie uma regra de firewall ao nível do servidor para ligar à base de dados única a partir de instalações ou ferramentas remotas. Para obter mais informações, consulte [Criar uma regra de firewall ao nível do servidor](firewall-create-server-level-portal-quickstart.md).
- Depois de criar uma regra de firewall ao nível do servidor, [conecte e questione a](connect-query-content-reference-guide.md) sua base de dados utilizando várias ferramentas e idiomas diferentes.
  - [Ligar e consultar com o SQL Server Management Studio](connect-query-ssms.md)
  - [Ligar e consultar com o Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- Para criar uma única base de dados utilizando o Azure CLI, consulte [as amostras do Azure CLI](az-cli-script-samples-content-guide.md).
- Para criar uma única base de dados utilizando a Azure PowerShell, consulte [as amostras do Azure PowerShell](powershell-script-content-guide.md).
- Para aprender a criar modelos de Gestor de Recursos, consulte [Criar o seu primeiro modelo](../../azure-resource-manager/templates/template-tutorial-create-first-template.md).
