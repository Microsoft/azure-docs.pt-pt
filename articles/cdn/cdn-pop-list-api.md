---
title: Recuperar a lista de IP POP atual para a CDN do Azure | Microsoft Docs
description: Saiba como recuperar a lista POP atual.
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
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2019
ms.locfileid: "72299251"
---
# <a name="retrieve-the-current-pop-ip-list-for-azure-cdn"></a>Recuperar a lista de IP POP atual para a CDN do Azure

## <a name="retrieve-the-current-verizon-pop-ip-list-for-azure-cdn"></a>Recuperar a lista de IPS POP do Verizon atual para a CDN do Azure

Você pode usar a API REST para recuperar o conjunto de IPs para servidores de ponto de presença (POP) da Verizon. Esses servidores POP fazem solicitações aos servidores de origem associados aos pontos de extremidade da CDN (rede de distribuição de conteúdo) do Azure em um perfil Verizon (**Azure CDN Standard da Verizon** ou **CDN Premium do Azure da Verizon**). Observe que esse conjunto de IPs é diferente dos IPs que um cliente veria ao fazer solicitações para os POPs. 

Para obter a sintaxe da operação da API REST para recuperar a lista POP, consulte [nós de borda – lista](https://docs.microsoft.com/rest/api/cdn/edgenodes/list).

## <a name="retrieve-the-current-microsoft-pop-ip-list-for-azure-cdn"></a>Recuperar a lista de IP POP atual da Microsoft para a CDN do Azure

Para bloquear seu aplicativo para aceitar o tráfego somente da CDN do Azure da Microsoft, você precisará configurar ACLs de IP para seu back-end. Você também pode restringir o conjunto de valores aceitos para o cabeçalho ' X-Forwarded-host ' enviado pela CDN do Azure da Microsoft. Essas etapas são detalhadas como a seguir:

Configure o IP atuação para seus back-ends para aceitar o tráfego da CDN do Azure do espaço de endereço IP de back-end da Microsoft e dos serviços de infraestrutura do Azure apenas. 

* CDN do Azure do espaço IP de back-end IPv4 da Microsoft: 147.243.0.0/16
* CDN do Azure do espaço IP de back-end IPv6 da Microsoft: 2a01:111:2050::/44

Os intervalos de IP e as marcas de serviço para os serviços da Microsoft podem ser encontrados [aqui](https://www.microsoft.com/download/details.aspx?id=56519)


## <a name="typical-use-case"></a>Caso de utilização típica

Para fins de segurança, você pode usar essa lista de IPS para impor que as solicitações para seu servidor de origem sejam feitas somente de um POP de Verizon válido. Por exemplo, se alguém descobriu o nome de host ou endereço IP para o servidor de origem de um ponto de extremidade CDN, ele pode fazer solicitações diretamente para o servidor de origem, ignorando, portanto, os recursos de dimensionamento e segurança fornecidos pela CDN do Azure. Ao definir os IPs na lista retornada como os únicos IPs permitidos em um servidor de origem, esse cenário pode ser impedido. Para garantir que você tenha a lista de POP mais recente, recupere-a pelo menos uma vez por dia. 

## <a name="next-steps"></a>Passos seguintes

Para obter informações sobre a API REST, consulte [API REST da CDN do Azure](https://docs.microsoft.com/rest/api/cdn/).
