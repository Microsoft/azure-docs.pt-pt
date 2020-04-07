---
title: Encriptação transparente de dados (Portal)
description: Encriptação transparente de dados (TDE) em Azure Synapse Analytics
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/30/2019
ms.author: jrasnick
ms.reviewer: rortloff
ms.custom: seo-lt-2019
ms.openlocfilehash: 247691326e3aa2c8027dd0318b23a2cbfcba1efe
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745240"
---
# <a name="get-started-with-transparent-data-encryption-tde"></a>Começar com encriptação de dados transparentes (TDE)

> [!div class="op_single_selector"]
>
> * [Visão geral de segurança](sql-data-warehouse-overview-manage-security.md)
> * [Autenticação](sql-data-warehouse-authentication.md)
> * [Encriptação (Portal)](sql-data-warehouse-encryption-tde.md)
> * [Encriptação (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)

## <a name="required-permissions"></a>Permissões Obrigatórias

Para ativar a Encriptação transparente de dados (TDE), deve ser um administrador ou um membro do papel de dbmanager.

## <a name="enabling-encryption"></a>Ativar a encriptação

Para ativar o TDE, siga os passos abaixo:

1. Abra a base de dados no [portal Azure](https://portal.azure.com)
2. Na lâmina de base de dados, clique no botão **Definições**
3. Selecione as definições do portal de opção ![de encriptação de dados **transparentes**](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png)
4. Selecione ![as definições do portal de definição **em**](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-on.png)
5. Selecione **guardar**
   ![definições do portal](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save.png)  

## <a name="disabling-encryption"></a>Encriptação incapacitante

Para desativar o TDE, siga os passos abaixo:

1. Abra a base de dados no [portal Azure](https://portal.azure.com)
2. Na lâmina de base de dados, clique no botão **Definições**
3. Selecione as definições do portal de opção ![de encriptação de dados **transparentes**](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png)
4. Selecione ![as definições do portal de definição **Off**](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-off.png)
5. Selecione **guardar**
   ![a definição do portal 2](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save2.png)  

## <a name="encryption-dmvs"></a>DMVs de encriptação

A encriptação pode ser confirmada com os seguintes DMVs:

* [bases de dados sys.](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.dm_pdw_nodes_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
