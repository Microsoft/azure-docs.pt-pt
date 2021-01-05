---
title: Otimizar a Azure Data Lake Storage Gen2 para desempenho Microsoft Docs
description: Entenda como otimizar a Azure Data Lake Storage Gen2 para desempenho. Ingerir dados, estruturar o seu conjunto de dados, e muito mais.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 11/18/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: f0f64d910d03e42008c5fe6fef28a5b9c0917abd
ms.sourcegitcommit: 1140ff2b0424633e6e10797f6654359947038b8d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/30/2020
ms.locfileid: "97814470"
---
# <a name="optimize-azure-data-lake-storage-gen2-for-performance"></a>Otimizar Azure Data Lake Storage Gen2 para desempenho

A Azure Data Lake Storage Gen2 suporta alta produção para análise intensiva de I/O e movimento de dados.  Na Data Lake Storage Gen2, usar todos os dados disponíveis – a quantidade de dados que podem ser lidos ou escritos por segundo – é importante para obter o melhor desempenho.  Isto é conseguido executando o maior número possível de leituras e escritos em paralelo.

![Desempenho da Gen2 de armazenamento de data lake](./media/data-lake-storage-performance-tuning-guidance/throughput.png)

Data Lake Storage Gen2 pode escalar para fornecer a produção necessária para todos os cenários de análise. Por padrão, uma conta Desconsitado de Armazenamento de Dados Gen2 fornece rendimento suficiente na sua configuração padrão para atender às necessidades de uma ampla categoria de casos de uso. Para os casos em que os clientes entram no limite por defeito, a conta Descontamento Deseconstrução Gen2 pode ser configurada para fornecer mais produção através do contacto com o [Suporte Azure](https://azure.microsoft.com/support/faq/).

## <a name="data-ingestion"></a>Ingestão de dados

Ao ingerir dados de um sistema de origem para data lake storage gen2, é importante considerar que o hardware de origem, hardware de rede de origem ou conectividade de rede para data lake storage Gen2 pode ser o estrangulamento.  

![Diagrama que mostra os fatores a ter em conta ao ingerir dados de um sistema de origem para a Data Lake Storage Gen2.](./media/data-lake-storage-performance-tuning-guidance/bottleneck.png)

É importante garantir que o movimento de dados não seja afetado por estes fatores.

### <a name="source-hardware"></a>Hardware de origem

Quer esteja a utilizar máquinas no local ou VMs em Azure, deve selecionar cuidadosamente o hardware apropriado. Para o hardware source disk, prefere SSDs a HDDs e escolha hardware de disco com eixos mais rápidos. Para o Hardware de Rede De Origem, utilize os NICs mais rápidos possíveis.  No Azure, recomendamos Azure D14 VMs que tenham o hardware de disco e rede adequadamente potente.

### <a name="network-connectivity-to-data-lake-storage-gen2"></a>Conectividade da rede com data lake storage gen2

A conectividade da rede entre os dados de origem e a Data Lake Storage Gen2 pode, por vezes, ser o estrangulamento. Quando os seus dados de origem estiverem no local, considere utilizar uma ligação dedicada com [a Azure ExpressRoute.](https://azure.microsoft.com/services/expressroute/) Se os seus dados de origem estiverem em Azure, o desempenho será melhor quando os dados estiverem na mesma região Azure que a conta Desarrume de Armazenamento de Dados Gen2.

### <a name="configure-data-ingestion-tools-for-maximum-parallelization"></a>Configure ferramentas de ingestão de dados para a máxima paralelização

Depois de ter abordado os estrangulamentos de hardware de origem e conectividade de rede acima, está pronto para configurar as suas ferramentas de ingestão. A tabela seguinte resume as definições-chave para várias ferramentas de ingestão populares e fornece artigos de afinação de desempenho aprofundados para eles.  Para saber mais sobre qual ferramenta usar para o seu cenário, visite este [artigo.](data-lake-storage-data-scenarios.md)

| Ferramenta               | Definições | Mais detalhes                                                                 |
|--------------------|------------------------------------------------------|------------------------------|
| DistCp            | -m (mapper)   | [Ligação](data-lake-storage-use-distcp.md#performance-considerations-while-using-distcp)                             |
| Azure Data Factory| paralelosCopias    | [Ligação](../../data-factory/copy-activity-performance.md)                          |
| Sqoop           | fs.azure.block.size, -m (mapper)    |   [Ligação](/archive/blogs/shanyu/performance-tuning-for-hdinsight-storm-and-microsoft-azure-eventhubs)        |

## <a name="structure-your-data-set"></a>Estruturar o seu conjunto de dados

Quando os dados são armazenados na Data Lake Storage Gen2, o tamanho do ficheiro, o número de ficheiros e a estrutura da pasta têm um impacto no desempenho.  A secção seguinte descreve as melhores práticas nestas áreas.  

### <a name="file-size"></a>Tamanho dos ficheiros

Tipicamente, os motores de análise como HDInsight e Azure Data Lake Analytics têm uma sobrecarga por ficheiro. Se armazenar os seus dados como muitos ficheiros pequenos, isso pode afetar negativamente o desempenho. Em geral, organize os seus dados em ficheiros de maior dimensão para um melhor desempenho (256MB a 100GB de tamanho). Alguns motores e aplicações podem ter problemas em processar ficheiros com mais de 100GB de tamanho.

Por vezes, os gasodutos de dados têm um controlo limitado sobre os dados brutos que têm muitos ficheiros pequenos. Em geral, recomendamos que o seu sistema tenha algum tipo de processo para agregar pequenos ficheiros em maiores para serem usados por aplicações a jusante.

### <a name="organizing-time-series-data-in-folders"></a>Organização de dados de séries de tempo em pastas

Para as cargas de trabalho da Colmeia, a poda de partição de dados da série de tempo pode ajudar algumas consultas a ler apenas um subconjunto dos dados que melhora o desempenho.    

Os oleodutos que ingerem dados de séries de tempo, muitas vezes colocam os seus ficheiros com um nome muito estruturado para ficheiros e pastas. Abaixo está um exemplo muito comum que vemos para os dados que são estruturados por data:

*\DataSet\YYYY\MM\DD\datafile_YYYY_MM_DD.tsv*

Note que as informações da data aparecem tanto como pastas como no nome de ficheiro.

Para data e hora, o seguinte é um padrão comum

*\DataSet\YYYY\MM\DD\HH\mm\datafile_YYYY_MM_DD_HH_mm.tsv*

Mais uma vez, a escolha que faz com a pasta e a organização de ficheiros deve otimizar para os tamanhos de ficheiros maiores e um número razoável de ficheiros em cada pasta.

## <a name="optimizing-io-intensive-jobs-on-hadoop-and-spark-workloads-on-hdinsight"></a>Otimização de trabalhos intensivos de I/O em Hadoop e Spark cargas de trabalho em HDInsight

Os postos de trabalho enquadram-se numa das três categorias seguintes:

* **CPU intensivo.**  Estes trabalhos têm longos tempos de cálculo com tempos mínimos de E/S.  Exemplos incluem aprendizagem automática e trabalhos de processamento de linguagem natural.  
* **Memória intensiva.**  Estes trabalhos usam muita memória.  Exemplos incluem PageRank e trabalhos de análise em tempo real.  
* **Intensivo de I/O.**  Estes trabalhos passam a maior parte do tempo a fazer E/S.  Um exemplo comum é um trabalho de cópia que só lê e escreve operações.  Outros exemplos incluem trabalhos de preparação de dados que lêem muitos dados, realizam alguma transformação de dados e, em seguida, escrevem os dados de volta para a loja.  

As seguintes orientações só se aplicam aos trabalhos intensivos de E/O.

## <a name="general-considerations"></a>Considerações gerais

Você pode ter um trabalho que lê ou escreve até 100MB em uma única operação, mas um tampão desse tamanho pode comprometer o desempenho.
Para otimizar o desempenho, tente manter o tamanho de uma operação de E/S entre 4MB e 16MB.

### <a name="general-considerations-for-an-hdinsight-cluster"></a>Considerações gerais para um cluster HDInsight

* **Versões HDInsight.** Para obter um melhor desempenho, utilize o mais recente lançamento do HDInsight.
* **Regiões.** Coloque a conta De armazenamento de dados Gen2 na mesma região que o cluster HDInsight.  

Um cluster HDInsight é composto por dois nós de cabeça e alguns nós operários. Cada nó de trabalhador fornece um número específico de núcleos e memória, que é determinado pelo tipo VM.  Ao executar um trabalho, o YARN é o negociador de recursos que aloca a memória e os núcleos disponíveis para criar recipientes.  Cada contentor executa as tarefas necessárias para completar o trabalho.  Os contentores funcionam em paralelo às tarefas de processo rapidamente. Por conseguinte, o desempenho é melhorado executando o maior número possível de recipientes paralelos.

Existem três camadas dentro de um cluster HDInsight que podem ser sintonizadas para aumentar o número de recipientes e usar todo o rendimento disponível.  

* **Camada física**
* **Camada de YARN**
* **Camada de carga de trabalho**

### <a name="physical-layer"></a>Camada Física

**Executar o cluster com mais nós e/ou VMs de tamanho maior.**  Um cluster maior permitir-lhe-á executar mais recipientes DE FIOS, como mostra a imagem abaixo.

![Diagrama que mostra como um cluster maior lhe permitirá executar mais recipientes DE FIOS.](./media/data-lake-storage-performance-tuning-guidance/VM.png)

**Utilize VMs com mais largura de banda de rede.**  A quantidade de largura de banda de rede pode ser um estrangulamento se houver menos largura de banda de rede do que a produção de Data Lake Storage Gen2.  Diferentes VMs terão diferentes tamanhos de largura de banda de rede.  Escolha um tipo VM que tenha a maior largura de banda de rede possível.

### <a name="yarn-layer"></a>Camada yarn

**Utilize recipientes de YARN mais pequenos.**  Reduza o tamanho de cada recipiente YARN para criar mais contentores com a mesma quantidade de recursos.

![Diagrama que mostra o resultado quando reduz o tamanho de cada recipiente DE YARN para criar mais recipientes.](./media/data-lake-storage-performance-tuning-guidance/small-containers.png)

Dependendo da sua carga de trabalho, haverá sempre um tamanho mínimo de recipiente YARN que é necessário. Se escolheres um recipiente muito pequeno, os teus empregos vão ficar sem memória. Normalmente, os recipientes DE FIOS não devem ser inferiores a 1GB. É comum ver recipientes de 3GB de YARN. Para algumas cargas de trabalho, poderá necessitar de recipientes de YARN maiores.  

**Aumente os núcleos por recipiente YARN.**  Aumente o número de núcleos atribuídos a cada contentor para aumentar o número de tarefas paralelas que funcionam em cada contentor.  Isto funciona para aplicações como a Spark que executam múltiplas tarefas por contentor.  Para aplicações como a Hive que executam um único fio em cada recipiente, é melhor ter mais contentores em vez de mais núcleos por recipiente.

### <a name="workload-layer"></a>Camada de carga de trabalho

**Utilize todos os recipientes disponíveis.**  Deite o número de tarefas iguais ou maiores do que o número de contentores disponíveis de modo a que todos os recursos sejam utilizados.

![Diagrama que mostra a utilização de todos os recipientes.](./media/data-lake-storage-performance-tuning-guidance/use-containers.png)

**As tarefas falhadas são dispendiosas.** Se cada tarefa tiver uma grande quantidade de dados para processar, então a falha de uma tarefa resulta numa reequimia dispendiosa.  Portanto, é melhor criar mais tarefas, cada uma das quais processa uma pequena quantidade de dados.

Além das diretrizes gerais acima, cada aplicação tem diferentes parâmetros disponíveis para sintonizar para essa aplicação específica. A tabela abaixo lista alguns dos parâmetros e links para começar com a sintonização de desempenho para cada aplicação.

| Carga de trabalho | Parâmetro para definir tarefas |
|----------|------------------------|
| [Spark no HDInsight](data-lake-storage-performance-tuning-spark.md) | <ul><li>Executores numéricos</li><li>Executor-memória</li><li>Executor-núcleos</li></ul> |
| [Colmeia em HDInsight](data-lake-storage-performance-tuning-hive.md) | <ul><li>hive.tez.container.tamanho</li></ul> |
| [MapReduce em HDInsight](data-lake-storage-performance-tuning-mapreduce.md) | <ul><li>Mapreduce.map.memory</li><li>Mapreduce.job.maps</li><li>Mapreduce.reduzir.memória</li><li>Mapreduce.job.reduz</li></ul> |
| [Storm no HDInsight](data-lake-storage-performance-tuning-storm.md)| <ul><li>Número de processos de trabalhadores</li><li>Número de casos de executor de bico</li><li>Número de instâncias executores de parafusos </li><li>Número de tarefas de bico</li><li>Número de tarefas de parafuso</li></ul>|

## <a name="see-also"></a>Veja também
* [Visão geral do Azure Data Lake Storage Gen2](data-lake-storage-introduction.md)
