---
title: Ativar encriptação transparente de dados para base de dados de alongamento (T-SQL)
description: Ativar encriptação transparente de dados (TDE) para base de dados de alongamento do servidor SQL em Azure TSQL
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "74033959"
---
# <a name="enable-transparent-data-encryption-tde-for-stretch-database-on-azure-transact-sql"></a>Ativar encriptação transparente de dados (TDE) para base de dados de alongamento seletiva em Azure (Transact-SQL)
> [!div class="op_single_selector"]
> * [Portal do Azure](sql-server-stretch-database-encryption-tde.md)
> * [Rio TSQL](sql-server-stretch-database-tde-tsql.md)
>
>

A Encriptação transparente de dados (TDE) ajuda a proteger contra a ameaça de atividades maliciosas através da encriptação e desencriptação em tempo real da base de dados, cópias de segurança associadas e ficheiros de registo de transações em repouso sem exigir alterações na aplicação.

A Encriptação de Dados Transparente encripta o armazenamento de uma base de dados completa ao utilizar uma chave simétrica chamada chave de encriptação de base de dados. A chave de encriptação da base de dados está protegida por um certificado de servidor incorporado. O certificado de servidor incorporado é único para cada servidor Azure. A Microsoft roda automaticamente estes certificados pelo menos a cada 90 dias. Para obter uma descrição geral do TDE, consulte a [Encriptação transparente de dados (TDE)].

## <a name="enabling-encryption"></a>Ativar a encriptação
Para ativar o TDE para uma base de dados Azure que armazene os dados migrados de uma base de dados do SQL Server ativada por extensão, faça as seguintes coisas:

1. Ligue-se à base de dados *principal* do servidor Azure que acolhe a base de dados utilizando um login que é um administrador ou um membro do papel **de gestor** na base de dados principal
2. Execute a seguinte declaração para encriptar a base de dados.

```sql
ALTER DATABASE [database_name] SET ENCRYPTION ON;
```

## <a name="disabling-encryption"></a>Encriptação incapacitante
Para desativar o TDE para uma base de dados Azure que armazene os dados migrados de uma base de dados do SQL Server ativado por extensão, faça as seguintes coisas:

1. Ligue-se à base de dados *principal* utilizando um login que seja um administrador ou um membro do papel **de gestor na** base de dados principal
2. Execute a seguinte declaração para encriptar a base de dados.

```sql
ALTER DATABASE [database_name] SET ENCRYPTION OFF;
```

## <a name="verifying-encryption"></a>Verificação da encriptação
Para verificar o estado de encriptação de uma base de dados Azure que armazena os dados migrados de uma base de dados do SQL Server ativado por extensão, faça as seguintes coisas:

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

<!--Anchors-->
[Encriptação de Dados Transparente (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->

<!--Link references-->
