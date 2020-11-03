---
title: Falhas na criação de recursos de resolução de problemas em Azure HDInsight
description: Neste artigo são fornecidos erros comuns de emissão de capacidade e técnicas de mitigação.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.custom: seoapr2020
ms.date: 04/22/2020
ms.openlocfilehash: 2e9f6898dba559f557f49cc0ee9ff39644da243f
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93287350"
---
# <a name="troubleshoot-resource-creation-failures-in-azure-hdinsight"></a>Falhas na criação de recursos de resolução de problemas em Azure HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao interagir com a Azure HDInsight.

## <a name="error-the-deployment-would-exceed-the-quota-of-800"></a>Erro: A implantação excederia a quota de '800'

O Azure tem um limite de quota de 800 implementações por grupo de recursos. São aplicadas quotas diferentes por grupo de recursos, subscrição, conta ou outros âmbitos. Por exemplo, a subscrição pode ser configurada para limitar o número de núcleos de uma região. Tentar implantar uma máquina virtual com mais núcleos do que os resultados permitidos em mensagens de erro indicando que a quota foi excedida.

Para resolver este problema, elimine as implementações que já não são necessárias utilizando o portal Azure, CLI ou PowerShell.

Para obter mais informações, veja [Resolver erros de quotas de recursos](../azure-resource-manager/templates/error-resource-quota.md).

## <a name="error-the-maximum-node-exceeded-the-available-cores-in-this-region"></a>Erro: O nó máximo excedeu os núcleos disponíveis nesta região

A subscrição pode ser configurada para limitar o número de núcleos de uma região. Tentar implantar um recurso com mais núcleos do que os resultados permitidos em mensagens de erro indicando que a quota foi excedida.

Para pedir um aumento da quota, sigas os passos seguintes:

1. Vá ao [portal Azure](https://portal.azure.com)e selecione **Ajuda + suporte**.

1. Selecione **Novo pedido de suporte**.

1. No separador Básico da página de pedido de **apoio novo,** forneça as **seguintes** informações:

   * **Tipo de emissão:** Selecione **Limites de Serviço e subscrição (quotas)**.
   * **Assinatura:** Selecione a subscrição que pretende modificar.
   * **Tipo de quota:** Selecione **HDInsight**.

Para obter mais informações, veja [Criar um pedido de suporte para aumentar o núcleo](hdinsight-capacity-planning.md#quotas).

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [troubleshooting next steps](../../includes/hdinsight-troubleshooting-next-steps.md)]