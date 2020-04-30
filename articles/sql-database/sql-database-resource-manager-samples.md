---
title: Modelos do Azure Resource Manager
description: Utilize modelos do Gestor de Recursos Azure para criar e configurar a Base de Dados Azure SQL.
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
ms.openlocfilehash: b254621cc414fb9b2b76263957adc80da6e9c22d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "79214010"
---
# <a name="azure-resource-manager-templates-for-azure-sql-database"></a>Modelos de Gestor de Recursos Azure para base de dados Azure SQL

Os modelos do Gestor de Recursos Azure permitem definir a sua infraestrutura como código e implementar as suas soluções para a nuvem Azure.

## <a name="single-database--elastic-pool"></a>[Única base de dados & piscina elástica](#tab/single-database)

A tabela seguinte inclui links para os modelos do Gestor de Recursos Azure para a Base de Dados Azure SQL.

| |  |
|---|---|
| [Base de dados única](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-database-transparent-encryption-create) | Este modelo de Gestor de Recursos Azure cria uma única Base de Dados Azure SQL com servidor lógico e configura regras de firewall. |
| [Servidor lógico](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-logical-server) | Este modelo de Gestor de Recursos Azure cria um servidor lógico para a Base de Dados SQL azure. |
| [Conjunto elástico](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-elastic-pool-create) | Este modelo permite-lhe implementar uma nova piscina Elástica com o seu novo Servidor SQL associado e novas Bases de Dados SQL para lhe atribuir. |
| [Grupos failover](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-with-failover-group) | Este modelo cria dois servidores lógicos Azure SQL, uma base de dados SQL e um grupo failover.|
| [Deteção de Ameaças](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-threat-detection-db-policy-multiple-databases) | Este modelo permite-lhe implementar um servidor lógico Azure SQL e um conjunto de Bases de Dados Azure SQL com Deteção de Ameaças ativadas, com um endereço de e-mail para alertas para cada base de dados. A Deteção de Ameaças faz parte da oferta de Proteção de Ameaças Avançadas (ATP) da SQL e fornece uma camada de segurança que responde a potenciais ameaças sobre servidores e bases de dados SQL.|
| [Auditoria ao Armazenamento de Blob Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-blob-storage) | Este modelo permite-lhe implementar um servidor lógico Azure SQL com Auditoria habilitado a escrever registos de auditoria para um armazenamento de blob. A auditoria da Base de Dados Azure SQL rastreia os eventos da base de dados e escreve-os num registo de auditoria que pode ser colocado na sua conta de armazenamento Azure, espaço de trabalho OMS ou Hubs de Eventos.|
| [Auditoria ao Azure Event Hub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-eventhub) | Este modelo permite-lhe implementar um servidor Azure SQL com Auditoria habilitado a escrever registos de auditoria para um Hub de Eventos de saída. Para enviar eventos de auditoria para o `Enabled` `State` Event `IsAzureMonitorTargetEnabled` Hub, detete as definições de auditoria e desempor como `true`. Além disso, configure as Definições de Diagnóstico com `SQLSecurityAuditEvents` categoria de registo na base de `master` dados (para auditoria de nível de serviço). A auditoria da Base de Dados Azure SQL e do SQL Data Warehouse rastreia eventos de base de dados e escreve-os num registo de auditoria que pode ser colocado na sua conta de armazenamento Azure, espaço de trabalho OMS ou Hubs de Eventos.|
| [Aplicação Web Azure com Base de Dados SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database) | Esta amostra cria uma Aplicação Web Azure gratuita e uma base de dados SQL gratuitas ao nível de serviço "Basic".|
| [Azure Web App e Redis Cache com Base de Dados SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-redis-cache-sql-database) | Este modelo cria uma Web App, Redis Cache e SQL Database no mesmo grupo de recursos, e cria duas cordas de ligação na Web App para a Base de Dados SQL e Redis Cache.|
| [Dados de importação de armazenamento de blob utilizando ADF V2](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-v2-blob-to-sql-copy) | Este modelo de Gestor de Recursos Azure cria a Azure Data Factory V2 que copia dados do Armazenamento De Blob Azure para a Base de Dados SQL.|
| [Cluster HDInsight com uma Base de Dados SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-with-sql-database) | Este modelo permite-lhe criar um cluster HDInsight, um servidor de base de dados SQL, uma Base de Dados SQL e duas tabelas. Este modelo é usado pelo [Use Sqoop com Hadoop no artigo HDInsight](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-use-sqoop) |
| [App Azure Logic que executa um Procedimento Armazenado SQL em um horário](https://github.com/Azure/azure-quickstart-templates/tree/master/101-logic-app-sql-proc) | Este modelo permite-lhe criar uma Aplicação Lógica que executará um procedimento armazenado por SQL no horário. Quaisquer argumentos para o procedimento podem ser colocados na secção do corpo do modelo.|

## <a name="managed-instance"></a>[Instância gerida](#tab/managed-instance)

A tabela seguinte inclui links para modelos de Gestor de Recursos Azure para base de dados Azure SQL - Instância Gerida.

| |  |
|---|---|
| [Instância gerida em um novo VNet](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sqlmi-new-vnet) | Este modelo de Gestor de Recursos Azure cria um novo Azure VNet configurado e Instância Gerida na VNet. |
| [Ambiente de rede para instância gerida](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment) | Esta implementação criará uma Rede Virtual Azure configurada com duas subredes - uma que será dedicada às suas Instâncias Geridas, e outra onde poderá colocar outros recursos (por exemplo VMs, ambientes de Serviço de Aplicações, etc.). Este modelo criará um ambiente de networking devidamente configurado onde pode implementar Instâncias Geridas. |
| [Instância gerida com ligação P2S](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sqlmi-new-vnet-w-point-to-site-vpn) | Esta implantação criará uma Rede Virtual `ManagedInstance` `GatewaySubnet`Azure com duas subredes e . A Instância Gerida será implantada na subnet ManagedInstance. O gateway de rede `GatewaySubnet` virtual será criado em subnet e configurado para a ligação VPN ponto-a-site. |
| [Instância gerida com máquina virtual](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sqlmi-new-vnet-w-jumpbox) | Esta implantação criará uma Rede Virtual `ManagedInstance` `Management`Azure com duas subredes e . A Instância Gerida será `ManagedInstance` implantada na sub-rede. A máquina virtual com a versão mais recente do SQL Server `Management` Management Studio (SSMS) será implantada na sub-rede. |

---
