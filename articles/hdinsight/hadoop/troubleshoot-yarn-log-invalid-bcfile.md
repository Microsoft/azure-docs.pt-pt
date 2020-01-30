---
title: Incapaz de ler log Apache Yarn em Azure HDInsight
description: Passos de resolução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: f7861ae4980f13fbd87780ab2d26fa3376f33532
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2020
ms.locfileid: "76776199"
---
# <a name="scenario-unable-to-read-apache-yarn-log-in-azure-hdinsight"></a>Cenário: Incapaz de ler log Apache Yarn no Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao interagir com clusters do Azure HDInsight.

## <a name="issue"></a>Problema

Os registos de Fios Apache encontrados na conta de armazenamento não são legíveis pelo homem. O parser de ficheiros não funciona e produz a seguinte mensagem de erro:

```
java.io.IOException: Not a valid BCFile.
```

## <a name="cause"></a>Causa

O registo Apache Yarn é agregado em formato `IndexFile`, que não é suportado pelo parser de ficheiros.

## <a name="resolution"></a>Resolução

1. Em um navegador da Web, navegue até `https://CLUSTERNAME.azurehdinsight.net`, em que `CLUSTERNAME` é o nome do cluster.

1. Da Ambari UI, navegue até **ao YARN** > **Configs** > **avançado** > **avançado site de fios avançados.**

1. Para armazenamento WASB: O valor predefinido para `yarn.log-aggregation.file-formats` é `IndexedFormat,TFile`. Mude o valor para `TFile`.

1. Para armazenamento ADLS: O valor predefinido para `yarn.nodemanager.log-aggregation.compression-type` é `gz`. Mude o valor para `none`.

1. Guarde a alteração e reinicie todos os serviços afetados.

## <a name="next-steps"></a>Passos seguintes

Se você não tiver visto seu problema ou não conseguir resolver o problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio do [suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/).

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) -a conta de Microsoft Azure oficial para melhorar a experiência do cliente. Conectando a Comunidade do Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, você poderá enviar uma solicitação de suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **suporte** na barra de menus ou abra o Hub **ajuda + suporte** . Para obter informações mais detalhadas, consulte [como criar uma solicitação de suporte do Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e ao suporte de cobrança está incluído na sua assinatura do Microsoft Azure, e o suporte técnico é fornecido por meio de um dos [planos de suporte do Azure](https://azure.microsoft.com/support/plans/).
