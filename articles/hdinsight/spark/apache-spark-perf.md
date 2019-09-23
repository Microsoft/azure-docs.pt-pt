---
title: Otimizar trabalhos do Spark para desempenho-Azure HDInsight
description: Mostre estratégias comuns para o melhor desempenho de clusters de Apache Spark no Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/03/2019
ms.openlocfilehash: 64dfd26e02526664a4edb204521f7a47a4463a12
ms.sourcegitcommit: a19bee057c57cd2c2cd23126ac862bd8f89f50f5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/23/2019
ms.locfileid: "71181074"
---
# <a name="optimize-apache-spark-jobs-in-hdinsight"></a>Otimizar Apache Spark trabalhos no HDInsight

Saiba como otimizar [Apache Spark](https://spark.apache.org/) configuração de cluster para sua carga de trabalho específica.  O desafio mais comum é a pressão de memória, devido a configurações incorretas (especialmente executores de tamanho errado), operações de execução longa e tarefas que resultam em operações cartesianas. Você pode acelerar os trabalhos com o cache apropriado e permitindo a [distorção de dados](#optimize-joins-and-shuffles). Para obter o melhor desempenho, monitore e examine as execuções de trabalho do Spark de longa execução e de consumo de recursos.

As seções a seguir descrevem as recomendações e otimizações comuns de trabalho do Spark.

## <a name="choose-the-data-abstraction"></a>Escolher a abstração de dados

As versões anteriores do Spark usam RDDs para abstrair dados, Spark 1,3 e 1,6 introduziu dataframes e DataSets, respectivamente. Considere os seguintes méritos relativos:

* **DataFrames**
    * Melhor opção na maioria das situações.
    * Fornece otimização de consulta por meio do Catalyst.
    * Geração de código de estágio completo.
    * Acesso direto à memória.
    * Sobrecarga de coleta de lixo baixo (GC).
    * Não como um desenvolvedor amigável como conjuntos de valores, pois não há verificações de tempo de compilação ou programação de objeto de domínio.
* **DataSets**
    * Bom em pipelines de ETL complexos em que o impacto no desempenho é aceitável.
    * Não é bom em agregações em que o impacto no desempenho pode ser considerável.
    * Fornece otimização de consulta por meio do Catalyst.
    * Amigável para desenvolvedores fornecendo programação de objeto de domínio e verificações de tempo de compilação.
    * Adiciona a sobrecarga de serialização/desserialização.
    * Alta sobrecarga de GC.
    * Interrompe a geração de código de estágio inteiro.
* **RDDs**
    * Você não precisa usar o RDDs, a menos que precise criar um novo RDD personalizado.
    * Nenhuma otimização de consulta por meio do Catalyst.
    * Nenhuma geração de código de estágio inteiro.
    * Alta sobrecarga de GC.
    * Deve usar APIs herdadas do Spark 1. x.

## <a name="use-optimal-data-format"></a>Usar o formato de dados ideal

O Spark dá suporte a muitos formatos, como CSV, JSON, XML, parquet, Orc e Avro. O Spark pode ser estendido para dar suporte a muitos outros formatos com fontes de dados externas-para obter mais informações, consulte [pacotes de Apache Spark](https://spark-packages.org).

O melhor formato para desempenho é parquet com *compactação de instantâneo*, que é o padrão no Spark 2. x. O parquet armazena dados no formato de coluna e é altamente otimizado no Spark.

## <a name="select-default-storage"></a>Selecionar armazenamento padrão

Ao criar um novo cluster Spark, você tem a opção de selecionar o armazenamento de BLOBs do Azure ou Azure Data Lake Storage como o armazenamento padrão do cluster. Ambas as opções oferecem o benefício do armazenamento de longo prazo para clusters transitórios, para que seus dados não sejam excluídos automaticamente quando você excluir o cluster. Você pode recriar um cluster transitório e ainda acessar seus dados.

| Tipo de repositório | Sistema de ficheiros | Velocidade | Transitório | Casos de Utilização |
| --- | --- | --- | --- | --- |
| Armazenamento de Blobs do Azure | **wasb:** //url/ | **Standard** | Sim | Cluster transitório |
| Azure Data Lake Storage Gen 2| **abfs:** //URL/ | **Meio** | Sim | Cluster transitório |
| Armazenamento do Azure Data Lake Ger 1| **adl:** //url/ | **Meio** | Sim | Cluster transitório |
| HDFS local | **hdfs:** //url/ | **Fastest** | Não | Cluster interativo 24/7 |

## <a name="use-the-cache"></a>Usar o cache

O Spark fornece seus próprios mecanismos de cache nativos, que podem ser usados por meio de `.persist()`métodos `.cache()`diferentes, `CACHE TABLE`como, e. Esse cache nativo é eficaz com pequenos conjuntos de dados, bem como em pipelines de ETL em que você precisa armazenar em cache resultados intermediários. No entanto, o cache nativo do Spark atualmente não funciona bem com o particionamento, uma vez que uma tabela armazenada em cache não mantém os dados de particionamento. Uma técnica de cache mais genérica e confiável é o *cache de camada de armazenamento*.

* Cache do Spark nativo (não recomendado)
    * Bom para pequenos conjuntos de valores.
    * Não funciona com particionamento, o que pode ser alterado em versões futuras do Spark.

* Cache de nível de armazenamento (recomendado)
    * Pode ser implementado usando [Alluxio](https://www.alluxio.org/).
    * Usa cache na memória e SSD.

* HDFS local (recomendado)
    * `hdfs://mycluster`Multi-Path.
    * Usa o cache SSD.
    * Os dados armazenados em cache serão perdidos quando você excluir o cluster, exigindo uma recompilação do cache.

## <a name="use-memory-efficiently"></a>Usar memória com eficiência

O Spark Opera colocando dados na memória, de modo que gerenciar recursos de memória é um aspecto fundamental da otimização da execução de trabalhos do Spark.  Há várias técnicas que você pode aplicar para usar a memória do cluster com eficiência.

* Prefira partições de dados menores e a conta para tamanho de dados, tipos e distribuição em sua estratégia de particionamento.
* Considere a [serialização de dados kryo](https://github.com/EsotericSoftware/kryo)mais recente e mais eficiente, em vez da serialização Java padrão.
* Prefira usar yarn, pois ele é separado `spark-submit` pelo lote.
* Monitorar e ajustar as definições de configuração do Spark.

Para sua referência, a estrutura de memória do Spark e alguns parâmetros de memória de executor de chave são mostrados na próxima imagem.

### <a name="spark-memory-considerations"></a>Considerações sobre memória do Spark

Se você estiver usando [Apache HADOOP yarn](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html), yarn controlará a soma máxima da memória usada por todos os contêineres em cada nó do Spark.  O diagrama a seguir mostra os objetos-chave e suas relações.

![Gerenciamento de memória YARN Spark](./media/apache-spark-perf/apache-yarn-spark-memory.png)

Para endereçar mensagens de "memória insuficiente", tente:

* Examine as embaralhamentos de gerenciamento do DAG. Reduza por redutos no lado do mapa, dados de origem previamente particionados (ou buckets), maximizar as embaralhamentos simples e reduzir a quantidade de dados enviados.
* Prefira `ReduceByKey` com seu limite de memória fixa `GroupByKey`para, que fornece agregações, janelas e outras funções, mas tem um limite de memória não associado a Ana.
* Prefira `TreeReduce`, que faz mais trabalho nos executores ou nas partições, para `Reduce`, o que faz tudo funcionar no driver.
* Aproveite quadros de molduras em vez de objetos RDD de nível inferior.
* Crie ComplexTypes que encapsulam ações, como "N principais", várias agregações ou operações de janela.

## <a name="optimize-data-serialization"></a>Otimizar a serialização de dados

Os trabalhos do Spark são distribuídos, portanto, a serialização de dados apropriada é importante para o melhor desempenho.  Há duas opções de serialização para o Spark:

* A serialização Java é o padrão.
* A serialização kryo é um formato mais recente e pode resultar em serialização mais rápida e compactada do que Java.  O kryo exige que você registre as classes em seu programa e ainda não oferece suporte a todos os tipos serializáveis.

## <a name="use-bucketing"></a>Usar bucketing

O bucketing é semelhante ao particionamento de dados, mas cada bucket pode conter um conjunto de valores de coluna em vez de apenas um. O Bucket funciona bem para o particionamento em um grande número de valores (em milhões ou mais), como identificadores de produtos. Um Bucket é determinado pelo hash da chave de Bucket da linha. As tabelas segmentadas oferecem otimizações exclusivas porque armazenam metadados sobre como elas foram segmentadas e classificadas.

Alguns recursos avançados de bucketing são:

* Otimização de consulta com base nas meta-informações do bucketing.
* Agregações otimizadas.
* Junções otimizadas.

Você pode usar particionamento e bucketing ao mesmo tempo.

## <a name="optimize-joins-and-shuffles"></a>Otimizar junções e embaralhamentos

Se você tiver trabalhos lentos em uma junção ou em ordem aleatória, a causa provavelmente será a *distorção de dados*, que é assimetria em seus dados de trabalho. Por exemplo, um trabalho de mapa pode levar 20 segundos, mas executar um trabalho onde os dados são ingressados ou embaralhados leva horas.   Para corrigir a distorção de dados, você deve usar o Salt da chave inteira ou utilizar um *Salt isolado* para apenas algum subconjunto de chaves.  Se você estiver usando um Salt isolado, deverá filtrar ainda mais para isolar seu subconjunto de chaves com Salt em junções de mapa. Outra opção é introduzir uma coluna de Bucket e agregar previamente em buckets primeiro.

Outro fator que causa junções lentas poderia ser o tipo de junção. Por padrão, o Spark usa `SortMerge` o tipo de junção. Esse tipo de junção é mais adequado para grandes conjuntos de dados, mas, de outra forma, é computacionalmente caro, pois ele deve primeiro classificar os lados esquerdo e direito dos dados antes de mesclá-los.

Uma `Broadcast` junção é mais adequada para conjuntos de dados menores ou onde um lado da junção é muito menor do que o outro lado. Esse tipo de junção difunde um lado para todos os executores e, portanto, requer mais memória para difusões em geral.

Você pode alterar o tipo de junção em sua configuração definindo `spark.sql.autoBroadcastJoinThreshold`ou pode definir uma dica de junção usando as APIs do dataframe (`dataframe.join(broadcast(df2))`).

```scala
// Option 1
spark.conf.set("spark.sql.autoBroadcastJoinThreshold", 1*1024*1024*1024)

// Option 2
val df1 = spark.table("FactTableA")
val df2 = spark.table("dimMP")
df1.join(broadcast(df2), Seq("PK")).
    createOrReplaceTempView("V_JOIN")
sql("SELECT col1, col2 FROM V_JOIN")
```

Se você estiver usando tabelas em Bucket, terá um terceiro tipo de junção, a `Merge` junção. Um conjunto de espaço de os previamente particionado e previamente classificado ignorará a fase de classificação cara `SortMerge` de uma junção.

A ordem das junções é importante, especialmente em consultas mais complexas. Comece com as junções mais seletivas. Além disso, mova junções que aumentam o número de linhas após as agregações, quando possível.

Para gerenciar o paralelismo, especificamente no caso de junções cartesianas, você pode adicionar estruturas aninhadas, janelas e, talvez, ignorar uma ou mais etapas em seu trabalho do Spark.

## <a name="customize-cluster-configuration"></a>Personalizar a configuração do cluster

Dependendo da carga de trabalho do cluster Spark, você pode determinar que uma configuração não padrão do Spark resultaria em uma execução de trabalhos Spark mais otimizada.  Execute testes de benchmark com cargas de trabalho de exemplo para validar as configurações de cluster não padrão.

Aqui estão alguns parâmetros comuns que você pode ajustar:

* `--num-executors`define o número apropriado de executores.
* `--executor-cores`define o número de núcleos para cada executor. Normalmente, você deve ter executores de tamanho intermediário, pois outros processos consomem parte da memória disponível.
* `--executor-memory`define o tamanho da memória para cada executor, que controla o tamanho do heap em YARN. Você deve deixar alguma memória para a sobrecarga de execução.

### <a name="select-the-correct-executor-size"></a>Selecionar o tamanho correto do executor

Ao decidir a configuração do executor, considere a sobrecarga de GC (coleta de lixo) do Java.

* Fatores para reduzir o tamanho do executor:
    1. Reduza o tamanho do heap abaixo de 32 GB para manter a sobrecarga do GC < 10%.
    2. Reduza o número de núcleos para manter a sobrecarga de GC < 10%.

* Fatores para aumentar o tamanho do executor:
    1. Reduza a sobrecarga de comunicação entre executores.
    2. Reduza o número de conexões abertas entre os executores (N2) em clusters maiores (> 100 executores).
    3. Aumente o tamanho do heap para acomodar tarefas com uso intensivo de memória.
    4. Opcional: Reduzir a sobrecarga de memória por executor.
    5. Opcional: Aumente a utilização e a simultaneidade da CPU sobrecarregar.

Como regra geral ao selecionar o tamanho do executor:
    
1. Comece com 30 GB por executor e distribua os núcleos de máquina disponíveis.
2. Aumente o número de núcleos de executor para clusters maiores (> executores 100).
3. Aumente ou diminua os tamanhos com base nas execuções de avaliação e nos fatores anteriores, como a sobrecarga de GC.

Ao executar consultas simultâneas, considere o seguinte:

1. Comece com 30 GB por executor e todos os núcleos de máquina.
2. Crie vários aplicativos paralelos do Spark por CPU sobrecarregar (cerca de 30% de melhoria de latência).
3. Distribua consultas em aplicativos paralelos.
4. Aumente ou diminua os tamanhos com base nas execuções de avaliação e nos fatores anteriores, como a sobrecarga de GC.

Monitore o desempenho de consulta para exceções ou outros problemas de desempenho examinando o modo de exibição de linha do tempo, SQL Graph, estatísticas de trabalho e assim por diante. Às vezes, um ou alguns dos executores são mais lentos do que os outros, e as tarefas demoram muito mais para serem executadas. Isso geralmente ocorre em clusters maiores (> 30 nós). Nesse caso, divida o trabalho em um número maior de tarefas para que o Agendador possa compensar tarefas lentas. Por exemplo, tenha pelo menos duas vezes mais tarefas que o número de núcleos de executor no aplicativo. Você também pode habilitar a execução especulativa de tarefas `conf: spark.speculation = true`com o.

## <a name="optimize-job-execution"></a>Otimizar a execução do trabalho

* Armazene em cache conforme necessário, por exemplo, se você usar os dados duas vezes e armazená-lo em cache.
* Transmita variáveis para todos os executores. As variáveis são serializadas apenas uma vez, resultando em pesquisas mais rápidas.
* Use o pool de threads no driver, o que resulta em uma operação mais rápida para muitas tarefas.

Monitore seus trabalhos em execução regularmente para problemas de desempenho. Se precisar de mais informações sobre determinados problemas, considere uma das seguintes ferramentas de criação de perfil de desempenho:

* A [ferramenta PAL Intel](https://github.com/intel-hadoop/PAT) monitora a utilização da CPU, do armazenamento e da largura de banda da rede.
* Perfis de [controle de missão do Oracle Java 8](https://www.oracle.com/technetwork/java/javaseproducts/mission-control/java-mission-control-1998576.html) , Spark e código de executor.

A chave para o desempenho de consulta do Spark 2. x é o mecanismo Tungsten, que depende da geração de código de estágio completo. Em alguns casos, a geração de código de estágio inteiro pode ser desabilitada. Por exemplo, se você usar um tipo não mutável (`string`) na expressão de agregação, `SortAggregate` aparecerá em vez de. `HashAggregate` Por exemplo, para obter um melhor desempenho, tente o seguinte e, em seguida, reabilite a geração de código:

```sql
MAX(AMOUNT) -> MAX(cast(AMOUNT as DOUBLE))
```

## <a name="next-steps"></a>Passos seguintes

* [Depurar trabalhos do Apache Spark em execução no Azure HDInsight](apache-spark-job-debugging.md)
* [Gerenciar recursos para um cluster Apache Spark no HDInsight](apache-spark-resource-manager.md)
* [Usar a API REST do Apache Spark para enviar trabalhos remotos para um cluster Apache Spark](apache-spark-livy-rest-interface.md)
* [Ajustando Apache Spark](https://spark.apache.org/docs/latest/tuning.html)
* [Como ajustar seus trabalhos de Apache Spark para que eles funcionem](https://www.slideshare.net/ilganeli/how-to-actually-tune-your-spark-jobs-so-they-work)
* [Serialização kryo](https://github.com/EsotericSoftware/kryo)
