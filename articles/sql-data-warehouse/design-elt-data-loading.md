---
title: Em vez de ETL, design ELT
description: Em vez de ETL, crie um processo ELT (extração, carregamento e transformação) para carregar dados ou SQL Data Warehouse do Azure.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: load-data
ms.date: 11/07/2019
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 9220bf0cf94eaae6ddc945e83deac2a6041158d2
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73748520"
---
# <a name="data-loading-strategies-for-azure-sql-data-warehouse"></a>Estratégias de carregamento de dados para o Azure SQL Data Warehouse

Os data warehouses SMP tradicionais usam um processo ETL (extração, transformação e carregamento) para carregar dados. O Azure SQL Data Warehouse é uma arquitetura MPP (processamento paralelo maciço) que aproveita a escalabilidade e a flexibilidade dos recursos de computação e armazenamento. A utilização de um processo ELT (extração, carregamento e transformação) pode aproveitar o MPP e eliminar os recursos necessários para transformar os dados antes do carregamento. Embora SQL Data Warehouse ofereça suporte a muitos métodos de carregamento, incluindo opções populares de SQL Server como BCP e a API do SQL BulkCopy, a maneira mais rápida e escalonável de carregar dados é por meio de tabelas externas do polybase e da [instrução de cópia](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (visualização).  Com o polybase e a instrução de cópia, você pode acessar dados externos armazenados no armazenamento de BLOBs do Azure ou Azure Data Lake Store por meio da linguagem T-SQL. Para obter mais flexibilidade ao carregar no SQL Data Warehouse, é recomendável usar a instrução de cópia. 

> [!NOTE]  
> A instrução de cópia está atualmente em visualização pública. Para fornecer comentários, envie um email para a seguinte lista de distribuição: sqldwcopypreview@service.microsoft.com.
>
        
 
> [!VIDEO https://www.youtube.com/embed/l9-wP7OdhDk]


## <a name="what-is-elt"></a>O que é ELT?

O ELT (extração, carregamento e transformação) é um processo pelo qual os dados são extraídos de um sistema de origem, carregados em um data warehouse e, em seguida, transformados. 

As etapas básicas para implementar ELT para SQL Data Warehouse são:

1. Extraia os dados de origem em arquivos de texto.
2. Pouse os dados no armazenamento de BLOBs do Azure ou Azure Data Lake Store.
3. Prepare os dados para carregar.
4. Carregue os dados em SQL Data Warehouse tabelas de preparo com o polybase ou o comando de cópia. 
5. Transforme os dados.
6. Insira os dados em tabelas de produção.


Para obter um tutorial de carregamento do polybase, consulte [usar o polybase para carregar dados do armazenamento de BLOBs do Azure para o Azure SQL data warehouse](load-data-from-azure-blob-storage-using-polybase.md).

Para obter mais informações, consulte [carregando blog de padrões](https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/). 


## <a name="1-extract-the-source-data-into-text-files"></a>1. Extraia os dados de origem em arquivos de texto

A obtenção de dados do seu sistema de origem depende do local de armazenamento.  O objetivo é mover os dados para o polybase e a cópia com suporte de texto delimitado ou arquivos CSV. 

### <a name="polybase-and-copy-external-file-formats"></a>Polybase e copiar formatos de arquivo externo

Com o polybase e a instrução de cópia, você pode carregar dados de texto delimitado codificado em UTF-8 e UTF-16 ou arquivos CSV. Além de texto delimitado ou arquivos CSV, ele é carregado a partir dos formatos de arquivo do Hadoop, como ORC e parquet. O polybase e a instrução de cópia também podem carregar dados de arquivos compactados gzip e de instantâneo. Não há suporte para ASCII estendido, formato de largura fixa e formatos aninhados como o WinZip ou XML. Se você estiver exportando do SQL Server, poderá usar a [ferramenta de linha de comando bcp](/sql/tools/bcp-utility?view=azure-sqldw-latest) para exportar os dados para arquivos de texto delimitados. 

## <a name="2-land-the-data-into-azure-blob-storage-or-azure-data-lake-store"></a>2. Pouse os dados no armazenamento de BLOBs do Azure ou Azure Data Lake Store

Para colocar os dados no armazenamento do Azure, você pode movê-los para o [armazenamento de BLOBs do Azure](../storage/blobs/storage-blobs-introduction.md) ou [Azure data Lake Store Gen2](../data-lake-store/data-lake-store-overview.md). Em qualquer local, os dados devem ser armazenados em arquivos de texto. O polybase e a instrução de cópia podem ser carregados de qualquer um dos locais.

Ferramentas e serviços que você pode usar para mover dados para o armazenamento do Azure:

- O serviço de [ExpressRoute do Azure](../expressroute/expressroute-introduction.md) aprimora a taxa de transferência, o desempenho e a previsibilidade da rede. O ExpressRoute é um serviço que roteia seus dados por meio de uma conexão privada dedicada ao Azure. As conexões do ExpressRoute não roteiam dados pela Internet pública. As conexões oferecem mais confiabilidade, velocidades mais rápidas, latências menores e maior segurança do que as conexões típicas pela Internet pública.
- O [utilitário AZCopy](../storage/common/storage-moving-data.md) move os dados para o armazenamento do Azure pela Internet pública. Isso funcionará se seus tamanhos de dados forem inferiores a 10 TB. Para executar cargas regularmente com o AZCopy, teste a velocidade da rede para ver se ela é aceitável. 
- [Azure data Factory (ADF)](../data-factory/introduction.md) tem um gateway que você pode instalar no servidor local. Em seguida, você pode criar um pipeline para mover dados do servidor local para o armazenamento do Azure. Para usar Data Factory com SQL Data Warehouse, consulte [carregar dados em SQL data warehouse](/azure/data-factory/load-azure-sql-data-warehouse).


## <a name="3-prepare-the-data-for-loading"></a>3. preparar os dados para carregar

Talvez seja necessário preparar e limpar os dados em sua conta de armazenamento antes de carregá-los em SQL Data Warehouse. A preparação de dados pode ser executada enquanto os dados estão na origem, à medida que você exporta os dados para arquivos de texto ou depois que os dados estão no armazenamento do Azure.  É mais fácil trabalhar com os dados o mais cedo possível no processo.  

### <a name="define-external-tables"></a>Definir tabelas externas

Se você estiver usando o polybase, precisará definir tabelas externas no seu data warehouse antes de carregar. As tabelas externas não são exigidas pela instrução de cópia. O polybase usa tabelas externas para definir e acessar os dados no armazenamento do Azure. Uma tabela externa é semelhante a uma exibição de banco de dados. A tabela externa contém o esquema de tabela e aponta para os dados que são armazenados fora do data warehouse. 

Definir tabelas externas envolve especificar a fonte de dados, o formato dos arquivos de texto e as definições de tabela. Os tópicos de sintaxe T-SQL que serão necessários são:
- [CRIAR FONTE DE DADOS EXTERNA](/sql/t-sql/statements/create-external-data-source-transact-sql?view=azure-sqldw-latest)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest)
- [CRIAR TABELA EXTERNA](/sql/t-sql/statements/create-external-table-transact-sql?view=azure-sqldw-latest)

Ao carregar parquet, o mapeamento de tipo de dados com o SQL DW é:

| **Tipo de dados parquet** |                      **Tipo de dados SQL**                       |
| :-------------------: | :----------------------------------------------------------: |
|        tinyint        |                           tinyint                            |
|       smallint        |                           smallint                           |
|          int          |                             int                              |
|        bigint         |                            bigint                            |
|        boolean        |                             parte                              |
|        double         |                            float                             |
|         float         |                             foto                             |
|        double         |                            gastar                             |
|        double         |                          smallmoney                          |
|        string         |                            nchar                             |
|        string         |                           nvarchar                           |
|        string         |                             º                             |
|        string         |                           varchar                            |
|        binário         |                            binário                            |
|        binário         |                          varbinary                           |
|       carimbo de data/hora       |                             date                             |
|       carimbo de data/hora       |                        smalldatetime                         |
|       carimbo de data/hora       |                          datetime2                           |
|       carimbo de data/hora       |                           datetime                           |
|       carimbo de data/hora       |                             hora                             |
|       date            |                             date                             |
|        decimal        |                            decimal                           |

Para obter um exemplo de criação de objetos externos, consulte a etapa [criar tabelas externas](load-data-from-azure-blob-storage-using-polybase.md#create-external-tables-for-the-sample-data) no tutorial de carregamento.

### <a name="format-text-files"></a>Formatar arquivos de texto

Se você estiver usando o polybase, os objetos externos definidos precisarão alinhar as linhas dos arquivos de texto com a tabela externa e a definição de formato de arquivo. Os dados em cada linha do arquivo de texto devem ser alinhados com a definição da tabela.
Para formatar os arquivos de texto:

- Se seus dados forem provenientes de uma fonte não relacional, você precisará transformá-los em linhas e colunas. Se os dados são de uma fonte relacional ou não relacional, os dados devem ser transformados para ficarem alinhados às definições de coluna da tabela na qual você planeja carregar os dados. 
- Formate os dados no arquivo de texto para alinhá-los com as colunas e os tipos de dados na tabela SQL Data Warehouse destino. O desalinhamento entre os tipos de dados nos arquivos de texto externos e a tabela de data warehouse faz com que as linhas sejam rejeitadas durante a carga.
- Separe os campos no arquivo de texto com um terminador.  Certifique-se de usar um caractere ou uma sequência de caracteres que não seja encontrada em seus dados de origem. Use o terminador especificado com [criar formato de arquivo externo](/sql/t-sql/statements/create-external-file-format-transact-sql).


## <a name="4-load-the-data-into-sql-data-warehouse-staging-tables-using-polybase-or-the-copy-statement"></a>4. carregar os dados em SQL Data Warehouse tabelas de preparo usando o polybase ou a instrução de cópia

É uma prática recomendada carregar dados em uma tabela de preparo. As tabelas de preparo permitem que você manipule erros sem interferir nas tabelas de produção. Uma tabela de preparo também oferece a oportunidade de usar SQL Data Warehouse MPP para transformações de dados antes de inserir os dados em tabelas de produção. A tabela precisará ser criada previamente ao carregar em uma tabela de preparo com cópia.

### <a name="options-for-loading-with-polybase-and-copy-statement"></a>Opções para carregamento com o polybase e a instrução de cópia

Para carregar dados com o polybase, você pode usar qualquer uma dessas opções de carregamento:

- O [polybase com o T-SQL](load-data-from-azure-blob-storage-using-polybase.md) funciona bem quando seus dados estão no armazenamento de BLOBs do Azure ou Azure data Lake Store. Ele oferece o máximo de controle sobre o processo de carregamento, mas também exige que você defina objetos de dados externos. Os outros métodos definem esses objetos em segundo plano conforme você mapeia tabelas de origem para tabelas de destino.  Para orquestrar cargas de T-SQL, você pode usar Azure Data Factory, SSIS ou Azure functions. 
- O [polybase com o SSIS](/sql/integration-services/load-data-to-sql-data-warehouse) funciona bem quando os dados de origem estão em SQL Server, seja SQL Server no local ou na nuvem. O SSIS define a origem para mapeamentos de tabela de destino e também orquestra a carga. Se você já tiver pacotes SSIS, poderá modificar os pacotes para trabalhar com o novo destino data warehouse. 
- O [polybase e a instrução de cópia com Azure data Factory (ADF)](sql-data-warehouse-load-with-data-factory.md) é outra ferramenta de orquestração.  Ele define um pipeline e agenda trabalhos. 
- O [polybase com Azure Databricks](../azure-databricks/databricks-extract-load-sql-data-warehouse.md) transfere dados de uma tabela SQL data warehouse para um dataframe do databricks e/ou grava dados de um dataframe do databricks em uma tabela SQL data warehouse usando o polybase.

### <a name="other-loading-options"></a>Outras opções de carregamento

Além do polybase e da instrução COPY, você pode usar o [bcp](/sql/tools/bcp-utility?view=azure-sqldw-latest) ou a [API SQLBulkCopy](https://msdn.microsoft.com/library/system.data.sqlclient.sqlbulkcopy.aspx). o bcp carrega diretamente no SQL Data Warehouse sem passar pelo armazenamento de BLOBs do Azure e destina-se apenas a pequenas cargas. Observe que o desempenho de carga dessas opções é mais lento do que o polybase e a instrução de cópia. 


## <a name="5-transform-the-data"></a>5. transformar os dados

Enquanto os dados estão na tabela de preparo, execute as transformações exigidas pela carga de trabalho. Em seguida, mova os dados para uma tabela de produção.


## <a name="6-insert-the-data-into-production-tables"></a>6. inserir os dados em tabelas de produção

A instrução INSERT INTO... A instrução SELECT move os dados da tabela de preparo para a tabela permanente. 

Ao criar um processo de ETL, tente executar o processo em um pequeno exemplo de teste. Tente extrair 1000 linhas da tabela para um arquivo, movê-la para o Azure e, em seguida, tentar carregá-la em uma tabela de preparo. 


## <a name="partner-loading-solutions"></a>Soluções de carregamento de parceiro

Muitos de nossos parceiros têm soluções de carregamento. Para obter mais informações, consulte uma lista de nossos [parceiros de solução](sql-data-warehouse-partner-business-intelligence.md). 


## <a name="next-steps"></a>Passos seguintes

Para obter diretrizes de carregamento, consulte [diretrizes para carregar dados](guidance-for-loading-data.md).


