---
title: Conectar-se com o sqlcmd
description: Use o utilitário de linha de comando sqlcmd para se conectar e consultar um SQL Data Warehouse do Azure.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: e4b432e0be0cdded5089965b9d272aa82e31bd36
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685749"
---
# <a name="connect-to-sql-data-warehouse-with-sqlcmd"></a>Ligar ao SQL Data Warehouse com sqlcmd
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

Use o utilitário de linha de comando [sqlcmd][sqlcmd] para se conectar e consultar um SQL data warehouse do Azure.  

## <a name="1-connect"></a>1. conectar
Para começar a utilizar o [sqlcmd][sqlcmd], abra a linha de comandos e escreva **sqlcmd**, seguido da cadeia de ligação da sua base de dados SQL Data Warehouse. A cadeia de ligação requer os parâmetros seguintes:

* **Server (-S):** servidor sob a forma `<`Nome do Servidor`>`. database.windows.net
* **Database (-d):** nome da base de dados.
* **Enable Quoted Identifiers (-I):** os identificadores delimitado por aspas têm de estar ativados para ligar a uma instância do SQL Data Warehouse.

Para utilizar a Autenticação do SQL Server tem de adicionar os parâmetros de nome de utilizador/palavra-passe:

* **User (-U):** utilizador do servidor sob a forma `<`Utilizador`>`
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
> 
> 

## <a name="2-query"></a>2. consulta
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
Consulte a [documentação do sqlcmd][sqlcmd] para obter mais detalhes sobre as opções disponíveis no sqlcmd.

<!--Image references-->

<!--Article references-->

<!--MSDN references--> 
[sqlcmd]: https://msdn.microsoft.com/library/ms162773.aspx
[Azure portal]: https://portal.azure.com

<!--Other Web references-->
