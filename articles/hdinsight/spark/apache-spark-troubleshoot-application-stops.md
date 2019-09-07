---
title: Apache Spark aplicativo de streaming é interrompido após 24 dias no Azure HDInsight
description: Um aplicativo Apache Spark streaming é interrompido após a execução por 24 dias e não há erros nos arquivos de log.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/29/2019
ms.openlocfilehash: c513c5df0d83eb0049683f88d85e8a1c41fd0bf0
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70736301"
---
# <a name="scenario-apache-spark-streaming-application-stops-after-executing-for-24-days-in-azure-hdinsight"></a>Cenário: Apache Spark aplicativo de streaming é interrompido após a execução por 24 dias no Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao usar os componentes do Apache Spark nos clusters do Azure HDInsight.

## <a name="issue"></a>Problema

Um aplicativo Apache Spark streaming é interrompido após a execução por 24 dias e não há erros nos arquivos de log.

## <a name="cause"></a>Causa

O `livy.server.session.timeout` valor controla por quanto tempo o Apache Livy deve aguardar a conclusão de uma sessão. Depois que o comprimento da sessão `session.timeout` atingir o valor, a sessão Livy e o aplicativo serão automaticamente eliminados.

## <a name="resolution"></a>Resolução

Para trabalhos de longa execução, aumente o valor `livy.server.session.timeout` de usando a interface do usuário do amAmbari. Você pode acessar a configuração Livy da interface do usuário do Ambari usando `https://<yourclustername>.azurehdinsight.net/#/main/services/LIVY/configs`a URL.

Substitua `<yourclustername>` pelo nome do seu cluster HDInsight, conforme mostrado no Portal.

## <a name="next-steps"></a>Passos seguintes

Se você não tiver visto seu problema ou não conseguir resolver o problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio do [suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/).

* Conecte- [@AzureSupport](https://twitter.com/azuresupport) se com a conta de Microsoft Azure oficial para melhorar a experiência do cliente conectando a Comunidade do Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, você poderá enviar uma solicitação de suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **suporte** na barra de menus ou abra o Hub **ajuda + suporte** . Para obter informações mais detalhadas, consulte [como criar uma solicitação de suporte do Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e ao suporte de cobrança está incluído na sua assinatura do Microsoft Azure, e o suporte técnico é fornecido por meio de um dos [planos de suporte do Azure](https://azure.microsoft.com/support/plans/).
