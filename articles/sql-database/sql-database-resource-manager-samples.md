---
title: Modelos do Azure Resource Manager para a base de dados SQL | Documentos da Microsoft
description: Utilize modelos Azure Resource Manager para criar e configurar a base de dados do Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: overview-samples
ms.devlang: ''
ms.topic: sample
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
manager: craigg
ms.date: 02/04/2019
ms.openlocfilehash: b967dc872529ec8b045df81542eec4c555b17a6c
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66418446"
---
# <a name="azure-resource-manager-templates-for-azure-sql-database"></a>Modelos do Azure Resource Manager para a base de dados do Azure SQL

Modelos Azure Resource Manager permitem-lhe definir a infraestrutura como código e implementar suas soluções de nuvem do Azure.

## <a name="single-database--elastic-pool"></a>Base de dados individual e conjunto elástico

A tabela seguinte inclui ligações para modelos Azure Resource Manager para a base de dados do Azure SQL.

| |  |
|---|---|
| [Base de dados](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-database-transparent-encryption-create) | Este modelo do Azure Resource Manager cria uma base de dados de SQL do Azure única com o servidor lógico e configura regras de firewall. |
| [Servidor lógico](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-logical-server) | Este modelo do Azure Resource Manager cria um servidor lógico da base de dados do Azure SQL. |
| [Conjunto elástico](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-elastic-pool-create) | Este modelo permite-lhe implementar um novo conjunto elástico com o seu novo servidor SQL associado e novas bases de dados do SQL para atribuir a ele. |
| [Grupos de ativação pós-falha](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-with-failover-group) | Este modelo cria dois servidores lógicos de SQL do Azure, base de dados SQL e um grupo de ativação pós-falha.|
| [Deteção de ameaças](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-threat-detection-db-policy-multiple-databases) | Este modelo permite-lhe implementar um servidor lógico SQL do Azure e um conjunto de bases de dados do Azure SQL ativada, com um endereço de e-mail para alertas para cada base de dados de deteção de ameaças. Deteção de ameaças faz parte da oferta SQL Advanced Threat Protection (ATP) e fornece uma camada de segurança que responde a potenciais ameaças em servidores SQL e bancos de dados.|
| [Auditoria para o armazenamento de Blobs do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-blob-storage) | Este modelo permite-lhe implementar um servidor lógico SQL do Azure com auditoria ativada para escrever registos de auditoria para um armazenamento de Blobs. A auditoria da base de dados do Azure SQL regista os eventos de base de dados e escreve-as num registo de auditoria que pode ser colocado na sua conta de armazenamento do Azure, área de trabalho do OMS ou os Hubs de eventos.|
| [Auditoria para o Hub de eventos do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-eventhub) | Este modelo permite-lhe implementar um servidor SQL do Azure com auditoria ativada para escrever registos de auditoria para um Hub de eventos existente. Para enviar eventos de auditoria para o Hub de eventos, definir configurações com de auditoria `Enabled` `State` e defina `IsAzureMonitorTargetEnabled` como `true`. Além disso, configure as definições de diagnóstico com `SQLSecurityAuditEvents` categoria de registos de diagnóstico sobre o `master` base de dados (para servir ao nível de auditoria). Auditoria para a base de dados do Azure SQL e o armazém de dados do SQL regista os eventos de base de dados e escreve-as num registo de auditoria que pode ser colocado na sua conta de armazenamento do Azure, área de trabalho do OMS ou os Hubs de eventos.|
| [Aplicação Web do Azure com a base de dados SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database) | Este exemplo cria uma aplicação Web do Azure gratuita e a base de dados SQL no nível de serviço "Básico".|
| [Aplicação Web do Azure e Cache de Redis com a base de dados SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-redis-cache-sql-database) | Este modelo cria uma aplicação Web, o Cache de Redis e a base de dados SQL no mesmo grupo de recursos e cria duas cadeias de ligação na aplicação Web para a base de dados SQL e o Cache de Redis.|
| [Importar dados do armazenamento de Blobs com o ADF V2](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-v2-blob-to-sql-copy) | Este modelo do Azure Resource Manager cria o Azure Data Factory V2, que copia dados de armazenamento de Blobs do Azure para a base de dados SQL.|
| [Cluster do HDInsight com um banco de dados SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-with-sql-database) | Este modelo permite-lhe criar um cluster do HDInsight, um servidor de base de dados SQL, uma base de dados SQL e duas tabelas. Este modelo é utilizado pelo [utilização Sqoop com o Hadoop no HDInsight artigo](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-use-sqoop) |
| [Aplicação lógica do Azure que executa um procedimento armazenado de SQL com base numa agenda](https://github.com/Azure/azure-quickstart-templates/tree/master/101-logic-app-sql-proc) | Este modelo permite-lhe criar uma aplicação lógica que será executado um procedimento SQL armazenado numa agenda. Quaisquer argumentos para o procedimento é possível colocar a seção de corpo do modelo.|

## <a name="managed-instance"></a>Instância Gerida

A tabela seguinte inclui ligações para modelos do Azure Resource Manager para o Azure SQL Database - instância gerida.

| |  |
|---|---|
| [Instância numa nova VNet gerida](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sqlmi-new-vnet) | Este modelo do Azure Resource Manager cria uma nova VNet do Azure configurada e a instância gerida na VNet. |
| [Ambiente de rede para a instância gerida](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment) | Esta implementação irá criar uma rede Virtual do Azure configurada com duas sub-redes - uma que irá ser dedicada às instâncias de geridas e outra em que pode colocar a outros recursos (por exemplo VMs, ambientes de serviço de aplicações, etc.). Este modelo irá criar um ambiente de rede configurado corretamente para poder implementar instâncias geridas. |
| [Instância gerida com ligação P2S](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sqlmi-new-vnet-w-point-to-site-vpn) | Esta implementação irá criar uma rede Virtual do Azure com duas sub-redes `ManagedInstance` e `GatewaySubnet`. Instância gerida será implantada na sub-rede ManagedInstance. Gateway de rede virtual será criado na `GatewaySubnet` sub-rede e configurado para ligação VPN ponto a Site. |
| [Instância gerida com a Máquina Virtual](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sqlmi-new-vnet-w-jumpbox) | Esta implementação irá criar uma rede Virtual do Azure com duas sub-redes `ManagedInstance` e `Management`. Instância gerida será implementada na `ManagedInstance` sub-rede. Máquina virtual com a versão mais recente do SQL Server Management Studio (SSMS) será implementada na `Management` sub-rede. |
