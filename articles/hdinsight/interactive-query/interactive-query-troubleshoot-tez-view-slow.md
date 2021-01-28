---
title: Apache Ambari Tez Ver cargas lentamente em Azure HDInsight
description: Apache Ambari Tez View pode carregar lentamente ou não carregar em Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: 4fe66b3104be0351a9b0e1df6b6545f71ff276ab
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98930772"
---
# <a name="scenario-apache-ambari-tez-view-loads-slowly-in-azure-hdinsight"></a>Cenário: Apache Ambari Tez Ver cargas lentamente em Azure HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao utilizar componentes de consultainterna interactive em clusters Azure HDInsight.

## <a name="issue"></a>Problema

Apache Ambari Tez View pode carregar lentamente ou não carregar de todo. Ao carregar Ambari Tez View, poderá ver processos em Headnodes a ficarem sem resposta.

## <a name="cause"></a>Causa

O acesso às APIs de Yarn ATS pode por vezes ter um fraco desempenho em clusters criados antes de outubro de 2017, quando o cluster tem um grande número de empregos de Colmeias executados nele.

## <a name="resolution"></a>Resolução

Esta é uma questão que foi corrigida em outubro de 2017. Recriar o seu cluster fará com que não volte a encontrar este problema.

## <a name="next-steps"></a>Próximos passos

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]