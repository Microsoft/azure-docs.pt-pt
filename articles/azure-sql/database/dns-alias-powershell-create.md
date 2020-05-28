---
title: Pseudónimos DNS (PowerShell & Azure CLI)
description: Os cmdlets PowerShell e Azure CLI permitem redirecionar novas ligações de clientes para um servidor SQL diferente em Azure, sem ter de tocar em nenhuma configuração do cliente.
keywords: dns sql base de dados
ms.custom: seo-lt-2019 sqldbrb=1
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.devlang: PowerShell
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: genemi, amagarwa, maboja, jrasnick, vanto
ms.date: 05/14/2019
ms.openlocfilehash: 3acbdd5c933bc2010a26e2039ece378774055d30
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84050402"
---
# <a name="powershell-for-dns-alias-to-azure-sql-database"></a>PowerShell para DNS Alias para Base de Dados SQL Azure
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Este artigo fornece um script PowerShell que demonstra como pode gerir um pseudónimo DNS para o [servidor SQL](logical-servers.md) que acolhe a sua Base de Dados Azure SQL.

> [!NOTE]
> Este artigo foi atualizado para utilizar o módulo Azure PowerShell Az ou o Azure CLI. Pode continuar a utilizar o módulo AzureRM, que continuará a receber correções de erros até, pelo menos, dezembro de 2020.
>
> Para saber mais sobre o módulo Az e compatibilidade AzureRM, consulte [a introdução do módulo Azure PowerShell Az](/powershell/azure/new-azureps-module-az). Para instruções de instalação, consulte [Instalar a PowerShell Azure](/powershell/azure/install-az-ps) ou [instalar o Azure CLI](/cli/azure/install-azure-cli).

## <a name="dns-alias-in-connection-string"></a>Pseudónimo dNS na cadeia de ligação

Para ligar um [servidor lógico sQL](logical-servers.md), um cliente como o SQL Server Management Studio (SSMS) pode fornecer o nome de pseudónimo DNS em vez do verdadeiro nome do servidor. Na seguinte cadeia de servidores exemplo, o pseudónimo *de qualquer nome único-pseudónimo* substitui o primeiro nó delimitado ponto na cadeia do servidor de quatro nós:

   `<yourServer>.database.windows.net`

## <a name="prerequisites"></a>Pré-requisitos

Se pretender executar o script de demonstração PowerShell dado neste artigo, aplicam-se os seguintes pré-requisitos:

- Uma subscrição e conta Azure, para teste gratuito, ver [testes de Azure](https://azure.microsoft.com/free/)
- Dois servidores

## <a name="example"></a>Exemplo

O seguinte exemplo de código começa por atribuir valores literais a várias variáveis.

Para executar o código, edite os valores do espaço reservado para combinar com valores reais no seu sistema.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

As cmdlets utilizadas são as seguintes:

- [New-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/New-azSqlServerDnsAlias): Cria um pseudónimo DNS no sistema de serviço de base de dados Azure SQL. O pseudónimo refere-se ao servidor 1.
- [Get-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlServerDnsAlias): Obtenha e enumere todos os pseudónimos atribuídos ao servidor 1.
- [Set-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Set-azSqlServerDnsAlias): Modifica o nome do servidor a que o pseudónimo está configurado para se referir, do servidor 1 ao servidor 2.
- [Remover-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Remove-azSqlServerDnsAlias): Remova o pseudónimo do servidor 2, utilizando o nome do pseudónimo.

Para instalar ou atualizar, veja [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps).

Use `Get-Module -ListAvailable Az` em *powershell \_ ise.exe,* para encontrar a versão.

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

- [az sql server dns-alias criar](https://docs.microsoft.com/powershell/module/az.Sql/New-azSqlServerDnsAlias): Cria um pseudónimo DNS para um servidor. O pseudónimo refere-se ao servidor 1.
- [az sql server dns-alias show](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlServerDnsAlias): Obtenha e enumere todos os pseudónimos atribuídos ao servidor 1.
- [az sql server dns-alias definido](https://docs.microsoft.com/powershell/module/az.Sql/Set-azSqlServerDnsAlias): Modifica o nome do servidor a que o pseudónimo está configurado para se referir, do servidor 1 ao servidor 2.
- [az sql server dns-alias delete](https://docs.microsoft.com/powershell/module/az.Sql/Remove-azSqlServerDnsAlias): Remova o pseudónimo do servidor 2, utilizando o nome do pseudónimo.

Para instalar ou atualizar, consulte [Instalar o Azure CLI](/cli/azure/install-azure-cli).

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

## <a name="next-steps"></a>Próximos passos

Para obter uma explicação completa da funcionalidade de pseudónimo DNS para base de dados SQL, consulte o pseudónimo DNS para a Base de [Dados Azure SQL](../../sql-database/dns-alias-overview.md).
