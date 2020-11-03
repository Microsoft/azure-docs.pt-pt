---
title: InvalidNetworkSecuritySecuritySecurityRules error - Azure HDInsight
description: A criação de cluster falha com o ErrorCode InvalidNetworkSecuritySecurityRules
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: b1423dc965a3169a5f615ccc371849cc177be244
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93289103"
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