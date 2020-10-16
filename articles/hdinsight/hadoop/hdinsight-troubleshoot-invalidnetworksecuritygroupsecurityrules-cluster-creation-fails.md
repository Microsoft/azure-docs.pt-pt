---
title: InvalidNetworkSecuritySecuritySecurityRules error - Azure HDInsight
description: A criação de cluster falha com o ErrorCode InvalidNetworkSecuritySecurityRules
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: 2cf4859d3bf4c34fff4cb076eec11bcd2d81e4ab
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "82780781"
---
# <a name="scenario-invalidnetworksecuritygroupsecurityrules---cluster-creation-fails-in-azure-hdinsight"></a>Cenário: InvalidNetworkSecurityGroupSecurityRules - criação de cluster falha no Azure HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.

## <a name="issue"></a>Problema

Recebe código de erro `InvalidNetworkSecurityGroupSecurityRules` com uma descrição semelhante a "As regras de segurança do Grupo de Segurança da Rede configuradas com a sub-rede não permitem a necessária conectividade de entrada e/ou saída."

## <a name="cause"></a>Causa

Provavelmente um problema com as regras do [grupo de segurança](../../virtual-network/virtual-network-vnet-plan-design-arm.md) de rede de entrada configuradas para o seu cluster.

## <a name="resolution"></a>Resolução

Vá ao portal Azure e identifique o NSG que está associado à sub-rede onde o cluster está a ser implantado. Na secção **regras de segurança de entrada,** certifique-se de que as regras permitem o acesso à porta 443 para os endereços IP [mencionados aqui.](../control-network-traffic.md)

## <a name="next-steps"></a>Passos seguintes

Se não viu o seu problema ou não conseguir resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

* Obtenha respostas de especialistas da Azure através do [Apoio Comunitário Azure.](https://azure.microsoft.com/support/community/)

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente ligando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** na barra de menu ou abra o hub **de suporte Help +.** Para obter informações mais detalhadas, [reveja como criar um pedido de suporte Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte Azure](https://azure.microsoft.com/support/plans/).
