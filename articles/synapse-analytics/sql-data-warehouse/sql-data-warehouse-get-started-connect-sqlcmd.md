---
title: Conecte-se com sqlcmd
description: Utilize um utilitário de linha de comando sqlcmd para ligar e consultar uma piscina Sinapse SQL.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 3d1d8d3ce3afece5a979aadc27cd82dc7ddaf0d5
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98676238"
---
# <a name="connect-to-sql-pool-in-azure-synapse-analytics-with-sqlcmd"></a>Conecte-se à piscina SQL em Azure Synapse Analytics com sqlcmd

> [!div class="op_single_selector"]
>
> * [Power BI](/power-bi/connect-data/service-azure-sql-data-warehouse-with-direct-connect)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md)
> * [SSMS](sql-data-warehouse-query-ssms.md)

Utilize o utilitário de linha de comando [sqlcmd] para ligar e consultar uma piscina SQL.  

## <a name="1-connect"></a>1. Ligar

Para começar com [sqlcmd][sqlcmd], abra a solicitação de comando e entre **em sqlcmd** seguido pela cadeia de ligação para a sua piscina SQL. A cadeia de ligação requer os parâmetros seguintes:

* **Server (-S):** servidor sob a forma `<`Nome do Servidor`>`. database.windows.net
* **Base de dados (-d):** Nome da piscina SQL.
* **Ativar identificadores citados (-I):** Os identificadores citados devem ser habilitados a ligar-se a uma placa de piscina SQL.

Para utilizar a Autenticação do SQL Server tem de adicionar os parâmetros de nome de utilizador/palavra-passe:

* **Utilizador (-U):** Utilizador do servidor no formulário `<` Utilizador`>`
* **Password (-P):** palavra-passe associada ao utilizador.

Por exemplo, a cadeia de ligação poderá ter o seguinte aspeto:

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
```

Para utilizar a autenticação Integrada do Azure Active Directory, tem de adicionar os parâmetros do Azure Active Directory:

* **Autenticação do Azure Active Directory (-G):** utilize o Azure Active Directory para autenticação

Por exemplo, a cadeia de ligação poderá ter o seguinte aspeto:

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -G -I
```

> [!NOTE]
> terá de [ativar a Autenticação do Azure Active Directory Authentication](sql-data-warehouse-authentication.md) para realizar a autenticação utilizando o Active Directory.

## <a name="2-query"></a>2. Consulta

Após a ligação, pode emitir quaisquer instruções Transact-SQL suportadas na instância.  Neste exemplo, as consultas são submetidas no modo interativo.

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
1> SELECT name FROM sys.tables;
2> GO
3> QUIT
```

Os exemplos que se seguem mostram como pode executar consultas no modo batch através da opção -Q ou ao ligar o seu SQL ao sqlcmd.

```sql
sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I -Q "SELECT name FROM sys.tables;"
```

```sql
"SELECT name FROM sys.tables;" | sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I > .\tables.out
```

## <a name="next-steps"></a>Passos seguintes

Para mais informações sobre as opções disponíveis em sqlcmd, consulte [a documentação sqlcmd](/sql/tools/sqlcmd-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).