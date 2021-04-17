---
title: Ligue ao SQL da Sinapse com sqlcmd
description: Utilize o utilitário sqlcmd da linha de comando para ligar e consultar a piscina SQL sem servidor e a piscina SQL dedicada.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 3abdd44f0684282e92da147dff996ff54f0ef23f
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565479"
---
# <a name="connect-to-synapse-sql-with-sqlcmd"></a>Ligue ao SQL da Sinapse com sqlcmd

> [!div class="op_single_selector"]
> * [Azure Data Studio)](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
> * [sqlcmd](../sql/get-started-connect-sqlcmd.md)
> * [SSMS](get-started-ssms.md)

Você pode usar o utilitário [sqlcmd](/sql/tools/sqlcmd-utility?view=azure-sqldw-latest&preserve-view=true) da linha de comando para ligar e consultar piscina SQL sem servidor e piscina SQL dedicada dentro do Sinaapse SQL.  

## <a name="1-connect"></a>1. Ligar
Para começar com [sqlcmd,](/sql/tools/sqlcmd-utility?view=azure-sqldw-latest&preserve-view=true)abra o pedido de comando e introduza **sqlcmd** seguido pela cadeia de ligação para a sua base de dados Synapse SQL. A cadeia de ligação requer os parâmetros seguintes:

* **Server (-S):** servidor sob a forma `<`Nome do Servidor`>`. database.windows.net
* **Base de dados (-d):** Nome da base de dados
* **Ativar identificadores citados (-I):** Os identificadores citados devem ser habilitados a ligar-se a uma instância Sinapse SQL

Para utilizar a autenticação do servidor SQL, é necessário adicionar o nome de utilizador e os parâmetros de senha:

* **Utilizador (-U):** Utilizador do servidor no formulário `<` Utilizador`>`
* **Palavra-passe (-P):** Palavra-passe associada ao utilizador

A sua cadeia de ligação pode parecer o seguinte exemplo:

**Piscina SQL sem servidor**

```sql
C:\>sqlcmd -S partyeunrt.database.windows.net -d demo -U Enter_Your_Username_Here -P Enter_Your_Password_Here -I
```

**Conjunto de SQL dedicado**

```
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
```

Para utilizar a autenticação Integrada do Azure Active Directory, tem de adicionar os parâmetros do Azure Active Directory:

* **Autenticação do Azure Active Directory (-G):** utilize o Azure Active Directory para autenticação

A sua cadeia de ligação pode parecer-se com os seguintes exemplos:

**Piscina SQL sem servidor**

```
C:\>sqlcmd -S partyeunrt.database.windows.net -d demo -G -I
```

**Conjunto de SQL dedicado**

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -G -I
```

> [!NOTE]
> terá de [ativar a Autenticação do Azure Active Directory Authentication](../sql-data-warehouse/sql-data-warehouse-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) para realizar a autenticação utilizando o Active Directory.

## <a name="2-query"></a>2. Consulta

### <a name="use-dedicated-sql-pool"></a>Use piscina SQL dedicada

Após a ligação, pode emitir quaisquer declarações [de Transact-SQL](/sql/t-sql/language-reference?view=azure-sqldw-latest&preserve-view=true) (T-SQL) suportadas contra a instância. Neste exemplo, as consultas são submetidas em modo interativo:

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
1> SELECT name FROM sys.tables;
2> GO
3> QUIT
```

Para uma piscina SQL dedicada, os seguintes exemplos mostram-lhe como executar consultas em modo de lote utilizando a opção -Q ou canalizando o seu SQL para sqlcmd:

```sql
sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I -Q "SELECT name FROM sys.tables;"
```

```sql
"SELECT name FROM sys.tables;" | sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I > .\tables.out
```

### <a name="use-serverless-sql-pool"></a>Utilizar conjunto de SQL sem servidor

Após a ligação, pode emitir quaisquer declarações [de Transact-SQL](/sql/t-sql/language-reference?view=azure-sqldw-latest&preserve-view=true) (T-SQL) suportadas contra a instância.  No exemplo seguinte, as consultas são submetidas em modo interativo:

```sql
C:\>sqlcmd -S partyeunrt.database.windows.net -d demo -U Enter_Your_Username_Here -P Enter_Your_Password_Here -I
1> SELECT COUNT(*) FROM  OPENROWSET(BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet', FORMAT='PARQUET')
2> GO
3> QUIT
```

Para a piscina SQL sem servidor, os exemplos que se seguem mostram-lhe como executar consultas no modo de lote utilizando a opção -Q ou canalizando o seu SQL para sqlcmd:

```sql
sqlcmd -S partyeunrt.database.windows.net -d demo -U Enter_Your_Username_Here -P 'Enter_Your_Password_Here' -I -Q "SELECT COUNT(*) FROM  OPENROWSET(BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet', FORMAT='PARQUET')"
```

```sql
"SELECT COUNT(*) FROM  OPENROWSET(BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet', FORMAT='PARQUET')" | sqlcmd -S partyeunrt.database.windows.net -d demo -U Enter_Your_Username_Here -P 'Enter_Your_Password_Here' -I > ./tables.out
```

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre as opções sqlcmd, consulte a [documentação sqlcmd](/sql/tools/sqlcmd-utility?view=azure-sqldw-latest&preserve-view=true).
