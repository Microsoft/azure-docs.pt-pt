---
title: Solucionar problemas de desempenho do Apache HBase no Azure HDInsight
description: Várias diretrizes e dicas de ajuste de desempenho do Apache HBase para obter um desempenho ideal no Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 09/24/2019
ms.openlocfilehash: c67f21a6ed8a7697977bb7737f0e46348efb2530
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2019
ms.locfileid: "71266656"
---
# <a name="troubleshoot-apache-hbase-performance-issues-on-azure-hdinsight"></a>Solucionar problemas de desempenho do Apache HBase no Azure HDInsight

Este documento descreve várias diretrizes e dicas de ajuste de desempenho do Apache HBase para obter um desempenho ideal no Azure HDInsight. Muitas dessas dicas dependem da carga de trabalho específica e do padrão de leitura/gravação/verificação. Teste totalmente as alterações de configuração antes de aplicar em um ambiente de produção.

## <a name="hdinsight-hbase-performance-insights"></a>Informações de desempenho do HBase no HDInsight

O principal afunilamento na maioria das cargas de trabalho do HBase é o log write ahead (WAL). Isso afeta seriamente o desempenho de gravação. O HBase do HDInsight tem um modelo de computação de armazenamento separado – ou seja, os dados são armazenados remotamente no armazenamento do Azure, mas os servidores de região são hospedados nas VMs. Até recentemente, o log write-ahead também foi gravado no armazenamento do Azure, o que amplifica esse afunilamento no caso do HDInsight. O recurso de [gravações aceleradas](./apache-hbase-accelerated-writes.md) foi projetado para resolver esse problema, gravando o log write-ahead nos discos gerenciados SSD do Azure Premium. Isso beneficia enormemente o desempenho e ajuda muitos problemas enfrentados por algumas das cargas de trabalho com uso intensivo de gravação.

Utilize a [Conta de Armazenamento de Blobs de Blocos Premium](https://azure.microsoft.com/blog/azure-premium-block-blob-storage-is-now-generally-available/) como armazenamento remoto para obter uma melhoria significativa nas operações de leitura. Esta opção só será possível se a funcionalidade Registos de Escrita Antecipada estiver ativada.

## <a name="compaction"></a>Compactação

A compactação é outro afunilamento em potencial fundamentalmente acordado na Comunidade.  Por padrão, a compactação principal é desabilitada em clusters HBase do HDInsight. Isso porque, Considerando que se trata de um processo de uso intensivo de recursos, queremos permitir que os clientes tenham total flexibilidade para agendá-lo de acordo com suas características de carga de trabalho – ou seja, fora do horário de pico. Além disso, dado que nosso armazenamento é remoto (apoiado pelo armazenamento do Azure) em oposição ao HDFS local, que é comum na maioria das instâncias locais, a localidade dos dados não é uma preocupação – que é um dos principais objetivos da compactação principal.

A suposição é que o cliente se encarrega de agendar a compactação principal de acordo com sua conveniência. Se essa manutenção não for feita, a compactação afetará significativamente o desempenho de leitura a longo prazo.

Para operações de verificação em particular, as latências muito maiores que 100 ms devem ser uma causa de preocupação. Verifique se a compactação principal foi agendada com precisão.

## <a name="know-your-apache-phoenix-workload"></a>Conheça sua carga de trabalho de Apache Phoenix

Responder às perguntas a seguir ajudará você a entender melhor sua carga de trabalho Apache Phoenix:

* Todas as suas "leituras" estão sendo traduzidas para verificações?
    * Em caso afirmativo, quais são as características dessas verificações?
    * Você otimizou o esquema da tabela Phoenix para essas verificações, incluindo a indexação apropriada?
* Você usou a instrução `EXPLAIN` para entender os planos de consulta gerados pela geração de "leituras".
* Suas gravações "Upsert-selects"?
    * Nesse caso, eles também fariam verificações. A latência esperada para verificações é da ordem de 100 ms em média, em oposição a 10 ms para o ponto em HBase.  

## <a name="test-methodology-and-metrics-monitoring"></a>Metodologia de teste e monitoramento de métricas

Se você estiver usando parâmetros de comparação como YCSB, JMeter ou Pherf para testar e ajustar o desempenho, verifique o seguinte:

1. Os computadores cliente não se tornam um afunilamento (verificar o uso da CPU em computadores cliente).

1. As configurações do lado do cliente – como o número de threads, e assim por diante, são ajustadas adequadamente para saturar a largura de banda do cliente.

1. Os resultados de teste são registrados de forma precisa e sistemática.

Se suas consultas começaram a fazer muito pior do que antes – Verifique se há bugs em potencial no código do aplicativo – ele gera repentinamente grandes quantidades de dados inválidos e, portanto, aumenta naturalmente as latências de leitura?

## <a name="migration-issues"></a>Problemas de migração

Se estiver migrando para o Azure HDInsight, certifique-se de que sua migração seja feita de forma sistemática e precisa, preferencialmente por meio da automação. Evite a migração manual. Verifique o seguinte:

1. Atributos de tabela – como compactação, filtros de flor e assim por diante, devem ser migrados com precisão.

1. Para tabelas Phoenix, as configurações de Salt devem ser mapeadas adequadamente para o novo tamanho do cluster. Por exemplo, é recomendável que o número de buckets Salt seja um múltiplo do número de nós de trabalho no cluster – o específico é um fator da quantidade de espera observada.  

## <a name="server-side-config-tunings"></a>Ajustes de configuração no lado do servidor

No HBase do HDInsight, os HFiles são armazenados no armazenamento remoto – assim, quando há um erro de cache, o custo das leituras definitivamente será maior do que os sistemas locais, que têm dados apoiados pelo HDFS local graças à latência de rede envolvida. Para a maioria dos cenários, o uso inteligente de caches do HBase (cache de blocos e cache de buckets) foi projetado para contornar esse problema. No entanto, permanecerão casos ocasionais em que isso pode ser um problema para o cliente. Usar a conta de blob de blocos Premium ajudou isso de certa forma. No entanto, com o blob WASB (driver de armazenamento do Windows Azure) que depende de determinadas propriedades, como `fs.azure.read.request.size` para buscar dados em blocos com base no que ele determina para ser modo de leitura (sequencial vs aleatório), poderemos continuar a ver instâncias de latências mais altas com leituras. Encontramos empírica experimentos que definem o tamanho do bloco de solicitação de leitura (`fs.azure.read.request.size`) como 512 KB e que correspondem ao tamanho do bloco das tabelas do HBase para ser o mesmo proporcionam o melhor resultado na prática.

O HBase do HDInsight, para os clusters de nós de grande porte, fornece `bucketcache` como um arquivo no SSD local anexado à VM, que executa o `regionservers`. Às vezes, usar off cache de heap pode dar algum aperfeiçoamento. Isso tem a limitação de usar a memória disponível e ser potencialmente de tamanho menor do que o cache baseado em arquivo, portanto, isso nem sempre é a melhor opção.

Alguns dos outros parâmetros específicos que ajustamos que pareciam ter ajudado a variar graus com alguma lógica, como a seguir:

1. Aumente o tamanho `memstore` do padrão 128 MB para 256 MB – essa configuração geralmente é recomendada para um cenário de gravação pesada.

1. Aumentando o número de threads dedicados para compactação – do padrão de 1 a 4. Essa configuração será relevante se observarmos compactações secundárias frequentes.

1. Evite bloquear a liberação de `memstore` devido ao limite de armazenamento. `Hbase.hstore.blockingStoreFiles` pode ser aumentado para 100 para fornecer esse buffer.

1. Para controlar liberações, os padrões podem ser tratados como abaixo:

    1. `Hbase.regionserver.maxlogs` pode ser upped a 140 de 32 (evitando liberações devido a limites de WAL).

    1. `Hbase.regionserver.global.memstore.lowerLimit` = 0,55.

    1. `Hbase.regionserver.global.memstore.upperLimit` = 0,60.

1. Configurações específicas de Phoenix para ajuste de pool de threads:

    1. `Phoenix.query.queuesize` pode ser aumentado para 10000.

    1. `Phoenix.query.threadpoolsize` pode ser aumentado para 512.

1. Outras configurações específicas de Phoenix:

    1. `Phoenix.rpc.index.handler.count` poderá ser definido como 50 se houver grandes ou muitas pesquisas de índice.

    1. `Phoenix.stats.updateFrequency` – pode ser upped a 1 hora do padrão de 15 minutos.

    1. `Phoenix.coprocessor.maxmetadatacachetimetolivems` – pode ser upped a uma hora de 30 minutos.

    1. `Phoenix.coprocessor.maxmetadatacachesize` – pode ser upped a 50 MB de 20 MB.

1. Tempos limite de RPC – tempo limite de RPC do HBase, tempo limite do scanner de cliente HBase e tempo limite de consulta Phoenix podem ser aumentados para 3 minutos. É importante observar que o parâmetro `hbase.client.scanner.caching` é definido como um valor que corresponde às extremidades do servidor e do cliente. Caso contrário, essa configuração leva a erros relacionados a `OutOfOrderScannerException` na extremidade do cliente. Essa configuração deve ser definida como um valor baixo para verificações grandes. Definimos esse valor como 100.

## <a name="other-considerations"></a>Outras considerações

Alguns outros parâmetros a serem considerados para ajuste:

1. `Hbase.rs.cacheblocksonwrite` – essa configuração é definida como true por padrão em HDI.

1. Configurações que permitem adiar a compactação secundária para mais tarde.

1. Configurações experimentais, como ajustar porcentagens de filas reservadas para solicitações de leitura e gravação.

## <a name="next-steps"></a>Passos seguintes

Se você não tiver visto seu problema ou não conseguir resolver o problema, visite um dos seguintes canais para obter mais suporte:

- Obtenha respostas de especialistas do Azure por meio do [suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/).

- Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) -a conta de Microsoft Azure oficial para melhorar a experiência do cliente. Conectando a Comunidade do Azure aos recursos certos: respostas, suporte e especialistas.

- Se precisar de mais ajuda, você poderá enviar uma solicitação de suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **suporte** na barra de menus ou abra o Hub **ajuda + suporte** . Para obter informações mais detalhadas, consulte [como criar uma solicitação de suporte do Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e ao suporte de cobrança está incluído na sua assinatura do Microsoft Azure, e o suporte técnico é fornecido por meio de um dos [planos de suporte do Azure](https://azure.microsoft.com/support/plans/).
