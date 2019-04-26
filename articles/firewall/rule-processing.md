---
title: Lógica de processamento de regra de Firewall do Azure
description: Saiba mais sobre a lógica de processamento de regra de Firewall do Azure
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 9/27/2018
ms.author: victorh
ms.openlocfilehash: 12d86793c0d75413559aad77c558c4adb7ac91af
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60193650"
---
# <a name="azure-firewall-rule-processing-logic"></a>Lógica de processamento de regra de Firewall do Azure
Firewall do Azure tem regras NAT, regras de rede e regras de aplicações. As regras são processadas, de acordo com o tipo de regra.


## <a name="network-rules-and-applications-rules"></a>Regras de rede e regras de aplicações 
Regras de rede são regras aplicadas, primeiro, então a aplicação. As regras são de terminação. Portanto, se uma correspondência for encontrada nas regras de rede, em seguida, regras de aplicações não são processadas.  Se não houver uma correspondência de regra de rede e o protocolo do pacote for HTTP/HTTPS, o pacote é então avaliado pelas regras de aplicação. Se ainda não for encontrada, o pacote é avaliado em relação a coleção de regras de infraestrutura. Se ainda não houver correspondência, então o pacote é negado por predefinição.

## <a name="nat-rules"></a>Regras de NAT
Conectividade de entrada pode ser ativada ao configurar a tradução de endereços de rede de destino (DNAT) conforme descrito em [Tutorial: Filtrar o tráfego de entrada com DNAT de Firewall do Azure no portal do Azure](tutorial-firewall-dnat.md). Regras DNAT são aplicadas pela primeira vez. Se for encontrada uma correspondência, é adicionada uma regra implícita de rede correspondente para permitir o tráfego traduzido. Pode substituir esse comportamento, ao adicionar explicitamente uma coleção de regras de rede com regras de negar que correspondem ao tráfego traduzido. Não existem regras de aplicações são aplicadas para que estas ligações.


## <a name="next-steps"></a>Passos Seguintes

- Saiba como [implementar e configurar uma Firewall de Azure](tutorial-firewall-deploy-portal.md).