---
title: Ligar ao Azure SQL Data Warehouse sqlcmd | Microsoft Docs
description: Utilize o utilitário sqlcmd de linha de comando para ligar e consultar um Azure SQL Data Warehouse.
services: sql-data-warehouse
author: XiaoyuL-Preview
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 72760c5123703a664695c1be4d286a38e96ecc3f
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/17/2019
ms.locfileid: "65873327"
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

Utilize o utilitário de linha de comandos [sqlcmd][sqlcmd] para ligar e consultar um Azure SQL Data Warehouse.  

## <a name="1-connect"></a>1. Ligar
Para começar a utilizar o [sqlcmd][sqlcmd], abra a linha de comandos e escreva **sqlcmd**, seguido da cadeia de ligação da sua base de dados SQL Data Warehouse. A cadeia de ligação requer os parâmetros seguintes:

* **Servidor (-S):** Servidor sob a forma `<`nome do servidor`>`. database.windows.net
* **Base de dados (-1!d):** Nome da base de dados.
* **Enable Quoted identificadores (-Me):** Identificadores delimitados por aspas tem de estar ativadas para ligar a uma instância do SQL Data Warehouse.

Para utilizar a Autenticação do SQL Server tem de adicionar os parâmetros de nome de utilizador/palavra-passe:

* **Utilizador (-U):** Utilizador do servidor sob a forma `<`utilizador`>`
* **Palavra-passe (-P):** Palavra-passe associado ao utilizador.

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

## <a name="next-steps"></a>Passos Seguintes
Veja a [documentação do sqlcmd][sqlcmd] para obter mais detalhes sobre as opções disponíveis no sqlcmd.

<!--Image references-->

<!--Article references-->

<!--MSDN references--> 
[sqlcmd]: https://msdn.microsoft.com/library/ms162773.aspx
[Azure portal]: https://portal.azure.com

<!--Other Web references-->
