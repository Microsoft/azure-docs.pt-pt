---
title: Opções de contexto de computação para serviços de ML no HDInsight – Azure
description: Saiba mais sobre as diferentes opções de contexto de computação disponíveis para usuários com serviços de ML no HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/02/2020
ms.openlocfilehash: b67bd5b6310e1f8ce35dc14690757209ef62c9d7
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/04/2020
ms.locfileid: "75660261"
---
# <a name="compute-context-options-for-ml-services-on-hdinsight"></a>Opções de contexto de computação para serviços de ML no HDInsight

Os serviços de ML no Azure HDInsight controlam como as chamadas são executadas definindo o contexto de computação. Este artigo descreve as opções disponíveis para especificar se e como a execução é paralelizada entre os núcleos do nó de borda ou do cluster HDInsight.

O nó de borda de um cluster fornece um local conveniente para se conectar ao cluster e executar os scripts do R. Com um nó de borda, você tem a opção de executar as funções distribuídas paralelizadas do RevoScaleR nos núcleos do servidor do nó de borda. Você também pode executá-los em todos os nós do cluster usando o mapa do Hadoop do RevoScaleR para reduzir ou Apache Spark contextos de computação.

## <a name="ml-services-on-azure-hdinsight"></a>Serviços de ML no Azure HDInsight

Os [serviços de ml no Azure HDInsight](r-server-overview.md) fornecem os recursos mais recentes para análise baseada em R. Ele pode usar dados armazenados em um contêiner Apache Hadoop HDFS em sua conta de armazenamento de [BLOBs do Azure](../../storage/common/storage-introduction.md "Armazenamento de Blobs do Azure") , em um data Lake Store ou no sistema de arquivos Linux local. Como os serviços ML se baseiam no R de software livre, os aplicativos baseados em R que você cria podem aplicar qualquer um dos mais de 8000 pacotes de R de software livre. Eles também podem usar as rotinas no [RevoScaleR](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler), o pacote de análise Big data da Microsoft que está incluído nos serviços de ml.  

## <a name="compute-contexts-for-an-edge-node"></a>Contextos de computação para um nó de borda

Em geral, um script R que é executado no cluster de serviços ML no nó de borda é executado no intérprete R nesse nó. As exceções são as etapas que chamam uma função RevoScaleR. As chamadas RevoScaleR são executadas em um ambiente de computação que é determinado pelo modo como você define o contexto de computação RevoScaleR.  Quando você executa o script R de um nó de borda, os valores possíveis do contexto de computação são:

- local sequencial (*local*)
- local paralelo (*localpar*)
- Redução de mapa
- Spark

As opções *local* e *localpar* diferem apenas em como as chamadas **rxExec** são executadas. Ambos executam outras chamadas de função RX de maneira paralela em todos os núcleos disponíveis, a menos que especificado de outra forma pelo uso da opção RevoScaleR **numCoresToUse** , por exemplo `rxOptions(numCoresToUse=6)`. As opções de execução paralela oferecem um desempenho ideal.

A tabela a seguir resume as várias opções de contexto de computação para definir como as chamadas são executadas:

| Contexto de cálculo  | Como definir                      | Contexto de execução                        |
| ---------------- | ------------------------------- | ---------------------------------------- |
| Sequencial local | rxSetComputeContext('local')    | Execução paralelizada entre os núcleos do servidor de nó de borda, exceto para chamadas rxExec, que são executadas em série |
| Local paralelo   | rxSetComputeContext('localpar') | Execução paralelizada entre os núcleos do servidor de nó de borda |
| Spark            | RxSpark()                       | Execução distribuída em paralelo por meio do Spark em todos os nós do cluster HDI |
| Redução de mapa       | RxHadoopMR()                    | Execução distribuída em paralelo por meio da redução de mapa em todos os nós do cluster HDI |

## <a name="guidelines-for-deciding-on-a-compute-context"></a>Diretrizes para decidir sobre um contexto de computação

Quais das três opções escolhidas que fornecem execução paralelizada depende da natureza do trabalho de análise, do tamanho e do local dos dados. Não há uma fórmula simples que indique a você, qual contexto de computação usar. No entanto, há alguns princípios de orientação que podem ajudá-lo a fazer a escolha certa ou, pelo menos, ajudá-lo a restringir suas escolhas antes de executar um parâmetro de comparação. Esses princípios de orientação incluem:

- O sistema de arquivos Linux local é mais rápido do que o HDFS.
- As análises repetidas serão mais rápidas se os dados forem locais e se estiverem em XDF.
- É preferível transmitir pequenas quantidades de dados de uma fonte de dados de texto. Se a quantidade de dados for maior, converta-o em XDF antes da análise.
- A sobrecarga de copiar ou transmitir os dados para o nó de borda para análise se torna não gerenciável para grandes quantidades de dados.
- ApacheSpark é mais rápido do que a redução de mapa para análise no Hadoop.

Considerando esses princípios, as seções a seguir oferecem algumas regras gerais para a seleção de um contexto de computação.

### <a name="local"></a>Local

- Se a quantidade de dados a ser analisada for pequena e não exigir análise repetida, transmita-a diretamente para a rotina de análise usando *local* ou *localpar*.
- Se a quantidade de dados a ser analisada for de pequeno ou médio porte e exigir uma análise repetida, copie-a para o sistema de arquivos local, importe-a para XDF e analise-a por meio de *local* ou *localpar*.

### <a name="apache-spark"></a>Apache Spark

- Se a quantidade de dados a ser analisada for grande, importe-a para um data frame do Spark usando **RxHiveData** ou **RXPARQUETDATA**ou para Xdf no HDFS (a menos que o armazenamento seja um problema) e analise-o usando o contexto de computação do Spark.

### <a name="apache-hadoop-map-reduce"></a>Redução do mapa de Apache Hadoop

- Use o contexto de computação de redução de mapa somente se você vir um problema insuperável com o contexto de computação do Spark, pois ele é geralmente mais lento.  

## <a name="inline-help-on-rxsetcomputecontext"></a>Ajuda embutida no rxSetComputeContext
Para obter mais informações e exemplos de contextos de computação RevoScaleR, consulte a ajuda embutida em R no método rxSetComputeContext, por exemplo:

    > ?rxSetComputeContext

Você também pode consultar a [visão geral da computação distribuída](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-distributed-computing) na [documentação do Machine Learning Server](https://docs.microsoft.com/machine-learning-server/).

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu sobre as opções disponíveis para especificar se e como a execução é paralelizada entre os núcleos do nó de borda ou do cluster HDInsight. Para saber mais sobre como usar os serviços de ML com clusters HDInsight, consulte os seguintes tópicos:

- [Visão geral dos serviços de ML para Apache Hadoop](r-server-overview.md)
- [Opções de armazenamento do Azure para serviços de ML no HDInsight](r-server-storage.md)
