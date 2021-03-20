---
title: Sincronizar dados do Azure SQL Edge utilizando o SQL Data Sync
description: Saiba sobre a sincronização de dados a partir do Azure SQL Edge utilizando o Azure SQL Data Sync
keywords: SQL Edge,sync dados de SQL Edge, SQL Edge sincronização de dados
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 5659ce5fa6f99463f58a33662563d768248fd8cb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93394905"
---
# <a name="tutorial-sync-data-from-sql-edge-to-azure-sql-database-by-using-sql-data-sync"></a>Tutorial: Sincronizar dados da SQL Edge para a Azure SQL Database utilizando o SQL Data Sync

Neste tutorial, você aprenderá a usar um *grupo de sincronização* de dados Azure SQL para sincronizar gradualmente dados de Azure SQL Edge para Azure SQL Database. SQL Data Sync é um serviço construído na Base de Dados Azure SQL que permite sincronizar os dados que seleciona bi-direcionalmente através de várias bases de dados em instâncias Azure SQL Database e SQL Server. Para obter mais informações sobre o SQL Data Sync, consulte [o Azure SQL Data Sync](../azure-sql/database/sql-data-sync-data-sql-server-sql-database.md).

Uma vez que o SQL Edge é construído nas versões mais recentes do [SQL Server Database Engine](/sql/sql-server/sql-server-technical-documentation/), qualquer mecanismo de sincronização de dados aplicável a uma instância do SQL Server também pode ser usado para sincronizar dados de ou para uma instância SQL Edge que esteja a funcionar num dispositivo de borda.

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial requer um computador Windows configurado com o [Agente de Sincronização de Dados para Azure SQL Data Sync](../azure-sql/database/sql-data-sync-agent-overview.md).

## <a name="before-you-begin"></a>Antes de começar

* Criar uma base de dados na Base de Dados Azure SQL. Para obter informações sobre como criar uma base de dados utilizando o portal Azure, consulte [criar uma única base de dados na Base de Dados Azure SQL](../azure-sql/database/single-database-create-quickstart.md?tabs=azure-portal).

* Crie as tabelas e outros objetos necessários na sua implementação da Base de Dados Azure SQL.

* Crie as tabelas e objetos necessários na sua implantação Azure SQL Edge. Para obter mais informações, consulte [a utilização de pacotes DAC de base de dados SQL com sql edge](deploy-dacpac.md).

* Registe a instância Azure SQL Edge com o Data Sync Agent for Azure SQL Data Sync. Para obter mais informações, consulte [uma base de dados do SQL Server](../azure-sql/database/sql-data-sync-sql-server-configure.md#add-on-prem).

## <a name="sync-data-between-a-database-in-azure-sql-database-and-sql-edge"></a>Sincronizar dados entre uma base de dados na Base de Dados Azure SQL e SQL Edge

A configuração da sincronização entre uma base de dados na Base de Dados Azure SQL e uma instância SQL Edge através da utilização do SQL Data Sync envolve três etapas-chave:  


1. Utilize o portal Azure para criar um grupo de sincronização. Para obter mais informações, consulte [Criar um grupo de sincronização.](../azure-sql/database/sql-data-sync-sql-server-configure.md#create-sync-group) Pode utilizar uma base de dados de um único *hub* para criar vários grupos de sincronização para sincronizar dados de várias instâncias SQL Edge para uma ou mais bases de dados na Base de Dados Azure SQL. 

2. Adicione membros sincronizados ao grupo de sincronização. Para obter mais informações, consulte [adicionar membros sincronizados](../azure-sql/database/sql-data-sync-sql-server-configure.md#add-sync-members).

3. Configurar o grupo de sincronização para selecionar as tabelas que farão parte da sincronização. Para obter mais informações, consulte [configurar um grupo de sincronização.](../azure-sql/database/sql-data-sync-sql-server-configure.md#add-sync-members)

Depois de completar os passos anteriores, terá um grupo de sincronização que inclui uma base de dados na Base de Dados Azure SQL e uma instância SQL Edge.

Para obter mais informações sobre o SQL Data Sync, consulte estes artigos:

* [Agente de sincronização de dados para Azure SQL Data Sync](../azure-sql/database/sql-data-sync-agent-overview.md)

* [Boas práticas](../azure-sql/database/sql-data-sync-best-practices.md) e [como resolver problemas com o Azure SQL Data Sync](../azure-sql/database/sql-data-sync-troubleshoot.md)

* [Monitor SQL Data Sync com registos do Monitor Azure](../azure-sql/database/monitor-tune-overview.md)

* [Atualizar o esquema de sincronização com Transact-SQL](../azure-sql/database/sql-data-sync-update-sync-schema.md) ou [PowerShell](../azure-sql/database/scripts/update-sync-schema-in-sync-group.md)

## <a name="next-steps"></a>Passos seguintes


* [Utilize o PowerShell para sincronizar entre a Base de Dados Azure SQL e a Borda Azure SQL](../azure-sql/database/scripts/sql-data-sync-sync-data-between-azure-onprem.md). Neste tutorial, substitua os detalhes da `OnPremiseServer` base de dados pelos detalhes do Azure SQL Edge.