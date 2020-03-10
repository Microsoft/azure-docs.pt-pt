---
title: Apache Hive Logs enchendo espaço de disco - Azure HDInsight
description: Os registos apache hive estão enchendo o espaço do disco nos nóóis da cabeça em Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: nisgoel
ms.author: nisgoel
ms.reviewer: jasonh
ms.date: 03/05/2020
ms.openlocfilehash: d843b942702d335065a5f3798572e34c71b4cd0e
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2020
ms.locfileid: "78943972"
---
# <a name="scenario-apache-hive-logs-are-filling-up-the-disk-space-on-the-head-nodes-in-azure-hdinsight"></a>Cenário: Os registos da Apache Hive estão a preencher o espaço do disco nos nódosos da Cabeça em Azure HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para questões relacionadas com o espaço de disco não suficiente nos nós da cabeça em clusters Azure HDInsight.

## <a name="issue"></a>Problema

Num aglomerado Apache Hive/LLAP, troncos indesejados estão ocupando todo o espaço do disco nos nós da cabeça. Devido ao que, na sequência de problemas, podia ser visto.

1. O acesso sSH falha por não ter espaço no nó da cabeça.
2. Ambari dá *ERRO HTTP: 503 Serviço Indisponível*.

Os registos `ambari-agent` mostrariam o seguinte quando o assunto acontecesse.
```
ambari_agent - Controller.py - [54697] - Controller - ERROR - Error:[Errno 28] No space left on device
```
```
ambari_agent - HostCheckReportFileHandler.py - [54697] - ambari_agent.HostCheckReportFileHandler - ERROR - Can't write host check file at /var/lib/ambari-agent/data/hostcheck.result
```

## <a name="cause"></a>Causa

Em configurações avançadas de Hive-log4j, o parâmetro *log4j.appender.RFA.MaxBackupIndex* é omitido. Causa uma geração interminável de ficheiros de registo.

## <a name="resolution"></a>Resolução

1. Navegue para o resumo do componente Hive no portal Ambari e clique no separador `Configs`.

2. Vá à secção `Advanced hive-log4j` dentro das definições avançadas.

3. Defina `log4j.appender.RFA` parâmetro como RollingFileAppender. 

4. Desloque `log4j.appender.RFA.MaxFileSize` e `log4j.appender.RFA.MaxBackupIndex` da seguinte forma.

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
5. Coloque `hive.root.logger` para `INFO,RFA` da seguinte forma. A definição predefinida é DEBUG, o que faz com que os registos se tornem muito grandes.

```
# Define some default values that can be overridden by system properties
hive.log.threshold=ALL
hive.root.logger=INFO,RFA
hive.log.dir=${java.io.tmpdir}/${user.name}
hive.log.file=hive.log
```

6. Guarde os configs e reinicie os componentes necessários.

## <a name="next-steps"></a>Passos Seguintes

Se não viu o seu problema ou não consegue resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

* Obtenha respostas de especialistas do Azure através do [Apoio Comunitário de Azure.](https://azure.microsoft.com/support/community/)

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente, ligando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** a partir da barra de menus ou abra o centro de **suporte Ajuda +.** Para obter informações mais detalhadas, por favor reveja [como criar um pedido de apoio Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte do Azure.](https://azure.microsoft.com/support/plans/)
