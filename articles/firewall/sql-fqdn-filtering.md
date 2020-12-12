---
title: Configurar as regras de aplicação do Azure Firewall com FQDNs do SQL
description: Neste artigo, você aprende a configurar SQL FQDNs em regras de aplicação Azure Firewall.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 06/18/2020
ms.author: victorh
ms.openlocfilehash: 2b1b68b32ccd5a4dda0b71736da4e2d1e2566b6b
ms.sourcegitcommit: fa807e40d729bf066b9b81c76a0e8c5b1c03b536
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/11/2020
ms.locfileid: "97348021"
---
# <a name="configure-azure-firewall-application-rules-with-sql-fqdns"></a>Configurar as regras de aplicação do Azure Firewall com FQDNs do SQL

Agora pode configurar as regras de aplicação do Azure Firewall com OS FQDNs SQL. Isto permite-lhe limitar o acesso das suas redes virtuais apenas às instâncias especificadas do servidor SQL.

Com os FQDNs do SQL, pode filtrar o tráfego:

- Desde os seus VNets até uma Base de Dados Azure SQL ou Azure Synapse Analytics. Por exemplo: Só permita o acesso a *sql-server1.database.windows.net*.
- Desde as instalações até ao Azure SQL Managed Instances ou sql iaaS a correr nos seus VNets.
- Desde falar com o Azure SQL Managed Instances ou SQL IaaS a correr nos seus VNets.

A filtragem SQL FQDN é suportada apenas em [modo proxy](../azure-sql/database/connectivity-architecture.md#connection-policy) (porta 1433). Se utilizar o SQL no modo de redirecionamento predefinido, pode filtrar o acesso utilizando a etiqueta de serviço SQL como parte das [regras](features.md#network-traffic-filtering-rules)da rede .
Se utilizar portas não predefinidas para o tráfego IaaS do SQL, poderá configurar essas portas nas regras de aplicação de firewall.

## <a name="configure-using-azure-cli"></a>Configure usando Azure CLI

1. Implementar uma [Firewall Azure utilizando o Azure CLI](deploy-cli.md).
2. Se filtrar o tráfego para Azure SQL Database, Azure Synapse Analytics ou SQL Managed Instance, certifique-se de que o modo de conectividade SQL está definido para **Proxy**. Para aprender a mudar o modo de conectividade SQL, consulte as [Definições de Conectividade Azure SQL](../azure-sql/database/connectivity-settings.md#change-the-connection-policy-via-the-azure-cli).

   > [!NOTE]
   > O modo *de procuração* SQL pode resultar em mais latência em comparação com *o redirecionamento*. Se pretender continuar a utilizar o modo de redireccionamento, que é o padrão para os clientes que se ligam dentro do Azure, pode filtrar o acesso utilizando a [etiqueta de serviço](service-tags.md) SQL nas [regras da rede](tutorial-firewall-deploy-portal.md#configure-a-network-rule)de firewall .

3. Crie uma nova coleção de regras com uma regra de aplicação utilizando o SQL FQDN para permitir o acesso a um servidor SQL:

   ```azurecli
    az extension add -n azure-firewall
    
    az network firewall application-rule create \ 
    -g FWRG \
    --f azfirewall \ 
    --c sqlRuleCollection \
    --priority 1000 \
    --action Allow \
    --name sqlRule \
    --protocols mssql=1433 \
    --source-addresses 10.0.0.0/24 \
    --target-fqdns sql-serv1.database.windows.net
   ```

## <a name="configure-using-azure-powershell"></a>Configure usando Azure PowerShell

1. Implementar uma [firewall Azure utilizando a Azure PowerShell](deploy-ps.md).
2. Se filtrar o tráfego para Azure SQL Database, Azure Synapse Analytics ou SQL Managed Instance, certifique-se de que o modo de conectividade SQL está definido para **Proxy**. Para aprender a mudar o modo de conectividade SQL, consulte as [Definições de Conectividade Azure SQL](../azure-sql/database/connectivity-settings.md#change-the-connection-policy-via-the-azure-cli).

   > [!NOTE]
   > O modo *de procuração* SQL pode resultar em mais latência em comparação com *o redirecionamento*. Se pretender continuar a utilizar o modo de redireccionamento, que é o padrão para os clientes que se ligam dentro do Azure, pode filtrar o acesso utilizando a [etiqueta de serviço](service-tags.md) SQL nas [regras da rede](tutorial-firewall-deploy-portal.md#configure-a-network-rule)de firewall .

3. Crie uma nova coleção de regras com uma regra de aplicação utilizando o SQL FQDN para permitir o acesso a um servidor SQL:

   ```azurepowershell
   $AzFw = Get-AzFirewall -Name "azfirewall" -ResourceGroupName "FWRG"
    
   $sqlRule = @{
      Name          = "sqlRule"
      Protocol      = "mssql:1433" 
      TargetFqdn    = "sql-serv1.database.windows.net"
      SourceAddress = "10.0.0.0/24"
   }
    
   $rule = New-AzFirewallApplicationRule @sqlRule
    
   $sqlRuleCollection = @{
      Name       = "sqlRuleCollection" 
      Priority   = 1000 
      Rule       = $rule
      ActionType = "Allow"
   }
    
   $ruleCollection = New-AzFirewallApplicationRuleCollection @sqlRuleCollection
    
   $Azfw.ApplicationRuleCollections.Add($ruleCollection)    
   Set-AzFirewall -AzureFirewall $AzFw    
   ```

## <a name="configure-using-the-azure-portal"></a>Configurar com o portal do Azure
1. Implementar uma [Firewall Azure utilizando o Azure CLI](deploy-cli.md).
2. Se filtrar o tráfego para Azure SQL Database, Azure Synapse Analytics ou SQL Managed Instance, certifique-se de que o modo de conectividade SQL está definido para **Proxy**. Para aprender a mudar o modo de conectividade SQL, consulte as [Definições de Conectividade Azure SQL](../azure-sql/database/connectivity-settings.md#change-the-connection-policy-via-the-azure-cli).  

   > [!NOTE]
   > O modo *de procuração* SQL pode resultar em mais latência em comparação com *o redirecionamento*. Se pretender continuar a utilizar o modo de redireccionamento, que é o padrão para os clientes que se ligam dentro do Azure, pode filtrar o acesso utilizando a [etiqueta de serviço](service-tags.md) SQL nas [regras da rede](tutorial-firewall-deploy-portal.md#configure-a-network-rule)de firewall .
3. Adicione a regra de aplicação com o protocolo, porta e FQDN SQL adequados e, em seguida, **selecione Save**.
   ![regra de aplicação com SQL FQDN](media/sql-fqdn-filtering/application-rule-sql.png)
4. Aceda ao SQL a partir de uma máquina virtual num VNet que filtra o tráfego através da firewall. 
5. Valide que [os registos da Azure Firewall](log-analytics-samples.md) mostrem que o tráfego é permitido.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre os modos de procuração e redireccionamento [sql, consulte a arquitetura de conectividade Azure SQL Database](../azure-sql/database/connectivity-architecture.md).