---
title: Lógica de processamento de regras do Azure Firewall
description: Conheça a lógica de processamento de regras da Firewall Azure
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: victorh
ms.openlocfilehash: 74e58c316651a1604984ac14c70a3a65d46d6d9f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73518206"
---
# <a name="azure-firewall-rule-processing-logic"></a>Lógica de processamento de regras do Azure Firewall

O Azure Firewall tem regras nat, regras de rede e regras de aplicações. As regras são processadas de acordo com o tipo de regra.

## <a name="network-rules-and-applications-rules"></a>Regras de rede e aplicações

As regras da rede são aplicadas primeiro, depois as regras de aplicação. As regras estão a acabar. Portanto, se um jogo for encontrado nas regras da rede, então as regras de aplicação não são processadas.  Se não houver uma correspondência de regra de rede e o protocolo do pacote for HTTP/HTTPS, o pacote é então avaliado pelas regras de aplicação. Se ainda não for encontrado um fósforo, o pacote é avaliado contra a coleção de regras de infraestrutura. Se ainda não houver correspondência, então o pacote é negado por predefinição.

## <a name="nat-rules"></a>Regras na NAT

A conectividade de entrada pode ser ativada através da configuração da Tradução de Endereços da Rede de Destino (DNAT), conforme descrito no [Tutorial: Filtrar o tráfego de entrada com o DNAT da Firewall Azure utilizando o portal Azure](../firewall/tutorial-firewall-dnat.md). As regras de ADN são aplicadas primeiro. Se for encontrada uma correspondência, é adicionada uma regra de rede correspondente implícita para permitir a adição do tráfego traduzido. Pode substituir esse comportamento, ao adicionar explicitamente uma coleção de regras de rede com regras de negar que correspondem ao tráfego traduzido. Não são aplicadas regras de aplicação para estas ligações.

## <a name="inherited-rules"></a>Regras herdadas

As coleções de regras de rede herdadas de uma política-mãe são sempre priorizadas acima das coleções de regras de rede que são definidas como parte da sua nova política. A mesma lógica aplica-se também às coleções de regras de aplicação. No entanto, as coleções de regras de rede são sempre processadas antes das coleções de regras de aplicação, independentemente da herança.

Por defeito, a sua política herda o modo de inteligência de ameaça de política dos pais. Pode anular isto definindo o seu modo de inteligência de ameaça para um valor diferente na página de definições de políticas. Só é possível anular com um valor mais rigoroso. Por exemplo, se a política dos pais estiver definida *apenas*para alertar, pode configurar esta política local para *alertar e negar,* mas não pode desligá-la.

## <a name="next-steps"></a>Passos seguintes

- [Saiba mais sobre a pré-visualização do Gestor de Firewall Azure](overview.md)