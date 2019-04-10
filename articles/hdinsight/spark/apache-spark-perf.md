---
title: Otimizar tarefas do Spark para um desempenho - Azure HDInsight
description: Mostra as estratégias comuns para um melhor desempenho de clusters do Spark.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/03/2019
ms.openlocfilehash: b846b19d180bf19a0d023a9cd0b92393132f47d4
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59283074"
---
# <a name="optimize-apache-spark-jobs"></a>Otimizar tarefas do Apache Spark

Saiba como otimizar [Apache Spark](https://spark.apache.org/) configuração de cluster para a sua carga de trabalho específica.  O desafio mais comuns é a pressão de memória, devido a configurações impróprias (particularmente mal em tamanho normal executores), longa operações e tarefas que resultam em operações Cartesianos. É possível agilizar tarefas com a colocação em cache apropriado e, permitindo [distorção de dados](#optimize-joins-and-shuffles). Para obter o melhor desempenho, monitorize e veja as execuções de tarefas de Spark de longa execução e o consumo de recursos.

As secções seguintes descrevem as recomendações e otimizações de tarefa do Spark comuns.

## <a name="choose-the-data-abstraction"></a>Escolha a abstração de dados

Versões anteriores do Spark utilizam RDDs para dados abstratos, Spark 1.3, e 1.6 introduzidos DataFrames e conjuntos de dados, respectivamente. Considere os méritos relativos seguintes:

* **DataFrames**
    * Melhor opção na maioria das situações.
    * Fornece a otimização de consultas através de Catalyst.
    * Geração de código de fase de todo.
    * Acesso direto à memória.
    * Baixa sobrecarga coleta de lixo (GC).
    * Não tão amigável ao desenvolvedor como conjuntos de dados, como não há verificações de tempo de compilação ou programação de objeto de domínio.
* **DataSets**
    * Bom em pipelines ETL complexas em que o impacto no desempenho é aceitável.
    * Não é boa em agregações em que o impacto de desempenho pode ser considerável.
    * Fornece a otimização de consultas através de Catalyst.
    * Ideal para programadores ao fornecer verificações de programação e tempo de compilação do objeto de domínio.
    * Adiciona a sobrecarga de serialização/desserialização.
    * Alta sobrecarga de GC.
    * Divide a geração de código de fase de todo.
* **RDDs**
    * Não é necessário utilizar RDDs, a menos que precisa para criar um novo RDD personalizado.
    * Nenhuma otimização de consultas através de Catalyst.
    * Não existem geração de código de fase de todo.
    * Alta sobrecarga de GC.
    * Tem de utilizar legado do Spark 1.x APIs.

## <a name="use-optimal-data-format"></a>Utilize o formato de dados ideal

O Spark suporta muitos formatos, como csv, json, xml, parquet, orc e avro. Spark pode ser estendido para suportar muitos formatos mais com origens de dados externas - para obter mais informações, consulte [pacotes de Apache Spark](https://spark-packages.org).

O melhor formato para o desempenho é parquet com *compressão snappy*, que é o padrão no Spark 2.x. Parquet armazena dados no formato colunar e está altamente otimizado no Spark.

## <a name="select-default-storage"></a>Selecione o armazenamento predefinido

Quando cria um novo cluster do Spark, terá a opção de selecionar o armazenamento de Blobs do Azure ou de armazenamento do Azure Data Lake como armazenamento predefinido do seu cluster. Ambas as opções dão-lhe o benefício de armazenamento de longa duração para clusters transitórios, pelo que os seus dados não são automaticamente eliminados quando elimina o cluster. Pode recriar um cluster transitório e continuar a aceder aos seus dados.

| Tipo de Store | Sistema de Ficheiros | Velocidade | Transitório | Casos de Utilização |
| --- | --- | --- | --- | --- |
| Armazenamento de Blobs do Azure | **wasb[s]:**//url/ | **Standard** | Sim | Cluster transitório |
| Azure Data Lake Storage Gen 2| **abfs[s]:**//url/ | **Mais rápido** | Sim | Cluster transitório |
| Armazenamento do Azure Data Lake Ger 1| **adl:**//url/ | **Mais rápido** | Sim | Cluster transitório |
| Local HDFS | **hdfs:**//url/ | **O mais rápido** | Não | Cluster do Interactive 24x7 |

## <a name="use-the-cache"></a>Utilizar a cache

Spark fornece seus próprios mecanismos de colocação em cache nativos, que podem ser utilizados por meio de métodos diferentes, tal como `.persist()`, `.cache()`, e `CACHE TABLE`. Esta cache nativo é eficaz com pequenos conjuntos de dados, bem como em pipelines ETL em que precisa de resultados intermediários de cache. No entanto, a cache nativo do Spark atualmente não funciona bem com a criação de partições, uma vez que uma tabela em cache não mantém os dados de criação de partições. É uma técnica de colocação em cache mais genérica e confiável *colocação em cache de camada de armazenamento*.

* Spark nativo, colocação em cache (não recomendado)
    * Vale a pequenos conjuntos de dados.
    * Funciona não com a criação de partições, que podem ser alterados em futuras versões do Spark.

* Armazenamento ao nível de colocação em cache (recomendado)
    * Pode ser implementada usando [Alluxio](https://www.alluxio.org/).
    * Utiliza na memória e SSD colocação em cache.

* HDFS local (recomendado)
    * `hdfs://mycluster` caminho.
    * Utiliza a colocação em cache de SSD.
    * Dados armazenados em cache serão perdidos quando eliminar o cluster, exigir uma recompilação de cache.

## <a name="use-memory-efficiently"></a>Utilizar a memória com eficiência

Spark funciona ao colocar dados na memória, para que o gerenciamento de recursos de memória é um aspecto fundamental de otimizar a execução de tarefas do Spark.  Há várias técnicas que pode aplicar a usar a memória do seu cluster com eficiência.

* Prefere partições de dados menores e de conta para o tamanho dos dados, tipos e a distribuição na sua estratégia de particionamento.
* Considere o mais recente, mais eficiente [serialização de dados de Kryo](https://github.com/EsotericSoftware/kryo), em vez da serialização de Java padrão.
* Prefiro a usar o YARN, como ele separa `spark-submit` pelo batch.
* Monitorizar e otimizar as definições de configuração de Spark.

Para referência, a estrutura de memória do Spark e alguns parâmetros de memória do executor principais são mostrados na imagem seguinte.

### <a name="spark-memory-considerations"></a>Considerações de memória do Spark

Se estiver a utilizar [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html), em seguida, o YARN controla a soma de máxima de memória utilizada por todos os contentores em cada nó do Spark.  O diagrama seguinte mostra os objetos principais e suas relações.

![Gerenciamento de memória do Spark YARN](./media/apache-spark-perf/yarn-spark-memory.png)

Para lidar com mensagens 'sem memória", tente:

* Reveja Shuffles de gestão do DAG. Reduzir ao lado do mapa reducting, previamente de partição (ou colocar em Buckets) da origem de dados, maximizar shuffles únicos e reduzir a quantidade de dados enviados.
* Prefira `ReduceByKey` com o respetivo limite de memória fixo para `GroupByKey`, que fornece as agregações, janelas e outras funções, mas tem o limite de memória não vinculado de ann.
* Prefira `TreeReduce`, que faz mais de trabalho o executor ou partições, para `Reduce`, que faz todo o trabalho no driver.
* Tire partido DataFrames em vez dos objetos RDD de nível inferior.
* Crie ComplexTypes que encapsulam ações, como "Top N", várias agregações ou operações de janelas.

## <a name="optimize-data-serialization"></a>Otimizar a serialização de dados

Tarefas do Spark são distribuídas, para que a serialização de dados apropriado é importante para o melhor desempenho.  Existem duas opções de serialização para o Spark:

* A serialização do Java é a predefinição.
* Serialização de Kryo é um formato mais recente e pode resultar em mais rápido e mais compactar a serialização de Java.  Kryo requer que se registrar as classes em seu programa, e ele ainda não suporta todos os tipos serializáveis.

## <a name="use-bucketing"></a>Utilizar bucketing

Bucketing é semelhante à criação de partições de dados, mas cada registo pode conter um conjunto de valores de coluna em vez de apenas um. Assemelhar-se à funciona bem para a criação de partições no grande (em milhões ou mais) número de valores, tais como identificadores de produto. Um registo é determinado pelo hash da chave de registo da linha. Tabelas agrupadas oferecem otimizações exclusivas, porque eles armazenam metadados sobre como eles foram registados e ordenados.

Algumas funcionalidades assemelhar-se à avançadas são:

* Com base no meta-informações assemelhar-se à Otimização da consulta.
* Agregações otimizadas.
* Associações otimizadas.

Pode usar a criação de partições e bucketing ao mesmo tempo.

## <a name="optimize-joins-and-shuffles"></a>Otimizar as associações e shuffles

Se tiver tarefas lentas numa junção ou Shuffle, a causa é provavelmente *distorção de dados*, que é a assimetria nos seus dados de tarefa. Por exemplo, uma tarefa de mapa pode demorar a 20 segundos, mas executar um trabalho em que os dados é associado a um ou combinadas de forma aleatória demora horas.   Para corrigir a distorção de dados, deve salt a chave completa ou utilizar um *isolado salt* para apenas um subconjunto de chaves.  Se estiver a utilizar um salt isolado, deve filtrar ainda mais para isolar o subconjunto de Salt chaves em associações de mapa. Outra opção é uma coluna de bucket de introduzir e pré-agregar nos registos em primeiro lugar.

Outro fator que faz com que as associações lentas pode ser o tipo de associação. Por predefinição, do Spark utiliza o `SortMerge` tipo de associação. Este tipo de associação é mais adequado para grandes conjuntos de dados, mas caso contrário, é dispendioso computacionalmente porque ele deve classificar primeiro as laterais esquerda e direita de dados antes da intercalação-los.

A `Broadcast` associação seja mais adequada para conjuntos de dados mais pequenos, ou em que um lado da associação é muito menor do que o outro lado. Este tipo de associação difunde um lado para todos os executores e, então, em geral, exige mais memória para difusões.

Pode alterar o tipo de associação na sua configuração através da definição `spark.sql.autoBroadcastJoinThreshold`, ou pode definir uma sugestão de associação usando as APIs de pacote de dados (`dataframe.join(broadcast(df2))`).

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

Se estiver a utilizar tabelas agrupadas, então tem um terceiro tipo, de associação a `Merge` associação. Um conjunto de dados previamente particionado e previamente classificado corretamente irá ignorar a fase de ordenação Caro de um `SortMerge` associação.

A ordem das junções é importante, especialmente em consultas mais complexas. Comece com a mais seletiva associa. Além disso, mova as associações que aumentam o número de linhas depois das agregações sempre que possível.

Para gerir o paralelismo, especificamente no caso de associações Cartesianos, pode adicionar estruturas aninhadas, janelas e talvez ignorar um ou mais passos na sua tarefa do Spark.

## <a name="customize-cluster-configuration"></a>Personalizar a configuração de cluster

Dependendo da sua carga de trabalho de cluster do Spark, pode determinar que uma configuração de Spark não predefinidas resultaria em obter mais informações com a otimização de execução de tarefa do Spark.  Realize testes com cargas de trabalho de exemplo para validar as configurações de cluster não-padrão de benchmark.

Aqui estão alguns parâmetros comuns, que pode ajustar:

* `--num-executors` Define o número apropriado de executor.
* `--executor-cores` Define o número de núcleos para cada executor. Normalmente, deve ter executores middle-sized, como outros processos consumam algumas da memória disponível.
* `--executor-memory` Define o tamanho da memória para cada executor, que controla o tamanho de heap no YARN. Deve deixar alguma memória para a sobrecarga de execução.

### <a name="select-the-correct-executor-size"></a>Selecione o tamanho de executor correto

Ao decidir a configuração de executor, considere a sobrecarga de (GC coleta) de lixo do Java.

* Fatores para reduzir o tamanho de executor:
    1. Reduza o tamanho do heap abaixo 32 GB para manter o GC sobrecarga < 10%.
    2. Reduza o número de núcleos para manter o GC sobrecarga < 10%.

* Fatores para aumentar o tamanho de executor:
    1. Reduza a comunicação sobrecarga entre executores.
    2. Reduzir o número de conexões abertas entre executores (N2) em clusters maiores (> 100 executores).
    3. Aumente o tamanho de heap para se prepararem para tarefas de memória intensiva.
    4. Opcional: Reduza a sobrecarga de memória por executor.
    5. Opcional: Aumente a utilização e simultaneidade, sobre-subscrever a CPU.

Como um princípio prático geral ao selecionar o tamanho de executor:
    
1. Comece com 30 GB por executor e distribuir os núcleos de máquina disponíveis.
2. Aumente o número de núcleos de executor para clusters maiores (> 100 executores).
3. Aumentar ou diminuir o tamanhos com base na versão de avaliação de execuções e nos fatores anteriores, como a sobrecarga de GC.

Ao executar consultas em simultâneo, considere o seguinte:

1. Comece com 30 GB por todos os núcleos de máquina e do executor.
2. Criar Spark paralelo vários aplicativos ao sobre-subscrever a CPU (aproximadamente 30% de melhoria de latência).
3. Distribua consultas entre aplicativos em paralelo.
4. Aumentar ou diminuir o tamanhos com base na versão de avaliação de execuções e nos fatores anteriores, como a sobrecarga de GC.

Monitorize o desempenho das consultas para valores atípicos ou outros problemas de desempenho, ao olhar para a vista de linha cronológica, gráfico SQL, estatísticas de tarefas e assim por diante. Por vezes, uma ou algumas dos executores estão mais lentas do que os outros e tarefas demoram muito mais tempo para executar. Isso acontece com freqüência em clusters maiores (> 30 nós). Neste caso, divida o trabalho num grande número de tarefas, para compensar o agendador de tarefas lentas. Por exemplo, ter, pelo menos, duas vezes mais tarefas, como o número de núcleos de executor no aplicativo. Também pode ativar a execução especulativa de tarefas com `conf: spark.speculation = true`.

## <a name="optimize-job-execution"></a>Otimizar a execução da tarefa

* Conforme necessário, por exemplo, se utilizar os dados duas vezes, em seguida, colocar em cache-lo em cache.
* Variáveis de difusão para todos os executores. As variáveis são serializadas apenas uma vez, resultando em pesquisas mais rápidas.
* Utilize o pool de threads no driver, o que resulta numa operação mais rápida para muitas das tarefas.

Monitorize os seus trabalhos em execução regularmente para problemas de desempenho. Se precisar de mais informações sobre certos problemas, considere um do ferramentas de perfis de desempenho seguinte:

* [Ferramenta de PAL Intel](https://github.com/intel-hadoop/PAT) monitoriza a CPU, armazenamento e utilização de largura de banda de rede.
* [Oracle Java 8 controlo da missão](https://www.oracle.com/technetwork/java/javaseproducts/mission-control/java-mission-control-1998576.html) perfis de código do Spark e do executor.

Chave de desempenho de consulta do Spark 2.x é o motor de Tungsten, que depende de geração de código de fase de todo. Em alguns casos, a geração de código de fase de inteiro pode ser desabilitada. Por exemplo, se usar um tipo não mutáveis (`string`) a expressão de agregação `SortAggregate` é apresentado em vez de `HashAggregate`. Por exemplo, para um melhor desempenho, experimente o seguinte e, em seguida, volte a ativar a geração de código:

```sql
MAX(AMOUNT) -> MAX(cast(AMOUNT as DOUBLE))
```

## <a name="next-steps"></a>Passos Seguintes

* [Depurar tarefas do Apache Spark em execução no Azure HDInsight](apache-spark-job-debugging.md)
* [Gerir os recursos de um cluster do Apache Spark no HDInsight](apache-spark-resource-manager.md)
* [Utilize a API de REST do Apache Spark para submeter tarefas remotas para um cluster do Apache Spark](apache-spark-livy-rest-interface.md)
* [Otimização do Apache Spark](https://spark.apache.org/docs/latest/tuning.html)
* [Como a otimização, na verdade, do Apache Spark tarefas por isso, eles funcionam](https://www.slideshare.net/ilganeli/how-to-actually-tune-your-spark-jobs-so-they-work)
* [Serialização de Kryo](https://github.com/EsotericSoftware/kryo)
