---
title: Carregar dados do ficheiro CSV para a base de dados de SQL do Azure (bcp) | Documentos da Microsoft
description: Para um tamanho de dados de pequena dimensão, utilize o bcp para importar dados para a Base de Dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: WenJason
ms.author: v-jay
ms.reviewer: carlrab
manager: digimobile
origin.date: 01/25/2019
ms.date: 03/04/2019
ms.openlocfilehash: 8d0d81d9c140c15f2e60d3f6e3033a8b0b629228
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60702474"
---
# <a name="load-data-from-csv-into-azure-sql-database-flat-files"></a>Carregar dados de ficheiros CSV para a Base de Dados SQL do Azure (ficheiros simples)

Pode utilizar o utilitário da linha de comandos do bcp para importar dados a partir de um ficheiro CSV para uma Base de Dados SQL do Azure.

## <a name="before-you-begin"></a>Antes de começar

### <a name="prerequisites"></a>Pré-requisitos

Para concluir os passos neste artigo, precisa:

* Um servidor de base de dados do Azure SQL e a base de dados
* Ter instalado o utilitário bcp de linha de comandos
* Ter instalado o utilitário sqlcmd de linha de comandos

Pode transferir os utilitários bcp e sqlcmd a partir do [Centro de Transferências da Microsoft][Microsoft Download Center].

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

## <a name="3-load-the-data"></a>3. Carregar os dados

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

## <a name="next-steps"></a>Passos Seguintes

Para migrar uma base de dados do SQL Server, veja [SQL Server database migration (Migração da base de dados do SQL Server)](sql-database-single-database-migrate.md).

<!--MSDN references-->
[bcp]: https://msdn.microsoft.com/library/ms162802.aspx
[CREATE TABLE syntax]: https://msdn.microsoft.com/library/mt203953.aspx

<!--Other Web references-->
[Microsoft Download Center]: https://www.microsoft.com/download/details.aspx?id=36433
<!--Update_Description: update metadata-->