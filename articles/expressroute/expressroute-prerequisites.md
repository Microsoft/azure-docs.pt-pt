---
title: 'Pré-requisitos - ExpressRoute: Azure | Microsoft Docs'
description: Esta página fornece uma lista de requisitos a serem satisfeitos para que possa ordenar um circuito do ExpressRoute do Azure. Ele inclui uma lista de verificação.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: mialdrid
ms.custom: seodec18
ms.openlocfilehash: afe8d3971a51d57498e3e32b7e1cf5bf5a3263d6
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58295203"
---
# <a name="expressroute-prerequisites--checklist"></a>Pré-requisitos e lista de verificação do ExpressRoute
Para ligar a serviços cloud da Microsoft com o ExpressRoute, terá de verificar se foram cumpridos os seguintes requisitos listados nas secções abaixo.

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

## <a name="azure-account"></a>Conta do Azure
* Uma conta ativa do Microsoft Azure. Esta conta é necessária para configurar o circuito do ExpressRoute. Os circuitos do ExpressRoute são recursos incluídos nas subscrições do Azure. Uma subscrição do Azure é um requisito, mesmo se a conectividade estiver limitada a serviços cloud não pertencentes ao Microsoft Azure, como serviços do Office 365 e do Dynamics 365.
* Uma subscrição ativa do Office 365 (se utilizar os serviços do Office 365). Para obter mais informações, consulte a secção de requisitos específicos do Office 365 deste artigo.

## <a name="connectivity-provider"></a>Fornecedor de conectividade

* Pode trabalhar com um [Parceiro de conectividade do ExpressRoute](expressroute-locations.md#partners) para se ligar à nuvem da Microsoft. Pode configurar uma ligação entre a sua rede no local e a Microsoft de [três modos](expressroute-introduction.md).
* Se o seu fornecedor não for um parceiro de conectividade do ExpressRoute, ainda pode ligar-se à nuvem da Microsoft através de um [fornecedor do Exchange na nuvem](expressroute-locations.md#connectivity-through-exchange-providers).

## <a name="network-requirements"></a>Requisitos da rede
* **Redundância em cada localização de peering**: A Microsoft requer sessões de BGP redundantes para ser configurado entre routers da Microsoft e routers de peering em cada circuito ExpressRoute (mesmo quando tem apenas [uma ligação física a uma troca de cloud](expressroute-faqs.md#onep2plink)).
* **Redundância para recuperação após desastre**: A Microsoft recomenda que configurar, pelo menos, dois circuitos do ExpressRoute em diferentes localizações de peering para evitar um ponto único de falha.
* **Encaminhamento**: consoante a forma como ligar a Cloud da Microsoft, utilizador ou o fornecedor tem de configurar e gerir as sessões de BGP para [domínios de encaminhamento](expressroute-circuit-peerings.md). Alguns fornecedores de conectividade Ethernet ou fornecedores do exchange na nuvem poderão oferecer gestão de BGP como um serviço de valor acrescentado.
* **NAT**: Microsoft só aceita endereços IP públicos através do peering da Microsoft. Se estiver a utilizar endereços IP privados na sua rede no local, utilizador ou o fornecedor tem de traduzir os endereços IP privados para os endereços IP públicos [com o NAT](expressroute-nat.md).
* **QoS**: Skype para empresas tem vários serviços (por exemplo, voz, vídeo, texto) que requerem tratamento do QoS diferenciado. O utilizador e o fornecedor devem cumprir os [Requisitos de QoS](expressroute-qos.md).
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

## <a name="dynamics-365"></a>Dynamics 365
Se planear ativar o Dynamics 365 no ExpressRoute, reveja os seguintes documentos para obter mais informações acerca do Dynamics 365

* [Documento técnico do Dynamics 365 e ExpressRoute](https://download.microsoft.com/download/B/2/8/B2896B38-9832-417B-9836-9EF240C0A212/Microsoft%20Dynamics%20365%20and%20ExpressRoute.pdf)
* [URL](https://support.microsoft.com/kb/2655102) e [intervalos de endereços de IP do Dynamics 365](https://support.microsoft.com/kb/2728473)

## <a name="next-steps"></a>Passos Seguintes
* Para obter mais informações acerca do ExpressRoute, veja as [FAQs do ExpressRoute](expressroute-faqs.md).
* Localizar um fornecedor de conectividade do ExpressRoute. Veja [Parceiros e localizações de peering do ExpressRoute ](expressroute-locations.md).
* Veja os requisitos de [Encaminhamento](expressroute-routing.md), [NAT](expressroute-nat.md) e [QoS](expressroute-qos.md).
* Configurar a ligação do ExpressRoute.
  * [Crie um circuito do ExpressRoute](expressroute-howto-circuit-arm.md)
  * [Configure o encaminhamento](expressroute-howto-routing-arm.md)
  * [Ligar uma VNet a um circuito do ExpressRoute](expressroute-howto-linkvnet-arm.md)
