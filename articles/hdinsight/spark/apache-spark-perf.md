---
title: Otimize os trabalhos da Spark para o desempenho - Azure HDInsight
description: Mostre estratégias comuns para o melhor desempenho dos clusters Apache Spark em Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/12/2020
ms.openlocfilehash: 3d8f4a28961be7e0ece517e00026d9711d8f67e9
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198876"
---
# <a name="optimize-apache-spark-jobs-in-hdinsight"></a>Otimizar os empregos da Apache Spark no HDInsight

Aprenda a otimizar a configuração do cluster [Apache Spark](https://spark.apache.org/) para a sua carga de trabalho específica.  O desafio mais comum é a pressão da memória, devido a configurações impróprias (executores particularmente de tamanho errado), operações de longa duração e tarefas que resultam em operações cartesianas. Pode acelerar os trabalhos com o cache adequado, e permitindo [distorções de dados](#optimize-joins-and-shuffles). Para obter o melhor desempenho, monitorize e reveja as execuções de emprego de Spark de longo prazo e que consome recursos. Para obter informações sobre como começar com Apache Spark no HDInsight, consulte [Create Apache Spark cluster usando o portal Azure](apache-spark-jupyter-spark-sql-use-portal.md).

As seguintes secções descrevem otimizações e recomendações comuns do trabalho da Spark.

## <a name="choose-the-data-abstraction"></a>Escolha a abstração de dados

As versões anteriores da Spark usam RDDs para dados abstratos, Spark 1.3 e 1.6 introduziram DataFrames e DataSets, respectivamente. Considere os seguintes méritos relativos:

* **DataFrames**
    * A melhor escolha na maioria das situações.
    * Fornece otimização de consulta através do Catalyst.
    * Geração de códigos em palco inteiro.
    * Acesso direto à memória.
    * Baixa recolha de lixo (GC) por cima.
    * Não tão amigável para o desenvolvedor como DataSets, uma vez que não existem verificações de tempo de compilação ou programação de objetos de domínio.
* **Conjuntos de Dados**
    * Bom em oleodutos ETL complexos onde o impacto de desempenho é aceitável.
    * Não é bom em agregações onde o impacto do desempenho pode ser considerável.
    * Fornece otimização de consulta através do Catalyst.
    * Amigável para o desenvolvedor, fornecendo programação de objetos de domínio e compilação de verificações de tempo.
    * Adiciona a sobrecarga de serialização/desserialização.
    * Altas despesas de GC.
    * Quebra a geração de códigos em palco inteiro.
* **RdDs**
    * Não precisa de usar RDDs, a não ser que precise de construir um novo RDD personalizado.
    * Sem otimização de consultas através do Catalyst.
    * Nenhuma geração de códigos em estágio inteiro.
    * Altas despesas de GC.
    * Deve usar AFIs legado Spark 1.x.

## <a name="use-optimal-data-format"></a>Utilize o formato de dados ideal

A faísca suporta muitos formatos, tais como csv, json, xml, parquet, orc e avro. A faísca pode ser estendida para suportar muitos mais formatos com fontes de dados externas - para mais informações, consulte [os pacotes Apache Spark](https://spark-packages.org).

O melhor formato para o desempenho é o parquet com *compressão snappy*, que é o padrão em Spark 2.x. Parquet armazena dados em formato colunaar, e é altamente otimizado em Spark.

## <a name="select-default-storage"></a>Selecione armazenamento por defeito

Quando criar um novo cluster Spark, pode selecionar o Armazenamento de Blob Azure ou o Armazenamento do Lago De dados Azure como armazenamento padrão do seu cluster. Ambas as opções dão-lhe o benefício do armazenamento a longo prazo para clusters transitórios, para que os seus dados não sejam automaticamente eliminados quando apaga o seu cluster. Pode recriar um cluster transitório e ainda aceder aos seus dados.

| Tipo de loja | Sistema de Ficheiros | Velocidade | Transitória | Casos de Utilização |
| --- | --- | --- | --- | --- |
| Armazenamento de Blobs do Azure | **wasb:** //url/ | **Standard** | Sim | Aglomerado transitório |
| Armazenamento De Blob Azure (seguro) | **wasbs:** //url/ | **Standard** | Sim | Aglomerado transitório |
| Azure Data Lake Storage Gen2| **abfs:** //url/ | **Mais rápido** | Sim | Aglomerado transitório |
| Armazenamento de lagos azure data gen 1| **adl:** //url/ | **Mais rápido** | Sim | Aglomerado transitório |
| HDFS locais | **Hdfs:** //url/ | **Mais rápido** | Não | Cluster interativo 24/7 |

Para obter uma descrição completa das opções de armazenamento disponíveis para os clusters HDInsight, consulte Compare opções de [armazenamento para utilização com clusters Azure HDInsight](../hdinsight-hadoop-compare-storage-options.md).

## <a name="use-the-cache"></a>Use a cache

A Faísca fornece os seus próprios mecanismos nativos de cache, que podem ser usados através de diferentes métodos, como `.persist()`, `.cache()`e `CACHE TABLE`. Este cache nativo é eficaz com pequenos conjuntos de dados, bem como em oleodutos ETL onde você precisa cache resultados intermédios. No entanto, o caching nativo spark atualmente não funciona bem com a divisão, uma vez que uma mesa em cache não mantém os dados de divisão. Uma técnica de cache mais genérica e fiável é o cache da camada de *armazenamento.*

* Caching de faísca nativa (não recomendado)
    * Bom para pequenos conjuntos de dados.
    * Não funciona com a partilha, o que pode mudar nos futuros lançamentos da Spark.

* Cacheching nível de armazenamento (recomendado)
    * Pode ser implementado no HDInsight utilizando a função [IO Cache.](apache-spark-improve-performance-iocache.md)
    * Usa na memória e caching SSD.

* HDFS local (recomendado)
    * `hdfs://mycluster` caminho.
    * Usa o cache de SSD.
    * Os dados em cache perder-se-ão quando eliminar o cluster, exigindo uma reconstrução em cache.

## <a name="use-memory-efficiently"></a>Use a memória de forma eficiente

A Spark opera colocando dados na memória, por isso gerir os recursos de memória é um aspeto fundamental para otimizar a execução de trabalhos spark.  Existem várias técnicas que pode aplicar para usar a memória do seu cluster de forma eficiente.

* Prefira divisórias de dados mais pequenas e contabilize o tamanho, tipos e distribuição de dados na sua estratégia de partilha.
* Considere a mais recente e eficiente serialização de [dados da Kryo,](https://github.com/EsotericSoftware/kryo)em vez da serialização padrão de Java.
* Prefira usar o ARN, uma vez que separa `spark-submit` por lote.
* Monitore e sintonize as definições de configuração spark.

Para a sua referência, a estrutura da memória Spark e alguns parâmetros de memória do executor chave são mostrados na imagem seguinte.

### <a name="spark-memory-considerations"></a>Considerações de memória de faísca

Se estiver a utilizar [o YARN Apache Hadoop,](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html)então o Arn yARN controla a soma máxima de memória utilizada por todos os recipientes em cada nó de Faísca.  O diagrama que se segue mostra os objetos-chave e as suas relações.

![Gestão da memória de faísca de fios](./media/apache-spark-perf/apache-yarn-spark-memory.png)

Para endereçar mensagens "fora de memória", tente:

* Reveja a GEStão DO DAG Shuffles. Reduza por dados de origem redutor do lado do mapa, pré-partição (ou bucketize), maximize as baralhadas individuais e reduza a quantidade de dados enviados.
* Prefira `ReduceByKey` com o seu limite de memória fixo para `GroupByKey`, que proporciona agregações, janelas e outras funções, mas tem um limite de memória ilimitado.
* Prefira `TreeReduce`, que trabalha mais nos executores ou divisórias, para `Reduce`, que faz todo o trabalho no motorista.
* Alavancar os DataFrames em vez dos objetos RDD de nível inferior.
* Criar Tipos Complexos que encapsulam ações, como "Top N", várias agregações ou operações de janela.

Para obter passos adicionais de resolução de problemas, consulte [exceções OutOfMemoryError para Apache Spark em Azure HDInsight](apache-spark-troubleshoot-outofmemory.md).

## <a name="optimize-data-serialization"></a>Otimizar a serialização de dados

Os trabalhos de faísca são distribuídos, por isso a serialização adequada dos dados é importante para o melhor desempenho.  Existem duas opções de serialização para a Spark:

* A serialização de Java é o padrão.
* A serialização da Kryo é um formato mais recente e pode resultar numa serialização mais rápida e compacta do que Java.  A Kryo exige que registe as aulas no seu programa, e ainda não suporta todos os tipos serlizáveis.

## <a name="use-bucketing"></a>Use baldes

Baldes é semelhante à divisão de dados, mas cada balde pode conter um conjunto de valores de coluna em vez de apenas um. O balde funciona bem para a divisão em grandes números (nos milhões ou mais) de valores, tais como identificadores de produto. Um balde é determinado por hashing a chave do balde da fila. As mesas em balde oferecem otimizações únicas porque armazenam metadados sobre como foram baldeados e classificados.

Algumas características avançadas de balde são:

* Otimização de consulta com base na meta-informação de balde.
* Agregações otimizadas.
* Juntas otimizadas.

Pode usar divisórias e baldes ao mesmo tempo.

## <a name="optimize-joins-and-shuffles"></a>Otimizar juntas e baralhar

Se tiver empregos lentos numa Join ou Shuffle, a causa é provavelmente *de sujeira*de dados , que é assimetria nos dados do seu trabalho. Por exemplo, um trabalho de mapa pode levar 20 segundos, mas executar um trabalho onde os dados são unidos ou baralhados leva horas. Para corrigir o enviesamento dos dados, deve salgar toda a chave ou utilizar um *sal isolado* para apenas um subconjunto de teclas. Se estiver a utilizar um sal isolado, deve filtrar ainda mais para isolar o seu subconjunto de chaves salgadas no mapa. Outra opção é introduzir uma coluna de balde e pré-agregar em baldes primeiro.

Outro fator que causa a juntas lentas pode ser o tipo de união. Por predefinição, a Spark utiliza o tipo de `SortMerge` de juntar. Este tipo de adesão é mais adequado para grandes conjuntos de dados, mas é computacionalmente caro porque deve primeiro classificar os lados esquerdo e direito dos dados antes de os fundir.

Uma `Broadcast` juntar é mais adequada para conjuntos de dados menores, ou onde um lado da junta é muito menor do que o outro lado. Este tipo de união transmite um lado para todos os executores, e assim requer mais memória para transmissões em geral.

Pode alterar o tipo de união na sua configuração definindo `spark.sql.autoBroadcastJoinThreshold`, ou pode definir uma dica de união utilizando as APIs dataFrame (`dataframe.join(broadcast(df2))`).

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

Se está a usar mesas baldeadas, então tem um terceiro tipo de adesão, o `Merge` juntar-se. Um conjunto de dados pré-dividido e pré-ordenado salta a fase de classificação cara de um `SortMerge` aderir.

A ordem de adesão importa, particularmente em consultas mais complexas. Comece com as juntas mais seletivas. Além disso, move-se que aumenta o número de linhas após agregações quando possível.

Para gerir o paralelismo para uniões cartesianas, pode adicionar estruturas aninhadas, janelas e talvez saltar um ou mais passos no seu Spark Job.

## <a name="customize-cluster-configuration"></a>Personalizar a configuração do cluster

Dependendo da carga de trabalho do cluster Spark, poderá determinar que uma configuração spark não predefinida resultaria numa execução de trabalho spark mais otimizada.  Efetue testes de benchmark com cargas de trabalho da amostra para validar quaisquer configurações de cluster não predefinidas.

Aqui estão alguns parâmetros comuns que pode ajustar:

* `--num-executors` define o número adequado de executores.
* `--executor-cores` define o número de núcleos para cada executor. Normalmente deve ter executores de tamanho médio, uma vez que outros processos consomem parte da memória disponível.
* `--executor-memory` define o tamanho da memória para cada executor, que controla o tamanho da pilha no ARN. Deviadeixar alguma memória para execução.

### <a name="select-the-correct-executor-size"></a>Selecione o tamanho correto do executor

Ao decidir a configuração do seu executor, considere a sobrecarga da coleção de lixo Java (GC).

* Fatores para reduzir o tamanho do executor:
    1. Reduza o tamanho da pilha abaixo de 32 GB para manter a sobrecarga gc < 10%.
    2. Reduza o número de núcleos para manter a sobrecarga gc < 10%.

* Fatores para aumentar o tamanho do executor:
    1. Reduza a sobrecarga de comunicação entre executores.
    2. Reduzir o número de ligações abertas entre executores (N2) em clusters maiores (>100 executores).
    3. Aumente o tamanho da pilha para acomodar tarefas intensivas de memória.
    4. Opcional: Reduzir a sobrecarga da memória por executor.
    5. Opcional: Aumentar a utilização e a conmoeda através da subscrição excessiva do CPU.

Regra geral ao selecionar o tamanho do executor:

1. Comece com 30 GB por executor e distribua os núcleos disponíveis da máquina.
2. Aumentar o número de núcleos de executores para clusters maiores (> 100 executores).
3. Modificar o tamanho com base tanto em ensaios como nos fatores anteriores, tais como a sobrecarga de GC.

Ao executar consultas simultâneas, considere o seguinte:

1. Comece com 30 GB por executor e todos os núcleos de máquinas.
2. Crie múltiplas aplicações paralelas spark ao subscrever CPU (cerca de 30% de melhoria da latência).
3. Distribua consultas através de aplicações paralelas.
4. Modificar o tamanho com base tanto em ensaios como nos fatores anteriores, tais como a sobrecarga de GC.

Para obter mais informações sobre a utilização de Ambari para configurar executores, consulte [as definições de Apache Spark - Executores de faíscas](apache-spark-settings.md#configuring-spark-executors).

Monitorize o seu desempenho de consulta para outliers ou outros problemas de desempenho, olhando para a vista da linha do tempo, gráfico SQL, estatísticas de trabalho, e assim por diante. Para obter informações sobre depuração de trabalhos spark usando O Fio e o servidor Spark History, consulte [os trabalhos de Debug Apache Spark em execução no Azure HDInsight](apache-spark-job-debugging.md). Para obter dicas sobre a utilização do Servidor de Linha do Tempo Do Fio YARN, consulte os registos de [aplicações do Access Apache Hadoop YARN](../hdinsight-hadoop-access-yarn-app-logs-linux.md).

Às vezes, um ou alguns dos executores são mais lentos que os outros, e as tarefas demoram muito mais tempo a executar. Isto acontece frequentemente em clusters maiores (> 30 nós). Neste caso, divida o trabalho num maior número de tarefas para que o programador possa compensar tarefas lentas. Por exemplo, ter pelo menos o dobro das tarefas do executor na aplicação. Também pode permitir a execução especulativa de tarefas com `conf: spark.speculation = true`.

## <a name="optimize-job-execution"></a>Otimizar a execução de emprego

* Cache conforme necessário, por exemplo, se utilizar os dados duas vezes, em seguida, cache-os.
* Transmita variáveis a todos os executores. As variáveis só são serializadas uma vez, resultando em procurações mais rápidas.
* Utilize a piscina de rosca no condutor, o que resulta numa operação mais rápida para muitas tarefas.

Monitorize regularmente os seus trabalhos de funcionamento para problemas de desempenho. Se precisar de mais informações sobre certas questões, considere uma das seguintes ferramentas de perfis de desempenho:

* [A Ferramenta Intel PAL](https://github.com/intel-hadoop/PAT) monitoriza a utilização de CPU, armazenamento e largura de banda da rede.
* [Oracle Java 8 Perfis de Controlo](https://www.oracle.com/technetwork/java/javaseproducts/mission-control/java-mission-control-1998576.html) de Missão Spark e código executor.

A chave para o desempenho da consulta Spark 2.x é o motor Tungsten, que depende da geração de códigos em toda a fase. Em alguns casos, a geração de códigos em fase inteira pode ser desativada. Por exemplo, se utilizar um tipo não mutável (`string`) na expressão de agregação, aparece `SortAggregate` em vez de `HashAggregate`. Por exemplo, para um melhor desempenho, tente o seguinte e, em seguida, reativar a geração de códigos:

```sql
MAX(AMOUNT) -> MAX(cast(AMOUNT as DOUBLE))
```

## <a name="next-steps"></a>Passos seguintes

* [Depurar trabalhos do Apache Spark em execução no Azure HDInsight](apache-spark-job-debugging.md)
* [Gerir recursos para um cluster Apache Spark no HDInsight](apache-spark-resource-manager.md)
* [Use a Apache Spark REST API para submeter trabalhos remotos a um cluster Apache Spark](apache-spark-livy-rest-interface.md)
* [Afinação da Faísca Apache](https://spark.apache.org/docs/latest/tuning.html)
* [Como realmente afinar os seus trabalhos de faísca Apache para que funcionem](https://www.slideshare.net/ilganeli/how-to-actually-tune-your-spark-jobs-so-they-work)
* [Kryo Serialization](https://github.com/EsotericSoftware/kryo)
