---
title: Cenários de dados que envolvem a geração 2 de armazenamento do Azure Data Lake | Documentos da Microsoft
description: Compreender os diferentes cenários e as ferramentas com que os dados podem ingeridos, processados, transferidas e visualizados no Data Lake Storage Gen2 (anteriormente conhecido como o Azure Data Lake Store)
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: normesta
ms.openlocfilehash: ec97cf903a37465f98007750fc41edc0952ab3a6
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56889601"
---
# <a name="using-azure-data-lake-storage-gen2-for-big-data-requirements"></a>Utilizar a geração 2 de armazenamento do Azure Data Lake para requisitos de grandes volumes de dados

Processamento de macrodados, existem quatro fases principais:

> [!div class="checklist"]
> * Ingerir grandes quantidades de dados num arquivo de dados em tempo real ou em lotes
> * Processar os dados
> * Transferir os dados
> * Visualizar os dados

Comece por criar uma conta de armazenamento e um sistema de ficheiros. Em seguida, conceda acesso aos dados. As primeira seções deste artigo ajudarão a realizar essas tarefas. Nas restantes secções, destacaremos as opções e ferramentas para cada fase de processamento.

## <a name="create-a-data-lake-storage-gen2-account"></a>Criar uma conta de geração 2 de armazenamento do Data Lake

Uma conta de geração 2 de armazenamento do Data Lake é uma conta de armazenamento que tenha um espaço de nomes hierárquico. 

Para criar um, consulte [início rápido: Criar uma conta de armazenamento de geração 2 de armazenamento do Azure Data Lake](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-account?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="create-a-file-system"></a>Criar um sistema de ficheiros

R *sistema de ficheiros* é um contentor para pastas e arquivos. Precisa de, pelo menos, um deles para iniciar a ingestão de dados na sua conta de armazenamento.  Aqui está uma lista de ferramentas que pode utilizar para criá-los.

|Ferramenta | Orientação |
|---|--|
|Explorador do Storage do Azure | [Criar um sistema de ficheiros através do Explorador de armazenamento](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-explorer#create-a-filesystem) |
|AzCopy | [Criar um contentor de BLOBs ou a partilha de ficheiros com AzCopyV10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#create-a-blob-container-or-file-share)|
|Ficheiro do Hadoop (HDFS) sistema comando Interface de linha (CLI) com o HDInsight |[Criar um sistema de ficheiros com HDFS com o HDInsight](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-use-hdfs-data-lake-storage?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#create-a-file-system) |
|Código num bloco de notas do Databricks do Azure|[Criar um sistema de ficheiros de conta de armazenamento (Scala)](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-databricks-account?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#create-storage-account-file-system) <br><br> [Criar um sistema de ficheiros e montá-la (Python)](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-use-databricks-spark?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#create-a-file-system-and-mount-it)|

É mais fácil criar sistemas de ficheiros utilizando o Explorador de armazenamento ou de AzCopy. Demora um pouco mais de trabalho para criar sistemas de ficheiros com o HDInsight e o Databricks. No entanto, se estiver a planear utilizar o HDInsight ou os clusters de Databricks para processar os seus dados de qualquer forma, em seguida, pode criar os clusters pela primeira vez e utilizar a CLI do HDFS para seus sistemas de ficheiros de criar.  

## <a name="grant-access-to-the-data"></a>Conceder acesso aos dados

Configure as permissões de acesso adequado à sua conta e os dados na sua conta, antes de iniciar a ingestão de dados.

Há três formas de conceder acesso:

* Atribua uma destas funções para um utilizador, grupo, identidade gerida pelo utilizador ou principal de serviço:

  [Leitor de dados de Blob de armazenamento](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader-preview)

  [Contribuinte de dados de Blob de armazenamento](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor-preview)

  [Proprietário de dados de Blob de armazenamento](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner-preview)

* Utilize um token de assinatura de acesso partilhado (SAS).

* Utilize uma chave de conta de armazenamento.

Esta tabela mostra como pode conceder acesso para cada serviço do Azure ou a ferramenta.

|Ferramenta | Para conceder acesso | Orientação |
|---|--|---|
|Explorador de Armazenamento| Atribuir uma função para utilizadores e grupos | [Atribuir funções de administrador e não-administrador aos utilizadores no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal) |
|AzCopy| Atribuir uma função para utilizadores e grupos <br>**ou**<br> Utilizar um token SAS| [Atribuir funções de administrador e não-administrador aos utilizadores no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal)<br><br>[Criar facilmente uma SAS para transferir um ficheiro de armazenamento do Azure – utilizar o Explorador de armazenamento do Azure](https://blogs.msdn.microsoft.com/jpsanders/2017/10/12/easily-create-a-sas-to-download-a-file-from-azure-storage-using-azure-storage-explorer/)|
|Apache DistCp | Atribuir uma função para uma identidade gerida atribuído ao utilizador | [Criar um cluster do HDInsight com geração 2 de armazenamento do Data Lake](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2) |
|Azure Data Factory| Atribuir uma função para uma identidade atribuída-gerida pelo utilizador<br>**ou**<br> Atribuir uma função para um principal de serviço<br>**ou**<br> Utilizar uma chave de conta de armazenamento | [Propriedades do serviço ligado](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#linked-service-properties) |
|Azure HDInsight| Atribuir uma função para uma identidade gerida atribuído ao utilizador | [Criar um cluster do HDInsight com geração 2 de armazenamento do Data Lake](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2)|
|Azure Databricks| Atribuir uma função para um principal de serviço | [How to: Utilizar o portal para criar uma aplicação do Azure AD e principal de serviço que pode aceder aos recursos](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)|

Para conceder acesso a pastas e ficheiros específicos, veja estes artigos.

* [Definir permissões de nível de ficheiros e diretórios a utilizar o Explorador de armazenamento do Azure com a geração 2 de armazenamento do Azure Data Lake](https://review.docs.microsoft.com/azure/storage/blobs/data-lake-storage-how-to-set-permissions-storage-explorer)

* [Listas de controlo de acesso em arquivos e diretórios](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control#access-control-lists-on-files-and-directories)

Para saber mais sobre como configurar outros aspectos de segurança, veja [guia de segurança de geração 2 de armazenamento do Azure Data Lake](https://review.docs.microsoft.com/azure/storage/common/storage-data-lake-storage-security-guide?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="ingest-the-data"></a>Incorporar os dados

Esta secção destaca as diferentes origens de dados e as diferentes formas em que esses dados podem ser ingeridos numa conta de geração 2 de armazenamento do Data Lake.

![Ingerir dados no Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/ingest-data.png "ingerir dados no Data Lake Storage Gen2")

### <a name="ad-hoc-data"></a>Dados ad hoc

Isso representa mais pequenos conjuntos de dados que são utilizados para criação de protótipos, um aplicativo de grandes volumes de dados. Existem formas diferentes de ingerir dados ad hoc, dependendo da origem dos dados. 

Aqui está uma lista de ferramentas que pode utilizar para a ingestão de dados ad hoc.

| Origem de Dados | São ingeridos com |
| --- | --- |
| Computador local |[Explorador de armazenamento](https://azure.microsoft.com/features/storage-explorer/)<br><br>[Ferramenta de AzCopy](../common/storage-use-azcopy-v10.md)|
| Blob de armazenamento do Azure |[Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md)<br><br>[Ferramenta de AzCopy](../common/storage-use-azcopy-v10.md)<br><br>[DistCp em execução no cluster do HDInsight](data-lake-storage-use-distcp.md)|

### <a name="streamed-data"></a>Dados de transmissão em fluxo contínuo

Isso representa os dados que podem ser gerados por várias origens, como aplicações, dispositivos, sensores, etc. Estes dados podem ser ingeridos no Data Lake Sorage Gen2 por uma variedade de ferramentas. Essas ferramentas normalmente irão capturar e processar os dados de uma forma de eventos por eventos em tempo real e, em seguida, gravar os eventos em lotes para a geração 2 de armazenamento do Data Lake para que possam ser processadas ainda mais.

Aqui está uma lista de ferramentas que pode usar para ingerir dados de transmissão em fluxo contínuo.

|Ferramenta | Orientação |
|---|--|
|O Azure HDInsight Storm | [Escrever para Apache Hadoop HDFS do Apache Storm no HDInsight](https://docs.microsoft.com/azure/hdinsight/storm/apache-storm-write-data-lake-store) |

### <a name="relational-data"></a>Dados relacionais

Também pode da origem de dados de bases de dados relacionais. Durante um período de tempo, bases de dados relacionais recolhem enormes quantidades de dados que podem fornecer informações de chaves se processada através de um pipeline de grandes volumes de dados. Pode utilizar as seguintes ferramentas para mover esses dados para a geração 2 de armazenamento do Data Lake.

Aqui está uma lista de ferramentas que pode utilizar para a ingestão de dados relacionais.

|Ferramenta | Orientação |
|---|--|
|Azure Data Factory | [Atividade de cópia numa fábrica de dados do Azure](https://docs.microsoft.com/azure/data-factory/copy-activity-overview) |

### <a name="web-server-log-data-upload-using-custom-applications"></a>Dados de registo do servidor Web (carregamento com aplicativos personalizados)

Este tipo de conjunto de dados é designado sobretudo porque a análise de dados de registo do servidor web é um caso de utilização comuns para aplicações de macrodados e requer grandes volumes de ficheiros de registo para ser carregado para a geração 2 de armazenamento do Data Lake. Pode utilizar qualquer uma das seguintes ferramentas para escrever seus próprios scripts ou aplicações para carregar esses dados.

Aqui está uma lista de ferramentas que pode usar para ingerir dados de registo do servidor Web.

|Ferramenta | Orientação |
|---|--|
|Azure Data Factory | [Atividade de cópia numa fábrica de dados do Azure](https://docs.microsoft.com/azure/data-factory/copy-activity-overview)  |

Para carregar dados de registo do servidor web e também para o carregamento de outros tipos de dados (por exemplo, dados de sentimentos social), é uma boa abordagem para escrever seus próprios scripts personalizadas/aplicações porque dá-lhe a flexibilidade para incluir os seus dados ao carregar o componente como parte do seu aplicativo de grandes volumes de dados maior. Em alguns casos, esse código pode tomar a forma de um script ou o utilitário de linha de comandos simples. Em outros casos, o código pode ser usado para integrar o processamento de macrodados num aplicativo de negócios ou solução.

### <a name="data-associated-with-azure-hdinsight-clusters"></a>Dados associados a clusters do HDInsight do Azure

A maioria dos tipos de cluster de HDInsight (Hadoop, HBase, Storm) suportam a geração 2 de armazenamento do Data Lake como um repositório de armazenamento de dados. Clusters do HDInsight aceder aos dados de Blobs de armazenamento do Azure (WASB). Para um melhor desempenho, pode copiar os dados de WASB para uma conta de geração 2 de armazenamento do Data Lake associada ao cluster. Pode utilizar as seguintes ferramentas para copiar os dados.

Aqui está uma lista de ferramentas que pode utilizar para a ingestão de dados associados a clusters do HDInsight.

|Ferramenta | Orientação |
|---|--|
|Apache DistCp | [Utilizar o DistCp para copiar dados entre os Blobs de armazenamento do Azure e de geração 2 de armazenamento do Azure Data Lake](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-use-distcp) |
|Ferramenta de AzCopy | [Transferir dados com o AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10) |
|Azure Data Factory | [Copiar dados de ou para a geração 1 de armazenamento do Azure Data Lake com o Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-store) |

### <a name="data-stored-in-on-premises-or-iaas-hadoop-clusters"></a>Dados armazenados no local ou IaaS Hadoop clusters

Grandes quantidades de dados podem ser armazenadas em clusters do Hadoop existentes, localmente em máquinas com HDFS. Os clusters do Hadoop podem estar numa implementação no local ou podem ser dentro de um cluster de IaaS no Azure. Pode haver requisitos para copiar esses dados para a geração 2 de armazenamento do Azure Data Lake para uma abordagem pontual ou de maneira recorrente. Existem várias opções que pode utilizar para atingir esse objetivo. Segue-se uma lista de alternativas e compromissos associados.

| Abordagem | Detalhes | Vantagens | Considerações |
| --- | --- | --- | --- |
| Utilizar o Azure Data Factory (ADF) para copiar dados diretamente a partir de clusters do Hadoop para a geração 2 de armazenamento do Azure Data Lake |[ADF suporta HDFS, como uma origem de dados](../../data-factory/connector-hdfs.md) |ADF fornece suporte de out-of-the-box para HDFS e de primeira categoria ponto-a-ponto gestão e monitorização |Requer o Data Management Gateway ser implementado no local ou no IaaS do cluster |
| Utilize o Distcp para copiar dados do Hadoop para o armazenamento do Azure. Em seguida, copie os dados do armazenamento do Azure para a geração 2 de armazenamento do Data Lake com o mecanismo apropriado. |Pode copiar dados do armazenamento do Azure para a utilização de geração 2 de armazenamento do Data Lake: <ul><li>[Azure Data Factory](../../data-factory/copy-activity-overview.md)</li><li>[Ferramenta de AzCopy](../common/storage-use-azcopy-v10.md)</li><li>[DistCp Apache em execução em clusters do HDInsight](data-lake-storage-use-distcp.md)</li></ul> |Pode utilizar ferramentas open source. |Processo de vários passo que envolve várias tecnologias |

### <a name="really-large-datasets"></a>Realmente grandes conjuntos de dados

Para carregar conjuntos de dados que variam em vários terabytes, usando os métodos descritos acima, às vezes, é possível lenta e dispendioso. Nesses casos, pode utilizar o Azure ExpressRoute.  

O Azure ExpressRoute permite-lhe criar ligações privadas entre datacenters do Azure e a infraestrutura no local. Isso fornece uma opção fiável para transferir grandes quantidades de dados. Para obter mais informações, consulte [documentação do Azure ExpressRoute](../../expressroute/expressroute-introduction.md).

## <a name="process-the-data"></a>Processar os dados

Depois dos dados estão disponíveis na geração 2 de armazenamento do Data Lake pode executar a análise de nesses dados com as aplicações de grandes volumes de dados suportados. 

![Analisar dados de geração 2 de armazenamento do Data Lake](./media/data-lake-storage-data-scenarios/analyze-data.png "analisar dados de geração 2 de armazenamento do Data Lake")

Aqui está uma lista de ferramentas que pode utilizar para executar tarefas de análises de dados em dados que são armazenados na geração 2 de armazenamento do Data Lake.

|Ferramenta | Orientação |
|---|--|
|Azure HDInsight | [Utilize a geração 2 de armazenamento do Azure Data Lake com clusters do HDInsight do Azure](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2) |
|Azure Databricks | [Geração 2 Lake armazenamento de dados do Azure](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html)<br><br>[Quickstart: Analisar dados de geração 2 de armazenamento do Azure Data Lake ao utilizar o Azure Databricks](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-databricks-account?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)<br><br>[Tutorial: Extrair, transformar e carregar dados com o Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|

## <a name="visualize-the-data"></a>Ver os dados

Pode utilizar uma combinação de serviços para criar representações visuais dos dados armazenados no Data Lake Storage Gen2.

![Visualizar dados no Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/visualize-data.png "Visualize os dados na geração 2 de armazenamento do Data Lake")

* Pode iniciar usando [Azure Data Factory para mover dados de geração 2 de armazenamento do Data Lake para o Azure SQL Data Warehouse](../../data-factory/copy-activity-overview.md)
* Depois disso, pode [integrar o Power BI com o Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-get-started-visualize-with-power-bi.md) para criar uma representação visual dos dados.

## <a name="download-the-data"></a>Transferir os dados

Pode também querer transferir ou mover dados de geração 2 de armazenamento do Azure Data Lake para cenários, tais como:

* Mova dados para outros repositórios a interface com os pipelines de processamento de dados existente. Por exemplo, pode querer mover dados de geração 2 de armazenamento do Data Lake para a base de dados do Azure SQL ou SQL Server no local.

* Transferir dados para o computador local para processamento nos ambientes de IDE durante a criação de protótipos de aplicação.

![Saída de dados de geração 2 de armazenamento do Data Lake](./media/data-lake-storage-data-scenarios/egress-data.png "saída de dados de geração 2 de armazenamento do Data Lake")

Aqui está uma lista de ferramentas que pode utilizar para transferir dados da geração 2 de armazenamento do Data Lake.

|Ferramenta | Orientação |
|---|--|
|Azure Data Factory | [Atividade de cópia numa fábrica de dados do Azure](https://docs.microsoft.com/azure/data-factory/copy-activity-overview) |
|Apache DistCop | [Utilizar o DistCp para copiar dados entre os Blobs de armazenamento do Azure e de geração 2 de armazenamento do Azure Data Lake](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-use-distcp) |
