---
title: 'Azure ExpressRoute: circuitos e observação'
description: Esta página fornece uma visão geral dos circuitos do ExpressRoute e domínios de encaminhamento/peering.
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: mialdrid
ms.openlocfilehash: c68ffd019937f902567c3deda8d879448dc082da
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78356600"
---
# <a name="expressroute-circuits-and-peering"></a>Circuitos do ExpressRoute e de peering

Circuitos do ExpressRoute ligar a sua infraestrutura no local para a Microsoft através de um fornecedor de conectividade. Este artigo ajuda-o a compreender os circuitos do ExpressRoute e domínios de encaminhamento/peering. A figura seguinte mostra uma representação lógica de conectividade entre a sua WAN e o Microsoft.

![](./media/expressroute-circuit-peerings/expressroute-basic.png)

> [!IMPORTANT]
> O público azure foi depreciado e não está disponível para novos circuitos ExpressRoute. Os novos circuitos suportam o peering da Microsoft e o peering privado.  
>

## <a name="circuits"></a>Circuitos ExpressRoute

Um circuito do ExpressRoute representa uma conexão lógica entre a infraestrutura no local e serviços cloud da Microsoft através de um fornecedor de conectividade. É possível pedir vários circuitos do ExpressRoute. Cada circuito pode estar as mesmas ou em diferentes regiões e pode ser ligado a local através de fornecedores de conectividade diferentes.

Circuitos do ExpressRoute não mapeiam para qualquer entidades físicas. Um circuito é exclusivamente identificado por um padrão chamado de GUID como uma chave de serviço (s-chave). A chave de serviço é a única parte das informações trocadas entre a Microsoft, o fornecedor de conectividade e o utilizador. A tecla s não é um segredo por motivos de segurança. Existe um mapeamento 1:1 entre um circuito do ExpressRoute e a tecla s.

Novos circuitos do ExpressRoute podem incluir duas peerings independentes: peering privado e peering da Microsoft. Ao passo que os circuitos do ExpressRoute existentes podem conter três peerings: pública do Azure, Azure privado e Microsoft. Cada peering é um par de sessões BGP independentes, cada um adquiria configurada para elevada disponibilidade. Existe um 1: n (1 < = N < = 3) de mapeamento entre um circuito do ExpressRoute e domínios de encaminhamento. Um circuito do ExpressRoute pode ter qualquer um, dois ou todos os três peerings ativados por circuito ExpressRoute.

Cada circuito tem uma largura de banda fixa (50 Mbps, 100 Mbps, 200 Mbps, 500 Mbps, 1 Gbps, 10 Gbps) e é mapeado para um fornecedor de conectividade e uma localização de peering. Selecionar a largura de banda é partilhada entre todos os peerings de circuito

### <a name="quotas"></a>Quotas, limites e limitações

Limites e quotas predefinidas aplicam-se para cada circuito ExpressRoute. Consulte a página [de Limites de Subscrição e Serviço do Azure, Quotas e Restrições](../azure-resource-manager/management/azure-subscription-service-limits.md) para obter informações atualizadas sobre quotas.

## <a name="routingdomains"></a>Peering ExpressRoute

Um circuito do ExpressRoute tem vários encaminhamento domínios/peerings associados à mesma: Azure privado do Azure público e Microsoft. Cada peering está configurado de forma idêntica num par de routers (no ativo-ativo ou a partilha de carga configuration) para elevada disponibilidade. Os serviços azure são classificados como *azure público* e *azure privado* para representar os esquemas de endereçamento IP.

![](./media/expressroute-circuit-peerings/expressroute-peerings.png)

### <a name="privatepeering"></a>Peering privado azure

Serviços, ou seja, as máquinas virtuais (IaaS) de computação do Azure e serviços cloud (PaaS), que são implementados numa rede virtual podem ser ligados através do domínio de peering privado. O domínio de peering privado é considerado numa extensão fidedigna de sua rede principal para o Microsoft Azure. Pode configurar a conectividade bidirecional entre a rede principal e redes virtuais do Azure (VNets). Este peering permite-lhe ligar às máquinas virtuais e serviços diretamente nos respetivos endereços IP privados em nuvem.  

Pode ligar-se mais de uma rede virtual para o domínio de peering privado. Reveja a [página de PERGUNTAS FREQUENTES](expressroute-faqs.md) para obter informações sobre limites e limitações. Pode visitar a página [De limites de Subscrição e Serviço do Azure, Quotas e Restrições](../azure-resource-manager/management/azure-subscription-service-limits.md) para informações atualizadas sobre limites.  Consulte a página [de Encaminhamento](expressroute-routing.md) para obter informações detalhadas sobre a configuração do encaminhamento.

### <a name="microsoftpeering"></a>Peering da Microsoft

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

A conectividade com os serviços online da Microsoft (serviços Office 365 e Azure PaaS) ocorre através do peering da Microsoft. Podemos ativar a conetividade bidirecional entre os serviços de cloud WAN e a Microsoft através do domínio de encaminhamento de peering de Microsoft. Tem de ligar a serviços cloud da Microsoft apenas através de endereços IP públicos, que são propriedade de utilizador ou o fornecedor de conectividade e terá de cumprir todas as regras definidas. Para mais informações, consulte a página [de pré-requisitos expressRoute.](expressroute-prerequisites.md)

Consulte a [página faq](expressroute-faqs.md) para obter mais informações sobre serviços suportados, custos e detalhes de configuração. Consulte a página [ExpressRoute Locations](expressroute-locations.md) para obter informações sobre a lista de fornecedores de conectividade que oferecem suporte de pares da Microsoft.

## <a name="peeringcompare"></a>Comparação de olhares

A tabela seguinte compara os três peerings:

[!INCLUDE [peering comparison](../../includes/expressroute-peering-comparison.md)]

Pode habilitar um ou mais dos domínios de encaminhamento como parte do seu circuito do ExpressRoute. Pode optar por ter todos os domínios de encaminhamento de colocar o VPN mesmo se desejar combiná-los num único domínio de encaminhamento. Também pode colocá-los com domínios de encaminhamento diferentes, semelhantes ao diagrama. A configuração recomendada é que o peering privado está ligado diretamente à rede principal, e o público e ligações de peering da Microsoft estão ligadas a sua rede de Perímetro.

Cada peering requer sessões de BGP separados (um par para cada tipo de peering). Os pares de sessão BGP fornecem uma ligação de elevada disponibilidade. Se estiver a ligar através de fornecedores de conectividade de 2 de camada, é responsável por configurar e gerir o encaminhamento. Pode saber mais revendo os [fluxos](expressroute-workflows.md) de trabalho para a configuração do ExpressRoute.

## <a name="health"></a>Saúde ExpressRoute

Os circuitos ExpressRoute podem ser monitorizados para disponibilidade, conectividade com VNets e utilização da largura de banda utilizando o [Network Performance Monitor](https://docs.microsoft.com/azure/networking/network-monitoring-overview) (NPM).

NPM monitoriza o estado de funcionamento de peering privado do Azure e peering da Microsoft. Consulte o nosso [post](https://azure.microsoft.com/blog/monitoring-of-azure-expressroute-in-preview/) para mais informações.

## <a name="next-steps"></a>Passos seguintes

* Encontre um fornecedor de serviços. Consulte [os prestadores e locais de serviço ExpressRoute.](expressroute-locations.md)
* Confirme que todos os pré-requisitos são cumpridos. Veja os [Pré-requisitos do ExpressRoute](expressroute-prerequisites.md).
* Configurar a ligação do ExpressRoute.
  * [Criar e gerir circuitos do ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md)
  * [Configurar o encaminhamento (peering) dos circuitos do ExpressRoute](expressroute-howto-routing-portal-resource-manager.md)
