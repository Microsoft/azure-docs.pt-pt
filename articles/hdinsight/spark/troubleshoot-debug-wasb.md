---
title: Operações de ficheiros Wasb dedepura no Azure HDInsight
description: Descreve etapas de resolução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/18/2020
ms.openlocfilehash: f1707c7f8d6324678c8bf5a470bbded1e58c719e
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77470722"
---
# <a name="debug-wasb-file-operations-in-azure-hdinsight"></a>Operações de ficheiros Wasb dedepura no Azure HDInsight

Há momentos em que pode querer entender que operações o condutor da WASB iniciou com o Armazenamento Azure. Para o lado do cliente, o controlador WASB produz registos para cada operação do sistema de ficheiros ao nível **DOBUG.** O controlador WASB utiliza log4j para controlar o nível de registo e o padrão é o nível **info.** Para registos de análise do lado do servidor do Armazém Azure, consulte o [registo de analíticos de armazenamento azure](../../storage/common/storage-analytics-logging.md).

Um registo produzido será semelhante a:

```log
18/05/13 04:15:55 DEBUG NativeAzureFileSystem: Moving wasb://xxx@yyy.blob.core.windows.net/user/livy/ulysses.txt/_temporary/0/_temporary/attempt_20180513041552_0000_m_000000_0/part-00000 to wasb://xxx@yyy.blob.core.windows.net/user/livy/ulysses.txt/part-00000
```

## <a name="turn-on-wasb-debug-log-for-file-operations"></a>Ligue o registo de depuração wasb para operações de ficheiros

1. De um navegador web, navegue até `https://CLUSTERNAME.azurehdinsight.net/#/main/services/SPARK2/configs`, onde `CLUSTERNAME` é o nome do seu cluster Spark.

1. Navegue para **propriedades avançadas de spark2-log4j**.

    1. Modificar `log4j.appender.console.Threshold=INFO` para `log4j.appender.console.Threshold=DEBUG`.

    1. Adicione `log4j.logger.org.apache.hadoop.fs.azure.NativeAzureFileSystem=DEBUG`.

1. Navegue para propriedades **avançadas de livy2-log4j**.

    Adicione `log4j.logger.org.apache.hadoop.fs.azure.NativeAzureFileSystem=DEBUG`.

1. Guarde as alterações.

## <a name="additional-logging"></a>Exploração madeireira adicional

Os registos acima devem fornecer uma compreensão de alto nível das operações do sistema de ficheiros. Se os registos acima não estiverem ainda a fornecer informações úteis, ou se pretender investigar chamadas api de armazenamento de bolhas, adicione `fs.azure.storage.client.logging=true` ao `core-site`. Esta definição permitirá os registos java sdk para o controlador de armazenamento wasb e imprimirá cada chamada para o servidor de armazenamento blob. Retire a definição após as investigações porque pode encher o disco rapidamente e pode abrandar o processo.

Se o backend for baseado em Azure Data Lake, utilize a seguinte definição de log4j para o componente (por exemplo, faísca/tez/hdfs):

```
log4j.logger.com.microsoft.azure.datalake.store=ALL,adlsFile
log4j.additivity.com.microsoft.azure.datalake.store=true
log4j.appender.adlsFile=org.apache.log4j.FileAppender
log4j.appender.adlsFile.File=/var/log/adl/adl.log
log4j.appender.adlsFile.layout=org.apache.log4j.PatternLayout
log4j.appender.adlsFile.layout.ConversionPattern=%p\t%d{ISO8601}\t%r\t%c\t[%t]\t%m%n
```

Procure os registos em `/var/log/adl/adl.log` para os registos.

## <a name="next-steps"></a>Passos Seguintes

Se não viu o seu problema ou não consegue resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

* Obtenha respostas de especialistas do Azure através do [Apoio Comunitário de Azure.](https://azure.microsoft.com/support/community/)

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Ligar a comunidade Azure aos recursos certos: respostas, apoio e especialistas.

* Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** a partir da barra de menus ou abra o centro de **suporte Ajuda +.** Para obter informações mais detalhadas, reveja [como criar um pedido de apoio azure.](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte do Azure.](https://azure.microsoft.com/support/plans/)
