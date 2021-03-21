---
title: Resolver problemas de desempenho do Apache HBase no Azure HDInsight
description: Várias diretrizes e dicas de afinação de desempenho apache HBase para obter o melhor desempenho em Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 09/24/2019
ms.openlocfilehash: 466fac524601e2d569bfa0ccf90179fe9419210d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98942888"
---
# <a name="troubleshoot-apache-hbase-performance-issues-on-azure-hdinsight"></a>Resolver problemas de desempenho do Apache HBase no Azure HDInsight

Este artigo descreve várias diretrizes e dicas de afinação de desempenho apache HBase para obter o melhor desempenho em Azure HDInsight. Muitas destas dicas dependem da carga de trabalho específica e do padrão de leitura/escrita/digitalização. Antes de aplicar alterações de configuração num ambiente de produção, teste-as cuidadosamente.

## <a name="hbase-performance-insights"></a>Insights de desempenho da HBase

O estrangulamento superior na maioria das cargas de trabalho da HBase é o Write Ahead Log (WAL). Tem um impacto severo no desempenho da escrita. HDInsight HBase tem um modelo separado de computação de armazenamento. Os dados são armazenados remotamente no Azure Storage, mesmo que as máquinas virtuais acolham os servidores da região. Até recentemente, o WAL também foi escrito para a Azure Storage. Em HDInsight, este comportamento amplificou este estrangulamento. A funcionalidade [Escritas Aceleradas](./apache-hbase-accelerated-writes.md) foi concebida para resolver este problema. Escreve o WAL para discos geridos pela Azure Premium SSD. Isto beneficia tremendamente o desempenho da escrita, e ajuda muitas questões enfrentadas por algumas das cargas de trabalho intensivas de escrita.

Para obter uma melhoria significativa nas operações de leitura, utilize a [Conta de Armazenamento Premium Block Blob](https://azure.microsoft.com/blog/azure-premium-block-blob-storage-is-now-generally-available/) como armazenamento remoto. Esta opção só é possível se a funcionalidade WAL estiver ativada.

## <a name="compaction"></a>Compactação

A compactação é outro potencial estrangulamento que é fundamentalmente acordado na comunidade. Por predefinição, a grande compactação é desativada nos clusters HDInsight HBase. A compactação é desativada porque, apesar de ser um processo intensivo de recursos, os clientes têm total flexibilidade para o agendar de acordo com as suas cargas de trabalho. Por exemplo, podem programar durante as horas de ponta. Além disso, a localidade dos dados não é uma preocupação porque o nosso armazenamento é remoto (apoiado pelo Azure Storage) em vez de um Sistema de Ficheiros Distribuídos Hadoop local (HDFS).

Os clientes devem agendar uma grande compactação à sua conveniência. Se não fizerem esta manutenção, a compactação afetará negativamente o desempenho da leitura a longo prazo.

Para as operações de digitalização, as latnciências médias que são muito superiores a 100 milissegundos devem ser motivo de preocupação. Verifique se a grande compactação foi programada com precisão.

## <a name="apache-phoenix-workload"></a>Carga de trabalho apache Phoenix

Responder às seguintes perguntas irá ajudá-lo a entender melhor a sua carga de trabalho Apache Phoenix:

* Todas as suas "leituras" estão a traduzir-se em exames?
    * Em caso afirmativo, quais são as características destes exames?
    * Otimou o seu esquema de mesa Phoenix para estes exames, incluindo a indexação adequada?
* Já usou a `EXPLAIN` declaração para entender os planos de consulta que as suas "leituras" geram?
* As suas escritas são "upsert-selects"?
    * Se assim fosse, também estariam a fazer exames. A latência esperada para os exames médias de aproximadamente 100 milissegundos, em comparação com 10 milissegundos para obter pontos em HBase.  

## <a name="test-methodology-and-metrics-monitoring"></a>Metodologia de teste e monitorização de métricas

Se está a usar referências como o Yahoo! Cloud Serving Benchmark, JMeter ou Pherf para testar e afinar o desempenho, certifique-se de que:

- As máquinas de clientes não se tornam um estrangulamento. Para isso, verifique a utilização do CPU nas máquinas cliente.

- As configurações do lado do cliente, como o número de fios, são ajustadas adequadamente para saturar a largura de banda do cliente.

- Os resultados dos testes são registados com precisão e sistematicamente.

Se as suas consultas de repente começarem a fazer muito pior do que antes, verifique se existem potenciais erros no seu código de aplicação. Está subitamente a gerar grandes quantidades de dados inválidos? Se for, pode aumentar as latências de leitura.

## <a name="migration-issues"></a>Problemas de migração

Se estiver a migrar para a Azure HDInsight, certifique-se de que a sua migração é feita de forma sistemática e precisa, de preferência através da automação. Evite a migração manual. Certifique-se de que:

- Os atributos da tabela são migrados com precisão. Os atributos podem incluir como compressão, filtros de floração, e assim por diante.

- As definições de salga nas tabelas Phoenix são mapeadas adequadamente para o novo tamanho do cluster. Por exemplo, o número de baldes de sal deve ser um múltiplo do número de nós de trabalhadores no aglomerado. E deve usar um múltiplo que é um fator da quantidade de manchas quentes.

## <a name="server-side-configuration-tunings"></a>Sintonizações de configuração do lado do servidor

No HDInsight HBase, os HFiles são armazenados em armazenamento remoto. Quando há uma falha de cache, o custo das leituras é maior do que os sistemas no local porque os dados dos sistemas no local são apoiados pelo HDFS local. Para a maioria dos cenários, a utilização inteligente de caches HBase (cache de bloco e cache de balde) foi concebida para contornar este problema. Nos casos em que o problema não é contornado, usar uma conta blob de bloco premium pode ajudar neste problema. O controlador do Windows Azure Storage Blob baseia-se em certas propriedades, tais como `fs.azure.read.request.size` para obter dados em blocos com base no que determina ser o modo de leitura (sequencial versus aleatório), pelo que pode continuar a existir casos de latências mais altas com leituras. Através de experiências empíricas, descobrimos que definir o tamanho do bloco de pedido de leitura ( `fs.azure.read.request.size` ) para 512 KB e combinar o tamanho do bloco das tabelas HBase para ser do mesmo tamanho produz o melhor resultado na prática.

Para a maioria dos aglomerados de nós de grande porte, o HDInsight HBase fornece `bucketcache` como um ficheiro num SSD Premium local que está ligado à máquina virtual, que funciona `regionservers` . A utilização de cache off-heap pode, em vez disso, proporcionar algumas melhorias. Esta solução tem a limitação de usar a memória disponível e potencialmente ser menor do que cache baseado em arquivo, por isso pode nem sempre ser a melhor escolha.

Seguem-se alguns dos outros parâmetros específicos que afinamos, e que pareciam ajudar a variar graus:

- Aumente `memstore` o tamanho de 128 MB para 256 MB. Normalmente, esta definição é recomendada para cenários de escrita pesada.

- Aumente o número de fios dedicados à compactação, da definição predefinição de **1** a **4**. Esta definição é relevante se observarmos pequenas compactações frequentes.

- Evite bloquear o `memstore` autoclismo por causa do limite da loja. Para fornecer este tampão, aumente a `Hbase.hstore.blockingStoreFiles` regulação para **100**.

- Para controlar as descargas, utilize as seguintes definições:

    - `Hbase.regionserver.maxlogs`: **140** (evita descargas por causa dos limites WAL)

    - `Hbase.regionserver.global.memstore.lowerLimit`: **0.55**

    - `Hbase.regionserver.global.memstore.upperLimit`: **0.60**

- Configurações específicas de Phoenix para afinação da piscina de fios:

    - `Phoenix.query.queuesize`: **10000**

    - `Phoenix.query.threadpoolsize`: **512**

- Outras configurações específicas da Phoenix:

    - `Phoenix.rpc.index.handler.count`: **50** (se houver grandes ou muitas análises de índice)

    - `Phoenix.stats.updateFrequency`: **1 hora**

    - `Phoenix.coprocessor.maxmetadatacachetimetolivems`: **1 hora**

    - `Phoenix.coprocessor.maxmetadatacachesize`: **50 MB**

- Intervalos de tempo RPC: **3 minutos**

   - Os intervalos de tempo RPC incluem o tempo limite de tempo do HBase RPC, o tempo limite do scanner do cliente HBase e o tempo limite de consulta da Phoenix. 
   - Certifique-se de que o `hbase.client.scanner.caching` parâmetro está definido para o mesmo valor tanto na extremidade do servidor como no fim do cliente. Se não forem os mesmos, esta definição leva a erros no final do cliente que estão relacionados com `OutOfOrderScannerException` . Esta definição deve ser definida para um valor baixo para grandes digitalizações. Definimos este valor para **100.**

## <a name="other-considerations"></a>Outras considerações

Seguem-se parâmetros adicionais a considerar a sintonização:

- `Hbase.rs.cacheblocksonwrite` – por padrão no HDI, esta definição é definida como **verdadeira**.

- Configurações que permitem adiar uma pequena compactação para mais tarde.

- Configurações experimentais, tais como ajustar percentagens de filas reservadas para pedidos de leitura e escrita.

## <a name="next-steps"></a>Passos seguintes

Se o seu problema não for resolvido, visite um dos seguintes canais para obter mais apoio:

- Obtenha respostas de especialistas da Azure através do [Apoio Comunitário Azure.](https://azure.microsoft.com/support/community/)

- Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) . Esta é a conta oficial da Microsoft Azure para melhorar a experiência do cliente. Liga a comunidade Azure aos recursos certos: respostas, apoio e especialistas.

- Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** na barra de menu ou abra o hub **de suporte Help +.** Para obter informações mais [detalhadas, reveja como criar um pedido de suporte Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). A sua subscrição do Microsoft Azure inclui acesso à gestão de subscrições e suporte de faturação, e o suporte técnico é fornecido através de um dos planos de suporte do [Azure.](https://azure.microsoft.com/support/plans/)