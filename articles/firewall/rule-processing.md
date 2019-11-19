---
title: Lógica de processamento de regras do Azure Firewall
description: O Firewall do Azure tem regras de NAT, regras de rede e regras de aplicativos. As regras são processadas de acordo com o tipo de regra.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2018
ms.author: victorh
ms.openlocfilehash: 0fc11221e0ff79d6e17b93282403792fc135c2a4
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74166790"
---
# <a name="azure-firewall-rule-processing-logic"></a>Lógica de processamento de regras do Azure Firewall
O Firewall do Azure tem regras de NAT, regras de rede e regras de aplicativos. As regras são processadas de acordo com o tipo de regra.


## <a name="network-rules-and-applications-rules"></a>Regras de aplicativos e regras de rede 
As regras de rede são aplicadas primeiro e, em seguida, regras de aplicativo. As regras estão sendo encerradas. Portanto, se uma correspondência for encontrada em regras de rede, as regras de aplicativo não serão processadas.  Se não houver uma correspondência de regra de rede e o protocolo do pacote for HTTP/HTTPS, o pacote é então avaliado pelas regras de aplicação. Se ainda não for encontrada nenhuma correspondência, o pacote será avaliado em relação à coleção de regras de infraestrutura. Se ainda não houver correspondência, então o pacote é negado por predefinição.

## <a name="nat-rules"></a>Regras de NAT
A conectividade de entrada pode ser habilitada Configurando DNAT (conversão de endereços de rede de destino) conforme descrito em [tutorial: filtrar o tráfego de entrada com o Firewall do Azure DNAT usando o portal do Azure](tutorial-firewall-dnat.md). As regras DNAT são aplicadas primeiro. Se uma correspondência for encontrada, uma regra de rede implícita correspondente para permitir o tráfego traduzido será adicionada. Pode substituir esse comportamento, ao adicionar explicitamente uma coleção de regras de rede com regras de negar que correspondem ao tráfego traduzido. Nenhuma regra de aplicativo é aplicada a essas conexões.


## <a name="next-steps"></a>Passos seguintes

- Saiba como [implantar e configurar um firewall do Azure](tutorial-firewall-deploy-portal.md).