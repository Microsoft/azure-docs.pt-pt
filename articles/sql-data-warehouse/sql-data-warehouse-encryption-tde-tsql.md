---
title: Encriptação transparente de dados (T-SQL)
description: Encriptação transparente de dados (TDE) em Azure Synapse Analytics (T-SQL)
services: sql-data-warehouse
author: julieMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: security
ms.date: 04/30/2019
ms.author: jrasnick
ms.reviewer: rortloff
ms.custom: seo-lt-2019
ms.openlocfilehash: bd6f40b8389284c1932a2f16a70060cd56e412fb
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78195810"
---
# <a name="get-started-with-transparent-data-encryption-tde"></a>Começar com encriptação de dados transparentes (TDE)
> [!div class="op_single_selector"]
> * [Visão geral de segurança](sql-data-warehouse-overview-manage-security.md)
> * [Autenticação](sql-data-warehouse-authentication.md)
> * [Encriptação (Portal)](sql-data-warehouse-encryption-tde.md)
> * [Encriptação (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

## <a name="required-permissions"></a>Permissões necessárias
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
> 
> 

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

Um resultado de ```1``` indica uma base de dados encriptada, ```0``` indica uma base de dados não encriptada.

## <a name="encryption-dmvs"></a>DMVs de encriptação
* [bases de dados sys.][sys.databases] 
* [sys.dm_pdw_nodes_database_encryption_keys][sys.dm_pdw_nodes_database_encryption_keys]

<!--Anchors-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx
[sys.databases]: https://msdn.microsoft.com/library/ms178534.aspx  
[sys.dm_pdw_nodes_database_encryption_keys]: https://msdn.microsoft.com/library/mt203922.aspx  

<!--Image references-->

<!--Link references-->
