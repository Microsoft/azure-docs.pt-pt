---
title: 'Azure ExpressRoute: Pré-requisitos'
description: Esta página fornece uma lista de requisitos a serem satisfeitos para que possa ordenar um circuito do ExpressRoute do Azure. Inclui uma lista de verificação.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: cherylmc
ms.openlocfilehash: a72eba9bde0745e66bdf8e7efd8eaec7d6a0b186
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74083361"
---
# <a name="expressroute-prerequisites--checklist"></a>Pré-requisitos e lista de verificação do ExpressRoute
Para ligar a serviços cloud da Microsoft com o ExpressRoute, terá de verificar se foram cumpridos os seguintes requisitos listados nas secções abaixo.

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

## <a name="azure-account"></a>Conta do Azure
* Uma conta ativa do Microsoft Azure. Esta conta é necessária para configurar o circuito do ExpressRoute. Os circuitos do ExpressRoute são recursos incluídos nas subscrições do Azure. Uma subscrição Azure é um requisito mesmo que a conectividade esteja limitada a serviços de nuvem não Azure Microsoft, como o Office 365.
* Uma subscrição ativa do Office 365 (se utilizar os serviços do Office 365). Para obter mais informações, consulte a secção Requisitos específicos do Office 365 deste artigo.

## <a name="connectivity-provider"></a>Fornecedor de conectividade

* Pode trabalhar com um [Parceiro de conectividade do ExpressRoute](expressroute-locations.md#partners) para se ligar à nuvem da Microsoft. Pode configurar uma ligação entre a sua rede no local e a Microsoft de [três modos](expressroute-introduction.md).
* Se o seu fornecedor não for um parceiro de conectividade do ExpressRoute, ainda pode ligar-se à nuvem da Microsoft através de um [fornecedor do Exchange na nuvem](expressroute-locations.md#connectivity-through-exchange-providers).

## <a name="network-requirements"></a>Requisitos da rede
* **Redundância em cada local**de observação : A Microsoft exige que sejam configuradas sessões de BGP redundantes entre os routers da Microsoft e os routers de pares em cada circuito ExpressRoute (mesmo quando se tem apenas uma [ligação física a uma troca](expressroute-faqs.md#onep2plink)de nuvens).
* **Redundância para recuperação**de desastres : A Microsoft recomenda vivamente que instale pelo menos dois circuitos ExpressRoute em diferentes locais de observação para evitar um único ponto de falha.
* **Encaminhamento**: dependendo da forma como se conecta ao Microsoft Cloud, você ou o seu fornecedor precisa de configurar e gerir as sessões de BGP para [domínios de encaminhamento](expressroute-circuit-peerings.md). Alguns fornecedores de conectividade Ethernet ou fornecedores de cloud exchange podem oferecer a gestão de BGP como um serviço de valor acrescentado.
* **NAT**: a Microsoft só aceita endereços IP públicos através do peering da Microsoft. Se estiver a utilizar endereços IP privados na sua rede no local, o seu fornecedor tem de traduzir os endereços IP privados para os endereços IP públicos [utilizando o NAT](expressroute-nat.md).
* **QoS**: o Skype para Empresas tem vários serviços (por exemplo, voz, vídeo, texto) que exigem um tratamento QoS diferenciado. O utilizador e o fornecedor devem cumprir os [Requisitos de QoS](expressroute-qos.md).
* **Segurança de Rede**: tenha em conta a [segurança de rede](../best-practices-network-security.md) ao ligar à Microsoft Cloud através do ExpressRoute.

## <a name="office-365"></a>Office 365
Se planear ativar o Office 365 no ExpressRoute, reveja os seguintes documentos para obter mais informações acerca dos requisitos do Office 365.

* [Descrição geral do ExpressRoute para o Office 365](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd)
* [Encaminhamento com ExpressRoute para o Office 365](https://support.office.com/article/Routing-with-ExpressRoute-for-Office-365-e1da26c6-2d39-4379-af6f-4da213218408)
* [Elevada disponibilidade e ativação pós-falha com o ExpressRoute](https://aka.ms/erhighavailability)
* [Intervalos de endereços IP e URLs do Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)
* [Planeamento de rede e otimização de desempenho para o Office 365](https://support.office.com/article/Network-planning-and-performance-tuning-for-Office-365-e5f1228c-da3c-4654-bf16-d163daee8848)
* [Calculadoras e ferramentas de largura de banda de rede](https://support.office.com/article/Network-and-migration-planning-for-Office-365-f5ee6c33-bcd7-4b0b-b0f8-dc1d9fb8d132)
* [Integração do Office 365 com ambientes no local](https://support.office.com/article/Office-365-integration-with-on-premises-environments-263faf8d-aa21-428b-aed3-2021837a4b65)
* [Vídeos de formação de avançada do ExpressRoute no Office 365](https://channel9.msdn.com/series/aer/)

## <a name="next-steps"></a>Passos seguintes
* Para mais informações sobre o ExpressRoute, consulte o [ExpressRoute FAQ](expressroute-faqs.md).
* Localizar um fornecedor de conectividade do ExpressRoute. Veja [Parceiros e localizações de peering do ExpressRoute ](expressroute-locations.md).
* Veja os requisitos de [Encaminhamento](expressroute-routing.md), [NAT](expressroute-nat.md) e [QoS](expressroute-qos.md).
* Configurar a ligação do ExpressRoute.
  * [Criar um circuito do ExpressRoute](expressroute-howto-circuit-arm.md)
  * [Configure o encaminhamento](expressroute-howto-routing-arm.md)
  * [Ligar uma VNet a um circuito do ExpressRoute](expressroute-howto-linkvnet-arm.md)
