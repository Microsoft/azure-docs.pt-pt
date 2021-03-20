---
title: InvalidNetworkSecuritySecuritySecurityRules error - Azure HDInsight
description: A criação de cluster falha com o ErrorCode InvalidNetworkSecuritySecurityRules
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/31/2019
ms.openlocfilehash: 7e0b984b3ec4a203f8a1118c0e6a166c5a9e1125
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98944383"
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

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]