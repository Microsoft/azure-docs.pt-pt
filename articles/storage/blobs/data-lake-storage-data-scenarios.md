---
title: Cenários de dados que envolvem Azure Data Lake Storage Gen2 | Microsoft Docs
description: Entenda os diferentes cenários e ferramentas usando quais dados podem ser ingeridos, processados, baixados e visualizados no Data Lake Storage Gen2 (anteriormente conhecido como Azure Data Lake Store)
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 2954f0bfcfe78243c2df12182f45034f46c8391d
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/23/2019
ms.locfileid: "69991901"
---
# <a name="using-azure-data-lake-storage-gen2-for-big-data-requirements"></a>Usando Azure Data Lake Storage Gen2 para requisitos de Big Data

Há quatro estágios principais no processamento de Big Data:

> [!div class="checklist"]
> * Ingestão de grandes quantidades de dados em um armazenamento de dados, em tempo real ou em lotes
> * Processando os dados
> * Baixando os dados
> * Visualizando os dados

Comece criando uma conta de armazenamento e um contêiner. Em seguida, conceda acesso aos dados. As primeiras seções deste artigo o ajudarão a realizar essas tarefas. Nas seções restantes, destacaremos as opções e as ferramentas para cada fase de processamento.

## <a name="create-a-data-lake-storage-gen2-account"></a>Criar uma conta de Data Lake Storage Gen2

Uma conta de Data Lake Storage Gen2 é uma conta de armazenamento que tem um namespace hierárquico. 

Para criar um, consulte [início rápido: Crie uma conta](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-account?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)de armazenamento Azure data Lake Storage Gen2.

## <a name="create-a-container"></a>Criar um contentor

Aqui está uma lista de ferramentas que você pode usar para criar um contêiner para seus arquivos.

|Ferramenta | Orientação |
|---|--|
|Explorador do Storage do Azure | [Criar um contêiner usando Gerenciador de Armazenamento](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-explorer#create-a-file-system) |
|AzCopy | [Criar um contêiner de BLOB ou compartilhamento de arquivos usando AzCopyV10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10#transfer-files)|
|CLI (interface de linha de comando) de contêiner do Hadoop (HDFS) com o HDInsight |[Criar um contêiner usando o HDFS com o HDInsight](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-use-hdfs-data-lake-storage?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#create-a-file-system) |
|Código em um bloco de anotações Azure Databricks|[Criar um contêiner de conta de armazenamento (escala)](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-databricks-account?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#create-storage-account-file-system) <br><br> [Criar um contêiner e montá-lo (Python)](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-use-databricks-spark?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#create-a-file-system-and-mount-it)|

É mais fácil criar sistemas de arquivos usando Gerenciador de Armazenamento ou AzCopy. É necessário um pouco mais de trabalho para criar sistemas de arquivos usando o HDInsight e o databricks. No entanto, se você estiver planejando usar clusters HDInsight ou databricks para processar seus dados de qualquer forma, poderá criar seus clusters primeiro e usar a CLI do HDFS para criar sistemas de arquivos.  

## <a name="grant-access-to-the-data"></a>Conceder acesso aos dados

Configure as permissões de acesso apropriadas para sua conta e os dados em sua conta, antes de começar a ingerir dados.

Há três maneiras de conceder acesso:

* Atribua uma dessas funções a um usuário, grupo, identidade gerenciada pelo usuário ou entidade de serviço:

  [Leitor de dados de blob de armazenamento](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader)

  [Colaborador de dados do blob de armazenamento](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor)

  [Proprietário de dados do blob de armazenamento](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)

* Use um token de SAS (assinatura de acesso compartilhado).

* Use uma chave de conta de armazenamento.

Esta tabela mostra como conceder acesso para cada serviço ou ferramenta do Azure.

|Ferramenta | Para conceder acesso | Orientação |
|---|--|---|
|Explorador de Armazenamento| Atribuir uma função a usuários e grupos | [Atribuir funções de administrador e não administrador a usuários com Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal) |
|AzCopy| Atribuir uma função a usuários e grupos <br>**ou**<br> Usar um token SAS| [Atribuir funções de administrador e não administrador a usuários com Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal)<br><br>[Crie facilmente uma SAS para baixar um arquivo do armazenamento do Azure – usando Gerenciador de Armazenamento do Azure](https://blogs.msdn.microsoft.com/jpsanders/2017/10/12/easily-create-a-sas-to-download-a-file-from-azure-storage-using-azure-storage-explorer/)|
|Apache DistCp | Atribuir uma função a uma identidade gerenciada atribuída pelo usuário | [Criando um cluster HDInsight com Data Lake Storage Gen2](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2) |
|Azure Data Factory| Atribuir uma função a uma identidade gerenciada pelo usuário<br>**ou**<br> Atribuir uma função a uma entidade de serviço<br>**ou**<br> Usar uma chave de conta de armazenamento | [Propriedades do serviço vinculado](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#linked-service-properties) |
|Azure HDInsight| Atribuir uma função a uma identidade gerenciada atribuída pelo usuário | [Criando um cluster HDInsight com Data Lake Storage Gen2](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2)|
|Azure Databricks| Atribuir uma função a uma entidade de serviço | [How to: Utilizar o portal para criar uma aplicação e um principal de serviço do Azure AD que possam aceder aos recursos](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)|

Para conceder acesso a arquivos e pastas específicas, consulte estes artigos.

* [Definir permissões de nível de arquivo e diretório usando Gerenciador de Armazenamento do Azure com Azure Data Lake Storage Gen2](https://review.docs.microsoft.com/azure/storage/blobs/data-lake-storage-how-to-set-permissions-storage-explorer)

* [Listas de controle de acesso em arquivos e diretórios](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control#access-control-lists-on-files-and-directories)

Para saber mais sobre como configurar outros aspectos de segurança, confira [Azure data Lake Storage Gen2 guia de segurança](https://docs.microsoft.com/azure/storage/common/storage-data-lake-storage-security-guide?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="ingest-the-data"></a>Incorporar os dados

Esta seção destaca as diferentes fontes de dados e as diferentes maneiras em que esses dados podem ser ingeridos em uma conta de Data Lake Storage Gen2.

![Ingerir dados em data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/ingest-data.png "Ingerir dados em data Lake Storage Gen2")

### <a name="ad-hoc-data"></a>Dados ad hoc

Isso representa conjuntos de dados menores que são usados para o protótipo de um aplicativo Big Data. Há diferentes maneiras de ingerir dados ad hoc dependendo da origem dos dados. 

Aqui está uma lista de ferramentas que você pode usar para ingerir dados ad hoc.

| Origem de Dados | Ingerir usando |
| --- | --- |
| Computador local |[Explorador de Armazenamento](https://azure.microsoft.com/features/storage-explorer/)<br><br>[Ferramenta AzCopy](../common/storage-use-azcopy-v10.md)|
| Azure Storage Blob |[Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md)<br><br>[Ferramenta AzCopy](../common/storage-use-azcopy-v10.md)<br><br>[DistCp em execução no cluster HDInsight](data-lake-storage-use-distcp.md)|

### <a name="streamed-data"></a>Dados transmitidos

Isso representa os dados que podem ser gerados por várias fontes, como aplicativos, dispositivos, sensores, etc. Esses dados podem ser ingeridos em Data Lake Sorage Gen2 por uma variedade de ferramentas. Essas ferramentas normalmente capturam e processam os dados em uma base evento por evento em tempo real e, em seguida, gravam os eventos em lotes em Data Lake Storage Gen2 para que possam ser processados ainda mais.

Aqui está uma lista de ferramentas que você pode usar para ingerir dados transmitidos.

|Ferramenta | Orientação |
|---|--|
|Azure HDInsight Storm | [Gravar em Apache Hadoop HDFS de Apache Storm no HDInsight](https://docs.microsoft.com/azure/hdinsight/storm/apache-storm-write-data-lake-store) |

### <a name="relational-data"></a>Dados relacionais

Você também pode originar dados de bancos de dado relacionais. Ao longo de um período de tempo, os bancos de dados relacionais coletam enormes quantidades de data que podem fornecer informações de chave, se processadas por meio de um pipeline de Big Data. Você pode usar as ferramentas a seguir para mover esses dados para Data Lake Storage Gen2.

Aqui está uma lista de ferramentas que você pode usar para ingerir dados relacionais.

|Ferramenta | Orientação |
|---|--|
|Azure Data Factory | [Copy Activity in Azure Data Factory](https://docs.microsoft.com/azure/data-factory/copy-activity-overview) (Atividade de Cópia no Azure Data Factory) |

### <a name="web-server-log-data-upload-using-custom-applications"></a>Dados de log do servidor Web (carregar usando aplicativos personalizados)

Esse tipo de conjunto de dados é especificamente chamado porque a análise de data de log do servidor Web é um caso de uso comum para Big Data aplicativos e requer que grandes volumes de arquivos de log sejam carregados para Data Lake Storage Gen2. Você pode usar qualquer uma das ferramentas a seguir para escrever seus próprios scripts ou aplicativos para carregar esses dados.

Aqui está uma lista de ferramentas que você pode usar para ingerir dados de log do servidor Web.

|Ferramenta | Orientação |
|---|--|
|Azure Data Factory | [Copy Activity in Azure Data Factory](https://docs.microsoft.com/azure/data-factory/copy-activity-overview) (Atividade de Cópia no Azure Data Factory)  |

Para carregar dados de log do servidor Web e também para carregar outros tipos de dados (por exemplo, dados de opiniões sociais), é uma boa abordagem para escrever seus próprios scripts/aplicativos personalizados, pois oferece a flexibilidade de incluir o componente de carregamento de dados como parte do seu aplicativo de Big Data maior. Em alguns casos, esse código pode assumir a forma de um script ou utilitário de linha de comando simples. Em outros casos, o código pode ser usado para integrar Big Data processamento em um aplicativo ou solução de negócios.

### <a name="data-associated-with-azure-hdinsight-clusters"></a>Dados associados aos clusters do Azure HDInsight

A maioria dos tipos de cluster HDInsight (Hadoop, HBase, Storm) oferece suporte a Data Lake Storage Gen2 como um repositório de armazenamento de dados. Os clusters HDInsight acessam dados de blobs de armazenamento do Azure (WASB). Para obter um melhor desempenho, você pode copiar os dados do WASB para uma conta do Data Lake Storage Gen2 associada ao cluster. Você pode usar as ferramentas a seguir para copiar os dados.

Aqui está uma lista de ferramentas que você pode usar para ingerir dados associados a clusters HDInsight.

|Ferramenta | Orientação |
|---|--|
|Apache DistCp | [Use DistCp para copiar dados entre os blobs de armazenamento do Azure e Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-use-distcp) |
|Ferramenta AzCopy | [Transferir dados com o AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10) |
|Azure Data Factory | [Copiar dados de ou para Azure Data Lake Storage Gen2 usando Azure Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2) |

### <a name="data-stored-in-on-premises-or-iaas-hadoop-clusters"></a>Dados armazenados em clusters Hadoop locais ou IaaS

Grandes quantidades de dados podem ser armazenadas em clusters Hadoop existentes, localmente em máquinas usando o HDFS. Os clusters Hadoop podem estar em uma implantação local ou podem estar em um cluster IaaS no Azure. Pode haver requisitos para copiar esses dados para Azure Data Lake Storage Gen2 para uma abordagem única ou de maneira recorrente. Há várias opções que você pode usar para conseguir isso. Abaixo está uma lista de alternativas e as compensações associadas.

| Abordagem | Detalhes | Vantagens | Considerações |
| --- | --- | --- | --- |
| Use Azure Data Factory (ADF) para copiar dados diretamente de clusters Hadoop para Azure Data Lake Storage Gen2 |[O ADF dá suporte ao HDFS como uma fonte de dados](../../data-factory/connector-hdfs.md) |O ADF fornece suporte pronto para o HDFS e o monitoramento e gerenciamento de ponta a ponta de primeira classe |Requer que Gerenciamento de Dados gateway seja implantado localmente ou no cluster IaaS |
| Use Distcp para copiar dados do Hadoop para o armazenamento do Azure. Em seguida, copie dados do armazenamento do Azure para Data Lake Storage Gen2 usando o mecanismo apropriado. |Você pode copiar dados do armazenamento do Azure para Data Lake Storage Gen2 usando: <ul><li>[Azure Data Factory](../../data-factory/copy-activity-overview.md)</li><li>[Ferramenta AzCopy](../common/storage-use-azcopy-v10.md)</li><li>[Apache DistCp em execução em clusters HDInsight](data-lake-storage-use-distcp.md)</li></ul> |Você pode usar ferramentas de código-fonte aberto. |Processo de várias etapas que envolve várias tecnologias |

### <a name="really-large-datasets"></a>Conjuntos de altos volumes de grandes

Para carregar conjuntos de valores que variam em vários terabytes, usar os métodos descritos acima pode, às vezes, ser lento e dispendioso. Nesses casos, você pode usar o Azure ExpressRoute.  

O Azure ExpressRoute permite criar conexões privadas entre os data centers do Azure e a infraestrutura local. Isso fornece uma opção confiável para transferir grandes quantidades de dados. Para saber mais, consulte a [documentação do Azure ExpressRoute](../../expressroute/expressroute-introduction.md).

## <a name="process-the-data"></a>Processar os dados

Depois que os dados estiverem disponíveis no Data Lake Storage Gen2 você poderá executar a análise nesses dados usando os aplicativos Big Data com suporte. 

![Analisar dados no data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/analyze-data.png "Analisar dados no data Lake Storage Gen2")

Aqui está uma lista de ferramentas que você pode usar para executar trabalhos de análise de dados em dados armazenados no Data Lake Storage Gen2.

|Ferramenta | Orientação |
|---|--|
|Azure HDInsight | [Utilizar o Azure Data Lake Storage Gen2 com clusters do Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2) |
|Azure Databricks | [Azure Data Lake Storage Gen2](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html)<br><br>[Quickstart: Analisar dados em Azure Data Lake Storage Gen2 usando Azure Databricks](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-databricks-account?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)<br><br>[Tutorial: Extrair, transformar e carregar dados usando Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|

## <a name="visualize-the-data"></a>Ver os dados

Você pode usar uma combinação de serviços para criar representações visuais de dados armazenados no Data Lake Storage Gen2.

![Visualizar dados no data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/visualize-data.png "Visualizar dados no data Lake Storage Gen2")

* Você pode começar usando [Azure data Factory para mover dados de data Lake Storage Gen2 para o Azure SQL data warehouse](../../data-factory/copy-activity-overview.md)
* Depois disso, você pode [integrar Power bi com o SQL data warehouse do Azure](../../sql-data-warehouse/sql-data-warehouse-get-started-visualize-with-power-bi.md) para criar a representação visual dos dados.

## <a name="download-the-data"></a>Baixar os dados

Talvez você também queira baixar ou mover dados de Azure Data Lake Storage Gen2 para cenários como:

* Mova dados para outros repositórios para a interface com seus pipelines de processamento de dados existentes. Por exemplo, talvez você queira mover dados de Data Lake Storage Gen2 para o banco de dados SQL do Azure ou local SQL Server.

* Baixe os dados no computador local para processamento em ambientes IDE durante a criação de protótipos de aplicativo.

![Dados de saída de data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/egress-data.png "Dados de saída de data Lake Storage Gen2")

Aqui está uma lista de ferramentas que você pode usar para baixar dados de Data Lake Storage Gen2.

|Ferramenta | Orientação |
|---|--|
|Azure Data Factory | [Copy Activity in Azure Data Factory](https://docs.microsoft.com/azure/data-factory/copy-activity-overview) (Atividade de Cópia no Azure Data Factory) |
|Apache DistCp | [Use DistCp para copiar dados entre os blobs de armazenamento do Azure e Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-use-distcp) |
