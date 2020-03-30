---
title: Opções de contexto computacional para serviços ML no HDInsight - Azure
description: Conheça as diferentes opções de contexto computacional disponíveis para os utilizadores com serviços ML no HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/02/2020
ms.openlocfilehash: b67bd5b6310e1f8ce35dc14690757209ef62c9d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75660261"
---
# <a name="compute-context-options-for-ml-services-on-hdinsight"></a>Opções de contexto computacional para serviços ML no HDInsight

Os serviços ML no Azure HDInsight controlam a forma como as chamadas são executadas definindo o contexto da computação. Este artigo descreve as opções disponíveis para especificar se e como a execução é paralelamente entre núcleos do nó de borda ou do cluster HDInsight.

O nó de borda de um cluster fornece um lugar conveniente para ligar ao cluster e executar os seus scripts R. Com um nó de borda, tem a opção de executar as funções distribuídas paralelas do RevoScaleR através dos núcleos do servidor do nó de borda. Também pode executá-los através dos nós do cluster utilizando os contextos de cálculo do Mapa hadoop do RevoScaleR ou da computação Apache Spark.

## <a name="ml-services-on-azure-hdinsight"></a>Serviços ML em Azure HDInsight

[Os serviços ML no Azure HDInsight](r-server-overview.md) fornecem as mais recentes capacidades para análises baseadas em R. Pode utilizar dados que são armazenados num recipiente Apache Hadoop HDFS na sua conta de armazenamento [Azure Blob,](../../storage/common/storage-introduction.md "Armazenamento de Blobs do Azure") numa Data Lake Store ou no sistema de ficheiros Linux local. Uma vez que os Serviços ML são construídos em código r aberto, as aplicações baseadas em R que você constrói podem aplicar qualquer um dos pacotes R de código aberto de 8000+. Também podem usar as rotinas no [RevoScaleR,](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler)o pacote de análise de big data da Microsoft que está incluído nos Serviços ML.  

## <a name="compute-contexts-for-an-edge-node"></a>Contextos computacionais para um nó de borda

Em geral, um script R que é executado no cluster ml Services no nó de borda corre dentro do intérprete R nesse nó. As exceções são as etapas que chamam uma função RevoScaleR. As chamadas RevoScaleR funcionam num ambiente de computação que é determinado pela forma como define o contexto computacional RevoScaleR.  Quando executa o seu script R a partir de um nó de borda, os valores possíveis do contexto computacional são:

- sequencial local *(local)*
- paralelo local *(localpar)*
- Reduzir o mapa
- Spark

As opções *locais* e *locais* diferem apenas na forma como as chamadas **rxExec** são executadas. Ambos executam outras chamadas de função rx de forma paralela em todos os núcleos disponíveis, a `rxOptions(numCoresToUse=6)`menos que especificado de outra forma através da utilização da opção RevoScaleR **numCoresToUse,** por exemplo. As opções de execução paralelas oferecem um desempenho ideal.

A tabela que se segue resume as várias opções de contexto computacional para definir como as chamadas são executadas:

| Contexto de cálculo  | Como definir                      | Contexto de execução                        |
| ---------------- | ------------------------------- | ---------------------------------------- |
| Sequencial local | rxSetComputeContext ('local')    | Execução paralela através dos núcleos do servidor do nó de borda, exceto para chamadas rxExec, que são executadas em série |
| Paralelo local   | rxSetComputeContext ('localpar') | Execução paralelizada através dos núcleos do servidor do nó de borda |
| Spark            | RxSpark()                       | Execução distribuída paralelamente através da Faísca através dos nós do cluster HDI |
| Reduzir o mapa       | RxHadoopMR()                    | Execução distribuída paralelamente via Mapa Reduzir através dos nós do cluster HDI |

## <a name="guidelines-for-deciding-on-a-compute-context"></a>Orientações para decidir sobre um contexto computacional

Qual das três opções que escolher que fornecem execução paralelizada depende da natureza do seu trabalho de análise, do tamanho e da localização dos seus dados. Não há uma fórmula simples que te diga, que contexto de computação usar. Existem, no entanto, alguns princípios orientadores que podem ajudá-lo a fazer a escolha certa, ou, pelo menos, ajudá-lo a reduzir as suas escolhas antes de executar uma referência. Estes princípios orientadores incluem:

- O sistema de ficheiros Linux local é mais rápido que o HDFS.
- As análises repetidas são mais rápidas se os dados forem locais, e se estiverem em XDF.
- É preferível transmitir pequenas quantidades de dados a partir de uma fonte de dados de texto. Se a quantidade de dados for maior, converta-os em XDF antes da análise.
- A sobrecarga de copiar ou transmitir os dados para o nó de borda para análise torna-se incontrolável para grandes quantidades de dados.
- ApacheSpark é mais rápido que map Reduce para análise em Hadoop.

Tendo em conta estes princípios, as seguintes secções oferecem algumas regras gerais do polegar para selecionar um contexto computacional.

### <a name="local"></a>Localização

- Se a quantidade de dados a analisar for pequena e não necessitar de análise repetida, em seguida, transmita-os diretamente para a rotina de análise utilizando *local* ou *localpar*.
- Se a quantidade de dados a analisar for pequena ou média e necessitar de análise repetida, copie-os para o sistema de ficheiros local, importe-os para XDF e analise-os através de *par local* ou *local*.

### <a name="apache-spark"></a>Apache Spark

- Se a quantidade de dados a analisar for grande, então importe-os para um Spark DataFrame utilizando **RxHiveData** ou **RxParquetData**, ou para XDF em HDFS (a menos que o armazenamento seja um problema), e analise-os usando o contexto de computação Spark.

### <a name="apache-hadoop-map-reduce"></a>Apache Hadoop Map Reduzir

- Utilize o contexto de cálculo mape apenas se encontrar um problema intransponível com o contexto da computação Spark, uma vez que é geralmente mais lento.  

## <a name="inline-help-on-rxsetcomputecontext"></a>Ajuda inline no rxSetComputeContext
Para obter mais informações e exemplos de contextos computacionais RevoScaleR, consulte a ajuda inline em R no método rxSetComputeContext, por exemplo:

    > ?rxSetComputeContext

Também pode consultar a visão geral da [computação distribuída](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-distributed-computing) na documentação do [Servidor de Aprendizagem automática.](https://docs.microsoft.com/machine-learning-server/)

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu sobre as opções que estão disponíveis para especificar se e como a execução é paralelizada através de núcleos do nó de borda ou cluster HDInsight. Para saber mais sobre como utilizar os Serviços ML com clusters HDInsight, consulte os seguintes tópicos:

- [Visão geral dos serviços ml para Apache Hadoop](r-server-overview.md)
- [Opções de Armazenamento Azure para serviços ML no HDInsight](r-server-storage.md)
