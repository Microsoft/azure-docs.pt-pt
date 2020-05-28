---
title: Modelos do Azure Resource Manager
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Utilize modelos do Gestor de Recursos Azure para criar e configurar a Base de Dados Azure SQL e a Instância Gerida Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: overview-samples sqldbrb=2
ms.devlang: ''
ms.topic: sample
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 02/04/2019
ms.openlocfilehash: 0e3688fdbeabd525e54aad1b9e36c82b9c86751f
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84053292"
---
# <a name="azure-resource-manager-templates-for-azure-sql-database--sql-managed-instance"></a>Modelos de Gestor de Recursos Azure para Base de Dados Azure SQL & Instância Gerida SQL
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Os modelos do Gestor de Recursos Azure permitem definir a sua infraestrutura como código e implementar as suas soluções para a nuvem Azure para a sua Base de Dados Azure SQL e Instância Gerida Azure SQL.

## <a name="azure-sql-database"></a>[Base de Dados SQL do Azure](#tab/single-database)

A tabela seguinte inclui links para os modelos do Gestor de Recursos Azure para a Base de Dados Azure SQL.

| |  |
|---|---|
| [Base de Dados SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-database-transparent-encryption-create) | Este modelo do Gestor de Recursos Azure cria uma única base de dados na Base de Dados Azure SQL e configura as regras de firewall IP ao nível do servidor. |
| [Servidor](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-logical-server) | Este modelo de Gestor de Recursos Azure cria um servidor para base de dados Azure SQL. |
| [Conjunto elástico](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-elastic-pool-create) | Este modelo permite-lhe implantar uma piscina elástica e atribuir bases de dados. |
| [Grupos failover](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-with-failover-group) | Este modelo cria dois servidores, uma única base de dados e um grupo de failover na Base de Dados Azure SQL.|
| [Deteção de Ameaças](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-threat-detection-db-policy-multiple-databases) | Este modelo permite-lhe implementar um servidor e um conjunto de bases de dados com Deteção de Ameaças ativada, com um endereço de e-mail para alertas para cada base de dados. A Deteção de Ameaças faz parte da oferta de Proteção de Ameaças Avançadas (ATP) da SQL e fornece uma camada de segurança que responde a potenciais ameaças sobre servidores e bases de dados.|
| [Auditoria ao Armazenamento de Blob Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-blob-storage) | Este modelo permite-lhe implementar um servidor com auditoria habilitada a escrever registos de auditoria para um armazenamento de bolhas. A auditoria da Base de Dados Azure SQL rastreia os eventos da base de dados e escreve-os num registo de auditoria que pode ser colocado na sua conta de armazenamento Azure, espaço de trabalho OMS ou Hubs de Eventos.|
| [Auditoria ao Azure Event Hub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-eventhub) | Este modelo permite-lhe implementar um servidor com auditoria habilitada a escrever registos de auditoria para um Hub de Eventos existente. Para enviar eventos de auditoria para o Event Hub, detete as definições de auditoria `Enabled` `State` e desempor `IsAzureMonitorTargetEnabled` como `true` . Além disso, configure as Definições de Diagnóstico com `SQLSecurityAuditEvents` categoria de registo na base de dados (para auditoria de `master` nível de serviço). A auditoria rastreia os eventos da base de dados e escreve-os num registo de auditoria que pode ser colocado na sua conta de armazenamento Azure, espaço de trabalho OMS ou Hubs de Eventos.|
| [Aplicação Web Azure com Base de Dados SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database) | Esta amostra cria uma Web App Azure gratuita e uma base de dados na Base de Dados Azure SQL ao nível de serviço "Basic".|
| [Azure Web App e Redis Cache com Base de Dados SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-redis-cache-sql-database) | Este modelo cria uma Web App, Redis Cache e SQL Database no mesmo grupo de recursos, e cria duas cordas de ligação na Web App para a Base de Dados SQL e Redis Cache.|
| [Dados de importação de armazenamento de blob utilizando ADF V2](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-v2-blob-to-sql-copy) | Este modelo de Gestor de Recursos Azure cria a Azure Data Factory V2 que copia dados do Armazenamento De Blob Azure para a Base de Dados SQL.|
| [Cluster HDInsight com uma Base de Dados SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-with-sql-database) | Este modelo permite criar um cluster HDInsight, um servidor SQL lógico, uma base de dados e duas tabelas. Este modelo é usado pelo [Use Sqoop com Hadoop no artigo HDInsight](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-use-sqoop) |
| [App Azure Logic que executa um Procedimento Armazenado SQL em um horário](https://github.com/Azure/azure-quickstart-templates/tree/master/101-logic-app-sql-proc) | Este modelo permite-lhe criar uma Aplicação Lógica que executará um procedimento armazenado por SQL no horário. Quaisquer argumentos para o procedimento podem ser colocados na secção do corpo do modelo.|

## <a name="azure-sql-managed-instance"></a>[Instância Gerida do Azure SQL](#tab/managed-instance)

A tabela seguinte inclui links para os modelos do Gestor de Recursos Azure para a Instância Gerida Azure SQL.

| |  |
|---|---|
| [SQL Managed Instance em um novo VNet](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sqlmi-new-vnet) | Este modelo de Gestor de Recursos Azure cria um novo Azure VNet configurado e SQL Managed Instance no VNet. |
| [Ambiente de rede para Instância Gerida SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment) | Esta implementação criará uma Rede Virtual Azure configurada com duas subredes - uma que será dedicada às suas Instâncias Geridas SQL, e outra onde poderá colocar outros recursos (por exemplo VMs, ambientes de App Service, etc.). Este modelo criará um ambiente de rede devidamente configurado, onde pode implementar instâncias geridas pelo SQL. |
| [Instância gerida SQL com ligação P2S](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sqlmi-new-vnet-w-point-to-site-vpn) | Esta implantação criará uma Rede Virtual Azure com duas subredes `ManagedInstance` e `GatewaySubnet` . A Instância Gerida sQL será implantada na subnet ManagedInstance. O gateway de rede virtual será criado em `GatewaySubnet` subnet e configurado para a ligação VPN ponto-a-site. |
| [Instância gerida sQL com máquina virtual](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sqlmi-new-vnet-w-jumpbox) | Esta implantação criará uma Rede Virtual Azure com duas subredes `ManagedInstance` e `Management` . A Instância Gerida sQL será implantada na `ManagedInstance` sub-rede. A máquina virtual com a versão mais recente do SQL Server Management Studio (SSMS) será implantada na `Management` sub-rede. |

---
