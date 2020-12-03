---
title: Em vez de ETL, desenhe ELT
description: Implementar estratégias flexíveis de carregamento de dados para piscinas SQL dedicadas dentro do Azure Synapse Analytics.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/20/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 64ba24eb0eab581310122908fc05d1d671ac1d40
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96531578"
---
# <a name="data-loading-strategies-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Estratégias de carregamento de dados para piscina sql dedicada em Azure Synapse Analytics

As piscinas tradicionais de SQL dedicadas sMP utilizam um processo de extrato, transformação e carga (ETL) para os dados de carregamento. A Synapse SQL, dentro da Azure Synapse Analytics, utiliza arquitetura de processamento de consultas distribuídas que aproveita a escalabilidade e flexibilidade dos recursos de computação e armazenamento.

Utilizando um processo de Extração, Carga e Transformação (ELT) alavanca as capacidades de processamento de consultas distribuídas incorporadas e elimina os recursos necessários para a transformação de dados antes do carregamento.

Enquanto as piscinas SQL dedicadas suportam muitos métodos de carregamento, incluindo opções populares do SQL Server, como o [BCP](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) e a [SqlBulkCopy API,](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)a forma mais rápida e escalável de carregar dados é através de tabelas externas polyBase e da [declaração COPY](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

Com a PolyBase e a declaração COPY, pode aceder a dados externos armazenados no armazenamento Azure Blob ou na Azure Data Lake Store através do idioma T-SQL. Para maior flexibilidade no carregamento, recomendamos a utilização da declaração COPY.


## <a name="what-is-elt"></a>O que é ELT?

Extrato, Carga e Transformação (ELT) é um processo pelo qual os dados são extraídos de um sistema de origem, carregados numa piscina DE SQL dedicada, e depois transformados.

Os passos básicos para a implementação do ELT são:

1. Extraia os dados de origem para ficheiros de texto.
2. Aterre os dados no armazém da Azure Blob ou na Azure Data Lake Store.
3. Prepare os dados para o carregamento.
4. Carregue os dados em tabelas de preparação com o PolyBase ou com o comando COPY.
5. Transforme os dados.
6. Insira os dados em tabelas de produção.

Para um tutorial de carregamento, consulte [os dados de carregamento do armazenamento da bolha Azure](load-data-from-azure-blob-storage-using-polybase.md).

## <a name="1-extract-the-source-data-into-text-files"></a>1. Extrair os dados de origem em ficheiros de texto

A obtenção de dados do seu sistema de origem depende da localização do armazenamento. O objetivo é mover os dados para textos delimitados suportados ou ficheiros CSV.

### <a name="supported-file-formats"></a>Formatos de ficheiro suportados

Com a PolyBase e a declaração COPY, pode carregar dados de UTF-8 e UTF-16 codificados por texto ou ficheiros CSV codificados. Além de ficheiros de texto ou CSV delimitados, carrega a partir dos formatos de ficheiros Hadoop, tais como ORC e Parquet. A PolyBase e a declaração COPY também podem carregar dados de ficheiros comprimidos Gzip e Snappy.

O ASCII alargado, o formato de largura fixa e os formatos aninhados como o WinZip ou o XML não são suportados. Se estiver a exportar do SQL Server, pode utilizar a [ferramenta da linha de comando do BCP](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) para exportar os dados para ficheiros de texto delimitados.

## <a name="2-land-the-data-into-azure-blob-storage-or-azure-data-lake-store"></a>2. Aterre os dados no armazenamento da Azure Blob ou na Azure Data Lake Store

Para aterrar os dados no armazenamento Azure, pode movê-lo para [o armazenamento Azure Blob](../../storage/blobs/storage-blobs-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) ou [para a Azure Data Lake Store Gen2](../../data-lake-store/data-lake-store-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Em qualquer local, os dados devem ser armazenados em ficheiros de texto. A PolyBase e a declaração COPY podem ser carregadas a partir de qualquer local.

Ferramentas e serviços que pode utilizar para mover dados para o Azure Storage:

- O serviço [Azure ExpressRoute](../../expressroute/expressroute-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) melhora a produção, desempenho e previsibilidade da rede. O ExpressRoute é um serviço que encaminha os seus dados através de uma ligação privada dedicada ao Azure. As ligações ExpressRoute não encaminham dados através da internet pública. As ligações oferecem mais fiabilidade, velocidades mais rápidas, latências mais baixas e maior segurança do que as ligações típicas através da internet pública.
- [O utilitário AZCopy](../../storage/common/storage-choose-data-transfer-solution.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) move dados para Azure Storage através da internet pública. Isto funciona se os seus dados forem inferiores a 10 TB. Para efetuar cargas regularmente com AZCopy, teste a velocidade da rede para ver se é aceitável.
- [A Azure Data Factory (ADF)](../../data-factory/introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) tem um portal que pode instalar no seu servidor local. Em seguida, pode criar um pipeline para mover dados do seu servidor local para o Azure Storage. Para utilizar a Data Factory com piscinas SQL dedicadas, consulte [os dados de carregamento para piscinas SQL dedicadas.](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)

## <a name="3-prepare-the-data-for-loading"></a>3. Preparar os dados para o carregamento

Pode ser necessário preparar e limpar os dados na sua conta de armazenamento antes de carregar. A preparação de dados pode ser realizada enquanto os seus dados estão na fonte, à medida que exporta os dados para ficheiros de texto, ou depois de os dados estarem no Azure Storage.  É mais fácil trabalhar com os dados o mais cedo possível no processo.  

### <a name="define-the-tables"></a>Definir as tabelas

Tem primeiro de definir a tabela para a que está a carregar na sua piscina SQL dedicada ao utilizar a declaração COPY.

Se estiver a utilizar o PolyBase, tem de definir tabelas externas na sua piscina SQL dedicada antes de carregar. A PolyBase utiliza tabelas externas para definir e aceder aos dados no Azure Storage. Uma tabela externa é semelhante a uma visão de base de dados. A tabela externa contém o esquema de tabela e aponta para dados que são armazenados fora da piscina de SQL dedicada.

Definir tabelas externas envolve especificar a fonte de dados, o formato dos ficheiros de texto e as definições de tabela. Os artigos de referência de sintaxe T-SQL que necessitará são:

- [CRIAR FONTE DE DADOS EXTERNA](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [CRIAR TABELA EXTERNA](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

Utilize o seguinte mapeamento do tipo de dados SQL ao carregar ficheiros Parquet:

|                         Tipo parquet                         |   Tipo lógico parquet (anotação)   |  Tipo de dados SQL   |
| :----------------------------------------------------------: | :-----------------------------------: | :--------------: |
|                           BOOLEANA                            |                                       |       bit        |
|                     BINÁRIO / BYTE_ARRAY                      |                                       |    varbinário     |
|                            DUPLO                            |                                       |      float       |
|                            FLUTUAR                             |                                       |       real       |
|                            INT32                             |                                       |       int        |
|                            INT64                             |                                       |      bigint      |
|                            INT96                             |                                       |    datetime2     |
|                     FIXED_LEN_BYTE_ARRAY                     |                                       |      binary      |
|                            BINÁRIO                            |                 UTF8                  |     nvarchar     |
|                            BINÁRIO                            |                CORDA                 |     nvarchar     |
|                            BINÁRIO                            |                 ENUM                  |     nvarchar     |
|                            BINÁRIO                            |                 UUID                  | uniqueidentifier |
|                            BINÁRIO                            |                DECIMAL                |     decimal      |
|                            BINÁRIO                            |                 JSON                  |  nvarchar(MAX)   |
|                            BINÁRIO                            |                 Rio BSON                  |  varbinário(máx)  |
|                     FIXED_LEN_BYTE_ARRAY                     |                DECIMAL                |     decimal      |
|                          BYTE_ARRAY                          |               INTERVALO                |  varchar(máx),   |
|                            INT32                             |             INT(8, verdade)              |     smallint     |
|                            INT32                             |            INT(16, verdade)            |     smallint     |
|                            INT32                             |             INT(32, verdade)             |       int        |
|                            INT32                             |            INT(8, falso)            |     tinyint      |
|                            INT32                             |            INT(16, falso)             |       int        |
|                            INT32                             |           INT(32, falso)            |      bigint      |
|                            INT32                             |                 DATE                  |       date       |
|                            INT32                             |                DECIMAL                |     decimal      |
|                            INT32                             |            TEMPO (MILLIS)             |       hora       |
|                            INT64                             |            INT(64, verdade)            |      bigint      |
|                            INT64                             |           INT(64, falso)            |  decimal (20,0)   |
|                            INT64                             |                DECIMAL                |     decimal      |
|                            INT64                             |         TEMPO (MILLIS)                 |       hora       |
|                            INT64                             | TIMETAMP (MILLIS)                  |    datetime2     |
| [Tipo complexo](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fgithub.com%2Fapache%2Fparquet-format%2Fblob%2Fmaster%2FLogicalTypes.md%23lists&data=02\|01\|kevin%40microsoft.com\|19f74d93f5ca45a6b73c08d7d7f5f111\|72f988bf86f141af91ab2d7cd011db47\|1\|0\|637215323617803168&sdata=6Luk047sK26ijTzfvKMYc%2FNu%2Fz0AlLCX8lKKTI%2F8B5o%3D&reserved=0) |                 LISTA                  |   varchar(max)   |
| [Tipo complexo](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fgithub.com%2Fapache%2Fparquet-format%2Fblob%2Fmaster%2FLogicalTypes.md%23maps&data=02\|01\|kevin%40microsoft.com\|19f74d93f5ca45a6b73c08d7d7f5f111\|72f988bf86f141af91ab2d7cd011db47\|1\|0\|637215323617803168&sdata=FiThqXxjgmZBVRyigHzfh5V7Z%2BPZHjud2IkUUM43I7o%3D&reserved=0) |                  MAPA                  |   varchar(max)   |

>[!IMPORTANT] 
>- As piscinas dedicadas SQL não suportam atualmente tipos de dados Parquet com precisão MICROS e NANOS. 
>- Pode experimentar o seguinte erro se os tipos forem desajustados entre Parquet e SQL ou se tiver tipos de dados de Parquet não **suportados: "HdfsBridge::recordReaderFillBuffer - Erro inesperado encontrado a preencher o tampão do leitor de registos: ClassCastException: ..."**
>- O carregamento de um valor fora do intervalo de 0-127 numa coluna minúscula para o formato de ficheiro Parquet e ORC não é suportado.

Para um exemplo de criação de objetos externos, consulte [criar tabelas externas](https://docs.microsoft.com/azure/synapse-analytics/sql/develop-tables-external-tables?tabs=sql-pool).

### <a name="format-text-files"></a>Arquivos de texto de formato

Se estiver a utilizar o PolyBase, os objetos externos definidos precisam de alinhar as linhas dos ficheiros de texto com a definição de quadro e formato de ficheiro externo. Os dados em cada linha do ficheiro de texto devem alinhar-se com a definição da tabela.
Para formatar os ficheiros de texto:

- Se os seus dados são provenientes de uma fonte não relacional, tem de os transformar em linhas e colunas. Quer os dados sejam de uma fonte relacional ou não relacional, os dados devem ser transformados para alinhar com as definições de coluna para a tabela em que planeia carregar os dados.
- Formato de dados no ficheiro de texto para alinhar com as colunas e tipos de dados na tabela de destino. O desalinhamento entre os tipos de dados nos ficheiros de texto externos e a tabela de piscinas SQL dedicada faz com que as filas sejam rejeitadas durante a carga.
- Separe os campos no ficheiro de texto com um exterminador.  Certifique-se de usar um personagem ou uma sequência de caracteres que não seja encontrado nos seus dados de origem. Utilize o exterminador especificado com [FORMATO DE FICHEIRO EXTERNO CREATE](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="4-load-the-data-using-polybase-or-the-copy-statement"></a>4. Carregue os dados utilizando a PolyBase ou a declaração COPY

É melhor a prática carregar dados numa tabela de preparação. As tabelas de preparação permitem-lhe lidar com erros sem interferir com as tabelas de produção. Uma tabela de encenação também lhe dá a oportunidade de usar a arquitetura de processamento paralelo de pool SQL dedicada para transformações de dados antes de inserir os dados em tabelas de produção.

### <a name="options-for-loading"></a>Opções de carregamento

Para carregar dados, pode utilizar qualquer uma destas opções de carregamento:

- A [declaração COPY](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) é o utilitário de carregamento recomendado, pois permite-lhe carregar de forma perfeita e flexível os dados. A declaração tem muitas capacidades de carregamento adicionais que a PolyBase não fornece. 
- [A PolyBase com T-SQL](load-data-from-azure-blob-storage-using-polybase.md) requer que defina objetos de dados externos.
- [A declaração de PolyBase e COPY com a Azure Data Factory (ADF)](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) é outra ferramenta de orquestração.  Define um oleoduto e programa empregos.
- [A PolyBase com o SSIS](/sql/integration-services/load-data-to-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) funciona bem quando os seus dados de origem estão no SQL Server. O SSIS define a fonte para os mapeamentos de mesa de destino, e também orquestra a carga. Se já tiver pacotes SSIS, pode modificar os pacotes para trabalhar com o novo destino do armazém de dados.
- [A PolyBase com a Azure Databricks](../../azure-databricks/databricks-extract-load-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) transfere dados de uma tabela para um dataframe de dados de Databricks e/ou escreve dados de um databricks para uma tabela utilizando a PolyBase.

### <a name="other-loading-options"></a>Outras opções de carregamento

Além da PolyBase e da declaração COPY, pode utilizar o [BCP](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) ou a [API sqlBulkCopy](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). o BCP carrega diretamente para a base de dados sem passar pelo armazenamento Azure Blob, e destina-se apenas a pequenas cargas.

> [!NOTE]
> O desempenho de carga destas opções é mais lento do que o PolyBase e a declaração COPY.

## <a name="5-transform-the-data"></a>5. Transformar os dados

Enquanto os dados estiverem na tabela de encenação, execute as transformações que a sua carga de trabalho requer. Em seguida, mova os dados para uma mesa de produção.

## <a name="6-insert-the-data-into-production-tables"></a>6. Inserir os dados nas tabelas de produção

O INSERÇÃO EM ... A declaração SELECT move os dados da tabela de preparação para a tabela permanente.

Ao conceber um processo ETL, tente executar o processo numa pequena amostra de teste. Tente extrair 1000 linhas da mesa para um ficheiro, movimente-o para Azure e tente carregá-lo numa mesa de preparação.

## <a name="partner-loading-solutions"></a>Soluções de carregamento de parceiros

Muitos dos nossos parceiros têm soluções de carregamento. Para saber mais, consulte uma lista dos [nossos parceiros de solução.](sql-data-warehouse-partner-business-intelligence.md)

## <a name="next-steps"></a>Passos seguintes

Para carregar a documentação de orientação, veja [Guidance for loading data (Documentação de orientação para carregar dados)](guidance-for-loading-data.md).
