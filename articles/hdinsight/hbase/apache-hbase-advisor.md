---
title: Otimizar para recomendações de consultor de cluster
titleSuffix: Azure HDInsight
description: Otimize o Apache HBase para recomendações de consultor de cluster em Azure HDInsight.
author: ramkrish86
ms.author: ramvasu
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/03/2021
ms.openlocfilehash: acb7a6aeb4084949be3b0ad40e770a414a13ab6d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98943021"
---
# <a name="apache-hbase-advisories-in-azure-hdinsight"></a>Avisos Apache HBase em Azure HDInsight

Este artigo descreve vários avisos para ajudá-lo a otimizar o desempenho do Apache HBase em Azure HDInsight. 

## <a name="optimize-hbase-to-read-most-recently-written-data"></a>Otimize a HBase para ler os dados mais recentemente escritos

Se o seu caso de utilização envolver a leitura dos dados mais recentemente escritos da HBase, este aviso pode ajudá-lo. Para um alto desempenho, é ideal que as leituras HBase sejam servidas a partir de memstore, em vez do armazenamento remoto.

O aviso de consulta indica que para uma determinada família de colunas numa tabela > 75% lê-se que estão a ser servidos na memstore. Este indicador sugere que mesmo que um flush aconteça na memstore o ficheiro recente precisa de ser acedido e que precisa de estar em cache. Os dados são escritos pela primeira vez para memstore o sistema acede aos dados recentes lá. Existe a possibilidade de que os fios internos do flusher HBase detetem que uma determinada região atingiu o tamanho de 128M (padrão) e pode desencadear um flush. Este cenário acontece mesmo com os dados mais recentes que foram escritos quando a memstore tinha cerca de 128M de tamanho. Portanto, uma leitura posterior desses registos recentes pode exigir uma leitura de ficheiro em vez de memstore. Assim, é melhor otimizar que mesmo os dados recentes que são recentemente lavados podem residir na cache.

Para otimizar os dados recentes em cache, considere as seguintes definições de configuração:

1. Definir `hbase.rs.cacheblocksonwrite` para `true` . Esta configuração predefinitiva em HDInsight HBase `true` é, por isso verifique se não é reiniciada para `false` .

2. Aumente o `hbase.hstore.compactionThreshold` valor para evitar que a compactação faça efeito. Por predefinição, este valor é `3`. Pode aumentá-lo para um valor mais elevado `10` como.

3. Se seguir o passo 2 e definir compactionThreshold, em seguida, mude `hbase.hstore.compaction.max` para um valor mais elevado, por `100` exemplo, e aumente o valor do config `hbase.hstore.blockingStoreFiles` para um valor mais elevado, por `300` exemplo.

4. Se tiver a certeza de que precisa de ler apenas os dados recentes, desajuste `hbase.rs.cachecompactedblocksonwrite` a configuração para **ON**. Esta configuração diz ao sistema que mesmo que a compactação aconteça, os dados permanecem em cache. As configurações também podem ser definidas ao nível da família. 

   Na Membrana HBase, executar o seguinte comando para definir `hbase.rs.cachecompactedblocksonwrite` config:
   
   ```
   alter '<TableName>', {NAME => '<FamilyName>', CONFIGURATION => {'hbase.hstore.blockingStoreFiles' => '300'}}
   ```

5. Cache de bloco pode ser desligado para uma dada família em uma mesa. Certifique-se de que está **ligado** para famílias que têm os dados mais recentes. Por defeito, o cache do bloco é ligado para todas as famílias numa mesa. Caso tenha desativado a cache do bloco para uma família e precise de o ligar, utilize o comando alter da base hbase.

   Estas configurações ajudam a garantir que os dados estão disponíveis em cache e que os dados recentes não são submetidos a compactação. Se um TTL for possível no seu cenário, considere usar a compactação com datas. Para mais informações, consulte [o Guia de Referência Apache HBase: Compactação Tiered Date](https://hbase.apache.org/book.html#ops.date.tiered)  

## <a name="optimize-the-flush-queue"></a>Otimizar a fila de descarga

Este aviso indica que as descargas HBase podem necessitar de sintonização. A configuração atual para os manipuladores de descarga pode não ser suficientemente alta para manusear com tráfego de escrita que pode levar a um abrandamento das descargas .

Na uI do servidor da região, note se a fila de descarga cresce para além de 100. Este limiar indica que as descargas são lentas e pode ter de sintonizar a   `hbase.hstore.flusher.count` configuração. Por predefinição, o valor é 2. Certifique-se de que os fios de descarga máximo não aumentam para além de 6.

Além disso, veja se tem uma recomendação para a contagem de regiões. Se sim, sugerimos que experimente a afinação da região para ver se isso ajuda em descargas mais rápidas. Caso contrário, afinação dos fios do autoclismo pode ajudá-lo.

## <a name="region-count-tuning"></a>Afinação da contagem da região

O aviso de afinação da contagem da região indica que o HBase bloqueou as atualizações, e a contagem da região pode ser mais do que o tamanho da pilha suportado da melhor forma. Podes afinar o tamanho do monte, o tamanho da memstore, e a contagem da região.

Como um cenário de exemplo:

- Assuma que o tamanho da pilha para o servidor da região é de 10 GB. Por defeito, o `hbase.hregion.memstore.flush.size` is `128M` . O valor predefinido `hbase.regionserver.global.memstore.size` `0.4` é. O que significa que dos 10 GB, 4 GB é atribuído para memstore (globalmente).

- Assuma que há uma distribuição uniforme da carga de escrita em todas as regiões e assumindo que todas as regiões crescem até 128 MB apenas então o número máximo de regiões nesta configuração são `32` regiões. Se um determinado servidor da região estiver configurado para ter 32 regiões, é melhor que o sistema evite bloquear atualizações.

- Com estas configurações em vigor, o número de regiões é de 100. A memstore global de 4 GB está agora dividida em 100 regiões. Assim, efetivamente cada região recebe apenas 40 MB para memstore. Quando as escritas são uniformes, o sistema faz descargas frequentes e menor tamanho da encomenda < 40 MB. Ter muitos fios de descarga pode aumentar a velocidade de descarga `hbase.hstore.flusher.count` .

O aviso significa que seria bom reconsiderar o número de regiões por servidor, o tamanho da pilha e a configuração global do tamanho da memstore, juntamente com a sintonização de linhas de descarga para evitar que as atualizações sejam bloqueadas.

## <a name="compaction-queue-tuning"></a>Afinação da fila de compactação

Se a fila de compactação HBase crescer para mais de 2000 e acontecer periodicamente, pode aumentar os fios de compactação para um valor maior.

Quando há um número excessivo de ficheiros para compactação, pode levar a uma utilização mais grande relacionada com a forma como os ficheiros interagem com o sistema de ficheiros Azure. Assim, é melhor completar a compactação o mais rápido possível. Algumas vezes em agrupamentos mais antigos as configurações de compactação relacionadas com o estrangulamento podem levar a uma taxa de compactação mais lenta.

Verifique as configurações `hbase.hstore.compaction.throughput.lower.bound` e `hbase.hstore.compaction.throughput.higher.bound` . Se já estão definidos para 50M e 100M, deixe-os como está. No entanto, se configurar essas definições para um valor mais baixo (o que foi o caso dos clusters mais antigos), altere os limites para 50M e 100M respectivamente.

As configurações são `hbase.regionserver.thread.compaction.small` e `hbase.regionserver.thread.compaction.large` (as padrão são 1 cada).
Limite o valor máximo para esta configuração ser inferior a 3.

## <a name="full-table-scan"></a>Digitalização completa da mesa

O aviso completo de digitalização da tabela indica que mais de 75% dos exames emitidos são exames de mesa/região completos. Pode revisitar a forma como o seu código chama as tomografias para melhorar o desempenho da consulta. Considere as seguintes práticas:

* Desa estação de partida e de paragem adequada para cada digitalização.

* Utilize a API **MultiRowRangeFilter** para que possa consultar diferentes gamas numa chamada de verificação. Para mais informações, consulte [a documentação da API multiRowRangeFilter](https://hbase.apache.org/2.1/apidocs/org/apache/hadoop/hbase/filter/MultiRowRangeFilter.html).

* Nos casos em que você precisa de uma mesa completa ou de uma varredura da região, verifique se há a possibilidade de evitar o uso de cache para essas consultas, para que outras consultas que usam a cache possam não despejar os blocos que estão quentes. Para garantir que as digitalizações não utilizam cache, utilize a API **de digitalização** com a opção **setCaching (falso)** no seu código: 

   ```
   scan#setCaching(false)
   ```
   
## <a name="next-steps"></a>Passos seguintes

[Otimizar Apache HBase usando Ambari](../optimize-hbase-ambari.md)
