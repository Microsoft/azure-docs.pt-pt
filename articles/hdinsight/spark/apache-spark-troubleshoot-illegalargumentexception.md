---
title: Erro de conceção ilegal para Apache Spark - Azure HDInsight
description: IllegalArgumentExcepção para atividade de Apache Spark em Azure HDInsight para Azure Data Factory
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: df62dbd8db7d41eb11207c7741aed76cec0ac7a8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "75894389"
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

Se não viu o seu problema ou não conseguir resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

* Obtenha respostas de especialistas da Azure através do [Apoio Comunitário Azure.](https://azure.microsoft.com/support/community/)

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente ligando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** na barra de menu ou abra o hub **de suporte Help +.** Para obter informações mais detalhadas, [reveja como criar um pedido de suporte Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte Azure](https://azure.microsoft.com/support/plans/).
