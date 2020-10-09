---
title: Apache Hive Logs enchendo o espaço do disco - Azure HDInsight
description: Os troncos da Colmeia Apache estão a encher o espaço do disco nos nós da cabeça em Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: nisgoel
ms.author: nisgoel
ms.reviewer: jasonh
ms.date: 03/05/2020
ms.openlocfilehash: d843b942702d335065a5f3798572e34c71b4cd0e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "78943972"
---
# <a name="scenario-apache-hive-logs-are-filling-up-the-disk-space-on-the-head-nodes-in-azure-hdinsight"></a>Cenário: Os registos da Colmeia Apache estão a preencher o espaço do disco nos nos nóns da Cabeça em Azure HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para questões relacionadas com o espaço de disco insuficiente nos nós da cabeça nos clusters Azure HDInsight.

## <a name="issue"></a>Problema

Num aglomerado Apache Hive/LLAP, troncos indesejados estão ocupando todo o espaço do disco nos nós da cabeça. Devido ao que, na sequência de problemas, poderia ser visto.

1. O acesso ao SSH falha por não ter espaço no nó da cabeça.
2. Ambari dá *HTTP ERROR: 503 Serviço Indisponível*.

Os `ambari-agent` registos mostrariam o seguinte quando o problema acontecesse.
```
ambari_agent - Controller.py - [54697] - Controller - ERROR - Error:[Errno 28] No space left on device
```
```
ambari_agent - HostCheckReportFileHandler.py - [54697] - ambari_agent.HostCheckReportFileHandler - ERROR - Can't write host check file at /var/lib/ambari-agent/data/hostcheck.result
```

## <a name="cause"></a>Causa

Nas configurações avançadas de Hive-log4j, o *parâmetro log4j.appender.RFA.MaxBackupIndex* é omitido. Causa uma geração interminável de ficheiros de registo.

## <a name="resolution"></a>Resolução

1. Navegue para o resumo do componente da Hive no portal Ambari e clique no `Configs` separador.

2. Aceda à `Advanced hive-log4j` secção dentro das definições Avançadas.

3. Definir `log4j.appender.RFA` o parâmetro como RollingFileAppender. 

4. Definir `log4j.appender.RFA.MaxFileSize` e da seguinte `log4j.appender.RFA.MaxBackupIndex` forma.

```
log4jhive.log.maxfilesize=1024MB
log4jhive.log.maxbackupindex=10

log4j.appender.RFA=org.apache.log4j.RollingFileAppender
log4j.appender.RFA.File=${hive.log.dir}/${hive.log.file}
log4j.appender.RFA.MaxFileSize=${log4jhive.log.maxfilesize}
log4j.appender.RFA.MaxBackupIndex=${log4jhive.log.maxbackupindex}
log4j.appender.RFA.layout=org.apache.log4j.PatternLayout
log4j.appender.RFA.layout.ConversionPattern=%d{ISO8601} %-5p [%t] %c{2}: %m%n
```
5. Definido `hive.root.logger` para o `INFO,RFA` seguinte. A definição predefinida é DEBUG, o que faz com que os troncos se tornem muito grandes.

```
# Define some default values that can be overridden by system properties
hive.log.threshold=ALL
hive.root.logger=INFO,RFA
hive.log.dir=${java.io.tmpdir}/${user.name}
hive.log.file=hive.log
```

6. Guarde as configs e reinicie os componentes necessários.

## <a name="next-steps"></a>Passos seguintes

Se não viu o seu problema ou não conseguir resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

* Obtenha respostas de especialistas da Azure através do [Apoio Comunitário Azure.](https://azure.microsoft.com/support/community/)

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente ligando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** na barra de menu ou abra o hub **de suporte Help +.** Para obter informações mais detalhadas, [reveja como criar um pedido de suporte Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte Azure](https://azure.microsoft.com/support/plans/).
