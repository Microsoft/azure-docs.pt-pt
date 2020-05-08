---
title: Otimizar Apache HBase com Apache Ambari em Azure HDInsight
description: Utilize o Apache Ambari web UI para configurar e otimizar o Apache HBase.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: a7da6bc23d797e0e89b2338f446fc850b0fd0577
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82797165"
---
# <a name="optimize-apache-hbase-with-apache-ambari-in-azure-hdinsight"></a>Otimizar Apache HBase com Apache Ambari em Azure HDInsight

Apache Ambari é uma interface web para gerir e monitorizar os clusters HDInsight. Para uma introdução ao Ambari Web UI, consulte [Os clusters Manage HDInsight utilizando o Apache Ambari Web UI](hdinsight-hadoop-manage-ambari.md).

A configuração Apache HBase é modificada a partir do separador **HBase Configs.** As seguintes secções descrevem algumas das definições de configuração importantes que afetam o desempenho da HBase.

## <a name="set-hbase_heapsize"></a>Definir HBASE_HEAPSIZE

O tamanho da pilha HBase especifica a quantidade máxima de pilha a ser usada em megabytes por *região* e servidores *principais.* O valor padrão é de 1.000 MB. Este valor deve ser ajustado para a carga de trabalho do cluster.

1. Para modificar, navegue para o painel **Avançado HBase-env** no separador `HBASE_HEAPSIZE` HBase **Configs** e, em seguida, encontre a definição.

1. Alterar o valor padrão para 5.000 MB.

    !['Apache Ambari HBase heapsize'](./media/optimize-hbase-ambari/ambari-hbase-heapsize.png)

## <a name="optimize-read-heavy-workloads"></a>Otimizar cargas de trabalho pesadas

As seguintes configurações são importantes para melhorar o desempenho das cargas de trabalho pesadas.

### <a name="block-cache-size"></a>Tamanho da cache do bloco

A cache do bloco é a cache de leitura. O seu tamanho `hfile.block.cache.size` é controlado pelo parâmetro. O valor padrão é de 0,4, que é 40 por cento da memória total do servidor da região. Quanto maior for o tamanho da cache do bloco, mais rápido será a leitura aleatória.

1. Para modificar este parâmetro, navegue para o separador **Definições** no separador HBase **Configs** e, em seguida, localize **% do RegionalServer Alocado para ler tampão**.

    ![Tamanho do bloco de cache do bloco de memória Apache HBase](./media/optimize-hbase-ambari/hbase-block-cache-size.png)

1. Para alterar o valor, selecione o ícone **Editar.**

### <a name="memstore-size"></a>Tamanho da loja

Todas as edições são armazenadas no amortecedor de memória, chamado *Memstore*. Este tampão aumenta a quantidade total de dados que podem ser escritos em disco numa única operação. Também acelera o acesso às edições recentes. O tamanho da Memstore é definido pelos dois parâmetros seguintes:

* `hbase.regionserver.global.memstore.UpperLimit`: Define a percentagem máxima do servidor da região que a Memstore combinada pode utilizar.

* `hbase.regionserver.global.memstore.LowerLimit`: Define a percentagem mínima do servidor da região que a Memstore combinada pode utilizar.

Para otimizar para leituras aleatórias, pode reduzir os limites superiores e inferiores da Memstore.

### <a name="number-of-rows-fetched-when-scanning-from-disk"></a>Número de linhas recolhidas ao digitalizar do disco

A `hbase.client.scanner.caching` definição define o número de linhas `next` lidas a partir do disco quando o método é chamado num scanner.  O valor predefinido é 100. Quanto maior for o número, menos chamadas remotas feitas do cliente para o servidor da região, resultando em exames mais rápidos. No entanto, esta definição também aumentará a pressão de memória sobre o cliente.

![Número de linhas Apache HBase recolhidas](./media/optimize-hbase-ambari/hbase-num-rows-fetched.png)

> [!IMPORTANT]  
> Não detete o valor de tal forma que o tempo entre a invocação do próximo método num scanner seja maior do que o tempo de tempo do scanner. A duração do tempo `hbase.regionserver.lease.period` limite do scanner é definida pela propriedade.

## <a name="optimize-write-heavy-workloads"></a>Otimizar cargas de trabalho pesadas

As seguintes configurações são importantes para melhorar o desempenho de cargas de trabalho pesadas.

### <a name="maximum-region-file-size"></a>Tamanho máximo de arquivo da região

A HBase armazena dados num formato interno de ficheiros, chamado *HFile*. A `hbase.hregion.max.filesize` propriedade define o tamanho de um único HFile para uma região.  Uma região é dividida em duas regiões se a soma de todos os HFiles numa região for maior do que este cenário.

!['Apache HBase HRegion max filesize'](./media/optimize-hbase-ambari/hbase-hregion-max-filesize.png)

Quanto maior o tamanho dos ficheiros da região, menor o número de divisões. Pode aumentar o tamanho do ficheiro para determinar um valor que resulte no desempenho máximo da escrita.

### <a name="avoid-update-blocking"></a>Evite o bloqueio de atualizações

* A `hbase.hregion.memstore.flush.size` propriedade define o tamanho a que a Memstore é lavada ao disco. O tamanho padrão é de 128 MB.

* O multiplicador de blocos `hbase.hregion.memstore.block.multiplier`da região HBase é definido por . O valor predefinido é 4. O máximo permitido é 8.

* HBase bloqueia atualizações se a`hbase.hregion.memstore.flush.size` * `hbase.hregion.memstore.block.multiplier`Memstore for () bytes.

    Com os valores predefinidos do tamanho do flush e do multiplicador de blocos, as atualizações são bloqueadas quando a Memstore tem 128 * 4 = 512 MB de tamanho. Para reduzir a contagem de bloqueio `hbase.hregion.memstore.block.multiplier`de atualização, aumente o valor de .

![Multiplicador de bloco de região De Apache HBase](./media/optimize-hbase-ambari/hbase-hregion-memstore-block-multiplier.png)

## <a name="define-memstore-size"></a>Definir tamanho Memstore

O tamanho da loja `hbase.regionserver.global.memstore.UpperLimit` é `hbase.regionserver.global.memstore.LowerLimit` definido pelos parâmetros e parâmetros. A definição destes valores iguais entre si reduz as pausas durante as escritas (também causando uma descarga mais frequente) e resulta num aumento do desempenho da escrita.

## <a name="set-memstore-local-allocation-buffer"></a>Definir tampão de alocação local Memstore

A utilização do tampão de `hbase.hregion.memstore.mslab.enabled`alocação local Memstore é determinada pela propriedade. Quando ativado (verdadeiro), esta definição impede a fragmentação da pilha durante o funcionamento da escrita pesada. O valor predefinido é true.

![hbase.hregion.memstore.mslab.habilitado](./media/optimize-hbase-ambari/hbase-hregion-memstore-mslab-enabled.png)

## <a name="next-steps"></a>Passos seguintes

* [Gerir clusters HDInsight com a UI web Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)
* [Otimizar aglomerados](./hdinsight-changing-configs-via-ambari.md)
* [Otimizar a Colmeia Apache](./optimize-hive-ambari.md)
* [Otimizar o Porco Apache](./optimize-pig-ambari.md)
