---
title: Configurar regras de Firewall do Azure de aplicações com o FQDN do SQL
description: Neste artigo, irá aprender a configurar o FQDN do SQL em regras de Firewall do Azure de aplicações.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 7/11/2019
ms.author: victorh
ms.openlocfilehash: e188a5dda8f936ad369aa2b9222bc726bb0d6a5e
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786592"
---
# <a name="configure-azure-firewall-application-rules-with-sql-fqdns"></a>Configurar regras de Firewall do Azure de aplicações com o FQDN do SQL

> [!IMPORTANT]
> Azure regras de Firewall de aplicações com o FQDN do SQL está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Agora pode configurar regras de Firewall do Azure de aplicações com o FQDN do SQL. Isto permite-lhe limitar o acesso das suas redes virtuais para apenas as instâncias de servidor SQL especificados.

Com o FQDN do SQL, pode filtrar o tráfego:

- Das suas VNets para uma base de dados SQL do Azure ou um armazém de dados SQL do Azure. Por exemplo: Permitir apenas o acesso ao *sql server1.database.windows.net*.
- No local para instâncias de geridas de SQL do Azure ou do IaaS do SQL em execução nas suas VNets.
- Do spoke-para-and-spoke para instâncias de geridas de SQL do Azure ou do IaaS do SQL em execução nas suas VNets.

Durante a pré-visualização pública, o FQDN do SQL filtragem é suportada no [modo de proxy](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture#connection-policy) apenas (a porta 1433). Se usar SQL no modo de redirecionamento de predefinido, pode filtrar o acesso usando a marca de serviço do SQL como parte da [regras de rede](overview.md#network-traffic-filtering-rules).
Se utilizar a porta não predefinidos para o tráfego de IaaS do SQL, pode configurar as portas em regras de firewall de aplicações.

> [!NOTE]
> Regras de aplicações com o FQDN do SQL está atualmente disponível em todas as regiões através da CLI do Azure, REST e modelos. A interface de utilizador do portal está a ser adicionada para as regiões de forma incremental e vai estar disponível em todas as regiões quando a implementação estiver concluída.

## <a name="configure-using-azure-cli"></a>Configurar a CLI do Azure

1. Implementar um [Firewall do Azure com a CLI do Azure](deploy-cli.md).
2. Se filtrar o tráfego para a base de dados do Azure SQL, o SQL Data Warehouse ou a instância gerida SQL, certifique-se do modo de conectividade SQL é definido como **Proxy**. Para saber como mudar o modo de conectividade SQL, veja [arquitetura de conectividade do SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture#change-azure-sql-database-connection-policy). 

   > [!NOTE]
   > SQL *proxy* modo pode resultar em mais latência em comparação comparada *redirecionar*. Se pretender continuar a utilizar o modo de redirecionamento, o que é o padrão para clientes que se conectam no Azure, pode filtrar o acesso com o SQL [etiqueta de serviço](service-tags.md) na firewall [regras de rede](tutorial-firewall-deploy-portal.md#configure-a-network-rule).

3. Configure uma regra de aplicação com o FQDN do SQL para permitir o acesso a um SQL server:

   ```azurecli
   az network firewall application-rule create \
   -g FWRG \
   -f azfirewall \
   -c FWAppRules \
   -n srule \
   --protocols mssql=1433 \
   --source-addresses 10.0.0.0/24 \
   --target-fqdns sql-serv1.database.windows.net
   ```

## <a name="configure-using-the-azure-portal"></a>Configurar no portal do Azure
1. Implementar um [Firewall do Azure com a CLI do Azure](deploy-cli.md).
2. Se filtrar o tráfego para a base de dados do Azure SQL, o SQL Data Warehouse ou a instância gerida SQL, certifique-se do modo de conectividade SQL é definido como **Proxy**. Para saber como mudar o modo de conectividade SQL, veja [arquitetura de conectividade do SQL Azure](../sql-database/sql-database-connectivity-architecture.md#change-azure-sql-database-connection-policy). 

   > [!NOTE]
   > SQL *proxy* modo pode resultar em mais latência em comparação comparada *redirecionar*. Se pretender continuar a utilizar o modo de redirecionamento, o que é o padrão para clientes que se conectam no Azure, pode filtrar o acesso com o SQL [etiqueta de serviço](service-tags.md) na firewall [regras de rede](tutorial-firewall-deploy-portal.md#configure-a-network-rule).
3. Adicionar a regra de aplicação com o protocolo apropriado, a porta e o FQDN do SQL e, em seguida, selecione **guardar**.
   ![regra de aplicação com o FQDN do SQL](media/sql-fqdn-filtering/application-rule-sql.png)
4. Acesso SQL a partir de uma máquina virtual numa VNet que filtra o tráfego através da firewall. 
5. Validar que [registos de Firewall do Azure](log-analytics-samples.md) Mostrar o tráfego é permitido.

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre o proxy do SQL e modos de redirecionamento, veja [arquitetura de conectividade de banco de dados SQL do Azure](../sql-database/sql-database-connectivity-architecture.md).