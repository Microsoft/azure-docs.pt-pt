---
title: Tipos de conexão de instância geridos
description: Saiba mais sobre tipos de conexão de instâncias geridas
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto
ms.date: 10/07/2019
ms.openlocfilehash: a7df26590ec1514edcab24a1af9d85048b332d92
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83773648"
---
# <a name="azure-sql-database-managed-instance-connection-types"></a>Base de dados Azure SQL gerido tipos de conexão de instância

Este artigo explica como os clientes se ligam à Base de Dados Azure SQL gerida em função do tipo de ligação. As amostras de script para alterar os tipos de ligação são fornecidas abaixo, juntamente com considerações relacionadas com a alteração das definições de conectividade padrão.

## <a name="connection-types"></a>Tipos de ligação

A instância gerida pela Base de Dados Azure SQL suporta os seguintes dois tipos de ligação:

- **Redirecionamento (recomendado):** Os clientes estabelecem ligações diretamente ao nó que acolhe a base de dados. Para permitir a conectividade utilizando o redirecionamento, deve abrir firewalls e Grupos de Segurança de Rede (NSG) para permitir o acesso nas portas 1433 e 11000-11999. Os pacotes vão diretamente para a base de dados, pelo que existem melhorias de desempenho de latência e de desempenho de desempenho utilizando o Redirect over Proxy.
- **Procuração (padrão):** Neste modo, todas as ligações estão a utilizar um componente proxy gateway. Para permitir a conectividade, só é necessário abrir a porta 1433 para redes privadas e porta 3342 para ligação pública. A escolha deste modo pode resultar numa maior latência e menor potência, dependendo da natureza da carga de trabalho. Recomendamos vivamente a política de ligação redirecionamento sobre a política de ligação proxy para a latência mais baixa e a maior dose de entrada.

## <a name="redirect-connection-type"></a>Tipo de ligação redireccional

O tipo de ligação redireccional significa que, após a sessão de TCP ser estabelecida no motor SQL, a sessão de cliente obtém o IP virtual de destino do nó de cluster virtual do equilibrista de carga. Os pacotes subsequentes fluem diretamente para o nó de cluster virtual, contornando o gateway. O diagrama que se segue ilustra este fluxo de tráfego.

![redirecionamento.png](media/sql-database-managed-instance-connection-types/redirect.png)

> [!IMPORTANT]
> O tipo de ligação de redirecionamento funciona atualmente apenas para pontos finais privados. Independentemente da definição do tipo de ligação, as ligações que venham através do ponto final do público seriam através de um proxy.

## <a name="proxy-connection-type"></a>Tipo de ligação proxy

O tipo de ligação proxy significa que a sessão de TCP é estabelecida utilizando o gateway e todos os pacotes subsequentes fluem através dele. O diagrama que se segue ilustra este fluxo de tráfego.

![proxy.png](media/sql-database-managed-instance-connection-types/proxy.png)

## <a name="script-to-change-connection-type-settings-using-powershell"></a>Script para alterar definições do tipo de ligação usando powerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

O seguinte script PowerShell mostra como alterar o tipo de ligação para uma instância gerida para Redirecionar.

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
- Saiba como [configurar um ponto final público em caso gerido](sql-database-managed-instance-public-endpoint-configure.md)
- Saiba mais sobre a arquitetura de [conectividade de instância gerida](sql-database-managed-instance-connectivity-architecture.md)