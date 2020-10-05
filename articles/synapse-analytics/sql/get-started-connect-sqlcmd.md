---
title: Ligue ao SQL da Sinapse com sqlcmd
description: Utilize o utilitário de linha de comando sqlcmd para ligar e consultar a piscina SQL on-demand (pré-visualização) e SQL.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 1af42a838463132a241447c11b90bfb489f879ac
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2020
ms.locfileid: "87059454"
---
# <a name="connect-to-synapse-sql-with-sqlcmd"></a>Ligue ao SQL da Sinapse com sqlcmd

> [!div class="op_single_selector"]
> * [Azure Data Studio (pré-visualização)](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
> * [sqlcmd](../sql/get-started-connect-sqlcmd.md)
> * [SSMS](get-started-ssms.md)

Pode utilizar o utilitário [sqlcmd](/sql/tools/sqlcmd-utility?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) da linha de comando para ligar e consultar a piscina SQL on-demand (pré-visualização) e SQL dentro do Synapse SQL.  

## <a name="1-connect"></a>1. Ligar
Para começar com [sqlcmd,](/sql/tools/sqlcmd-utility?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)abra o pedido de comando e introduza **sqlcmd** seguido pela cadeia de ligação para a sua base de dados Synapse SQL. A cadeia de ligação requer os parâmetros seguintes:

* **Server (-S):** servidor sob a forma `<`Nome do Servidor`>`. database.windows.net
* **Base de dados (-d):** Nome da base de dados
* **Ativar identificadores citados (-I):** Os identificadores citados devem ser habilitados a ligar-se a uma instância Sinapse SQL

Para utilizar a autenticação do servidor SQL, é necessário adicionar o nome de utilizador e os parâmetros de senha:

* **Utilizador (-U):** Utilizador do servidor no formulário `<` Utilizador`>`
* **Palavra-passe (-P):** Palavra-passe associada ao utilizador

A sua cadeia de ligação pode parecer o seguinte exemplo:

**SQL a pedido**

```sql
C:\>sqlcmd -S partyeunrt.database.windows.net -d demo -U Enter_Your_Username_Here -P Enter_Your_Password_Here -I
```

**Conjunto de SQL**

```
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
```

Para utilizar a autenticação Integrada do Azure Active Directory, tem de adicionar os parâmetros do Azure Active Directory:

* **Autenticação do Azure Active Directory (-G):** utilize o Azure Active Directory para autenticação

A sua cadeia de ligação pode parecer-se com os seguintes exemplos:

**SQL a pedido**

```
C:\>sqlcmd -S partyeunrt.database.windows.net -d demo -G -I
```

**Conjunto de SQL**

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -G -I
```

> [!NOTE]
> terá de [ativar a Autenticação do Azure Active Directory Authentication](../sql-data-warehouse/sql-data-warehouse-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) para realizar a autenticação utilizando o Active Directory.

## <a name="2-query"></a>2. Consulta

### <a name="use-sql-pool"></a>Use a piscina SQL

Após a ligação, pode emitir quaisquer declarações [de Transact-SQL](/sql/t-sql/language-reference?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) (T-SQL) suportadas contra a instância. Neste exemplo, as consultas são submetidas em modo interativo:

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
1> SELECT name FROM sys.tables;
2> GO
3> QUIT
```

Para a piscina SQL, os seguintes exemplos mostram-lhe como executar consultas em modo de lote utilizando a opção -Q ou canalizando o seu SQL para sqlcmd:

```sql
sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I -Q "SELECT name FROM sys.tables;"
```

```sql
"SELECT name FROM sys.tables;" | sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I > .\tables.out
```

### <a name="use-sql-on-demand"></a>Utilizar o SQL a pedido

Após a ligação, pode emitir quaisquer declarações [de Transact-SQL](/sql/t-sql/language-reference?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) (T-SQL) suportadas contra a instância.  No exemplo seguinte, as consultas são submetidas em modo interativo:

```sql
C:\>sqlcmd -S partyeunrt.database.windows.net -d demo -U Enter_Your_Username_Here -P Enter_Your_Password_Here -I
1> SELECT COUNT(*) FROM  OPENROWSET(BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet', FORMAT='PARQUET')
2> GO
3> QUIT
```

Para o SQL on demand, os exemplos que se seguem mostram-lhe como executar consultas no modo de lote utilizando a opção -Q ou canalizando o seu SQL para sqlcmd:

```sql
sqlcmd -S partyeunrt.database.windows.net -d demo -U Enter_Your_Username_Here -P 'Enter_Your_Password_Here' -I -Q "SELECT COUNT(*) FROM  OPENROWSET(BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet', FORMAT='PARQUET')"
```

```sql
"SELECT COUNT(*) FROM  OPENROWSET(BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet', FORMAT='PARQUET')" | sqlcmd -S partyeunrt.database.windows.net -d demo -U Enter_Your_Username_Here -P 'Enter_Your_Password_Here' -I > ./tables.out
```

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre as opções sqlcmd, consulte a [documentação sqlcmd](/sql/tools/sqlcmd-utility?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).
