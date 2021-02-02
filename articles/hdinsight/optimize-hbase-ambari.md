---
title: Otimizar Apache HBase com Apache Ambari em Azure HDInsight
description: Utilize a UI web Apache Ambari para configurar e otimizar o Apache HBase.
ms.service: hdinsight
ms.topic: how-to
ms.date: 02/01/2021
ms.openlocfilehash: 60c9916bc7d7b3b380a332f41924ee744002fd66
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/02/2021
ms.locfileid: "99428205"
---
# <a name="optimize-apache-hbase-with-apache-ambari-in-azure-hdinsight"></a>Otimizar Apache HBase com Apache Ambari em Azure HDInsight

Apache Ambari é uma interface web para gerir e monitorizar clusters HDInsight. Para uma introdução à Ambari Web UI, consulte [gerencie os clusters HDInsight utilizando o Apache Ambari Web UI](hdinsight-hadoop-manage-ambari.md).

A configuração Apache HBase é modificada a partir do separador **HBase Configs.** As seguintes secções descrevem algumas das definições de configuração importantes que afetam o desempenho da HBase.

## <a name="set-hbase_heapsize"></a>Definir HBASE_HEAPSIZE

> [!NOTE]
> Este artigo contém referências ao termo *mestre*, um termo que a Microsoft já não utiliza. Quando o termo for removido do software, vamos removê-lo deste artigo.

O tamanho da pilha HBase especifica a quantidade máxima de pilha a ser usada em megabytes por *região* e servidores *principais.* O valor predefinido é de 1.000 MB. Este valor deve ser afinado para a carga de trabalho do cluster.

1. Para modificar, navegue no painel **avançado de base hbase-env** no separador HBase **Configs** e, em seguida, encontre a `HBASE_HEAPSIZE` definição.

1. Altere o valor predefinido para 5.000 MB.

    !['Apache Ambari HBase memória heapsize'](./media/optimize-hbase-ambari/ambari-hbase-heapsize.png)

## <a name="optimize-read-heavy-workloads"></a>Otimizar cargas de trabalho pesadas de leitura

As seguintes configurações são importantes para melhorar o desempenho das cargas de trabalho pesadas de leitura.

### <a name="block-cache-size"></a>Tamanho da cache do bloco

A cache do bloco é a cache de leitura. O seu tamanho é controlado pelo `hfile.block.cache.size` parâmetro. O valor predefinido é de 0,4, o que equivale a 40% da memória total do servidor da região. Quanto maior for o tamanho da cache do bloco, mais rápido será o número de leituras aleatórias.

1. Para modificar este parâmetro, navegue no **separador Definições** no separador HBase **Configs** e, em seguida, localize **% do RegiãoServer Atribuído a Tampão de Leitura**.

    ![Tamanho do cache do bloco de memória Apache HBase](./media/optimize-hbase-ambari/hbase-block-cache-size.png)

1. Para alterar o valor, selecione o ícone **Editar.**

### <a name="memstore-size"></a>Tamanho de Memstore

Todas as edições são armazenadas no tampão de memória, chamado *Memstore.* Este tampão aumenta a quantidade total de dados que podem ser escritos para o disco numa única operação. Também acelera o acesso às edições recentes. O tamanho memstore é definido pelos seguintes dois parâmetros:

* `hbase.regionserver.global.memstore.UpperLimit`: Define a percentagem máxima do servidor regional que a Memstore combinada pode utilizar.

* `hbase.regionserver.global.memstore.LowerLimit`: Define a percentagem mínima do servidor regional que a Memstore combinada pode utilizar.

Para otimizar para leituras aleatórias, pode reduzir os limites superiores e inferiores da Memstore.

### <a name="number-of-rows-fetched-when-scanning-from-disk"></a>Número de linhas recolhidas ao digitalizar a partir do disco

A `hbase.client.scanner.caching` definição define o número de linhas lidas a partir do disco quando o `next` método é chamado num scanner.  O valor predefinido é 100. Quanto maior for o número, menos chamadas remotas feitas do cliente para o servidor da região, resultando em sondagens mais rápidas. No entanto, esta definição também aumentará a pressão de memória sobre o cliente.

![Apache HBase número de linhas recolhidas](./media/optimize-hbase-ambari/hbase-num-rows-fetched.png)

> [!IMPORTANT]  
> Não desa um valor tal que o tempo entre a invocação do método seguinte num scanner seja maior do que o tempo limite do scanner. A duração do tempo limite do scanner é definida pela `hbase.regionserver.lease.period` propriedade.

## <a name="optimize-write-heavy-workloads"></a>Otimize cargas de trabalho pesadas

As seguintes configurações são importantes para melhorar o desempenho de cargas de trabalho pesadas.

### <a name="maximum-region-file-size"></a>Tamanho máximo do ficheiro da região

A HBase armazena dados num formato interno de ficheiros, chamado *HFile*. A propriedade `hbase.hregion.max.filesize` define o tamanho de um único HFile para uma região.  Uma região é dividida em duas regiões se a soma de todos os HFiles de uma região for maior do que este cenário.

!['Apache HBase HRegion max filesize'](./media/optimize-hbase-ambari/hbase-hregion-max-filesize.png)

Quanto maior for o tamanho do ficheiro da região, menor é o número de divisões. Pode aumentar o tamanho do ficheiro para determinar um valor que resulte no desempenho máximo de escrita.

### <a name="avoid-update-blocking"></a>Evite bloquear a atualização

* A propriedade `hbase.hregion.memstore.flush.size` define o tamanho em que a Memstore é lavada para o disco. O tamanho padrão é de 128 MB.

* O multiplicador de blocos da região HBase é definido por `hbase.hregion.memstore.block.multiplier` . O valor predefinido é 4. O máximo permitido é 8.

* HBase bloqueia atualizações se a Memstore estiver `hbase.hregion.memstore.flush.size`  *  `hbase.hregion.memstore.block.multiplier` () bytes.

    Com os valores predefinidos do tamanho de descarga e multiplicador de blocos, as atualizações são bloqueadas quando a Memstore tem 128 * 4 = 512 MB de tamanho. Para reduzir a contagem de bloqueio de atualização, aumente o valor de `hbase.hregion.memstore.block.multiplier` .

![Multiplicador de blocos da região Apache HBase](./media/optimize-hbase-ambari/hbase-hregion-memstore-block-multiplier.png)

## <a name="define-memstore-size"></a>Definir o tamanho de Memstore

O tamanho da memstore é definido pelos `hbase.regionserver.global.memstore.upperLimit` `hbase.regionserver.global.memstore.lowerLimit` parâmetros e parâmetros. Definir estes valores iguais uns aos outros reduz as pausas durante as escritas (causando também uma lavagem mais frequente) e resulta num aumento do desempenho da escrita.

## <a name="set-memstore-local-allocation-buffer"></a>Definir tampão de atribuição local memstore

O uso do tampão de atribuição local memstore é determinado pela propriedade `hbase.hregion.memstore.mslab.enabled` . Quando ativado (verdadeiro), esta definição evita a fragmentação da pilha durante uma operação de escrita pesada. O valor predefinido é true.

![hbase.hregion.memstore.mslab.enabled](./media/optimize-hbase-ambari/hbase-hregion-memstore-mslab-enabled.png)

## <a name="next-steps"></a>Passos seguintes

* [Gerir clusters HDInsight com a Web UI Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)
* [Otimizar clusters](./hdinsight-changing-configs-via-ambari.md)
* [Otimizar Apache Hive](./optimize-hive-ambari.md)
* [Otimizar Apache Pig](./optimize-pig-ambari.md)
