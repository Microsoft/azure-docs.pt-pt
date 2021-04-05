---
title: Mau desempenho em consultas Apache Hive LLAP em Azure HDInsight
description: As consultas em Apache Hive LLAP estão executando mais lentamente do que o esperado em Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: 80fe51c2131da5a6fcb69eb5526b5d4f33a45e03
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98930896"
---
# <a name="scenario-poor-performance-in-apache-hive-llap-queries-in-azure-hdinsight"></a>Cenário: Mau desempenho em consultas Apache Hive LLAP em Azure HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao utilizar componentes de consultainterna interactive em clusters Azure HDInsight.

## <a name="issue"></a>Problema

As configurações do cluster predefinido não estão suficientemente sintonizadas para a sua carga de trabalho. As consultas na Hive LLAP estão a executar mais lentamente do que o esperado.

## <a name="cause"></a>Causa

Isto pode acontecer devido a uma variedade de razões.

## <a name="resolution"></a>Resolução

LLAP é otimizado para consultas que envolvem junções e agregados. Consultas como as seguintes não têm um bom desempenho num cluster de Colmeias Interativas:

```
select * from table where column = "columnvalue"
```

Para melhorar o desempenho da consulta de pontos na Hive LLAP, desaponte as seguintes configurações:

```
hive.llap.io.enabled=false; (disable LLAP IO)
hive.optimize.index.filter=false; (disable ORC row index)
hive.exec.orc.split.strategy=BI; (to avoid recombining splits)
```

Também pode aumentar a utilização da cache LLAP para melhorar o desempenho com a seguinte alteração de configuração:

```
hive.fetch.task.conversion=none
```

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]