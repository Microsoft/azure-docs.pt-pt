---
title: Tipos de ligação
titleSuffix: Azure SQL Managed Instance
description: Saiba mais sobre os tipos de conexão Azure SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto
ms.date: 10/07/2019
ms.openlocfilehash: 347f9522bacc768265027f1a2070ac4605ade158
ms.sourcegitcommit: 5a8c8ac84c36859611158892422fc66395f808dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/10/2020
ms.locfileid: "84655679"
---
# <a name="azure-sql-managed-instance-connection-types"></a>Tipos de conexão Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Este artigo explica como os clientes se ligam à Azure SQL Managed Instance dependendo do tipo de ligação. As amostras de script para alterar os tipos de conexão são fornecidas abaixo, juntamente com considerações relacionadas com a alteração das definições de conectividade padrão.

## <a name="connection-types"></a>Tipos de ligação

Azure SQL Managed Instance suporta os seguintes dois tipos de ligação:

- **Redirecionamento (recomendado):** Os clientes estabelecem ligações diretamente ao nó que hospeda a base de dados. Para permitir a conectividade utilizando o redirecionamento, tem de abrir firewalls e Grupos de Segurança de Rede (NSG) para permitir o acesso nas portas 1433 e 11000-11999. Os pacotes vão diretamente para a base de dados, e, portanto, existem melhorias de desempenho de latência e produção usando redirecionamento sobre proxy.
- **Proxy (padrão):** Neste modo, todas as ligações estão a utilizar um componente de gateway proxy. Para permitir a conectividade, é necessário abrir apenas a porta 1433 para redes privadas e o porto 3342 para a ligação pública. A escolha deste modo pode resultar numa maior latência e menor produção, dependendo da natureza da carga de trabalho. Recomendamos vivamente a política de redireccionamento da ligação sobre a política de ligação por procuração para a menor latência e maior produção.

## <a name="redirect-connection-type"></a>Redirecione o tipo de ligação

No tipo de ligação de redirecionamento, após a sessão TCP ser estabelecida para o motor SQL, a sessão do cliente obtém o destino IP virtual do nó de cluster virtual do balançador de carga. Os pacotes subsequentes fluem diretamente para o nó de cluster virtual, contornando o gateway. O diagrama que se segue ilustra este fluxo de tráfego.

![redirect.png](./media/connection-types-overview/redirect.png)

> [!IMPORTANT]
> Atualmente, o tipo de ligação de redireccionamento funciona apenas para um ponto final privado. Independentemente da definição do tipo de ligação, as ligações que entram no ponto final público seriam através de um representante.

## <a name="proxy-connection-type"></a>Tipo de ligação proxy

No tipo de ligação proxy, a sessão TCP é estabelecida usando o gateway e todos os pacotes subsequentes fluem através dele. O diagrama que se segue ilustra este fluxo de tráfego.

![proxy.png](./media/connection-types-overview/proxy.png)

## <a name="script-to-change-connection-type-settings-using-powershell"></a>Script para alterar definições do tipo de ligação usando PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

O seguinte script PowerShell mostra como alterar o tipo de ligação para uma instância gerida para `Redirect` .

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

## <a name="next-steps"></a>Próximos passos

- [Restaurar uma base de dados para SQL Gestditundo Instância](restore-sample-database-quickstart.md)
- Saiba como [configurar um ponto final público em SQL Managed Instance](public-endpoint-configure.md)
- Saiba mais sobre [a arquitetura de conectividade SQL Managed Instance](connectivity-architecture-overview.md)