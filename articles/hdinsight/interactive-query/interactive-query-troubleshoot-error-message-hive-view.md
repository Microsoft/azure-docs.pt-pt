---
title: Mensagem de erro não mostrada na Visão de Colmeia Apache - Azure HDInsight
description: A consulta falha no Apache Hive View sem quaisquer detalhes sobre o cluster Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 5aa03ac3537783daef87c9e7cb7d4ec58988ea9e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "75895207"
---
# <a name="scenario-query-error-message-not-displayed-in-apache-hive-view-in-azure-hdinsight"></a>Cenário: Mensagem de erro de consulta não exibida na Visão de Colmeia Apache em Azure HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao utilizar componentes de consultainterna interactive em clusters Azure HDInsight.

## <a name="issue"></a>Problema

A mensagem de erro de consulta Apache Hive View será algo assim, sem mais informações:

```
"Failed to execute query. <a href="#/messages/1">(details)</a>"
```

## <a name="cause"></a>Causa

Por vezes, a mensagem de erro de uma falha de consulta pode ser demasiado grande para ser exibida na página principal do Hive View.

## <a name="resolution"></a>Resolução

Consulte o separador Notificações no canto superior direito do Hive_view para ver a mensagem completa de Stacktrace e Error.

## <a name="next-steps"></a>Passos seguintes

Se não viu o seu problema ou não conseguir resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

* Obtenha respostas de especialistas da Azure através do [Apoio Comunitário Azure.](https://azure.microsoft.com/support/community/)

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente ligando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** na barra de menu ou abra o hub **de suporte Help +.** Para obter informações mais detalhadas, [reveja como criar um pedido de suporte Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte Azure](https://azure.microsoft.com/support/plans/).
