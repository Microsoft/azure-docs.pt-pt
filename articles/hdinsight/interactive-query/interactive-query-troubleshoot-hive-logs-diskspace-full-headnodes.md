---
title: 'Resolução de problemas: Os registos da Colmeia Apache preenchem o espaço do disco - Azure HDInsight'
description: Este artigo fornece passos de resolução de problemas a seguir quando os registos da Hive Apache estão a preencher o espaço do disco nos nós da cabeça em Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: nisgoel
ms.author: nisgoel
ms.date: 10/05/2020
ms.openlocfilehash: cd7e6a7f13f6cccb5be5d23d69c2a44fc655cf55
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98930953"
---
# <a name="scenario-apache-hive-logs-are-filling-up-the-disk-space-on-the-head-nodes-in-azure-hdinsight"></a>Cenário: Os troncos da Colmeia Apache estão a encher o espaço do disco nos nos nos acenos da cabeça em Azure HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas relacionados com o espaço insuficiente do disco nos nós da cabeça nos clusters Azure HDInsight.

## <a name="issue"></a>Problema

Num aglomerado Apache Hive/LLAP, troncos indesejados estão ocupando todo o espaço do disco nos nós da cabeça. Esta condição pode causar os seguintes problemas:

- O acesso ao SSH falha porque não há espaço no nó da cabeça.
- Ambari lança *HTTP ERROR: 503 Serviço Indisponível*.
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

2. Aceda à `Advanced hive-log4j` secção em **definições avançadas**.

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

## <a name="next-steps"></a>Próximos passos

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]
