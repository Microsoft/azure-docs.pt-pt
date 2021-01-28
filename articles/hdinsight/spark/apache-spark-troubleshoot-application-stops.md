---
title: Aplicação Apache Spark Streaming para após 24 dias em Azure HDInsight
description: Uma aplicação Apache Spark Streaming para após a execução por 24 dias e não há erros nos ficheiros de registo.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/29/2019
ms.openlocfilehash: b702cbf915e4991df4c202564677ea7e0a02f9c4
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98929465"
---
# <a name="scenario-apache-spark-streaming-application-stops-after-executing-for-24-days-in-azure-hdinsight"></a>Cenário: Pedido de streaming Apache Spark para após ser executado durante 24 dias em Azure HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao utilizar componentes Apache Spark em clusters Azure HDInsight.

## <a name="issue"></a>Problema

Uma aplicação Apache Spark Streaming para após a execução por 24 dias e não há erros nos ficheiros de registo.

## <a name="cause"></a>Causa

O `livy.server.session.timeout` valor controla quanto tempo a Apache Livy deve esperar que uma sessão esteja concluída. Uma vez que o comprimento da sessão atinja o `session.timeout` valor, a sessão Livy e a aplicação são automaticamente eliminadas.

## <a name="resolution"></a>Resolução

Para trabalhos de longa duração, aumente o valor da `livy.server.session.timeout` utilização da UI Ambari. Pode aceder à configuração Livy a partir da UI Ambari utilizando o URL `https://<yourclustername>.azurehdinsight.net/#/main/services/LIVY/configs` .

`<yourclustername>`Substitua-o pelo nome do seu cluster HDInsight, como mostrado no portal.

## <a name="next-steps"></a>Próximos passos

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]