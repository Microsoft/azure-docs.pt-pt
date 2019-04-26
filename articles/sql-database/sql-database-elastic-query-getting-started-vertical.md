---
title: Introdução às consultas entre bases de dados (criação de partições verticais) | Documentos da Microsoft
description: como utilizar a consulta de base de dados elástica com bases de dados particionados verticalmente
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 116a465a0ddc913e342e0ffcc1fb29f5bf969419
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60307449"
---
# <a name="get-started-with-cross-database-queries-vertical-partitioning-preview"></a>Introdução às consultas entre bases de dados (criação de partições verticais) (pré-visualização)

Consulta de base de dados elástica (pré-visualização) para a base de dados do Azure SQL permite-lhe executar consultas de T-SQL que abrangem várias bases de dados a utilizar um ponto de ligação único. Este artigo aplica-se ao [particionados verticalmente as bases de dados](sql-database-elastic-query-vertical-partitioning.md).  

Quando concluído, irá: Saiba como configurar e utilizar uma base de dados do SQL do Azure para executar consultas que abrangem várias bases de dados relacionados.

Para obter mais informações sobre a funcionalidade de consulta de base de dados elásticas, consulte [descrição geral das consultas de base de dados elástica de base de dados do Azure SQL](sql-database-elastic-query-overview.md).

## <a name="prerequisites"></a>Pré-requisitos

É necessária a permissão ALTER qualquer origem de dados externa. Esta permissão está incluída com a permissão ALTER DATABASE. São necessárias permissões de alterar qualquer origem de dados externa para fazer referência à origem de dados subjacente.

## <a name="create-the-sample-databases"></a>Criar as bases de dados de exemplo

Para começar, crie duas bases de dados **os clientes** e **ordens**, em servidores de base de dados SQL idêntica ou diferentes.

Execute as seguintes consultas na **encomendas** base de dados para criar o **OrderInformation** de tabela e os dados de exemplo de entrada.

    CREATE TABLE [dbo].[OrderInformation](
        [OrderID] [int] NOT NULL,
        [CustomerID] [int] NOT NULL
        )
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (123, 1)
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (149, 2)
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (857, 2)
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (321, 1)
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (564, 8)

Agora, execute a seguinte consulta no **os clientes** base de dados para criar o **CustomerInformation** de tabela e os dados de exemplo de entrada.

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

### <a name="database-scoped-master-key-and-credentials"></a>Chave mestra do âmbito e as credenciais de base de dados

1. Abra o SQL Server Management Studio ou o SQL Server Data Tools no Visual Studio.
2. Ligue à base de dados de encomendas e execute os seguintes comandos T-SQL:

        CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<master_key_password>';
        CREATE DATABASE SCOPED CREDENTIAL ElasticDBQueryCred
        WITH IDENTITY = '<username>',
        SECRET = '<password>';  

    O "nomedeutilizador" e "password" devem ser o nome de utilizador e palavra-passe utilizada para iniciar sessão na base de dados de clientes.
    Autenticação com o Azure Active Directory com consultas elásticas não é atualmente suportada.

### <a name="external-data-sources"></a>Origens de dados externas

Para criar uma origem de dados externa, execute o seguinte comando na base de dados de encomendas:

    CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc WITH
        (TYPE = RDBMS,
        LOCATION = '<server_name>.database.windows.net',
        DATABASE_NAME = 'Customers',
        CREDENTIAL = ElasticDBQueryCred,
    ) ;

### <a name="external-tables"></a>Tabelas externas

Crie uma tabela externa a base de dados de encomendas, que corresponde à definição da tabela CustomerInformation:

    CREATE EXTERNAL TABLE [dbo].[CustomerInformation]
    ( [CustomerID] [int] NOT NULL,
      [CustomerName] [varchar](50) NOT NULL,
      [Company] [varchar](50) NOT NULL)
    WITH
    ( DATA_SOURCE = MyElasticDBQueryDataSrc)

## <a name="execute-a-sample-elastic-database-t-sql-query"></a>Executar uma consulta de T-SQL da base de dados elástica de exemplo

Depois de definir a origem de dados externos e as tabelas externas, pode agora utilizar T-SQL para consultar as tabelas externas. Execute esta consulta na base de dados de encomendas:

    SELECT OrderInformation.CustomerID, OrderInformation.OrderId, CustomerInformation.CustomerName, CustomerInformation.Company
    FROM OrderInformation
    INNER JOIN CustomerInformation
    ON CustomerInformation.CustomerID = OrderInformation.CustomerID

## <a name="cost"></a>Custo

Atualmente, a funcionalidade de consulta de base de dados elástica está incluída no custo da base de dados SQL do Azure.  

Para obter informações sobre preços, consulte [preços de base de dados SQL](https://azure.microsoft.com/pricing/details/sql-database).

## <a name="next-steps"></a>Passos Seguintes

* Para obter uma descrição geral de consulta elástica, veja [descrição geral de consulta elástica](sql-database-elastic-query-overview.md).
* Para consultas de exemplo de sintaxe e para dados particionados verticalmente, consulte [consultar verticalmente particionada dados)](sql-database-elastic-query-vertical-partitioning.md)
* Para obter um tutorial de criação de partições horizontais (fragmentação), consulte [guia de introdução consulta elástica para a criação de partições horizontais (fragmentação)](sql-database-elastic-query-getting-started.md).
* Para consultas de exemplo de sintaxe e para dados particionados horizontalmente, consulte [horizontalmente a consulta particionados dados)](sql-database-elastic-query-horizontal-partitioning.md)
* Ver [sp\_execute \_remoto](https://msdn.microsoft.com/library/mt703714) para um procedimento armazenado que executa uma instrução de Transact-SQL num único remoto SQL Database do Azure ou um conjunto de bases de dados que serve como fragmentos num esquema de partições horizontal.
