---
title: Erro InvalidNetworkSecurityGroupSecurityRules-Azure HDInsight
description: A criação do cluster falha com o ErrorCode InvalidNetworkSecurityGroupSecurityRules
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: a73e1e9f7a9c017dd29b627a24c25ae2e064c0a9
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/11/2020
ms.locfileid: "75894135"
---
# <a name="scenario-invalidnetworksecuritygroupsecurityrules---cluster-creation-fails-in-azure-hdinsight"></a>Cenário: InvalidNetworkSecurityGroupSecurityRules-a criação de cluster falha no Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao interagir com clusters do Azure HDInsight.

## <a name="issue"></a>Problema

Você recebe o código de erro `InvalidNetworkSecurityGroupSecurityRules` com uma descrição semelhante a "as regras de segurança no grupo de segurança de rede configuradas com a sub-rede não permitem a conectividade de entrada e/ou de saída necessária".

## <a name="cause"></a>Causa

Provavelmente um problema com as regras de [grupo de segurança de rede](../../virtual-network/virtual-network-vnet-plan-design-arm.md) de entrada configuradas para o cluster.

## <a name="resolution"></a>Resolução

Vá para a portal do Azure e identifique o NSG associado à sub-rede em que o cluster está sendo implantado. Na seção **regras de segurança de entrada** , verifique se as regras permitem o acesso de entrada à porta 443 para os endereços IP mencionados [aqui](../hdinsight-plan-virtual-network-deployment.md#hdinsight-ip).

## <a name="next-steps"></a>Passos seguintes

Se você não tiver visto seu problema ou não conseguir resolver o problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio do [suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/).

* Conecte-se com o [@AzureSupport](https://twitter.com/azuresupport) -a conta de Microsoft Azure oficial para melhorar a experiência do cliente conectando a Comunidade do Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, você poderá enviar uma solicitação de suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **suporte** na barra de menus ou abra o Hub **ajuda + suporte** . Para obter informações mais detalhadas, consulte [como criar uma solicitação de suporte do Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e ao suporte de cobrança está incluído na sua assinatura do Microsoft Azure, e o suporte técnico é fornecido por meio de um dos [planos de suporte do Azure](https://azure.microsoft.com/support/plans/).
