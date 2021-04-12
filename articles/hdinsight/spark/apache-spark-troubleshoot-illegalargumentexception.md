---
title: Erro de conceção ilegal para Apache Spark - Azure HDInsight
description: IllegalArgumentExcepção para atividade de Apache Spark em Azure HDInsight para Azure Data Factory
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/29/2019
ms.openlocfilehash: 429659d605cdaf8aad978841e486a17da321cce4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98929395"
---
# <a name="scenario-illegalargumentexception-for-apache-spark-activity-in-azure-hdinsight"></a>Cenário: IllegalArgumentExcepção para atividade de Apache Spark em Azure HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao utilizar componentes Apache Spark em clusters Azure HDInsight.

## <a name="issue"></a>Problema

Recebe a seguinte exceção ao tentar executar uma atividade Spark num oleoduto Azure Data Factory:

```error
Exception in thread "main" java.lang.IllegalArgumentException:
Wrong FS: wasbs://additional@xxx.blob.core.windows.net/spark-examples_2.11-2.1.0.jar, expected: wasbs://wasbsrepro-2017-11-07t00-59-42-722z@xxx.blob.core.windows.net
```

## <a name="cause"></a>Causa

Uma função Spark falhará se o ficheiro do frasco de aplicação não estiver localizado no armazenamento predefinido/primário do cluster Spark.

Este é um problema conhecido com o quadro de código aberto spark rastreado neste bug: [O trabalho de faísca falha se fs.defaultFS e frasco de aplicação são diferentes url](https://issues.apache.org/jira/browse/SPARK-22587).

Esta questão foi resolvida na Spark 2.3.0.

## <a name="resolution"></a>Resolução

Certifique-se de que o frasco de aplicação está guardado no armazenamento predefinido/primário para o cluster HDInsight. No caso da Azure Data Factory, certifique-se de que o serviço ligado a ADF está apontado para o recipiente padrão HDInsight em vez de um recipiente secundário.

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]