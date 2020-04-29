---
title: Em vez de ETL, desenhe ELT para piscina Synapse SQL [ Piscina SQL ] Microsoft Docs
description: Em vez da ETL, desenhe um processo de Extrato, Carga e Transformação (ELT) para carregar dados ou piscina SQL.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 596f4bcf2e3f829430fdc90eb1806a44a84b2bc5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81429593"
---
# <a name="designing-a-polybase-data-loading-strategy-for-azure-synapse-sql-pool"></a>Conceber uma estratégia de carregamento de dados da PolyBase para o pool Azure Synapse SQL

Os armazéns tradicionais de dados SMP utilizam um processo de extração, transformação e carga (ETL) para carregar dados. A piscina Azure SQL é uma arquitetura de processamento massivamente paralela (MPP) que tira partido da escalabilidade e flexibilidade dos recursos de computação e armazenamento. A utilização de um processo de Extração, Carga e Transformação (ELT) pode tirar partido do MPP e eliminar os recursos necessários para transformar os dados antes do carregamento.

Enquanto o pool SQL suporta muitos métodos de carregamento, incluindo opções não-Polybase, como BCP e SQL BulkCopy API, a forma mais rápida e escalável de carregar data é através da PolyBase.  PolyBase é uma tecnologia que acede a dados externos armazenados no armazenamento Azure Blob ou na Azure Data Lake Store através do idioma T-SQL.

> [!VIDEO https://www.youtube.com/embed/l9-wP7OdhDk]

## <a name="what-is-elt"></a>O que é ELT?

Extrair, Carregar e Transformar (ELT) é um processo pelo qual os dados são extraídos de um sistema de origem, carregados num armazém de dados e depois transformados.

Os passos básicos para a implementação de um PolyBase ELT para piscina SQL são:

1. Extraia os dados de origem para ficheiros de texto.
2. Aterre os dados no armazenamento azure Blob ou na Azure Data Lake Store.
3. Prepare os dados para o carregamento.
4. Carregue os dados em mesas de preparação de piscinaS SQL utilizando a PolyBase.
5. Transforme os dados.
6. Insira os dados em tabelas de produção.

Para um tutorial de carregamento, consulte [Use PolyBase para carregar dados do armazenamento de blob Azure para o Armazém de Dados Azure SQL](../sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

Para mais informações, consulte o [blog Loading Patterns](https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/).

## <a name="1-extract-the-source-data-into-text-files"></a>1. Extrair os dados de origem em ficheiros de texto

Tirar dados do seu sistema de origem depende do local de armazenamento.  O objetivo é transferir os dados para ficheiros de texto delimitados apoiados pela PolyBase.

### <a name="polybase-external-file-formats"></a>Formatos de ficheiros externos da PolyBase

A PolyBase carrega dados de ficheiros de texto delimitados UTF-8 e UTF-16 codificados. Além dos ficheiros de texto delimitados, ele carrega a partir dos formatos de ficheiro Hadoop RC File, ORC e Parquet. A PolyBase também pode carregar dados de ficheiros comprimidos Gzip e Snappy. A PolyBase não suporta atualmente formato ASCII alargado, formato de largura fixa e formatos aninhados como WinZip, JSON e XML.

Se estiver a exportar do SQL Server, pode utilizar a [ferramenta de linha de comando do BCP](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) para exportar os dados para ficheiros de texto delimitados. O mapeamento do tipo de dados Parquet to SQL DW é o seguinte:

| **Tipo de dados de Parquet** |                      **Tipo de dados SQL**                       |
| :-------------------: | :----------------------------------------------------------: |
|        tinyint        |                           tinyint                            |
|       smallint        |                           smallint                           |
|          int          |                             int                              |
|        bigint         |                            bigint                            |
|        boolean        |                             bit                              |
|        double         |                            float                             |
|         float         |                             real                             |
|        double         |                            dinheiro                             |
|        double         |                          dinheiro pequeno                          |
|        string         |                            nchar                             |
|        string         |                           nvarchar                           |
|        string         |                             char                             |
|        string         |                           varchar                            |
|        binary         |                            binary                            |
|        binary         |                          varbinary                           |
|       carimbo de data/hora       |                             date                             |
|       carimbo de data/hora       |                        tempo de data pequena                         |
|       carimbo de data/hora       |                          datetime2                           |
|       carimbo de data/hora       |                           datetime                           |
|       carimbo de data/hora       |                             hora                             |
|       date            |                             date                             |
|        decimal        |                            decimal                           |

## <a name="2-land-the-data-into-azure-blob-storage-or-azure-data-lake-store"></a>2. Aterrissar os dados no armazenamento da Blob Azure ou na Azure Data Lake Store

Para aterrar os dados no armazenamento do Azure, pode movê-lo para [o armazenamento Azure Blob](../../storage/blobs/storage-blobs-introduction.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) ou [azure Data Lake Store.](../../data-lake-store/data-lake-store-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) Em qualquer dos locais, os dados devem ser armazenados em ficheiros de texto. A PolyBase pode carregar de qualquer local.

Ferramentas e serviços que pode utilizar para mover dados para o Armazenamento Azure:

- O serviço [Azure ExpressRoute](../../expressroute/expressroute-introduction.md) melhora a entrada, desempenho e previsibilidade da rede. O ExpressRoute é um serviço que encaminha os seus dados através de uma ligação privada dedicada ao Azure. As ligações ExpressRoute não encaminham dados através da internet pública. As ligações oferecem mais fiabilidade, velocidades mais rápidas, mais latências e maior segurança do que as ligações típicas através da internet pública.
- [O utilitário AZCopy](../../storage/common/storage-use-azcopy-v10.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) transfere dados para o Armazenamento Azure através da internet pública. Isto funciona se os seus tamanhos de dados forem inferiores a 10 TB. Para efetuar cargas regularmente com a AZCopy, teste a velocidade da rede para ver se é aceitável.
- [A Azure Data Factory (ADF)](../../data-factory/introduction.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) tem uma porta de entrada que pode instalar no seu servidor local. Em seguida, pode criar um pipeline para mover dados do seu servidor local para o Armazenamento Azure. Para utilizar a Data Factory com piscina SQL, consulte [os dados de carga no pool SQL](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

## <a name="3-prepare-the-data-for-loading"></a>3. Preparar os dados para o carregamento

Poderá ter de preparar e limpar os dados da sua conta de armazenamento antes de os colocar na piscina SQL. A preparação de dados pode ser realizada enquanto os seus dados estiverem na fonte, à medida que exporta os dados para ficheiros de texto, ou após os dados estarem no Armazenamento Do Azure.  É mais fácil trabalhar com os dados o mais cedo possível no processo.  

### <a name="define-external-tables"></a>Definir tabelas externas

Antes de poder carregar dados, tem de definir tabelas externas no seu armazém de dados. A PolyBase utiliza tabelas externas para definir e aceder aos dados no Armazenamento Azure. Uma tabela externa é semelhante a uma visão de base de dados. A tabela externa contém o esquema da tabela e aponta para os dados armazenados fora do armazém de dados.

A definição de tabelas externas implica especificar a fonte de dados, o formato dos ficheiros de texto e as definições de tabela. Estes são os tópicos de sintaxe T-SQL que você vai precisar:

- [CRIAR FONTE DE DADOS EXTERNAS](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [CRIAR TABELA EXTERNA](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

Para um exemplo de criação de objetos externos, consulte o passo das [tabelas externas Criar](../sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#create-external-tables-for-the-sample-data) no tutorial de carregamento.

### <a name="format-text-files"></a>Ficheiros de texto de formato

Uma vez definidos os objetos externos, é necessário alinhar as linhas dos ficheiros de texto com a tabela externa e a definição de formato de ficheiro. Os dados de cada linha do ficheiro de texto devem alinhar-se com a definição da tabela.
Para formatar os ficheiros de texto:

- Se os seus dados vierem de uma fonte não relacional, tem de transformá-lo em linhas e colunas. Quer os dados sejam de uma fonte relacional ou não relacional, os dados devem ser transformados para se alinharem com as definições de coluna para a tabela em que pretende carregar os dados.
- Formato dados no ficheiro de texto para alinhar com as colunas e tipos de dados na tabela de destino de piscina SQL. O desalinhamento entre os tipos de dados nos ficheiros de texto externos e na tabela do armazém de dados faz com que as linhas sejam rejeitadas durante a carga.
- Campos separados no ficheiro de texto com um exterminador.  Certifique-se de que utiliza um personagem ou uma sequência de caracteres que não se encontre nos seus dados de origem. Utilize o exterminador especificado com [o FORMATO DE FICHEIRO EXTERNO CREATE](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="4-load-the-data-into-sql-pool-staging-tables-using-polybase"></a>4. Carregue os dados em mesas de preparação de piscinaS SQL utilizando a PolyBase

É a melhor prática carregar dados numa mesa de preparação. As tabelas de preparação permitem-lhe lidar com erros sem interferir com as tabelas de produção. Uma tabela de encenação também lhe dá a oportunidade de usar MPP de piscina SQL para transformações de dados antes de inserir os dados em tabelas de produção.

### <a name="options-for-loading-with-polybase"></a>Opções para carregar com polyBase

Para carregar dados com a PolyBase, pode utilizar qualquer uma destas opções de carregamento:

- [A PolyBase com T-SQL](../sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) funciona bem quando os seus dados estão no armazenamento Azure Blob ou na Azure Data Lake Store. Dá-lhe o maior controlo sobre o processo de carregamento, mas também requer que defina objetos de dados externos. Os outros métodos definem estes objetos nos bastidores enquanto mapeia tabelas de origem para tabelas de destino.  Para orquestrar cargas T-SQL, pode utilizar funções Azure Data Factory, SSIS ou Azure.
- [O PolyBase com o SSIS](/sql/integration-services/load-data-to-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) funciona bem quando os seus dados de origem estão no Servidor SQL, quer no SQL Server no local quer na nuvem. O SSIS define a fonte para os mapeamentos de tabelas de destino, e também orquestra a carga. Se já tem pacotes SSIS, pode modificar os pacotes para trabalhar com o novo destino de armazém de dados.
- [PolyBase com Azure Data Factory (ADF)](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) é outra ferramenta de orquestração.  Define um oleoduto e programa empregos.
- [A PolyBase com os Databricks Azure](../../azure-databricks/databricks-extract-load-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) transfere dados de uma tabela de Armazém de Dados SQL para um dataframe de databricks e/ou escreve dados de um dataframe de Databricks para uma tabela de Armazém de Dados SQL usando a PolyBase.

### <a name="non-polybase-loading-options"></a>Opções de carregamento não PolyBase

Se os seus dados não forem compatíveis com a PolyBase, pode utilizar o [BCP](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) ou a [API SQLBulkCopy](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json). o BCP carrega diretamente para a piscina SQL sem passar pelo armazenamento azure Blob, e destina-se apenas a pequenas cargas. Note que o desempenho da carga destas opções é significativamente mais lento do que a PolyBase.

## <a name="5-transform-the-data"></a>5. Transformar os dados

Enquanto os dados estiverem na tabela de preparação, execute as transformações que a sua carga de trabalho requer. Em seguida, mova os dados para uma tabela de produção.

## <a name="6-insert-the-data-into-production-tables"></a>6. Insira os dados nas tabelas de produção

O INSerÇÃO EM ... A declaração SELECT move os dados da tabela de preparação para a tabela permanente.

Ao conceber um processo ETL, experimente executar o processo numa pequena amostra de teste. Tente extrair 1000 linhas da mesa para um ficheiro, mova-o para Azure e, em seguida, tente carregá-lo em uma mesa de preparação.

## <a name="partner-loading-solutions"></a>Soluções de carregamento de parceiros

Muitos dos nossos parceiros têm soluções de carregamento. Para saber mais, consulte uma lista dos [nossos parceiros de solução.](../sql-data-warehouse/sql-data-warehouse-partner-business-intelligence.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="next-steps"></a>Passos seguintes

Para obter orientação de carregamento, consulte [Orientação para os dados de carga](data-loading-best-practices.md).
