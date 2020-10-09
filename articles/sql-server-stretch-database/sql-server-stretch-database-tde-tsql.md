---
title: Ativar encriptação de dados transparentes para base de dados de alongamento (T-SQL)
description: Ativar encriptação de dados transparentes (TDE) para base de dados de alongamento de servidor SQL em Azure TSQL
services: sql-server-stretch-database
documentationcenter: ''
ms.assetid: 27753d91-9ca2-4d47-b34d-b5e2c2f029bb
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/23/2017
author: blazem-msft
ms.author: blazem
ms.reviewer: jroth
manager: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 6f1f5f55348069dbfe11b4d5857d93f8ba8c9b19
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "74033959"
---
# <a name="enable-transparent-data-encryption-tde-for-stretch-database-on-azure-transact-sql"></a>Ativar encriptação de dados transparentes (TDE) para base de dados de alongamento em Azure (Transact-SQL)
> [!div class="op_single_selector"]
> * [Portal do Azure](sql-server-stretch-database-encryption-tde.md)
> * [TSQL](sql-server-stretch-database-tde-tsql.md)
>
>

A Encriptação de Dados Transparente (TDE) ajuda a proteger contra a ameaça de atividade maliciosa, realizando encriptação e desencriptação em tempo real da base de dados, cópias de segurança associadas e ficheiros de registo de transações em repouso sem exigir alterações à aplicação.

A TDE encripta o armazenamento de uma base de dados completa ao utilizar uma chave simétrica denominada chave de encriptação de base de dados. A chave de encriptação da base de dados está protegida por um certificado de servidor incorporado. O certificado de servidor incorporado é único para cada servidor Azure. A Microsoft roda automaticamente estes certificados pelo menos a cada 90 dias. Para obter uma descrição geral do TDE, consulte [a Encriptação de Dados Transparente (TDE)].

## <a name="enabling-encryption"></a>Habilitar a encriptação
Para ativar o TDE para uma base de dados Azure que está a armazenar os dados migrados de uma base de dados do SQL Server ativada por alongamentos, faça as seguintes coisas:

1. Conecte-se à base de dados *principal* no servidor Azure que hospeda a base de dados utilizando um login que seja um administrador ou um membro da função **dbmanager** na base de dados principal
2. Execute a seguinte declaração para encriptar a base de dados.

```sql
ALTER DATABASE [database_name] SET ENCRYPTION ON;
```

## <a name="disabling-encryption"></a>Encriptação incapacitante
Para desativar o TDE para uma base de dados Azure que está a armazenar os dados migrados de uma base de dados do SQL Server ativada por alongamentos, faça as seguintes coisas:

1. Ligue-se à base de dados *principal* usando um login que seja um administrador ou um membro da função **dbmanager** na base de dados principal
2. Execute a seguinte declaração para encriptar a base de dados.

```sql
ALTER DATABASE [database_name] SET ENCRYPTION OFF;
```

## <a name="verifying-encryption"></a>Verificação da encriptação
Para verificar o estado de encriptação de uma base de dados Azure que está a armazenar os dados migrados de uma base de dados do SQL Server ativada por alongamentos, faça as seguintes coisas:

1. Ligue-se à base de *dados principal* ou de instância usando um login que seja um administrador ou um membro da função **dbmanager** na base de dados principal
2. Execute a seguinte declaração para encriptar a base de dados.

```sql
SELECT
    [name],
    [is_encrypted]
FROM
    sys.databases;
```

O resultado indica uma base de ```1``` dados encriptada, ```0``` indica uma base de dados não encriptada.

<!--Anchors-->
[Encriptação de Dados Transparente (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->

<!--Link references-->
