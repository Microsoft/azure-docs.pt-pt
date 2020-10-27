---
title: Incapaz de entrar no cluster Azure HDInsight
description: Resolução de problemas por que não consegue entrar no aglomerado Apache Hadoop em Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: 059fee89fdb7f0bbc046c98e36c6386d06653fff
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92540419"
---
# <a name="scenario-unable-to-log-into-azure-hdinsight-cluster"></a>Cenário: Incapaz de entrar no cluster Azure HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.

## <a name="issue"></a>Problema

Incapaz de entrar no cluster Azure HDInsight.

## <a name="cause"></a>Causa

As razões podem variar. Tenha em mente que ao iniciar sessão nos painéis de instrumentos de agrupamento ou aplicações, utilize as suas credenciais de "cluster login" ou HTTP. Ao ligar remotamente, utilize as credenciais Secure Shell (SSH) ou de Ambiente de Trabalho Remoto.

## <a name="resolution"></a>Resolução

Para resolver problemas comuns, experimente um ou mais dos métodos seguintes.

* Experimente abrir o dashboard do cluster num novo separador do browser em modo de privacidade.

* Se não conseguir recordar as suas credenciais SSH, pode [redefinir as credenciais dentro da UI Ambari.](../hdinsight-administer-use-portal-linux.md#change-passwords)

## <a name="next-steps"></a>Passos seguintes

Se não viu o seu problema ou não conseguir resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

* Obtenha respostas de especialistas da Azure através do [Apoio Comunitário Azure.](https://azure.microsoft.com/support/community/)

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente ligando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** na barra de menu ou abra o hub **de suporte Help +.** Para obter informações mais detalhadas, [reveja como criar um pedido de suporte Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte Azure](https://azure.microsoft.com/support/plans/).