---
title: Sincronizar dados do Azure SQL Database Edge usando o Sincronização de Dados SQL | Microsoft Docs
description: Saiba mais sobre a sincronização de dados entre o Azure SQL Database Edge com o Azure Sincronização de Dados SQL
keywords: borda do banco de dados SQL, sincronização de dados da borda do banco de dados SQL, sincronização de dado do SQL Database Edge
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: tutorial
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 857cee30ac4c1002fb7ca57d6be5fa461a14e9ee
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73501317"
---
# <a name="tutorial-sync-data-from-sql-database-edge-to-azure-sql-database-using-sql-data-sync"></a>Tutorial: sincronizar dados do SQL Database Edge para o Azure SQL usando Sincronização de Dados SQL

Neste tutorial, você aprenderá a usar Sincronização de Dados SQL grupo de *sincronização* para sincronizar de forma incremental dados do Azure SQL Database Edge para o banco de dado SQL do Azure. O Sincronização de Dados SQL é um serviço criado no banco de dados SQL do Azure que permite sincronizar o bi-Directional em vários bancos de dado SQL e SQL Server instâncias. Para obter mais informações sobre Sincronização de Dados SQL do Azure, consulte [sincronização de dados SQL do Azure](../sql-database/sql-database-sync-data.md).

Como o Azure SQL Database Edge é criado nas versões mais recentes do [mecanismo de banco de dados do Microsoft SQL Server](/sql/sql-server/sql-server-technical-documentation/), qualquer mecanismo de sincronização de dados aplicável a uma instância de SQL Server local também pode ser usado para sincronizar dados de ou para uma borda de banco de dado SQL instância em execução em um dispositivo de borda.

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial requer um computador Windows configurado com o [agente de sincronização de dados SQL do Azure](../sql-database/sql-database-data-sync-agent.md).

## <a name="before-you-begin"></a>Antes de começar

* Crie um banco de dados SQL do Azure. Para obter informações sobre como criar um banco de dados SQL do Azure usando portal do Azure, consulte [criar um banco de dados individual no banco de dados SQL do Azure](../sql-database/sql-database-single-database-get-started.md?tabs=azure-portal).

* Crie as tabelas e outros objetos necessários na implantação do banco de dados SQL do Azure.

* Crie as tabelas e os objetos necessários na implantação de borda do banco de dados SQL do Azure. Para obter mais informações, consulte [usando pacotes de DAC do banco de dados SQL com borda do banco de dados SQL](stream-analytics.md).

* Registre a instância de borda do banco de dados SQL do Azure com o agente de Sincronização de Dados SQL do Azure. Para obter mais informações, consulte [Adicionar um banco de dados SQL Server local](../sql-database/sql-database-get-started-sql-data-sync.md#add-on-prem).

## <a name="sync-data-between-an-azure-sql-database-and-sql-database-edge"></a>Sincronizar dados entre um banco de dados SQL do Azure e a borda do banco de dados SQL

Configurar a sincronização entre um banco de dados SQL do Azure e uma instância de borda do banco de dados SQL usando Sincronização de Dados SQL envolve três etapas principais.  

1. Use portal do Azure para criar um grupo de sincronização. Para criar o grupo de sincronização, consulte [Criar grupo de sincronização usando portal do Azure](../sql-database/sql-database-get-started-sql-data-sync.md#create-sync-group). O mesmo banco de dados de *Hub* pode ser usado para criar vários grupos de sincronização diferentes para sincronizar dados de instâncias diferentes do banco de dados SQL para um ou mais bancos de dado SQL no Azure.

2. Adicione membros de sincronização ao grupo de sincronização. Para adicionar membros ao grupo de sincronização, consulte [adicionar membros ao grupo de sincronização de dados SQL](../sql-database/sql-database-get-started-sql-data-sync.md#add-sync-members).

3. Configure o grupo de sincronização para selecionar as tabelas que serão parte dessa sincronização. Para configurar o grupo de sincronização, consulte [Configurar grupo de sincronização](../sql-database/sql-database-get-started-sql-data-sync.md#add-sync-members).

Depois de concluir as etapas acima, você terá um grupo de sincronização que inclui um banco de dados SQL do Azure e uma instância de borda do banco de dados SQL.

Para obter mais informações sobre Sincronização de Dados SQL, veja os seguintes artigos:

* [Agente de sincronização de dados para Sincronização de Dados SQL do Azure](../sql-database/sql-database-data-sync-agent.md)

* [Práticas recomendadas](../sql-database/sql-database-best-practices-data-sync.md) e [como solucionar problemas com o Azure sincronização de dados SQL](../sql-database/sql-database-troubleshoot-data-sync.md)

* [Monitorar Sincronização de Dados SQL com logs de Azure Monitor](../sql-database/sql-database-sync-monitor-oms.md)

* [Atualizar o esquema de sincronização com o Transact-SQL](../sql-database/sql-database-update-sync-schema.md) ou o [PowerShell](../sql-database/scripts/sql-database-sync-update-schema.md)

## <a name="next-steps"></a>Passos seguintes

* [Use o PowerShell para sincronizar entre o banco de dados SQL do Azure e a borda do banco de dados SQL](../sql-database/scripts/sql-database-sync-data-between-azure-onprem.md). No tutorial, substitua os detalhes do banco de dados *OnPremiseServer* com os detalhes de borda do banco de dados SQL do Azure.
