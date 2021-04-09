---
title: Introdução do Azure Data Lake Storage Gen2
description: Leia uma introdução ao Azure Data Lake Storage Gen2. Aprenda as principais características. Reveja as funcionalidades de armazenamento blob suportadas, integrações de serviços Azure e plataformas.
author: normesta
ms.service: storage
ms.topic: overview
ms.date: 02/25/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 1c4d04e25bf8f7d981c998baafb468f04b66eaf1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98879903"
---
# <a name="introduction-to-azure-data-lake-storage-gen2"></a>Introdução ao Azure Data Lake Storage Gen2

Azure Data Lake Storage Gen2 é um conjunto de capacidades dedicadas à análise de big data, construídas no [armazenamento Azure Blob.](storage-blobs-introduction.md) 

Data Lake Storage Gen2 converge as capacidades do [Azure Data Lake Storage Gen1](../../data-lake-store/index.yml) com o armazenamento Azure Blob. Por exemplo, a Data Lake Storage Gen2 fornece semântica do sistema de ficheiros, segurança ao nível do ficheiro e escala. Como estas capacidades são construídas no armazenamento Blob, você também receberá armazenamento de baixo custo, tiered, com alta disponibilidade/capacidade de recuperação de desastres.

## <a name="designed-for-enterprise-big-data-analytics"></a>Projetado para analítica de big data da empresa

Data Lake Storage Gen2 faz do Azure Storage a base para a construção de lagos de dados empresariais em Azure. Projetado desde o início para o serviço de múltiplos petabytes de informação enquanto sustenta centenas de gigabits de produção, Data Lake Storage Gen2 permite-lhe gerir facilmente quantidades massivas de dados.

Uma parte fundamental do Data Lake Storage Gen2 é a adição de um [espaço hierárquico](data-lake-storage-namespace.md) de nomes para o armazenamento blob. O espaço hierárquico organiza objetos/ficheiros numa hierarquia de diretórios para um acesso eficiente aos dados. Uma convenção comum de nomeação de objetos usa cortes no nome para imitar uma estrutura hierárquica do diretório. Esta estrutura torna-se real com data lake storage gen2. Operações como renomear ou eliminar um diretório, tornam-se operações únicas de metadados atómicos no diretório. Não há necessidade de enumerar e processar todos os objetos que partilham o nome prefixo do diretório.

Data Lake Storage Gen2 baseia-se no armazenamento blob e melhora o desempenho, gestão e segurança das seguintes formas:

-   **O desempenho** é otimizado porque não precisa de copiar ou transformar dados como pré-requisito para análise. Em comparação com o espaço de nome plano no armazenamento blob, o espaço hierárquico melhora consideravelmente o desempenho das operações de gestão de diretórios, o que melhora o desempenho geral do trabalho.

-   **A gestão** é mais fácil porque pode organizar e manipular ficheiros através de diretórios e subdiretórios.

-   **A segurança** é executável porque pode definir permissões POSIX em diretórios ou ficheiros individuais.

Além disso, o Data Lake Storage Gen2 é muito rentável porque é construído em cima do [armazenamento low-cost Azure Blob.](storage-blobs-introduction.md) As características adicionais reduzem ainda mais o custo total de propriedade para executar big data analytics em Azure.

## <a name="key-features-of-data-lake-storage-gen2"></a>Principais características do Data Lake Storage Gen2

-   **Acesso compatível com Hadoop**: Data Lake Storage Gen2 permite-lhe gerir e aceder a dados tal como faria com um [Sistema de Ficheiros Distribuídos Hadoop (HDFS).](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html) O novo [controlador ABFS](data-lake-storage-abfs-driver.md) (usado para aceder aos dados) está disponível em todos os ambientes apache Hadoop. Estes ambientes incluem [Azure HDInsight,](../../hdinsight/index.yml) [Azure Databricks,](/azure/databricks/)e [Azure Synapse Analytics](../../synapse-analytics/index.yml).

-   **Um superconjunto de permissões POSIX**: O modelo de segurança para Data Lake Gen2 suporta permissões ACL e POSIX juntamente com alguma granularidade extra específica para data lake storage gen2. As definições podem ser configuradas através do Storage Explorer ou através de estruturas como a Colmeia e a Spark.

-   **Custo efetivo**: Data Lake Storage Gen2 oferece capacidade de armazenamento de baixo custo e transações. Características como [o ciclo de vida de armazenamento Azure Blob](storage-lifecycle-management-concepts.md) otimizam os custos à medida que os dados transitam através do seu ciclo de vida.

-   **Condutor otimizado**: O condutor da ABFS está [otimizado especificamente](data-lake-storage-abfs-driver.md) para análise de big data. As APIs de REST correspondentes são emergidas através do ponto `dfs.core.windows.net` final.

### <a name="scalability"></a>Escalabilidade

O Azure Storage é escalável por design, quer aceda através de interfaces de armazenamento do Data Lake Storage Gen2 ou Blob. É capaz de armazenar e servir *muitos exabytes de dados.* Esta quantidade de armazenamento está disponível com produção medida em gigabits por segundo (Gbps) em altos níveis de operações de entrada/saída por segundo (IOPS). O processamento é executado em latências quase constantes por pedido que são medidas nos níveis de serviço, conta e arquivo.

### <a name="cost-effectiveness"></a>Rentabilidade

Como a Data Lake Storage Gen2 é construída em cima do armazenamento da Azure Blob, a capacidade de armazenamento e os custos de transação são mais baixos. Ao contrário de outros serviços de armazenamento em nuvem, não precisa mover ou transformar os seus dados antes de poder analisá-los. Para obter mais informações sobre preços, consulte [os preços de Armazenamento Azure](https://azure.microsoft.com/pricing/details/storage).

Além disso, características como o [espaço hierárquico melhoram](data-lake-storage-namespace.md) significativamente o desempenho global de muitos trabalhos analíticos. Esta melhoria no desempenho significa que você precisa de menos poder de computação para processar a mesma quantidade de dados, resultando num menor custo total de propriedade (TCO) para o trabalho de análise de ponta a ponta.

### <a name="one-service-multiple-concepts"></a>Um serviço, vários conceitos

Como o Data Lake Storage Gen2 é construído em cima do armazenamento Azure Blob, vários conceitos podem descrever as mesmas coisas partilhadas.

Seguem-se as entidades equivalentes, conforme descrito por diferentes conceitos. Salvo especificação em contrário, estas entidades são diretamente sinónimos:

| Conceito                                | Organização de Alto Nível | Organização de nível inferior                                            | Contentor de Dados |
|----------------------------------------|------------------------|---------------------------------------------------------------------|----------------|
| Blobs - Armazenamento de objetos de finalidade geral | Contentor              | Diretório virtual (apenas SDK – não fornece manipulação atómica) | Blobs           |
| Azure Data Lake Storage Gen2 – Analytics Storage          | Contentor            | Diretório                                                           | Ficheiro           |

## <a name="supported-blob-storage-features"></a>Funcionalidades suportadas do Armazenamento de Blobs

Funcionalidades de armazenamento de blob, tais como [registo de diagnóstico,](../common/storage-analytics-logging.md) [níveis de acesso](storage-blob-storage-tiers.md)e políticas de [gestão do ciclo de vida blob armazenamento](storage-lifecycle-management-concepts.md) estão disponíveis na sua conta. 

Para obter uma lista de funcionalidades de armazenamento blob suportadas, consulte [as funcionalidades de Armazenamento Blob disponíveis no Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md).

## <a name="supported-azure-service-integrations"></a>Integrações de serviços suportados aZure

Data Lake Storage gen2 suporta vários serviços Azure. Pode usá-los para ingerir dados, realizar análises e criar representações visuais. Para obter uma lista de serviços Azure suportados, consulte [os serviços Azure que suportam a Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md).

## <a name="supported-open-source-platforms"></a>Plataformas open source suportadas

Várias plataformas de código aberto suportam data lake storage gen2. Para obter uma lista completa, consulte [plataformas Open source que suportam a Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md).

## <a name="see-also"></a>Ver também

- [Problemas conhecidos com Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)
- [Acesso multi-protocolo no Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)