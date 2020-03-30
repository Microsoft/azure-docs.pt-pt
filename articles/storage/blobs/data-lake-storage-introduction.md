---
title: Introdução do Armazenamento do Lago Azure Data Gen2
description: Fornece uma visão geral do Azure Data Lake Storage Gen2
author: normesta
ms.service: storage
ms.topic: overview
ms.date: 02/25/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 2f920e29fafdc55478e0e2c16d683bd1c3bc81d8
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "78942922"
---
# <a name="introduction-to-azure-data-lake-storage-gen2"></a>Introdução ao Armazenamento de Lagos De Dados Azure Gen2

Azure Data Lake Storage Gen2 é um conjunto de capacidades dedicadas à análise de big data, construídas sobre [o armazenamento de Blob Azure.](storage-blobs-introduction.md) Data Lake Storage Gen2 é o resultado da convergência das capacidades dos nossos dois serviços de armazenamento existentes, armazenamento Azure Blob e Armazenamento de Lago De dados Azure Gen1. As funcionalidades do [Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/index), tais como semântica do sistema de ficheiros, diretório e segurança e escala de nível de ficheiros são combinadas com armazenamento e escala de baixo custo, hierárquicos, alta disponibilidade/capacidade de recuperação de desastres a partir do [armazenamento de Blob Azure.](storage-blobs-introduction.md)

## <a name="designed-for-enterprise-big-data-analytics"></a>Projetado para aanálise de big data da empresa

Data Lake Storage Gen2 faz do Azure Storage a base para a construção de lagos de dados empresariais em Azure. Projetado desde o início para servir vários petabytes de informação, ao mesmo tempo que sustenta centenas de gigabits de entrada, data Lake Storage Gen2 permite-lhe gerir facilmente quantidades massivas de dados.

Uma parte fundamental do Data Lake Storage Gen2 é a adição de um [espaço hierárquico](data-lake-storage-namespace.md) ao armazenamento blob. O espaço hierárquico organiza objetos/ficheiros numa hierarquia de diretórios para um acesso eficiente de dados. Uma convenção de nomeação de loja de objetos comuns usa cortes no nome para imitar uma estrutura hierárquica de diretório. Esta estrutura torna-se real com data lake storage Gen2. Operações como renomear ou apagar um diretório tornam-se operações únicas de metadados atómicos no diretório em vez de enumerar e processar todos os objetos que partilham o nome prefixo do diretório.

Data Lake Storage Gen2 baseia-se no armazenamento blob e melhora o desempenho, gestão e segurança das seguintes formas:

-   **O desempenho** é otimizado porque não precisa de copiar ou transformar dados como pré-requisito para análise. Em comparação com o espaço de nome plano no armazenamento blob, o espaço hierárquico de nomes melhora consideravelmente o desempenho das operações de gestão de diretórios, o que melhora o desempenho global do trabalho.

-   **A gestão** é mais fácil porque pode organizar e manipular ficheiros através de diretórios e subdiretórios.

-   **A segurança** é executável porque pode definir permissões POSIX em diretórios ou ficheiros individuais.

Além disso, data Lake Storage Gen2 é muito rentável porque é construído em cima do [armazenamento azure blob](storage-blobs-introduction.md)de baixo custo . As funcionalidades adicionais reduzem ainda mais o custo total de propriedade para executar analítica de big data no Azure.

## <a name="key-features-of-data-lake-storage-gen2"></a>Principais características do Data Lake Storage Gen2

-   **Acesso compatível com hadoop**: Data Lake Storage Gen2 permite-lhe gerir e aceder a dados tal como faria com um Sistema de [Ficheiros Distribuídos Hadoop (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). O novo [controlador ABFS](data-lake-storage-abfs-driver.md) está disponível em todos os ambientes Apache Hadoop, incluindo [Azure HDInsight,](https://docs.microsoft.com/azure/hdinsight/index)*,* [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/index)e [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/) para aceder aos dados armazenados em Data Lake Storage Gen2.

-   **Um superconjunto de permissões POSIX**: O modelo de segurança para Data Lake Gen2 suporta permissões ACL e POSIX juntamente com alguma granularidade extra específica para Data Lake Storage Gen2. As definições podem ser configuradas através do Storage Explorer ou através de estruturas como a Hive e a Spark.

-   **Custo eficaz:** Data Lake Storage Gen2 oferece capacidade de armazenamento de baixo custo e transações. À medida que os dados transitam através do seu ciclo de vida completo, as taxas de faturação mudam mantendo os custos no mínimo através de funcionalidades incorporadas, como o ciclo de vida de [armazenamento de Blob Azure.](storage-lifecycle-management-concepts.md)

-   **Condutor otimizado**: O condutor da ABFS está [otimizado especificamente](data-lake-storage-abfs-driver.md) para análise de big data. As APIs de REPOUSO correspondentes `dfs.core.windows.net`são surgidas através do ponto final .

### <a name="scalability"></a>Escalabilidade

O Armazenamento Azure é escalável por design, quer aceda através de interfaces de armazenamento de data Lake Gen2 ou Blob. É capaz de armazenar e servir *muitos exabytes de dados.* Esta quantidade de armazenamento está disponível com a produção medida em gigabits por segundo (Gbps) em níveis elevados de operações de entrada/saída por segundo (IOPS). Além da persistência, o processamento é executado em tardios quase constantes por pedido que são medidos nos níveis de serviço, conta e ficheiros.

### <a name="cost-effectiveness"></a>Eficácia de custos

Um dos muitos benefícios da construção do Data Lake Storage Gen2 em cima do armazenamento da Blob Azure é o baixo custo de capacidade de armazenamento e transações. Ao contrário de outros serviços de armazenamento em nuvem, os dados armazenados em Data Lake Storage Gen2 não são obrigados a ser movidos ou transformados antes de realizar análises. Para obter mais informações sobre preços, consulte o preço do [Armazenamento De Azure.](https://azure.microsoft.com/pricing/details/storage)

Além disso, características como o [espaço hierárquico](data-lake-storage-namespace.md) de nomes melhoram significativamente o desempenho global de muitos trabalhos de análise. Esta melhoria no desempenho significa que você precisa de menos poder de computação para processar a mesma quantidade de dados, resultando num menor custo total de propriedade (TCO) para o trabalho de análise de ponta a ponta.

### <a name="one-service-multiple-concepts"></a>Um serviço, vários conceitos

Data Lake Storage Gen2 é uma capacidade adicional para a análise de big data, construída em cima do armazenamento Azure Blob. Embora existam muitos benefícios em alavancar os componentes existentes da plataforma de Blobs para criar e operar lagos de dados para análise, isso leva a múltiplos conceitos descrevendo as mesmas coisas partilhadas.

Seguem-se as entidades equivalentes, conforme descrito por diferentes conceitos. Salvo especificação em contrário, estas entidades são diretamente sinónimo:

| Conceito                                | Organização de Alto Nível | Organização de Nível Inferior                                            | Recipiente de dados |
|----------------------------------------|------------------------|---------------------------------------------------------------------|----------------|
| Blobs – Armazenamento de objetos de propósito geral | Contentor              | Diretório virtual (apenas SDK – não fornece manipulação atómica) | Blobs           |
| Armazenamento de lagos azure data Gen2 – Armazenamento analítico          | Contentor            | Diretório                                                           | Ficheiro           |

## <a name="supported-blob-storage-features"></a>Recursos de armazenamento Blob suportados

As funcionalidades de armazenamento blob, tais como [a exploração madeireira de diagnóstico,](../common/storage-analytics-logging.md)os níveis de [acesso,](storage-blob-storage-tiers.md)e as políticas de gestão do ciclo de [vida blob Storage](storage-lifecycle-management-concepts.md) funcionam agora com contas que têm um espaço de nome hierárquico. Por isso, pode ativar espaços hierárquicos nas suas contas de armazenamento Blob sem perder acesso a estas funcionalidades. 

Para obter uma lista de funcionalidades de armazenamento Blob suportadas, consulte [as funcionalidades de armazenamento blob disponíveis no Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md).

## <a name="supported-azure-service-integrations"></a>Integrações de serviços Azure suportadas

Data Lake Storage gen2 suporta vários serviços Azure que você pode usar para ingerir dados, realizar análises e criar representações visuais. Para obter uma lista de serviços Azure suportados, consulte [os serviços Azure que suportam o Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md).

## <a name="supported-open-source-platforms"></a>Plataformas de código aberto suportadas

Várias plataformas de código aberto suportam data Lake Storage Gen2. Para obter uma lista completa, consulte [plataformas Open source que suportam o Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md).

## <a name="see-also"></a>Consulte também

- [Questões conhecidas com Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)
- [Acesso multiprotocolo ao Armazenamento de Lagos De Dados Azure](data-lake-storage-multi-protocol-access.md)


