---
title: Otimize Azure Data Lake Storage Gen2 para desempenho / Microsoft Docs
description: Diretrizes de afinação de desempenho do Lago Azure Data Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: f1a16228b72d7e0f45048669ade94a0c78d9ac52
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74327949"
---
# <a name="optimize-azure-data-lake-storage-gen2-for-performance"></a>Otimize Azure Data Lake Storage Gen2 para desempenho

O Azure Data Lake Storage Gen2 suporta alta produção para análise intensiva de I/O e movimento de dados.  No Data Lake Storage Gen2, a utilização de toda a entrada disponível – a quantidade de dados que podem ser lidos ou escritos por segundo – é importante para obter o melhor desempenho.  Isto é conseguido executando o maior número possível de leituras e escritos em paralelo.

![Desempenho do Data Lake Storage Gen2](./media/data-lake-storage-performance-tuning-guidance/throughput.png)

Data Lake Storage Gen2 pode escalar para fornecer a entrada necessária para todos os cenários de análise. Por padrão, uma conta Data Lake Storage Gen2 fornece automaticamente o suficiente para atender às necessidades de uma ampla categoria de casos de uso. Para os casos em que os clientes se esgotam no limite padrão, a conta Data Lake Storage Gen2 pode ser configurada para fornecer mais energia contactando [o Suporte Azure](https://azure.microsoft.com/support/faq/).

## <a name="data-ingestion"></a>Ingestão de dados

Ao ingerir dados de um sistema de origem para data lake storage Gen2, é importante considerar que o hardware de origem, hardware de rede de origem e conectividade de rede para Data Lake Storage Gen2 pode ser o estrangulamento.  

![Desempenho do Data Lake Storage Gen2](./media/data-lake-storage-performance-tuning-guidance/bottleneck.png)

É importante assegurar que o movimento de dados não seja afetado por estes fatores.

### <a name="source-hardware"></a>Hardware de origem

Quer esteja a utilizar máquinas no local ou VMs em Azure, deve selecionar cuidadosamente o hardware apropriado. Para hardware de disco de origem, prefira SSDs a HDDs e escolha hardware de disco com eixos mais rápidos. Para hardware de rede source, utilize os NICs mais rápidos possíveis.  No Azure, recomendamos os VMs Azure D14 que possuam o disco e hardware de rede adequadamente potentes.

### <a name="network-connectivity-to-data-lake-storage-gen2"></a>Conectividade de rede com Data Lake Storage Gen2

A conectividade da rede entre os seus dados de origem e data lake storage Gen2 pode, por vezes, ser o estrangulamento. Quando os seus dados de origem estiverem no Local, considere utilizar uma ligação dedicada com o [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) . Se os seus dados de origem estiverem em Azure, o desempenho será melhor quando os dados estiverem na mesma região do Azure que a conta Data Lake Storage Gen2.

### <a name="configure-data-ingestion-tools-for-maximum-parallelization"></a>Configure ferramentas de ingestão de dados para uma paralelização máxima

Depois de ter abordado os estrangulamentos de hardware de origem e conectividade da rede acima, está pronto para configurar as suas ferramentas de ingestão. A tabela que se segue resume as definições-chave para várias ferramentas de ingestão populares e fornece artigos de afinação de desempenho aprofundados para eles.  Para saber mais sobre qual ferramenta usar para o seu cenário, visite este [artigo.](data-lake-storage-data-scenarios.md)

| Ferramenta               | Definições     | Mais detalhes                                                                 |
|--------------------|------------------------------------------------------|------------------------------|
| DistCp            | -m (mapper)   | [Ligação](data-lake-storage-use-distcp.md#performance-considerations-while-using-distcp)                             |
| Azure Data Factory| paraleloCópias    | [Ligação](../../data-factory/copy-activity-performance.md)                          |
| Sqoop           | fs.azure.block.size, -m (mapper)    |   [Ligação](https://blogs.msdn.microsoft.com/bigdatasupport/2015/02/17/sqoop-job-performance-tuning-in-hdinsight-hadoop/)        |

## <a name="structure-your-data-set"></a>Estruturar o seu conjunto de dados

Quando os dados são armazenados no Data Lake Storage Gen2, o tamanho do ficheiro, o número de ficheiros e a estrutura das pastas têm um impacto no desempenho.  A secção seguinte descreve as melhores práticas nestas áreas.  

### <a name="file-size"></a>Tamanho dos ficheiros

Tipicamente, motores de análise como HDInsight e Azure Data Lake Analytics têm uma sobrecarga por ficheiro. Se armazenar os seus dados como muitos ficheiros pequenos, isso pode afetar negativamente o desempenho. Em geral, organize os seus dados em ficheiros de maior dimensão para um melhor desempenho (256MB a 100GB de tamanho). Alguns motores e aplicações podem ter problemas de processamento eficiente de ficheiros superiores a 100GB de tamanho.

Por vezes, os gasodutos de dados têm um controlo limitado sobre os dados brutos que têm muitos ficheiros pequenos. Recomenda-se que tenha um processo de "cozedura" que gere ficheiros maiores para utilizar para aplicações a jusante.

### <a name="organizing-time-series-data-in-folders"></a>Organização de dados da série de tempo em pastas

Para as cargas de trabalho da Hive, a poda de partilha de dados da série time pode ajudar algumas consultas a ler apenas um subconjunto dos dados que melhora o desempenho.    

Os oleodutos que ingebem dados da série de tempo, muitas vezes colocam os seus ficheiros com um nome muito estruturado para ficheiros e pastas. Abaixo é um exemplo muito comum que vemos para dados estruturados por data:

    \DataSet\YYYY\MM\DD\datafile_YYYY_MM_DD.tsv

Note que as informações da data aparecem tanto como pastas como no nome de ficheiro.

Para data e hora, o seguinte é um padrão comum

    \DataSet\YYYY\MM\DD\HH\mm\datafile_YYYY_MM_DD_HH_mm.tsv

Mais uma vez, a escolha que fizer com a pasta e a organização de ficheiros deve otimizar para os tamanhos de ficheiro maiores e um número razoável de ficheiros em cada pasta.

## <a name="optimizing-io-intensive-jobs-on-hadoop-and-spark-workloads-on-hdinsight"></a>Otimização de trabalhos intensivos de I/O em cargas de trabalho hadoop e spark em HDInsight

Os postos de trabalho enquadram-se numa das três categorias seguintes:

* **CPU intensivo.**  Estes trabalhos têm longos tempos de computação com tempos mínimos de i/O.  Exemplos incluem machine learning e trabalhos de processamento de linguagem natural.  
* **Memória intensiva.**  Estes trabalhos usam muita memória.  Exemplos incluem PageRank e trabalhos de análise em tempo real.  
* **Intensivo.**  Estes trabalhos passam a maior parte do tempo a fazer I/O.  Um exemplo comum é um trabalho de cópia que só lê e escreve operações.  Outros exemplos incluem trabalhos de preparação de dados que lêem muitos dados, realizam alguma transformação de dados e depois escrevem os dados de volta à loja.  

As seguintes orientações aplicam-se apenas aos postos de trabalho intensivos em I/O.

## <a name="general-considerations"></a>Considerações gerais

Você pode ter um trabalho que lê ou escreve até 100MB numa única operação, mas um tampão desse tamanho pode comprometer o desempenho.
Para otimizar o desempenho, tente manter o tamanho de uma operação de I/S entre 4MB e 16MB.

### <a name="general-considerations-for-an-hdinsight-cluster"></a>Considerações gerais para um cluster HDInsight

* **Versões HDInsight.** Para obter o melhor desempenho, utilize o mais recente lançamento do HDInsight.
* **Regiões, regiões.** Coloque a conta Data Lake Storage Gen2 na mesma região que o cluster HDInsight.  

Um cluster HDInsight é composto por dois nós de cabeça e alguns nós de trabalhador. Cada nó de trabalhador fornece um número específico de núcleos e memória, que é determinado pelo tipo VM.  Ao gerir um trabalho, a YARN é o negociador de recursos que aloca a memória e os núcleos disponíveis para criar contentores.  Cada recipiente executa as tarefas necessárias para completar o trabalho.  Os recipientes funcionam em paralelo às tarefas de processo rapidamente. Por conseguinte, o desempenho é melhorado com o funcionamento do maior número possível de contentores paralelos.

Existem três camadas dentro de um cluster HDInsight que pode ser sintonizado para aumentar o número de recipientes e usar toda a entrada disponível.  

* **Camada física**
* **Camada de ARN**
* **Camada de carga de trabalho**

### <a name="physical-layer"></a>Camada Física

**Executar cluster com mais nós e/ou VMs de tamanho maior.**  Um cluster maior permitir-lhe-á executar mais recipientes de ARN, como mostra a imagem abaixo.

![Desempenho do Data Lake Storage Gen2](./media/data-lake-storage-performance-tuning-guidance/VM.png)

**Utilize VMs com mais largura de banda de rede.**  A quantidade de largura de banda da rede pode ser um estrangulamento se houver menos largura de banda de rede do que a entrada de Data Lake Storage Gen2.  Diferentes VMs terão tamanhos de largura de banda de rede variados.  Escolha um tipo VM que tenha a maior largura de banda possível de rede.

### <a name="yarn-layer"></a>Camada de ARN

**Utilize recipientes yARN mais pequenos.**  Reduza o tamanho de cada recipiente yARN para criar mais contentores com a mesma quantidade de recursos.

![Desempenho do Data Lake Storage Gen2](./media/data-lake-storage-performance-tuning-guidance/small-containers.png)

Dependendo da sua carga de trabalho, haverá sempre um tamanho mínimo de recipiente de ARN que é necessário. Se escolherum recipiente muito pequeno, os seus empregos terão problemas fora de memória. Normalmente, os recipientes de ARN não devem ser inferiores a 1GB. É comum ver recipientes de ARN de 3GB. Para algumas cargas de trabalho, pode precisar de recipientes yARN maiores.  

**Aumente os núcleos por recipiente yARN.**  Aumentar o número de núcleos atribuídos a cada recipiente para aumentar o número de tarefas paralelas que funcionam em cada recipiente.  Isto funciona para aplicações como a Spark que executam múltiplas tarefas por recipiente.  Para aplicações como a Hive que executam um único fio em cada recipiente, é melhor ter mais contentores do que mais núcleos por recipiente.

### <a name="workload-layer"></a>Camada de carga de trabalho

**Utilize todos os recipientes disponíveis.**  Detete o número de tarefas iguais ou maiores do que o número de contentores disponíveis para que todos os recursos sejam utilizados.

![Desempenho do Data Lake Storage Gen2](./media/data-lake-storage-performance-tuning-guidance/use-containers.png)

**Tarefas falhadas são dispendiosas.** Se cada tarefa tiver uma grande quantidade de dados para processar, então a falha de uma tarefa resulta numa retentativa cara.  Portanto, é melhor criar mais tarefas, cada uma das quais processa uma pequena quantidade de dados.

Para além das orientações gerais acima referidas, cada aplicação dispõe de diferentes parâmetros disponíveis para sintonizar para essa aplicação específica. A tabela abaixo lista alguns dos parâmetros e links para começar com a sintonização de desempenho para cada aplicação.

| Carga de trabalho | Parâmetro para definir tarefas |
|----------|------------------------|
| [Spark no HDInsight](data-lake-storage-performance-tuning-spark.md) | <ul><li>Executores num</li><li>Memória do executor</li><li>Executor-núcleos</li></ul> |
| [Colmeia no HDInsight](data-lake-storage-performance-tuning-hive.md) | <ul><li>hive.tez.container.size.size</li></ul> |
| [MapReduce em HDInsight](data-lake-storage-performance-tuning-mapreduce.md) | <ul><li>Mapreduce.map.memory</li><li>Mapreduce.job.maps</li><li>Mapreduce.reduce.memory</li><li>Mapreduce.job.reduz</li></ul> |
| [Storm no HDInsight](data-lake-storage-performance-tuning-storm.md)| <ul><li>Número de processos de trabalhador</li><li>Número de casos de executor de bico</li><li>Número de casos de executor de parafusos </li><li>Número de tarefas de bico</li><li>Número de tarefas de parafusos</li></ul>|

## <a name="see-also"></a>Consulte também
* [Visão geral do Armazenamento de Lagos De Dados Azure Gen2](data-lake-storage-introduction.md)
