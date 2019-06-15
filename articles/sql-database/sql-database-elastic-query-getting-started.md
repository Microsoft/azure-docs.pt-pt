---
title: Relatórios entre bases de dados de cloud aumentadas horizontalmente (criação de partições horizontais) | Documentos da Microsoft
description: Utilize a base de dados entre várias consultas de bases de dados ao relatório em várias bases de dados.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein
manager: craigg
ms.date: 12/18/2018
ms.openlocfilehash: a73938c98ebaea310875f0db8b665d0f1aed55e8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60556275"
---
# <a name="report-across-scaled-out-cloud-databases-preview"></a>Relatórios entre bases de dados de cloud aumentadas horizontalmente (pré-visualização)

Pode criar relatórios a partir de várias bases de dados SQL do Azure de um ponto de ligação única com uma [consulta elástica](sql-database-elastic-query-overview.md). As bases de dados tem de ser particionados horizontalmente (também conhecido como "em partição horizontal").

Se tiver uma base de dados existente, veja [bases de dados existentes do tópico migrar para bases de dados aumentadas horizontalmente](sql-database-elastic-convert-to-use-elastic-tools.md).

Para compreender os objetos SQL necessários para consultar, consulte [consultas entre bases de dados particionadas horizontalmente](sql-database-elastic-query-horizontal-partitioning.md).

## <a name="prerequisites"></a>Pré-requisitos

Transferir e executar o [introdução ao exemplo de ferramentas de bases de dados elásticas](sql-database-elastic-scale-get-started.md).

## <a name="create-a-shard-map-manager-using-the-sample-app"></a>Criar uma partição horizontal Gestor de mapas de utilizar a aplicação de exemplo
Aqui, irá criar um mapa de partições horizontais manager juntamente com vários fragmentos, seguido de inserção de dados em partições horizontais. Se já tiver o programa de configuração de partições horizontais com dados em partição horizontal nos mesmos, pode ignorar os passos seguintes e mover para a secção seguinte.

1. Criar e executar o **introdução às ferramentas de bases de dados elásticas** aplicação de exemplo. Siga os passos até o passo 7 na secção [transferir e executar a aplicação de exemplo](sql-database-elastic-scale-get-started.md#download-and-run-the-sample-app). No final do passo 7, verá a seguinte linha de comandos:

    ![linha de comandos][1]
2. Na janela de comandos, escreva "1" e prima **Enter**. Esta ação cria a partição horizontal Gestor de mapas e adiciona as partições dois horizontais para o servidor. Em seguida, escreva "3" e prima **Enter**; repita a ação de quatro vezes. Isso insere as linhas de dados de exemplo em suas partições horizontais.
3. O [portal do Azure](https://portal.azure.com) deve mostrar três novas bases de dados no seu servidor:

   ![Confirmação do Visual Studio][2]

   Neste momento, as consultas entre bases de dados são suportadas através das bibliotecas de cliente da base de dados elástica. Por exemplo, utilize a opção 4 na janela de comando. Os resultados de uma consulta de vários fragmentos são sempre um **UNION ALL** dos resultados de todas as partições horizontais.

   Na próxima seção, vamos criar um ponto de extremidade de base de dados de exemplo que oferece suporte a consultas mais avançado dos dados entre partições horizontais.

## <a name="create-an-elastic-query-database"></a>Criar uma base de dados de consulta elástica
1. Abra o [portal do Azure](https://portal.azure.com) e iniciar sessão.
2. Crie uma nova base de dados SQL do Azure no mesmo servidor que a configuração de partição horizontal. Nome da base de dados "ElasticDBQuery."

    ![Portal do Azure e o escalão de preço][3]

    > [!NOTE]
    > Pode utilizar uma base de dados existente. Se fizer isso, tem de ser uma das partições horizontais que gostaria de executar as suas consultas em não. Esta base de dados será utilizado para criar os objetos de metadados de uma consulta de base de dados elástica.
    >

## <a name="create-database-objects"></a>Criar objetos de base de dados
### <a name="database-scoped-master-key-and-credentials"></a>Chave mestra do banco de dados no âmbito e as credenciais
Estes são utilizados para ligar ao Gestor de mapas de partições horizontais e as partições horizontais:

1. Abra o SQL Server Management Studio ou o SQL Server Data Tools no Visual Studio.
2. Ligue à base de dados de ElasticDBQuery e execute os seguintes comandos T-SQL:

        CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<password>';

        CREATE DATABASE SCOPED CREDENTIAL ElasticDBQueryCred
        WITH IDENTITY = '<username>',
        SECRET = '<password>';

    "nomedeutilizador" e "password" devem ser o mesmo que as informações de início de sessão utilizadas no passo 6 da [transferir e executar a aplicação de exemplo](sql-database-elastic-scale-get-started.md#download-and-run-the-sample-app) na [introdução às ferramentas de bases de dados elásticas](sql-database-elastic-scale-get-started.md).

### <a name="external-data-sources"></a>Origens de dados externas
Para criar uma origem de dados externa, execute o seguinte comando na base de dados ElasticDBQuery:

    CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc WITH
      (TYPE = SHARD_MAP_MANAGER,
      LOCATION = '<server_name>.database.windows.net',
      DATABASE_NAME = 'ElasticScaleStarterKit_ShardMapManagerDb',
      CREDENTIAL = ElasticDBQueryCred,
       SHARD_MAP_NAME = 'CustomerIDShardMap'
    ) ;

 "CustomerIDShardMap" é o nome do mapa de partições horizontais, se tiver criado o mapa de partições horizontais e o Gestor de mapas de partições horizontais com o exemplo de ferramentas de bases de dados elásticas. No entanto, se utilizou a sua configuração personalizada para este exemplo, em seguida, deve ser o nome do mapa de partições horizontais que escolheu na sua aplicação.

### <a name="external-tables"></a>Tabelas externas
Crie uma tabela externa que corresponde à tabela de clientes em partições horizontais, executando o seguinte comando na base de dados de ElasticDBQuery:

    CREATE EXTERNAL TABLE [dbo].[Customers]
    ( [CustomerId] [int] NOT NULL,
      [Name] [nvarchar](256) NOT NULL,
      [RegionId] [int] NOT NULL)
    WITH
    ( DATA_SOURCE = MyElasticDBQueryDataSrc,
      DISTRIBUTION = SHARDED([CustomerId])
    ) ;

## <a name="execute-a-sample-elastic-database-t-sql-query"></a>Executar uma consulta de T-SQL da base de dados elástica de exemplo
Depois de definir a origem de dados externos e as tabelas externas podem agora utilizar o T-SQL completa sobre as tabelas externas.

Execute esta consulta na base de dados ElasticDBQuery:

    select count(CustomerId) from [dbo].[Customers]

Observará que a consulta agrega os resultados de todas as partições horizontais e oferece a seguinte saída:

![Detalhes de saída][4]

## <a name="import-elastic-database-query-results-to-excel"></a>Importar os resultados da consulta de base de dados elástica para Excel
 Pode importar os resultados de uma consulta para um ficheiro do Excel.

1. Inicie o Excel 2013.
2. Navegue para o **dados** faixa de opções.
3. Clique em **de outras origens** e clique em **do SQL Server**.

   ![Importação de Excel a partir de outras origens][5]
4. Na **Assistente de ligação de dados** escreva as credenciais de início de sessão e o nome do servidor. Clique depois em **Seguinte**.
5. Na caixa de diálogo **selecione a base de dados que contém os dados que pretende**, selecione a **ElasticDBQuery** base de dados.
6. Selecione o **os clientes** da tabela na vista de lista e clique em **próxima**. Em seguida, clique em **concluir**.
7. Na **importar dados** formam, em **Selecione como pretende ver estes dados no seu livro**, selecione **tabela** e clique em **OK**.

Todas as linhas da **clientes** tabela, armazenada em partições horizontais diferentes preencher a folha do Excel.

Agora, pode utilizar as funções de visualização de dados poderoso do Excel. Pode utilizar a cadeia de ligação com o nome do servidor, nome de base de dados e as credenciais para ligar as suas ferramentas de integração BI e os dados na base de dados de consulta elástica. Certifique-se de que o SQL Server é suportado como uma origem de dados para a sua ferramenta. Pode consultar a base de dados de consulta elástica e tabelas externas, assim como qualquer outro banco de dados do SQL Server e tabelas do SQL Server que iria a estabelecer ligação com a sua ferramenta.

### <a name="cost"></a>Custo
Não é sem custos adicionais para utilizar a funcionalidade de consulta de base de dados elástica.

Para obter informações sobre preços, consulte [detalhes de preços de base de dados do SQL](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="next-steps"></a>Passos Seguintes

* Para obter uma descrição geral de consulta elástica, veja [descrição geral de consulta elástica](sql-database-elastic-query-overview.md).
* Para obter um tutorial de criação de partições vertical, consulte [guia de introdução consultas entre bases de dados (criação de partições verticais)](sql-database-elastic-query-getting-started-vertical.md).
* Para consultas de exemplo de sintaxe e para dados particionados verticalmente, consulte [consultar verticalmente particionada dados)](sql-database-elastic-query-vertical-partitioning.md)
* Para consultas de exemplo de sintaxe e para dados particionados horizontalmente, consulte [horizontalmente a consulta particionados dados)](sql-database-elastic-query-horizontal-partitioning.md)
* Ver [sp\_execute \_remoto](https://msdn.microsoft.com/library/mt703714) para um procedimento armazenado que executa uma instrução de Transact-SQL num único remoto SQL Database do Azure ou um conjunto de bases de dados que serve como fragmentos num esquema de partições horizontal.


<!--Image references-->
[1]: ./media/sql-database-elastic-query-getting-started/cmd-prompt.png
[2]: ./media/sql-database-elastic-query-getting-started/portal.png
[3]: ./media/sql-database-elastic-query-getting-started/tiers.png
[4]: ./media/sql-database-elastic-query-getting-started/details.png
[5]: ./media/sql-database-elastic-query-getting-started/exel-sources.png
<!--anchors-->
