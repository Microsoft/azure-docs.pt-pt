---
title: Lógica de processamento de regras do Azure Firewall
description: Saiba mais sobre a lógica de processamento da regra de firewall do Azure
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: victorh
ms.openlocfilehash: 74e58c316651a1604984ac14c70a3a65d46d6d9f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73518206"
---
# <a name="azure-firewall-rule-processing-logic"></a>Lógica de processamento de regras do Azure Firewall

O Firewall do Azure tem regras de NAT, regras de rede e regras de aplicativos. As regras são processadas de acordo com o tipo de regra.

## <a name="network-rules-and-applications-rules"></a>Regras de aplicativos e regras de rede

As regras de rede são aplicadas primeiro e, em seguida, regras de aplicativo. As regras estão sendo encerradas. Portanto, se uma correspondência for encontrada em regras de rede, as regras de aplicativo não serão processadas.  Se não houver uma correspondência de regra de rede e o protocolo do pacote for HTTP/HTTPS, o pacote é então avaliado pelas regras de aplicação. Se ainda não for encontrada nenhuma correspondência, o pacote será avaliado em relação à coleção de regras de infraestrutura. Se ainda não houver correspondência, então o pacote é negado por predefinição.

## <a name="nat-rules"></a>Regras de NAT

A conectividade de entrada pode ser habilitada Configurando DNAT (conversão de endereços de rede de destino) conforme descrito em [tutorial: filtrar o tráfego de entrada com o Firewall do Azure DNAT usando o portal do Azure](../firewall/tutorial-firewall-dnat.md). As regras DNAT são aplicadas primeiro. Se uma correspondência for encontrada, uma regra de rede implícita correspondente para permitir o tráfego traduzido será adicionada. Pode substituir esse comportamento, ao adicionar explicitamente uma coleção de regras de rede com regras de negar que correspondem ao tráfego traduzido. Nenhuma regra de aplicativo é aplicada a essas conexões.

## <a name="inherited-rules"></a>Regras herdadas

As coleções de regras de rede herdadas de uma política pai sempre são priorizadas acima das coleções de regras de rede que são definidas como parte de sua nova política. A mesma lógica também se aplica às coleções de regras de aplicativo. No entanto, as coleções de regras de rede são sempre processadas antes das coleções de regras de aplicativo, independentemente da herança.

Por padrão, sua política herda o modo de inteligência contra ameaças da política pai. Você pode substituir isso definindo seu modo de inteligência contra ameaças para um valor diferente na página de configurações de política. Só é possível substituir por um valor mais estrito. Por exemplo, se a política pai estiver definida como *alerta somente*, você poderá configurar essa política local para *alertar e negar*, mas não poderá desativá-la.

## <a name="next-steps"></a>Passos seguintes

- [Saiba mais sobre a versão prévia do Gerenciador de firewall do Azure](overview.md)