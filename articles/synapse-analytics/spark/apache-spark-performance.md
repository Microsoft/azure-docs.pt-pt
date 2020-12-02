---
title: Otimizar empregos de faíscas para desempenho
description: Este artigo fornece uma introdução a Apache Spark in Azure Synapse Analytics.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: b94ece73d5f9dc9b8343e45fb1f616599b9a1c1f
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96450932"
---
# <a name="optimize-apache-spark-jobs-in-azure-synapse-analytics"></a>Otimizar os empregos da Apache Spark no Azure Synapse Analytics

Aprenda a otimizar uma configuração de cluster [Apache Spark](https://spark.apache.org/) para a sua carga de trabalho particular.  O problema mais comum é a pressão da memória, devido a configurações inadequadas (em especial, executores com tamanho incorreto), operações de execução longa e tarefas que resultam em Operações cartesianas. Pode acelerar os postos de trabalho com o caching apropriado, e permitindo [distorcer os dados.](#optimize-joins-and-shuffles) Para obter o melhor desempenho, monitorize e reveja execuções de empregos de spark de longa duração e de consumo de recursos.

As seguintes secções descrevem otimizações e recomendações comuns de emprego spark.

## <a name="choose-the-data-abstraction"></a>Escolha a abstração de dados

As versões Spark anteriores usam RDDs para dados abstratos, Spark 1.3 e 1.6 introduzidos DataFrames e DataSets, respectivamente. Considere os seguintes méritos relativos:

* **DataFrames**
  * A melhor escolha na maioria das situações.
  * Fornece otimização de consulta através do Catalyst.
  * Geração de códigos em todo o estágio.
  * Acesso direto à memória.
  * Baixa recolha de lixo (GC) por cima.
  * Não tão amigável como os DataSets, uma vez que não existem verificações de tempo de compilação ou programação de objetos de domínio.
* **Conjuntos de Dados**
  * Bom em oleodutos ETL complexos onde o impacto de desempenho é aceitável.
  * Não é bom em agregações onde o impacto no desempenho pode ser considerável.
  * Fornece otimização de consulta através do Catalyst.
  * Amiga do programador, fornecendo programação de objetos de domínio e verificações de tempo de compilação.
  * Adiciona serialização/deserialização por cima.
  * Alta GC sobrecarga.
  * Quebra a geração de códigos de todo o estágio.
* **RDDs**
  * Não precisa de usar RDDs, a não ser que precise de construir um novo RDD personalizado.
  * Sem otimização de consulta através do Catalyst.
  * Nenhuma geração de código de todo o estágio.
  * Alta GC sobrecarga.
  * Deve utilizar as APIs do legado Spark 1.x.

## <a name="use-optimal-data-format"></a>Utilizar o formato de dados ideal

A faísca suporta muitos formatos, tais como csv, json, xml, parquet, orc e avro. A faísca pode ser estendida para suportar muitos mais formatos com fontes de dados externas - para mais informações, consulte [os pacotes Apache Spark](https://spark-packages.org).

O melhor formato para desempenho é o parquet com *compressão snappy,* que é o padrão em Spark 2.x. O Parquet armazena dados em formato colunar, e está altamente otimizado em Spark. Além disso, embora *a compressão snappy* possa resultar em ficheiros maiores do que a compressão gzip. Devido à natureza splittable desses ficheiros, eles vão descomprimir mais rapidamente.

## <a name="use-the-cache"></a>Utilizar a cache

A faísca fornece os seus próprios mecanismos nativos de caching, que podem ser usados através de diferentes métodos, tais `.persist()` `.cache()` como, e `CACHE TABLE` . Este cache nativo é eficaz com pequenos conjuntos de dados, bem como em oleodutos ETL onde você precisa cache resultados intermédios. No entanto, o caching nativo de Spark atualmente não funciona bem com a partição, uma vez que uma mesa em cache não mantém os dados de partição.

## <a name="use-memory-efficiently"></a>Utilizar a memória de forma eficiente

A Spark opera colocando dados na memória, por isso gerir os recursos de memória é um aspeto fundamental para otimizar a execução de empregos spark.  Existem várias técnicas que pode aplicar para usar a memória do seu cluster de forma eficiente.

* Prefere divisórias de dados mais pequenas e responsável pelo tamanho dos dados, tipos e distribuição na sua estratégia de partição.
* Considere a mais recente e eficiente [serialização de dados kryo](https://github.com/EsotericSoftware/kryo), em vez da serialização padrão de Java.
* Monitorize e afina as definições de configuração do Spark.

Para a sua referência, a estrutura da memória Spark e alguns parâmetros de memória do executor chave são mostrados na imagem seguinte.

### <a name="spark-memory-considerations"></a>Considerações de memória de faísca

Apache Spark in Azure Synapse usa YARN [Apache Hadoop YARN,](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html)YARN controla a soma máxima de memória usada por todos os recipientes em cada nó de faísca.  O diagrama que se segue mostra os objetos-chave e as suas relações.

![Gestão da memória de faíscas de YARN](./media/apache-spark-perf/apache-yarn-spark-memory.png)

Para endereçar mensagens 'fora da memória', tente:

* Reveja o DAG Management Shuffles. Reduzir por dados de origem de redução do lado do mapa, pré-partição (ou baldear), maximizar as baralhadas individuais e reduzir a quantidade de dados enviados.
* Prefere `ReduceByKey` com o seu limite de memória fixa a , que fornece `GroupByKey` agregações, janelas e outras funções, mas tem um limite de memória ilimitado.
* Prefere, `TreeReduce` que trabalha mais nos executores ou divisórias, para , que faz todo o trabalho no `Reduce` motorista.
* Alavancar dataFrames em vez dos objetos RDD de nível inferior.
* Criar ComplexTypes que englobam ações, como "Top N", várias agregações ou operações de janela.

## <a name="optimize-data-serialization"></a>Otimizar a serialização de dados

Os trabalhos de faísca são distribuídos, por isso a serialização adequada dos dados é importante para o melhor desempenho.  Existem duas opções de serialização para a Spark:

* A serialização de Java é o padrão.
* A serialização de Kryo é um formato mais recente e pode resultar numa serialização mais rápida e compacta do que a Java.  A Kryo requer que registe as aulas no seu programa, e ainda não suporta todos os tipos serializáveis.

## <a name="use-bucketing"></a>Utilizar os registos

O balde é semelhante à partição de dados, mas cada balde pode conter um conjunto de valores de coluna em vez de apenas um. O balde funciona bem para a divisão em grandes (milhões ou mais) números de valores, como identificadores de produtos. Um balde é determinado por hashing a chave do balde da linha. As mesas baldeadas oferecem otimizações únicas porque armazenam metadados sobre como foram baldes e classificados.

Algumas características avançadas de balde são:

* Otimização de consultas com base em meta-informação de balde.
* Agregações otimizadas.
* Juntas otimizadas.

Pode usar divisórias e baldes ao mesmo tempo.

## <a name="optimize-joins-and-shuffles"></a>Otimizar as associações e misturas

Se tiver empregos lentos num Join ou Shuffle, a causa é provavelmente *distorcer dados*, o que é assimetria nos dados do seu trabalho. Por exemplo, um trabalho de mapa pode demorar 20 segundos, mas executar um trabalho onde os dados são unidos ou baralhados leva horas. Para corrigir o distorcer de dados, deve salgar toda a tecla ou utilizar um *sal isolado* para apenas alguns subconjuntos de teclas. Se estiver a utilizar um sal isolado, deve filtrar ainda mais o subconjunto de teclas salgadas em juntas de mapa. Outra opção é introduzir uma coluna de balde e pré-agregar primeiro em baldes.

Outro fator que causa a junção lenta pode ser o tipo de junção. Por predefinição, a Spark utiliza o `SortMerge` tipo de junção. Este tipo de junção é mais adequado para grandes conjuntos de dados, mas é de outra forma computacionalmente caro porque deve primeiro separar os lados esquerdo e direito dos dados antes de os fundir.

Uma `Broadcast` junção é mais adequada para conjuntos de dados menores, ou onde um lado da junta é muito menor do que o outro lado. Este tipo de junção transmite um lado para todos os executores, e assim requer mais memória para transmissões em geral.

Pode alterar o tipo de junção na sua configuração definindo `spark.sql.autoBroadcastJoinThreshold` , ou pode definir uma sugestão de junção utilizando as APIs dataframe `dataframe.join(broadcast(df2))` ().

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

Se estiver a usar mesas de balde, então tem um terceiro tipo de junção, a `Merge` junção. Um conjunto de dados pré-dividido e pré-classificado corretamente irá saltar a fase de classificação cara a partir de uma `SortMerge` junção.

A ordem de junção importa, particularmente em consultas mais complexas. Comece com as juntas mais seletivas. Além disso, mova-se junta-se que aumente o número de filas após agregações, quando possível.

Para gerir o paralelismo para a cartesia, pode adicionar estruturas aninhadas, janelas e talvez saltar um ou mais passos no seu Spark Job.

### <a name="select-the-correct-executor-size"></a>Selecione o tamanho correto do executor

Ao decidir a configuração do executor, considere a recolha de lixo Java (GC) por cima.

* Fatores para reduzir o tamanho do executor:
  * Reduza o tamanho da pilha abaixo de 32 GB para manter a carga de GC < 10%.
  * Reduza o número de núcleos para manter a GC < 10%.

* Fatores para aumentar o tamanho do executor:
  * Reduzir a sobrecarga de comunicação entre os executores.
  * Reduza o número de ligações abertas entre executores (N2) em agrupamentos maiores (>100 executores).
  * Aumente o tamanho da pilha para acomodar para tarefas intensivas de memória.
  * Opcional: Reduza a memória por executor.
  * Opcional: Aumentar a utilização e a concordância por subscrição excessiva da CPU.

Regra geral do polegar ao selecionar o tamanho do executor:

* Comece com 30 GB por executor e distribua os núcleos de máquina disponíveis.
* Aumente o número de núcleos executor para agrupamentos maiores (> 100 executores).
* Modificar o tamanho com base tanto em ensaios como nos fatores anteriores, tais como sobrecargas de GC.

Ao executar consultas simultâneas, considere o seguinte:

* Comece com 30 GB por executor e todos os núcleos de máquina.
* Crie múltiplas aplicações paralelas de Spark por subscrição excessiva de CPU (melhoria de latência de cerca de 30%).
* Distribuir consultas através de aplicações paralelas.
* Modificar o tamanho com base tanto em ensaios como nos fatores anteriores, tais como sobrecargas de GC.

Monitorize o desempenho da sua consulta para problemas de desempenho ou outliers, olhando para a visão da linha do tempo, gráfico SQL, estatísticas de emprego, etc. Às vezes, um ou alguns dos executores são mais lentos do que os outros, e as tarefas demoram muito mais tempo a ser executadas. Isto acontece frequentemente em aglomerados maiores (> 30 nosdes). Neste caso, divida o trabalho num maior número de tarefas para que o programador possa compensar tarefas lentas. 

Por exemplo, ter pelo menos o dobro das tarefas que o número de núcleos executor na aplicação. Também pode permitir a execução especulativa de tarefas com `conf: spark.speculation = true` .

## <a name="optimize-job-execution"></a>Otimizar a execução de tarefas

* Cache, se necessário, por exemplo, se utilizar os dados duas vezes, em seguida, cache-os.
* Variáveis de transmissão a todos os executores. As variáveis só são serializadas uma vez, resultando em procuras mais rápidas.
* Utilize a piscina de rosca no controlador, o que resulta numa operação mais rápida para muitas tarefas.

A chave para o desempenho da consulta Spark 2.x é o motor Tungsten, que depende da geração de códigos em todo o estágio. Em alguns casos, a geração de códigos em todo o estágio pode ser desativada. 

Por exemplo, se utilizar um tipo não mutável `string` () na expressão de agregação, `SortAggregate` aparece em vez de `HashAggregate` . Por exemplo, para um melhor desempenho, experimente o seguinte e, em seguida, reeguir a geração de código:

```sql
MAX(AMOUNT) -> MAX(cast(AMOUNT as DOUBLE))
```

## <a name="next-steps"></a>Passos seguintes

- [Afinação de Faíscas Apache](https://spark.apache.org/docs/2.4.5/tuning.html)
- [Como realmente sintonizar os seus apache spark jobs para que eles funcionem](https://www.slideshare.net/ilganeli/how-to-actually-tune-your-spark-jobs-so-they-work)
- [Kryo Serialização](https://github.com/EsotericSoftware/kryo)
