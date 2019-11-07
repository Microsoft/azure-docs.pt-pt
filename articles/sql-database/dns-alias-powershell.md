---
title: PowerShell para alias DNS Azure SQL
description: Os cmdlets do PowerShell, como New-AzSqlServerDNSAlias, permitem que você redirecione novas conexões de cliente para um servidor de banco de dados SQL do Azure diferente, sem precisar tocar em nenhuma configuração de cliente.
keywords: banco de dados SQL DNS
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.devlang: PowerShell
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: genemi, amagarwa, maboja, jrasnick, vanto
ms.date: 05/14/2019
ms.openlocfilehash: ebbca309b7f6acd071c7075e63e670a8efa49f4e
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685316"
---
# <a name="powershell-for-dns-alias-to-azure-sql-database"></a>PowerShell para alias DNS para o banco de dados SQL do Azure

Este artigo fornece um script do PowerShell que demonstra como você pode gerenciar um alias DNS para o banco de dados SQL do Azure. O script executa os seguintes cmdlets que executam as seguintes ações:

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Os cmdlets usados no exemplo de código são os seguintes:

- [New-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/New-azSqlServerDnsAlias): cria um novo alias DNS no sistema de serviço do banco de dados SQL do Azure. O alias refere-se ao servidor de banco de dados SQL do Azure 1.
- [Get-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlServerDnsAlias): obter e listar todos os aliases de DNS atribuídos ao servidor de banco de BD SQL 1.
- [Set-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Set-azSqlServerDnsAlias): modifica o nome do servidor ao qual o alias está configurado para se referir, do servidor 1 ao servidor de banco de BD SQL 2.
- [Remove-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Remove-azSqlServerDnsAlias): Remove o alias de DNS do servidor de banco de BD SQL 2, usando o nome do alias.

## <a name="dns-alias-in-connection-string"></a>Alias de DNS na cadeia de conexão

Para conectar um determinado servidor de banco de dados SQL do Azure, um cliente como o SQL Server Management Studio (SSMS) pode fornecer o nome do alias DNS em vez do nome do servidor verdadeiro. No exemplo de cadeia de caracteres de servidor a seguir, o alias *any-Unique-alias-name* substitui o primeiro nó delimitado por ponto na cadeia de caracteres do servidor de quatro nós:

- Cadeia de caracteres de servidor de exemplo: `any-unique-alias-name.database.windows.net`.

## <a name="prerequisites"></a>Pré-requisitos

Se você quiser executar o script de demonstração do PowerShell fornecido neste artigo, os seguintes pré-requisitos se aplicarão:

- Uma assinatura e conta do Azure. Para uma avaliação gratuita, clique em [https://azure.microsoft.com/free/][https://azure.microsoft.com/free/].
- Azure PowerShell Module, com cmdlet **New-AzSqlServerDNSAlias**.
  - Para instalar ou atualizar, veja [Instalar o módulo do Azure PowerShell][install-Az-ps-84p].
  - Execute `Get-Module -ListAvailable Az;` no PowerShell\_ISE. exe para localizar a versão.
- Dois servidores de banco de dados SQL do Azure.

## <a name="code-example"></a>Exemplo de código

O exemplo de código do PowerShell a seguir começa atribuindo valores literais a várias variáveis. Para executar o código, você deve primeiro editar todos os valores de espaço reservado para corresponder valores reais em seu sistema. Ou você pode apenas estudar o código. E a saída do console do código também é fornecida.

```powershell
################################################################
###    Assign prerequisites.                                 ###
################################################################
cls;

$SubscriptionName             = '<EDIT-your-subscription-name>';
[string]$SubscriptionGuid_Get = '?'; # The script assigns this value, not you.

$SqlServerDnsAliasName = '<EDIT-any-unique-alias-name>';

$1ResourceGroupName = '<EDIT-rg-1>';  # Can be same or different than $2ResourceGroupName.
$1SqlServerName     = '<EDIT-sql-1>'; # Must differ from $2SqlServerName.

$2ResourceGroupName = '<EDIT-rg-2>';
$2SqlServerName     = '<EDIT-sql-2>';

# Login to your Azure subscription, first time per session.
Write-Host "You must log into Azure once per powershell_ise.exe session,";
Write-Host "  thus type 'yes' only the first time.";
Write-Host " ";
$yesno = Read-Host '[yes/no]  Do you need to log into Azure now?';
if ('yes' -eq $yesno)
{
    Connect-AzAccount -SubscriptionName $SubscriptionName;
}

$SubscriptionGuid_Get = Get-AzSubscription `
    -SubscriptionName $SubscriptionName;

################################################################
###    Working with DNS aliasing for Azure SQL DB server.    ###
################################################################

Write-Host '[1] Assign a DNS alias to SQL DB server 1.';
New-AzSqlServerDNSAlias `
    –ResourceGroupName  $1ResourceGroupName `
    -ServerName         $1SqlServerName `
    -ServerDNSAliasName $SqlServerDnsAliasName;

Write-Host '[2] Get and list all the DNS aliases that are assigned to SQL DB server 1.';
Get-AzSqlServerDNSAlias `
    –ResourceGroupName $1ResourceGroupName `
    -ServerName        $1SqlServerName;

Write-Host '[3] Move the DNS alias from 1 to SQL DB server 2.';
Set-AzSqlServerDNSAlias `
    –ResourceGroupName  $2ResourceGroupName `
    -NewServerName      $2SqlServerName `
    -ServerDNSAliasName $SqlServerDnsAliasName `
    -OldServerResourceGroup  $1ResourceGroupName `
    -OldServerName           $1SqlServerName `
    -OldServerSubscriptionId $SubscriptionGuid_Get;

# Here your client, such as SSMS, can connect to your "$2SqlServerName"
# by using "$SqlServerDnsAliasName" in the server name.
# For example, server:  "any-unique-alias-name.database.windows.net".

# Remove-AzSqlServerDNSAlias  - would fail here for SQL DB server 1.

Write-Host '[4] Remove the DNS alias from SQL DB server 2.';
Remove-AzSqlServerDNSAlias `
    –ResourceGroupName  $2ResourceGroupName `
    -ServerName         $2SqlServerName `
    -ServerDNSAliasName $SqlServerDnsAliasName;
```

### <a name="actual-console-output-from-the-powershell-example"></a>Saída de console real do exemplo do PowerShell

A saída do console a seguir foi copiada e colada de uma execução real.

```powershell
You must log into Azure once per powershell_ise.exe session,
  thus type 'yes' only the first time.

[yes/no]  Do you need to log into Azure now?: yes


Environment           : AzureCloud
Account               : gm@acorporation.com
TenantId              : 72f988bf-1111-1111-1111-111111111111
SubscriptionId        : 45651c69-2222-2222-2222-222222222222
SubscriptionName      : mysubscriptionname
CurrentStorageAccount :

[1] Assign a DNS alias to SQL DB server 1.
[2] Get the DNS alias that is assigned to SQL DB server 1.
[3] Move the DNS alias from 1 to SQL DB server 2.
[4] Remove the DNS alias from SQL DB server 2.
ResourceGroupName ServerName         ServerDNSAliasName
----------------- ----------         ------------------
gm-rg-dns-1       gm-sqldb-dns-1     unique-alias-name-food
gm-rg-dns-1       gm-sqldb-dns-1     unique-alias-name-food
gm-rg-dns-2       gm-sqldb-dns-2     unique-alias-name-food


[C:\windows\system32\]
>>
```

## <a name="next-steps"></a>Passos seguintes

Para obter uma explicação completa do recurso de alias de DNS para o banco de dados SQL, consulte [alias DNS para o banco de dados SQL do Azure][dns-alias-overview-37v].

<!-- Article links. -->

[https://azure.microsoft.com/free/]: https://azure.microsoft.com/free/

[install-Az-ps-84p]: https://docs.microsoft.com/powershell/azure/install-az-ps

[dns-alias-overview-37v]: dns-alias-overview.md
