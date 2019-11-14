---
title: Habilitar Transparent Data Encryption para Stretch Database (T-SQL)
description: Habilitar Transparent Data Encryption (TDE) para SQL Server Stretch Database no TSQL do Azure
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
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033959"
---
# <a name="enable-transparent-data-encryption-tde-for-stretch-database-on-azure-transact-sql"></a>Habilitar Transparent Data Encryption (TDE) para Stretch Database no Azure (Transact-SQL)
> [!div class="op_single_selector"]
> * [Portal do Azure](sql-server-stretch-database-encryption-tde.md)
> * [TSQL](sql-server-stretch-database-tde-tsql.md)
>
>

O Transparent Data Encryption (TDE) ajuda a proteger contra a ameaça de atividades mal-intencionadas, executando criptografia e descriptografia em tempo real do banco de dados, backups associados e arquivos de log de transações em repouso, sem a necessidade de alterações no aplicativo.

O TDE criptografa o armazenamento de um banco de dados inteiro usando uma chave simétrica chamada chave de criptografia do banco de dados. A chave de criptografia de banco de dados é protegida por um certificado de servidor interno. O certificado de servidor interno é exclusivo para cada servidor do Azure. A Microsoft gira automaticamente esses certificados pelo menos a cada 90 dias. Para obter uma descrição geral do TDE, consulte [Encriptação de Dados Transparente (TDE)].

## <a name="enabling-encryption"></a>Habilitando a criptografia
Para habilitar o TDE para um banco de dados do Azure que está armazenando o dado migrado de um banco de SQL Server habilitado para Stretch, faça o seguinte:

1. Conecte-se ao banco de dados *mestre* no servidor do Azure que hospeda o banco de dados usando um logon que seja um administrador ou membro da função **DBManager** no banco de dados mestre
2. Execute a instrução a seguir para criptografar o banco de dados.

```sql
ALTER DATABASE [database_name] SET ENCRYPTION ON;
```

## <a name="disabling-encryption"></a>Desabilitando a criptografia
Para desabilitar o TDE para um banco de dados do Azure que está armazenando o dado migrado de um banco de SQL Server habilitado para Stretch, faça o seguinte:

1. Conectar-se ao banco de dados *mestre* usando um logon que seja um administrador ou membro da função **DBManager** no banco de dados mestre
2. Execute a instrução a seguir para criptografar o banco de dados.

```sql
ALTER DATABASE [database_name] SET ENCRYPTION OFF;
```

## <a name="verifying-encryption"></a>Verificando a criptografia
Para verificar o status de criptografia de um banco de dados do Azure que está armazenando os dados migrados de um banco de SQL Server habilitado para Stretch, faça o seguinte:

1. Conecte-se ao banco de dados *mestre* ou de instância usando um logon que seja um administrador ou membro da função **DBManager** no banco de dados mestre
2. Execute a instrução a seguir para criptografar o banco de dados.

```sql
SELECT
    [name],
    [is_encrypted]
FROM
    sys.databases;
```

Um resultado de ```1``` indica um banco de dados criptografado, ```0``` indica um banco de dados não criptografado.

<!--Anchors-->
[Encriptação de Dados Transparente (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->

<!--Link references-->
