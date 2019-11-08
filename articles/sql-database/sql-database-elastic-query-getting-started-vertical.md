---
title: Introdução às consultas entre bases de dados
description: como usar a consulta de banco de dados elástico com bancos de dados particionados verticalmente
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: af93035766eaf1afa12d124b8379ee55c5567260
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823804"
---
# <a name="get-started-with-cross-database-queries-vertical-partitioning-preview"></a>Introdução às consultas entre bancos de dados (particionamento vertical) (visualização)

A consulta de banco de dados elástico (visualização) para o banco de dados SQL do Azure permite executar consultas T-SQL que abrangem vários bancos de dados usando um único ponto de conexão. Este artigo se aplica a [bancos de dados particionados verticalmente](sql-database-elastic-query-vertical-partitioning.md).  

Quando concluído, você irá: aprender a configurar e usar um banco de dados SQL do Azure para executar consultas que abrangem vários bancos de dados relacionados.

Para obter mais informações sobre o recurso de consulta de banco de dados elástico, consulte [visão geral da consulta de banco de dados elástico do banco de dados SQL](sql-database-elastic-query-overview.md)

## <a name="prerequisites"></a>Pré-requisitos

A permissão alterar qualquer fonte de dados externa é necessária. Essa permissão está incluída com a permissão ALTER DATABASE. As permissões alterar qualquer fonte de dados externa são necessárias para fazer referência à fonte de dados subjacente.

## <a name="create-the-sample-databases"></a>Criar os bancos de dados de exemplo

Para começar, crie dois bancos de dados, **clientes** e **pedidos**, tanto no mesmo servidor quanto em servidores de banco de dados SQL diferentes.

Execute as consultas a seguir no banco de dados **Orders** para criar a tabela **OrderInformation** e inserir os dados de exemplo.

    CREATE TABLE [dbo].[OrderInformation](
        [OrderID] [int] NOT NULL,
        [CustomerID] [int] NOT NULL
        )
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (123, 1)
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (149, 2)
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (857, 2)
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (321, 1)
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (564, 8)

Agora, execute a consulta a seguir no banco de dados **Customers** para criar a tabela **CustomerInformation** e inserir os dados de exemplo.

    CREATE TABLE [dbo].[CustomerInformation](
        [CustomerID] [int] NOT NULL,
        [CustomerName] [varchar](50) NULL,
        [Company] [varchar](50) NULL
        CONSTRAINT [CustID] PRIMARY KEY CLUSTERED ([CustomerID] ASC)
    )
    INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (1, 'Jack', 'ABC')
    INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (2, 'Steve', 'XYZ')
    INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (3, 'Lylla', 'MNO')

## <a name="create-database-objects"></a>Criar objetos de banco de dados

### <a name="database-scoped-master-key-and-credentials"></a>Chave mestra e credenciais de escopo do banco de dados

1. Abra SQL Server Management Studio ou SQL Server Data Tools no Visual Studio.
2. Conecte-se ao banco de dados Orders e execute os seguintes comandos T-SQL:

        CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<master_key_password>';
        CREATE DATABASE SCOPED CREDENTIAL ElasticDBQueryCred
        WITH IDENTITY = '<username>',
        SECRET = '<password>';  

    O "nome de usuário" e a "senha" devem ser o nome de usuário e a senha usados para fazer logon no banco de dados de clientes.
    Atualmente, não há suporte para a autenticação usando Azure Active Directory com consultas elásticas.

### <a name="external-data-sources"></a>Fontes de dados externas

Para criar uma fonte de dados externa, execute o seguinte comando no banco de dado pedidos:

    CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc WITH
        (TYPE = RDBMS,
        LOCATION = '<server_name>.database.windows.net',
        DATABASE_NAME = 'Customers',
        CREDENTIAL = ElasticDBQueryCred,
    ) ;

### <a name="external-tables"></a>Tabelas externas

Crie uma tabela externa no banco de dados Orders, que corresponde à definição da tabela CustomerInformation:

    CREATE EXTERNAL TABLE [dbo].[CustomerInformation]
    ( [CustomerID] [int] NOT NULL,
      [CustomerName] [varchar](50) NOT NULL,
      [Company] [varchar](50) NOT NULL)
    WITH
    ( DATA_SOURCE = MyElasticDBQueryDataSrc)

## <a name="execute-a-sample-elastic-database-t-sql-query"></a>Executar uma consulta T-SQL de banco de dados elástico de exemplo

Depois de definir sua fonte de dados externa e suas tabelas externas, agora você pode usar o T-SQL para consultar suas tabelas externas. Execute esta consulta no banco de dados Orders:

    SELECT OrderInformation.CustomerID, OrderInformation.OrderId, CustomerInformation.CustomerName, CustomerInformation.Company
    FROM OrderInformation
    INNER JOIN CustomerInformation
    ON CustomerInformation.CustomerID = OrderInformation.CustomerID

## <a name="cost"></a>Custo

Atualmente, o recurso de consulta de banco de dados elástico está incluído no custo do seu banco de dados SQL do Azure.  

Para obter informações sobre preços, consulte [preços do banco de dados SQL](https://azure.microsoft.com/pricing/details/sql-database).

## <a name="next-steps"></a>Passos seguintes

* Para obter uma visão geral da consulta elástica, consulte [visão geral da consulta elástica](sql-database-elastic-query-overview.md).
* Para obter sintaxe e exemplos de consultas para dados particionados verticalmente, consulte [consultando dados particionados verticalmente)](sql-database-elastic-query-vertical-partitioning.md)
* Para obter um tutorial horizontal de particionamento (fragmentação), consulte [introdução à consulta elástica para particionamento horizontal (fragmentação)](sql-database-elastic-query-getting-started.md).
* Para obter sintaxe e exemplos de consultas para dados particionados horizontalmente, consulte [consultando dados particionados horizontalmente)](sql-database-elastic-query-horizontal-partitioning.md)
* Consulte [sp\_execute \_Remote](https://msdn.microsoft.com/library/mt703714) para obter um procedimento armazenado que executa uma instrução TRANSACT-SQL em um único banco de dados SQL do Azure remoto ou conjunto de bancos de dados que serve como fragmentos em um esquema de particionamento horizontal.
