---
title: Encriptação de dados transparentes (Portal) para piscina SQL dedicada (anteriormente SQL DW)
description: Encriptação de dados transparentes (TDE) para piscina SQL dedicada (anteriormente SQL DW) em Azure Synapse Analytics
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/30/2019
ms.author: jrasnick
ms.reviewer: rortloff
ms.custom: seo-lt-2019
ms.openlocfilehash: da4be6f4bc8335e0976a0a4a87c4d232b2a2285f
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98676311"
---
# <a name="get-started-with-transparent-data-encryption-tde-for-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Começar com a Transparent Data Encryption (TDE) para piscina SQL dedicada (anteriormente SQL DW) em Azure Synapse Analytics

> [!div class="op_single_selector"]
>
> * [Visão geral da segurança](sql-data-warehouse-overview-manage-security.md)
> * [Autenticação](sql-data-warehouse-authentication.md)
> * [Encriptação (Portal)](sql-data-warehouse-encryption-tde.md)
> * [Encriptação (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)

## <a name="required-permissions"></a>Permissões Obrigatórias

Para ativar a Encriptação de Dados Transparente (TDE), tem de ser administrador ou membro da função dbmanager.

## <a name="enabling-encryption"></a>Habilitar a encriptação

Para ativar o TDE, siga os passos abaixo:

1. Abra a base de dados no [portal Azure](https://portal.azure.com)
2. Na lâmina da base de **dados,** clique no botão Definições
3. Selecione as definições do portal de opção de **encriptação** de ![ dados transparentes](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png)
4. Selecione as definições do portal **On** Setting ![](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-on.png)
5. **Selecione Guardar as** 
    ![ definições do portal](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save.png)  

## <a name="disabling-encryption"></a>Encriptação incapacitante

Para desativar o TDE, siga os passos abaixo:

1. Abra a base de dados no [portal Azure](https://portal.azure.com)
2. Na lâmina da base de **dados,** clique no botão Definições
3. Selecione as definições do portal de opção de **encriptação** de ![ dados transparentes](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png)
4. Selecione as definições do portal de definição **off** ![](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-off.png)
5. **Selecione Guardar** 
    ![ a definição do portal guardar 2](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save2.png)  

## <a name="encryption-dmvs"></a>DMVs de encriptação

A encriptação pode ser confirmada com os seguintes DMVs:

* [sys.databases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
* [sys.dm_pdw_nodes_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
