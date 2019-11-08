---
title: Tipos de conexão de instância gerenciada
description: Saiba mais sobre os tipos de conexão de instância gerenciada
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto
ms.date: 10/07/2019
ms.openlocfilehash: 46223d1701b930d93de7c49c1e216a41045dda16
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819464"
---
# <a name="azure-sql-database-managed-instance-connection-types"></a>Tipos de conexão de instância gerenciada do banco de dados SQL do Azure

Este artigo explica como os clientes se conectam à instância gerenciada do banco de dados SQL do Azure, dependendo do tipo de conexão. Exemplos de script para alterar tipos de conexão são fornecidos abaixo, juntamente com considerações relacionadas à alteração das configurações de conectividade padrão.

## <a name="connection-types"></a>Tipos de ligação

A instância gerenciada do banco de dados SQL do Azure oferece suporte aos seguintes dois tipos de conexão:

- **Redirecionar (recomendado):** Os clientes estabelecem conexões diretamente com o nó que hospeda o banco de dados. Para habilitar a conectividade usando o redirecionamento, você deve abrir firewalls e grupos de segurança de rede (NSG) para permitir o acesso nas portas 1433 e 11000-11999. Os pacotes vão diretamente para o banco de dados e, portanto, há melhorias de desempenho de latência e taxa de transferência usando o redirecionamento por proxy.
- **Proxy (padrão):** Nesse modo, todas as conexões estão usando um componente de gateway de proxy. Para habilitar a conectividade, somente a porta 1433 para redes privadas e a porta 3342 para conexão pública precisam ser abertas. Escolher esse modo pode resultar em latência mais alta e menor taxa de transferência, dependendo da natureza da carga de trabalho. É altamente recomendável a política de conexão de redirecionamento pela política de conexão de proxy para a menor latência e taxa de transferência mais alta.

## <a name="redirect-connection-type"></a>Redirecionar tipo de conexão

Redirecionar o tipo de conexão significa que, depois que a sessão TCP for estabelecida com o mecanismo do SQL, a sessão do cliente obterá o IP virtual de destino do nó do cluster virtual do balanceador de carga. Os pacotes subsequentes fluem diretamente para o nó do cluster virtual, ignorando o gateway. O diagrama a seguir ilustra esse fluxo de tráfego.

![redirect. png](media/sql-database-managed-instance-connection-types/redirect.png)

> [!IMPORTANT]
> O tipo de conexão de redirecionamento atualmente funciona apenas para o ponto de extremidade privado. Independentemente da configuração do tipo de conexão, as conexões provenientes do ponto de extremidade público seriam por meio de um proxy.

## <a name="proxy-connection-type"></a>Tipo de conexão de proxy

Tipo de conexão de proxy significa que a sessão TCP é estabelecida usando o gateway e todos os pacotes subsequentes fluem através dele. O diagrama a seguir ilustra esse fluxo de tráfego.

![proxy. png](media/sql-database-managed-instance-connection-types/proxy.png)

## <a name="script-to-change-connection-type-settings-using-powershell"></a>Script para alterar as configurações de tipo de conexão usando o PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

O script do PowerShell a seguir mostra como alterar o tipo de conexão de uma instância gerenciada para redirecionar.

```powershell
Install-Module -Name Az
Import-Module Az.Accounts
Import-Module Az.Sql

Connect-AzAccount
# Get your SubscriptionId from the Get-AzSubscription command
Get-AzSubscription
# Use your SubscriptionId in place of {subscription-id} below
Select-AzSubscription -SubscriptionId {subscription-id}
# Replace {rg-name} with the resource group for your managed instance, and replace {mi-name} with the name of your managed instance
$mi = Get-AzSqlInstance -ResourceGroupName {rg-name} -Name {mi-name}
$mi = $mi | Set-AzSqlInstance -ProxyOverride "Redirect" -force
```

## <a name="next-steps"></a>Passos seguintes

- [Restaurar uma base de dados numa instância gerida](sql-database-managed-instance-get-started-restore.md)
- Saiba como [configurar um ponto de extremidade público na instância gerenciada](sql-database-managed-instance-public-endpoint-configure.md)
- Saiba mais sobre a [arquitetura de conectividade da instância gerenciada](sql-database-managed-instance-connectivity-architecture.md)