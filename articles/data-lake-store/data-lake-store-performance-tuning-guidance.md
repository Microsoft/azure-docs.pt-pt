---
title: Azure Data Lake Storage Gen1 - afinação de desempenho
description: Descreve como sintonizar o Azure Data Lake Storage Gen1 para desempenho.
author: stewu
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 06/30/2017
ms.author: stewu
ms.openlocfilehash: 2521700e0f07691541ee6cbbf085a8be72f08129
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "73904629"
---
# <a name="tune-azure-data-lake-storage-gen1-for-performance"></a>Tune Azure Data Lake Storage Gen1 para desempenho

Data Lake Storage Gen1 suporta alta produção para análise intensiva de I/O e movimento de dados. Em Data Lake Storage Gen1, a utilização de toda a entrada disponível – a quantidade de dados que podem ser lidos ou escritos por segundo – é importante para obter o melhor desempenho. Isto é conseguido executando o maior número possível de leituras e escritos em paralelo.

![Desempenho da Gen1 de Armazenamento de Data Lake](./media/data-lake-store-performance-tuning-guidance/throughput.png)

Data Lake Storage Gen1 pode escalar para fornecer a entrada necessária para todos os cenários de análise. Por padrão, uma conta Gen1 de Armazenamento de Data Lake fornece automaticamente o suficiente para atender às necessidades de uma ampla categoria de casos de uso. Para os casos em que os clientes se esgotam no limite padrão, a conta Data Lake Storage Gen1 pode ser configurada para fornecer mais entrada contactando o suporte da Microsoft.

## <a name="data-ingestion"></a>Ingestão de dados

Ao ingerir dados de um sistema de origem para data lake storage Gen1, é importante considerar que o hardware de origem, hardware de rede de origem e conectividade de rede para Data Lake Storage Gen1 pode ser o estrangulamento.

![Desempenho da Gen1 de Armazenamento de Data Lake](./media/data-lake-store-performance-tuning-guidance/bottleneck.png)

É importante garantir que o movimento de dados não seja afetado por estes fatores.

### <a name="source-hardware"></a>Hardware de origem

Quer esteja a utilizar máquinas no local ou VMs em Azure, deve selecionar cuidadosamente o hardware apropriado. Para hardware de disco de origem, prefira SSDs a HDDs e escolha hardware de disco com eixos mais rápidos. Para hardware de rede source, utilize os NICs mais rápidos possíveis. No Azure, recomendamos os VMs Azure D14 que possuam o disco e hardware de rede adequadamente potentes.

### <a name="network-connectivity-to-data-lake-storage-gen1"></a>Conectividade de rede com Data Lake Storage Gen1

A conectividade da rede entre os seus dados de origem e data lake storage Gen1 pode, por vezes, ser o estrangulamento. Quando os seus dados de origem estiverem no Local, considere utilizar uma ligação dedicada com o [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) . Se os seus dados de origem estiverem em Azure, o desempenho será melhor quando os dados estiverem na mesma região do Azure que a conta Data Lake Storage Gen1.

### <a name="configure-data-ingestion-tools-for-maximum-parallelization"></a>Configure ferramentas de ingestão de dados para uma paralelização máxima

Depois de ter abordado os estrangulamentos de hardware de origem e conectividade da rede, está pronto para configurar as suas ferramentas de ingestão. A tabela que se segue resume as definições-chave para várias ferramentas de ingestão populares e fornece artigos de afinação de desempenho aprofundados para eles. Para saber mais sobre qual ferramenta usar para o seu cenário, visite este [artigo.](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-data-scenarios)

| Ferramenta          | Definições | Mais detalhes                                                                 |
|--------------------|------------------------------------------------------|------------------------------|
| PowerShell       | PerFileThreadCount, ConcurrentFileCount | [Ligação](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-powershell) |
| AdlCopy    | Unidades azure Data Lake Analytics | [Ligação](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob#performance-considerations-for-using-adlcopy)         |
| DistCp            | -m (mapper) | [Ligação](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-wasb-distcp#performance-considerations-while-using-distcp)                             |
| Azure Data Factory| paraleloCópias | [Ligação](../data-factory/copy-activity-performance.md)                          |
| Sqoop           | fs.azure.block.size, -m (mapper) | [Ligação](https://blogs.msdn.microsoft.com/bigdatasupport/2015/02/17/sqoop-job-performance-tuning-in-hdinsight-hadoop/)        |

## <a name="structure-your-data-set"></a>Estruturar o seu conjunto de dados

Quando os dados são armazenados no Data Lake Storage Gen1, o tamanho do ficheiro, o número de ficheiros e a estrutura da pasta afetam o desempenho. A secção seguinte descreve as melhores práticas nestas áreas.

### <a name="file-size"></a>Tamanho dos ficheiros

Tipicamente, motores de análise como HDInsight e Azure Data Lake Analytics têm uma sobrecarga por ficheiro. Se armazenar os seus dados como muitos ficheiros pequenos, isso pode afetar negativamente o desempenho.

Em geral, organize os seus dados em ficheiros de maior dimensão para um melhor desempenho. Como regra geral, organize conjuntos de dados em ficheiros de 256 MB ou maiores. Em alguns casos, como imagens e dados binários, não é possível processá-las paralelamente. Nestes casos, recomenda-se manter ficheiros individuais abaixo de 2 GB.

Por vezes, os gasodutos de dados têm um controlo limitado sobre os dados brutos que têm muitos ficheiros pequenos. Recomenda-se que tenha um processo de "cozedura" que gere ficheiros maiores para utilizar para aplicações a jusante.

### <a name="organize-time-series-data-in-folders"></a>Organizar dados da série de tempo em pastas

Para as cargas de trabalho da Hive e da ADLA, a poda de partilha de dados da série time pode ajudar algumas consultas a ler apenas um subconjunto dos dados, o que melhora o desempenho.

Os oleodutos que ingebem dados da série de tempo, muitas vezes colocam os seus ficheiros com um nomeamento estruturado para ficheiros e pastas. Segue-se um exemplo comum que vemos para os dados estruturados por data:

    \DataSet\YYYY\MM\DD\datafile_YYYY_MM_DD.tsv

Note que as informações da data aparecem tanto como pastas como no nome de ficheiro.

Para data e hora, o seguinte é um padrão comum

    \DataSet\YYYY\MM\DD\HH\mm\datafile_YYYY_MM_DD_HH_mm.tsv

Mais uma vez, a escolha que fizer com a pasta e a organização de ficheiros deve otimizar para os tamanhos de ficheiro maiores e um número razoável de ficheiros em cada pasta.

## <a name="optimize-io-intensive-jobs-on-hadoop-and-spark-workloads-on-hdinsight"></a>Otimizar trabalhos intensivos de I/O em cargas de trabalho hadoop e spark em HDInsight

Os postos de trabalho enquadram-se numa das três categorias seguintes:

* **CPU intensivo.** Estes trabalhos têm longos tempos de computação com tempos mínimos de i/O. Exemplos incluem machine learning e trabalhos de processamento de linguagem natural.
* **Memória intensiva.** Estes trabalhos usam muita memória. Exemplos incluem PageRank e trabalhos de análise em tempo real.
* **Intensivo.** Estes trabalhos passam a maior parte do tempo a fazer I/O. Um exemplo comum é um trabalho de cópia que só lê e escreve operações. Outros exemplos incluem trabalhos de preparação de dados que lêem inúmeros dados, realizam alguma transformação de dados e depois escrevem os dados de volta à loja.

As seguintes orientações aplicam-se apenas aos postos de trabalho intensivos em I/O.

### <a name="general-considerations-for-an-hdinsight-cluster"></a>Considerações gerais para um cluster HDInsight

* **Versões HDInsight.** Para obter o melhor desempenho, utilize o mais recente lançamento do HDInsight.
* **Regiões, regiões.** Coloque a conta Data Lake Storage Gen1 na mesma região que o cluster HDInsight.

Um cluster HDInsight é composto por dois nós de cabeça e alguns nós de trabalhador. Cada nó de trabalhador fornece um número específico de núcleos e memória, que é determinado pelo tipo VM. Ao gerir um trabalho, a YARN é o negociador de recursos que aloca a memória e os núcleos disponíveis para criar contentores. Cada recipiente executa as tarefas necessárias para completar o trabalho. Os recipientes funcionam em paralelo às tarefas de processo rapidamente. Por conseguinte, o desempenho é melhorado com o funcionamento do maior número possível de contentores paralelos.

Existem três camadas dentro de um cluster HDInsight que pode ser sintonizado para aumentar o número de recipientes e usar toda a entrada disponível.

* **Camada física**
* **Camada de ARN**
* **Camada de carga de trabalho**

### <a name="physical-layer"></a>Camada física

**Executar cluster com mais nós e/ou VMs de tamanho maior.** Um cluster maior permitir-lhe-á executar mais recipientes de ARN, como mostra a imagem abaixo.

![Desempenho da Gen1 de Armazenamento de Data Lake](./media/data-lake-store-performance-tuning-guidance/VM.png)

**Utilize VMs com mais largura de banda de rede.** A quantidade de largura de banda da rede pode ser um estrangulamento se houver menos largura de banda de rede do que a entrada de Data Lake Storage Gen1. Diferentes VMs terão tamanhos de largura de banda de rede variados. Escolha um tipo VM que tenha a maior largura de banda possível de rede.

### <a name="yarn-layer"></a>Camada de ARN

**Utilize recipientes yARN mais pequenos.** Reduza o tamanho de cada recipiente yARN para criar mais contentores com a mesma quantidade de recursos.

![Desempenho da Gen1 de Armazenamento de Data Lake](./media/data-lake-store-performance-tuning-guidance/small-containers.png)

Dependendo da sua carga de trabalho, haverá sempre um tamanho mínimo de recipiente de ARN que é necessário. Se escolherum recipiente muito pequeno, os seus empregos terão problemas fora de memória. Normalmente, os recipientes DE ARN não devem ser inferiores a 1 GB. É comum ver contentores de ARN de 3 GB. Para algumas cargas de trabalho, pode precisar de recipientes yARN maiores.

**Aumente os núcleos por recipiente yARN.** Aumentar o número de núcleos atribuídos a cada recipiente para aumentar o número de tarefas paralelas que funcionam em cada recipiente. Isto funciona para aplicações como a Spark, que executam várias tarefas por recipiente. Para aplicações como a Hive que executam um único fio em cada recipiente, é melhor ter mais contentores do que mais núcleos por recipiente.

### <a name="workload-layer"></a>Camada de carga de trabalho

**Utilize todos os recipientes disponíveis.** Definir o número de tarefas iguais ou maiores do que o número de contentores disponíveis para que todos os recursos sejam utilizados.

![Desempenho da Gen1 de Armazenamento de Data Lake](./media/data-lake-store-performance-tuning-guidance/use-containers.png)

**Tarefas falhadas são dispendiosas.** Se cada tarefa tiver uma grande quantidade de dados para processar, então a falha de uma tarefa resulta numa retentativa cara. Portanto, é melhor criar mais tarefas, cada uma das quais processa uma pequena quantidade de dados.

Para além das orientações gerais acima referidas, cada aplicação dispõe de diferentes parâmetros disponíveis para sintonizar para essa aplicação específica. A tabela abaixo lista alguns dos parâmetros e links para começar com a sintonização de desempenho para cada aplicação.

| Carga de trabalho               | Parâmetro para definir tarefas                                                         |
|--------------------|-------------------------------------------------------------------------------------|
| [Spark no HDInsight](data-lake-store-performance-tuning-spark.md)  | <ul><li>Executores num</li><li>Memória do executor</li><li>Executor-núcleos</li></ul> |
| [Colmeia no HDInsight](data-lake-store-performance-tuning-hive.md)    | <ul><li>hive.tez.container.size.size</li></ul>         |
| [MapReduce em HDInsight](data-lake-store-performance-tuning-mapreduce.md)            | <ul><li>Mapreduce.map.memory</li><li>Mapreduce.job.maps</li><li>Mapreduce.reduce.memory</li><li>Mapreduce.job.reduz</li></ul> |
| [Storm no HDInsight](data-lake-store-performance-tuning-storm.md)| <ul><li>Número de processos de trabalhador</li><li>Número de casos de executor de bico</li><li>Número de casos de executor de parafusos </li><li>Número de tarefas de bico</li><li>Número de tarefas de parafusos</li></ul>|

## <a name="see-also"></a>Consulte também

* [Visão geral do Armazenamento de Lagos De Dados Azure Gen1](data-lake-store-overview.md)
* [Introdução ao Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
