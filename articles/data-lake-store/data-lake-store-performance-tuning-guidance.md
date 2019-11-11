---
title: Ajuste de Azure Data Lake Storage Gen1 desempenho
description: Descreve como ajustar Azure Data Lake Storage Gen1 para desempenho.
author: stewu
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 06/30/2017
ms.author: stewu
ms.openlocfilehash: 2521700e0f07691541ee6cbbf085a8be72f08129
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904629"
---
# <a name="tune-azure-data-lake-storage-gen1-for-performance"></a>Ajustar Azure Data Lake Storage Gen1 para desempenho

O Data Lake Storage Gen1 dá suporte à alta taxa de transferência para análise intensiva de e/s e movimentação de dados. Em Data Lake Storage Gen1, usando toda a taxa de transferência disponível – a quantidade de dados que podem ser lidos ou gravados por segundo – é importante para obter o melhor desempenho. Isso é obtido com o desempenho máximo de leituras e gravações em paralelo.

![Desempenho de Data Lake Storage Gen1](./media/data-lake-store-performance-tuning-guidance/throughput.png)

Data Lake Storage Gen1 pode dimensionar para fornecer a taxa de transferência necessária para todo o cenário de análise. Por padrão, uma conta de Data Lake Storage Gen1 fornece uma taxa de transferência suficientemente automática para atender às necessidades de uma categoria ampla de casos de uso. Para os casos em que os clientes têm o limite padrão, a conta de Data Lake Storage Gen1 pode ser configurada para fornecer mais produtividade contatando o suporte da Microsoft.

## <a name="data-ingestion"></a>Ingestão de dados

Ao ingerir dados de um sistema de origem para Data Lake Storage Gen1, é importante considerar que o hardware de origem, o hardware de rede de origem e a conectividade de rede para Data Lake Storage Gen1 podem ser o afunilamento.

![Desempenho de Data Lake Storage Gen1](./media/data-lake-store-performance-tuning-guidance/bottleneck.png)

É importante garantir que a movimentação de dados não seja afetada por esses fatores.

### <a name="source-hardware"></a>Hardware de origem

Se você estiver usando máquinas locais ou VMs no Azure, deverá selecionar cuidadosamente o hardware apropriado. Para hardware de disco de origem, prefira SSDs para HDDs e escolha o hardware de disco com eixos mais rápidos. Para hardware de rede de origem, use as NICs mais rápidas possíveis. No Azure, recomendamos as VMs D14 do Azure que têm o hardware de rede e de disco adequadamente avançado.

### <a name="network-connectivity-to-data-lake-storage-gen1"></a>Conectividade de rede para Data Lake Storage Gen1

A conectividade de rede entre seus dados de origem e Data Lake Storage Gen1 pode, às vezes, ser o afunilamento. Quando os dados de origem estiverem no local, considere usar um link dedicado com o [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) . Se os dados de origem estiverem no Azure, o desempenho será melhor quando os dados estiverem na mesma região do Azure que a conta de Data Lake Storage Gen1.

### <a name="configure-data-ingestion-tools-for-maximum-parallelization"></a>Configurar as ferramentas de ingestão de dados para a paralelização máxima

Depois de solucionar os afunilamentos de conectividade de rede e hardware de origem, você estará pronto para configurar suas ferramentas de ingestão. A tabela a seguir resume as principais configurações de várias ferramentas de ingestão populares e fornece artigos de ajuste de desempenho detalhados para elas. Para saber mais sobre qual ferramenta usar para seu cenário, visite este [artigo](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-data-scenarios).

| Ferramenta          | Definições | Mais detalhes                                                                 |
|--------------------|------------------------------------------------------|------------------------------|
| PowerShell       | PerFileThreadCount, ConcurrentFileCount | [Ligação](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-powershell) |
| AdlCopy    | Unidades de Azure Data Lake Analytics | [Ligação](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob#performance-considerations-for-using-adlcopy)         |
| DistCp            | -m (mapeador) | [Ligação](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-wasb-distcp#performance-considerations-while-using-distcp)                             |
| Azure Data Factory| parallelCopies | [Ligação](../data-factory/copy-activity-performance.md)                          |
| Sqoop           | FS. Azure. Block. Size,-m (mapeador) | [Ligação](https://blogs.msdn.microsoft.com/bigdatasupport/2015/02/17/sqoop-job-performance-tuning-in-hdinsight-hadoop/)        |

## <a name="structure-your-data-set"></a>Estruturar seu conjunto de dados

Quando os dados são armazenados em Data Lake Storage Gen1, o tamanho do arquivo, o número de arquivos e a estrutura de pastas afetam o desempenho. A seção a seguir descreve as práticas recomendadas nessas áreas.

### <a name="file-size"></a>Tamanho dos ficheiros

Normalmente, os mecanismos de análise, como o HDInsight e o Azure Data Lake Analytics, têm uma sobrecarga por arquivo. Se você armazenar seus dados como muitos arquivos pequenos, isso pode afetar negativamente o desempenho.

Em geral, Organize seus dados em arquivos de tamanho maior para melhorar o desempenho. Como regra geral, organize conjuntos de dados em arquivos de 256 MB ou mais. Em alguns casos, como imagens e dados binários, não é possível processá-los em paralelo. Nesses casos, é recomendável manter arquivos individuais em 2 GB.

Às vezes, os pipelines de dados têm controle limitado sobre os dados brutos que têm muitos arquivos pequenos. É recomendável ter um processo de "culinária" que gera arquivos maiores a serem usados para aplicativos downstream.

### <a name="organize-time-series-data-in-folders"></a>Organizar dados de série temporal em pastas

Para cargas de trabalho do hive e do ADLA, a remoção de partições de dados de série temporal pode ajudar algumas consultas a ler apenas um subconjunto dos dados, o que melhora o desempenho.

Esses pipelines que ingerim dados de série temporal, geralmente colocam seus arquivos com uma nomenclatura estruturada para arquivos e pastas. Veja a seguir um exemplo comum que vemos para dados estruturados por data:

    \DataSet\YYYY\MM\DD\datafile_YYYY_MM_DD.tsv

Observe que as informações de data e hora são exibidas como pastas e no nome de arquivo.

Para data e hora, a seguir está um padrão comum

    \DataSet\YYYY\MM\DD\HH\mm\datafile_YYYY_MM_DD_HH_mm.tsv

Novamente, a escolha feita com a pasta e a organização de arquivos deve otimizar para os tamanhos de arquivo maiores e um número razoável de arquivos em cada pasta.

## <a name="optimize-io-intensive-jobs-on-hadoop-and-spark-workloads-on-hdinsight"></a>Otimizar trabalhos com uso intensivo de e/s em cargas de trabalho do Hadoop e do Spark no HDInsight

Os trabalhos se enquadram em uma das três categorias a seguir:

* **Uso intensivo da CPU.** Esses trabalhos têm tempos de computação longos com tempos mínimos de e/s. Os exemplos incluem aprendizado de máquina e trabalhos de processamento de idioma natural.
* **Uso intensivo de memória.** Esses trabalhos usam muita memória. Os exemplos incluem PageRank e trabalhos de análise em tempo real.
* **Uso intensivo de e/s.** Esses trabalhos passam a maior parte do tempo fazendo e/s. Um exemplo comum é um trabalho de cópia que faz apenas operações de leitura e gravação. Outros exemplos incluem trabalhos de preparação de dados que lêem vários dados, executam alguma transformação de dados e, em seguida, grava os dados de volta no repositório.

As diretrizes a seguir só são aplicáveis a trabalhos com uso intensivo de e/s.

### <a name="general-considerations-for-an-hdinsight-cluster"></a>Considerações gerais para um cluster HDInsight

* **Versões do HDInsight.** Para obter o melhor desempenho, use a versão mais recente do HDInsight.
* **Regiões.** Coloque a conta de Data Lake Storage Gen1 na mesma região que o cluster HDInsight.

HDInsight An cluster é composto por dois nós de cabeçalho e alguns nós de trabalho. Cada nó de trabalho fornece um número específico de núcleos e memória, que é determinado pelo tipo de VM. Ao executar um trabalho, YARN é o recurso negociador que aloca a memória disponível e os núcleos para criar contêineres. Cada contêiner executa as tarefas necessárias para concluir o trabalho. Os contêineres são executados em paralelo para processar tarefas rapidamente. Portanto, o desempenho é melhorado executando o máximo possível de contêineres paralelos.

Há três camadas em um cluster HDInsight que podem ser ajustadas para aumentar o número de contêineres e usar toda a taxa de transferência disponível.

* **Camada física**
* **Camada YARN**
* **Camada de carga de trabalho**

### <a name="physical-layer"></a>Camada física

**Execute o cluster com mais nós e/ou VMs de tamanho maior.** Um cluster maior permitirá que você execute mais contêineres do YARN, conforme mostrado na imagem abaixo.

![Desempenho de Data Lake Storage Gen1](./media/data-lake-store-performance-tuning-guidance/VM.png)

**Use VMs com mais largura de banda de rede.** A quantidade de largura de banda de rede pode ser um afunilamento se houver menos largura de banda de rede do que Data Lake Storage Gen1 taxa de transferência. Diferentes VMs terão tamanhos de largura de banda de rede diferentes. Escolha um tipo de VM que tenha a maior largura de banda de rede possível.

### <a name="yarn-layer"></a>Camada YARN

**Use contêineres YARN menores.** Reduza o tamanho de cada contêiner YARN para criar mais contêineres com a mesma quantidade de recursos.

![Desempenho de Data Lake Storage Gen1](./media/data-lake-store-performance-tuning-guidance/small-containers.png)

Dependendo de sua carga de trabalho, sempre haverá um tamanho mínimo de contêiner YARN necessário. Se você escolher um contêiner muito pequeno, seus trabalhos serão executados em problemas de memória insuficiente. Normalmente, os contêineres YARN não devem ser menores que 1 GB. É comum ver contêineres YARN de 3 GB. Para algumas cargas de trabalho, talvez sejam necessários contêineres YARN maiores.

**Aumente os núcleos por contêiner YARN.** Aumente o número de núcleos alocados para cada contêiner para aumentar o número de tarefas paralelas que são executadas em cada contêiner. Isso funciona para aplicativos como o Spark, que executa várias tarefas por contêiner. Para aplicativos como o hive que executam um único thread em cada contêiner, é melhor ter mais contêineres em vez de mais núcleos por contêiner.

### <a name="workload-layer"></a>Camada de carga de trabalho

**Use todos os contêineres disponíveis.** Defina o número de tarefas como igual ou maior que o número de contêineres disponíveis para que todos os recursos sejam usados.

![Desempenho de Data Lake Storage Gen1](./media/data-lake-store-performance-tuning-guidance/use-containers.png)

**As tarefas com falha são dispendiosas.** Se cada tarefa tiver uma grande quantidade de dados a serem processados, a falha de uma tarefa resultará em uma nova tentativa cara. Portanto, é melhor criar mais tarefas, cada uma delas processa uma pequena quantidade de dados.

Além das diretrizes gerais acima, cada aplicativo tem parâmetros diferentes disponíveis para ajuste para esse aplicativo específico. A tabela a seguir lista alguns dos parâmetros e links para começar a usar o ajuste de desempenho para cada aplicativo.

| Carga de trabalho               | Parâmetro para definir tarefas                                                         |
|--------------------|-------------------------------------------------------------------------------------|
| [Spark no HDInsight](data-lake-store-performance-tuning-spark.md)  | <ul><li>Número-executores</li><li>Executor-memória</li><li>Executor-núcleos</li></ul> |
| [Hive no HDInsight](data-lake-store-performance-tuning-hive.md)    | <ul><li>Hive. tez. Container. Size</li></ul>         |
| [MapReduce no HDInsight](data-lake-store-performance-tuning-mapreduce.md)            | <ul><li>MapReduce. map. Memory</li><li>MapReduce. Jobs. Maps</li><li>MapReduce. reduzir. memória</li><li>MapReduce. Job. reduz</li></ul> |
| [Storm no HDInsight](data-lake-store-performance-tuning-storm.md)| <ul><li>Número de processos de trabalho</li><li>Número de instâncias de executor Spout</li><li>Número de instâncias de executor de raio </li><li>Número de tarefas Spout</li><li>Número de tarefas de raio</li></ul>|

## <a name="see-also"></a>Ver também

* [Visão geral do Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Introdução ao Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
