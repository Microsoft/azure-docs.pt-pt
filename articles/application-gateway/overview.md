---
title: O que é o Gateway de Aplicação do Azure
description: Saiba como pode utilizar um gateway de aplicação do Azure para gerir o tráfego da Web para a sua aplicação.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: overview
ms.custom: mvc
ms.date: 08/26/2020
ms.author: victorh
ms.openlocfilehash: 4344cd38d9a58eec27c6202e81b8ef678a510681
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102176013"
---
# <a name="what-is-azure-application-gateway"></a>O que é o Gateway de Aplicação do Azure?

O Gateway de Aplicação do Azure é um balanceador de carga do tráfego da Web que lhe permite gerir o tráfego para as suas aplicações Web. Os balanceadores de carga tradicionais funcionam na camada de transporte (camada OSI 4 - TCP e UDP) e encaminham o tráfego com base no endereço IP de origem e porta, para uma porta e um endereço IP de destino.

O Application Gateway pode tomar decisões de encaminhamento com base em atributos adicionais de um pedido HTTP, por exemplo, caminho URI ou cabeçalhos de anfitrião. Por exemplo, pode encaminhar tráfego com base no URL de origem. Por isso, se `/images` estiver no URL de origem, pode encaminhar o tráfego para um conjunto específico de servidores (conhecido como agrupamento) configurado para imagens. Se `/video` estiver na URL, esse tráfego é encaminhado para outra piscina que está otimizada para vídeos.

![imageURLroute](./media/application-gateway-url-route-overview/figure1-720.png)

Este tipo de encaminhamento é conhecido como balanceamento de carga da camada de aplicação (camada OSI 7). O Gateway de Aplicação do Azure pode fazer encaminhamento com base no URL e muito mais.

>[!NOTE]
> O Azure oferece um conjunto de soluções de balanceamento de carga totalmente geridas para os seus cenários. 
> * Se estiver a procurar fazer o encaminhamento global baseado em DNS e **não** tiver requisitos para a rescisão do protocolo de Segurança da Camada de Transporte (TLS) ("descarga SSL"), por pedido HTTP/HTTPS ou processamento de camadas de aplicação, reveja [o Gestor de Tráfego.](../traffic-manager/traffic-manager-overview.md) 
> * Se precisar de otimizar o encaminhamento global do seu tráfego web e otimizar o desempenho e fiabilidade do utilizador final de topo através de uma rápida falha global, consulte [a Porta da Frente](../frontdoor/front-door-overview.md).
> * Para equilibrar a carga da camada de rede, reveja [o Balanceador de Carga](../load-balancer/load-balancer-overview.md). 
> 
> Os seus cenários de ponta a ponta podem beneficiar de combinar estas soluções conforme necessário.
> Para uma comparação de opções de equilíbrio de carga Azure, consulte [a visão geral das opções de equilíbrio de carga em Azure](/azure/architecture/guide/technology-choices/load-balancing-overview).


## <a name="features"></a>Funcionalidades

Para saber mais sobre as funcionalidades do Gateway de Aplicações, consulte [as funcionalidades do Gateway de Aplicações Azure](features.md).

## <a name="pricing-and-sla"></a>Preços e SLA

Para obter informações sobre preços do Gateway de aplicação, consulte [os preços do Gateway de Aplicação](https://azure.microsoft.com/pricing/details/application-gateway/).

Para obter informações sobre o Gateway SLA, consulte [o Gateway SLA](https://azure.microsoft.com/support/legal/sla/application-gateway/v1_2/)da aplicação .

## <a name="whats-new"></a>Novidades

Para saber quais as novidades com o Azure Application Gateway, consulte as atualizações do [Azure](https://azure.microsoft.com/updates/?category=networking&query=Application%20Gateway).

## <a name="next-steps"></a>Passos seguintes

Dependendo dos seus requisitos e ambiente, pode criar um Portal de Aplicação de teste utilizando o portal Azure PowerShell ou O Azure CLI.

- [Início Rápido: Direcionar tráfego da Web com o Gateway de Aplicação do Azure - portal do Azure](quick-create-portal.md)
- [Início Rápido: Direcionar tráfego da Web com o Gateway de Aplicação do Azure - Azure PowerShell](quick-create-powershell.md)
- [Início Rápido: Direcionar tráfego da Web com o Gateway de Aplicação do Azure - CLI do Azure](quick-create-cli.md)