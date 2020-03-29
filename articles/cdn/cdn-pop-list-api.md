---
title: Recupere a lista pop IP atual para Azure CDN. Microsoft Docs
description: Saiba como recuperar a lista pop atual.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2019
ms.author: magattus
ms.custom: ''
ms.openlocfilehash: 95b85aa11d99ddd48c90c8d9fa28789e79ee979f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72299251"
---
# <a name="retrieve-the-current-pop-ip-list-for-azure-cdn"></a>Recupere a lista pop IP atual para Azure CDN

## <a name="retrieve-the-current-verizon-pop-ip-list-for-azure-cdn"></a>Recupere a lista IP da Verizon POP para Azure CDN

Pode utilizar a API REST para recuperar o conjunto de IPs para os servidores de ponto de presença (POP) da Verizon. Estes servidores POP fazem pedidos para servidores de origem que estão associados com pontos finais da Rede de Entrega de Conteúdo (CDN) azure num perfil Verizon **(Padrão Azure CDN da Verizon** ou **Azure CDN Premium da Verizon).** Note que este conjunto de IPs é diferente dos IPs que um cliente veria ao fazer pedidos aos POPs. 

Para a sintaxe da operação REST API para recuperar a lista POP, consulte [Edge Nodes - List](https://docs.microsoft.com/rest/api/cdn/edgenodes/list).

## <a name="retrieve-the-current-microsoft-pop-ip-list-for-azure-cdn"></a>Recupere a lista IP pop da Microsoft para Azure CDN

Para bloquear a sua aplicação para aceitar o tráfego apenas a partir do Azure CDN da Microsoft, terá de configurar OS ACLs IP para o seu backend. Também pode restringir o conjunto de valores aceites para o cabeçalho 'X-Forwarded-Host' enviado pelo Azure CDN da Microsoft. Estes passos são detalhados como abaixo:

Configure o IP ACLing para os seus backends para aceitar o tráfego do Azure CDN a partir do espaço de endereçoIP backend da Microsoft e apenas dos serviços de infraestrutura do Azure. 

* Azure CDN do espaço IP iPv4 da Microsoft: 147.243.0.0.0/16
* Azure CDN do espaço IP de backend IPv6 da Microsoft: 2a01:111:2050::/44

Gamas IP e etiquetas de serviço para serviços da Microsoft podem ser consultadas [aqui](https://www.microsoft.com/download/details.aspx?id=56519)


## <a name="typical-use-case"></a>Caso de utilização típica

Para fins de segurança, pode utilizar esta lista ip para impor que os pedidos ao seu servidor de origem são feitos apenas a partir de um Verizon POP válido. Por exemplo, se alguém descobrisse o nome de anfitrião ou o endereço IP para o servidor de origem de um ponto final da CDN, poderia fazer pedidos diretamente para o servidor de origem, contornando assim as capacidades de escala e segurança fornecidas pelo Azure CDN. Ao definir os IPs na lista devolvida como os únicos IPs permitidos num servidor de origem, este cenário pode ser evitado. Para garantir que tem a mais recente lista pop, recupere-a pelo menos uma vez por dia. 

## <a name="next-steps"></a>Passos seguintes

Para obter informações sobre a Rest API, consulte [Azure CDN REST API](https://docs.microsoft.com/rest/api/cdn/).
