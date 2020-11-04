---
title: Desenhe uma estratégia de carregamento de dados da PolyBase para piscina sql dedicada
description: Em vez de ETL, desenhe um processo de Extrato, Carga e Transformação (ELT) para carregar dados com SQL dedicado.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: a57abd080bdbbaefbe07258a2b241c093dc8c441
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93308750"
---
# <a name="design-a-polybase-data-loading-strategy-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Desenhe uma estratégia de carregamento de dados da PolyBase para piscina SQL dedicada em Azure Synapse Analytics

Os armazéns de dados tradicionais SMP utilizam um processo de extração, transformação e carga (ETL) para o carregamento de dados. A piscina Azure SQL é uma arquitetura de processamento massivamente paralela (MPP) que aproveita a escalabilidade e flexibilidade dos recursos de computação e armazenamento. A utilização de um processo de Extração, Carga e Transformação (ELT) pode tirar partido das capacidades de processamento de consultas distribuídas incorporadas e eliminar os recursos necessários para transformar os dados antes do carregamento.

Enquanto a piscina SQL suporta muitos métodos de carregamento, incluindo opções não-Polybase, como BCP e SQL BulkCopy API, a forma mais rápida e escalável de carregar data é através da PolyBase.  PolyBase é uma tecnologia que acede a dados externos armazenados no armazenamento Azure Blob ou na Azure Data Lake Store através da linguagem T-SQL.

> [!VIDEO https://www.youtube.com/embed/l9-wP7OdhDk]

## <a name="extract-load-and-transform-elt"></a>Extrato, Carga e Transformação (ELT)

Extrato, Carga e Transformação (ELT) é um processo pelo qual os dados são extraídos de um sistema de origem, carregados num armazém de dados e depois transformados.

Os passos básicos para a implementação de um PolyBase ELT para piscina SQL dedicada são:

1. Extraia os dados de origem para ficheiros de texto.
2. Aterre os dados no armazém da Azure Blob ou na Azure Data Lake Store.
3. Prepare os dados para o carregamento.
4. Carregue os dados em mesas de preparação de piscinas SQL dedicadas utilizando a PolyBase.
5. Transforme os dados.
6. Insira os dados em tabelas de produção.

Para um tutorial de carregamento, consulte [Use PolyBase para carregar os dados do armazenamento de blob Azure para a Azure Synapse Analytics](../sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

Para obter mais informações, consulte [o blog Loading patterns](https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/).

## <a name="1-extract-the-source-data-into-text-files"></a>1. Extrair os dados de origem em ficheiros de texto

A obtenção de dados do seu sistema de origem depende da localização do armazenamento.  O objetivo é mover os dados para ficheiros de texto delimitados suportados pela PolyBase.

### <a name="polybase-external-file-formats"></a>Formatos de ficheiros externos PolyBase

A PolyBase carrega dados de ficheiros de texto delimitados UTF-8 e UTF-16. Além dos ficheiros de texto delimitados, carrega a partir dos formatos de ficheiro Hadoop RC File, ORC e Parquet. O PolyBase também pode carregar dados de ficheiros comprimidos Gzip e Snappy. ATualmente, a PolyBase não suporta formatos ASCII estendidos, formato de largura fixa e formatos aninhados como WinZip, JSON e XML.

Se estiver a exportar a partir do SQL Server, pode utilizar a [ferramenta da linha de comando do BCP](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) para exportar os dados para ficheiros de texto delimitados. O mapeamento do tipo de dados Parquet a Azure Synapse Analytics é o seguinte:

| **Tipo de dados parquet** |                      **Tipo de dados SQL**                       |
| :-------------------: | :----------------------------------------------------------: |
|        tinyint        |                           tinyint                            |
|       smallint        |                           smallint                           |
|          int          |                             int                              |
|        bigint         |                            bigint                            |
|        boolean        |                             bit                              |
|        double         |                            float                             |
|         float         |                             real                             |
|        double         |                            dinheiro                             |
|        double         |                          pequeno dinheiro                          |
|        string         |                            nchar                             |
|        string         |                           nvarchar                           |
|        string         |                             char                             |
|        string         |                           varchar                            |
|        binary         |                            binary                            |
|        binary         |                          varbinário                           |
|       carimbo de data/hora       |                             date                             |
|       carimbo de data/hora       |                        hora pequena                         |
|       carimbo de data/hora       |                          datetime2                           |
|       carimbo de data/hora       |                           datetime                           |
|       carimbo de data/hora       |                             hora                             |
|       date            |                             date                             |
|        decimal        |                            decimal                           |

## <a name="2-land-the-data-into-azure-blob-storage-or-azure-data-lake-store"></a>2. Aterre os dados no armazenamento da Azure Blob ou na Azure Data Lake Store

Para aterrar os dados no armazenamento Azure, pode movê-lo para [o armazenamento Azure Blob](../../storage/blobs/storage-blobs-introduction.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) ou [para a Azure Data Lake Store.](../../data-lake-store/data-lake-store-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) Em qualquer local, os dados devem ser armazenados em ficheiros de texto. A PolyBase pode carregar a partir de qualquer local.

Ferramentas e serviços que pode utilizar para mover dados para o Azure Storage:

- O serviço [Azure ExpressRoute](../../expressroute/expressroute-introduction.md) melhora a produção, desempenho e previsibilidade da rede. O ExpressRoute é um serviço que encaminha os seus dados através de uma ligação privada dedicada ao Azure. As ligações ExpressRoute não encaminham dados através da internet pública. As ligações oferecem mais fiabilidade, velocidades mais rápidas, latências mais baixas e maior segurança do que as ligações típicas através da internet pública.
- [O utilitário AZCopy](../../storage/common/storage-use-azcopy-v10.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) move dados para Azure Storage através da internet pública. Isto funciona se os seus dados forem inferiores a 10 TB. Para efetuar cargas regularmente com AZCopy, teste a velocidade da rede para ver se é aceitável.
- [A Azure Data Factory (ADF)](../../data-factory/introduction.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) tem um portal que pode instalar no seu servidor local. Em seguida, pode criar um pipeline para mover dados do seu servidor local para o Azure Storage. Para utilizar a Data Factory com piscina SQL dedicada, consulte [os dados de carga em piscina SQL dedicada.](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="3-prepare-the-data-for-loading"></a>3. Preparar os dados para o carregamento

Poderá ter de preparar e limpar os dados na sua conta de armazenamento antes de os colocar numa piscina SQL dedicada. A preparação de dados pode ser realizada enquanto os seus dados estão na fonte, à medida que exporta os dados para ficheiros de texto, ou depois de os dados estarem no Azure Storage.  É mais fácil trabalhar com os dados o mais cedo possível no processo.  

### <a name="define-external-tables"></a>Definir tabelas externas

Antes de poder carregar dados, tem de definir tabelas externas no seu armazém de dados. A PolyBase utiliza tabelas externas para definir e aceder aos dados no Azure Storage. Uma tabela externa é semelhante a uma visão de base de dados. A tabela externa contém o esquema de tabela e aponta para dados que são armazenados fora do armazém de dados.

Definir tabelas externas envolve especificar a fonte de dados, o formato dos ficheiros de texto e as definições de tabela. Seguem-se os tópicos de sintaxe T-SQL de que vai precisar:

- [CRIAR FONTE DE DADOS EXTERNA](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [CRIAR TABELA EXTERNA](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

### <a name="format-text-files"></a>Arquivos de texto de formato

Uma vez definidos os objetos externos, é necessário alinhar as linhas dos ficheiros de texto com a definição de quadro e formato de ficheiro externo. Os dados em cada linha do ficheiro de texto devem alinhar-se com a definição da tabela.
Para formatar os ficheiros de texto:

- Se os seus dados são provenientes de uma fonte não relacional, tem de os transformar em linhas e colunas. Quer os dados sejam de uma fonte relacional ou não relacional, os dados devem ser transformados para alinhar com as definições de coluna para a tabela em que planeia carregar os dados.
- Formato de dados no ficheiro de texto para alinhar com as colunas e tipos de dados na tabela de destinos de piscina SQL. O desalinhamento entre os tipos de dados nos ficheiros de texto externos e a tabela do armazém de dados faz com que as filas sejam rejeitadas durante a carga.
- Separe os campos no ficheiro de texto com um exterminador.  Certifique-se de que utiliza um personagem ou uma sequência de caracteres que não se encontra nos seus dados de origem. Utilize o exterminador especificado com [FORMATO DE FICHEIRO EXTERNO CREATE](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="4-load-the-data-into-dedicated-sql-pool-staging-tables-using-polybase"></a>4. Carregue os dados em mesas de paragem dedicadas ao SQL utilizando a PolyBase

É melhor a prática carregar dados numa tabela de preparação. As tabelas de preparação permitem-lhe lidar com erros sem interferir com as tabelas de produção. Uma tabela de encenação também lhe dá a oportunidade de usar capacidades de processamento de consulta distribuída incorporadas sql para transformações de dados antes de inserir os dados em tabelas de produção.

### <a name="options-for-loading-with-polybase"></a>Opções para carregamento com PolyBase

Para carregar dados com a PolyBase, pode utilizar qualquer uma destas opções de carregamento:

- [A PolyBase com T-SQL](../sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) funciona bem quando os seus dados estão no armazenamento Azure Blob ou na Azure Data Lake Store. Dá-lhe mais controlo sobre o processo de carregamento, mas também requer que você defina objetos de dados externos. Os outros métodos definem estes objetos nos bastidores à medida que mapeia as tabelas de origem para as tabelas de destino.  Para orquestrar cargas T-SQL, pode utilizar funções Azure Data Factory, SSIS ou Azure.
- [A PolyBase com o SSIS](/sql/integration-services/load-data-to-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) funciona bem quando os seus dados de origem estão no SQL Server. O SSIS define a fonte para os mapeamentos de mesa de destino, e também orquestra a carga. Se já tiver pacotes SSIS, pode modificar os pacotes para trabalhar com o novo destino do armazém de dados.
- [PolyBase com Azure Data Factory (ADF)](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) é outra ferramenta de orquestração.  Define um oleoduto e programa empregos.
- [A PolyBase com Azure Databricks](../../azure-databricks/databricks-extract-load-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) transfere dados de uma tabela Azure Synapse Analytics para um dataframe databricks e/ou escreve dados de um dataframe databricks para uma tabela Azure Synapse Analytics usando a PolyBase.

### <a name="non-polybase-loading-options"></a>Opções de carregamento não-PolyBase

Se os seus dados não forem compatíveis com a PolyBase, pode utilizar o [BCP](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) ou a [API SQLBulkCopy](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json). o BCP carrega diretamente para piscina SQL dedicada sem passar pelo armazenamento Azure Blob, e destina-se apenas a pequenas cargas. Note que o desempenho da carga destas opções é significativamente mais lento do que o PolyBase.

## <a name="5-transform-the-data"></a>5. Transformar os dados

Enquanto os dados estiverem na tabela de encenação, execute as transformações que a sua carga de trabalho requer. Em seguida, mova os dados para uma mesa de produção.

## <a name="6-insert-the-data-into-production-tables"></a>6. Inserir os dados nas tabelas de produção

O INSERÇÃO EM ... A declaração SELECT move os dados da tabela de preparação para a tabela permanente.

Ao conceber um processo ETL, tente executar o processo numa pequena amostra de teste. Tente extrair 1000 linhas da mesa para um ficheiro, movimente-o para Azure e tente carregá-lo numa mesa de preparação.

## <a name="partner-loading-solutions"></a>Soluções de carregamento de parceiros

Muitos dos nossos parceiros têm soluções de carregamento. Para saber mais, consulte uma lista dos [nossos parceiros de solução.](../sql-data-warehouse/sql-data-warehouse-partner-business-intelligence.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="next-steps"></a>Passos seguintes

Para obter orientação de carregamento, consulte [orientação para os dados de carga](data-loading-best-practices.md).
