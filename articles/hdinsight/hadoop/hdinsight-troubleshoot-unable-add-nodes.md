---
title: Não é possível adicionar nós ao cluster HDInsight do Azure
description: Solucionar problemas por que não é possível adicionar nós ao cluster Apache Hadoop no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: 97d7f34fff324a9959292460e534c15110c3e532
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/11/2020
ms.locfileid: "75894086"
---
# <a name="scenario-unable-to-add-nodes-to-azure-hdinsight-cluster"></a>Cenário: não é possível adicionar nós ao cluster HDInsight do Azure

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao interagir com clusters do Azure HDInsight.

## <a name="issue"></a>Problema

Não é possível adicionar nós ao cluster HDInsight do Azure.

## <a name="cause"></a>Causa

Os motivos podem variar.

## <a name="resolution"></a>Resolução

Usando o recurso de [tamanho do cluster](../hdinsight-scaling-best-practices.md) , calcule o número de núcleos adicionais necessários para o cluster. Isto baseia-se o número total de núcleos em nós de trabalho novos. Em seguida, tente uma ou mais das seguintes etapas:

* Verifique se há algum núcleo disponível no local do cluster.

* Observe o número de núcleos disponíveis noutras localizações. Considere recriar o cluster numa localização diferente com núcleos suficientes disponíveis.

* Se pretender aumentar a quota principal para uma localização específica, envie um pedido de suporte para um aumento de quota principal do HDInsight.

## <a name="next-steps"></a>Passos seguintes

Se você não tiver visto seu problema ou não conseguir resolver o problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio do [suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/).

* Conecte-se com o [@AzureSupport](https://twitter.com/azuresupport) -a conta de Microsoft Azure oficial para melhorar a experiência do cliente conectando a Comunidade do Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, você poderá enviar uma solicitação de suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **suporte** na barra de menus ou abra o Hub **ajuda + suporte** . Para obter informações mais detalhadas, consulte [como criar uma solicitação de suporte do Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e ao suporte de cobrança está incluído na sua assinatura do Microsoft Azure, e o suporte técnico é fornecido por meio de um dos [planos de suporte do Azure](https://azure.microsoft.com/support/plans/).
