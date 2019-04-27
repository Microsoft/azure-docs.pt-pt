---
title: Ativar a encriptação de dados transparente para o Stretch Database TSQL - Azure | Documentos da Microsoft
description: Ativar a encriptação de dados transparente (TDE) para SQL Server Stretch Database, no TSQL do Azure
services: sql-server-stretch-database
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.assetid: 27753d91-9ca2-4d47-b34d-b5e2c2f029bb
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/23/2017
ms.author: douglasl
ms.openlocfilehash: 634e1b097f2ca16a8c52da02445b3f5f2af7ff3d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60708826"
---
# <a name="enable-transparent-data-encryption-tde-for-stretch-database-on-azure-transact-sql"></a>Ativar a encriptação de dados transparente (TDE) para Stretch Database no Azure (Transact-SQL)
> [!div class="op_single_selector"]
> * [Portal do Azure](sql-server-stretch-database-encryption-tde.md)
> * [TSQL](sql-server-stretch-database-tde-tsql.md)
>
>

Encriptação de dados transparente (TDE) ajuda a proteger contra ameaças de atividades maliciosas através de encriptação em tempo real e a desencriptação da base de dados, cópias de segurança associadas e ficheiros de registo de transação em repouso sem a necessidade de alterações à aplicação.

TDE criptografa o armazenamento de uma base de dados completa com uma chave simétrica denominada a chave de encriptação da base de dados. A chave de encriptação da base de dados está protegida por um certificado de servidor interno. O certificado de servidor interno é exclusivo para cada servidor do Azure. Microsoft gira automaticamente estes certificados, pelo menos a cada 90 dias. Para obter uma descrição geral de TDE, consulte [encriptação de dados transparente (TDE)].

## <a name="enabling-encryption"></a>Ativar a encriptação
Para ativar a TDE para um Azure, base de dados que está armazenando os dados migrados de uma base de dados do SQL Server com a Stretch ativada, efetue os seguintes procedimentos:

1. Ligar para o *mestre* base de dados no servidor do Azure que aloja a base de dados com um início de sessão que é um administrador ou membro da **dbmanager** função na base de dados mestra
2. Execute a declaração seguinte para encriptar a base de dados.

```sql
ALTER DATABASE [database_name] SET ENCRYPTION ON;
```

## <a name="disabling-encryption"></a>A desativação da encriptação
Para desativar a TDE para um Azure base de dados que está armazenando os dados migrados de uma base de dados do SQL Server com a Stretch ativada, efetue os seguintes procedimentos:

1. Ligar para o *mestre* base de dados com um início de sessão que é um administrador ou membro da **dbmanager** função na base de dados mestra
2. Execute a declaração seguinte para encriptar a base de dados.

```sql
ALTER DATABASE [database_name] SET ENCRYPTION OFF;
```

## <a name="verifying-encryption"></a>Verificar a encriptação
Para verificar o estado de encriptação para uma base de dados do Azure que está armazenando os dados migrados de uma base de dados do SQL Server com a Stretch ativada, efetue os seguintes procedimentos:

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

<!--Anchors-->
[Encriptação de Dados Transparente (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->

<!--Link references-->
