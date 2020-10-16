---
title: Opções de contexto de computação para serviços ML em HDInsight - Azure
description: Conheça as diferentes opções de contexto computacional disponíveis para utilizadores com Serviços ML em HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/02/2020
ms.openlocfilehash: 4df3c24c6f0853c1ae7447a8e20e8c2944319686
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86087610"
---
# <a name="compute-context-options-for-ml-services-on-hdinsight"></a>Opções de contexto computacional para serviços ML em HDInsight

Os serviços ML em Azure HDInsight controlam a forma como as chamadas são executadas definindo o contexto do cálculo. Este artigo descreve as opções disponíveis para especificar se e como a execução é paralelamente através de núcleos do nó de borda ou cluster HDInsight.

O nó de borda de um cluster fornece um lugar conveniente para se conectar ao cluster e executar os seus scripts R. Com um nó de borda, você tem a opção de executar as funções distribuídas paralelas de RevoScaleR através dos núcleos do servidor de nó de borda. Também pode executá-los através dos nós do cluster, utilizando os contextos de cálculo Hadoop Do RevoScaleR ou apache spark.

## <a name="ml-services-on-azure-hdinsight"></a>Serviços ML em Azure HDInsight

[ML Services on Azure HDInsight](r-server-overview.md) fornece as mais recentes capacidades para análise baseada em R. Pode utilizar dados que são armazenados num recipiente Apache Hadoop HDFS na sua conta de armazenamento [Azure Blob,](../../storage/common/storage-introduction.md "Armazenamento de Blobs do Azure") na Data Lake Store ou no sistema de ficheiros Linux local. Uma vez que os Serviços ML são construídos em R de código aberto, as aplicações baseadas em R que você constrói podem aplicar qualquer um dos mais de 8000 pacotes R de código aberto. Também podem utilizar as rotinas no [RevoScaleR,](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler)o pacote de análise de big data da Microsoft que está incluído nos Serviços ML.  

## <a name="compute-contexts-for-an-edge-node"></a>Contextos de cálculo para um nó de borda

Em geral, um script R que é executado no cluster ML Services no nó de borda corre dentro do intérprete R nesse nó. As exceções são os passos que chamam uma função RevoScaleR. As chamadas RevoScaleR são executadas num ambiente computacional que é determinado pela forma como define o contexto computacional RevoScaleR.  Quando executa o seu script R a partir de um nó de borda, os valores possíveis do contexto do cálculo são:

- sequencial local *(local)*
- paralelo local *(localpar)*
- Reduzir mapa
- Spark

As opções *locais* e *locais diferem* apenas na forma como as chamadas **rxExec** são executadas. Ambos executam outras chamadas de função rx de forma paralela em todos os núcleos disponíveis, salvo especificação em contrário através da utilização da opção RevoScaleR **numCoresToUse,** por exemplo `rxOptions(numCoresToUse=6)` . Opções de execução paralelas oferecem um desempenho ideal.

A tabela a seguir resume as várias opções de contexto computacional para definir como as chamadas são executadas:

| Contexto de cálculo  | Como definir                      | Contexto de execução                        |
| ---------------- | ------------------------------- | ---------------------------------------- |
| Sequencial local | rxSetComputeContext ('local')    | Execução paralela através dos núcleos do servidor de nó de borda, exceto para chamadas rxExec, que são executadas em série |
| Paralelo local   | rxSetComputeContext ('localpar') | Execução paralela através dos núcleos do servidor de nó de borda |
| Spark            | RxSpark()                       | Execução distribuída paralelamente através da Faísca através dos nóns do cluster HDI |
| Reduzir mapa       | RxHadoopMR()                    | Execução distribuída paralelamente através do mapa Reduza através dos nóns do cluster HDI |

## <a name="guidelines-for-deciding-on-a-compute-context"></a>Diretrizes para decidir sobre um contexto computacional

Qual das três opções que escolhe que fornecem execução paralela depende da natureza do seu trabalho de análise, do tamanho e da localização dos seus dados. Não há uma fórmula simples que te diga qual o contexto computacional a usar. Existem, no entanto, alguns princípios orientadores que podem ajudá-lo a fazer a escolha certa, ou, pelo menos, ajudá-lo a reduzir as suas escolhas antes de executar uma referência. Estes princípios orientadores incluem:

- O sistema de ficheiros Linux local é mais rápido que o HDFS.
- As análises repetidas são mais rápidas se os dados forem locais, e se estiver em XDF.
- É preferível transmitir pequenas quantidades de dados a partir de uma fonte de dados de texto. Se a quantidade de dados for maior, converta-os em XDF antes da análise.
- A sobrecarga de copiar ou transmitir os dados para o nó de borda para análise torna-se incontrolável para grandes quantidades de dados.
- ApacheSpark é mais rápido que o Mapa Reduzir para análise em Hadoop.

Tendo em conta estes princípios, as seguintes secções oferecem algumas regras gerais do polegar para a seleção de um contexto computacional.

### <a name="local"></a>Local

- Se a quantidade de dados a analisar for pequena e não necessitar de análise repetida, em seguida, transmita-a diretamente para a rotina de análise *utilizando* local ou *localpar*.
- Se a quantidade de dados a analisar for pequena ou média e necessitar de análise repetida, copie-os para o sistema de ficheiros local, importe-os para XDF e analise-os através de *localidade* ou *local.*

### <a name="apache-spark"></a>Apache Spark

- Se a quantidade de dados a analisar for grande, então importe-a para um DataFrame de Faísca utilizando **RxHiveData** ou **RxParquetData,** ou para XDF em HDFS (a menos que o armazenamento seja um problema), e analise-o utilizando o contexto do computo Spark.

### <a name="apache-hadoop-map-reduce"></a>Mapa de Hadoop Apache Reduza

- Utilize o contexto de cálculo 'Mapa' apenas se encontrar um problema intransponível com o contexto de computação Spark, uma vez que é geralmente mais lento.  

## <a name="inline-help-on-rxsetcomputecontext"></a>Ajuda inline no rxSetComputeContexto
Para obter mais informações e exemplos de contextos de computação RevoScaleR, consulte a ajuda inline em R no método rxSetComputeContext, por exemplo:

```console
> ?rxSetComputeContext
```

Também pode consultar a [visão geral](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-distributed-computing) da computação distribuída na [documentação do Servidor de Aprendizagem automática](https://docs.microsoft.com/machine-learning-server/).

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu sobre as opções que estão disponíveis para especificar se e como a execução é paralelamente através de núcleos do nó de borda ou cluster HDInsight. Para saber mais sobre como utilizar os Serviços ML com clusters HDInsight, consulte os seguintes tópicos:

- [Visão geral dos serviços da ML para Apache Hadoop](r-server-overview.md)
- [Opções de Armazenamento Azure para Serviços ML em HDInsight](r-server-storage.md)
