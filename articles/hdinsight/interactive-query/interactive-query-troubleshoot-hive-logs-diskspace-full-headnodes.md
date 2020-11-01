---
title: 'Resolução de problemas: Os registos da Colmeia Apache preenchem o espaço do disco - Azure HDInsight'
description: Este artigo fornece passos de resolução de problemas a seguir quando os registos da Hive Apache estão a preencher o espaço do disco nos nós da cabeça em Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: nisgoel
ms.author: nisgoel
ms.reviewer: jasonh
ms.date: 10/05/2020
ms.openlocfilehash: 64bf5714f5eb99df9929a47fef414a827ec680af
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2020
ms.locfileid: "93145638"
---
# <a name="scenario-apache-hive-logs-are-filling-up-the-disk-space-on-the-head-nodes-in-azure-hdinsight"></a>Cenário: Os troncos da Colmeia Apache estão a encher o espaço do disco nos nos nos acenos da cabeça em Azure HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas relacionados com o espaço insuficiente do disco nos nós da cabeça nos clusters Azure HDInsight.

## <a name="issue"></a>Problema

Num aglomerado Apache Hive/LLAP, troncos indesejados estão ocupando todo o espaço do disco nos nós da cabeça. Esta condição pode causar os seguintes problemas:

- O acesso ao SSH falha porque não há espaço no nó da cabeça.
- Ambari lança *HTTP ERROR: 503 Serviço Indisponível* .
- A HiveServer2 Interactive não consegue reiniciar.

Os `ambari-agent` registos incluirão as seguintes entradas quando o problema acontecer:
```
ambari_agent - Controller.py - [54697] - Controller - ERROR - Error:[Errno 28] No space left on device
```
```
ambari_agent - HostCheckReportFileHandler.py - [54697] - ambari_agent.HostCheckReportFileHandler - ERROR - Can't write host check file at /var/lib/ambari-agent/data/hostcheck.result
```

## <a name="cause"></a>Causa

Nas configurações avançadas do log4j da Hive, o atual crono de eliminação por defeito é eliminar ficheiros com mais de 30 dias, com base na última data modificada.

## <a name="resolution"></a>Resolução

1. Vá ao resumo do componente da Colmeia no portal Ambari e selecione o **separador Configs.**

2. Aceda à `Advanced hive-log4j` secção em **definições avançadas** .

3. Desa parte do `appender.RFA.strategy.action.condition.age` parâmetro para uma idade à sua escolha. Este exemplo fixará a idade para 14 dias: `appender.RFA.strategy.action.condition.age = 14D`

4. Se não vir nenhuma definição relacionada, apece estas definições:
    ```
    # automatically delete hive log
    appender.RFA.strategy.action.type = Delete
    appender.RFA.strategy.action.basePath = ${sys:hive.log.dir}
    appender.RFA.strategy.action.condition.type = IfLastModified
    appender.RFA.strategy.action.condition.age = 30D
    appender.RFA.strategy.action.PathConditions.type = IfFileName
    appender.RFA.strategy.action.PathConditions.regex = hive*.*log.*
    ```

5. Definido `hive.root.logger` para , como mostrado no exemplo `INFO,RFA` seguinte. A definição predefinida é `DEBUG` , o que torna os registos grandes.

    ```
    # Define some default values that can be overridden by system properties
    hive.log.threshold=ALL
    hive.root.logger=INFO,RFA
    hive.log.dir=${java.io.tmpdir}/${user.name}
    hive.log.file=hive.log
    ```

6. Guarde as configurações e reinicie os componentes necessários.

## <a name="next-steps"></a>Passos seguintes

Se não viu o seu problema ou não conseguir resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

* Obtenha respostas de especialistas da Azure através do [Apoio Comunitário Azure.](https://azure.microsoft.com/support/community/)

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente ligando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** na barra de menu ou abra o hub **de suporte Help +.** Para obter informações mais detalhadas, [reveja como criar um pedido de suporte Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte Azure](https://azure.microsoft.com/support/plans/).
