---
title: PSEUDÓNIMO DNS (PowerShell & Azure CLI)
description: Os cmdlets PowerShell e Azure CLI permitem redirecionar novas ligações de clientes para um servidor SQL diferente em Azure, sem ter de tocar em nenhuma configuração do cliente.
keywords: base de dados dns sql
ms.custom: seo-lt-2019 sqldbrb=1, devx-track-azurecli
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.devlang: PowerShell
ms.topic: how-to
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: genemi, amagarwa, maboja, jrasnick, vanto
ms.date: 05/14/2019
ms.openlocfilehash: 02cfd839ed1b75fd85553f2e5a5150cadc29ff8e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92790462"
---
# <a name="powershell-for-dns-alias-to-azure-sql-database"></a>PowerShell para DNS Alias para Azure SQL Database
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Este artigo fornece um script PowerShell que demonstra como pode gerir um pseudónimo DNS para o [servidor SQL](logical-servers.md) que hospeda a sua Base de Dados Azure SQL.

> [!NOTE]
> Este artigo foi atualizado para utilizar o módulo Azure PowerShell Az ou O Azure CLI. Pode continuar a utilizar o módulo AzureRM, que continuará a receber correções de erros até, pelo menos, dezembro de 2020.
>
> Para saber mais sobre o módulo Az e a compatibilidade AzureRM, consulte [a introdução do módulo Azure PowerShell Az](/powershell/azure/new-azureps-module-az). Para obter instruções de instalação, consulte [instalar a Azure PowerShell](/powershell/azure/install-az-ps) ou [instalar o Azure CLI](/cli/azure/install-azure-cli).

## <a name="dns-alias-in-connection-string"></a>Pseudónimo DNS na cadeia de ligação

Para ligar um [servidor SQL lógico,](logical-servers.md)um cliente como o SQL Server Management Studio (SSMS) pode fornecer o nome de pseudónimo DNS em vez do verdadeiro nome do servidor. Na seguinte cadeia de servidor de exemplo, o *pseudónimo de qualquer nome de pseudónimo único* substitui o primeiro nó delimitado de pontos na cadeia do servidor de quatro nó:

   `<yourServer>.database.windows.net`

## <a name="prerequisites"></a>Pré-requisitos

Se pretender executar o guião PowerShell de demonstração dado neste artigo, aplicam-se os seguintes pré-requisitos:

- Uma subscrição da Azure e conta, para teste gratuito, ver [julgamentos do Azure](https://azure.microsoft.com/free/)
- Dois servidores

## <a name="example"></a>Exemplo

O exemplo de código a seguir começa por atribuir valores literais a várias variáveis.

Para executar o código, edite os valores do espaço reservado para corresponder aos valores reais do seu sistema.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Os cmdlets utilizados são os seguintes:

- [New-AzSqlServerDNSAlias](/powershell/module/az.Sql/New-azSqlServerDnsAlias): Cria um pseudónimo DNS no sistema de serviço de base de dados Azure SQL. O pseudónimo refere-se ao servidor 1.
- [Get-AzSqlServerDNSAlias](/powershell/module/az.Sql/Get-azSqlServerDnsAlias): Obter e listar todos os pseudónimos atribuídos ao servidor 1.
- [Set-AzSqlServerDNSAlias](/powershell/module/az.Sql/Set-azSqlServerDnsAlias): Modifica o nome do servidor a que o pseudónimo está configurado para se referir, do servidor 1 ao servidor 2.
- [Remove-AzSqlServerDNSAlias](/powershell/module/az.Sql/Remove-azSqlServerDnsAlias): Remova o pseudónimo do servidor 2, utilizando o nome do pseudónimo.

Para instalar ou atualizar, veja [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps).

Use `Get-Module -ListAvailable Az` emise.exede *powershell \_*, para encontrar a versão.

```powershell
$subscriptionName = '<subscriptionName>';
$sqlServerDnsAliasName = '<aliasName>';
$resourceGroupName = '<resourceGroupName>';  
$sqlServerName = '<sqlServerName>';
$resourceGroupName2 = '<resourceGroupNameTwo>'; # can be same or different than $resourceGroupName
$sqlServerName2 = '<sqlServerNameTwo>'; # must be different from $sqlServerName.

# login to Azure
Connect-AzAccount -SubscriptionName $subscriptionName;
$subscriptionId = Get-AzSubscription -SubscriptionName $subscriptionName;

Write-Host 'Assign an alias to server 1...';
New-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName -ServerName $sqlServerName `
    -Name $sqlServerDnsAliasName;

Write-Host 'Get the aliases assigned to server 1...';
Get-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName -ServerName $sqlServerName;

Write-Host 'Move the alias from server 1 to server 2...';
Set-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName2 -TargetServerName $sqlServerName2 `
    -Name $sqlServerDnsAliasName `
    -SourceServerResourceGroup $resourceGroupName -SourceServerName $sqlServerName `
    -SourceServerSubscriptionId $subscriptionId.Id;

Write-Host 'Get the aliases assigned to server 2...';
Get-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName2 -ServerName $sqlServerName2;

Write-Host 'Remove the alias from server 2...';
Remove-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName2 -ServerName $sqlServerName2 `
    -Name $sqlServerDnsAliasName;
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Os comandos utilizados são os seguintes:

- [az sql servidor dns-alias create](/powershell/module/az.Sql/New-azSqlServerDnsAlias): Cria um pseudónimo DNS para um servidor. O pseudónimo refere-se ao servidor 1.
- [az sql servidor dns-alias show](/powershell/module/az.Sql/Get-azSqlServerDnsAlias): Obter e listar todos os pseudónimos atribuídos ao servidor 1.
- [az sql servidor dns-alias set](/powershell/module/az.Sql/Set-azSqlServerDnsAlias): Modifica o nome do servidor a que o pseudónimo está configurado para se referir, do servidor 1 ao servidor 2.
- [az sql servidor dns-alias delete](/powershell/module/az.Sql/Remove-azSqlServerDnsAlias): Remova o pseudónimo do servidor 2, utilizando o nome do pseudónimo.

Para instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

```azurecli-interactive
$subscriptionName = '<subscriptionName>';
$sqlServerDnsAliasName = '<aliasName>';
$resourceGroupName = '<resourceGroupName>';  
$sqlServerName = '<sqlServerName>';
$resourceGroupName2 = '<resourceGroupNameTwo>'; # can be same or different than $resourceGroupName
$sqlServerName2 = '<sqlServerNameTwo>'; # must be different from $sqlServerName.

# login to Azure
az login -SubscriptionName $subscriptionName;
$subscriptionId = az account list[0].i -SubscriptionName $subscriptionName;

Write-Host 'Assign an alias to server 1...';
az sql server dns-alias create –-resource-group $resourceGroupName --server $sqlServerName `
    --name $sqlServerDnsAliasName;

Write-Host 'Get the aliases assigned to server 1...';
az sql server dns-alias show –-resource-group $resourceGroupName --server $sqlServerName;

Write-Host 'Move the alias from server 1 to server 2...';
az sql server dns-alias set –-resource-group $resourceGroupName2 --server $sqlServerName2 `
    --name $sqlServerDnsAliasName `
    --original-resource-group $resourceGroupName --original-server $sqlServerName `
    --original-subscription-id $subscriptionId.Id;

Write-Host 'Get the aliases assigned to server 2...';
az sql server dns-alias show –-resource-group $resourceGroupName2 --server $sqlServerName2;

Write-Host 'Remove the alias from server 2...';
az sql server dns-alias delete –-resource-group $resourceGroupName2 --server $sqlServerName2 `
    --name $sqlServerDnsAliasName;
```

* * *

## <a name="next-steps"></a>Passos seguintes

Para obter uma explicação completa da funcionalidade de pseudónimo DNS para a Base de Dados SQL, consulte [o pseudónimo DNS para Azure SQL Database](./dns-alias-overview.md).