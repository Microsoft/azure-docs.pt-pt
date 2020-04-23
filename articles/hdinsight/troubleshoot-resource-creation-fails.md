---
title: Falhas na criação de recursos em Azure HDInsight
description: Neste artigo são fornecidos erros e técnicas de mitigação de problemas de capacidade comum.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/22/2020
ms.openlocfilehash: 871e6b1253b0fc9a5b6ae5d3389982cfdd6af3bd
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82103181"
---
# <a name="troubleshoot-resource-creation-failures-in-azure-hdinsight"></a>Falhas na criação de recursos em Azure HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao interagir com o Azure HDInsight.

## <a name="error-the-deployment-would-exceed-the-quota-of-800"></a>Erro: A implantação excederia a quota de '800'

O Azure tem um limite de quota de 800 implementações por grupo de recursos. São aplicadas quotas diferentes por grupo de recursos, subscrição, conta ou outros âmbitos. Por exemplo, a subscrição pode ser configurada para limitar o número de núcleos de uma região. Tentar implantar uma máquina virtual com mais núcleos do que os resultados permitidos na mensagem de erro indicando que a quota foi ultrapassada.

Para resolver este problema, elimine as implementações que já não são necessárias utilizando o portal Azure, CLI ou PowerShell.

Para obter mais informações, veja [Resolver erros de quotas de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors).

## <a name="error-the-maximum-node-exceeded-the-available-cores-in-this-region"></a>Erro: O nó máximo excedeu os núcleos disponíveis nesta região

A subscrição pode ser configurada para limitar o número de núcleos de uma região. Tentar implantar um recurso com mais núcleos do que os resultados permitidos na mensagem de erro indicando que a quota foi ultrapassada.

Para pedir um aumento da quota, sigas os passos seguintes:

1. Vá ao [portal Azure](https://portal.azure.com)e selecione **Ajuda + suporte**.

1. Selecione **Novo pedido de suporte**.

1. No separador Basics da nova página de pedido de **suporte,** forneça as **seguintes** informações:

   * **Tipo de emissão:** Selecione limites de **serviço e subscrição (quotas)**.
   * **Subscrição:** Selecione a subscrição que pretende modificar.
   * **Tipo de quota:** Selecione **HDInsight**.

Para obter mais informações, veja [Criar um pedido de suporte para aumentar o núcleo](hdinsight-capacity-planning.md#quotas).

## <a name="next-steps"></a>Passos seguintes

Se não viu o seu problema ou não consegue resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

* Obtenha respostas de especialistas do Azure através do [Apoio Comunitário de Azure.](https://azure.microsoft.com/support/community/)

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Ligar a comunidade Azure aos recursos certos: respostas, apoio e especialistas.

* Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** a partir da barra de menus ou abra o centro de **suporte Ajuda +.** Para obter informações mais detalhadas, reveja [como criar um pedido de apoio azure.](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte do Azure.](https://azure.microsoft.com/support/plans/)
