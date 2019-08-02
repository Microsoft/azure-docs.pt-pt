---
title: Relatório entre bancos de dados de nuvem expandidos (particionamento horizontal) | Microsoft Docs
description: Use consultas de banco de dados de banco de dados cruzado para relatar em vários bancos de dados.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein
ms.date: 12/18/2018
ms.openlocfilehash: cc59d7cb1ce09aad834130818e5af533719e04c1
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568614"
---
# <a name="report-across-scaled-out-cloud-databases-preview"></a>Relatório entre bancos de dados de nuvem expandidos (visualização)

Você pode criar relatórios de vários bancos de dados SQL do Azure de um único ponto de conexão usando uma [consulta elástica](sql-database-elastic-query-overview.md). Os bancos de dados devem ser particionados horizontalmente (também conhecido como "fragmentado").

Se você tiver um banco de dados existente, consulte Migrando bancos de dados [existentes para bancos de](sql-database-elastic-convert-to-use-elastic-tools.md)dados escalados horizontalmente.

Para entender os objetos SQL necessários para consultar, consulte [consultar em bancos de dados particionados horizontalmente](sql-database-elastic-query-horizontal-partitioning.md).

## <a name="prerequisites"></a>Pré-requisitos

Baixe e execute o [exemplo introdução às ferramentas de banco de dados elástico](sql-database-elastic-scale-get-started.md).

## <a name="create-a-shard-map-manager-using-the-sample-app"></a>Criar um Gerenciador de mapa de fragmentos usando o aplicativo de exemplo
Aqui, você criará um Gerenciador de mapa de fragmentos junto com vários fragmentos, seguidos pela inserção de dados nos fragmentos. Se você já tiver os fragmentos configurados com dados fragmentados neles, poderá ignorar as etapas a seguir e mover para a próxima seção.

1. Compile e execute o aplicativo de exemplo **introdução às ferramentas de banco de dados elástico** . Siga as etapas até a etapa 7 na seção [baixar e executar o aplicativo de exemplo](sql-database-elastic-scale-get-started.md#download-and-run-the-sample-app). No final da etapa 7, você verá o seguinte prompt de comando:

    ![prompt de comando][1]
2. Na janela de comando, digite "1" e pressione **Enter**. Isso cria o Gerenciador de mapa de fragmentos e adiciona dois fragmentos ao servidor. Em seguida, digite "3" e pressione **Enter**; Repita a ação quatro vezes. Isso insere linhas de dados de exemplo em seus fragmentos.
3. O [portal do Azure](https://portal.azure.com) deve mostrar três novos bancos de dados em seu servidor:

   ![Confirmação do Visual Studio][2]

   Neste ponto, há suporte para consultas entre bancos de dados por meio das bibliotecas de cliente do banco de dados elástico. Por exemplo, use a opção 4 na janela de comando. Os resultados de uma consulta de vários fragmentos sempre são uma **União de todos** os resultados de todos os fragmentos.

   Na próxima seção, criamos um ponto de extremidade de banco de dados de exemplo que oferece suporte a consultas mais ricas nos fragmentos.

## <a name="create-an-elastic-query-database"></a>Criar um banco de dados de consulta elástica
1. Abra o [portal do Azure](https://portal.azure.com) e faça logon.
2. Crie um novo banco de dados SQL do Azure no mesmo servidor que a configuração de seu fragmento. Nomeie o banco de dados "ElasticDBQuery".

    ![portal do Azure e tipo de preço][3]

    > [!NOTE]
    > Você pode usar um banco de dados existente. Se você puder fazer isso, ele não deverá ser um dos fragmentos nos quais você gostaria de executar suas consultas. Esse banco de dados será usado para criar os objetos de metadados para uma consulta de banco de dados elástico.
    >

## <a name="create-database-objects"></a>Criar objetos de banco de dados
### <a name="database-scoped-master-key-and-credentials"></a>Chave mestra com escopo de banco de dados e credenciais
Eles são usados para se conectar ao Gerenciador de mapa de fragmentos e aos fragmentos:

1. Abra SQL Server Management Studio ou SQL Server Data Tools no Visual Studio.
2. Conecte-se ao banco de dados ElasticDBQuery e execute os seguintes comandos T-SQL:

        CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<password>';

        CREATE DATABASE SCOPED CREDENTIAL ElasticDBQueryCred
        WITH IDENTITY = '<username>',
        SECRET = '<password>';

    "username" e "password" devem ser iguais às informações de logon usadas na etapa 6 do [download e executar o aplicativo de exemplo](sql-database-elastic-scale-get-started.md#download-and-run-the-sample-app) em [introdução às ferramentas de banco de dados elástico](sql-database-elastic-scale-get-started.md).

### <a name="external-data-sources"></a>Origens de dados externas
Para criar uma fonte de dados externa, execute o seguinte comando no banco de ElasticDBQuery:

    CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc WITH
      (TYPE = SHARD_MAP_MANAGER,
      LOCATION = '<server_name>.database.windows.net',
      DATABASE_NAME = 'ElasticScaleStarterKit_ShardMapManagerDb',
      CREDENTIAL = ElasticDBQueryCred,
       SHARD_MAP_NAME = 'CustomerIDShardMap'
    ) ;

 "CustomerIDShardMap" é o nome do mapa de fragmentos, se você criou o mapa de fragmentos e o Gerenciador de mapa de fragmentos usando o exemplo de ferramentas de banco de dados elástico. No entanto, se você usou a configuração personalizada para este exemplo, ele deve ser o nome do mapa de fragmentos que você escolheu em seu aplicativo.

### <a name="external-tables"></a>Tabelas externas
Crie uma tabela externa que corresponda à tabela Customers nos fragmentos executando o seguinte comando no banco de dados ElasticDBQuery:

    CREATE EXTERNAL TABLE [dbo].[Customers]
    ( [CustomerId] [int] NOT NULL,
      [Name] [nvarchar](256) NOT NULL,
      [RegionId] [int] NOT NULL)
    WITH
    ( DATA_SOURCE = MyElasticDBQueryDataSrc,
      DISTRIBUTION = SHARDED([CustomerId])
    ) ;

## <a name="execute-a-sample-elastic-database-t-sql-query"></a>Executar uma consulta T-SQL de banco de dados elástico de exemplo
Depois de definir sua fonte de dados externa e suas tabelas externas, agora você pode usar o T-SQL completo em suas tabelas externas.

Execute esta consulta no banco de dados ElasticDBQuery:

    select count(CustomerId) from [dbo].[Customers]

Você observará que a consulta agrega resultados de todos os fragmentos e fornece a seguinte saída:

![Detalhes da saída][4]

## <a name="import-elastic-database-query-results-to-excel"></a>Importar resultados da consulta do banco de dados elástico para o Excel
 Você pode importar os resultados de uma consulta para um arquivo do Excel.

1. Inicie o Excel 2013.
2. Navegue até a faixa de de **dados** .
3. Clique em **de outras fontes** e clique em **de SQL Server**.

   ![Importação do Excel de outras fontes][5]
4. No **Assistente de conexão de dados** , digite o nome do servidor e as credenciais de logon. Clique depois em **Seguinte**.
5. Na caixa de diálogo, **Selecione o banco de dados que contém o dado que você deseja**, selecione o banco de **ElasticDBQuery** .
6. Selecione a tabela **clientes** na exibição de lista e clique em **Avançar**. Em seguida, clique em **concluir**.
7. No formulário **importar dados** , em **selecionar como você deseja exibir esses dados em sua pasta de trabalho**, selecione **tabela** e clique em **OK**.

Todas as linhas da tabela Customers, armazenadas em fragmentos diferentes, preenchem a planilha do Excel.

Agora você pode usar as poderosas funções de visualização de dados do Excel. Você pode usar a cadeia de conexão com o nome do servidor, o nome do banco de dados e as credenciais para conectar suas ferramentas de integração de dados e BI ao banco de dado de consulta elástica. Verifique se SQL Server tem suporte como uma fonte de dados para sua ferramenta. Você pode consultar o banco de dados de consulta elástica e tabelas externas, assim como qualquer outro banco de dados SQL Server e SQL Server tabelas às quais você se conectaria com sua ferramenta.

### <a name="cost"></a>Custo
Não há nenhum custo adicional para usar o recurso de consulta de banco de dados elástico.

Para obter informações sobre preços, consulte [detalhes de preços do banco de dados SQL](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="next-steps"></a>Passos Seguintes

* Para obter uma visão geral da consulta elástica, consulte [visão geral da consulta elástica](sql-database-elastic-query-overview.md).
* Para obter um tutorial de particionamento vertical, consulte [introdução à consulta entre bancos de dados (particionamento vertical)](sql-database-elastic-query-getting-started-vertical.md).
* Para obter sintaxe e exemplos de consultas para dados particionados verticalmente, consulte [consultando dados particionados verticalmente)](sql-database-elastic-query-vertical-partitioning.md)
* Para obter sintaxe e exemplos de consultas para dados particionados horizontalmente, consulte [consultando dados particionados horizontalmente)](sql-database-elastic-query-horizontal-partitioning.md)
* Confira [\_SP \_execute Remote](https://msdn.microsoft.com/library/mt703714) para um procedimento armazenado que execute uma instrução Transact-SQL em um único banco de dados SQL do Azure remoto ou em conjunto de dados que servem como fragmentos em um esquema de particionamento horizontal.


<!--Image references-->
[1]: ./media/sql-database-elastic-query-getting-started/cmd-prompt.png
[2]: ./media/sql-database-elastic-query-getting-started/portal.png
[3]: ./media/sql-database-elastic-query-getting-started/tiers.png
[4]: ./media/sql-database-elastic-query-getting-started/details.png
[5]: ./media/sql-database-elastic-query-getting-started/exel-sources.png
<!--anchors-->
