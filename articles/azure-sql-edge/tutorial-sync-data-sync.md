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
ms.openlocfilehash: 7971681c3f0c99a11567e6a30e61167c5d42348c
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83680513"
---
# <a name="tutorial-sync-data-from-sql-edge-to-azure-sql-database-by-using-sql-data-sync"></a>Tutorial: Sync data from SQL Edge to Azure SQL Database utilizando SQL Data Sync

Neste tutorial, você aprenderá a usar um grupo de *sincronização* de dados Azure SQL para sincronizar gradualmente os dados de Azure SQL Edge para Azure SQL Database. SQL Data Sync é um serviço construído na Base de Dados Azure SQL que permite sincronizar os dados que seleciona bidirecionalmente em várias bases de dados SQL e instâncias do Servidor SQL. Para obter mais informações sobre o SQL Data Sync, consulte [o SQL Data Sync](../sql-database/sql-database-sync-data.md).

Uma vez que o SQL Edge é construído nas versões mais recentes do Motor de Base de Dados do [Servidor SQL,](/sql/sql-server/sql-server-technical-documentation/)qualquer mecanismo de sincronização de dados aplicável a uma instância do SQL Server no local também pode ser usado para sincronizar dados de ou para uma instância SQL Edge em execução num dispositivo de borda.

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial requer um computador Windows configurado com o Agente de Sincronização de Dados para O Sincronização de [Dados Azure SQL](../sql-database/sql-database-data-sync-agent.md).

## <a name="before-you-begin"></a>Antes de começar

* Crie uma Base de Dados SQL do Azure. Para obter informações sobre como criar uma base de dados Azure SQL utilizando o portal Azure, consulte Criar uma única base de dados na Base de [Dados Azure SQL](../sql-database/sql-database-single-database-get-started.md?tabs=azure-portal).

* Crie as tabelas e outros objetos necessários na sua implementação de Base de Dados Azure SQL.

* Crie as tabelas e objetos necessários na sua implantação Azure SQL Edge. Para mais informações, consulte utilizando pacotes DAC da Base de [Dados SQL com SQL Edge](deploy-dacpac.md).

* Registe a instância Azure SQL Edge com o Agente de Sincronização de Dados para O Sincronizador de Dados Azure SQL. Para mais informações, consulte Adicionar uma base de dados do [SQL Server no local](../sql-database/sql-database-get-started-sql-data-sync.md#add-on-prem).

## <a name="sync-data-between-an-azure-sql-database-and-sql-edge"></a>Sync dados entre uma base de dados Azure SQL e SQL Edge

A criação de sincronização entre uma base de dados Azure SQL e uma instância SQL Edge utilizando o SQL Data Sync envolve três passos-chave:  

1. Utilize o portal Azure para criar um grupo de sincronização. Para mais informações, consulte [Criar um grupo de sincronização](../sql-database/sql-database-get-started-sql-data-sync.md#create-sync-group). Pode utilizar uma única base de dados de *hub* para criar múltiplos grupos de sincronização para sincronizar dados de várias instâncias do SQL Edge para uma ou mais bases de dados SQL em Azure.

2. Adicione membros sincronizados ao grupo de sincronização. Para mais informações, consulte [Adicionar membros do sync](../sql-database/sql-database-get-started-sql-data-sync.md#add-sync-members).

3. Configurar o grupo de sincronização para selecionar as tabelas que farão parte da sincronização. Para mais informações, consulte [Configure um grupo de sincronização](../sql-database/sql-database-get-started-sql-data-sync.md#add-sync-members).

Depois de completar os passos anteriores, terá um grupo de sincronização que inclui uma base de dados Azure SQL e uma instância SQL Edge.

Para mais informações sobre o SQL Data Sync, consulte estes artigos:

* [Agente de sincronização de dados para sincronização de dados Azure SQL](../sql-database/sql-database-data-sync-agent.md)

* [Boas práticas](../sql-database/sql-database-best-practices-data-sync.md) e [como resolver problemas com o Azure SQL Data Sync](../sql-database/sql-database-troubleshoot-data-sync.md)

* [Monitor SQL Data Sync com registos do Monitor Azure](../sql-database/sql-database-sync-monitor-oms.md)

* [Atualize o esquema de sincronização com transact-SQL](../sql-database/sql-database-update-sync-schema.md) ou [PowerShell](../sql-database/scripts/sql-database-sync-update-schema.md)

## <a name="next-steps"></a>Próximos passos

* Utilize a PowerShell para sincronizar entre a Base de [Dados Azure SQL e a Borda SQL Azure](../sql-database/scripts/sql-database-sync-data-between-azure-onprem.md). Neste tutorial, substitua os dados da base de `OnPremiseServer` dados com os detalhes do Azure SQL Edge.
