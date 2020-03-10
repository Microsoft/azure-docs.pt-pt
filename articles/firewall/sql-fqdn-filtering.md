---
title: Configure regras de aplicação de firewall azure com FQDNs SQL
description: Neste artigo, aprende a configurar fQDNs SQL nas regras de aplicação da Firewall Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 07/19/2019
ms.author: victorh
ms.openlocfilehash: a42d6bcdcec2a5de7432f11216a4d8dd0c1deef9
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2020
ms.locfileid: "78942571"
---
# <a name="configure-azure-firewall-application-rules-with-sql-fqdns"></a>Configure regras de aplicação de firewall azure com FQDNs SQL

> [!IMPORTANT]
> As regras de aplicação da Firewall Azure com FQDNs SQL estão atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Agora pode configurar as regras de aplicação da Firewall Azure com FQDNs SQL. Isto permite-lhe limitar o acesso das suas redes virtuais apenas às instâncias especificadas do servidor SQL.

Com FQDNs SQL, pode filtrar o tráfego:

- Desde os seus VNets até uma Base de Dados Azure SQL ou um Armazém de Dados Azure SQL. Por exemplo: Só permite o acesso a *sql-server1.database.windows.net*.
- Desde as instalações até às instâncias geridas pelo Azure SQL ou ao SQL IaaS a correr nos seus VNets.
- Desde instâncias geridas por Azure SQL ou SQL IaaS a correr nos seus VNets.

Durante a [pré-visualização](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture#connection-policy) pública, a filtragem SQL FQDN é suportada apenas em modo proxy (porta 1433). Se utilizar o SQL no modo de redirecionamento predefinido, pode filtrar o acesso utilizando a etiqueta de serviço SQL como parte das regras da [rede](overview.md#network-traffic-filtering-rules).
Se utilizar portas não predefinidas para tráfego SQL IaaS, pode configurar essas portas nas regras de aplicação da firewall.

As regras de aplicação com FQDNs SQL estão atualmente disponíveis em todas as regiões através do portal Azure, Azure CLI, REST e modelos.

## <a name="configure-using-azure-cli"></a>Configure usando o Azure CLI

1. Implemente uma [Firewall Azure utilizando o Azure CLI](deploy-cli.md).
2. Se filtrar o tráfego para a Base de Dados Azure SQL, O Armazém de Dados SQL ou a Instância Gerida SQL, certifique-se de que o modo de conectividade SQL está definido para **Proxy**. Para aprender a mudar o modo de conectividade SQL, consulte as Definições de [Conectividade SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-settingse#change-azure-sql-database-connection-policy).

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

## <a name="configure-using-the-azure-portal"></a>Configure usando o portal Azure
1. Implemente uma [Firewall Azure utilizando o Azure CLI](deploy-cli.md).
2. Se filtrar o tráfego para a Base de Dados Azure SQL, O Armazém de Dados SQL ou a Instância Gerida SQL, certifique-se de que o modo de conectividade SQL está definido para **Proxy**. Para aprender a mudar o modo de conectividade SQL, consulte as Definições de [Conectividade SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-settingse#change-azure-sql-database-connection-policy).  

   > [!NOTE]
   > O modo *de procuração* SQL pode resultar em mais latência em comparação com *o redirecionamento*. Se pretender continuar a utilizar o modo de redirecionamento, que é o padrão para os clientes que se conectam dentro do Azure, pode filtrar o acesso utilizando a etiqueta de [serviço](service-tags.md) SQL nas regras da [rede](tutorial-firewall-deploy-portal.md#configure-a-network-rule)firewall .
3. Adicione a regra de aplicação com o protocolo apropriado, porta e SQL FQDN e, em seguida, **selecione Save**.
   regra de aplicação ![com](media/sql-fqdn-filtering/application-rule-sql.png) SQL FQDN
4. Aceda ao SQL a partir de uma máquina virtual num VNet que filtra o tráfego através da firewall. 
5. Valide que os [registos da Firewall Azure](log-analytics-samples.md) mostrem que o tráfego é permitido.

## <a name="next-steps"></a>Passos Seguintes

Para saber sobre os modos de procuração e redirecionamento da SQL, consulte a arquitetura de conectividade da base de [dados Azure SQL](../sql-database/sql-database-connectivity-architecture.md).