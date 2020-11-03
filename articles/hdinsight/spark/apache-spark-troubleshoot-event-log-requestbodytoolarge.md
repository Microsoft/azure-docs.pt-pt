---
title: PedidoBodyTooLarge erro da aplicação Apache Spark - Azure HDInsight
description: NativeAzureFileSystem ... RequestBodyTooLarge aparece em log para a aplicação de streaming Apache Spark em Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: 38d6e5bfea1ae7ad4eead3a3f614007d31f0a7cb
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93287928"
---
# <a name="nativeazurefilesystemrequestbodytoolarge-appear-in-apache-spark-streaming-app-log-in-hdinsight"></a>"NativeAzureFileSystem... RequestBodyTooLarge" aparecem no log de aplicativos de streaming Apache Spark em HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao utilizar componentes Apache Spark em clusters Azure HDInsight.

## <a name="issue"></a>Problema

O erro: `NativeAzureFileSystem ... RequestBodyTooLarge` aparece no registo do controlador para uma aplicação de streaming Apache Spark.

## <a name="cause"></a>Causa

O ficheiro de registo de evento spark provavelmente está a atingir o limite de comprimento do ficheiro para WASB.

No Spark 2.3, cada aplicação Spark gera um ficheiro de registo de evento spark. O ficheiro de registo de eventos Spark para uma aplicação de streaming Spark continua a crescer enquanto a aplicação está em execução. Hoje, um ficheiro no WASB tem um limite de bloco de 50000, e o tamanho do bloco predefinido é de 4 MB. Assim, na configuração predefinida, o tamanho do ficheiro máximo é de 195 GB. No entanto, o Azure Storage aumentou o tamanho máximo do bloco para 100 MB, o que efetivamente elevou o limite de ficheiro único para 4,75 TB. Para obter mais informações, consulte [os objetivos de Escalabilidade e desempenho para o armazenamento blob.](../../storage/blobs/scalability-targets.md)

## <a name="resolution"></a>Resolução

Existem três soluções disponíveis para este erro:

* Aumente o tamanho do bloco para até 100 MB. Na UI Ambari, modifique a propriedade de configuração HDFS `fs.azure.write.request.size` (ou crie-a na `Custom core-site` secção). Coloque a propriedade num valor maior, por exemplo: 33554432. Guarde a configuração atualizada e reinicie os componentes afetados.

* Parada periodicamente e reenvia o trabalho de streaming de faíscas.

* Utilize HDFS para armazenar registos de eventos Spark. A utilização de HDFS para armazenamento pode resultar na perda de dados do evento Spark durante a escala do cluster ou atualizações do Azure.

    1. Fazer alterações para `spark.eventlog.dir` e `spark.history.fs.logDirectory` via Ambari UI:

        ```
        spark.eventlog.dir = hdfs://mycluster/hdp/spark2-events
        spark.history.fs.logDirectory = "hdfs://mycluster/hdp/spark2-events"
        ```

    1. Criar diretórios em HDFS:

        ```
        hadoop fs -mkdir -p hdfs://mycluster/hdp/spark2-events
        hadoop fs -chown -R spark:hadoop hdfs://mycluster/hdp
        hadoop fs -chmod -R 777 hdfs://mycluster/hdp/spark2-events
        hadoop fs -chmod -R o+t hdfs://mycluster/hdp/spark2-events
        ```

    1. Reinicie todos os serviços afetados através da Ambari UI.

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]