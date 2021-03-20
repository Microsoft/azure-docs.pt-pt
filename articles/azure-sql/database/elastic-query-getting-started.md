---
title: Relatório através de bases de dados de nuvem escalonadas
description: Utilize consultas de base de dados cruzadas para reportar em várias bases de dados.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein
ms.date: 10/10/2019
ms.openlocfilehash: 586dad7439cc57ed2c863ee5f6692e12f7a78c50
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92781231"
---
# <a name="report-across-scaled-out-cloud-databases-preview"></a>Relatório através de bases de dados de nuvem escalonadas (pré-visualização)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Pode criar relatórios a partir de várias bases de dados a partir de um único ponto de ligação utilizando uma [consulta elástica](elastic-query-overview.md). As bases de dados devem ser divididas horizontalmente (também conhecidas como "fragmentos").

Se tiver uma base de dados existente, consulte [bases de dados existentes em migração para bases de dados em escala.](elastic-convert-to-use-elastic-tools.md)

Para compreender os objetos SQL necessários para consultar, consulte [a Consulta através de bases de dados horizontalmente divididas.](elastic-query-horizontal-partitioning.md)

## <a name="prerequisites"></a>Pré-requisitos

Descarregue e execute a [amostra de ferramentas Elásticas database](elastic-scale-get-started.md).

## <a name="create-a-shard-map-manager-using-the-sample-app"></a>Crie um gestor de mapas de fragmentos usando a aplicação da amostra
Aqui irá criar um gestor de mapas de fragmentos juntamente com vários fragmentos, seguido da inserção de dados nos fragmentos. Se por acaso já tiver fragmentos configurados com dados fragmentos, pode saltar os passos seguintes e passar para a secção seguinte.

1. Construa e execute a aplicação de amostra **de ferramentas elastic Database** seguindo os passos na secção de [artigos Descarregue e execute a aplicação da amostra](elastic-scale-get-started.md#download-and-run-the-sample-app-1). Assim que terminar todos os passos, verá o seguinte pedido de comando:

    ![comando pronta][1]
2. Na janela de comando, escreva "1" e prima **Enter**. Isto cria o gestor de mapas de fragmentos e adiciona dois fragmentos ao servidor. Em seguida, escreva "3" e prima **Enter;** repetir a ação quatro vezes. Isto insere linhas de dados de amostra nos seus fragmentos.
3. O [portal Azure](https://portal.azure.com) deve apresentar três novas bases de dados no seu servidor:

   ![Confirmação do Estúdio Visual][2]

   Neste momento, as consultas de base de dados cruzadas são suportadas através das bibliotecas de clientes elastic Database. Por exemplo, utilize a opção 4 na janela de comando. Os resultados de uma consulta multi-fragmentos são sempre uma **UNIÃO TODOS** os resultados de todos os fragmentos.

   Na secção seguinte, criamos um ponto final de base de dados de amostra que suporta uma consulta mais rica dos dados através de fragmentos.

## <a name="create-an-elastic-query-database"></a>Criar uma base de dados de consulta elástica

1. Abra o [portal Azure](https://portal.azure.com) e faça login.
2. Crie uma nova base de dados na Base de Dados Azure SQL no mesmo servidor que a configuração do seu fragmento. Nomeie a base de dados "ElasticDBQuery".

    ![Portal Azure e nível de preços][3]

    > [!NOTE]
    > pode utilizar uma base de dados existente. Se puder fazê-lo, não deve ser um dos fragmentos em que gostaria de executar as suas consultas. Esta base de dados será utilizada para criar os objetos de metadados para uma consulta de base de dados elástica.
    >

## <a name="create-database-objects"></a>Criar objetos de base de dados
### <a name="database-scoped-master-key-and-credentials"></a>Chave e credenciais principais de âmbito de base de dados
Estes são usados para ligar ao gestor de mapas de fragmentos e aos fragmentos:

1. Abra o SQL Server Management Studio ou as Ferramentas de Dados do Servidor SQL em Estúdio Visual.
2. Ligue à base de dados ElasticDBQuery e execute os seguintes comandos T-SQL:

    ```tsql
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<master_key_password>';

    CREATE DATABASE SCOPED CREDENTIAL ElasticDBQueryCred
    WITH IDENTITY = '<username>',
    SECRET = '<password>';
    ```

    "username" e "password" devem ser os mesmos que as informações de login utilizadas no passo 3 da secção [Descarregue e execute a aplicação de amostra](elastic-scale-get-started.md#download-and-run-the-sample-app) na Obtenção iniciada com artigo **de ferramentas Elastic Database.**

### <a name="external-data-sources"></a>Origens de dados externas
Para criar uma fonte de dados externa, execute o seguinte comando na base de dados ElasticDBQuery:

```tsql
CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc WITH
    (TYPE = SHARD_MAP_MANAGER,
    LOCATION = '<server_name>.database.windows.net',
    DATABASE_NAME = 'ElasticScaleStarterKit_ShardMapManagerDb',
    CREDENTIAL = ElasticDBQueryCred,
    SHARD_MAP_NAME = 'CustomerIDShardMap'
) ;
```    

 "CustomerIDShardMap" é o nome do mapa de fragmentos, se criou o mapa de fragmentos e o gestor de mapas de fragmentos utilizando a amostra de ferramentas de base de dados elásticas. No entanto, se usou a sua configuração personalizada para esta amostra, então deve ser o nome do mapa de fragmentos que escolheu na sua aplicação.

### <a name="external-tables"></a>Tabelas externas
Criar uma tabela externa que corresponda à tabela clientes nos fragmentos executando o seguinte comando na base de dados ElasticDBQuery:

```tsql
CREATE EXTERNAL TABLE [dbo].[Customers]
( [CustomerId] [int] NOT NULL,
    [Name] [nvarchar](256) NOT NULL,
    [RegionId] [int] NOT NULL)
WITH
( DATA_SOURCE = MyElasticDBQueryDataSrc,
    DISTRIBUTION = SHARDED([CustomerId])
) ;
```

## <a name="execute-a-sample-elastic-database-t-sql-query"></a>Execute uma consulta de base de dados elástica de amostra T-SQL
Depois de definir a sua fonte de dados externa e as suas tabelas externas, pode agora utilizar o T-SQL completo sobre as suas tabelas externas.

Execute esta consulta na base de dados ElasticDBQuery:

```tsql
select count(CustomerId) from [dbo].[Customers]
```

Notará que os agregados de consultas resultam de todos os fragmentos e dão a seguinte saída:

![Detalhes da saída][4]

## <a name="import-elastic-database-query-results-to-excel"></a>Importar resultados de consulta de base de dados elásticas para o Excel
 Pode importar os resultados de uma consulta para um ficheiro Excel.

1. Lançamento Excel 2013.
2. Navegue para a fita **Data.**
3. Clique **em Outras Fontes** e clique **no SqL Server**.

   ![Excel importa de outras fontes][5]
4. No Assistente de Ligação de **Dados** digite o nome do servidor e as credenciais de login. Em seguida, clique em **Seguinte**.
5. Na caixa de diálogo **Selecione a base de dados que contém os dados que pretende,** selecione a base de dados **ElasticDBQuery.**
6. Selecione a tabela **Clientes** na vista da lista e clique em **Seguinte**. Em seguida, clique em **Concluir**.
7. No formulário **Dados de Importação,** em **Selecione como pretende ver estes dados no seu livro,** selecione **Tabela** e clique **em OK**.

Todas as linhas da tabela **Clientes,** armazenadas em fragmentos diferentes povoam a folha excel.

Agora pode utilizar as poderosas funções de visualização de dados do Excel. Pode utilizar a cadeia de ligação com o nome do servidor, nome da base de dados e credenciais para ligar o seu BI e ferramentas de integração de dados à base de dados de consultas elásticas. Certifique-se de que o SQL Server é suportado como fonte de dados para a sua ferramenta. Pode consultar a base de dados de consultas elásticas e as tabelas externas, tal como qualquer outra base de dados do SQL Server e as tabelas sql Server a que se ligaria à sua ferramenta.

### <a name="cost"></a>Custo
Não existe qualquer custo adicional para a utilização da função de consulta de base de dados elástica.

Para obter informações sobre preços consulte [detalhes de preços da base de dados SQL](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="next-steps"></a>Passos seguintes

* Para obter uma visão geral da consulta elástica, consulte [a visão geral da consulta elástica](elastic-query-overview.md).
* Para obter um tutorial de partição vertical, consulte [Começar com consulta de base de dados cruzada (partição vertical)](elastic-query-getting-started-vertical.md).
* Para consultas de sintaxe e amostra para dados verticalmente divididos, consulte [consulta de dados partidos verticalmente)](elastic-query-vertical-partitioning.md)
* Para consultas de sintaxe e amostra para dados com divisórias horizontais, consulte [consulta de dados com partição horizontal)](elastic-query-horizontal-partitioning.md)
* Consulte [o sp executar o controlo \_ \_ remoto](/sql/relational-databases/system-stored-procedures/sp-execute-remote-azure-sql-database) para um procedimento armazenado que execute uma declaração Transact-SQL numa única base de dados remota do Azure SQL ou num conjunto de bases de dados que servem de fragmentos num esquema de partição horizontal.


<!--Image references-->
[1]: ./media/elastic-query-getting-started/cmd-prompt.png
[2]: ./media/elastic-query-getting-started/portal.png
[3]: ./media/elastic-query-getting-started/tiers.png
[4]: ./media/elastic-query-getting-started/details.png
[5]: ./media/elastic-query-getting-started/exel-sources.png
<!--anchors-->