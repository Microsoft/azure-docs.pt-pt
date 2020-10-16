---
title: Azure Data Lake Storage Gen1 - afinação de desempenho
description: Saiba como usar toda a produção disponível no Azure Data Lake Storage Gen1 é importante para obter o melhor desempenho executando o maior número possível de leituras e escritos em paralelo.
author: stewu
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 06/30/2017
ms.author: stewu
ms.openlocfilehash: 4c9cb1d0496fe05c208cfd446a51cbf4ef8e8d4e
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92108614"
---
# <a name="tune-azure-data-lake-storage-gen1-for-performance"></a>Tune Azure Data Lake Storage Gen1 para desempenho

Data Lake Storage Gen1 suporta alta produção para análise intensiva de I/O e movimento de dados. Na Data Lake Storage Gen1, usar todos os dados disponíveis – a quantidade de dados que podem ser lidos ou escritos por segundo – é importante para obter o melhor desempenho. Isto é conseguido executando o maior número possível de leituras e escritos em paralelo.

![Desempenho da Gen1 de armazenamento de dados](./media/data-lake-store-performance-tuning-guidance/throughput.png)

Data Lake Storage Gen1 pode escalar para fornecer a produção necessária para todos os cenários de análise. Por padrão, uma conta De armazenamento de dados Gen1 fornece automaticamente o rendimento suficiente para satisfazer as necessidades de uma ampla categoria de casos de uso. Para os casos em que os clientes entram no limite padrão, a conta Desconsuito de Armazenamento gen1 pode ser configurada para fornecer mais rendimento contactando o suporte da Microsoft.

## <a name="data-ingestion"></a>Ingestão de dados

Ao ingerir dados de um sistema de origem para a Data Lake Storage Gen1, é importante considerar que o hardware de origem, hardware de rede de origem e conectividade de rede para data lake storage gen1 pode ser o estrangulamento.

![Desempenho da Gen1 de armazenamento de dados](./media/data-lake-store-performance-tuning-guidance/bottleneck.png)

É importante garantir que o movimento de dados não seja afetado por estes fatores.

### <a name="source-hardware"></a>Hardware de origem

Quer esteja a utilizar máquinas no local ou VMs em Azure, deve selecionar cuidadosamente o hardware apropriado. Para o hardware source disk, prefere SSDs a HDDs e escolha hardware de disco com eixos mais rápidos. Para o Hardware de Rede De Origem, utilize os NICs mais rápidos possíveis. No Azure, recomendamos Azure D14 VMs que tenham o hardware de disco e rede adequadamente potente.

### <a name="network-connectivity-to-data-lake-storage-gen1"></a>Conectividade da rede com data lake storage gen1

A conectividade da rede entre os dados de origem e a Rede de Armazenamento de Data Lake pode, por vezes, ser o estrangulamento. Quando os seus dados de origem estiverem no local, considere utilizar uma ligação dedicada com [a Azure ExpressRoute.](https://azure.microsoft.com/services/expressroute/) Se os seus dados de origem estiverem em Azure, o desempenho será melhor quando os dados estiverem na mesma região Azure que a conta Desarrume de Armazenamento de Dados Gen1.

### <a name="configure-data-ingestion-tools-for-maximum-parallelization"></a>Configure ferramentas de ingestão de dados para a máxima paralelização

Depois de ter abordado os estrangulamentos de hardware de origem e conectividade da rede, está pronto para configurar as suas ferramentas de ingestão. A tabela seguinte resume as definições-chave para várias ferramentas de ingestão populares e fornece artigos de afinação de desempenho aprofundados para eles. Para saber mais sobre qual ferramenta usar para o seu cenário, visite este [artigo.](./data-lake-store-data-scenarios.md)

| Ferramenta          | Definições | Mais detalhes                                                                 |
|--------------------|------------------------------------------------------|------------------------------|
| PowerShell       | PerFileThreadCount, ConcurrentFileCount | [Ligação](./data-lake-store-get-started-powershell.md) |
| AdlCopy    | Unidades Azure Data Lake Analytics | [Ligação](./data-lake-store-copy-data-azure-storage-blob.md#performance-considerations-for-using-adlcopy)         |
| DistCp            | -m (mapper) | [Ligação](./data-lake-store-copy-data-wasb-distcp.md#performance-considerations-while-using-distcp)                             |
| Azure Data Factory| paralelosCopias | [Ligação](../data-factory/copy-activity-performance.md)                          |
| Sqoop           | fs.azure.block.size, -m (mapper) | [Ligação](/archive/blogs/shanyu/performance-tuning-for-hdinsight-storm-and-microsoft-azure-eventhubs)        |

## <a name="structure-your-data-set"></a>Estruturar o seu conjunto de dados

Quando os dados são armazenados na Data Lake Storage Gen1, o tamanho do ficheiro, o número de ficheiros e a estrutura da pasta afetam o desempenho. A secção seguinte descreve as melhores práticas nestas áreas.

### <a name="file-size"></a>Tamanho dos ficheiros

Tipicamente, os motores de análise como HDInsight e Azure Data Lake Analytics têm uma sobrecarga por ficheiro. Se armazenar os seus dados como muitos ficheiros pequenos, isso pode afetar negativamente o desempenho.

Em geral, organize os seus dados em ficheiros de maior dimensão para um melhor desempenho. Como regra geral, organize conjuntos de dados em ficheiros de 256 MB ou maiores. Em alguns casos, como imagens e dados binários, não é possível processá-los em paralelo. Nestes casos, recomenda-se manter os ficheiros individuais abaixo de 2 GB.

Por vezes, os gasodutos de dados têm controlo limitado sobre os dados brutos que têm muitos ficheiros pequenos. Recomenda-se ter um processo de "cozinhar" que gere ficheiros maiores para utilizar para aplicações a jusante.

### <a name="organize-time-series-data-in-folders"></a>Organizar dados de séries temporítem em pastas

Para as cargas de trabalho da Hive e da ADLA, a poda de partição de dados da série de tempo pode ajudar algumas consultas a ler apenas um subconjunto dos dados, o que melhora o desempenho.

Os oleodutos que ingerem dados de séries de tempo, muitas vezes colocam os seus ficheiros com um nome estruturado para ficheiros e pastas. O exemplo comum que vemos para os dados que são estruturados por data: *\DataSet\YYYY\MM\DD\DD\datafile_YYYY_MM_DD.tsv*.

Note que as informações da data aparecem tanto como pastas como no nome de ficheiro.

Para a data e hora, o seguinte é um padrão comum: *\DataSet\YYYY\MM\DD\HH\mm\datafile_YYYY_MM_DD_HH_mm.tsv*.

Mais uma vez, a escolha que faz com a pasta e a organização de ficheiros deve otimizar para os tamanhos de ficheiros maiores e um número razoável de ficheiros em cada pasta.

## <a name="optimize-io-intensive-jobs-on-hadoop-and-spark-workloads-on-hdinsight"></a>Otimizar os trabalhos intensivos de I/O em Hadoop e Spark workloads em HDInsight

Os postos de trabalho enquadram-se numa das três categorias seguintes:

* **CPU intensivo.** Estes trabalhos têm longos tempos de cálculo com tempos mínimos de E/S. Exemplos incluem aprendizagem automática e trabalhos de processamento de linguagem natural.
* **Memória intensiva.** Estes trabalhos usam muita memória. Exemplos incluem PageRank e trabalhos de análise em tempo real.
* **Intensivo de I/O.** Estes trabalhos passam a maior parte do tempo a fazer E/S. Um exemplo comum é um trabalho de cópia que só lê e escreve operações. Outros exemplos incluem trabalhos de preparação de dados que lêem inúmeros dados, realizam alguma transformação de dados e, em seguida, escrevem os dados de volta para a loja.

As seguintes orientações só se aplicam aos trabalhos intensivos de E/O.

### <a name="general-considerations-for-an-hdinsight-cluster"></a>Considerações gerais para um cluster HDInsight

* **Versões HDInsight.** Para obter um melhor desempenho, utilize o mais recente lançamento do HDInsight.
* **Regiões.** Coloque a conta De armazenamento de dados Gen1 na mesma região que o cluster HDInsight.

Um cluster HDInsight é composto por dois nós de cabeça e alguns nós operários. Cada nó de trabalhador fornece um número específico de núcleos e memória, que é determinado pelo tipo VM. Ao executar um trabalho, o YARN é o negociador de recursos que aloca a memória e os núcleos disponíveis para criar recipientes. Cada contentor executa as tarefas necessárias para completar o trabalho. Os contentores funcionam em paralelo às tarefas de processo rapidamente. Por conseguinte, o desempenho é melhorado executando o maior número possível de recipientes paralelos.

Existem três camadas dentro de um cluster HDInsight que podem ser sintonizadas para aumentar o número de recipientes e usar todo o rendimento disponível.

* **Camada física**
* **Camada de YARN**
* **Camada de carga de trabalho**

### <a name="physical-layer"></a>Camada física

**Executar o cluster com mais nós e/ou VMs de tamanho maior.** Um cluster maior permitir-lhe-á executar mais recipientes DE FIOS, como mostra a imagem abaixo.

![Desempenho da Gen1 de armazenamento de dados](./media/data-lake-store-performance-tuning-guidance/VM.png)

**Utilize VMs com mais largura de banda de rede.** A quantidade de largura de banda de rede pode ser um estrangulamento se houver menos largura de banda de rede do que a produção de Data Lake Storage Gen1. Diferentes VMs terão diferentes tamanhos de largura de banda de rede. Escolha um tipo VM que tenha a maior largura de banda de rede possível.

### <a name="yarn-layer"></a>Camada de YARN

**Utilize recipientes de YARN mais pequenos.** Reduza o tamanho de cada recipiente YARN para criar mais contentores com a mesma quantidade de recursos.

![Desempenho da Gen1 de armazenamento de dados](./media/data-lake-store-performance-tuning-guidance/small-containers.png)

Dependendo da sua carga de trabalho, haverá sempre um tamanho mínimo de recipiente YARN que é necessário. Se escolheres um recipiente muito pequeno, os teus empregos vão ficar sem memória. Normalmente, os recipientes de YARN não devem ser inferiores a 1 GB. É comum ver 3 GB de contentores de YARN. Para algumas cargas de trabalho, poderá necessitar de recipientes de YARN maiores.

**Aumente os núcleos por recipiente YARN.** Aumente o número de núcleos atribuídos a cada contentor para aumentar o número de tarefas paralelas que funcionam em cada contentor. Isto funciona para aplicações como a Spark, que executam múltiplas tarefas por contentor. Para aplicações como a Hive que executam um único fio em cada recipiente, é melhor ter mais recipientes em vez de mais núcleos por recipiente.

### <a name="workload-layer"></a>Camada de carga de trabalho

**Utilize todos os recipientes disponíveis.** Deite o número de tarefas iguais ou maiores do que o número de contentores disponíveis de modo a que todos os recursos sejam utilizados.

![Desempenho da Gen1 de armazenamento de dados](./media/data-lake-store-performance-tuning-guidance/use-containers.png)

**As tarefas falhadas são dispendiosas.** Se cada tarefa tiver uma grande quantidade de dados para processar, então a falha de uma tarefa resulta numa reequimia dispendiosa. Portanto, é melhor criar mais tarefas, cada uma das quais processa uma pequena quantidade de dados.

Além das diretrizes gerais acima, cada aplicação tem diferentes parâmetros disponíveis para sintonizar para essa aplicação específica. A tabela abaixo lista alguns dos parâmetros e links para começar com a sintonização de desempenho para cada aplicação.

| Carga de trabalho               | Parâmetro para definir tarefas                                                         |
|--------------------|-------------------------------------------------------------------------------------|
| [Spark no HDInsight](data-lake-store-performance-tuning-spark.md)  | <ul><li>Executores numéricos</li><li>Executor-memória</li><li>Executor-núcleos</li></ul> |
| [Colmeia em HDInsight](data-lake-store-performance-tuning-hive.md)    | <ul><li>hive.tez.container.tamanho</li></ul>         |
| [MapReduce em HDInsight](data-lake-store-performance-tuning-mapreduce.md)            | <ul><li>Mapreduce.map.memory</li><li>Mapreduce.job.maps</li><li>Mapreduce.reduzir.memória</li><li>Mapreduce.job.reduz</li></ul> |
| [Storm no HDInsight](data-lake-store-performance-tuning-storm.md)| <ul><li>Número de processos de trabalhadores</li><li>Número de casos de executor de bico</li><li>Número de instâncias executores de parafusos </li><li>Número de tarefas de bico</li><li>Número de tarefas de parafuso</li></ul>|

## <a name="see-also"></a>Ver também

* [Visão geral do Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Introdução ao Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md)