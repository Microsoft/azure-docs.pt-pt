---
title: Incapaz de ler log Apache Yarn em Azure HDInsight
description: Etapas de resolução de problemas e possíveis resoluções para problemas ao interagir com os clusters Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: 02a79de8aee169f5f702d5fae67194c62363e8c4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98943040"
---
# <a name="scenario-unable-to-read-apache-yarn-log-in-azure-hdinsight"></a>Cenário: Incapaz de ler log Apache Yarn em Azure HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.

## <a name="issue"></a>Problema

Os registos apache yarn encontrados na conta de armazenamento não são legíveis pelo homem. O analisador de ficheiros não funciona e produz a seguinte mensagem de erro:

```
java.io.IOException: Not a valid BCFile.
```

## <a name="cause"></a>Causa

O log Apache Yarn é agregado em `IndexFile` formato, o que não é suportado pelo analisador de ficheiros.

## <a name="resolution"></a>Resolução

1. A partir de um navegador web, navegue `https://CLUSTERNAME.azurehdinsight.net` para, onde `CLUSTERNAME` está o nome do seu cluster.

1. A partir da UI Ambari, navegue até **yarn**  >  **Configs**  >  **Advanced**  >  **Yarn Advanced yarn-site**.

1. Para o armazenamento WASB: O valor padrão para `yarn.log-aggregation.file-formats` é `IndexedFormat,TFile` . Altere o valor para `TFile` .

1. Para o armazenamento ADLS: O valor padrão para `yarn.nodemanager.log-aggregation.compression-type` é `gz` . Altere o valor para `none` .

1. Guarde a alteração e reinicie todos os serviços afetados.

## <a name="next-steps"></a>Passos seguintes

Se não viu o seu problema ou não conseguir resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

* Obtenha respostas de especialistas da Azure através do [Apoio Comunitário Azure.](https://azure.microsoft.com/support/community/)

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Ligação da comunidade Azure aos recursos certos: respostas, apoio e especialistas.

* Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** na barra de menu ou abra o hub **de suporte Help +.** Para obter informações mais [detalhadas, reveja como criar um pedido de suporte Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte Azure](https://azure.microsoft.com/support/plans/).