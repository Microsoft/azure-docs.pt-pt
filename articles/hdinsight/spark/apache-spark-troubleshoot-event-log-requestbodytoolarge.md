---
title: Erro de RequestBodyTooLarge do aplicativo Apache Spark-Azure HDInsight
description: NativeAzureFileSystem ... RequestBodyTooLarge aparece no log para o aplicativo de streaming Apache Spark no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: 2d2e929335f6af2ee24a81e719d9d0d899f7b8ef
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2019
ms.locfileid: "73241851"
---
# <a name="nativeazurefilesystemrequestbodytoolarge-appear-in-apache-spark-streaming-app-log-in-hdinsight"></a>"NativeAzureFileSystem... RequestBodyTooLarge "aparece no log do aplicativo Apache Spark streaming no HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao usar os componentes do Apache Spark nos clusters do Azure HDInsight.

## <a name="issue"></a>Problema

O erro: `NativeAzureFileSystem ... RequestBodyTooLarge` aparece no log de driver para um aplicativo de streaming Apache Spark.

## <a name="cause"></a>Causa

O arquivo de log de eventos do Spark provavelmente está atingindo o limite de comprimento do arquivo para WASB.

No Spark 2,3, cada aplicativo Spark gera um arquivo de log de eventos do Spark. O arquivo de log de eventos do Spark para um aplicativo de streaming do Spark continua crescendo enquanto o aplicativo está em execução. Hoje, um arquivo em WASB tem um limite de bloco de 50000 e o tamanho de bloco padrão é 4 MB. Portanto, na configuração padrão, o tamanho máximo do arquivo é 195 GB. No entanto, o armazenamento do Azure aumentou o tamanho máximo do bloco para 100 MB, o que efetivamente trouxe o limite de arquivo único para 4,75 TB. Para obter mais informações, consulte [metas de desempenho e escalabilidade do armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets).

## <a name="resolution"></a>Resolução

Há três soluções disponíveis para este erro:

* Aumente o tamanho do bloco para até 100 MB. Na interface do usuário do amAmbari, modifique a propriedade de configuração HDFS `fs.azure.write.request.size` (ou crie-a na seção `Custom core-site`). Defina a propriedade como um valor maior, por exemplo: 33554432. Salve a configuração atualizada e reinicie os componentes afetados.

* Pare periodicamente e envie novamente o trabalho de streaming do Spark.

* Use o HDFS para armazenar logs de eventos do Spark. Usar o HDFS para armazenamento pode resultar em perda de dados de evento do Spark durante o dimensionamento do cluster ou atualizações do Azure.

    1. Faça alterações em `spark.eventlog.dir` e `spark.history.fs.logDirectory` por meio da interface do usuário do Ambari:

        ```
        spark.eventlog.dir = hdfs://mycluster/hdp/spark2-events
        spark.history.fs.logDirectory = "hdfs://mycluster/hdp/spark2-events"
        ```

    1. Criar diretórios no HDFS:

        ```
        hadoop fs -mkdir -p hdfs://mycluster/hdp/spark2-events
        hadoop fs -chown -R spark:hadoop hdfs://mycluster/hdp
        hadoop fs -chmod -R 777 hdfs://mycluster/hdp/spark2-events
        hadoop fs -chmod -R o+t hdfs://mycluster/hdp/spark2-events
        ```

    1. Reinicie todos os serviços afetados por meio da interface do usuário do Ambari.

## <a name="next-steps"></a>Passos seguintes

Se você não tiver visto seu problema ou não conseguir resolver o problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio do [suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/).

* Conecte-se com o [@AzureSupport](https://twitter.com/azuresupport) -a conta de Microsoft Azure oficial para melhorar a experiência do cliente conectando a Comunidade do Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, você poderá enviar uma solicitação de suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **suporte** na barra de menus ou abra o Hub **ajuda + suporte** . Para obter informações mais detalhadas, consulte [como criar uma solicitação de suporte do Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e ao suporte de cobrança está incluído na sua assinatura do Microsoft Azure, e o suporte técnico é fornecido por meio de um dos [planos de suporte do Azure](https://azure.microsoft.com/support/plans/).
