---
title: Começar com consultas de base de dados cruzadas
description: como usar consulta de base de dados elástica com bases de dados verticalmente divididas
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "73823804"
---
# <a name="get-started-with-cross-database-queries-vertical-partitioning-preview"></a>Começar com consultas de base de dados cruzadas (partição vertical) (pré-visualização)

Consulta de base de dados elástica (pré-visualização) para Base de Dados SQL Azure permite-lhe executar consultas T-SQL que abrangem várias bases de dados usando um único ponto de ligação. Este artigo aplica-se a bases de [dados verticalmente divididas](sql-database-elastic-query-vertical-partitioning.md).  

Quando estiver concluído, irá: aprender como configurar e utilizar uma Base de Dados Azure SQL para realizar consultas que abrangem várias bases de dados relacionadas.

Para mais informações sobre a funcionalidade de consulta de base de dados elástica, consulte a visão geral da consulta de [dados elástica azure SQL](sql-database-elastic-query-overview.md)Database .

## <a name="prerequisites"></a>Pré-requisitos

ALTERE qualquer permissão externa de FONTE DE DADOS. Esta permissão está incluída com a permissão alter DATABASE. ALTERE quaisquer permissões externas de FONTE DE DADOS são necessárias para se referir à fonte de dados subjacente.

## <a name="create-the-sample-databases"></a>Criar as bases de dados da amostra

Para começar, crie duas bases de dados, **Clientes** e **Encomendas,** quer nos mesmos ou diferentes servidores de Base de Dados SQL.

Execute as seguintes consultas na base de dados de **Encomendas** para criar a tabela **Informação** de Encomendas e inserir os dados da amostra.

    CREATE TABLE [dbo].[OrderInformation](
        [OrderID] [int] NOT NULL,
        [CustomerID] [int] NOT NULL
        )
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (123, 1)
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (149, 2)
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (857, 2)
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (321, 1)
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (564, 8)

Agora, execute a seguinte consulta na base de dados dos **Clientes** para criar a tabela de **Informação** do Cliente e inserir os dados da amostra.

    CREATE TABLE [dbo].[CustomerInformation](
        [CustomerID] [int] NOT NULL,
        [CustomerName] [varchar](50) NULL,
        [Company] [varchar](50) NULL
        CONSTRAINT [CustID] PRIMARY KEY CLUSTERED ([CustomerID] ASC)
    )
    INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (1, 'Jack', 'ABC')
    INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (2, 'Steve', 'XYZ')
    INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (3, 'Lylla', 'MNO')

## <a name="create-database-objects"></a>Criar objetos de base de dados

### <a name="database-scoped-master-key-and-credentials"></a>Chave e credenciais de base de dados

1. Abra o Estúdio de Gestão de Servidores SQL ou as Ferramentas de Dados do Servidor SQL no Estúdio Visual.
2. Ligue-se à base de dados de Encomendas e execute os seguintes comandos T-SQL:

        CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<master_key_password>';
        CREATE DATABASE SCOPED CREDENTIAL ElasticDBQueryCred
        WITH IDENTITY = '<username>',
        SECRET = '<password>';  

    O "nome de utilizador" e "password" devem ser o nome de utilizador e a palavra-passe utilizados para iniciar sessão na base de dados dos Clientes.
    A autenticação utilizando o Diretório Ativo Azure com consultas elásticas não é suportada atualmente.

### <a name="external-data-sources"></a>Fontes de dados externas

Para criar uma fonte de dados externa, execute o seguinte comando na base de dados de Encomendas:

    CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc WITH
        (TYPE = RDBMS,
        LOCATION = '<server_name>.database.windows.net',
        DATABASE_NAME = 'Customers',
        CREDENTIAL = ElasticDBQueryCred,
    ) ;

### <a name="external-tables"></a>Tabelas externas

Crie uma tabela externa na base de dados de Encomendas, que corresponda à definição da tabela Informação do Cliente:

    CREATE EXTERNAL TABLE [dbo].[CustomerInformation]
    ( [CustomerID] [int] NOT NULL,
      [CustomerName] [varchar](50) NOT NULL,
      [Company] [varchar](50) NOT NULL)
    WITH
    ( DATA_SOURCE = MyElasticDBQueryDataSrc)

## <a name="execute-a-sample-elastic-database-t-sql-query"></a>Executar uma consulta de base de dados elástica t-SQL de amostra

Depois de definir a sua fonte de dados externa e as suas tabelas externas, pode agora utilizar o T-SQL para consultar as suas tabelas externas. Execute esta consulta na base de dados de Encomendas:

    SELECT OrderInformation.CustomerID, OrderInformation.OrderId, CustomerInformation.CustomerName, CustomerInformation.Company
    FROM OrderInformation
    INNER JOIN CustomerInformation
    ON CustomerInformation.CustomerID = OrderInformation.CustomerID

## <a name="cost"></a>Custo

Atualmente, a funcionalidade de consulta de base de dados elástica está incluída no custo da sua Base de Dados Azure SQL.  

Para obter informações sobre preços, consulte o Preço da Base de [Dados SQL](https://azure.microsoft.com/pricing/details/sql-database).

## <a name="next-steps"></a>Passos seguintes

* Para uma visão geral da consulta elástica, consulte a visão geral da [consulta elástica](sql-database-elastic-query-overview.md).
* Para consultas de sintaxe e amostras para dados verticalmente divididos, consulte a [consulta de dados verticalmente divididos)](sql-database-elastic-query-vertical-partitioning.md)
* Para um tutorial de partição horizontal (sharding), consulte [Iniciar-se com consulta elástica para partição horizontal (sharding)](sql-database-elastic-query-getting-started.md).
* Para consultas de sintaxe e amostras para dados horizontalmente divididos, consulte dados [horizontalmente divididos)](sql-database-elastic-query-horizontal-partitioning.md)
* Consulte o [sp\_execute \_o controlo remoto](https://msdn.microsoft.com/library/mt703714) para um procedimento armazenado que execute uma declaração Transact-SQL numa única base de dados remota do Azure SQL ou num conjunto de bases de dados que servem como fragmentos num esquema de partição horizontal.
