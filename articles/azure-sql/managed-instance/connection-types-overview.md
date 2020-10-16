---
title: Tipos de ligação
titleSuffix: Azure SQL Managed Instance
description: Saiba mais sobre os tipos de conexão Azure SQL Managed Instance
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto
ms.date: 10/07/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: f1c4fe8268d24026609f55d76a102a5c9a4e8295
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91356321"
---
# <a name="azure-sql-managed-instance-connection-types"></a>Tipos de ligação do Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Este artigo explica como os clientes se ligam à Azure SQL Managed Instance dependendo do tipo de ligação. As amostras de script para alterar os tipos de conexão são fornecidas abaixo, juntamente com considerações relacionadas com a alteração das definições de conectividade padrão.

## <a name="connection-types"></a>Tipos de ligação

Azure SQL Managed Instance suporta os seguintes dois tipos de ligação:

- **Redirecionamento (recomendado):** Os clientes estabelecem ligações diretamente ao nó que hospeda a base de dados. Para ativar a conectividade através do redirecionamento, tem de abrir as firewalls e os Grupos de Segurança de Rede (NSG) para permitir o acesso nas portas 1433 e 11000-11999. Os pacotes vão diretamente para a base de dados e, portanto, há melhorias de desempenho de débito e latência através do redirecionamento em vez do proxy.
- **Proxy (padrão):** Neste modo, todas as ligações estão a utilizar um componente de gateway proxy. Para ativar a conectividade, apenas a porta 1433 para as redes privadas e a porta 3342 para a ligação pública precisam de ser abertas. Escolher este modo pode resultar numa latência superior e débito inferior, dependendo da natureza da carga de trabalho. É altamente recomendável a política de ligação de redirecionamento em vez da política de ligação de proxy para obter a menor latência e o maior débito.

## <a name="redirect-connection-type"></a>Redirecione o tipo de ligação

No tipo de ligação de redirecionamento, após a sessão TCP ser estabelecida para o motor SQL, a sessão do cliente obtém o destino IP virtual do nó de cluster virtual do balançador de carga. Os pacotes subsequentes fluem diretamente para o nó de cluster virtual, contornando o gateway. O diagrama que se segue ilustra este fluxo de tráfego.

![O diagrama mostra uma rede no local com redireccionamento-db ligado a uma porta de entrada numa rede virtual Azure e uma consulta de redirecionamento ligada a um nó primário de base de dados na rede virtual.](./media/connection-types-overview/redirect.png)

> [!IMPORTANT]
> Atualmente, o tipo de ligação de redireccionamento funciona apenas para um ponto final privado. Independentemente da definição do tipo de ligação, as ligações que entram no ponto final público seriam através de um representante.

## <a name="proxy-connection-type"></a>Tipo de ligação proxy

No tipo de ligação proxy, a sessão TCP é estabelecida usando o gateway e todos os pacotes subsequentes fluem através dele. O diagrama que se segue ilustra este fluxo de tráfego.

![O diagrama mostra uma rede no local com um proxy ligado a um gateway numa rede virtual Azure, conectando-se ao lado de um nó primário de base de dados na rede virtual.](./media/connection-types-overview/proxy.png)

## <a name="changing-connection-type"></a>Alteração do tipo de ligação

- **Utilização do Portal:** Para alterar o Tipo de Ligação utilizando o portal Azure, abra a página 'Rede Virtual' e utilize a definição **do tipo de Ligação** para alterar o tipo de ligação e guardar as alterações.

- **Script para alterar definições do tipo de ligação usando PowerShell:**

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

## <a name="next-steps"></a>Passos seguintes

- [Restaurar uma base de dados para SQL Gestditundo Instância](restore-sample-database-quickstart.md)
- Saiba como [configurar um ponto final público em SQL Managed Instance](public-endpoint-configure.md)
- Saiba mais sobre [a arquitetura de conectividade SQL Managed Instance](connectivity-architecture-overview.md)
