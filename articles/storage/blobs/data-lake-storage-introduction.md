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
ms.openlocfilehash: 75bd27f0945c66b9757055c0777b43a050ba67d7
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2020
ms.locfileid: "77920999"
---
# <a name="introduction-to-azure-data-lake-storage-gen2"></a>Introdução ao Armazenamento de Lagos De Dados Azure Gen2

Azure Data Lake Storage Gen2 é um conjunto de capacidades dedicadas à análise de big data, construídas sobre [o armazenamento de Blob Azure.](storage-blobs-introduction.md) Geração 2 de armazenamento do Data Lake é o resultado da convergir as capacidades das nossas dois serviços de armazenamento existente, o armazenamento de Blobs do Azure e a geração 1 de armazenamento do Azure Data Lake. As funcionalidades do [Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/index), tais como semântica do sistema de ficheiros, diretório e segurança e escala de nível de ficheiros são combinadas com armazenamento e escala de baixo custo, hierárquicos, alta disponibilidade/capacidade de recuperação de desastres a partir do [armazenamento de Blob Azure.](storage-blobs-introduction.md)

## <a name="designed-for-enterprise-big-data-analytics"></a>Concebido para análise de macrodados de enterprise

Geração 2 de armazenamento do Data Lake torna o armazenamento do Azure a base para a criação de enterprise lakes de dados no Azure. Projetada desde o início para atender a vários petabytes de informações ao suporte a centenas de gigabits de débito, geração 2 de armazenamento do Data Lake permite-lhe gerir facilmente quantidades enormes de dados.

Uma parte fundamental do Data Lake Storage Gen2 é a adição de um [espaço hierárquico](data-lake-storage-namespace.md) ao armazenamento blob. O espaço de nomes hierárquico organiza os ficheiros de objetos de/para uma hierarquia de diretórios para acesso a dados eficiente. Uma convenção de nomenclatura objeto ao arquivo comuns utiliza barras no nome para simular uma estrutura de diretório hierárquica. Esta estrutura torna-se real com geração 2 de armazenamento do Data Lake. Operações como mudar o nome ou eliminar um diretório tornam-se operações de metadados de atômica única no diretório em vez de enumerar e processamento de todos os objetos que compartilham o prefixo do nome do diretório.

No passado, análise baseada na cloud tinha de comprometer nas áreas de desempenho, gerenciamento e segurança. Geração 2 de armazenamento do Data Lake resolve cada um desses aspectos das seguintes formas:

-   **O desempenho** é otimizado porque não precisa de copiar ou transformar dados como pré-requisito para análise. O espaço de nomes hierárquico melhora consideravelmente o desempenho de operações de gerenciamento de diretório, que melhora o desempenho geral da tarefa.

-   **A gestão** é mais fácil porque pode organizar e manipular ficheiros através de diretórios e subdiretórios.

-   **A segurança** é executável porque pode definir permissões POSIX em diretórios ou ficheiros individuais.

-   **A eficácia** dos custos é possível, uma vez que o Data Lake Storage Gen2 é construído em cima do armazenamento de [blob azure](storage-blobs-introduction.md)de baixo custo. As funcionalidades adicionais reduzir o custo total de propriedade para executar a análise de macrodados no Azure.

## <a name="key-features-of-data-lake-storage-gen2"></a>Principais recursos de geração 2 de armazenamento do Data Lake

-   **Acesso compatível com hadoop**: Data Lake Storage Gen2 permite-lhe gerir e aceder a dados tal como faria com um Sistema de [Ficheiros Distribuídos Hadoop (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). O novo [controlador ABFS](data-lake-storage-abfs-driver.md) está disponível em todos os ambientes Apache Hadoop, incluindo [Azure HDInsight,](https://docs.microsoft.com/azure/hdinsight/index) [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/index)e [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/) para aceder aos dados armazenados em Data Lake Storage Gen2.

-   **Um superconjunto de permissões POSIX**: O modelo de segurança para Data Lake Gen2 suporta permissões ACL e POSIX juntamente com alguma granularidade extra específica para Data Lake Storage Gen2. Definições podem ser configuradas através do Explorador de armazenamento ou por meio de estruturas como o Hive e do Spark.

-   **Custo eficaz:** Data Lake Storage Gen2 oferece capacidade de armazenamento de baixo custo e transações. À medida que os dados transitam através do seu ciclo de vida completo, as taxas de faturação mudam mantendo os custos no mínimo através de funcionalidades incorporadas, como o ciclo de vida de [armazenamento de Blob Azure.](storage-lifecycle-management-concepts.md)

-   **Condutor otimizado**: O condutor da ABFS está [otimizado especificamente](data-lake-storage-abfs-driver.md) para análise de big data. As APIs de REPOUSO correspondentes são surgidas através do ponto final `dfs.core.windows.net`.

### <a name="scalability"></a>Escalabilidade

Armazenamento do Azure é dimensionável por design, se acessar por meio de interfaces de armazenamento de BLOBs ou de geração 2 de armazenamento do Data Lake. É capaz de armazenar e servir *muitos exabytes de dados.* Esta quantidade de armazenamento está disponível com débito medido em gigabits por segundo (Gbps) num altos níveis de operações de entrada/saída por segundo (IOPS). Além apenas persistência, o processamento é executado em latências de perto constante por solicitação, que são medidas ao nível de ficheiro, da conta e serviço.

### <a name="cost-effectiveness"></a>Relação custo-eficácia

Um dos muitos benefícios da criação de geração 2 de armazenamento do Data Lake com base no armazenamento de Blobs do Azure é de baixo custo de capacidade de armazenamento e transações. Ao contrário de outros serviços de armazenamento na cloud, os dados armazenados na geração 2 de armazenamento do Data Lake não são necessários ser movida ou transformadas antes de efetuar a análise. Para obter mais informações sobre preços, consulte o preço do [Armazenamento De Azure.](https://azure.microsoft.com/pricing/details/storage)

Além disso, características como o [espaço hierárquico](data-lake-storage-namespace.md) de nomes melhoram significativamente o desempenho global de muitos trabalhos de análise. Esta melhoria no desempenho significa que exigem menos poder de computação para processar a mesma quantidade de dados, que resulta num menor custo total de propriedade (TCO) para a tarefa de análise de ponto-a-ponto.

### <a name="one-service-multiple-concepts"></a>Um serviço, vários conceitos

Geração 2 de armazenamento do Data Lake é uma capacidade adicional para análise de macrodados, criado com base no armazenamento de Blobs do Azure. Embora haja muitos benefícios em aproveitar os componentes de plataforma existentes de Blobs para criar e operar data lakes para análise, ele leva a vários conceitos descrever as coisas as mesmo, partilhadas.

Seguem-se as entidades equivalentes, conforme descrito pelo conceitos diferentes. A menos que especificado, caso contrário, estas entidades são sinônimos de diretamente:

| Conceito                                | Organização de nível superior | Organização de nível inferior                                            | Contentor de dados |
|----------------------------------------|------------------------|---------------------------------------------------------------------|----------------|
| BLOBs – armazenamento de objetos de fins gerais | Contentor              | Diretório virtual (SDK apenas – não fornece manipulação atômica) | Blobs           |
| Armazenamento de lagos azure data Gen2 – Armazenamento analítico          | Contentor            | Diretório                                                           | Ficheiro           |

## <a name="supported-blob-storage-features"></a>Recursos de armazenamento Blob suportados

As funcionalidades de armazenamento blob, tais como [a exploração madeireira de diagnóstico,](../common/storage-analytics-logging.md)os níveis de [acesso,](storage-blob-storage-tiers.md)e as políticas de gestão do ciclo de [vida blob Storage](storage-lifecycle-management-concepts.md) funcionam agora com contas que têm um espaço de nome hierárquico. Por isso, pode ativar espaços hierárquicos nas suas contas de armazenamento Blob sem perder acesso a estas funcionalidades. 

Para obter uma lista de funcionalidades de armazenamento Blob suportadas, consulte [as funcionalidades de armazenamento blob disponíveis no Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md).

## <a name="supported-azure-service-integrations"></a>Integrações de serviços Azure suportadas

Data Lake Storage gen2 suporta vários serviços Azure que você pode usar para ingerir dados, realizar análises e criar representações visuais. Para obter uma lista de serviços Azure suportados, consulte [os serviços Azure que suportam o Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md).

## <a name="supported-open-source-platforms"></a>Código-fonte aberto plataformas suportadas

Várias plataformas de código-fonte aberto suportam a geração 2 de armazenamento do Data Lake. Para obter uma lista completa, consulte [plataformas Open source que suportam o Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md).

## <a name="see-also"></a>Consulte também

- [Questões conhecidas com Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)
- [Acesso multiprotocolo ao Armazenamento de Lagos De Dados Azure](data-lake-storage-multi-protocol-access.md)


