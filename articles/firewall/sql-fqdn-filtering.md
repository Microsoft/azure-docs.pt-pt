---
title: Configure regras de aplicação de firewall azure com FQDNs SQL
description: Neste artigo, aprende a configurar fQDNs SQL nas regras de aplicação da Firewall Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 05/11/2020
ms.author: victorh
ms.openlocfilehash: 28252b42264dc6c1be403e99689f845d7143b1f7
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83200474"
---
# <a name="configure-azure-firewall-application-rules-with-sql-fqdns"></a>Configure regras de aplicação de firewall azure com FQDNs SQL

Agora pode configurar as regras de aplicação da Firewall Azure com FQDNs SQL. Isto permite-lhe limitar o acesso das suas redes virtuais apenas às instâncias especificadas do servidor SQL.

Com FQDNs SQL, pode filtrar o tráfego:

- Desde os seus VNets até uma Base de Dados Azure SQL ou um Armazém de Dados Azure SQL. Por exemplo: Só permite o acesso a *sql-server1.database.windows.net*.
- Desde as instalações até às instâncias geridas pelo Azure SQL ou ao SQL IaaS a correr nos seus VNets.
- Desde instâncias geridas por Azure SQL ou SQL IaaS a correr nos seus VNets.

A filtragem SQL FQDN é suportada apenas no [modo proxy](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture#connection-policy) (porta 1433). Se utilizar o SQL no modo de redirecionamento predefinido, pode filtrar o acesso utilizando a etiqueta de serviço SQL como parte das regras da [rede](overview.md#network-traffic-filtering-rules).
Se utilizar portas não predefinidas para tráfego SQL IaaS, pode configurar essas portas nas regras de aplicação da firewall.

As regras de aplicação com FQDNs SQL estão atualmente disponíveis em todas as regiões através do portal Azure, Azure CLI, REST e modelos.

## <a name="configure-using-azure-cli"></a>Configure usando o Azure CLI

1. Implemente uma [Firewall Azure utilizando o Azure CLI](deploy-cli.md).
2. Se filtrar o tráfego para a Base de Dados Azure SQL, O Armazém de Dados SQL ou a Instância Gerida SQL, certifique-se de que o modo de conectividade SQL está definido para **Proxy**. Para aprender a mudar o modo de conectividade SQL, consulte as Definições de [Conectividade SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-settings#change-connection-policy-via-azure-cli).

   > [!NOTE]
   > O modo *de procuração* SQL pode resultar em mais latência em comparação com *o redirecionamento*. Se pretender continuar a utilizar o modo de redirecionamento, que é o padrão para os clientes que se conectam dentro do Azure, pode filtrar o acesso utilizando a etiqueta de [serviço](service-tags.md) SQL nas regras da [rede](tutorial-firewall-deploy-portal.md#configure-a-network-rule)firewall .

3. Configure uma regra de aplicação com o SQL FQDN para permitir o acesso a um servidor SQL:

   ```azurecli
   az extension add -n azure-firewall

   az network firewall application-rule create \
   -g FWRG \
   -f azfirewall \
   -c FWAppRules \
   -n srule \
   --protocols mssql=1433 \
   --source-addresses 10.0.0.0/24 \
   --target-fqdns sql-serv1.database.windows.net
   ```

## <a name="configure-using-the-azure-portal"></a>Configurar com o portal do Azure
1. Implemente uma [Firewall Azure utilizando o Azure CLI](deploy-cli.md).
2. Se filtrar o tráfego para a Base de Dados Azure SQL, O Armazém de Dados SQL ou a Instância Gerida SQL, certifique-se de que o modo de conectividade SQL está definido para **Proxy**. Para aprender a mudar o modo de conectividade SQL, consulte as Definições de [Conectividade SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-settings#change-connection-policy-via-azure-cli).  

   > [!NOTE]
   > O modo *de procuração* SQL pode resultar em mais latência em comparação com *o redirecionamento*. Se pretender continuar a utilizar o modo de redirecionamento, que é o padrão para os clientes que se conectam dentro do Azure, pode filtrar o acesso utilizando a etiqueta de [serviço](service-tags.md) SQL nas regras da [rede](tutorial-firewall-deploy-portal.md#configure-a-network-rule)firewall .
3. Adicione a regra de aplicação com o protocolo apropriado, porta e SQL FQDN e, em seguida, **selecione Save**.
   ![regra de aplicação com SQL FQDN](media/sql-fqdn-filtering/application-rule-sql.png)
4. Aceda ao SQL a partir de uma máquina virtual num VNet que filtra o tráfego através da firewall. 
5. Valide que os [registos da Firewall Azure](log-analytics-samples.md) mostrem que o tráfego é permitido.

## <a name="next-steps"></a>Passos seguintes

Para saber sobre os modos de procuração e redirecionamento da SQL, consulte a arquitetura de conectividade da base de [dados Azure SQL](../sql-database/sql-database-connectivity-architecture.md).