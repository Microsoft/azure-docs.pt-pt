---
title: Operações de ficheiros DEBUG WASB em Azure HDInsight
description: Descreve etapas de resolução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/18/2020
ms.openlocfilehash: 85e974d51f49bbb6742683ed253c077bb3ff69de
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96014657"
---
# <a name="debug-wasb-file-operations-in-azure-hdinsight"></a>Operações de ficheiros DEBUG WASB em Azure HDInsight

Há alturas em que poderá querer perceber que operações o condutor wasb começou com o Azure Storage. Para o lado do cliente, o controlador WASB produz registos para cada operação do sistema de ficheiros ao nível **de DEBUG.** O controlador WASB utiliza log4j para controlar o nível de registo e o padrão é o nível **INFO.** Para registos de análise do lado do servidor do Azure Storage, consulte [o registo de análises de armazenamento Azure](../../storage/common/storage-analytics-logging.md).

Um tronco produzido será semelhante a:

```log
18/05/13 04:15:55 DEBUG NativeAzureFileSystem: Moving wasb://xxx@yyy.blob.core.windows.net/user/livy/ulysses.txt/_temporary/0/_temporary/attempt_20180513041552_0000_m_000000_0/part-00000 to wasb://xxx@yyy.blob.core.windows.net/user/livy/ulysses.txt/part-00000
```

## <a name="turn-on-wasb-debug-log-for-file-operations"></a>Ligue o registo de depuro WASB para operações de ficheiros

1. A partir de um navegador web, navegue `https://CLUSTERNAME.azurehdinsight.net/#/main/services/SPARK2/configs` para, onde `CLUSTERNAME` está o nome do seu cluster Spark.

1. Navegue para **propriedades avançadas de spark2-log4j**.

    1. Modificar `log4j.appender.console.Threshold=INFO` para `log4j.appender.console.Threshold=DEBUG` .

    1. Adicione `log4j.logger.org.apache.hadoop.fs.azure.NativeAzureFileSystem=DEBUG` .

1. Navegue para **propriedades avançadas de livy2-log4j**.

    Adicione `log4j.logger.org.apache.hadoop.fs.azure.NativeAzureFileSystem=DEBUG` .

1. Guardar alterações.

## <a name="additional-logging"></a>Registo adicional

Os registos acima devem fornecer uma compreensão de alto nível das operações do sistema de ficheiros. Se os registos acima referidos ainda não fornecerem informações úteis, ou se pretender investigar chamadas api de armazenamento de bolhas, adicione `fs.azure.storage.client.logging=true` ao `core-site` . Esta definição permitirá os registos java Sdk para o controlador de armazenamento de wasb e imprimirá cada chamada para o servidor de armazenamento blob. Remova a definição após as investigações porque poderia encher o disco rapidamente e poderia abrandar o processo.

Se o backend for baseado em Azure Data Lake, em seguida, use a seguinte definição log4j para o componente (por exemplo, faísca/tez/hdfs):

```
log4j.logger.com.microsoft.azure.datalake.store=ALL,adlsFile
log4j.additivity.com.microsoft.azure.datalake.store=true
log4j.appender.adlsFile=org.apache.log4j.FileAppender
log4j.appender.adlsFile.File=/var/log/adl/adl.log
log4j.appender.adlsFile.layout=org.apache.log4j.PatternLayout
log4j.appender.adlsFile.layout.ConversionPattern=%p\t%d{ISO8601}\t%r\t%c\t[%t]\t%m%n
```

Procure os registos `/var/log/adl/adl.log` para os registos.

## <a name="next-steps"></a>Passos seguintes

Se não viu o seu problema ou não conseguir resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

* Obtenha respostas de especialistas da Azure através do [Apoio Comunitário Azure.](https://azure.microsoft.com/support/community/)

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Ligação da comunidade Azure aos recursos certos: respostas, apoio e especialistas.

* Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** na barra de menu ou abra o hub **de suporte Help +.** Para obter informações mais [detalhadas, reveja como criar um pedido de suporte Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte Azure](https://azure.microsoft.com/support/plans/).