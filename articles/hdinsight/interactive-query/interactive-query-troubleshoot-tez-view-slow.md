---
title: A exibição do Apache Ambari tez é carregada lentamente no Azure HDInsight
description: A exibição do Apache Ambari tez pode ser carregada lentamente ou não pode ser carregada no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 218f1b66152354cbf7f23b48e0ebf01be62e5550
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091279"
---
# <a name="scenario-apache-ambari-tez-view-loads-slowly-in-azure-hdinsight"></a>Cenário: A exibição do Apache Ambari tez é carregada lentamente no Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao usar componentes de consulta interativa em clusters do Azure HDInsight.

## <a name="issue"></a>Problema

A exibição do Apache Ambari tez pode ser carregada lentamente ou não pode ser carregada. Ao carregar o modo de exibição Ambari tez, você poderá ver os processos em cabeçalho ficando sem resposta.

## <a name="cause"></a>Causa

O acesso às APIs do yarn ATS às vezes pode ter baixo desempenho em clusters criados antes de outubro de 2017 quando o cluster tem um grande número de trabalhos do hive executados nele.

## <a name="resolution"></a>Resolução

Esse é um problema que foi corrigido em outubro de 2017. Recriar o cluster fará com que ele não fique com esse problema novamente.

## <a name="next-steps"></a>Passos Seguintes

Se você não tiver visto seu problema ou não conseguir resolver o problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio do [suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/).

* Conecte- [@AzureSupport](https://twitter.com/azuresupport) se com a conta de Microsoft Azure oficial para melhorar a experiência do cliente conectando a Comunidade do Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, você poderá enviar uma solicitação de suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **suporte** na barra de menus ou abra o Hub **ajuda + suporte** . Para obter informações mais detalhadas, consulte [como criar uma solicitação de suporte do Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e ao suporte de cobrança está incluído na sua assinatura do Microsoft Azure, e o suporte técnico é fornecido por meio de um dos [planos de suporte do Azure](https://azure.microsoft.com/support/plans/).
