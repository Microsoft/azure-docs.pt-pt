---
title: Modelos de Gestor de Recursos Azure - Azure SQL Database & SQL Managed Instance
description: Utilize modelos de Gestor de Recursos Azure para criar e configurar a Base de Dados Azure SQL e a Azure SQL Managed Instance.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: overview-samples sqldbrb=2
ms.devlang: ''
ms.topic: guide
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 02/04/2019
ms.openlocfilehash: 5202145af6f60f34bdce57aa94f4fec92a8b227f
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92677271"
---
# <a name="azure-resource-manager-templates-for-azure-sql-database--sql-managed-instance"></a>Modelos de gestor de recursos Azure para Azure SQL Database & SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Os modelos do Azure Resource Manager permitem definir a infraestrutura como código e implementar as soluções na cloud do Azure para a Base de Dados SQL e o Azure SQL Managed Instance.

## <a name="azure-sql-database"></a>[Base de Dados SQL do Azure](#tab/single-database)

A tabela seguinte inclui links para modelos de Gestor de Recursos Azure para Azure SQL Database.

|Ligação |Descrição|
|---|---|
| [Base de Dados SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-database-transparent-encryption-create) | Este modelo de Gestor de Recursos Azure cria uma única base de dados na Base de Dados Azure SQL e configura as regras de firewall IP de nível do servidor. |
| [Servidor](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-logical-server) | Este modelo de Gestor de Recursos Azure cria um servidor para a Base de Dados Azure SQL. |
| [Conjunto elástico](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-elastic-pool-create) | Este modelo permite-lhe implantar uma piscina elástica e atribuir-lhe bases de dados. |
| [Grupos de ativação pós-falha](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-with-failover-group) | Este modelo cria dois servidores, uma única base de dados e um grupo de failover na Base de Dados Azure SQL.|
| [Deteção de Ameaças](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-threat-detection-db-policy-multiple-databases) | Este modelo permite-lhe implementar um servidor e um conjunto de bases de dados com Deteção de Ameaças ativadas, com um endereço de e-mail para alertas para cada base de dados. A Deteção de Ameaças faz parte da oferta de Proteção avançada de ameaças (ATP) sql e fornece uma camada de segurança que responde a potenciais ameaças sobre servidores e bases de dados.|
| [Auditoria ao armazenamento da Azure Blob](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-blob-storage) | Este modelo permite-lhe implementar um servidor com auditoria habilitada a escrever registos de auditoria para um armazenamento blob. A auditoria ao Azure SQL Database rastreia eventos de base de dados e escreve-os para um registo de auditoria que pode ser colocado na sua conta de armazenamento Azure, espaço de trabalho OMS ou Centros de Eventos.|
| [Auditoria ao Azure Event Hub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-eventhub) | Este modelo permite-lhe implementar um servidor com auditoria habilitada a escrever registos de auditoria para um centro de eventos existente. Para enviar eventos de auditoria para os Centros de Eventos, defina definições de auditoria `Enabled` `State` com, e definir `IsAzureMonitorTargetEnabled` como `true` . Além disso, configurar definições de diagnóstico com a `SQLSecurityAuditEvents` categoria de registo na base de `master` dados (para auditoria ao nível do servidor). A auditoria rastreia eventos de base de dados e escreve-os para um registo de auditoria que pode ser colocado na sua conta de armazenamento Azure, espaço de trabalho OMS ou Centros de Eventos.|
| [App Azure Web com Base de Dados SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database) | Esta amostra cria uma aplicação web Azure gratuita e uma base de dados na Base de Dados Azure SQL ao nível de serviço "Básico".|
| [Azure Web App e Redis Cache com Base de Dados SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-redis-cache-sql-database) | Este modelo cria uma aplicação web, Redis Cache, e base de dados no mesmo grupo de recursos e cria duas cadeias de conexão na aplicação web para a base de dados e Redis Cache.|
| [Dados de importação do armazenamento blob utilizando a ADF V2](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-v2-blob-to-sql-copy) | Este modelo de Gestor de Recursos Azure cria uma instância de Azure Data Factory V2 que copia dados do armazenamento de Azure Blob para a Base de Dados SQL.|
| [Cluster HDInsight com uma base de dados](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-with-sql-database) | Este modelo permite criar um cluster HDInsight, um servidor SQL lógico, uma base de dados e duas tabelas. Este modelo é utilizado pelo [Use Sqoop com Hadoop em artigo HDInsight](../../hdinsight/hadoop/hdinsight-use-sqoop.md). |
| [Azure Logic App que executa um Procedimento MLD armazenado em um horário](https://github.com/Azure/azure-quickstart-templates/tree/master/101-logic-app-sql-proc) | Este modelo permite criar uma aplicação lógica que executará um procedimento armazenado SQL no horário. Quaisquer argumentos para o procedimento podem ser colocados na secção do corpo do modelo.|

## <a name="azure-sql-managed-instance"></a>[Instância Gerida do SQL no Azure](#tab/managed-instance)

A tabela seguinte inclui links para modelos de Gestor de Recursos Azure para Azure SQL Managed Instance.

|Ligação|Descrição|
|---|---|
| [SQL Managed Instance em um novo VNet](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sqlmi-new-vnet) | Este modelo de Gestor de Recursos Azure cria uma nova rede virtual Azure configurada e gerido na rede virtual. |
| [Ambiente de rede para sql caso gerido](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment) | Esta implementação criará uma rede virtual Azure configurada com duas sub-redes, uma que será dedicada às suas instâncias geridas e outra onde poderá colocar outros recursos (por exemplo, VMs, ambientes de Serviço de Aplicações, etc.). Este modelo criará um ambiente de networking devidamente configurado onde pode implementar casos geridos. |
| [Sql Caso gerido com ligação P2S](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sqlmi-new-vnet-w-point-to-site-vpn) | Esta implementação criará uma rede virtual Azure com duas sub-redes, `ManagedInstance` e `GatewaySubnet` . Sql Managed Instance será implantado na sub-rede ManagedInstance. Um gateway de rede virtual será criado na `GatewaySubnet` sub-rede e configurado para a ligação VPN ponto-a-local. |
| [SQL Caso gerido com uma máquina virtual](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sqlmi-new-vnet-w-jumpbox) | Esta implementação criará uma rede virtual Azure com duas sub-redes, `ManagedInstance` e `Management` . SQL Managed Instance será implantado na `ManagedInstance` sub-rede. Uma máquina virtual com a versão mais recente do SQL Server Management Studio (SSMS) será implantada na `Management` sub-rede. |

---