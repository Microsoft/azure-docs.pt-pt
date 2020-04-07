---
title: Encriptação transparente de dados (T-SQL)
description: Encriptação transparente de dados (TDE) em Azure Synapse Analytics (T-SQL)
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
ms.openlocfilehash: ae751cc5b8e3ab67f3e65757724d0ebae1c45e02
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745244"
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

Siga estes passos para permitir o TDE:

1. Ligue-se à base de dados *principal* do servidor que acolhe a base de dados utilizando um login que é um administrador ou um membro do papel **de gestor** na base de dados principal
2. Execute a seguinte declaração para encriptar a base de dados.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

## <a name="disabling-encryption"></a>Encriptação incapacitante

Siga estes passos para desativar o TDE:

1. Ligue-se à base de dados *principal* utilizando um login que seja um administrador ou um membro do papel **de gestor na** base de dados principal
2. Execute a seguinte declaração para encriptar a base de dados.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION OFF;
```

> [!NOTE]
> Uma piscina SQL pausada deve ser retomada antes de efazer alterações nas definições do TDE.

## <a name="verifying-encryption"></a>Verificação da encriptação

Para verificar o estado da encriptação, siga os passos abaixo:

1. Ligue-se à base de dados *master* ou instância utilizando um login que seja um administrador ou um membro do papel **de gestor na** base de dados principal
2. Execute a seguinte declaração para encriptar a base de dados.

```sql
SELECT
    [name],
    [is_encrypted]
FROM
    sys.databases;
```

O resultado ```1``` indica uma base ```0``` de dados encriptada, indica uma base de dados não encriptada.

## <a name="encryption-dmvs"></a>DMVs de encriptação

* [bases de dados sys.](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.dm_pdw_nodes_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
