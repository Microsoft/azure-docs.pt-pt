---
title: Otimizar o Porco Apache com Apache Ambari em Azure HDInsight
description: Utilize a UI web Apache Ambari para configurar e otimizar o Apache Pig.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 469019eb1e90654d1953156337593d5de99b46c0
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82796684"
---
# <a name="optimize-apache-pig-with-apache-ambari-in-azure-hdinsight"></a>Otimizar o Porco Apache com Apache Ambari em Azure HDInsight

Apache Ambari é uma interface web para gerir e monitorizar os clusters HDInsight. Para uma introdução ao Ambari Web UI, consulte [Os clusters Manage HDInsight utilizando o Apache Ambari Web UI](hdinsight-hadoop-manage-ambari.md).

As propriedades do Apache Pig podem ser modificadas a partir da UI web ambari para afinar consultas de porco. Modificar as propriedades do porco de Ambari modifica `/etc/pig/2.4.2.0-258.0/pig.properties` diretamente as propriedades do Porco no ficheiro.

1. Para modificar as propriedades do porco, navegue até ao separador Pig **Configs** e, em seguida, expanda o painel **avançado de propriedades de porco.**

1. Encontre, não comente e altere o valor do imóvel que pretende modificar.

1. Selecione **Guardar** no lado superior direito da janela para economizar o novo valor. Algumas propriedades podem necessitar de um reinício do serviço.

    ![Propriedades avançadas de porco apache](./media/optimize-pig-ambari/advanced-pig-properties.png)

> [!NOTE]  
> Quaisquer configurações de nível `pig.properties` de sessão sobrepõem os valores de propriedade no ficheiro.

## <a name="tune-execution-engine"></a>Motor de execução de melodia

Dois motores de execução estão disponíveis para executar scripts de porco: MapReduce e Tez. Tez é um motor otimizado e é muito mais rápido que mapReduce.

1. Para modificar o motor de execução, no painel `exectype`avançado de propriedades de **porco,** encontre a propriedade .

1. O valor padrão é **MapReduce**. Mude para **Tez.**

## <a name="enable-local-mode"></a>Ativar o modo local

Semelhante à Hive, o modo local é usado para acelerar os trabalhos com quantidades relativamente menores de dados.

1. Para ativar o `pig.auto.local.enabled` modo local, ajuste-se a **verdade.** O valor predefinido é false.

1. Os empregos com uma dimensão de dados de entrada inferior ao valor da `pig.auto.local.input.maxbytes` propriedade são considerados pequenos empregos. O valor padrão é de 1 GB.

## <a name="copy-user-jar-cache"></a>Copiar cache de jarro de utilizador

O porco copia os ficheiros JAR exigidos pelos UDFs a uma cache distribuída para os disponibilizar para os nódosos de tarefa. Estes frascos não mudam com frequência. Se ativada, `pig.user.cache.enabled` a definição permite que os frascos sejam colocados numa cache para os reutilizar em trabalhos geridos pelo mesmo utilizador. Esta definição resulta num pequeno aumento do desempenho do trabalho.

1. Para permitir, `pig.user.cache.enabled` definido para verdade. O padrão é falso.

1. Para definir o caminho base dos frascos em cache, coloque-o `pig.user.cache.location` no caminho base. A predefinição é `/tmp`.

## <a name="optimize-performance-with-memory-settings"></a>Otimizar o desempenho com definições de memória

As seguintes definições de memória podem ajudar a otimizar o desempenho do script do Porco.

* `pig.cachedbag.memusage`: A quantidade de memória dada a um saco. Um saco é uma coleção de tuples. Um tuple é um conjunto ordenado de campos, e um campo é um pedaço de dados. Se os dados de um saco estão para além da memória dada, é derramado para o disco. O valor padrão é de 0,2, o que representa 20% da memória disponível. Esta memória é partilhada em todos os sacos numa aplicação.

* `pig.spill.size.threshold`: Os sacos maiores do que este limiar de tamanho de derrame (in bytes) são derramados em disco. O valor padrão é de 5 MB.

## <a name="compress-temporary-files"></a>Comprimir ficheiros temporários

Porco gera ficheiros temporários durante a execução do emprego. A compressão dos ficheiros temporários resulta num aumento de desempenho ao ler ou escrever ficheiros para disco. As seguintes definições podem ser utilizadas para comprimir ficheiros temporários.

* `pig.tmpfilecompression`: Quando for verdade, permite a compressão temporária dos ficheiros. O valor predefinido é false.

* `pig.tmpfilecompression.codec`: O códice de compressão a utilizar para comprimir os ficheiros temporários. Os códigos de compressão recomendados são LZO e Snappy para uma utilização mais baixa do CPU.

## <a name="enable-split-combining"></a>Ativar a combinação de divisão

Quando ativados, pequenos ficheiros são combinados para menos tarefas de mapa. Esta definição melhora a eficiência dos postos de trabalho com muitos pequenos ficheiros. Para permitir, `pig.noSplitCombination` definido para verdade. O valor predefinido é false.

## <a name="tune-mappers"></a>Mappers de melodia

O número de mappers é controlado `pig.maxCombinedSplitSize`modificando a propriedade. Esta propriedade especifica o tamanho dos dados a serem processados por uma única tarefa de mapa. O valor predefinido é o tamanho do bloco padrão do sistema de ficheiros. Aumentar este valor resulta num número mais baixo de tarefas mapeadas.

## <a name="tune-reducers"></a>Redutores de melodia

O número de redutores é calculado `pig.exec.reducers.bytes.per.reducer`com base no parâmetro . O parâmetro especifica o número de bytes processados por redutor, por predefinição de 1 GB. Para limitar o número máximo de `pig.exec.reducers.max` redutores, defino a propriedade, por padrão 999.

## <a name="next-steps"></a>Passos seguintes

* [Gerir clusters HDInsight com a UI web Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)
* [Otimizar aglomerados](./hdinsight-changing-configs-via-ambari.md)
* [Otimizar Apache HBase](./optimize-hbase-ambari.md)
* [Otimizar a Colmeia Apache](./optimize-hive-ambari.md)
