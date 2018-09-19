---
title: Diretrizes de ajuste de desempenho de geração 1 de Lake armazenamento de dados do Azure | Documentos da Microsoft
description: Diretrizes de ajuste de desempenho de geração 1 de Lake armazenamento de dados do Azure
services: data-lake-store
documentationcenter: ''
author: stewu
manager: amitkul
editor: cgronlun
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: stewu
ms.openlocfilehash: ba46ba6429640cf29d9abc75055563fb1578d2e2
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2018
ms.locfileid: "46129595"
---
# <a name="tuning-azure-data-lake-storage-gen1-for-performance"></a>Otimização de geração 1 de armazenamento do Azure Data Lake para o desempenho

Geração de armazenamento 1 do Azure Data Lake suporta o alto débito para movimento de dados e análises intensivo no e/s.  No Data Lake Storage Gen1, usar todos os débito disponíveis – a quantidade de dados que podem ser lidos ou escritos por segundo – é importante para obter o melhor desempenho.  Isso é conseguido ao executar o número de leituras e escritas em paralelo possível.

![Desempenho do Data Lake Storage Gen1](./media/data-lake-store-performance-tuning-guidance/throughput.png)

Geração 1 de armazenamento do Data Lake pode ser dimensionado para fornecer o débito necessário para todos os cenário de análise. Por predefinição, uma conta de geração 1 de armazenamento do Data Lake fornece automaticamente débito suficiente para satisfazer as necessidades de uma categoria abrangente de casos de utilização. Para os casos em que os clientes se deparar com o limite predefinido, a conta de geração 1 de armazenamento do Data Lake pode ser configurada para fornecer mais débito contactando o suporte da Microsoft.

## <a name="data-ingestion"></a>Ingestão de dados

Quando a ingestão de dados de um sistema de origem para a geração 1 de armazenamento do Data Lake, é importante considerar que o hardware de código, o hardware de rede de origem e a conectividade de rede para a geração 1 de armazenamento do Data Lake podem ser o afunilamento.  

![Desempenho do Data Lake Storage Gen1](./media/data-lake-store-performance-tuning-guidance/bottleneck.png)

É importante certificar-se de que o movimento de dados não é afetado por esses fatores.

### <a name="source-hardware"></a>Hardware de origem

Se estiver a utilizar máquinas no local ou VMs no Azure, deve selecionar cuidadosamente o hardware adequado. Para Hardware do disco de origem, prefira SSDs em HDDs e escolha o hardware de disco com eixos mais rápidos. Para o Hardware de rede de origem, utilize o mais rápido possível de NICs.  No Azure, recomendamos que as VMs do Azure D14 que tem o disco adequadamente poderoso e o hardware de rede.

### <a name="network-connectivity-to-data-lake-storage-gen1"></a>Conectividade de rede para a geração 1 do Data Lake Storage

A conectividade de rede entre a origem de dados e a geração 1 de armazenamento do Data Lake, às vezes, pode ser o afunilamento. Quando os dados de origem estão no local, considere a utilização de uma ligação dedicada com [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) . Se os dados de origem estiverem-se no Azure, o desempenho será melhor quando os dados estão na mesma região do Azure como a conta de geração 1 de armazenamento do Data Lake.

### <a name="configure-data-ingestion-tools-for-maximum-parallelization"></a>Configurar as ferramentas de ingestão de dados para paralelização máxima

Depois de ter resolvido o hardware de origem e acima de afunilamentos de conectividade de rede, está pronto para configurar as suas ferramentas de ingestão. A tabela seguinte resume as definições da chave para várias ferramentas populares de ingestão e fornece desempenho aprofundadas ajuste artigos para os mesmos.  Para saber mais sobre qual ferramenta para utilizar para o seu cenário, visite [artigo](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-data-scenarios).

| Ferramenta               | Definições     | Mais Detalhes                                                                 |
|--------------------|------------------------------------------------------|------------------------------|
| PowerShell       | PerFileThreadCount, ConcurrentFileCount |  [Link](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-powershell#performance-guidance-while-using-powershell) |
| AdlCopy    | Unidades do Azure Data Lake Analytics  |   [Link](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob#performance-considerations-for-using-adlcopy)         |
| DistCp            | -m (mapeador de pontos)   | [Link](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-wasb-distcp#performance-considerations-while-using-distcp)                             |
| Azure Data Factory| parallelCopies    | [Link](../data-factory/copy-activity-performance.md)                          |
| Sqoop           | FS.Azure.Block.size, -m (mapeador de pontos)    |   [Link](https://blogs.msdn.microsoft.com/bigdatasupport/2015/02/17/sqoop-job-performance-tuning-in-hdinsight-hadoop/)        |

## <a name="structure-your-data-set"></a>Estruturar o seu conjunto de dados

Quando dados são armazenados no Data Lake Storage geração 1, o tamanho do ficheiro, o número de ficheiros e estrutura de pastas ter um impacto no desempenho.  A secção seguinte descreve as melhores práticas nas seguintes áreas.  

### <a name="file-size"></a>Tamanho dos ficheiros

Normalmente, os motores de análise, como o HDInsight e Azure Data Lake Analytics tem uma sobrecarga de por arquivo.  Se armazenar seus dados tantos arquivos pequenos, isso pode afetar negativamente o desempenho.  

Em geral, organize os dados em arquivos maiores de tamanho para um melhor desempenho.  Como regra prática, organize os conjuntos de dados em arquivos de 256MB ou superior. Em alguns casos, como imagens e os dados binários, não é possível para processá-las em paralelo.  Nestes casos, recomenda-se de manter menos de 2GB de arquivos individuais.

Às vezes, pipelines de dados tem controlo limitado sobre os dados não processados com vários arquivos pequenos.  É recomendado ter um processo "inspirações", que gera arquivos maiores a utilizar para aplicações a jusante.

### <a name="organizing-time-series-data-in-folders"></a>Organizar os dados de séries de tempo em pastas

Para cargas de trabalho do Hive e ADLA, a eliminação de partições dos dados de séries temporais pode ajudar a algumas consultas de apenas um subconjunto dos dados que melhora o desempenho de leitura.    

Os pipelines de ingestão de dados de séries de tempo, muitas vezes, colocar os seus arquivos com um muito estruturado de nomenclatura para ficheiros e pastas. Segue-se um exemplo muito comum que vemos para dados que estão estruturados por data:

    \DataSet\YYYY\MM\DD\datafile_YYYY_MM_DD.tsv

Tenha em atenção que as informações de datetime são apresentadas como pastas e no nome de ficheiro.

Data e hora, este é um padrão comum

    \DataSet\YYYY\MM\DD\HH\mm\datafile_YYYY_MM_DD_HH_mm.tsv

Novamente, a escolha que faz com que a pasta e deve otimizar a organização de arquivos para os tamanhos de ficheiro maior e um número razoável de ficheiros em cada pasta.

## <a name="optimizing-io-intensive-jobs-on-hadoop-and-spark-workloads-on-hdinsight"></a>Otimizar tarefas intensivas de e/s nas cargas de trabalho Hadoop e do Spark no HDInsight

Tarefas enquadram-se uma das três categorias a seguir:

* **Com uso intensivo de CPU.**  Estas tarefas têm horas de computação longa com tempos de e/s mínimo.  Exemplos machine learning e tarefas de processamento de linguagem natural.  
* **Elevado consumo de memória.**  Estas tarefas utilizam muita memória.  Os exemplos incluem PageRank e tarefas de análise em tempo real.  
* **Intensivas de e/s.**  Estas tarefas passam a maior parte do tempo fazendo a e/s.  Um exemplo comum é uma tarefa de cópia que só de leitura e escrita de operações.  Outros exemplos incluem tarefas de preparação de dados que uma grande quantidade de dados de leitura, faz algumas transformação de dados e, em seguida, escreve os dados de volta para o arquivo.  

As seguintes orientações só é aplicável a tarefas de e/s intensivas.

### <a name="general-considerations-for-an-hdinsight-cluster"></a>Considerações gerais para um cluster do HDInsight

* **Versões do HDInsight.** Para obter melhor desempenho, utilize a versão mais recente do HDInsight.
* **Regiões.** Coloque a conta de geração 1 do Data Lake Storage na mesma região que o cluster do HDInsight.  

Um cluster do HDInsight é composto por dois nós principais e alguns nós de trabalho. Cada nó de trabalho fornece um número específico de núcleos e memória, o que é determinada pelo tipo de VM.  Ao executar uma tarefa, o YARN é o negociador de recursos que aloca a memória disponível e núcleos para criar contentores.  Cada contentor executa as tarefas necessárias para concluir a tarefa.  Executam contentores em paralelo para processar tarefas rapidamente. Por conseguinte, o desempenho é melhorado ao executar contentores paralelas tantas quanto possível.

Existem três camadas dentro de um cluster do HDInsight que podem ser otimizadas para aumentar o número de contentores e utilize todos os débito disponíveis.  

* **Camada física**
* **Camada YARN**
* **Camada de carga de trabalho**

### <a name="physical-layer"></a>Camada física

**Execute o cluster com mais nós de e/ou VMs de tamanho maior.**  Um cluster maior permitirá que execute mais contentores do YARN, conforme mostrado na imagem abaixo.

![Desempenho do Data Lake Storage Gen1](./media/data-lake-store-performance-tuning-guidance/VM.png)

**Utilize VMs com mais largura de banda de rede.**  A quantidade de largura de banda de rede pode ser um estrangulamento, se houver menos largura de banda de rede que o débito de geração 1 de armazenamento do Data Lake.  Diferentes VMs terão variados tamanhos de largura de banda de rede.  Escolha um tipo de VM que tem a maior largura de banda de rede possível.

### <a name="yarn-layer"></a>Camada YARN

**Utilize contentores do YARN menores.**  Reduza o tamanho de cada contentor do YARN para criar mais contentores com a mesma quantidade de recursos.

![Desempenho do Data Lake Storage Gen1](./media/data-lake-store-performance-tuning-guidance/small-containers.png)

Dependendo da sua carga de trabalho, sempre haverá um tamanho mínimo de contentores YARN que é necessária. Se escolher demasiado pequeno um contentor, as tarefas serão executadas em problemas de insuficiência de memória. Normalmente, contentores YARN devem ser não inferior a 1GB. É comum ver contentores YARN de 3GB. Para algumas cargas de trabalho, poderá ter maior contentores YARN.  

**Aumente núcleos por contentor YARN.**  Aumente o número de núcleos alocados para cada contentor para aumentar o número de tarefas paralelas que são executados em cada contentor.  Isto funciona para aplicações, como o Spark que executem várias tarefas por contentor.  Para aplicações como o Hive que execute um único thread em cada contentor, é melhor ter mais contentores em vez de mais núcleos por contentor.

### <a name="workload-layer"></a>Camada de carga de trabalho

**Utilize todos os contentores disponíveis.**  Defina o número de tarefas para ser igual ou maior do que o número de contentores disponíveis para que todos os recursos são utilizados.

![Desempenho do Data Lake Storage Gen1](./media/data-lake-store-performance-tuning-guidance/use-containers.png)

**Tarefas falhadas são dispendiosas.** Se cada tarefa tem uma grande quantidade de dados para processar, em seguida, falha de uma tarefa resulta numa repetição cara.  Portanto, é melhor criar mais tarefas, sendo que cada um processa uma pequena quantidade de dados.

Além das Diretrizes gerais acima, cada aplicativo tem parâmetros diferentes disponíveis para otimizar para aquele aplicativo específico. A tabela abaixo lista alguns dos parâmetros e ligações para começar a utilizar com o ajuste de desempenho para cada aplicativo.

| Carga de trabalho               | Parâmetro para definir as tarefas                                                         |
|--------------------|-------------------------------------------------------------------------------------|
| [O Spark no Hdinsight](data-lake-store-performance-tuning-spark.md)       | <ul><li>Núm. executores</li><li>Memória de executor</li><li>Núcleos de executor</li></ul> |
| [Hive no HDInsight](data-lake-store-performance-tuning-hive.md)    | <ul><li>Hive.tez.Container.size</li></ul>         |
| [MapReduce no HDInsight](data-lake-store-performance-tuning-mapreduce.md)            | <ul><li>Mapreduce.Map.Memory</li><li>Mapreduce.job.Maps</li><li>Mapreduce.reduce.Memory</li><li>Mapreduce.job.reduces</li></ul> |
| [Storm no HDInsight](data-lake-store-performance-tuning-storm.md)| <ul><li>Número de processos de trabalho</li><li>Número de instâncias de executor de spout</li><li>Número de instâncias de executor de bolt </li><li>Número de tarefas de spout</li><li>Número de tarefas de bolt</li></ul>|

## <a name="see-also"></a>Consulte também
* [Descrição geral do Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Introdução ao Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
