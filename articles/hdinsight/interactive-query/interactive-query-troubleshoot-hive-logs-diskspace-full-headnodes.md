---
title: Apache Hive Logs enchendo o espaço do disco - Azure HDInsight
description: Os troncos da Colmeia Apache estão a encher o espaço do disco nos nós da cabeça em Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: nisgoel
ms.author: nisgoel
ms.reviewer: jasonh
ms.date: 10/05/2020
ms.openlocfilehash: 5554a66927fc70f22ec552b938ae62038a04acb9
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92533024"
---
# <a name="scenario-apache-hive-logs-are-filling-up-the-disk-space-on-the-head-nodes-in-azure-hdinsight"></a>Cenário: Os registos da Colmeia Apache estão a preencher o espaço do disco nos nos nóns da Cabeça em Azure HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para questões relacionadas com o espaço de disco insuficiente nos nós da cabeça nos clusters Azure HDInsight.

## <a name="issue"></a>Problema

Num aglomerado Apache Hive/LLAP, troncos indesejados estão ocupando todo o espaço do disco nos nós da cabeça. Devido ao que, na sequência de problemas, poderia ser visto.

1. O acesso ao SSH falha por não ter espaço no nó da cabeça.
2. Ambari dá *HTTP ERROR: 503 Serviço Indisponível* .
3. A HiveServer2 Interactive não consegue reiniciar.

Os `ambari-agent` registos mostrariam o seguinte quando o problema acontecesse.
```
ambari_agent - Controller.py - [54697] - Controller - ERROR - Error:[Errno 28] No space left on device
```
```
ambari_agent - HostCheckReportFileHandler.py - [54697] - ambari_agent.HostCheckReportFileHandler - ERROR - Can't write host check file at /var/lib/ambari-agent/data/hostcheck.result
```

## <a name="cause"></a>Causa

Nas configurações avançadas de hive-log4j, o atual calendário de eliminação por defeito é definido para ficheiros com mais de 30 dias com base na última data modificada.

## <a name="resolution"></a>Resolução

1. Navegue para o resumo do componente da Hive no portal Ambari e clique no `Configs` separador.

2. Aceda à `Advanced hive-log4j` secção dentro das definições Avançadas.

3. Desa parte `appender.RFA.strategy.action.condition.age` para uma idade à sua escolha. Exemplo para 14 dias: `appender.RFA.strategy.action.condition.age = 14D`

4. Se não vir nenhuma definição relacionada, apece estas seguintes definições.
    ```
    # automatically delete hive log
    appender.RFA.strategy.action.type = Delete
    appender.RFA.strategy.action.basePath = ${sys:hive.log.dir}
    appender.RFA.strategy.action.condition.type = IfLastModified
    appender.RFA.strategy.action.condition.age = 30D
    appender.RFA.strategy.action.PathConditions.type = IfFileName
    appender.RFA.strategy.action.PathConditions.regex = hive*.*log.*
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

* Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** na barra de menu ou abra o hub **de suporte Help +.** Para obter informações mais detalhadas, [reveja como criar um pedido de suporte Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte Azure](https://azure.microsoft.com/support/plans/).