---
title: Erro de InvalidClassException da Apache Spark-Azure HDInsight
description: Apache Spark trabalho falha com InvalidClassException, incompatibilidade de versão de classe, no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: 124d5586180258589c5db17454b8fbf1e465fc24
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74106481"
---
# <a name="apache-spark-job-fails-with-invalidclassexception-class-version-mismatch-in-azure-hdinsight"></a>Apache Spark trabalho falha com InvalidClassException, incompatibilidade de versão de classe, no Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao usar os componentes do Apache Spark nos clusters do Azure HDInsight.

## <a name="issue"></a>Problema

Você tenta criar um trabalho de Apache Spark em um cluster Spark 2. x. Ele falha com um erro semelhante ao seguinte:

```
18/09/18 09:32:26 WARN TaskSetManager: Lost task 0.0 in stage 1.0 (TID 1, wn7-dev-co.2zyfbddadfih0xdq0cdja4g.ax.internal.cloudapp.net, executor 4): java.io.InvalidClassException:
org.apache.commons.lang3.time.FastDateFormat; local class incompatible: stream classdesc serialVersionUID = 2, local class serialVersionUID = 1
        at java.io.ObjectStreamClass.initNonProxy(ObjectStreamClass.java:699)
        at java.io.ObjectInputStream.readNonProxyDesc(ObjectInputStream.java:1885)
        at java.io.ObjectInputStream.readClassDesc(ObjectInputStream.java:1751)
        at java.io.ObjectInputStream.readOrdinaryObject(ObjectInputStream.java:2042)
        at java.io.ObjectInputStream.readObject0(ObjectInputStream.java:1573)
```

## <a name="cause"></a>Causa

Esse erro pode ser causado pela adição de um jar adicional à configuração do `spark.yarn.jars`, especificamente um jar sombreado que inclui uma versão diferente do pacote `commons-lang3` e apresenta uma incompatibilidade de classe. Por padrão, o Spark 2.1/2/3 usa a versão 3,5 de `commons-lang3`.

> [!TIP]
> Para sombrear uma biblioteca, coloque seu conteúdo em seu próprio jar, alterando seu pacote. Isso é diferente de empacotar a biblioteca, que está colocando a biblioteca em seu próprio jar sem reempacotar.

## <a name="resolution"></a>Resolução

Remova o jar ou recompile o jar personalizado (AzureLogAppender) e use o [plug-in Maven-Shad-plugin](https://maven.apache.org/plugins/maven-shade-plugin/examples/class-relocation.html) para realocar classes.

## <a name="next-steps"></a>Passos seguintes

Se você não tiver visto seu problema ou não conseguir resolver o problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio do [suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/).

* Conecte-se com o [@AzureSupport](https://twitter.com/azuresupport) -a conta de Microsoft Azure oficial para melhorar a experiência do cliente conectando a Comunidade do Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, você poderá enviar uma solicitação de suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **suporte** na barra de menus ou abra o Hub **ajuda + suporte** . Para mais informações, consulte ["Como criar um pedido de suporte do Azure"](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). O acesso ao suporte para Gestão de Subscrições e faturação está incluído na sua subscrição do Microsoft Azure e o Suporte Técnico é disponibilizado através de um dos [Planos de Suporte do Azure](https://azure.microsoft.com/support/plans/).
