---
title: Modelos do Azure Resource Manager
description: Use modelos de Azure Resource Manager para criar e configurar o banco de dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: overview-samples
ms.devlang: ''
ms.topic: sample
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 02/04/2019
ms.openlocfilehash: 16091286aecc6dfdbcfa833a76920ff4bc9c64de
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73821135"
---
# <a name="azure-resource-manager-templates-for-azure-sql-database"></a>Modelos de Azure Resource Manager para o banco de dados SQL do Azure

Os modelos de Azure Resource Manager permitem que você defina sua infraestrutura como código e implante suas soluções na nuvem do Azure.

## <a name="single-database--elastic-pooltabsingle-database"></a>[Banco de dados individual & pool elástico](#tab/single-database)

A tabela a seguir inclui links para modelos de Azure Resource Manager para o banco de dados SQL do Azure.

| |  |
|---|---|
| [Banco de dados individual](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-database-transparent-encryption-create) | Este modelo de Azure Resource Manager cria um único banco de dados SQL do Azure com servidor lógico e configura as regras de firewall. |
| [Servidor lógico](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-logical-server) | Este modelo de Azure Resource Manager cria um servidor lógico para o banco de dados SQL do Azure. |
| [Pool elástico](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-elastic-pool-create) | Este modelo permite que você implante um novo pool elástico com seu novo SQL Server associado e novos bancos de dados SQL a serem atribuídos a ele. |
| [Grupos de failover](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-with-failover-group) | Este modelo cria dois servidores lógicos do SQL Azure, um banco de dados SQL e um grupo de failover.|
| [Detecção de ameaças](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-threat-detection-db-policy-multiple-databases) | Este modelo permite que você implante um servidor lógico do SQL Azure e um conjunto de bancos de dados SQL do Azure com a detecção de ameaças habilitada, com um endereço de email para alertas para cada banco de dados. A detecção de ameaças faz parte da oferta de proteção de ameaças avançadas do SQL (ATP) e fornece uma camada de segurança que responde a ameaças potenciais em servidores e bancos de dados SQL.|
| [Auditoria para o armazenamento de BLOBs do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-blob-storage) | Este modelo permite que você implante um servidor lógico do SQL Azure com a auditoria habilitada para gravar logs de auditoria em um armazenamento de BLOBs. A auditoria do banco de dados SQL do Azure rastreia eventos de banco de dados e os grava em um log de auditoria que pode ser colocado em sua conta de armazenamento do Azure, espaço de trabalho do OMS ou hubs de eventos.|
| [Auditoria para o Hub de eventos do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-eventhub) | Este modelo permite que você implante um SQL Server do Azure com a auditoria habilitada para gravar logs de auditoria em um hub de eventos existente. Para enviar eventos de auditoria para o Hub de eventos, defina as configurações de auditoria com `Enabled` `State` e defina `IsAzureMonitorTargetEnabled` como `true`. Além disso, defina as configurações de diagnóstico com `SQLSecurityAuditEvents` categoria de logs de diagnóstico no banco de dados `master` (para a auditoria de nível de serviço). A auditoria do banco de dados SQL do Azure e SQL Data Warehouse rastreia eventos de banco de dados e os grava em um log de auditoria que pode ser colocado em sua conta de armazenamento do Azure, espaço de trabalho do OMS ou hubs de eventos.|
| [Aplicativo Web do Azure com Banco de dados SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database) | Este exemplo cria um aplicativo Web do Azure gratuito e um banco de dados SQL no nível de serviço "básico".|
| [Aplicativo Web do Azure e cache Redis com o banco de dados SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-redis-cache-sql-database) | Este modelo cria um aplicativo Web, o cache Redis e o banco de dados SQL no mesmo grupo de recursos e cria duas cadeias de conexão no aplicativo Web para o banco de dados SQL e o cache Redis.|
| [Importar dados do armazenamento de BLOBs usando o ADF v2](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-v2-blob-to-sql-copy) | Este modelo de Azure Resource Manager cria Azure Data Factory v2 que copia dados do armazenamento de BLOBs do Azure para o banco de dados SQL.|
| [Cluster HDInsight com um banco de dados SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-with-sql-database) | Este modelo permite que você crie um cluster HDInsight, um servidor de banco de dados SQL, um banco de dados SQL e duas tabelas. Este modelo é usado pelo [artigo usar o Sqoop com Hadoop no HDInsight](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-use-sqoop) |
| [Aplicativo lógico do Azure que executa um procedimento armazenado SQL em um agendamento](https://github.com/Azure/azure-quickstart-templates/tree/master/101-logic-app-sql-proc) | Este modelo permite que você crie um aplicativo lógico que executará um procedimento armazenado SQL no agendamento. Quaisquer argumentos para o procedimento podem ser colocados na seção corpo do modelo.|

## <a name="managed-instancetabmanaged-instance"></a>[Instância Gerenciada](#tab/managed-instance)

A tabela a seguir inclui links para modelos de Azure Resource Manager para o banco de dados SQL do Azure-Instância Gerenciada.

| |  |
|---|---|
| [Instância Gerenciada em uma nova VNet](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sqlmi-new-vnet) | Este modelo de Azure Resource Manager cria uma nova VNet do Azure configurada e Instância Gerenciada na VNet. |
| [Ambiente de rede para Instância Gerenciada](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment) | Essa implantação criará uma rede virtual do Azure configurada com duas sub-redes – uma que será dedicada às instâncias gerenciadas e outra em que você pode posicionar outros recursos (por exemplo, VMs, ambientes de serviço de aplicativo, etc.). Este modelo criará um ambiente de rede configurado corretamente onde você pode implantar instâncias gerenciadas. |
| [Instância Gerenciada com conexão P2S](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sqlmi-new-vnet-w-point-to-site-vpn) | Essa implantação criará uma rede virtual do Azure com duas sub-redes `ManagedInstance` e `GatewaySubnet`. Instância Gerenciada serão implantadas na sub-rede ManagedInstance. O gateway de rede virtual será criado em `GatewaySubnet` sub-rede e configurado para a conexão VPN ponto a site. |
| [Instância Gerenciada com a máquina virtual](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sqlmi-new-vnet-w-jumpbox) | Essa implantação criará uma rede virtual do Azure com duas sub-redes `ManagedInstance` e `Management`. Instância Gerenciada serão implantadas na sub-rede `ManagedInstance`. A máquina virtual com a versão mais recente do SQL Server Management Studio (SSMS) será implantada na sub-rede `Management`. |

---
