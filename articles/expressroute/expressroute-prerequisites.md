---
title: 'Pré-requisitos – ExpressRoute: Azure | Microsoft Docs'
description: Esta página fornece uma lista de requisitos a serem satisfeitos para que possa ordenar um circuito do ExpressRoute do Azure. Ele inclui uma lista de verificação.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 4ef1a768356078c160b798c1a96412adda0330f6
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/19/2019
ms.locfileid: "71123302"
---
# <a name="expressroute-prerequisites--checklist"></a>Pré-requisitos e lista de verificação do ExpressRoute
Para ligar a serviços cloud da Microsoft com o ExpressRoute, terá de verificar se foram cumpridos os seguintes requisitos listados nas secções abaixo.

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

## <a name="azure-account"></a>Conta do Azure
* Uma conta ativa do Microsoft Azure. Esta conta é necessária para configurar o circuito do ExpressRoute. Os circuitos do ExpressRoute são recursos incluídos nas subscrições do Azure. Uma assinatura do Azure é um requisito, mesmo se a conectividade estiver limitada a serviços de nuvem da Microsoft não Azure, como o Office 365.
* Uma subscrição ativa do Office 365 (se utilizar os serviços do Office 365). Para obter mais informações, consulte a seção requisitos específicos do Office 365 deste artigo.

## <a name="connectivity-provider"></a>Fornecedor de conectividade

* Pode trabalhar com um [Parceiro de conectividade do ExpressRoute](expressroute-locations.md#partners) para se ligar à nuvem da Microsoft. Pode configurar uma ligação entre a sua rede no local e a Microsoft de [três modos](expressroute-introduction.md).
* Se o seu fornecedor não for um parceiro de conectividade do ExpressRoute, ainda pode ligar-se à nuvem da Microsoft através de um [fornecedor do Exchange na nuvem](expressroute-locations.md#connectivity-through-exchange-providers).

## <a name="network-requirements"></a>Requisitos da rede
* **Redundância em cada local de emparelhamento**: A Microsoft exige que sessões BGP redundantes sejam configuradas entre os roteadores da Microsoft e os roteadores de emparelhamento em cada circuito do ExpressRoute (mesmo quando você tem apenas [uma conexão física com uma troca de nuvem](expressroute-faqs.md#onep2plink)).
* **Redundância para recuperação de desastres**: A Microsoft recomenda enfaticamente que você configure pelo menos dois circuitos de ExpressRoute em diferentes locais de emparelhamento para evitar um ponto único de falha.
* **Encaminhamento**: consoante a forma como ligar a Cloud da Microsoft, utilizador ou o fornecedor tem de configurar e gerir as sessões de BGP para [domínios de encaminhamento](expressroute-circuit-peerings.md). Alguns fornecedores de conectividade Ethernet ou fornecedores do exchange na nuvem poderão oferecer gestão de BGP como um serviço de valor acrescentado.
* **NAT**: A Microsoft só aceita endereços IP públicos por meio do emparelhamento da Microsoft. Se estiver a utilizar endereços IP privados na sua rede no local, utilizador ou o fornecedor tem de traduzir os endereços IP privados para os endereços IP públicos [com o NAT](expressroute-nat.md).
* **QoS**: O Skype for Business tem vários serviços (por exemplo, voz, vídeo, texto) que exigem tratamento de QoS diferenciado. O utilizador e o fornecedor devem cumprir os [Requisitos de QoS](expressroute-qos.md).
* **Segurança de Rede**: tenha em conta a [segurança de rede](../best-practices-network-security.md) ao ligar à Microsoft Cloud através do ExpressRoute.

## <a name="office-365"></a>Office 365
Se planear ativar o Office 365 no ExpressRoute, reveja os seguintes documentos para obter mais informações acerca dos requisitos do Office 365.

* [Descrição geral do ExpressRoute para o Office 365](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd)
* [Encaminhamento com o ExpressRoute para o Office 365](https://support.office.com/article/Routing-with-ExpressRoute-for-Office-365-e1da26c6-2d39-4379-af6f-4da213218408)
* [Elevada disponibilidade e ativação pós-falha com o ExpressRoute](https://aka.ms/erhighavailability)
* [Intervalos de endereços IP e URLs do Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)
* [Planeamento de rede e otimização de desempenho para o Office 365](https://support.office.com/article/Network-planning-and-performance-tuning-for-Office-365-e5f1228c-da3c-4654-bf16-d163daee8848)
* [Calculadoras e ferramentas de largura de banda de rede](https://support.office.com/article/Network-and-migration-planning-for-Office-365-f5ee6c33-bcd7-4b0b-b0f8-dc1d9fb8d132)
* [Integração do Office 365 com ambientes no local](https://support.office.com/article/Office-365-integration-with-on-premises-environments-263faf8d-aa21-428b-aed3-2021837a4b65)
* [Vídeos de formação de avançada do ExpressRoute no Office 365](https://channel9.msdn.com/series/aer/)

## <a name="next-steps"></a>Passos seguintes
* Para obter mais informações acerca do ExpressRoute, veja as [FAQs do ExpressRoute](expressroute-faqs.md).
* Localizar um fornecedor de conectividade do ExpressRoute. Veja [Parceiros e localizações de peering do ExpressRoute ](expressroute-locations.md).
* Veja os requisitos de [Encaminhamento](expressroute-routing.md), [NAT](expressroute-nat.md) e [QoS](expressroute-qos.md).
* Configurar a ligação do ExpressRoute.
  * [Crie um circuito do ExpressRoute](expressroute-howto-circuit-arm.md)
  * [Configure o encaminhamento](expressroute-howto-routing-arm.md)
  * [Ligar uma VNet a um circuito do ExpressRoute](expressroute-howto-linkvnet-arm.md)
