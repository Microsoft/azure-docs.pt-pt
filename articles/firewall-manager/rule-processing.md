---
title: Lógica de processamento de regras do Azure Firewall Manager
description: Saiba mais sobre a lógica de processamento de regras do Azure Firewall
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: conceptual
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: 9184bf7baa85420e067edb4c0aafccb7e6711225
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "86512185"
---
# <a name="azure-firewall-rule-processing-logic"></a>Lógica de processamento de regras do Azure Firewall

O Azure Firewall tem regras NAT, regras de rede e regras de aplicações. As regras são processadas de acordo com o tipo de regra.

## <a name="network-rules-and-applications-rules"></a>Regras de rede e regras de aplicações

As regras de rede são aplicadas primeiro, depois as regras de aplicação. As regras estão a acabar. Portanto, se uma correspondência for encontrada nas regras da rede, então as regras de aplicação não são processadas.  Se nenhuma regra de rede corresponder, e se o protocolo do pacote for HTTP/HTTPS, o pacote é então avaliado pelas regras de aplicação. Se ainda não for encontrado correspondência, o pacote é avaliado contra a recolha da regra da infraestrutura. Se ainda não houver correspondência, então o pacote é negado por defeito.

## <a name="nat-rules"></a>Regras NAT

A conectividade de entrada pode ser ativada configurando a tradução de endereços de rede de destino (DNAT) como descrito no [Tutorial: Filtrar o tráfego de entrada com DNAT Azure Firewall utilizando o portal Azure](../firewall/tutorial-firewall-dnat.md). As regras do DNAT são aplicadas primeiro. Se for encontrada uma correspondência, é adicionada uma regra implícita de rede correspondente para permitir o tráfego traduzido. Pode substituir esse comportamento, ao adicionar explicitamente uma coleção de regras de rede com regras de negar que correspondem ao tráfego traduzido. Não são aplicadas regras de candidatura para estas ligações.

## <a name="inherited-rules"></a>Regras herdadas

As coleções de regras de rede herdadas de uma política de pais são sempre prioritárias acima das coleções de regras de rede que são definidas como parte da sua nova política. A mesma lógica aplica-se também às coleções de regras de aplicação. No entanto, as coleções de regras de rede são sempre processadas antes das recolhas de regras de aplicação, independentemente da herança.

Por defeito, a sua política herda o modo de inteligência de ameaça de ameaça de mãe. Pode sobrepor-se a isto definindo o modo de inteligência de ameaça para um valor diferente na página de definições de política. Só é possível anular com um valor mais rigoroso. Por exemplo, se a política dos pais estiver definida apenas para *Alerta,* pode configurar esta política local para *Alertar e negar*, mas não pode desligá-la.

## <a name="next-steps"></a>Passos seguintes

- [Saiba mais sobre o Azure Firewall Manager](overview.md)