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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76776199"
---
# <a name="scenario-unable-to-read-apache-yarn-log-in-azure-hdinsight"></a>Cenário: Incapaz de ler log Apache Yarn no Azure HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.

## <a name="issue"></a>Problema

Os registos de Fios Apache encontrados na conta de armazenamento não são legíveis pelo homem. O parser de ficheiros não funciona e produz a seguinte mensagem de erro:

```
java.io.IOException: Not a valid BCFile.
```

## <a name="cause"></a>Causa

O log Apache Yarn `IndexFile` é agregado em formato, que não é suportado pelo parser de ficheiros.

## <a name="resolution"></a>Resolução

1. De um navegador web, navegue até, `https://CLUSTERNAME.azurehdinsight.net`onde `CLUSTERNAME` está o nome do seu cluster.

1. A partir de Ambari UI, navegue até **yARN** > **Configs** > **Advanced** > **Advanced arn-site**.

1. Para armazenamento WASB: O `yarn.log-aggregation.file-formats` `IndexedFormat,TFile`valor predefinido para é . Alterar o `TFile`valor para .

1. Para armazenamento ADLS: O `yarn.nodemanager.log-aggregation.compression-type` `gz`valor predefinido para é . Alterar o `none`valor para .

1. Guarde a alteração e reinicie todos os serviços afetados.

## <a name="next-steps"></a>Passos seguintes

Se não viu o seu problema ou não consegue resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

* Obtenha respostas de especialistas do Azure através do [Apoio Comunitário de Azure.](https://azure.microsoft.com/support/community/)

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Ligar a comunidade Azure aos recursos certos: respostas, apoio e especialistas.

* Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** a partir da barra de menus ou abra o centro de **suporte Ajuda +.** Para obter informações mais detalhadas, reveja [como criar um pedido de apoio azure.](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte do Azure.](https://azure.microsoft.com/support/plans/)
