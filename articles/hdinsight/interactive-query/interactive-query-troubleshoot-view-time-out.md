---
title: Apache Hive View vezes fora do resultado da consulta - Azure HDInsight
description: Apache Hive View vezes fora ao obter um resultado de consulta em Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: 474fa5e084acfa508a4391075b3c78d96b01aa46
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98930740"
---
# <a name="scenario-apache-hive-view-times-out-when-fetching-a-query-result-in-azure-hdinsight"></a>Cenário: Apache Hive View times out when fetching a questionry result in Azure HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao utilizar componentes de consultainterna interactive em clusters Azure HDInsight.

## <a name="issue"></a>Problema

Ao executar certas consultas a partir da vista da Colmeia Apache, pode encontrar-se o seguinte erro:

```
result fetch timed out
java.util.concurrent.TimeoutException: deadline passed
```

## <a name="cause"></a>Causa

O valor de tempo limite padrão hive View pode não ser adequado para a consulta que está a executar. O período de tempo especificado é demasiado curto para que a Vista de Colmeias procure o resultado da consulta.

## <a name="resolution"></a>Resolução

Aumente os intervalos de tempo apache Ambari Hive View definindo as seguintes propriedades em `/etc/ambari-server/conf/ambari.properties` .

```
views.ambari.request.read.timeout.millis=300000
views.request.read.timeout.millis=300000
views.ambari.hive.<HIVE_VIEW_INSTANCE_NAME>.result.fetch.timeout=300000
```

O valor `HIVE_VIEW_INSTANCE_NAME` está disponível no final do URL Hive View.

## <a name="next-steps"></a>Próximos passos

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]