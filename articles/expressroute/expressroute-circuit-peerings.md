---
title: 'Azure ExpressRoute: circuitos e observação'
description: Esta página fornece uma visão geral dos circuitos ExpressRoute e domínios de encaminhamento/peering.
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: mialdrid
ms.openlocfilehash: c68ffd019937f902567c3deda8d879448dc082da
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79281356"
---
# <a name="expressroute-circuits-and-peering"></a>ExpressRoute circuits and peering (Circuitos e peering do ExpressRoute)

Os circuitos ExpressRoute ligam a sua infraestrutura no local à Microsoft através de um fornecedor de conectividade. Este artigo ajuda-o a compreender os circuitos ExpressRoute e os domínios/observação de encaminhamento. A figura que se segue mostra uma representação lógica da conectividade entre o seu WAN e a Microsoft.

![](./media/expressroute-circuit-peerings/expressroute-basic.png)

> [!IMPORTANT]
> O público azure foi depreciado e não está disponível para novos circuitos ExpressRoute. Os novos circuitos suportam o peering da Microsoft e o peering privado.  
>

## <a name="expressroute-circuits"></a><a name="circuits"></a>Circuitos do ExpressRoute

Um circuito ExpressRoute representa uma ligação lógica entre a sua infraestrutura no local e os serviços de nuvem da Microsoft através de um fornecedor de conectividade. Pode encomendar vários circuitos ExpressRoute. Cada circuito pode estar nas mesmas regiões ou regiões diferentes, e pode ser conectado às suas instalações através de diferentes fornecedores de conectividade.

Os circuitos ExpressRoute não mapeiam para nenhuma entidade física. Um circuito é identificado exclusivamente por um GUID padrão chamado de chave de serviço (s-key). A chave de serviço é a única informação trocada entre a Microsoft, o fornecedor de conectividade, e você. A chave s não é segredo para fins de segurança. Há um mapeamento 1:1 entre um circuito ExpressRoute e a chave s.

Os novos circuitos ExpressRoute podem incluir dois pares independentes: peering privado e peering microsoft. Enquanto os circuitos ExpressRoute existentes podem conter três pinos: Azure Public, Azure Private e Microsoft. Cada olhar é um par de sessões independentes de BGP, cada uma delas configurada redundantemente para alta disponibilidade. Existe um mapeamento de 1:N (1 <= N <= 3) entre um circuito ExpressRoute e domínios de encaminhamento. Um circuito ExpressRoute pode ter qualquer um, dois ou todos os três pares ativados por circuito ExpressRoute.

Cada circuito tem uma largura de banda fixa (50 Mbps, 100 Mbps, 200 Mbps, 500 Mbps, 1 Gbps, 10 Gbps) e é mapeado para um provedor de conectividade e uma localização de observação. A largura de banda selecionada é partilhada em todos os pares de circuitos

### <a name="quotas-limits-and-limitations"></a><a name="quotas"></a>Quotas, limites e limitações

As quotas e limites predefinidos aplicam-se a todos os circuitos ExpressRoute. Consulte a página [de Limites de Subscrição e Serviço do Azure, Quotas e Restrições](../azure-resource-manager/management/azure-subscription-service-limits.md) para obter informações atualizadas sobre quotas.

## <a name="expressroute-peering"></a><a name="routingdomains"></a>Peering ExpressRoute

Um circuito ExpressRoute tem vários domínios/pinos de encaminhamento associados a ele: Azure público, Azure private e Microsoft. Cada epeering é configurado de forma idêntica num par de routers (na configuração ativa ou de partilha de carga) para uma elevada disponibilidade. Os serviços azure são classificados como *azure público* e *azure privado* para representar os esquemas de endereçamento IP.

![](./media/expressroute-circuit-peerings/expressroute-peerings.png)

### <a name="azure-private-peering"></a><a name="privatepeering"></a>Peering privado do Azure

Os serviços de computação Azure, nomeadamente máquinas virtuais (IaaS) e serviços de nuvem (PaaS), que são implantados dentro de uma rede virtual, podem ser conectados através do domínio de peering privado. O domínio de epeering privado é considerado uma extensão fidedigna da sua rede central para o Microsoft Azure. Pode configurar conectividade bidirecional entre a sua rede central e as redes virtuais Azure (VNets). Este peering permite ligar-se diretamente a máquinas virtuais e serviços de nuvem nos seus endereços IP privados.  

Pode ligar mais do que uma rede virtual ao domínio de observação privada. Reveja a [página de PERGUNTAS FREQUENTES](expressroute-faqs.md) para obter informações sobre limites e limitações. Pode visitar a página [De limites de Subscrição e Serviço do Azure, Quotas e Restrições](../azure-resource-manager/management/azure-subscription-service-limits.md) para informações atualizadas sobre limites.  Consulte a página [de Encaminhamento](expressroute-routing.md) para obter informações detalhadas sobre a configuração do encaminhamento.

### <a name="microsoft-peering"></a><a name="microsoftpeering"></a>Peering da Microsoft

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

A conectividade com os serviços online da Microsoft (serviços Office 365 e Azure PaaS) ocorre através do peering da Microsoft. Permitimos a conectividade bidirecional entre os seus serviços de nuvem WAN e Microsoft através do domínio de encaminhamento da Microsoft. Deve ligar-se aos serviços de nuvem da Microsoft apenas através de endereços IP públicos que são propriedade de si ou do seu fornecedor de conectividade e deve aderir a todas as regras definidas. Para mais informações, consulte a página [de pré-requisitos expressRoute.](expressroute-prerequisites.md)

Consulte a [página faq](expressroute-faqs.md) para obter mais informações sobre serviços suportados, custos e detalhes de configuração. Consulte a página [ExpressRoute Locations](expressroute-locations.md) para obter informações sobre a lista de fornecedores de conectividade que oferecem suporte de pares da Microsoft.

## <a name="peering-comparison"></a><a name="peeringcompare"></a>Comparação de olhares

A tabela seguinte compara os três pares:

[!INCLUDE [peering comparison](../../includes/expressroute-peering-comparison.md)]

Pode ativar um ou mais domínios de encaminhamento como parte do seu circuito ExpressRoute. Pode optar por ter todos os domínios de encaminhamento colocados na mesma VPN se quiser combiná-los num único domínio de encaminhamento. Também pode colocá-los em diferentes domínios de encaminhamento, semelhantes ao diagrama. A configuração recomendada é que o epeering privado está ligado diretamente à rede central, e as ligações de peering público e Microsoft estão ligadas ao seu DMZ.

Cada epeering requer sessões bGP separadas (um par para cada tipo de peering). Os pares de sessões de BGP fornecem um link altamente disponível. Se estiver a ligar-se através de fornecedores de conectividade camada 2, é responsável por configurar e gerir o encaminhamento. Pode saber mais revendo os [fluxos](expressroute-workflows.md) de trabalho para a configuração do ExpressRoute.

## <a name="expressroute-health"></a><a name="health"></a>Estado de funcionamento do ExpressRoute

Os circuitos ExpressRoute podem ser monitorizados para disponibilidade, conectividade com VNets e utilização da largura de banda utilizando o [Network Performance Monitor](https://docs.microsoft.com/azure/networking/network-monitoring-overview) (NPM).

A NPM monitoriza a saúde do peering privado Azure e do peering da Microsoft. Consulte o nosso [post](https://azure.microsoft.com/blog/monitoring-of-azure-expressroute-in-preview/) para mais informações.

## <a name="next-steps"></a>Passos seguintes

* Encontre um fornecedor de serviços. Consulte [os prestadores e locais de serviço ExpressRoute.](expressroute-locations.md)
* Confirme que todos os pré-requisitos são cumpridos. Veja os [Pré-requisitos do ExpressRoute](expressroute-prerequisites.md).
* Configurar a ligação do ExpressRoute.
  * [Criar e gerir circuitos do ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md)
  * [Configurar o encaminhamento (peering) dos circuitos do ExpressRoute](expressroute-howto-routing-portal-resource-manager.md)
