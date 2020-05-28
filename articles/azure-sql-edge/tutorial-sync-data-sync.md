---
title: Sync dados do Azure SQL Edge (Pré-visualização) utilizando o SQL Data Sync
description: Saiba mais sobre sincronizar dados a partir de Azure SQL Edge (Pré-visualização) utilizando o Azure SQL Data Sync
keywords: SQL Edge,sync dados do SQL Edge, ssync de dados SQL Edge
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: tutorial
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: b269c42258a813f7c08e1c3e9f2065b8b5255ed9
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84018281"
---
# <a name="tutorial-sync-data-from-sql-edge-to-azure-sql-database-by-using-sql-data-sync"></a>Tutorial: Sync data from SQL Edge to Azure SQL Database utilizando SQL Data Sync


Neste tutorial, você aprenderá a usar um grupo de *sincronização* de dados Azure SQL para sincronizar gradualmente os dados de Azure SQL Edge para Azure SQL Database. O SQL Data Sync é um serviço construído na Base de Dados Azure SQL que permite sincronizar os dados que seleciona bidirecionalmente em várias bases de dados em casos de Base de Dados SQL EQL Azure e SQL Server. Para obter mais informações sobre o SQL Data Sync, consulte [o SQL Data Sync](../azure-sql/database/sql-data-sync-data-sql-server-sql-database.md).

Uma vez que o SQL Edge é construído nas versões mais recentes do Motor de Base de Dados do [Servidor SQL,](/sql/sql-server/sql-server-technical-documentation/)qualquer mecanismo de sincronização de dados aplicável a uma instância do SQL Server no local também pode ser usado para sincronizar dados de ou para uma instância SQL Edge em execução num dispositivo de borda.

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial requer um computador Windows configurado com o Agente de Sincronização de Dados para O Sincronização de [Dados Azure SQL](../azure-sql/database/sql-data-sync-agent-overview.md).

## <a name="before-you-begin"></a>Antes de começar

* Crie uma Base de Dados SQL do Azure. Para obter informações sobre como criar uma base de dados Azure SQL utilizando o portal Azure, consulte Criar uma única base de dados na Base de [Dados Azure SQL](../azure-sql/database/single-database-create-quickstart.md?tabs=azure-portal).

* Crie as tabelas e outros objetos necessários na sua implementação de Base de Dados Azure SQL.

* Crie as tabelas e objetos necessários na sua implantação Azure SQL Edge. Para mais informações, consulte utilizando pacotes DAC da Base de [Dados SQL com SQL Edge](deploy-dacpac.md).

* Registe a instância Azure SQL Edge com o Agente de Sincronização de Dados para O Sincronizador de Dados Azure SQL. Para mais informações, consulte Adicionar uma base de dados do [SQL Server no local](../azure-sql/database/sql-data-sync-sql-server-configure.md#add-on-prem).

## <a name="sync-data-between-an-azure-sql-database-and-sql-edge"></a>Sync dados entre uma base de dados Azure SQL e SQL Edge

A criação de sincronização entre uma base de dados Azure SQL e uma instância SQL Edge utilizando o SQL Data Sync envolve três passos-chave:  


1. Utilize o portal Azure para criar um grupo de sincronização. Para mais informações, consulte [Criar um grupo de sincronização](../azure-sql/database/sql-data-sync-sql-server-configure.md#create-sync-group). Pode utilizar uma única base de dados de *hub* para criar múltiplos grupos de sincronização para sincronizar dados de várias instâncias do SQL Edge para uma ou mais bases de dados na Base de Dados Azure SQL. 

2. Adicione membros sincronizados ao grupo de sincronização. Para mais informações, consulte [Adicionar membros do sync](../azure-sql/database/sql-data-sync-sql-server-configure.md#add-sync-members).

3. Configurar o grupo de sincronização para selecionar as tabelas que farão parte da sincronização. Para mais informações, consulte [Configure um grupo de sincronização](../azure-sql/database/sql-data-sync-sql-server-configure.md#add-sync-members).

Depois de completar os passos anteriores, terá um grupo de sincronização que inclui uma base de dados Azure SQL e uma instância SQL Edge.

Para mais informações sobre o SQL Data Sync, consulte estes artigos:

* [Agente de sincronização de dados para sincronização de dados Azure SQL](../azure-sql/database/sql-data-sync-agent-overview.md)

* [Boas práticas](../azure-sql/database/sql-data-sync-best-practices.md) e [como resolver problemas com o Azure SQL Data Sync](../azure-sql/database/sql-data-sync-troubleshoot.md)

* [Monitor SQL Data Sync com registos do Monitor Azure](../azure-sql/database/sql-data-sync-monitor-sync.md)

* [Atualize o esquema de sincronização com transact-SQL](../azure-sql/database/sql-data-sync-update-sync-schema.md) ou [PowerShell](../azure-sql/database/scripts/update-sync-schema-in-sync-group.md)

## <a name="next-steps"></a>Próximos passos


* Utilize a PowerShell para sincronizar entre a Base de [Dados Azure SQL e a Borda SQL Azure](../azure-sql/database/scripts/sql-data-sync-sync-data-between-azure-onprem.md). Neste tutorial, substitua os dados da base de `OnPremiseServer` dados com os detalhes do Azure SQL Edge.
