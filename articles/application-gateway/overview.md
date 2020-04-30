---
title: O que é o Gateway de Aplicação do Azure
description: Saiba como pode utilizar um gateway de aplicação do Azure para gerir o tráfego da Web para a sua aplicação.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: overview
ms.custom: mvc
ms.date: 03/04/2020
ms.author: victorh
ms.openlocfilehash: 4a4395801218409fe77d1081689ba80b495fcfad
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "78302581"
---
# <a name="what-is-azure-application-gateway"></a>O que é o Gateway de Aplicação do Azure?

O Gateway de Aplicação do Azure é um balanceador de carga do tráfego da Web que lhe permite gerir o tráfego para as suas aplicações Web. Os balanceadores de carga tradicionais funcionam na camada de transporte (camada OSI 4 - TCP e UDP) e encaminham o tráfego com base no endereço IP de origem e porta, para uma porta e um endereço IP de destino.

O Application Gateway pode tomar decisões de encaminhamento com base em atributos adicionais de um pedido HTTP, por exemplo, caminho URI ou cabeçalhos hospedeiros. Por exemplo, pode encaminhar tráfego com base no URL de origem. Por isso, se `/images` estiver no URL de origem, pode encaminhar o tráfego para um conjunto específico de servidores (conhecido como agrupamento) configurado para imagens. Se `/video` estiver no URL, o tráfego é encaminhado para outra piscina otimizada para vídeos.

![imageURLroute](./media/application-gateway-url-route-overview/figure1-720.png)

Este tipo de encaminhamento é conhecido como balanceamento de carga da camada de aplicação (camada OSI 7). O Gateway de Aplicação do Azure pode fazer encaminhamento com base no URL e muito mais.

>[!NOTE]
> O Azure oferece um conjunto de soluções de balanceamento de carga totalmente geridas para os seus cenários. Se precisa de alto desempenho, baixa latência, equilíbrio de carga camada 4, veja [o que é o Azure Load Balancer?](../load-balancer/load-balancer-overview.md) Se procura o equilíbrio global de carga slédi, veja o que é o Gestor de [Tráfego?](../traffic-manager/traffic-manager-overview.md) Os seus cenários de ponta a ponta podem beneficiar da combinação destas soluções.
>
> Para uma comparação de opções de equilíbrio de carga Azure, consulte [a visão geral das opções de equilíbrio de carga em Azure](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview).

## <a name="features"></a>Funcionalidades

Para saber mais sobre as funcionalidades do Application Gateway, consulte as funcionalidades do Gateway da [Aplicação Do Azure.](features.md)

## <a name="pricing-and-sla"></a>Preços e SLA

Para informações sobre preços de Gateway de aplicação, consulte preços de Gateway de [aplicação](https://azure.microsoft.com/pricing/details/application-gateway/).

Para informações sobre Gateway De aplicação SLA, consulte [Application Gateway SLA](https://azure.microsoft.com/support/legal/sla/application-gateway/v1_2/).

## <a name="next-steps"></a>Passos seguintes

Dependendo dos seus requisitos e ambiente, pode criar um Gateway de aplicação de teste utilizando o portal Azure, Azure PowerShell ou Azure CLI.

- [Início Rápido: Direcionar tráfego da Web com o Gateway de Aplicação do Azure - portal do Azure](quick-create-portal.md)
- [Início Rápido: Direcionar tráfego da Web com o Gateway de Aplicação do Azure - Azure PowerShell](quick-create-powershell.md)
- [Início Rápido: Direcionar tráfego da Web com o Gateway de Aplicação do Azure - CLI do Azure](quick-create-cli.md)
