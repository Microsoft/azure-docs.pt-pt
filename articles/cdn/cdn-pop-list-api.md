---
title: Recupere a lista atual de POP IP para o Azure CDN. Microsoft Docs
description: Saiba como obter servidores POP utilizando a API REST. Os servidores POP fazem pedidos para servidores de origem associados aos pontos finais da Rede de Entrega de Conteúdos Azure.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2019
ms.author: allensu
ms.custom: ''
ms.openlocfilehash: 43b4bcaee447d84efa088e84340ccfc717fe2777
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96005185"
---
# <a name="retrieve-the-current-pop-ip-list-for-azure-cdn"></a>Recupere a lista ip pop atual para Azure CDN

## <a name="retrieve-the-current-verizon-pop-ip-list-for-azure-cdn"></a>Recupere a lista ip pop atual da Verizon para o Azure CDN

Pode utilizar a API REST para recuperar o conjunto de IPs para os servidores do ponto de presença de Verizon (POP). Estes servidores POP fazem pedidos para servidores de origem associados a pontos finais da Azure Content Delivery Network (CDN) num perfil Verizon **(Azure CDN Standard from Verizon** ou **Azure CDN Premium da Verizon).** Note que este conjunto de IPs é diferente dos IPs que um cliente veria ao fazer pedidos aos POPs. 

Para a sintaxe da operação REST API para recuperar a lista POP, consulte [Os Nós de Borda - Lista](/rest/api/cdn/edgenodes/list).

## <a name="retrieve-the-current-microsoft-pop-ip-list-for-azure-cdn"></a>Recupere a lista atual do Microsoft POP IP para O Azure CDN

Para bloquear a sua aplicação para aceitar o tráfego apenas a partir do Azure CDN da Microsoft, terá de configurar ACLs IP para o seu backend. Pode também restringir o conjunto de valores aceites para o cabeçalho 'X-Forwarded-Host' enviado pela Azure CDN da Microsoft. Estes passos são detalhados como abaixo:

Configure o IP ACLing para que os seus backends aceitem o tráfego da Azure CDN do espaço de endereço IP backend da Microsoft e apenas os serviços de infraestrutura do Azure. 

* Azure CDN do espaço IP de backend IPv4 da Microsoft: 147.243.0.0/16
* Azure CDN do espaço IP de backend IPv6 da Microsoft: 2a01:111:2050::/44

Para utilizar tags de serviço com Azure CDN da Microsoft, utilize a etiqueta Azure Front Door. As gamas IP e as tags de serviço para os serviços da Microsoft podem ser encontradas [aqui](https://www.microsoft.com/download/details.aspx?id=56519)


## <a name="typical-use-case"></a>Caso de utilização típica

Para fins de segurança, pode utilizar esta lista de IP para impor que os pedidos ao seu servidor de origem são feitos apenas a partir de um Verizon POP válido. Por exemplo, se alguém descobrisse o nome de anfitrião ou o endereço IP para o servidor de origem de um cdN, poderia fazer pedidos diretamente para o servidor de origem, contornando assim as capacidades de escala e segurança fornecidas pela Azure CDN. Ao definir os IPs na lista de devoluções como os únicos IPs permitidos num servidor de origem, este cenário pode ser evitado. Para garantir que tem a mais recente lista POP, recupere-a pelo menos uma vez por dia. 

## <a name="next-steps"></a>Passos seguintes

Para obter informações sobre a API REST, consulte [Azure CDN REST API](/rest/api/cdn/).