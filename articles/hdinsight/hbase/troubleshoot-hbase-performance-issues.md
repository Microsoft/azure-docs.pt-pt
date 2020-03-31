---
title: Resolver problemas de desempenho do Apache HBase no Azure HDInsight
description: Várias diretrizes de afinação de desempenho Apache HBase e dicas para obter um desempenho ideal no Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 09/24/2019
ms.openlocfilehash: 93698fadcecf190dd8bbc24a9d03978899d3c5e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887160"
---
# <a name="troubleshoot-apache-hbase-performance-issues-on-azure-hdinsight"></a>Resolver problemas de desempenho do Apache HBase no Azure HDInsight

Este artigo descreve várias diretrizes de afinação de desempenho apache HBase e dicas para obter um desempenho ideal no Azure HDInsight. Muitas destas dicas dependem da carga de trabalho específica e do padrão de leitura/escrita/digitalização. Antes de aplicar alterações de configuração num ambiente de produção, teste-as cuidadosamente.

## <a name="hbase-performance-insights"></a>Insights de desempenho da HBase

O estrangulamento superior na maioria das cargas de trabalho da HBase é o Write Ahead Log (WAL). Impacta severamente a escrita do desempenho. HDInsight HBase tem um modelo separado de cálculo de armazenamento. Os dados são armazenados remotamente no Armazenamento Azure, mesmo que as máquinas virtuais administem os servidores da região. Até recentemente, o WAL também foi escrito para o Armazenamento Azure. No HDInsight, este comportamento amplificou este estrangulamento. A funcionalidade [Accelerated Writes](./apache-hbase-accelerated-writes.md) foi concebida para resolver este problema. Escreve o WAL para discos geridos por SSD Azure Premium. Isto beneficia tremendamente o desempenho da escrita, e ajuda muitas questões enfrentadas por algumas das cargas de trabalho intensivas.

Para obter melhorias significativas nas operações de leitura, utilize a Conta de [Armazenamento Blob premium](https://azure.microsoft.com/blog/azure-premium-block-blob-storage-is-now-generally-available/) Block como armazenamento remoto. Esta opção só é possível se a funcionalidade WAL estiver ativada.

## <a name="compaction"></a>Compaction

A compactação é outro potencial estrangulamento que é fundamentalmente acordado na comunidade. Por predefinição, a compaction principal é desativada nos clusters HBase HDInsight. A compactação é desativada porque, apesar de ser um processo intensivo de recursos, os clientes têm total flexibilidade para a agendar de acordo com as suas cargas de trabalho. Por exemplo, podem agendar durante as horas fora do pico. Além disso, a localização de dados não é uma preocupação porque o nosso armazenamento é remoto (apoiado pelo Armazenamento Azure) em vez de um Sistema de Ficheiros Distribuídos hadoop local (HDFS).

Os clientes devem agendar uma grande compactação à sua conveniência. Se não fizerem esta manutenção, a compactação afetará negativamente o desempenho da leitura a longo prazo.

Para as operações de digitalização, as tardios médias que são muito superiores a 100 milissegundos devem ser motivo de preocupação. Verifique se a grande compactação foi programada com precisão.

## <a name="apache-phoenix-workload"></a>Carga de trabalho Apache Phoenix

Responder às seguintes perguntas irá ajudá-lo a entender melhor a sua carga de trabalho Apache Phoenix:

* Todas as suas "leituras" estão traduzindo-se em exames?
    * Em caso afirmativo, quais são as características destes exames?
    * Otimizou o seu esquema de mesa Phoenix para estas análises, incluindo a indexação adequada?
* Usou a `EXPLAIN` declaração para entender os planos de consulta que as suas "leituras" geram?
* Os seus escritos são "upsert-selects"?
    * Se assim for, também estariam a fazer exames. A latência esperada para os exames tem uma média de aproximadamente 100 milissegundos, em comparação com 10 milissegundos para o ponto em HBase.  

## <a name="test-methodology-and-metrics-monitoring"></a>Metodologia de teste e monitorização de métricas

Se está a usar referências como o Yahoo! Cloud Serving Benchmark, JMeter ou Pherf para testar e afinar o desempenho, certifique-se de que:

- As máquinas de clientes não se tornam um estrangulamento. Para tal, verifique o uso do CPU nas máquinas de clientes.

- As configurações do lado do cliente, como o número de fios, são ajustadas adequadamente para saturar a largura de banda do cliente.

- Os resultados dos testes são registados com precisão e sistematicamente.

Se as suas consultas começaram subitamente a fazer muito pior do que antes, verifique se há potenciais bugs no seu código de aplicação. De repente está a gerar grandes quantidades de dados inválidos? Se for, pode aumentar as latenciências de leitura.

## <a name="migration-issues"></a>Questões migratórias

Se estiver a migrar para o Azure HDInsight, certifique-se de que a sua migração é feita de forma sistemática e precisa, de preferência através da automação. Evite a migração manual. Confirme que:

- Os atributos de mesa são migrados com precisão. Os atributos podem incluir como compressão, filtros de floração, e assim por diante.

- As definições de salga nas tabelas Phoenix são mapeadas adequadamente para o novo tamanho do cluster. Por exemplo, o número de baldes de sal deve ser um múltiplo do número de nós operários no aglomerado. E deve usar um múltiplo que é um fator da quantidade de manchas quentes.

## <a name="server-side-configuration-tunings"></a>Afinações de configuração do lado do servidor

No HDInsight HBase, os HFiles são armazenados em armazenamento remoto. Quando há uma falha de cache, o custo das leituras é maior do que os sistemas no local porque os dados sobre sistemas no local são apoiados pelo HDFS local. Para a maioria dos cenários, o uso inteligente de caches HBase (cache de bloco e cache de balde) foi concebido para contornar este problema. Nos casos em que o problema não é contornado, usar uma conta de bloco sinuoso premium pode ajudar a este problema. O controlador Windows Azure Storage Blob baseia-se em determinadas propriedades, tais como `fs.azure.read.request.size` recolher dados em blocos com base no que determina ser lido em modo (sequencial versus aleatório), pelo que pode continuar a haver casos de tardios mais elevados com leituras. Através de experiências empíricas, descobrimos que definir o`fs.azure.read.request.size`tamanho do bloco de leitura () para 512 KB e combinar o tamanho do bloco das tabelas HBase para ser do mesmo tamanho produz o melhor resultado na prática.

Para a maioria dos clusters de nós de grande `bucketcache` porte, o HDInsight HBase fornece como um ficheiro `regionservers`num SSD Premium local que está ligado à máquina virtual, que funciona . A utilização de cache off-heap pode, em vez disso, proporcionar algumas melhorias. Esta sutiã tem a limitação de usar a memória disponível e potencialmente ser menor do que a cache baseada em ficheiros, pelo que pode nem sempre ser a melhor escolha.

Seguem-se alguns dos outros parâmetros específicos que afinamos, e que pareciam ajudar a variar graus:

- Aumente `memstore` o tamanho de 128 MB padrão para 256 MB. Tipicamente, esta configuração é recomendada para cenários de escrita pesadas.

- Aumente o número de fios dedicados à compactação, da definição padrão de **1** a **4**. Esta definição é relevante se observarmos compactions menores frequentes.

- Evite bloquear `memstore` o autoclismo por causa do limite da loja. Para fornecer este tampão, aumente a `Hbase.hstore.blockingStoreFiles` regulação para **100**.

- Para controlar as descargas, utilize as seguintes definições:

    - `Hbase.regionserver.maxlogs`: **140** (evita descargas por causa dos limites do WAL)

    - `Hbase.regionserver.global.memstore.lowerLimit`: **0,55**

    - `Hbase.regionserver.global.memstore.upperLimit`: **0,60**

- Configurações específicas de Phoenix para afinação de piscinas de fios:

    - `Phoenix.query.queuesize`: **10000**

    - `Phoenix.query.threadpoolsize`: **512**

- Outras configurações específicas da Phoenix:

    - `Phoenix.rpc.index.handler.count`: **50** (se houver grandes ou muitos índices de procuração)

    - `Phoenix.stats.updateFrequency`: **1 hora**

    - `Phoenix.coprocessor.maxmetadatacachetimetolivems`: **1 hora**

    - `Phoenix.coprocessor.maxmetadatacachesize`: **50 MB**

- Intervalos de RPC: **3 minutos**

   - Os intervalos de tempo do RPC incluem o tempo de tempo de tempo para o scanner do cliente HBase, o tempo de intervalo do scanner do cliente HBase e o tempo de intervalo de consulta da Phoenix. 
   - Certifique-se `hbase.client.scanner.caching` de que o parâmetro está definido para o mesmo valor tanto na extremidade do servidor como no fim do cliente. Se não forem iguais, esta definição leva a erros `OutOfOrderScannerException`de fim de cliente que estão relacionados com . Esta definição deve ser definida para um valor baixo para grandes digitalizações. Definimos este valor para **100**.

## <a name="other-considerations"></a>Outras considerações

Seguem-se parâmetros adicionais a considerar a afinação:

- `Hbase.rs.cacheblocksonwrite`– por defeito no HDI, esta definição está definida como **verdadeira**.

- Definições que permitem adiar a compactação menor para mais tarde.

- Configurações experimentais, tais como o ajuste de percentagens de filas reservadas para leitura e pedidos de escrita.

## <a name="next-steps"></a>Passos seguintes

Se o seu problema não for resolvido, visite um dos seguintes canais para obter mais apoio:

- Obtenha respostas de especialistas do Azure através do [Apoio Comunitário de Azure.](https://azure.microsoft.com/support/community/)

- Conecte-se com [@AzureSupport](https://twitter.com/azuresupport). Esta é a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Liga a comunidade Azure aos recursos certos: respostas, apoio e especialistas.

- Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** a partir da barra de menus ou abra o centro de **suporte Ajuda +.** Para obter informações mais detalhadas, reveja [como criar um pedido de apoio azure.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) A subscrição do Microsoft Azure inclui acesso à gestão de subscrições e suporte à faturação, e o suporte técnico é fornecido através de um dos planos de [suporte do Azure.](https://azure.microsoft.com/support/plans/)
