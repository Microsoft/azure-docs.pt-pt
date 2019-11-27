---
title: PowerShell para alias DNS
description: Os cmdlets do PowerShell, como New-AzSqlServerDNSAlias, permitem que você redirecione novas conexões de cliente para um servidor de banco de dados SQL do Azure diferente, sem precisar tocar em nenhuma configuração de cliente.
keywords: banco de dados SQL DNS
ms.custom: seo-lt-2019
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.devlang: PowerShell
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: genemi, amagarwa, maboja, jrasnick, vanto
ms.date: 05/14/2019
ms.openlocfilehash: 9232a99ddd29201e6743c09455d79e9ba22b3b9c
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2019
ms.locfileid: "74420400"
---
# <a name="powershell-for-dns-alias-to-azure-sql-database"></a>PowerShell para alias DNS para o banco de dados SQL do Azure

Este artigo fornece um script do PowerShell que demonstra como você pode gerenciar um alias DNS para o banco de dados SQL do Azure.

> [!NOTE]
> Este artigo foi atualizado para usar o módulo Azure PowerShell AZ ou CLI do Azure. Pode continuar a utilizar o módulo AzureRM, que continuará a receber correções de erros até, pelo menos, dezembro de 2020.
>
> Para saber mais sobre o módulo AZ e a compatibilidade do AzureRM, consulte [apresentando o módulo Azure PowerShell AZ](/powershell/azure/new-azureps-module-az). Para obter instruções de instalação, consulte [instalar Azure PowerShell](/powershell/azure/install-az-ps) ou [instalar CLI do Azure](/cli/azure/install-azure-cli).

## <a name="dns-alias-in-connection-string"></a>Alias de DNS na cadeia de conexão

Para conectar um determinado servidor de banco de dados SQL do Azure, um cliente como o SQL Server Management Studio (SSMS) pode fornecer o nome do alias DNS em vez do nome do servidor verdadeiro. No exemplo de cadeia de caracteres de servidor a seguir, o alias *any-Unique-alias-name* substitui o primeiro nó delimitado por ponto na cadeia de caracteres do servidor de quatro nós:

   `<yourServer>.database.windows.net`

## <a name="prerequisites"></a>Pré-requisitos

Se você quiser executar o script de demonstração do PowerShell fornecido neste artigo, os seguintes pré-requisitos se aplicarão:

- Uma assinatura e conta do Azure, para avaliação gratuita, consulte [avaliações do Azure](https://azure.microsoft.com/free/)
- Dois servidores de banco de dados SQL do Azure

## <a name="example"></a>Exemplo

O exemplo de código a seguir começa atribuindo valores literais a várias variáveis.

Para executar o código, edite os valores de espaço reservado para corresponder aos valores reais em seu sistema.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Os cmdlets usados são os seguintes:

- [New-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/New-azSqlServerDnsAlias): cria um alias DNS no sistema de serviço do banco de dados SQL do Azure. O alias refere-se ao servidor de banco de dados 1.
- [Get-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlServerDnsAlias): obter e listar todos os aliases atribuídos ao servidor de banco de BD SQL 1.
- [Set-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Set-azSqlServerDnsAlias): modifica o nome do servidor ao qual o alias está configurado para se referir, do servidor 1 ao servidor 2.
- [Remove-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Remove-azSqlServerDnsAlias): Remove o alias do servidor de banco de dados 2, usando o nome do alias.

Para instalar ou atualizar, veja [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps).

Use `Get-Module -ListAvailable Az` no *powershell\_ISE. exe*para localizar a versão.

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

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

Os comandos usados são os seguintes:

- [AZ SQL Server DNS-Create alias](https://docs.microsoft.com/powershell/module/az.Sql/New-azSqlServerDnsAlias): cria um alias DNS no sistema de serviço do banco de dados SQL do Azure. O alias refere-se ao servidor de banco de dados 1.
- [AZ SQL Server DNS-alias show](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlServerDnsAlias): obter e listar todos os aliases atribuídos ao servidor de banco de BD SQL 1.
- [AZ SQL Server DNS-alias Set](https://docs.microsoft.com/powershell/module/az.Sql/Set-azSqlServerDnsAlias): modifica o nome do servidor ao qual o alias está configurado para se referir, do servidor 1 para o servidor 2.
- [AZ SQL Server DNS-alias Delete](https://docs.microsoft.com/powershell/module/az.Sql/Remove-azSqlServerDnsAlias): Remova o alias do servidor de banco de dados 2, usando o nome do alias.

Para instalar ou atualizar, consulte [instalar CLI do Azure](/cli/azure/install-azure-cli).

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

Para obter uma explicação completa do recurso de alias de DNS para o banco de dados SQL, consulte [alias DNS para o banco de dados SQL do Azure](dns-alias-overview.md).
