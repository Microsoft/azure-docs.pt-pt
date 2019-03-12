---
title: PowerShell para o Azure SQL, no alias de DNS | Documentos da Microsoft
description: Cmdlets do PowerShell, como o novo AzSqlServerDNSAlias permitem-lhe redirecionar novas ligações de cliente para um servidor de base de dados do Azure SQL diferente, sem precisar tocar qualquer configuração de cliente.
keywords: base de dados de sql de DNS
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.devlang: PowerShell
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: genemi,amagarwa,maboja, jrasnick
manager: craigg
ms.date: 03/07/2019
ms.openlocfilehash: cc73ae23b25fbbef2ee058f926138cf0873d451c
ms.sourcegitcommit: 30a0007f8e584692fe03c0023fe0337f842a7070
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57576604"
---
# <a name="powershell-for-dns-alias-to-azure-sql-database"></a>PowerShell para o Alias de DNS para a base de dados SQL do Azure

Este artigo fornece um script do PowerShell que demonstra como pode gerir um alias de DNS para a base de dados do Azure SQL. O script executa os seguintes cmdlets que efetua as seguintes ações:

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Os cmdlets utilizados no exemplo de código são as seguintes:

- [New-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/New-azSqlServerDnsAlias): Cria um novo alias DNS no sistema de serviço de base de dados do Azure SQL. O alias de suporte de dados refere-se ao servidor de base de dados do Azure SQL 1.
- [Get-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlServerDnsAlias): Obter e listar todos os aliases DNS que estão atribuídos ao servidor de BD SQL 1.
- [Set-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Set-azSqlServerDnsAlias): Modifica o nome do servidor que o alias está configurado para consultar, do servidor 1 para o servidor de BD SQL 2.
- [Remove-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Remove-azSqlServerDnsAlias): Remova o alias de DNS de BD do SQL server 2, com o nome de alias.

## <a name="dns-alias-in-connection-string"></a>Alias de DNS na cadeia de ligação

Para ligar um determinado servidor de base de dados do Azure SQL, um cliente, como o SQL Server Management Studio (SSMS) pode fornecer o nome de alias DNS em vez do nome de servidor verdadeiro. Na cadeia de servidor de exemplo seguinte, o alias *qualquer-exclusivo--nome de alias* substitui o primeiro nó delimitados por ponto a cadeia de caracteres do servidor de quatro nós:

- Cadeia de caracteres de servidor de exemplo: `any-unique-alias-name.database.windows.net`.

## <a name="prerequisites"></a>Pré-requisitos

Se quiser executar a demonstração de script do PowerShell apresentada neste artigo, aplicam-se os seguintes pré-requisitos:

- Uma conta e subscrição do Azure. Para obter uma avaliação gratuita, clique em [ https://azure.microsoft.com/free/ ] [ https://azure.microsoft.com/free/].
- Módulo do PowerShell do Azure, com o cmdlet **New-AzSqlServerDNSAlias**.
  - Para instalar ou atualizar, veja [módulo de instalar o Azure PowerShell][install-Az-ps-84p].
  - Execute `Get-Module -ListAvailable Az;` no powershell\_ise.exe, para localizar a versão.
- Dois servidores de base de dados do Azure SQL.

## <a name="code-example"></a>Exemplo de código

O PowerShell seguinte exemplo de código começa por atribuir valores literais para diversas variáveis. Para executar o código, primeiro tem de editar todos os valores de marcador de posição para correspondem aos valores reais no seu sistema. Ou apenas pode estudar o código. E o resultado da consola do código também é fornecido.

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

### <a name="actual-console-output-from-the-powershell-example"></a>Resultado da consola real do exemplo do PowerShell

A seguinte saída de consola foi copiada e colada a partir de uma execução real.

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

## <a name="next-steps"></a>Passos Seguintes

Para obter uma explicação completa do recurso de Alias de DNS para a base de dados SQL, consulte [alias de DNS para o Azure SQL Database][dns-alias-overview-37v].

<!-- Article links. -->

[https://azure.microsoft.com/free/]: https://azure.microsoft.com/free/

[install-Az-ps-84p]: https://docs.microsoft.com/powershell/azure/install-az-ps

[dns-alias-overview-37v]: dns-alias-overview.md
