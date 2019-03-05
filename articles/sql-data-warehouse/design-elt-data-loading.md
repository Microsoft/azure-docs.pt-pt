---
title: Em vez de ETL, estruturar o ELT para o Azure SQL Data Warehouse | Documentos da Microsoft
description: Em vez de ETL, crie um processo de extração, carregamento e transformação (ELT) para carregar os dados ou o Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ckarst
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 04/17/2018
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: 96f6da7e081430768b5a6f8fd874e289b8256271
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57308487"
---
# <a name="designing-a-polybase-data-loading-strategy-for-azure-sql-data-warehouse"></a>Conceber um estratégia de carregamento para o Azure SQL Data Warehouse de dados de PolyBase

Armazéns de dados SMP tradicionais utilizam um processo de extração, transformação e carregamento (ETL) de carregamento de dados. O Azure SQL Data Warehouse é uma arquitetura de processamento paralelo em massa (MPP), que tira partido da escalabilidade e a flexibilidade de recursos de computação e armazenamento. Utilizar um processo de extração, carregamento e transformação (ELT), pode tirar partido do MPP e elimine os recursos necessários para transformar os dados antes de carregar. Embora o SQL Data Warehouse suporta vários métodos de carregamento, incluindo opções de não-Polybase como BCP e SQL BulkCopy API, a forma mais rápida e mais escalonável para carregar data é através do PolyBase.  O PolyBase é uma tecnologia que acede a dados externos armazenados no armazenamento de Blobs do Azure ou no Azure Data Lake Store através de linguagem T-SQL.

> [!VIDEO https://www.youtube.com/embed/l9-wP7OdhDk]


## <a name="what-is-elt"></a>O que é o ELT?

Extrair, carga, e transformação (ELT) é um processo pelo qual os dados são extraídos de um sistema de origem, carregados para um armazém de dados e, em seguida, transformados. 

Os passos básicos para a implementação de um ELT PolyBase do SQL Data Warehouse são:

1. Extrair a origem de dados em ficheiros de texto.
2. Direcionado para os dados no armazenamento de Blobs do Azure ou do Azure Data Lake Store.
3. Prepare os dados para carregamento.
4. Carregar os dados para o SQL Data Warehouse com o PolyBase de tabelas de testes. 
5. Transforme os dados.
6. Inserir os dados em tabelas de produção.


Para obter um tutorial de carregamento, veja [utilizar o PolyBase para carregar dados do armazenamento de Blobs do Azure ao Azure SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md).

Para obter mais informações, consulte [blog de padrões de carregamento](https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/). 


## <a name="1-extract-the-source-data-into-text-files"></a>1. Extrair os dados de origem para ficheiros de texto

Obter dados fora do seu sistema de origem depende da localização de armazenamento.  O objetivo é mover os dados em PolyBase suportado de ficheiros de texto delimitados. 

### <a name="polybase-external-file-formats"></a>Formatos de ficheiro externo de PolyBase

O PolyBase carrega dados de UTF-8 e UTF-16 codificado de ficheiros de texto delimitados. Além dos ficheiros de texto delimitado, ele carrega dos formatos de ficheiro Hadoop RC ficheiro ORC e no Parquet. O PolyBase também pode carregar dados do Gzip e arquivos compactados Snappy. O PolyBase atualmente não suporta ASCII estendido, o formato de largura fixa e formatos aninhados, como o WinZip, JSON e XML. Se estiver a exportar do SQL Server, pode usar [ferramenta da linha de comandos do bcp](/sql/tools/bcp-utility) para exportar os dados em arquivos de texto delimitado.


## <a name="2-land-the-data-into-azure-blob-storage-or-azure-data-lake-store"></a>2. Direcionado para os dados no armazenamento de Blobs do Azure ou do Azure Data Lake Store

Parar os dados no armazenamento do Azure, pode movê-lo para [armazenamento de Blobs do Azure](../storage/blobs/storage-blobs-introduction.md) ou [do Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md). Em ambos os locais, os dados devem ser armazenados em arquivos de texto. O PolyBase pode carregar a partir de ambos os locais.

Ferramentas e serviços que pode utilizar para mover dados para o armazenamento do Azure:

- [O Azure ExpressRoute](../expressroute/expressroute-introduction.md) serviço melhora o débito de rede, desempenho e previsibilidade. O ExpressRoute é um serviço que encaminha seus dados através de uma ligação privada dedicada para o Azure. As ligações ExpressRoute não encaminhar os dados através da internet pública. As ligações oferecem mais fiabilidade, velocidades mais rápidas, latências mais baixas e maior segurança do que as ligações normais pela internet pública.
- [O utilitário AZCopy](../storage/common/storage-moving-data.md) move os dados ao armazenamento do Azure através da internet pública. Isso funciona se os tamanhos de dados for inferior a 10 TB. Para executar cargas de forma regular, com o AZCopy, teste a velocidade da rede para ver se é aceitável. 
- [O Azure Data Factory (ADF)](../data-factory/introduction.md) tem um gateway que pode instalar no seu servidor local. Em seguida, pode criar um pipeline para mover dados do seu servidor local até o armazenamento do Azure. Para utilizar o Data Factory com o SQL Data Warehouse, consulte [carregar dados para o SQL Data Warehouse](/azure/data-factory/load-azure-sql-data-warehouse).


## <a name="3-prepare-the-data-for-loading"></a>3. Preparar os dados para carregamento

Poderá ter de preparar e limpar os dados na sua conta de armazenamento antes de os carregar para o SQL Data Warehouse. Preparação de dados pode ser executada enquanto os seus dados estão na fonte, como exportar os dados em arquivos de texto, ou depois dos dados no armazenamento do Azure.  É mais fácil de trabalhar com os dados mais cedo no processo de possível.  

### <a name="define-external-tables"></a>Definir tabelas externas

Antes de carregar dados, terá de definir tabelas externas no seu armazém de dados. O PolyBase utiliza tabelas externas para definir e aceder aos dados no armazenamento do Azure. Uma tabela externa é semelhante a uma vista de base de dados. A tabela externa contém o esquema da tabela e aponta para os dados armazenados fora do armazém de dados. 

Definir tabelas externas envolve a especificação da origem de dados, o formato dos ficheiros de texto e as definições de tabela. Estes são os tópicos de sintaxe de T-SQL que será necessário:
- [CRIAR ORIGEM DE DADOS EXTERNA](/sql/t-sql/statements/create-external-data-source-transact-sql)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql)
- [CRIAR TABELA EXTERNA](/sql/t-sql/statements/create-external-table-transact-sql)

Para obter um exemplo de criação de objetos externos, consulte a [criar tabelas externas](load-data-from-azure-blob-storage-using-polybase.md#create-external-tables-for-the-sample-data) passo do tutorial de carregamento.

### <a name="format-text-files"></a>Ficheiros de texto de formato

Assim que são definidos os objetos externos, tem de alinhar as linhas dos ficheiros de texto com a tabela externa e a definição de formato de ficheiro. Os dados em cada linha do arquivo de texto devem estar alinhadas com a definição da tabela.
Para formatar os arquivos de texto:

- Se seus dados é proveniente de uma origem de não-relacionais, terá de transformá-lo em linhas e colunas. Se os dados estão a partir de uma origem relacional ou não relacionais, os dados precisam ser transformados para alinhar com as definições da coluna para a tabela na qual pretende carregar os dados. 
- Formatar dados no arquivo de texto para alinhar com os colunas e tipos de dados da tabela de destino do SQL Data Warehouse. Alinhamento incorreto entre tipos de dados nos arquivos de texto externos e a tabela de armazém de dados faz com que as linhas para serem rejeitadas durante o carregamento.
- Campos separados no arquivo de texto com um terminador.  Certifique-se de que utiliza um caráter ou uma seqüência de caracteres que não se encontra nos seus dados de origem. Utilizar o terminador especificado com [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql).


## <a name="4-load-the-data-into-sql-data-warehouse-staging-tables-using-polybase"></a>4. Carregar os dados para o SQL Data Warehouse com o PolyBase de tabelas de testes

É melhor prática para carregar dados para uma tabela de teste. Tabelas de teste permitem-lhe processar erros sem interferir com as tabelas de produção. Uma tabela de teste também lhe dá a oportunidade de usar o SQL Data Warehouse MPP para transformações de dados antes de inserir os dados em tabelas de produção.

### <a name="options-for-loading-with-polybase"></a>Opções de carregamento com o PolyBase

Para carregar dados com o PolyBase, pode usar qualquer uma destas opções de carregamento:

- [O PolyBase com T-SQL](load-data-from-azure-blob-storage-using-polybase.md) funciona bem quando os seus dados estão no armazenamento de Blobs do Azure ou no Azure Data Lake Store. Dá-lhe mais controle sobre o processo de carregamento, mas também requer que definir os objetos de dados externa. Os outros métodos definem esses objetos em segundo plano, como mapear tabelas de origem para tabelas de destino.  Para orquestrar a cargas de T-SQL, pode utilizar o Azure Data Factory, SSIS ou as funções do Azure. 
- [O PolyBase com o SSIS](/sql/integration-services/load-data-to-sql-data-warehouse) funciona bem quando os dados de origem estão no SQL Server, SQL Server no local ou na cloud. SSIS define a origem para mapeamentos de tabela de destino e também orquestra a carga. Se já tiver pacotes do SSIS, pode modificar os pacotes para trabalhar com o novo destino de armazém de dados. 
- [O PolyBase com o Azure Data Factory (ADF)](sql-data-warehouse-load-with-data-factory.md) é outra ferramenta de orquestração.  Ele define um pipeline e agenda trabalhos. 
- [O PolyBase com o Azure DataBricks](../azure-databricks/databricks-extract-load-sql-data-warehouse.md) transfere dados a partir de uma tabela do SQL Data Warehouse para um dataframe do Databricks e/ou escreve dados de um pacote de dados do Databricks a uma tabela do SQL Data Warehouse.

### <a name="non-polybase-loading-options"></a>Opções de carregamento PolyBase não

Se seus dados não são compatíveis com o PolyBase, pode utilizar [bcp](/sql/tools/bcp-utility) ou o [SQLBulkCopy API](https://msdn.microsoft.com/library/system.data.sqlclient.sqlbulkcopy.aspx). BCP carrega diretamente ao SQL Data Warehouse sem passar pelo armazenamento de Blobs do Azure e destina-se apenas para pequenas cargas. Tenha em atenção de que o desempenho de carga dessas opções é significativamente mais lento do que o PolyBase. 


## <a name="5-transform-the-data"></a>5. Transformar os dados

Embora seja dados na tabela de teste, efetue transformações que precisa de sua carga de trabalho. Em seguida, mova os dados na tabela de produção.


## <a name="6-insert-the-data-into-production-tables"></a>6. Inserir os dados em tabelas de produção

A inserção em... Instrução SELECT move os dados da tabela de teste para a tabela permanente. 

À medida que concebe um processo de ETL, tente executar o processo de uma amostra de teste pequeno. Tente extrair 1000 linhas da tabela para um ficheiro, movê-lo para o Azure e, em seguida, tente carregar para uma tabela de teste. 


## <a name="partner-loading-solutions"></a>Soluções de carregamento de parceiros

Muitos dos nossos parceiros de ter a carregar soluções. Para obter mais informações, consulte uma lista de nosso [parceiros de solução](sql-data-warehouse-partner-business-intelligence.md). 


## <a name="next-steps"></a>Passos Seguintes

Para carregar a documentação de orientação, veja [documentação de orientação para carregar dados](guidance-for-loading-data.md).


