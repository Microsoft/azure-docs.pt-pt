---
title: 'Azure ExpressRoute: circuitos e espreitar'
description: Esta página fornece uma visão geral dos circuitos ExpressRoute e domínios de encaminhamento/observação.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: duau
ms.openlocfilehash: 072eeb0bee0d0441549d2edad448f3b1c85a28a4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89566505"
---
# <a name="expressroute-circuits-and-peering"></a>ExpressRoute circuits and peering (Circuitos e peering do ExpressRoute)

Os circuitos ExpressRoute ligam a sua infraestrutura no local à Microsoft através de um fornecedor de conectividade. Este artigo ajuda-o a compreender os circuitos ExpressRoute e os domínios de encaminhamento/observação. A seguinte figura mostra uma representação lógica da conectividade entre o wan e a Microsoft.

![Diagrama mostrando como os circuitos ExpressRoute ligam a sua infraestrutura no local à Microsoft através de um fornecedor de conectividade.](./media/expressroute-circuit-peerings/expressroute-basic.png)

> [!IMPORTANT]
> O azure de espreitar público foi depreciado e não está disponível para novos circuitos ExpressRoute. Os novos circuitos suportam o espreitamento da Microsoft e o seu espreitamento privado.  
>

## <a name="expressroute-circuits"></a><a name="circuits"></a>Circuitos do ExpressRoute

Um circuito ExpressRoute representa uma ligação lógica entre a sua infraestrutura no local e os serviços de cloud da Microsoft através de um fornecedor de conectividade. Pode encomendar vários circuitos ExpressRoute. Cada circuito pode estar nas mesmas regiões ou diferentes, e pode ser conectado às suas instalações através de diferentes fornecedores de conectividade.

Os circuitos ExpressRoute não mapeiam para nenhuma entidade física. Um circuito é identificado exclusivamente por um GUID padrão chamado como chave de serviço (s-key). A chave de serviço é a única informação trocada entre a Microsoft, o fornecedor de conectividade, e você. A chave não é segredo para fins de segurança. Há um mapeamento 1:1 entre um circuito ExpressRoute e a chave s.

Os novos circuitos ExpressRoute podem incluir dois pares independentes: olhando privado e olhando a Microsoft. Enquanto os circuitos ExpressRoute existentes podem conter três patas: Azure Public, Azure Private e Microsoft. Cada um dos seus olhos é um par de sessões de BGP independentes, cada uma delas configurada de forma redundante para uma elevada disponibilidade. Existe um mapeamento de 1:N (1 <= N <= 3) entre um circuito ExpressRoute e domínios de encaminhamento. Um circuito ExpressRoute pode ter qualquer um, dois ou todos os três espreitadores ativados por circuito ExpressRoute.

Cada circuito tem uma largura de banda fixa (50 Mbps, 100 Mbps, 200 Mbps, 500 Mbps, 1 Gbps, 10 Gbps) e é mapeado para um provedor de conectividade e um local de observação. A largura de banda que seleciona é partilhada em todos os persituos do circuito

### <a name="quotas-limits-and-limitations"></a><a name="quotas"></a>Quotas, limites e limitações

As quotas e limites predefinidos aplicam-se a todos os circuitos ExpressRoute. Consulte a página [Azure Subscription and Service Limits, Quotas e Constraints](../azure-resource-manager/management/azure-subscription-service-limits.md) para obter informações atualizadas sobre quotas.

## <a name="expressroute-peering"></a><a name="routingdomains"></a>ExpressRoute olhando

Um circuito ExpressRoute tem vários domínios/pares associados: Público Azure, Azure privado e Microsoft. Cada espreguite é configurado de forma idêntica num par de routers (em configuração ativa ou de partilha de carga) para uma elevada disponibilidade. Os serviços Azure são classificados como *públicos Azure* e *Azure privados* para representar os esquemas de endereçamento ip.

![Diagrama mostrando como o público Azure, a Azure privado e os seus pares microsoft são configurados num circuito ExpressRoute.](./media/expressroute-circuit-peerings/expressroute-peerings.png)

### <a name="azure-private-peering"></a><a name="privatepeering"></a>Peering privado do Azure

Os serviços de computação Azure, nomeadamente máquinas virtuais (IaaS) e serviços de nuvem (PaaS), que são implantados dentro de uma rede virtual podem ser conectados através do domínio de observação privada. O domínio de observação privada é considerado uma extensão fidedigna da sua rede principal para o Microsoft Azure. Pode configurar a conectividade biducional entre a sua rede central e as redes virtuais Azure (VNets). Este espreitante permite-lhe ligar-se a máquinas virtuais e serviços de nuvem diretamente nos seus endereços IP privados.  

Pode ligar mais do que uma rede virtual ao domínio de observação privada. Reveja a [página de PERGUNTAS Frequentes](expressroute-faqs.md) para obter informações sobre limites e limitações. Pode visitar a página [Azure Subscription and Service Limits, Quotas e Restrições](../azure-resource-manager/management/azure-subscription-service-limits.md) para obter informações atualizadas sobre limites.  Consulte a página [de encaminhamento](expressroute-routing.md) para obter informações detalhadas sobre a configuração do encaminhamento.

### <a name="microsoft-peering"></a><a name="microsoftpeering"></a>Peering da Microsoft

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

A conectividade com os serviços online da Microsoft (os serviços Microsoft 365 e Azure PaaS) ocorre através do persto da Microsoft. Ativamos a conectividade biducional entre os seus serviços de nuvem WAN e Microsoft através do domínio de encaminhamento de encaminhamento da Microsoft. Tem de se ligar aos serviços de cloud da Microsoft apenas através de endereços IP públicos que são propriedade de si ou do seu fornecedor de conectividade e deve respeitar todas as regras definidas. Para mais informações, consulte a página [pré-requisitos do ExpressRoute.](expressroute-prerequisites.md)

Consulte a [página de PERGUNTAS Frequentes](expressroute-faqs.md) para obter mais informações sobre serviços suportados, custos e detalhes de configuração. Consulte a página [ExpressRoute Locations](expressroute-locations.md) para obter informações sobre a lista de fornecedores de conectividade que oferecem suporte para espreitar a Microsoft.

## <a name="peering-comparison"></a><a name="peeringcompare"></a>Comparação de pares

A tabela a seguir compara os três pares:

[!INCLUDE [peering comparison](../../includes/expressroute-peering-comparison.md)]

Pode ativar um ou mais domínios de encaminhamento como parte do seu circuito ExpressRoute. Pode optar por colocar todos os domínios de encaminhamento na mesma VPN se quiser combiná-los num único domínio de encaminhamento. Também pode colocá-los em diferentes domínios de encaminhamento, semelhantes ao diagrama. A configuração recomendada é que o espreitamento privado está ligado diretamente à rede principal, e as ligações de observação do público e da Microsoft estão ligadas ao seu DMZ.

Cada espreitamento requer sessões de BGP separadas (um par para cada tipo de espreitar). Os pares de sessão BGP fornecem um link altamente disponível. Se estiver a ligar através de fornecedores de conectividade da camada 2, é responsável pela configuração e gestão do encaminhamento. Pode aprender mais revendo os [fluxos de trabalho](expressroute-workflows.md) para a criação do ExpressRoute.

## <a name="expressroute-health"></a><a name="health"></a>Estado de funcionamento do ExpressRoute

Os circuitos ExpressRoute podem ser monitorizados para disponibilidade, conectividade com VNets e utilização de largura de banda utilizando [o Network Performance Monitor](https://docs.microsoft.com/azure/networking/network-monitoring-overview) (NPM).

O NPM monitoriza a saúde do Azure private peering e microsoft peering. Consulte o nosso [posto](https://azure.microsoft.com/blog/monitoring-of-azure-expressroute-in-preview/) para mais informações.

## <a name="next-steps"></a>Passos seguintes

* Encontre um fornecedor de serviços. Consulte [os prestadores e locais de serviço ExpressRoute](expressroute-locations.md).
* Confirme que todos os pré-requisitos são cumpridos. Veja os [Pré-requisitos do ExpressRoute](expressroute-prerequisites.md).
* Configurar a ligação do ExpressRoute.
  * [Criar e gerir circuitos do ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md)
  * [Configurar o encaminhamento (peering) dos circuitos do ExpressRoute](expressroute-howto-routing-portal-resource-manager.md)
