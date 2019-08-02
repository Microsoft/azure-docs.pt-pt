---
title: Apache Spark trabalhos de streaming demoram mais do que o normal para serem processados no Azure HDInsight
description: Apache Spark trabalhos de streaming demoram mais do que o normal para serem processados no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/29/2019
ms.openlocfilehash: 7c7baaaa5fd08a102b3b55c38fb2c4bf892480c5
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68679435"
---
# <a name="scenario-apache-spark-streaming-jobs-take-longer-than-usual-to-process-in-azure-hdinsight"></a>Cenário: Apache Spark trabalhos de streaming demoram mais do que o normal para serem processados no Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao usar os componentes do Apache Spark nos clusters do Azure HDInsight.

## <a name="issue"></a>Problema

Observe que alguns dos trabalhos de streaming de Apache Spark estão lentos ou levando mais tempo do que o normal para processar. Para aplicativos de streaming do Spark, cada lote de mensagens corresponde a um trabalho enviado para o Spark. Se um trabalho normalmente leva X segundos para ser processado, ocasionalmente pode levar de 2-3 minutos mais do que o normal.

## <a name="cause"></a>Causa

Uma causa possível é que o produtor de Apache Kafka demora mais de 2 minutos para concluir a gravação no cluster Kafka. Para depurar o problema Kafka, você pode adicionar algum log ao código que usa um produtor do Kafka para enviar mensagens e correlacionar isso com os logs do cluster Kafka.

Outra causa possível é que leituras e gravações frequentes em WASB podem fazer com que micro lotes subsequentes sejam retardados. A implementação de WASB `Filesystem.listStatus` de é muito lenta devido a `O(n!)` um algoritmo para remover duplicatas. Ele usa muita memória devido à conversão extra de `BlobListItem` `FileStatus`para `FileMetadata` . Por exemplo, o algoritmo leva mais de 30 minutos para listar os arquivos 700.000. Portanto, `ListBlobs` se estiver sendo chamado agressivamente por SparkSQL todos os microlote, isso fará com que os micro lotes subseqüentes sejam atrasados, resultando no que você enfrenta com atrasos de agendamento altos. [Esse patch](https://issues.apache.org/jira/browse/HADOOP-15547) corrige o problema, mas se ele estiver ausente em seu ambiente, `ListBlobs` ocorrerá alta latência. Além disso, mesmo que você exclua arquivos a cada hora, a listagem no back-end deve iterar em todas as linhas (inclusive excluídas) porque o processo de coleta de lixo ainda não foi concluído. Embora o patch possa resolver algum problema, o problema de coleta de lixo ainda pode causar atraso no processamento de fluxos de lotes.

## <a name="resolution"></a>Resolução

Aplique a correção [HADOOP-15547](https://issues.apache.org/jira/browse/HADOOP-15547) . Se isso não for possível, você poderá usar HDFS como o local do ponto de verificação. Defina `checkpointDirectory` como algo como: `hdfs://mycluster/checkpoint`.

## <a name="next-steps"></a>Passos Seguintes

Se você não tiver visto seu problema ou não conseguir resolver o problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio do [suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/).

* Conecte- [@AzureSupport](https://twitter.com/azuresupport) se com a conta de Microsoft Azure oficial para melhorar a experiência do cliente conectando a Comunidade do Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, você poderá enviar uma solicitação de suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **suporte** na barra de menus ou abra o Hub **ajuda + suporte** . Para obter informações mais detalhadas, consulte [como criar uma solicitação de suporte do Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e ao suporte de cobrança está incluído na sua assinatura do Microsoft Azure, e o suporte técnico é fornecido por meio de um dos [planos de suporte do Azure](https://azure.microsoft.com/support/plans/).
