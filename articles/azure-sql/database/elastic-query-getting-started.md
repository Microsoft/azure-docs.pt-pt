---
title: Relatório através de bases de dados de nuvem escaldada
description: Utilize consultas de base de dados cruzadas para reportar em várias bases de dados.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein
ms.date: 10/10/2019
ms.openlocfilehash: 871ff0fe7fdf92e82b30b1c93867d753ce9a82b0
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84048526"
---
# <a name="report-across-scaled-out-cloud-databases-preview"></a>Relatório através de bases de dados de nuvem escalada (pré-visualização)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Pode criar relatórios a partir de várias bases de dados a partir de um único ponto de ligação utilizando uma [consulta elástica](elastic-query-overview.md). As bases de dados devem ser divididas horizontalmente (também conhecidas como "fragmentos").

Se tiver uma base de dados existente, consulte as bases de dados existentes para bases [de dados dimensionadas](elastic-convert-to-use-elastic-tools.md).

Para compreender os objetos SQL necessários para consultar, consulte a Consulta através de bases de [dados horizontalmente divididas](elastic-query-horizontal-partitioning.md).

## <a name="prerequisites"></a>Pré-requisitos

Descarregue e execute o Getting started com a amostra de [ferramentas de Base de Dados Elásticas](elastic-scale-get-started.md).

## <a name="create-a-shard-map-manager-using-the-sample-app"></a>Criar um gestor de mapas de fragmentos usando a app de amostra
Aqui você vai criar um gestor de mapas de fragmentos juntamente com vários fragmentos, seguido de inserção de dados nos fragmentos. Se por acaso já tiver fragmentos configurados com dados fragmentos, pode saltar os seguintes passos e passar para a secção seguinte.

1. Construa e execute o **Getting started com elástico ferramentas de base** de dados aplicação, seguindo os passos na secção de [artigoS Descarregue e execute a aplicação de amostra](elastic-scale-get-started.md#download-and-run-the-sample-app-1). Assim que terminar todos os passos, verá o seguinte pedido de comando:

    ![pedido de comando][1]
2. Na janela de comando, escreva "1" e prima **Enter**. Isto cria o gestor de mapas de fragmentos, e adiciona dois fragmentos ao servidor. Em seguida, digite "3" e prima **Enter;** repetir a ação quatro vezes. Isto insere as linhas de dados da amostra nos seus fragmentos.
3. O [portal Azure](https://portal.azure.com) deve apresentar três novas bases de dados no seu servidor:

   ![Confirmação do Estúdio Visual][2]

   Neste ponto, as consultas de base de dados cruzadas são suportadas através das bibliotecas de clientes da Base de Dados Elástica. Por exemplo, utilize a opção 4 na janela de comando. Os resultados de uma consulta multi-fragmentos são sempre uma **UNIÃO TODOS** os resultados de todos os fragmentos.

   Na secção seguinte, criamos um ponto final de base de dados de amostras que suporta a consulta mais rica dos dados em fragmentos.

## <a name="create-an-elastic-query-database"></a>Criar uma base de dados de consulta elástica

1. Abra o [portal Azure](https://portal.azure.com) e faça login.
2. Crie uma nova base de dados na Base de Dados Azure SQL no mesmo servidor que a sua configuração de fragmentos. Nomeie a base de dados "ElasticDBQuery".

    ![Portal azure e nível de preços][3]

    > [!NOTE]
    > pode utilizar uma base de dados existente. Se o conseguires fazer, não deve ser um dos cacos em que gostarias de executar as tuas perguntas. Esta base de dados será utilizada para a criação dos objetos de metadados para uma consulta de base de dados elástica.
    >

## <a name="create-database-objects"></a>Criar objetos de base de dados
### <a name="database-scoped-master-key-and-credentials"></a>Chave e credenciais de base de dados com âmbito de base de dados
Estes são usados para ligar ao gestor de mapas de fragmentos e aos fragmentos:

1. Abra o Estúdio de Gestão de Servidores SQL ou as Ferramentas de Dados do Servidor SQL no Estúdio Visual.
2. Ligue-se à base de dados ElasticDBQuery e execute os seguintes comandos T-SQL:

        CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<master_key_password>';

        CREATE DATABASE SCOPED CREDENTIAL ElasticDBQueryCred
        WITH IDENTITY = '<username>',
        SECRET = '<password>';

    "username" e "password" devem ser os mesmos que as informações de login utilizadas no passo 3 da secção [Descarregue e execute a aplicação de amostra](elastic-scale-get-started.md#download-and-run-the-sample-app) no **artigo de ferramentas** De base de dados elástica.

### <a name="external-data-sources"></a>Fontes de dados externas
Para criar uma fonte de dados externa, execute o seguinte comando na base de dados ElasticDBQuery:

    CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc WITH
      (TYPE = SHARD_MAP_MANAGER,
      LOCATION = '<server_name>.database.windows.net',
      DATABASE_NAME = 'ElasticScaleStarterKit_ShardMapManagerDb',
      CREDENTIAL = ElasticDBQueryCred,
       SHARD_MAP_NAME = 'CustomerIDShardMap'
    ) ;

 "CustomerIDShardMap" é o nome do mapa do fragmento, se criou o mapa de fragmentos e o gestor de mapas de fragmentos utilizando a amostra de ferramentas de base de dados elástica. No entanto, se usou a sua configuração personalizada para esta amostra, então deve ser o nome do mapa de fragmentos que escolheu na sua aplicação.

### <a name="external-tables"></a>Tabelas externas
Crie uma tabela externa que corresponda à tabela Clientes nos fragmentos executando o seguinte comando na base de dados ElasticDBQuery:

    CREATE EXTERNAL TABLE [dbo].[Customers]
    ( [CustomerId] [int] NOT NULL,
      [Name] [nvarchar](256) NOT NULL,
      [RegionId] [int] NOT NULL)
    WITH
    ( DATA_SOURCE = MyElasticDBQueryDataSrc,
      DISTRIBUTION = SHARDED([CustomerId])
    ) ;

## <a name="execute-a-sample-elastic-database-t-sql-query"></a>Executar uma consulta de base de dados elástica t-SQL de amostra
Depois de definir a sua fonte de dados externo e as suas tabelas externas, pode agora utilizar t-SQL completo sobre as suas tabelas externas.

Execute esta consulta na base de dados ElasticDBQuery:

    select count(CustomerId) from [dbo].[Customers]

Note que os agregados de consulta resultam de todos os fragmentos e dá a seguinte saída:

![Detalhes da saída][4]

## <a name="import-elastic-database-query-results-to-excel"></a>Importar resultados de consulta de base de dados elástica seletos para Excel
 Pode importar os resultados de uma consulta para um ficheiro Excel.

1. Lançamento Excel 2013.
2. Navegue para a fita **Data.**
3. Clique **em Outras Fontes** e clique **no Servidor SQL**.

   ![Excel importa de outras fontes][5]
4. No Assistente de **Ligação** de Dados digite o nome do servidor e as credenciais de login. Em seguida, clique em **Seguinte**.
5. Na caixa de diálogo **Selecione a base**de dados que contém os dados que deseja , selecione a base de dados **ElasticDBQuery.**
6. Selecione a tabela **Clientes** na vista da lista e clique **em Next**. Em seguida, clique em **Concluir**.
7. No formulário **Dados de Importação,** em **Select como pretende visualizar estes dados no seu livro**de trabalho, selecione **Tabela** e clique **EM OK**.

Todas as filas da tabela **Clientes,** armazenadas em diferentes fragmentos povoam a folha excel.

Agora pode utilizar as poderosas funções de visualização de dados do Excel. Pode utilizar a cadeia de ligação com o nome do servidor, nome da base de dados e credenciais para ligar as ferramentas de BIO e integração de dados à base de dados de consulta elástica. Certifique-se de que o SQL Server é suportado como fonte de dados para a sua ferramenta. Pode consultar a base de dados de consultas elásticas e as tabelas externas, tal como qualquer outra base de dados do SQL Server e as tabelas do SQL Server a que se ligaria à sua ferramenta.

### <a name="cost"></a>Custo
Não existe qualquer custo adicional para a utilização da funcionalidade Deconsulta de Base de Dados Elástica.

Para obter informações sobre preços consulte os detalhes de preços da base de [dados SQL](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="next-steps"></a>Próximos passos

* Para uma visão geral da consulta elástica, consulte a visão geral da [consulta elástica](elastic-query-overview.md).
* Para um tutorial de partição vertical, consulte [Iniciar-se com consulta de base de dados cruzada (partição vertical)](elastic-query-getting-started-vertical.md).
* Para consultas de sintaxe e amostras para dados verticalmente divididos, consulte a [consulta de dados verticalmente divididos)](elastic-query-vertical-partitioning.md)
* Para consultas de sintaxe e amostras para dados horizontalmente divididos, consulte dados [horizontalmente divididos)](elastic-query-horizontal-partitioning.md)
* Consulte o [sp \_ execute o controlo \_ remoto](https://msdn.microsoft.com/library/mt703714) para um procedimento armazenado que execute uma declaração Transact-SQL numa única base de dados remota do Azure SQL ou num conjunto de bases de dados que servem como fragmentos num esquema de partição horizontal.


<!--Image references-->
[1]: ./media/elastic-query-getting-started/cmd-prompt.png
[2]: ./media/elastic-query-getting-started/portal.png
[3]: ./media/elastic-query-getting-started/tiers.png
[4]: ./media/elastic-query-getting-started/details.png
[5]: ./media/elastic-query-getting-started/exel-sources.png
<!--anchors-->
