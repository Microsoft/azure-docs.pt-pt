---
title: Tráfego seguro entre o Gateway de Aplicações e piscinas de backend
titleSuffix: Azure Virtual WAN
description: Cenários para o encaminhamento - tráfego seguro viajando através de um gateway de aplicação implantado em um VNet falado ligado a um hub virtual WAN seguro.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 04/12/2021
ms.author: cherylmc
ms.openlocfilehash: d9cb1251b90cf1c928f8286072bcd91e5ddf767e
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107315673"
---
# <a name="scenario-secure-traffic-between-application-gateway-and-backend-pools"></a>Cenário: Tráfego seguro entre o Gateway de Aplicações e piscinas de backend

Ao trabalhar com o encaminhamento virtual do hub virtual WAN, existem alguns cenários disponíveis. Neste cenário, o tráfego de um utilizador entra em Azure através de um gateway de aplicação implantado num VNet falado que está ligado a um hub virtual WAN seguro (centro VIRTUAL WAN com uma Firewall Azure). O objetivo é usar o Azure Firewall no centro virtual seguro para inspecionar o tráfego entre o gateway de aplicação e as piscinas de backend.

Existem dois padrões de design específicos neste cenário, dependendo se o gateway de aplicação e piscinas de backend estão no mesmo VNet, ou VNets diferentes.

* **Cenário 1:** O gateway de aplicações e as piscinas de backend estão na mesma rede virtual espreitada para um hub Virtual WAN (sub-redes separadas).
* **Cenário 2:** O gateway de aplicações e piscinas de backend estão em diferentes redes virtuais espreitadas para um hub Virtual WAN.

## <a name="scenario-1---same-vnet"></a><a name="scenario-1"></a>Cenário 1 - Mesmo VNet

Neste cenário, o gateway de aplicações e as piscinas de backend estão na mesma rede virtual espreitada para um hub Virtual WAN (sub-redes separadas).

:::image type="content" source="./media/routing-scenarios/secured-application-gateway/same-vnet.png" alt-text="Diagrama para o Cenário 1." lightbox="./media/routing-scenarios/secured-application-gateway/same-vnet.png":::

### <a name="workflow"></a>Fluxo de trabalho

Atualmente, as rotas que são publicitadas a partir da tabela de rotas VIRTUAL WAN para redes virtuais faladas são aplicadas a toda a rede virtual, e não nas sub-redes do VNet falado. Como resultado, as rotas definidas pelo utilizador são necessárias para permitir este cenário. Para obter informações sobre as rotas definidas pelo utilizador (UDR), consulte [as rotas personalizadas da Rede Virtual.](../virtual-network/virtual-networks-udr-overview.md#user-defined)


1. No Azure Firewall Manager, na rede virtual falada que contém o gateway de aplicações e piscinas de backend, selecione **Enable Secure Internet Traffic** e Enable Secure Private **Traffic**.
1. Configure as rotas definidas pelo utilizador (UDRs) na sub-rede do gateway de aplicação.

   * Para garantir que o gateway de aplicações é capaz de enviar tráfego diretamente para a Internet, especifique o seguinte UDR:

     * **Prefixo do endereço:** 0.0.0.0.0/0
     * **Próximo salto:** Internet

   * Para garantir que o gateway de aplicações é capaz de enviar tráfego para a piscina de backend através do Azure Firewall no hub Virtual WAN, especifique o seguinte UDR:

      * **Prefixo de endereço:** Sub-rede de piscina de backend (10.2.0.0/24)
      * **Próximo salto:** IP privado Azure Firewall

1. Configure uma rota definida pelo utilizador (UDR) na sub-rede do pool backend.

   * **Prefixo de endereço:** Sub-rede gateway de aplicação
   * **Próximo salto:** IP privado Azure Firewall

## <a name="scenario-2---different-vnets"></a><a name="scenario-2"></a>Cenário 2 - VNets diferentes

Neste cenário, as portas de aplicação e as piscinas de backend estão em diferentes redes virtuais que são espreitadas para um hub VIRTUAL WAN.

:::image type="content" source="./media/routing-scenarios/secured-application-gateway/different-vnets.png" alt-text="Diagrama para o Cenário 2." lightbox="./media/routing-scenarios/secured-application-gateway/different-vnets.png":::

### <a name="workflow"></a>Fluxo de trabalho

Atualmente, as rotas que são publicitadas a partir da tabela de rotas VIRTUAL WAN para redes virtuais faladas são aplicadas a toda a rede virtual, e não nas sub-redes do VNet falado. Como resultado, as rotas definidas pelo utilizador são necessárias para permitir este cenário. Para obter informações sobre as rotas definidas pelo utilizador (UDR), consulte [as rotas personalizadas da Rede Virtual.](../virtual-network/virtual-networks-udr-overview.md#user-defined)

1. No **Azure Firewall Manager**, selecione Ative Secure Internet **Traffic** e Enable Secure **Private Traffic** em ambas as redes virtuais faladas.

1. Configure as rotas definidas pelo utilizador (UDRs) na sub-rede do gateway de aplicação. Para garantir que o gateway de aplicações é capaz de enviar tráfego diretamente para a Internet, especifique o seguinte UDR:

   * **Prefixo do endereço:** 0.0.0.0.0/0
   * **Próximo salto:** Internet

## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre o encaminhamento de hubs virtuais, consulte [sobre o encaminhamento do hub virtual](about-virtual-hub-routing.md).
* Para obter mais informações sobre as rotas definidas pelo utilizador, consulte [as rotas personalizadas da Rede Virtual.](../virtual-network/virtual-networks-udr-overview.md#user-defined)
* Para obter informações sobre os centros virtuais seguros WAN, consulte [os centros virtuais seguros.](../firewall-manager/secured-virtual-hub.md)
