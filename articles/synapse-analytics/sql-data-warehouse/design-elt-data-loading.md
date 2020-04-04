---
title: Em vez de ETL, design ELT
description: Implementar estratégias flexíveis de carregamento de dados para piscina SYnapse SQL dentro do Azure Synapse Analytics
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/19/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 87b33e91076f8f7f31740795f0ec05cea49a1e83
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631192"
---
# <a name="data-loading-strategies-for-synapse-sql-pool"></a>Estratégias de carregamento de dados para piscina Synapse SQL

As piscinas Tradicionais SMP SQL utilizam um processo de extração, transformação e carga (ETL) para carregar dados. A piscina SYnapse SQL, dentro da Azure Synapse Analytics, tem uma arquitetura de processamento massivamente paralela (MPP) que tira partido da escalabilidade e flexibilidade dos recursos de computação e armazenamento.

A utilização de um processo de Extração, Carga e Transformação (ELT) alavanca o MPP e elimina os recursos necessários para a transformação de dados antes do carregamento.

Enquanto o pool SQL suporta muitos métodos de carregamento, incluindo opções populares do SQL Server, como o [BCP](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) e o [SqlBulkCopy API,](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/sql-data-warehouse?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)a forma mais rápida e escalável de carregar dados é através de tabelas externas da PolyBase e da [declaração COPY](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (pré-visualização).

Com a PolyBase e a declaração COPY, pode aceder a dados externos armazenados no armazenamento Azure Blob ou na Azure Data Lake Store através do idioma T-SQL. Para a maior flexibilidade ao carregar, recomendamos a utilização da declaração COPY.

> [!NOTE]  
> A declaração do COPY encontra-se atualmente em pré-visualização pública. Para fornecer feedback, envie e-mail sqldwcopypreview@service.microsoft.compara a seguinte lista de distribuição: .

> [!VIDEO https://www.youtube.com/embed/l9-wP7OdhDk]

## <a name="what-is-elt"></a>O que é ELT?

Extrair, Carregar e Transformar (ELT) é um processo pelo qual os dados são extraídos de um sistema de origem, carregados numa piscina SQL e depois transformados.

Os passos básicos para a implementação do ELT são:

1. Extraia os dados de origem para ficheiros de texto.
2. Aterre os dados no armazenamento azure Blob ou na Azure Data Lake Store.
3. Prepare os dados para o carregamento.
4. Carregue os dados em tabelas de preparação com a PolyBase ou o comando COPY.
5. Transforme os dados.
6. Insira os dados em tabelas de produção.

Para um tutorial de carregamento PolyBase, consulte [Use PolyBase para carregar dados do armazenamento de blob Azure](load-data-from-azure-blob-storage-using-polybase.md).

Para mais informações, consulte o [blog Loading Patterns](https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/).

## <a name="1-extract-the-source-data-into-text-files"></a>1. Extrair os dados de origem em ficheiros de texto

Tirar dados do seu sistema de origem depende do local de armazenamento.  O objetivo é mover os dados para polyBase e os ficheiros de texto ou CSV de texto limitado suportados pela COPY.

### <a name="polybase-and-copy-external-file-formats"></a>Formatos de ficheiros externos PolyBase e COPY

Com a PolyBase e a declaração COPY, pode carregar dados de ficheiros de texto limitado UTF-8 e UTF-16 codificados ou CSV. Além de ficheiros de texto ou CSV delimitados, ele carrega a partir dos formatos de ficheirohado como ORC e Parquet. A PolyBase e a declaração COPY também podem carregar dados de ficheiros comprimidos Gzip e Snappy.

Não são suportados formatos ascii estendidos, de largura fixa e formatos aninhados como o WinZip ou o XML. Se estiver a exportar do SQL Server, pode utilizar a [ferramenta de linha de comando do BCP](/sql/tools/bcp-utility?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) para exportar os dados para ficheiros de texto delimitados.

## <a name="2-land-the-data-into-azure-blob-storage-or-azure-data-lake-store"></a>2. Aterrissar os dados no armazenamento da Blob Azure ou na Azure Data Lake Store

Para aterrar os dados no armazenamento do Azure, pode movê-lo para [o armazenamento Azure Blob](../../storage/blobs/storage-blobs-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) ou [para a Azure Data Lake Store Gen2](../../data-lake-store/data-lake-store-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Em qualquer dos locais, os dados devem ser armazenados em ficheiros de texto. A PolyBase e a declaração COPY podem ser carregadas em qualquer local.

Ferramentas e serviços que pode utilizar para mover dados para o Armazenamento Azure:

- O serviço [Azure ExpressRoute](../../expressroute/expressroute-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) melhora a entrada, desempenho e previsibilidade da rede. O ExpressRoute é um serviço que encaminha os seus dados através de uma ligação privada dedicada ao Azure. As ligações ExpressRoute não encaminham dados através da internet pública. As ligações oferecem mais fiabilidade, velocidades mais rápidas, mais latências e maior segurança do que as ligações típicas através da internet pública.
- [O utilitário AZCopy](../../storage/common/storage-choose-data-transfer-solution.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) transfere dados para o Armazenamento Azure através da internet pública. Isto funciona se os seus tamanhos de dados forem inferiores a 10 TB. Para efetuar cargas regularmente com a AZCopy, teste a velocidade da rede para ver se é aceitável.
- [A Azure Data Factory (ADF)](../../data-factory/introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) tem uma porta de entrada que pode instalar no seu servidor local. Em seguida, pode criar um pipeline para mover dados do seu servidor local para o Armazenamento Azure. Para utilizar a Data Factory com SQL Analytics, consulte [os dados de carregamento para SQL Analytics](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

## <a name="3-prepare-the-data-for-loading"></a>3. Preparar os dados para o carregamento

Pode ser necessário preparar e limpar os dados da sua conta de armazenamento antes de carregar. A preparação de dados pode ser realizada enquanto os seus dados estiverem na fonte, à medida que exporta os dados para ficheiros de texto, ou após os dados estarem no Armazenamento Do Azure.  É mais fácil trabalhar com os dados o mais cedo possível no processo.  

### <a name="define-external-tables"></a>Definir tabelas externas

Se estiver a utilizar o PolyBase, tem de definir tabelas externas na sua piscina SQL antes de carregar. As tabelas externas não são exigidas pela declaração COPY. A PolyBase utiliza tabelas externas para definir e aceder aos dados no Armazenamento Azure.

Uma tabela externa é semelhante a uma visão de base de dados. A tabela externa contém o esquema da tabela e aponta para os dados que são armazenados fora do pool SQL.

A definição de tabelas externas implica especificar a fonte de dados, o formato dos ficheiros de texto e as definições de tabela. Os artigos de referência da sintaxe T-SQL que necessitará são:

- [CRIAR FONTE DE DADOS EXTERNAS](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [CRIAR TABELA EXTERNA](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

Ao carregar parquet, o mapeamento do tipo de dados SQL é:

| **Tipo de dados de Parquet** | **Tipo de dados SQL** |
| :-------------------: | :---------------: |
|        tinyint        |      tinyint      |
|       smallint        |     smallint      |
|          int          |        int        |
|        bigint         |      bigint       |
|        boolean        |        bit        |
|        double         |       float       |
|         float         |       real        |
|        double         |       dinheiro       |
|        double         |    dinheiro pequeno     |
|        string         |       nchar       |
|        string         |     nvarchar      |
|        string         |       char        |
|        string         |      varchar      |
|        binary         |      binary       |
|        binary         |     varbinary     |
|       carimbo de data/hora       |       date        |
|       carimbo de data/hora       |   tempo de data pequena   |
|       carimbo de data/hora       |     datetime2     |
|       carimbo de data/hora       |     datetime      |
|       carimbo de data/hora       |       hora        |
|         date          |       date        |
|        decimal        |      decimal      |

Para um exemplo de criação de objetos externos, consulte o passo das [tabelas externas Criar](load-data-from-azure-blob-storage-using-polybase.md#create-external-tables-for-the-sample-data) no tutorial de carregamento.

### <a name="format-text-files"></a>Ficheiros de texto de formato

Se estiver a utilizar a PolyBase, os objetos externos definidos precisam de alinhar as linhas dos ficheiros de texto com a tabela externa e a definição de formato de ficheiro. Os dados de cada linha do ficheiro de texto devem alinhar-se com a definição da tabela.
Para formatar os ficheiros de texto:

- Se os seus dados vierem de uma fonte não relacional, tem de transformá-lo em linhas e colunas. Quer os dados sejam de uma fonte relacional ou não relacional, os dados devem ser transformados para se alinharem com as definições de coluna para a tabela em que pretende carregar os dados.
- Formato dados no ficheiro de texto para alinhar com as colunas e os tipos de dados na tabela de destino. O desalinhamento entre os tipos de dados nos ficheiros de texto externos e na tabela de bilhar SQL faz com que as linhas sejam rejeitadas durante a carga.
- Campos separados no ficheiro de texto com um exterminador.  Certifique-se de usar um personagem ou uma sequência de caracteres que não esteja encontrada nos seus dados de origem. Utilize o exterminador especificado com [o FORMATO DE FICHEIRO EXTERNO CREATE](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="4-load-the-data-using-polybase-or-the-copy-statement"></a>4. Carregue os dados utilizando a PolyBase ou a declaração COPY

É a melhor prática carregar dados numa mesa de preparação. As tabelas de preparação permitem-lhe lidar com erros sem interferir com as tabelas de produção. Uma tabela de encenação também lhe dá a oportunidade de usar o MPP do pool SQL para transformações de dados antes de inserir os dados em tabelas de produção.

A tabela terá de ser pré-criada ao carregar numa mesa de preparação com COPY.

### <a name="options-for-loading-with-polybase-and-copy-statement"></a>Opções para carregamento com declaração polyBase e COPY

Para carregar dados com a PolyBase, pode utilizar qualquer uma destas opções de carregamento:

- [A PolyBase com T-SQL](load-data-from-azure-blob-storage-using-polybase.md) funciona bem quando os seus dados estão no armazenamento Azure Blob ou na Azure Data Lake Store. Dá-lhe o maior controlo sobre o processo de carregamento, mas também requer que defina objetos de dados externos. Os outros métodos definem estes objetos nos bastidores enquanto mapeia tabelas de origem para tabelas de destino.  Para orquestrar cargas T-SQL, pode utilizar funções Azure Data Factory, SSIS ou Azure.
- [O PolyBase com o SSIS](/sql/integration-services/load-data-to-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) funciona bem quando os seus dados de origem estão no Servidor SQL, quer no SQL Server no local quer na nuvem. O SSIS define a fonte para os mapeamentos de tabelas de destino, e também orquestra a carga. Se já tem pacotes SSIS, pode modificar os pacotes para trabalhar com o novo destino de armazém de dados.
- [A declaração da PolyBase e copy com a Azure Data Factory (ADF)](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) é outra ferramenta de orquestração.  Define um oleoduto e programa empregos.
- [A PolyBase com os Bricks Azure](../../azure-databricks/databricks-extract-load-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) transfere dados de uma tabela para um dataframe de Databricks e/ou escreve dados de um dataframe de Databricks para uma tabela utilizando a PolyBase.

### <a name="other-loading-options"></a>Outras opções de carregamento

Além da PolyBase e da declaração COPY, pode utilizar o [BCP](https://docs.microsoft.com/sql/tools/bcp-utility?view=sql-server-ver15) ou a [SqlBulkCopy API](https://msdn.microsoft.com/library/system.data.sqlclient.sqlbulkcopy.aspx). o BCP carrega diretamente para a base de dados sem passar pelo armazenamento do Azure Blob, e destina-se apenas a pequenas cargas.

> [!NOTE]
> Nota: o desempenho da carga destas opções é mais lento do que a PolyBase e a declaração COPY.

## <a name="5-transform-the-data"></a>5. Transformar os dados

Enquanto os dados estiverem na tabela de preparação, execute as transformações que a sua carga de trabalho requer. Em seguida, mova os dados para uma tabela de produção.

## <a name="6-insert-the-data-into-production-tables"></a>6. Insira os dados nas tabelas de produção

O INSerÇÃO EM ... A declaração SELECT move os dados da tabela de preparação para a tabela permanente.

Ao conceber um processo ETL, experimente executar o processo numa pequena amostra de teste. Tente extrair 1000 linhas da mesa para um ficheiro, mova-o para Azure e, em seguida, tente carregá-lo em uma mesa de preparação.

## <a name="partner-loading-solutions"></a>Soluções de carregamento de parceiros

Muitos dos nossos parceiros têm soluções de carregamento. Para saber mais, consulte uma lista dos [nossos parceiros de solução.](sql-data-warehouse-partner-business-intelligence.md)

## <a name="next-steps"></a>Passos seguintes

Para carregar a documentação de orientação, veja [Guidance for loading data (Documentação de orientação para carregar dados)](guidance-for-loading-data.md).
