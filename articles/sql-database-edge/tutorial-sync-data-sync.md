---
title: Sync dados do Azure SQL Database Edge utilizando o SQL Data Sync [ SQL Data Sync] Microsoft Docs
description: Saiba mais sobre sincronizar dados a partir do Azure SQL Database Edge utilizando o Azure SQL Data Sync
keywords: borda de base de dados sql,sync dados de borda de base de dados sql, sql database edge data sync
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: tutorial
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 7c38ba6dbabef4affd8672295a93d46fd4b0e494
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "74384173"
---
# <a name="tutorial-sync-data-from-sql-database-edge-to-azure-sql-database-by-using-sql-data-sync"></a>Tutorial: Sync data from SQL Database Edge to Azure SQL Database, utilizando o SQL Data Sync

Neste tutorial, você aprenderá a usar um grupo de *sincronização* de dados Azure SQL para sincronizar gradualmente os dados de Azure SQL Database Edge para Azure SQL Database. SQL Data Sync é um serviço construído na Base de Dados Azure SQL que permite sincronizar os dados que seleciona bidirecionalmente em várias bases de dados SQL e instâncias do Servidor SQL. Para obter mais informações sobre o SQL Data Sync, consulte [o SQL Data Sync](../sql-database/sql-database-sync-data.md).

Uma vez que o SQL Database Edge foi construído nas versões mais recentes do Motor de Base de Dados do [Servidor SQL,](/sql/sql-server/sql-server-technical-documentation/)qualquer mecanismo de sincronização de dados aplicável a uma instância do SQL Server no local também pode ser usado para sincronizar dados de ou para uma instância de SQL Database Edge em execução num dispositivo de borda.

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial requer um computador Windows configurado com o Agente de Sincronização de Dados para O Sincronização de [Dados Azure SQL](../sql-database/sql-database-data-sync-agent.md).

## <a name="before-you-begin"></a>Antes de começar

* Crie uma Base de Dados SQL do Azure. Para obter informações sobre como criar uma base de dados Azure SQL utilizando o portal Azure, consulte Criar uma única base de dados na Base de [Dados Azure SQL](../sql-database/sql-database-single-database-get-started.md?tabs=azure-portal).

* Crie as tabelas e outros objetos necessários na sua implementação de Base de Dados Azure SQL.

* Crie as tabelas e objetos necessários na sua implementação de Borda de Base de Dados Azure SQL. Para mais informações, consulte utilizando pacotes DAC da Base de [Dados SQL com Borda](stream-analytics.md)de Base de Dados SQL .

* Registe a instância de Borda de Base de Dados Azure SQL com o Agente de Sincronização de Dados para O Sincronizado de Dados Azure SQL. Para mais informações, consulte Adicionar uma base de dados do [SQL Server no local](../sql-database/sql-database-get-started-sql-data-sync.md#add-on-prem).

## <a name="sync-data-between-an-azure-sql-database-and-sql-database-edge"></a>Sync dados entre uma base de dados Azure SQL e SQL Database Edge

A criação de sincronização entre uma base de dados Azure SQL e uma instância De base de dados SQL, utilizando o SQL Data Sync, envolve três passos-chave:  

1. Utilize o portal Azure para criar um grupo de sincronização. Para mais informações, consulte [Criar um grupo de sincronização](../sql-database/sql-database-get-started-sql-data-sync.md#create-sync-group). Pode utilizar uma única base de dados de *hub* para criar múltiplos grupos de sincronização para sincronizar dados de várias instâncias de SQL Database Edge para uma ou mais bases de dados SQL em Azure.

2. Adicione membros sincronizados ao grupo de sincronização. Para mais informações, consulte [Adicionar membros do sync](../sql-database/sql-database-get-started-sql-data-sync.md#add-sync-members).

3. Configurar o grupo de sincronização para selecionar as tabelas que farão parte da sincronização. Para mais informações, consulte [Configure um grupo de sincronização](../sql-database/sql-database-get-started-sql-data-sync.md#add-sync-members).

Depois de completar os passos anteriores, terá um grupo de sincronização que inclui uma base de dados Azure SQL e uma instância de Margem de Dados De Base de Dados SQL.

Para mais informações sobre o SQL Data Sync, consulte estes artigos:

* [Agente de sincronização de dados para sincronização de dados Azure SQL](../sql-database/sql-database-data-sync-agent.md)

* [Boas práticas](../sql-database/sql-database-best-practices-data-sync.md) e [como resolver problemas com o Azure SQL Data Sync](../sql-database/sql-database-troubleshoot-data-sync.md)

* [Monitor SQL Data Sync com registos do Monitor Azure](../sql-database/sql-database-sync-monitor-oms.md)

* [Atualize o esquema de sincronização com transact-SQL](../sql-database/sql-database-update-sync-schema.md) ou [PowerShell](../sql-database/scripts/sql-database-sync-update-schema.md)

## <a name="next-steps"></a>Passos seguintes

* Utilize a PowerShell para sincronizar entre a Base de [Dados Azure SQL e a Borda](../sql-database/scripts/sql-database-sync-data-between-azure-onprem.md)da Base de Dados Azure SQL . Neste tutorial, substitua os dados da `OnPremiseServer` base de dados com os detalhes do Edge da Base de Dados Azure SQL.
