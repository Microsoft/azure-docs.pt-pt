---
title: InvalidClassException erro de Apache Spark - Azure HDInsight
description: Apache Spark job falha com InvalidClassException, incompatibilidade da versão de classe, em Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/29/2019
ms.openlocfilehash: 0b0889ac1e71ce33406e89ead62370a0c0168763
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98929260"
---
# <a name="apache-spark-job-fails-with-invalidclassexception-class-version-mismatch-in-azure-hdinsight"></a>Apache Spark job falha com InvalidClassException, incompatibilidade da versão de classe, em Azure HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao utilizar componentes Apache Spark em clusters Azure HDInsight.

## <a name="issue"></a>Problema

Tentas criar um trabalho de Apache Spark num aglomerado de Faíscas 2.x. Falha com um erro semelhante ao seguinte:

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

Este erro pode ser causado adicionando um frasco adicional ao `spark.yarn.jars` config, especificamente um frasco sombreado que inclui uma versão diferente do `commons-lang3` pacote e introduz um desfasamento de classe. Por predefinição, o Spark 2.1/2/3 utiliza a versão 3.5 de `commons-lang3` .

> [!TIP]
> Ensombrar uma biblioteca é colocar o seu conteúdo no seu próprio jarro, mudando o seu pacote. Isto difere da embalagem da biblioteca, que está colocando a biblioteca no seu próprio jarro sem reembalar.

## <a name="resolution"></a>Resolução

Remova o jarro ou recompile o frasco personalizado (AzureLogAppender) e use [plugin de sombra de maven](https://maven.apache.org/plugins/maven-shade-plugin/examples/class-relocation.html) para realojar as aulas.

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]