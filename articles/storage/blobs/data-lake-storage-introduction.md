---
title: Introdução de geração 2 de Lake armazenamento de dados do Azure
description: Fornece uma descrição geral de geração 2 de armazenamento do Azure Data Lake
services: storage
author: jamesbak
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: jamesbak
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: b25a47e929ef540ad3a74a86d431962d37cde434
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58009429"
---
# <a name="introduction-to-azure-data-lake-storage-gen2"></a>Introdução ao Azure Data Lake Storage Gen2

Geração de armazenamento 2 do Azure Data Lake é um conjunto de recursos dedicado a análise de macrodados, criado nos [armazenamento de Blobs do Azure](storage-blobs-introduction.md). Geração 2 de armazenamento do Data Lake é o resultado da convergir as capacidades das nossas dois serviços de armazenamento existente, o armazenamento de Blobs do Azure e a geração 1 de armazenamento do Azure Data Lake. Funcionalidades do [Gen1 de armazenamento do Azure Data Lake](https://docs.microsoft.com/azure/data-lake-store/index), como semântica do sistema de arquivo, diretório e segurança ao nível do ficheiro e dimensionamento são combinados com o armazenamento em camadas e de baixo custo, capacidades de recuperação de alta disponibilidade/desastre partir [Armazenamento de Blobs do azure](storage-blobs-introduction.md).

## <a name="designed-for-enterprise-big-data-analytics"></a>Concebido para análise de macrodados de enterprise

Geração 2 de armazenamento do Data Lake torna o armazenamento do Azure a base para a criação de enterprise lakes de dados no Azure. Projetada desde o início para atender a vários petabytes de informações ao suporte a centenas de gigabits de débito, geração 2 de armazenamento do Data Lake permite-lhe gerir facilmente quantidades enormes de dados.

Uma parte fundamental de geração 2 de armazenamento do Data Lake é a adição de um [espaço de nomes hierárquico](data-lake-storage-namespace.md) para armazenamento de Blobs. O espaço de nomes hierárquico organiza os ficheiros de objetos de/para uma hierarquia de diretórios para acesso a dados eficiente. Uma convenção de nomenclatura objeto ao arquivo comuns utiliza barras no nome para simular uma estrutura de diretório hierárquica. Esta estrutura torna-se real com geração 2 de armazenamento do Data Lake. Operações como mudar o nome ou eliminar um diretório tornam-se operações de metadados de atômica única no diretório em vez de enumerar e processamento de todos os objetos que compartilham o prefixo do nome do diretório.

No passado, análise baseada na cloud tinha de comprometer nas áreas de desempenho, gerenciamento e segurança. Geração 2 de armazenamento do Data Lake resolve cada um desses aspectos das seguintes formas:

-   **Desempenho** está otimizado porque não é necessário copiar ou transformar os dados como um pré-requisito para análise. O espaço de nomes hierárquico melhora consideravelmente o desempenho de operações de gerenciamento de diretório, que melhora o desempenho geral da tarefa.

-   **Gestão** é mais fácil, pois pode organizar e manipular arquivos por meio de diretórios e subdiretórios.

-   **Segurança** é praticável, pois pode definir permissões de acesso POSIX em diretórios ou ficheiros individuais.

-   **Relação custo-eficácia** se tornou possível de geração 2 de armazenamento do Data Lake é criado com base em cima o baixo custo [armazenamento de Blobs do Azure](storage-blobs-introduction.md). As funcionalidades adicionais reduzir o custo total de propriedade para executar a análise de macrodados no Azure.

## <a name="key-features-of-data-lake-storage-gen2"></a>Principais recursos de geração 2 de armazenamento do Data Lake

-   **Acesso compatível do Hadoop**: Geração 2 de armazenamento do Data Lake permite-lhe gerir e aceder aos dados, tal como faria com um [Hadoop Distributed File System (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). A nova [ABFS driver](data-lake-storage-abfs-driver.md) está disponível em todos os ambientes do Apache Hadoop, incluindo [Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/index)*,* [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/index), e [O SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/) para acessar dados armazenados na geração 2 de armazenamento do Data Lake.

-   **Um conjunto mais amplo de permissões de POSIX**: O modelo de segurança para a geração 2 do Data Lake suporta permissões de ACL e POSIX, juntamente com alguns extra granularidade específica de geração 2 de armazenamento do Data Lake. Definições podem ser configuradas através do Explorador de armazenamento ou por meio de estruturas como o Hive e do Spark.

-   **Custo-eficiência**: Geração 2 de armazenamento do Data Lake oferece a capacidade de armazenamento de baixo custo e transações. Como transições de dados por meio de seu ciclo de vida completo, taxas de faturação alterar os custos de manutenção ao mínimo possível por meio de recursos incorporados, tal como [ciclo de vida de armazenamento de Blobs do Azure](storage-lifecycle-management-concepts.md).

-   **Controlador otimizada**: É o controlador de abfs [otimizado especificamente](data-lake-storage-abfs-driver.md) para análise de macrodados. As APIs REST correspondente são apresentadas através do ponto final de dfs, dfs.core.windows.net.

### <a name="scalability"></a>Escalabilidade

Armazenamento do Azure é dimensionável por design, se acessar por meio de interfaces de armazenamento de BLOBs ou de geração 2 de armazenamento do Data Lake. Ele é capaz de armazenar e servir *muitos exabytes de dados*. Esta quantidade de armazenamento está disponível com débito medido em gigabits por segundo (Gbps) num altos níveis de operações de entrada/saída por segundo (IOPS). Além apenas persistência, o processamento é executado em latências de perto constante por solicitação, que são medidas ao nível de ficheiro, da conta e serviço.

### <a name="cost-effectiveness"></a>Relação custo-eficácia

Um dos muitos benefícios da criação de geração 2 de armazenamento do Data Lake com base no armazenamento de Blobs do Azure é de baixo custo de capacidade de armazenamento e transações. Ao contrário de outros serviços de armazenamento na cloud, os dados armazenados na geração 2 de armazenamento do Data Lake não são necessários ser movida ou transformadas antes de efetuar a análise. Para obter mais informações sobre preços, consulte [preços de armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage).

Além disso, funcionalidades como a [espaço de nomes hierárquico](data-lake-storage-namespace.md) melhorar significativamente o desempenho geral de muitas tarefas de análise. Esta melhoria no desempenho significa que exigem menos poder de computação para processar a mesma quantidade de dados, que resulta num menor custo total de propriedade (TCO) para a tarefa de análise de ponto-a-ponto.

### <a name="one-service-multiple-concepts"></a>Um serviço, vários conceitos

Geração 2 de armazenamento do Data Lake é uma capacidade adicional para análise de macrodados, criado com base no armazenamento de Blobs do Azure. Embora haja muitos benefícios em aproveitar os componentes de plataforma existentes de Blobs para criar e operar data lakes para análise, ele leva a vários conceitos descrever as coisas as mesmo, partilhadas.

Seguem-se as entidades equivalentes, conforme descrito pelo conceitos diferentes. A menos que especificado, caso contrário, estas entidades são sinônimos de diretamente:

| Conceito                                | Organização de nível superior | Organização de nível inferior                                            | Contentor de dados |
|----------------------------------------|------------------------|---------------------------------------------------------------------|----------------|
| BLOBs – armazenamento de objetos de fins gerais | Contentor              | Diretório virtual (SDK apenas – não fornece manipulação atômica) | Blobs           |
| ADLS Gen2 – análise de armazenamento          | Sistema de ficheiros             | Diretório                                                           | Ficheiro           |

## <a name="supported-open-source-platforms"></a>Código-fonte aberto plataformas suportadas

Várias plataformas de código-fonte aberto suportam a geração 2 de armazenamento do Data Lake. Essas plataformas são apresentados na tabela seguinte.

> [!NOTE]
> São suportadas apenas as versões que aparecem nesta tabela.

| Plataforma |  Versões suportadas | Mais Informações |
| --- | --- | --- |
| [HDInsight](https://azure.microsoft.com/services/hdinsight/) | 3.6 + | [Quais são os componentes do Apache Hadoop e versões disponíveis com o HDInsight?](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning?toc=%2Fen-us%2Fazure%2Fhdinsight%2Fstorm%2FTOC.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)
| [Hadoop](https://hadoop.apache.org/) | 3.2+ | [Arquivo de versões do Apache Hadoop](https://hadoop.apache.org/release.html) |
| [Cloudera](https://www.cloudera.com/) | 6.1 + | [Notas de versão do 6.x Cloudera Enterprise](https://www.cloudera.com/documentation/enterprise/6/release-notes/topics/rg_cdh_6_release_notes.html) |
| [Azure Databricks](https://azure.microsoft.com/services/databricks/) | 5.1+ | [Versões de Runtime do Databricks](https://docs.databricks.com/release-notes/runtime/databricks-runtime-ver.html) |
|[Hortonworks](https://hortonworks.com/)| 3.1.x++ | [Configurar o acesso a dados na cloud](https://docs.hortonworks.com/HDPDocuments/Cloudbreak/Cloudbreak-2.9.0/cloud-data-access/content/cb_configuring-access-to-adls2.html) |

## <a name="next-steps"></a>Passos Seguintes

Os seguintes artigos descrevem alguns dos principais conceitos de geração 2 de armazenamento do Data Lake e detalhes como armazenar, acessar, gerir e obter informações a partir dos seus dados:

-   [Espaço de nomes hierárquico](data-lake-storage-namespace.md)
-   [Criar uma conta de armazenamento](data-lake-storage-quickstart-create-account.md)
-   [Utilizar uma conta de geração 2 de armazenamento do Data Lake no Azure Databricks](data-lake-storage-quickstart-create-databricks-account.md)
