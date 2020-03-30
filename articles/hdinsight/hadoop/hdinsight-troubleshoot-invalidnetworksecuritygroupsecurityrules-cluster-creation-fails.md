---
title: Erro InvalidNetworkSecurityGroupSecurityRules - Azure HDInsight
description: Criação de cluster falha com o Código de Erro InvalidNetworkSecuritySecurityRules
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: a73e1e9f7a9c017dd29b627a24c25ae2e064c0a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894135"
---
# <a name="scenario-invalidnetworksecuritygroupsecurityrules---cluster-creation-fails-in-azure-hdinsight"></a>Cenário: InvalidNetworkSecurityGroupSecurityRules - criação de cluster falha no Azure HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.

## <a name="issue"></a>Problema

Recebe código `InvalidNetworkSecurityGroupSecurityRules` de erro com uma descrição semelhante a "As regras de segurança do Grupo de Segurança da Rede configuradas com subnet não permitem a conectividade de entrada e/ou saída necessárias."

## <a name="cause"></a>Causa

Provavelmente um problema com as regras do grupo de segurança da [rede](../../virtual-network/virtual-network-vnet-plan-design-arm.md) de entrada configuradas para o seu cluster.

## <a name="resolution"></a>Resolução

Vá ao portal Azure e identifique o NSG que está associado à subrede onde o cluster está a ser implantado. Na secção de regras de segurança de **entrada,** certifique-se de que as regras permitem o acesso à entrada na porta 443 para os endereços IP [aqui](../hdinsight-plan-virtual-network-deployment.md#hdinsight-ip)mencionados .

## <a name="next-steps"></a>Passos seguintes

Se não viu o seu problema ou não consegue resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

* Obtenha respostas de especialistas do Azure através do [Apoio Comunitário de Azure.](https://azure.microsoft.com/support/community/)

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente, ligando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** a partir da barra de menus ou abra o centro de **suporte Ajuda +.** Para obter informações mais detalhadas, por favor reveja [como criar um pedido de apoio Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte do Azure.](https://azure.microsoft.com/support/plans/)
