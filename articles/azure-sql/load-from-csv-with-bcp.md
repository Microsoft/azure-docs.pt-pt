---
title: Carregar dados do ficheiro CSV numa base de dados (bcp)
description: Para um tamanho de dados de pequena dimensão, utilize o bcp para importar dados para a Base de Dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: 09ae46ec6455b6998bcf4da5648d2ceaef4d5b19
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2021
ms.locfileid: "105644802"
---
# <a name="load-data-from-csv-into-azure-sql-database-or-sql-managed-instance-flat-files"></a>Carregar dados do CSV para a Base de Dados SQL do Azure ou para a SqL (ficheiros planos)
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

Pode utilizar o utilitário da linha de comando do BCP para importar dados de um ficheiro CSV para a Base de Dados Azure SQL ou para a Azure SQL Gerenciada.

## <a name="before-you-begin"></a>Antes de começar

### <a name="prerequisites"></a>Pré-requisitos

Para completar os passos deste artigo, você precisa:

* Uma base de dados na Base de Dados Azure SQL
* Ter instalado o utilitário bcp de linha de comandos
* Ter instalado o utilitário sqlcmd de linha de comandos

Pode descarregar os utilitários bcp e sqlcmd da [Microsoft sqlcmd Documentation](/sql/tools/sqlcmd-utility?view=sql-server-ver15&preserve-view=true).

### <a name="data-in-ascii-or-utf-16-format"></a>Dados no formato ASCII ou UTF-16

Se estiver a experimentar este tutorial com os seus dados, estes têm de utilizar a codificação ASCII ou UTF-16, uma vez que o bcp não suporta UTF-8.

## <a name="1-create-a-destination-table"></a>1. Criar uma tabela de destino

Defina uma tabela na Base de Dados SQL como a tabela de destino. As colunas na tabela têm de corresponder aos dados em cada linha do ficheiro de dados.

Para criar uma tabela, abra uma linha de comandos e utilize sqlcmd.exe para executar o seguinte comando:

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    CREATE TABLE DimDate2
    (
        DateId INT NOT NULL,
        CalendarQuarter TINYINT NOT NULL,
        FiscalQuarter TINYINT NOT NULL
    )
    ;
"
```

## <a name="2-create-a-source-data-file"></a>2. Criar um ficheiro de dados de origem

Abra o Bloco de Notas e copie as seguintes linhas de dados para um novo ficheiro de texto e, em seguida, guarde este ficheiro no diretório temporário local, C:\Temp\DimDate2.txt. Estes dados estão no formato ASCII.

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

(Opcional) Para exportar os dados a partir de uma base de dados SQL Server, abra uma linha de comandos e execute o seguinte comando. Substitua TableName, ServerName, DatabaseName, Username e Password pelas suas informações.

```bcp
bcp <TableName> out C:\Temp\DimDate2_export.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <Password> -q -c -t ,
```

## <a name="3-load-the-data"></a>3. Carregue os dados

Para carregar os dados, abra uma linha de comandos e execute o seguinte comando, substituindo os valores para o Nome do Servidor, nome da Base de Dados, Nome de Utilizador e Palavra-passe pelas suas informações.

```bcp
bcp DimDate2 in C:\Temp\DimDate2.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <password> -q -c -t  ,
```

Utilize este comando para verificar se os dados foram carregados corretamente

```bcp
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "SELECT * FROM DimDate2 ORDER BY 1;"
```

Os resultados devem ter o seguinte aspeto:

| DateId | CalendarQuarter | FiscalQuarter |
| --- | --- | --- |
| 20150101 |1 |3 |
| 20150201 |1 |3 |
| 20150301 |1 |3 |
| 20150401 |2 |4 |
| 20150501 |2 |4 |
| 20150601 |2 |4 |
| 20150701 |3 |1 |
| 20150801 |3 |1 |
| 20150801 |3 |1 |
| 20151001 |4 |2 |
| 20151101 |4 |2 |
| 20151201 |4 |2 |

## <a name="next-steps"></a>Passos seguintes

Para migrar uma base de dados do SQL Server, veja [SQL Server database migration (Migração da base de dados do SQL Server)](database/migrate-to-database-from-sql-server.md).

<!--MSDN references-->
[bcp]: /sql/tools/bcp-utility
[CREATE TABLE syntax]: /sql/t-sql/statements/create-table-azure-sql-data-warehouse

<!--Other Web references-->
[Microsoft Download Center]: https://www.microsoft.com/download/details.aspx?id=36433
