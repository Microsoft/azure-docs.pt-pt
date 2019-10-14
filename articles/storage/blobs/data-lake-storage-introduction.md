---
title: Introdução Azure Data Lake Storage Gen2
description: Fornece uma visão geral de Azure Data Lake Storage Gen2
author: normesta
ms.service: storage
ms.topic: overview
ms.date: 10/11/2019
ms.author: normesta
ms.reviewer: jamesbak
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: d843e288297db656cca6e2a07f2e1f3322ebfa89
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2019
ms.locfileid: "72299644"
---
# <a name="introduction-to-azure-data-lake-storage-gen2"></a>Introdução ao Azure Data Lake Storage Gen2

Azure Data Lake Storage Gen2 é um conjunto de recursos dedicados à análise de Big Data, criado no [armazenamento de BLOBs do Azure](storage-blobs-introduction.md). Data Lake Storage Gen2 é o resultado da convergente dos recursos de nossos dois serviços de armazenamento existentes, do armazenamento de BLOBs do Azure e do Azure Data Lake Storage Gen1. Os recursos do [Azure data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/index), como a semântica do sistema de arquivos, o diretório e a segurança e a escala de nível de arquivo, são combinados com armazenamento em camadas de baixo custo, alta disponibilidade/recuperação de desastre do [armazenamento de BLOBs do Azure](storage-blobs-introduction.md).

## <a name="designed-for-enterprise-big-data-analytics"></a>Projetado para o Enterprise Big Data Analytics

Data Lake Storage Gen2 torna o armazenamento do Azure a base para criar lagos de dados corporativos no Azure. Projetado desde o início até a manutenção de vários petabytes de informações e, ao mesmo tempo, manter centenas de gigabits de taxa de transferência, Data Lake Storage Gen2 permite que você gerencie facilmente grandes quantidades de dados.

Uma parte fundamental da Data Lake Storage Gen2 é a adição de um [namespace hierárquico](data-lake-storage-namespace.md) ao armazenamento de BLOBs. O namespace hierárquico organiza objetos/arquivos em uma hierarquia de diretórios para acesso eficiente aos dados. Uma convenção comum de nomenclatura do repositório de objetos usa barras no nome para imitar uma estrutura hierárquica de diretórios. Essa estrutura se torna real com Data Lake Storage Gen2. Operações como renomear ou excluir um diretório se tornam operações de metadados atômicas únicas no diretório em vez de enumerar e processar todos os objetos que compartilham o prefixo de nome do diretório.

No passado, a análise baseada em nuvem tinha de comprometer as áreas de desempenho, gerenciamento e segurança. Data Lake Storage Gen2 trata de cada um desses aspectos das seguintes maneiras:

-   O **desempenho** é otimizado porque você não precisa copiar ou transformar dados como um pré-requisito para análise. O namespace hierárquico melhora muito o desempenho das operações de gerenciamento de diretório, o que melhora o desempenho geral do trabalho.

-   O **Gerenciamento** é mais fácil porque você pode organizar e manipular arquivos por meio de diretórios e subdiretórios.

-   A **segurança** é imposta porque você pode definir permissões POSIX em diretórios ou arquivos individuais.

-   A **economia de custo** é possível, pois data Lake Storage Gen2 é criada sobre o armazenamento de [BLOBs do Azure](storage-blobs-introduction.md)de baixo custo. Os recursos adicionais reduzem ainda mais o custo total de propriedade para executar a análise de Big Data no Azure.

## <a name="key-features-of-data-lake-storage-gen2"></a>Principais recursos do Data Lake Storage Gen2

-   **Acesso compatível com Hadoop**: o data Lake Storage Gen2 permite que você gerencie e acesse dados da mesma forma que faria com um [sistema de arquivos distribuído do Hadoop (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). O novo [Driver ABFS](data-lake-storage-abfs-driver.md) está disponível em todos os ambientes de Apache Hadoop, incluindo [Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/index) *,* [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/index)e [SQL data warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/) para acessar os dados armazenados no data Lake Storage Gen2.

-   **Um superconjunto de permissões POSIX**: o modelo de segurança para data Lake Gen2 dá suporte a permissões de ACL e POSIX, juntamente com alguma granularidade extra específica para data Lake Storage Gen2. As configurações podem ser configuradas por meio de Gerenciador de Armazenamento ou por meio de estruturas como Hive e Spark.

-   **Econômico: data Lake Storage Gen2**oferece capacidade e transações de armazenamento de baixo custo. Conforme as transições de dados passam por seu ciclo de vida completo, as taxas de cobrança mudam para reduzir os custos para um mínimo por meio de recursos internos como o [ciclo de vida do armazenamento de BLOBs do Azure](storage-lifecycle-management-concepts.md)

-   **Driver otimizado**: o driver ABFS é [otimizado especificamente](data-lake-storage-abfs-driver.md) para análise de Big Data. As APIs REST correspondentes são apresentadas por meio do ponto de extremidade `dfs.core.windows.net`.

### <a name="scalability"></a>Escalabilidade

O armazenamento do Azure é escalonável por design, independentemente de você acessar por meio de interfaces de armazenamento de BLOBs ou Data Lake Storage Gen2. Ele é capaz de armazenar e fornecer *muitos exabytes de dados*. Essa quantidade de armazenamento está disponível com a taxa de transferência medida em gigabits por segundo (Gbps) em altos níveis de operações de entrada/saída por segundo (IOPS). Além de apenas persistência, o processamento é executado em latências de solicitação quase constantes, que são medidas nos níveis de serviço, conta e arquivo.

### <a name="cost-effectiveness"></a>Economia de custo

Um dos muitos benefícios da criação de Data Lake Storage Gen2 sobre o armazenamento de BLOBs do Azure é o baixo custo da capacidade e das transações de armazenamento. Ao contrário de outros serviços de armazenamento em nuvem, os dados armazenados no Data Lake Storage Gen2 não precisam ser movidos ou transformados antes de executar a análise. Para obter mais informações sobre preços, consulte [preços do armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage).

Além disso, recursos como o [namespace hierárquico](data-lake-storage-namespace.md) melhoram significativamente o desempenho geral de muitos trabalhos de análise. Essa melhoria no desempenho significa que você precisa de menos capacidade de computação para processar a mesma quantidade de dados, resultando em um TCO (custo total de propriedade) mais baixo para o trabalho de análise de ponta a ponta.

### <a name="one-service-multiple-concepts"></a>Um serviço, vários conceitos

Data Lake Storage Gen2 é um recurso adicional para Big Data Analytics, criado com base no armazenamento de BLOBs do Azure. Embora haja muitos benefícios em aproveitar componentes existentes da plataforma de BLOBs para criar e operar data lagos para análise, ele leva a vários conceitos que descrevem as mesmas coisas compartilhadas.

A seguir estão as entidades equivalentes, conforme descrito por diferentes conceitos. A menos que especificado caso contrário, essas entidades são diretamente sinônimos:

| Conceito                                | Organização de nível superior | Organização de nível inferior                                            | Contêiner de dados |
|----------------------------------------|------------------------|---------------------------------------------------------------------|----------------|
| BLOBs – armazenamento de objeto de uso geral | Contentor              | Diretório virtual (somente SDK – não fornece manipulação atômica) | Blob           |
| Azure Data Lake Storage Gen2 – armazenamento de análise          | Contentor            | Diretório                                                           | Ficheiros           |

## <a name="supported-open-source-platforms"></a>Plataformas de software livre com suporte

Várias plataformas de software livre dão suporte a Data Lake Storage Gen2. Essas plataformas aparecem na tabela a seguir.

> [!NOTE]
> Somente as versões que aparecem nessa tabela têm suporte.

| Plataforma |  Versão (ões) com suporte | Mais Informações |
| --- | --- | --- |
| [HDInsight](https://azure.microsoft.com/services/hdinsight/) | 3.6 + | [Quais são os componentes Apache Hadoop e as versões disponíveis com o HDInsight?](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning?toc=%2Fen-us%2Fazure%2Fhdinsight%2Fstorm%2FTOC.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)
| [Hadoop](https://hadoop.apache.org/) | 3.2 + | [Arquivo morto de versões Apache Hadoop](https://hadoop.apache.org/release.html) |
| [Cloudera](https://www.cloudera.com/) | 6.1 + | [Notas de versão do Cloudera Enterprise 6. x](https://www.cloudera.com/documentation/enterprise/6/release-notes/topics/rg_cdh_6_release_notes.html) |
| [Azure Databricks](https://azure.microsoft.com/services/databricks/) | 5.1 + | [Versões do Databricks Runtime](https://docs.databricks.com/release-notes/runtime/databricks-runtime-ver.html) |
|[Hortonworks](https://hortonworks.com/)| 3.1. x + + | [Configurando o acesso a dados na nuvem](https://docs.hortonworks.com/HDPDocuments/Cloudbreak/Cloudbreak-2.9.0/cloud-data-access/content/cb_configuring-access-to-adls2.html) |

## <a name="supported-azure-services"></a>Serviços do Azure com suporte

Data Lake Storage Gen2 dá suporte a vários serviços do Azure que você pode usar para ingerir dados, executar análises e criar representações visuais. Para obter uma lista de serviços do Azure com suporte, consulte [integrar Azure data Lake Storage com os serviços do Azure](data-lake-store-integrate-with-azure-services.md).

## <a name="next-steps"></a>Passos seguintes

Os artigos a seguir descrevem alguns dos principais conceitos de Data Lake Storage Gen2 e detalhes sobre como armazenar, acessar, gerenciar e obter informações de seus dados:

- [Namespace hierárquico](data-lake-storage-namespace.md)
- [Criar uma conta de armazenamento](data-lake-storage-quickstart-create-account.md)
- [Acesso de vários protocolos no Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)
- [Integre Azure data Lake Storage com os serviços do Azure](data-lake-store-integrate-with-azure-services.md);
