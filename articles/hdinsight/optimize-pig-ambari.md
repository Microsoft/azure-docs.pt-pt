---
title: Otimizar o Porco Apache com Apache Ambari em Azure HDInsight
description: Use a uI web Apache Ambari para configurar e otimizar o Porco Apache.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 05/04/2020
ms.openlocfilehash: d6f20780ccd90d83631ce07411820fb8c9280c3e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86084091"
---
# <a name="optimize-apache-pig-with-apache-ambari-in-azure-hdinsight"></a>Otimizar o Porco Apache com Apache Ambari em Azure HDInsight

Apache Ambari é uma interface web para gerir e monitorizar clusters HDInsight. Para uma introdução à Ambari Web UI, consulte [gerencie os clusters HDInsight utilizando o Apache Ambari Web UI](hdinsight-hadoop-manage-ambari.md).

As propriedades do Porco Apache podem ser modificadas a partir da web UI de Ambari para sintonizar consultas de porco. Modificar as propriedades do Porco de Ambari modifica diretamente as propriedades do Porco no `/etc/pig/2.4.2.0-258.0/pig.properties` ficheiro.

1. Para modificar as propriedades do porco, navegue até ao separador Pig **Configs** e, em seguida, expanda o painel **avançado de propriedades de porco.**

1. Encontre, descompras e altere o valor do imóvel que deseja modificar.

1. **Selecione Guarde** no lado superior direito da janela para guardar o novo valor. Algumas propriedades podem necessitar de um reinício de serviço.

    ![Propriedades avançadas de porco Apache](./media/optimize-pig-ambari/advanced-pig-properties.png)

> [!NOTE]  
> Quaisquer definições de nível de sessão substituem os valores de propriedade no `pig.properties` ficheiro.

## <a name="tune-execution-engine"></a>Afinar o motor de execução

Dois motores de execução estão disponíveis para executar scripts de porco: MapReduce e Tez. Tez é um motor otimizado e é muito mais rápido que mapReduce.

1. Para modificar o motor de execução, no painel **avançado de propriedades de porco,** encontre a `exectype` propriedade.

1. O valor predefinido é **MapReduce.** Mude para **Tez.**

## <a name="enable-local-mode"></a>Ativar o modo local

Semelhante à Colmeia, o modo local é usado para acelerar trabalhos com quantidades relativamente menores de dados.

1. Para ativar o modo local, `pig.auto.local.enabled` ajuste-se a **verdade**. O valor predefinido é false.

1. Os empregos com um tamanho de dados de entrada inferiores `pig.auto.local.input.maxbytes` ao valor da propriedade são considerados pequenos postos de trabalho. O valor predefinido é de 1 GB.

## <a name="copy-user-jar-cache"></a>Cache de frasco de utilizador de cópia

O porco copia os ficheiros JAR exigidos pelos UDFs a uma cache distribuída para os disponibilizar para os nós de tarefa. Estes frascos não mudam com frequência. Se ativado, a `pig.user.cache.enabled` definição permite que os frascos sejam colocados numa cache para os reutilizar para trabalhos geridos pelo mesmo utilizador. Esta definição resulta num pequeno aumento do desempenho do trabalho.

1. Para ativar, definido `pig.user.cache.enabled` para verdade. O padrão é falso.

1. Para definir o caminho base dos frascos em cache, coloque `pig.user.cache.location` no caminho base. A predefinição é `/tmp`.

## <a name="optimize-performance-with-memory-settings"></a>Otimizar o desempenho com as definições de memória

As seguintes definições de memória podem ajudar a otimizar o desempenho do script do Porco.

* `pig.cachedbag.memusage`: A quantidade de memória dada a um saco. Um saco é uma coleção de tuples. Um tuple é um conjunto ordenado de campos, e um campo é um pedaço de dados. Se os dados num saco estão para além da memória dada, é derramado para o disco. O valor predefinido é de 0,2, o que representa 20 por cento da memória disponível. Esta memória é partilhada em todos os sacos numa aplicação.

* `pig.spill.size.threshold`: Sacos maiores do que este limiar de tamanho de derrame (em bytes) são derramados em disco. O valor predefinido é de 5 MB.

## <a name="compress-temporary-files"></a>Comprimir ficheiros temporários

O porco gera ficheiros temporários durante a execução do trabalho. Comprimir os ficheiros temporários resulta num aumento de desempenho ao ler ou escrever ficheiros para o disco. As seguintes definições podem ser usadas para comprimir ficheiros temporários.

* `pig.tmpfilecompression`: Quando verdadeiro, permite a compressão temporária do ficheiro. O valor predefinido é false.

* `pig.tmpfilecompression.codec`: O códice de compressão para comprimir os ficheiros temporários. Os codecs de compressão recomendados são LZO e Snappy para uma utilização mais baixa do CPU.

## <a name="enable-split-combining"></a>Ativar a combinação de divisão

Quando ativados, os pequenos ficheiros são combinados para menos tarefas de mapa. Esta definição melhora a eficiência dos empregos com muitos ficheiros pequenos. Para ativar, definido `pig.noSplitCombination` para verdade. O valor predefinido é false.

## <a name="tune-mappers"></a>Sintonizar mappers

O número de mappers é controlado modificando a `pig.maxCombinedSplitSize` propriedade. Esta propriedade especifica o tamanho dos dados a serem processados por uma única tarefa de mapa. O valor predefinido é o tamanho do bloco predefinido do sistema de ficheiros. Aumentar este valor resulta num menor número de tarefas de mapper.

## <a name="tune-reducers"></a>Redutores de afinação

O número de redutores é calculado com base no parâmetro `pig.exec.reducers.bytes.per.reducer` . O parâmetro especifica o número de bytes processados por redutor, por defeito de 1 GB. Para limitar o número máximo de redutores, desafine a `pig.exec.reducers.max` propriedade, por defeito 999.

## <a name="next-steps"></a>Passos seguintes

* [Gerir clusters HDInsight com a Web UI Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)
* [Otimizar clusters](./hdinsight-changing-configs-via-ambari.md)
* [Otimizar Apache HBase](./optimize-hbase-ambari.md)
* [Otimizar Apache Hive](./optimize-hive-ambari.md)
