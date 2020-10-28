---
title: Começar com consultas de base de dados cruzadas
description: como utilizar consulta de base de dados elástica com bases de dados verticalmente divididas
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: dd8a30c60c81ef2761d5dc051d2dfeb300e1bac4
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92792128"
---
# <a name="get-started-with-cross-database-queries-vertical-partitioning-preview"></a>Começar com consultas de base de dados cruzadas (partição vertical) (pré-visualização)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

A consulta de base de dados elástica (pré-visualização) para Azure SQL Database permite executar consultas T-SQL que abrangem várias bases de dados utilizando um único ponto de ligação. Este artigo aplica-se a [bases de dados verticalmente divididas.](elastic-query-vertical-partitioning.md)  

Quando concluído, irá: aprender a configurar e utilizar uma Base de Dados Azure SQL para realizar consultas que abrangem várias bases de dados relacionadas.

Para obter mais informações sobre a funcionalidade de consulta de base de dados elástica, consulte  [a visão geral da consulta de base de dados elástica Azure SQL Database](elastic-query-overview.md).

## <a name="prerequisites"></a>Pré-requisitos

ALTERAR qualquer autorização EXTERNA DE FONTE DE DADOS É necessária. Esta permissão está incluída na permissão ALTER DATABASE. ALTERAR QUAISquer permissões EXTERNAS DE FONTE DE DADOS são necessárias para consultar a fonte de dados subjacente.

## <a name="create-the-sample-databases"></a>Criar as bases de dados de amostras

Para começar, crie duas bases de dados, **Clientes** e **Encomendas,** quer nos mesmos servidores, quer em servidores diferentes.

Execute as seguintes consultas na base de **dados Encomendas** para criar a tabela **Informação ordem** e inserir os dados da amostra.

```tsql
CREATE TABLE [dbo].[OrderInformation](
    [OrderID] [int] NOT NULL,
    [CustomerID] [int] NOT NULL
    )
INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (123, 1)
INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (149, 2)
INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (857, 2)
INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (321, 1)
INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (564, 8)
```

Agora, execute a seguinte consulta na base de **dados clientes** para criar a tabela **De Informação** do Cliente e inserir os dados da amostra.

```tsql
CREATE TABLE [dbo].[CustomerInformation](
    [CustomerID] [int] NOT NULL,
    [CustomerName] [varchar](50) NULL,
    [Company] [varchar](50) NULL
    CONSTRAINT [CustID] PRIMARY KEY CLUSTERED ([CustomerID] ASC)
)
INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (1, 'Jack', 'ABC')
INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (2, 'Steve', 'XYZ')
INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (3, 'Lylla', 'MNO')
```

## <a name="create-database-objects"></a>Criar objetos de base de dados

### <a name="database-scoped-master-key-and-credentials"></a>Chave e credenciais principais do âmbito da base de dados

1. Abra o SQL Server Management Studio ou as Ferramentas de Dados do Servidor SQL em Estúdio Visual.
2. Ligue à base de dados encomendas e execute os seguintes comandos T-SQL:

    ```tsql
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<master_key_password>';
    CREATE DATABASE SCOPED CREDENTIAL ElasticDBQueryCred
    WITH IDENTITY = '<username>',
    SECRET = '<password>';  
    ```

    O "nome de utilizador" e "palavra-passe" deve ser o nome de utilizador e a palavra-passe utilizada para iniciar sessão na base de dados dos Clientes.
    A autenticação utilizando o Azure Ative Directory com consultas elásticas não é suportada atualmente.

### <a name="external-data-sources"></a>Fontes de dados externas

Para criar uma fonte de dados externa, execute o seguinte comando na base de dados Encomendas:

```tsql
CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc WITH
    (TYPE = RDBMS,
    LOCATION = '<server_name>.database.windows.net',
    DATABASE_NAME = 'Customers',
    CREDENTIAL = ElasticDBQueryCred,
) ;
```

### <a name="external-tables"></a>Tabelas externas

Criar uma tabela externa na base de dados encomendas, que corresponda à definição da tabela Informação do Cliente:

```tsql
CREATE EXTERNAL TABLE [dbo].[CustomerInformation]
( [CustomerID] [int] NOT NULL,
    [CustomerName] [varchar](50) NOT NULL,
    [Company] [varchar](50) NOT NULL)
WITH
( DATA_SOURCE = MyElasticDBQueryDataSrc)
```

## <a name="execute-a-sample-elastic-database-t-sql-query"></a>Execute uma consulta de base de dados elástica de amostra T-SQL

Uma vez definida a sua fonte de dados externa e as suas tabelas externas, pode agora utilizar o T-SQL para consultar as suas tabelas externas. Execute esta consulta na base de dados encomendas:

```tsql
SELECT OrderInformation.CustomerID, OrderInformation.OrderId, CustomerInformation.CustomerName, CustomerInformation.Company
FROM OrderInformation
INNER JOIN CustomerInformation
ON CustomerInformation.CustomerID = OrderInformation.CustomerID
```

## <a name="cost"></a>Custo

Atualmente, a função de consulta de base de dados elástica está incluída no custo da sua Base de Dados Azure SQL.  

Para obter informações sobre preços, consulte [os preços da base de dados SQL.](https://azure.microsoft.com/pricing/details/sql-database)

## <a name="next-steps"></a>Passos seguintes

* Para obter uma visão geral da consulta elástica, consulte [a visão geral da consulta elástica](elastic-query-overview.md).
* Para consultas de sintaxe e amostra para dados verticalmente divididos, consulte [consulta de dados partidos verticalmente)](elastic-query-vertical-partitioning.md)
* Para obter um tutorial de partição horizontal (fragmento), consulte [Começar com consulta elástica para partição horizontal (fragmento)](elastic-query-getting-started.md).
* Para consultas de sintaxe e amostra para dados com divisórias horizontais, consulte [consulta de dados com partição horizontal)](elastic-query-horizontal-partitioning.md)
* Consulte [o sp executar o controlo \_ \_ remoto](/sql/relational-databases/system-stored-procedures/sp-execute-remote-azure-sql-database) para um procedimento armazenado que execute uma declaração Transact-SQL numa única base de dados remota do Azure SQL ou num conjunto de bases de dados que servem de fragmentos num esquema de partição horizontal.