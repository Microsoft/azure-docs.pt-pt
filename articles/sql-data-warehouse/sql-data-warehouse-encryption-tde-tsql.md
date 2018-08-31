---
title: Encriptação de dados transparente no armazém de dados SQL (T-SQL) | Documentos da Microsoft
description: Encriptação de dados transparente (TDE) no armazém de dados SQL (T-SQL)
services: sql-data-warehouse
author: kavithaj
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: ccdba241a2921a59f7db9668ec2b6f0921aa9f44
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/30/2018
ms.locfileid: "43307692"
---
# <a name="get-started-with-transparent-data-encryption-tde"></a>Começar com encriptação de dados transparente (TDE)
> [!div class="op_single_selector"]
> * [Descrição geral da segurança](sql-data-warehouse-overview-manage-security.md)
> * [Autenticação](sql-data-warehouse-authentication.md)
> * [Encriptação (Portal)](sql-data-warehouse-encryption-tde.md)
> * [Encriptação (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

## <a name="required-permssions"></a>Permissões necessárias
Para ativar a encriptação de dados transparente (TDE), tem de ser um administrador ou membro da função dbmanager.

## <a name="enabling-encryption"></a>Ativar a encriptação
Siga estes passos para ativar a TDE para um SQL Data Warehouse:

1. Ligar para o *mestre* base de dados no servidor que aloja a base de dados com um início de sessão que é um administrador ou membro da **dbmanager** função na base de dados mestra
2. Execute a declaração seguinte para encriptar a base de dados.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

## <a name="disabling-encryption"></a>A desativação da encriptação
Siga estes passos para desativar a TDE para um SQL Data Warehouse:

1. Ligar para o *mestre* base de dados com um início de sessão que é um administrador ou membro da **dbmanager** função na base de dados mestra
2. Execute a declaração seguinte para encriptar a base de dados.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION OFF;
```

> [!NOTE]
> Deve ser retomado um SQL Data Warehouse em pausa antes de efetuar alterações às definições da TDE.
> 
> 

## <a name="verifying-encryption"></a>Verificar a encriptação
Para verificar o estado de encriptação para um SQL Data Warehouse, siga os passos abaixo:

1. Ligar para o *mestre* ou a base de dados de instância com um início de sessão que é um administrador ou membro da **dbmanager** função na base de dados mestra
2. Execute a declaração seguinte para encriptar a base de dados.

```sql
SELECT
    [name],
    [is_encrypted]
FROM
    sys.databases;
```

O resultado de ```1``` indica um banco de dados criptografado, ```0``` indica uma base de dados não encriptados.

## <a name="encryption-dmvs"></a>DMVs de encriptação
* [sys. Databases][sys.databases] 
* [sys.dm_pdw_nodes_database_encryption_keys][sys.dm_pdw_nodes_database_encryption_keys]

<!--Anchors-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx
[sys.databases]: http://msdn.microsoft.com/library/ms178534.aspx  
[sys.dm_pdw_nodes_database_encryption_keys]: https://msdn.microsoft.com/library/mt203922.aspx  

<!--Image references-->

<!--Link references-->
