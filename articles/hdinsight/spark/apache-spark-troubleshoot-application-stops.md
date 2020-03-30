---
title: Aplicação Apache Spark Streaming para após 24 dias no Azure HDInsight
description: Uma aplicação Apache Spark Streaming para após a execução durante 24 dias e não há erros nos ficheiros de registo.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: ff410ea1b6c54d2f58babeb20c68fe95033e9728
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894424"
---
# <a name="scenario-apache-spark-streaming-application-stops-after-executing-for-24-days-in-azure-hdinsight"></a>Cenário: Aplicação Apache Spark Streaming para após ser executada durante 24 dias no Azure HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao usar componentes Apache Spark em clusters Azure HDInsight.

## <a name="issue"></a>Problema

Uma aplicação Apache Spark Streaming para após a execução durante 24 dias e não há erros nos ficheiros de registo.

## <a name="cause"></a>Causa

O `livy.server.session.timeout` valor controla quanto tempo a Apache Livy deve esperar que uma sessão esteja concluída. Uma vez que o `session.timeout` comprimento da sessão atinge o valor, a sessão de Livy e a aplicação são automaticamente mortas.

## <a name="resolution"></a>Resolução

Para trabalhos de longa `livy.server.session.timeout` duração, aumente o valor da utilização da UI Ambari. Pode aceder à configuração Livy a partir do `https://<yourclustername>.azurehdinsight.net/#/main/services/LIVY/configs`Ambari UI utilizando o URL .

Substitua-o `<yourclustername>` pelo nome do seu cluster HDInsight, como mostrado no portal.

## <a name="next-steps"></a>Passos seguintes

Se não viu o seu problema ou não consegue resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

* Obtenha respostas de especialistas do Azure através do [Apoio Comunitário de Azure.](https://azure.microsoft.com/support/community/)

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente, ligando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** a partir da barra de menus ou abra o centro de **suporte Ajuda +.** Para obter informações mais detalhadas, por favor reveja [como criar um pedido de apoio Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte do Azure.](https://azure.microsoft.com/support/plans/)
