---
title: 'Azure ExpressRoute: Pré-requisitos'
description: Esta página fornece uma lista de requisitos a serem satisfeitos para que possa ordenar um circuito do ExpressRoute do Azure. Inclui uma lista de verificação.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: duau
ms.openlocfilehash: 1fad1bca18d16ac3b6a654a3c289d0a14e3cd2e2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92204797"
---
# <a name="expressroute-prerequisites--checklist"></a>Pré-requisitos e lista de verificação do ExpressRoute
Para ligar a serviços cloud da Microsoft com o ExpressRoute, terá de verificar se foram cumpridos os seguintes requisitos listados nas secções abaixo.

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

## <a name="azure-account"></a>Conta do Azure
* Uma conta ativa do Microsoft Azure. Esta conta é necessária para configurar o circuito do ExpressRoute. Os circuitos do ExpressRoute são recursos incluídos nas subscrições do Azure. Uma subscrição do Azure é um requisito mesmo que a conectividade esteja limitada a serviços de cloud não-Azure Microsoft, como o Microsoft 365.
* Uma subscrição ativa do Microsoft 365 (se utilizar os serviços microsoft 365). Para mais informações, consulte a secção de requisitos específicos da Microsoft 365 deste artigo.

## <a name="connectivity-provider"></a>Fornecedor de conectividade

* Pode trabalhar com um [Parceiro de conectividade do ExpressRoute](expressroute-locations.md#partners) para se ligar à nuvem da Microsoft. Pode configurar uma ligação entre a sua rede no local e a Microsoft de [três modos](expressroute-introduction.md).
* Se o seu fornecedor não for um parceiro de conectividade do ExpressRoute, ainda pode ligar-se à nuvem da Microsoft através de um [fornecedor do Exchange na nuvem](expressroute-locations.md#connectivity-through-exchange-providers).

## <a name="network-requirements"></a>Requisitos de rede
* **Redundância em cada local de observação**: A Microsoft exige que sejam configuradas sessões de BGP redundantes entre os routers da Microsoft e os routers de pares em cada circuito ExpressRoute (mesmo quando tem apenas [uma ligação física a uma troca de nuvens).](expressroute-faqs.md#onep2plink)
* **Redundância para recuperação de desastres**: A Microsoft recomenda vivamente que crie pelo menos dois circuitos ExpressRoute em diferentes locais de observação para evitar um único ponto de falha.
* **Encaminhamento**: dependendo da forma como se conecta à Cloud microsoft, você ou o seu fornecedor precisa de configurar e gerir as sessões de BGP para [domínios de encaminhamento](expressroute-circuit-peerings.md). Alguns fornecedores de conectividade Ethernet ou fornecedores de intercâmbio em nuvem podem oferecer a gestão do BGP como um serviço de valor acrescentado.
* **NAT**: a Microsoft só aceita endereços IP públicos através do peering da Microsoft. Se estiver a utilizar endereços IP privados na sua rede de instalações, você ou o seu fornecedor precisa de traduzir os endereços IP privados para os endereços IP públicos [utilizando o NAT](expressroute-nat.md).
* **QoS**: o Skype para Empresas tem vários serviços (por exemplo, voz, vídeo, texto) que exigem um tratamento QoS diferenciado. O utilizador e o fornecedor devem cumprir os [Requisitos de QoS](expressroute-qos.md).
* **Segurança de Rede**: tenha em conta a [segurança de rede](/azure/cloud-adoption-framework/reference/networking-vdc) ao ligar à Microsoft Cloud através do ExpressRoute.

## <a name="microsoft-365"></a>Microsoft 365
Se planeia ativar o Microsoft 365 no ExpressRoute, reveja os seguintes documentos para obter mais informações sobre os requisitos da Microsoft 365.

* [Azure ExpressRoute para a Microsoft 365](/microsoft-365/enterprise/azure-expressroute)
* [Encaminhamento com ExpressRoute para Microsoft 365](/microsoft-365/enterprise/routing-with-expressroute)
* [Elevada disponibilidade e ativação pós-falha com o ExpressRoute](/microsoft-365/enterprise/network-planning-with-expressroute)
* [Gamas de endereços Microsoft 365 URLs e IP](/microsoft-365/enterprise/urls-and-ip-address-ranges)
* [Planeamento de rede e afinação de desempenho para o Microsoft 365](/microsoft-365/enterprise/network-planning-and-performance)
* [Plano de rede e migração para a Microsoft 365](/microsoft-365/enterprise/network-and-migration-planning)
* [Integração microsoft 365 com ambientes no local](/microsoft-365/enterprise/microsoft-365-integration)
* [Vídeos de formação de avançada do ExpressRoute no Office 365](https://channel9.msdn.com/series/aer/)

## <a name="next-steps"></a>Passos seguintes
* Para obter mais informações sobre o ExpressRoute, consulte as [FAQ ExpressRoute.](expressroute-faqs.md)
* Localizar um fornecedor de conectividade do ExpressRoute. Veja [Parceiros e localizações de peering do ExpressRoute ](expressroute-locations.md).
* Veja os requisitos de [Encaminhamento](expressroute-routing.md), [NAT](expressroute-nat.md) e [QoS](expressroute-qos.md).
* Configurar a ligação do ExpressRoute.
  * [Criar um circuito do ExpressRoute](expressroute-howto-circuit-arm.md)
  * [Configure o encaminhamento](expressroute-howto-routing-arm.md)
  * [Ligar uma VNet a um circuito do ExpressRoute](expressroute-howto-linkvnet-arm.md)