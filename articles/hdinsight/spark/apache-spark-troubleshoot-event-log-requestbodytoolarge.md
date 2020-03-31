---
title: RequestBodyTooLarge erro da app Apache Spark - Azure HDInsight
description: NativeAzureFileSystem ... RequestBodyTooLarge aparece em diário de bordo para a aplicação de streaming Apache Spark no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: 777d06670238a7625d190c92f78a55cd4794d226
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894404"
---
# <a name="nativeazurefilesystemrequestbodytoolarge-appear-in-apache-spark-streaming-app-log-in-hdinsight"></a>"NativeAzureFileSystem... RequestBodyTooLarge" aparece no registo de aplicações de streaming Apache Spark no HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao usar componentes Apache Spark em clusters Azure HDInsight.

## <a name="issue"></a>Problema

O erro: `NativeAzureFileSystem ... RequestBodyTooLarge` aparece no registo do controlador para uma aplicação de streaming Apache Spark.

## <a name="cause"></a>Causa

O ficheiro de registo de eventos spark está provavelmente a atingir o limite de comprimento do ficheiro para wasb.

Em Spark 2.3, cada aplicação Spark gera um ficheiro de registo de eventos Spark. O ficheiro de registo de eventos Spark para uma aplicação de streaming Spark continua a crescer enquanto a aplicação está em execução. Hoje, um ficheiro no WASB tem um limite de bloco de 50000, e o tamanho do bloco padrão é de 4 MB. Assim, na configuração predefinida, o tamanho máximo do ficheiro é de 195 GB. No entanto, o Armazenamento Azure aumentou o tamanho do bloco máximo para 100 MB, o que efetivamente elevou o limite de ficheiro único para 4,75 TB. Para obter mais informações, consulte [a escalabilidade e os objetivos](../../storage/blobs/scalability-targets.md)de desempenho para o armazenamento blob .

## <a name="resolution"></a>Resolução

Existem três soluções disponíveis para este erro:

* Aumente o tamanho do bloco até 100 MB. Na Ambari UI, modifique `fs.azure.write.request.size` a propriedade de `Custom core-site` configuração HDFS (ou crie-a na secção). Coloque a propriedade num valor maior, por exemplo: 33554432. Guarde a configuração atualizada e reinicie os componentes afetados.

* Parando periodicamente e reenvie o trabalho de streaming de faíscas.

* Utilize o HDFS para armazenar registos de eventos Spark. A utilização de HDFS para armazenamento pode resultar na perda de dados do evento Spark durante a escala de cluster ou atualizações do Azure.

    1. Faça alterações de `spark.eventlog.dir` e `spark.history.fs.logDirectory` via Ambari UI:

        ```
        spark.eventlog.dir = hdfs://mycluster/hdp/spark2-events
        spark.history.fs.logDirectory = "hdfs://mycluster/hdp/spark2-events"
        ```

    1. Crie diretórios em HDFS:

        ```
        hadoop fs -mkdir -p hdfs://mycluster/hdp/spark2-events
        hadoop fs -chown -R spark:hadoop hdfs://mycluster/hdp
        hadoop fs -chmod -R 777 hdfs://mycluster/hdp/spark2-events
        hadoop fs -chmod -R o+t hdfs://mycluster/hdp/spark2-events
        ```

    1. Reinicie todos os serviços afetados via Ambari UI.

## <a name="next-steps"></a>Passos seguintes

Se não viu o seu problema ou não consegue resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

* Obtenha respostas de especialistas do Azure através do [Apoio Comunitário de Azure.](https://azure.microsoft.com/support/community/)

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente, ligando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** a partir da barra de menus ou abra o centro de **suporte Ajuda +.** Para obter informações mais detalhadas, por favor reveja [como criar um pedido de apoio Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte do Azure.](https://azure.microsoft.com/support/plans/)
