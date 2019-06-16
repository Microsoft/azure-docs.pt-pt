---
title: Opções de contexto para os serviços de ML no HDInsight - Azure de computação
description: Saiba mais sobre as opções de contexto diferente de computação disponíveis para os utilizadores com os serviços de ML a no HDInsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/27/2018
ms.openlocfilehash: 9dac7aa19e428c964bd10c3ef62df949393e8d1f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64681775"
---
# <a name="compute-context-options-for-ml-services-on-hdinsight"></a>Opções de contexto para os serviços de ML no HDInsight de computação

Serviços de ML no Azure HDInsight controla como as chamadas são executadas ao definir o contexto de cálculo. Este artigo descreve as opções que estão disponíveis para especificar se e como a execução é paralelizada entre os núcleos do nó de extremidade ou cluster do HDInsight.

Nó de extremidade de um cluster fornece um local conveniente para ligar ao cluster e para executar os scripts R. Com um nó de extremidade, tem a opção de executar as funções em paralelo distribuídas de RevoScaleR entre os núcleos do servidor de nó edge. Também pode executá-los em todos os nós do cluster através da utilização Hadoop mapeamento redução do RevoScaleR ou contextos de computação do Apache Spark.

## <a name="ml-services-on-azure-hdinsight"></a>Serviços de ML no Azure HDInsight
[Serviços de ML no Azure HDInsight](r-server-overview.md) fornece as capacidades mais recentes para análise baseada em R. Pode utilizar os dados armazenados no contentor de Apache Hadoop HDFS no seu [BLOBs do Azure](../../storage/common/storage-introduction.md "armazenamento de Blobs do Azure") conta de armazenamento, um arquivo do Data Lake ou sistema de arquivos local do Linux. Como os serviços de ML é criado no R de código aberto, podem aplicar os aplicativos baseados em R que criar nenhum dos pacotes de R open-source 8000 +. Também podem utilizar as rotinas em [RevoScaleR](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler), pacote de análise de macrodados da Microsoft que está incluído com os serviços de ML.  

## <a name="compute-contexts-for-an-edge-node"></a>Contextos para um nó de extremidade de computação
Em geral, um script R que é executado no cluster de serviços de ML no nó de extremidade é executado dentro do interpretador de R nesse nó. As exceções são os passos que chamam uma função de RevoScaleR. As chamadas de RevoScaleR executam num ambiente de computação que é determinado pelo como definir o contexto de cálculo de RevoScaleR.  Quando executar o script R a partir de um nó de extremidade, os valores possíveis do contexto de computação são:

- local seqüencial (*local*)
- local parallel (*localpar*)
- Reduzir o mapa
- Spark

O *local* e *localpar* opções sejam diferentes apenas em como **rxExec** chamadas são executadas. Ambos executar outras chamadas de função rx de forma paralela nos núcleos disponíveis, a menos que especificado de outra forma através da utilização do RevoScaleR **numCoresToUse** opção, por exemplo `rxOptions(numCoresToUse=6)`. As opções de execução paralela oferecem um desempenho ideal.

A tabela seguinte resume as várias opções de contexto de computação para definir como as chamadas são executadas:

| Contexto de cálculo  | Como configurar                      | Contexto de execução                        |
| ---------------- | ------------------------------- | ---------------------------------------- |
| Local sequencial | rxSetComputeContext('local')    | Execução em paralelo entre os núcleos de servidor de nó edge, exceto para chamadas de rxExec, que são executados serialmente |
| Paralelo local   | rxSetComputeContext('localpar') | Execução em paralelo entre os núcleos de servidor de nó edge |
| Spark            | RxSpark()                       | Execução distribuída em paralelo através do Spark em todos os nós do HDI cluster |
| Reduzir o mapa       | RxHadoopMR()                    | Execução distribuída em paralelo através do mapeamento redução em todos os nós do HDI cluster |

## <a name="guidelines-for-deciding-on-a-compute-context"></a>Diretrizes para decidir sobre o contexto de cálculo

Qual das três opções escolha que fornecem a execução em paralelo depende da natureza de seu trabalho de análise, o tamanho e a localização dos seus dados. Não há nenhuma fórmula simples, que indica quais contexto utilizar de cálculo. No entanto, existem alguns princípios importantes que podem ajudá-lo a escolha certa, ou, pelo menos, ajudar a limitar suas opções antes de executar um parâmetro de comparação. Estes princípios básicos incluem:

- O sistema de ficheiros de Linux local é mais rápido que HDFS.
- Análises repetidas são mais rápidos, se os dados são locais e se está a ser XDF.
- É preferível para transmissão em fluxo pequenas quantidades de dados a partir de uma origem de dados de texto. Se a quantidade de dados é maior, convertê-la XDF antes de análise.
- A sobrecarga de copiar ou os dados para o nó de extremidade para análise de transmissão em fluxo torna-se impossível de gerenciar para grandes quantidades de dados.
- ApacheSpark é mais rápido do que o mapa reduzir para análise no Hadoop.

Tendo em conta esses princípios, as seções a seguir oferecem algumas regras básicas para a seleção de um contexto de cálculo.

### <a name="local"></a>Localização
* Se a quantidade de dados para analisar é pequena e não necessita de análise repetido, em seguida, transmiti-la diretamente na análise rotina usando *local* ou *localpar*.
* Se a quantidade de dados para analisar é pequeno ou médio porte e requer a análise repetida, em seguida, copiá-lo para o sistema de arquivos local, importá-lo para XDF e analisá-los por meio *local* ou *localpar*.

### <a name="apache-spark"></a>Apache Spark
* Se a quantidade de dados para analisar for grande, em seguida, importá-lo para um DataFrame do Spark através de **RxHiveData** ou **RxParquetData**, ou para XDF hdfs (a menos que o armazenamento é um problema) e analise-os com a computação de Spark contexto.

### <a name="apache-hadoop-map-reduce"></a>Reduzir o mapa do Apache Hadoop
* Utilize o contexto de cálculo de mapeamento redução apenas se ocorrer um problema insuperável com o contexto de cálculo do Spark, uma vez que é geralmente mais lento.  

## <a name="inline-help-on-rxsetcomputecontext"></a>Ajuda inline no rxSetComputeContext
Para obter mais informações e exemplos de contextos de cálculo de RevoScaleR, consulte o inline ajudar em R no método rxSetComputeContext, por exemplo:

    > ?rxSetComputeContext

Também pode consultar o [distribuído descrição geral da informática](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-distributed-computing) na [documentação do Machine Learning Server](https://docs.microsoft.com/machine-learning-server/).

## <a name="next-steps"></a>Passos Seguintes
Neste artigo, aprendeu sobre as opções que estão disponíveis para especificar se e como a execução é paralelizada entre os núcleos do nó de extremidade ou cluster do HDInsight. Para saber mais sobre como utilizar os serviços de ML com clusters do HDInsight, consulte os seguintes tópicos:

* [Descrição geral dos serviços de ML para o Apache Hadoop](r-server-overview.md)
* [Introdução aos serviços de ML para o Apache Hadoop](r-server-get-started.md)
* [Opções de armazenamento do Azure para os serviços de ML no HDInsight](r-server-storage.md)

