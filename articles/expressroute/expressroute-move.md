---
title: 'ExpressRoute: Mover circuitos do clássico para o Azure Resource Manager'
description: Saiba o que acontece quando se move um circuito Azure ExpressRoute do clássico para o modelo de implementação do Azure Resource Manager.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 12/15/2020
ms.author: duau
ms.openlocfilehash: dcba2e9de2b37e8c432f94781b3c4c369ad52395
ms.sourcegitcommit: 02ed9acd4390b86c8432cad29075e2204f6b1bc3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/29/2020
ms.locfileid: "97807946"
---
# <a name="moving-expressroute-circuits-from-the-classic-to-the-resource-manager-deployment-model"></a>Mover circuitos ExpressRoute do modelo de implementação clássica para o modelo do Resource Manager
Este artigo fornece uma visão geral do que acontece quando se move um circuito Azure ExpressRoute do clássico para o modelo de implementação do Azure Resource Manager.

Pode utilizar um único circuito ExpressRoute para ligar redes virtuais que são implantadas tanto nos modelos clássicos como nos modelos de implementação do Gestor de Recursos.

![Um circuito de ExpressRoute que se liga a redes virtuais através de ambos os modelos de implementação](./media/expressroute-move/expressroute-move-1.png)

## <a name="expressroute-circuits-that-are-created-in-the-classic-deployment-model"></a>Circuitos ExpressRoute criados com o modelo de implementação clássico
Os circuitos ExpressRoute criados no modelo de implementação clássico precisam de migrar primeiro para o modelo de implementação do Gestor de Recursos. Só assim é possível permitir a conectividade tanto aos modelos clássicos como aos modelos de implementação do Gestor de Recursos. A conectividade não é perda ou perturbação quando uma ligação está a ser movida. Todas as ligações de rede de circuito-a-virtual no modelo de implementação clássico dentro da mesma subscrição e subscrição cruzada são preservadas.

Depois de concluído o movimento com sucesso, o circuito ExpressRoute comportar-se-á exatamente como um circuito ExpressRoute que foi criado no modelo de implementação do Gestor de Recursos. Agora, pode criar ligações para redes virtuais no modelo de implementação Resource Manager.

Depois de ter mudado o circuito ExpressRoute para o modelo de implementação do Gestor de Recursos, só o poderá gerir no modelo de implementação do Gestor de Recursos. As operações de gestão de persituos, a atualização das propriedades dos circuitos e a eliminação de circuitos só estarão disponíveis através do modelo de implementação do Gestor de Recursos. Consulte a seguinte secção para mais detalhes sobre como pode gerir o acesso a ambos os modelos de implementação.

Não é preciso envolver o seu fornecedor de conectividade para mover o seu circuito para o modelo de implementação do Gestor de Recursos.

## <a name="expressroute-circuits-that-are-created-in-the-resource-manager-deployment-model"></a>Circuitos ExpressRoute criados no modelo de implementação Resource Manager
Pode ativar os circuitos ExpressRoute criados no modelo de implementação Resource Manager para serem acessíveis a partir de ambos os modelos de implementação. Qualquer circuito ExpressRoute na sua subscrição pode ser configurado para ter acesso a partir de ambos os modelos de implementação.

* Os circuitos ExpressRoute que foram criados no modelo de implementação do Gestor de Recursos não têm acesso ao modelo clássico de implementação por padrão.
* Os circuitos ExpressRoute que foram transferidos do modelo clássico de implementação para o modelo de implementação do Gestor de Recursos estão acessíveis a partir de ambos os modelos de implementação por padrão.
* Um circuito ExpressRoute tem sempre acesso ao modelo de implementação do Gestor de Recursos, quer tenha sido criado no Gestor de Recursos ou no modelo de implementação clássico. Pode criar ligações a redes virtuais seguindo instruções sobre [como ligar redes virtuais.](expressroute-howto-linkvnet-arm.md)
* O acesso ao modelo de implementação clássica é controlado pelo parâmetro **allowClassicOperations** no circuito ExpressRoute.

> [!IMPORTANT]
> Todas as quotas documentadas na página [limites de serviços](../azure-resource-manager/management/azure-subscription-service-limits.md) aplicam-se. Por exemplo, um circuito standard pode ter no máximo 10 ligações/links de rede virtual entre o modelo e implementação clássica e o modelo do Source Manager.
> 
> 

## <a name="controlling-access-to-the-classic-deployment-model"></a>Controlar o acesso ao modelo de implementação clássica
Pode ativar um circuito ExpressRoute para ligar a redes virtuais em ambos os modelos de implementação. Para tal, desaperte o parâmetro **de operações de admissão** no circuito ExpressRoute.

Quando **allowClassicOperations** é definido como TRUE, pode ligar redes virtuais de ambos os modelos de implementação ao circuito ExpressRoute. 
* Para ligar redes virtuais no modelo clássico de implementação, veja [como ligar redes virtuais para o modelo de implementação clássico.](expressroute-howto-linkvnet-classic.md)
* Para ligar redes virtuais no modelo de implementação do Gestor de Recursos, consulte [como ligar redes virtuais no modelo de implementação do Gestor de Recursos.](expressroute-howto-linkvnet-arm.md)

Quando **allowClassicOperations** é definido como FALSE, o acesso ao circuito a partir do modelo de implementação clássica é bloqueado. No entanto, todas as redes virtuais ligadas no modelo de implementação clássica ainda estão preservadas. O circuito ExpressRoute não é visível no modelo clássico de implantação.

## <a name="supported-operations-in-the-classic-deployment-model"></a>Operações suportadas no modelo de implementação clássica
As seguintes operações clássicas são suportadas num circuito ExpressRoute quando **allowClassicOperations** é definido como TRUE:

* Obter informações do circuito ExpressRoute
* Criar/atualizar/obter/eliminar ligações de rede virtual em redes virtuais clássicas
* Criar/atualizar/obter/eliminar autorizações de ligações de rede virtual para conectividade entre subscrições

No entanto, quando **permitir operações de classe** é definido para TRUE, não pode executar as seguintes operações clássicas:

* Criar/atualizar/obter/eliminar peerings do protocolo BGP (Border Gateway Protocol) para peerings do Azure privado, Azure público e da Microsoft
* Eliminar um circuito do ExpressRoute

## <a name="communication-between-the-classic-and-the-resource-manager-deployment-models"></a>Comunicação entre o modelo de implementação clássica e o modelo do Resource Manager
O circuito ExpressRoute funciona como uma ponte entre o modelo de implementação clássica e o modelo do Resource Manager. O tráfego entre redes virtuais para ambos os modelos de implantação pode passar pelo circuito ExpressRoute se ambas as redes virtuais estiverem ligadas ao mesmo circuito.

O débito agregado é limitado pela capacidade de débito do gateway de rede virtual. O tráfego não entra nas redes do fornecedor de conectividade ou nas suas redes nestes casos. O fluxo de tráfego entre as redes virtuais encontra-se completamente contido dentro da rede da Microsoft.

## <a name="access-to-azure-public-and-microsoft-peering-resources"></a>Acesso aos recursos do peering público do Azure e o peering da Microsoft
Pode continuar a aceder a recursos que geralmente são acessíveis através de peering público do Azure e peering da Microsoft sem qualquer interrupção.  

## <a name="whats-supported"></a>O que é suportado
Esta secção descreve o que é suportado para circuitos ExpressRoute:

* Pode utilizar um circuito ExpressRoute único para aceder a redes virtuais que são implementadas no modelo de implementação clássica e o modelo do Resource Manager.
* Pode mover um circuito ExpressRoute do modelo de implementação clássica para o modelo do Resource Manager. Uma vez movido, o circuito ExpressRoute continuará a funcionar como qualquer outro circuito ExpressRoute que seja criado no modelo de implementação do Gestor de Recursos.
* Pode mover apenas o circuito ExpressRoute. As ligações de circuito, redes virtuais e os gateways de VPN não podem ser movidos através desta operação.
* Após a mudança de um circuito ExpressRoute para o modelo de implementação Resource Manager, pode gerir o ciclo de vida do circuito ExpressRoute com apenas o modelo de implementação Resource Manager. Isto significa que pode executar operações como adicionar/atualizar/eliminar os pares, atualizar propriedades de circuitos (como largura de banda, SKU e tipo de faturação) e eliminar circuitos apenas no modelo de implementação do Gestor de Recursos.
* O circuito ExpressRoute funciona como uma ponte entre o modelo de implementação clássica e o modelo do Resource Manager. O tráfego entre máquinas virtuais em redes virtuais clássicas e máquinas virtuais em redes virtuais do Resource Manager pode comunicar através do ExpressRoute se ambas as redes virtuais estiverem ligadas ao mesmo circuito ExpressRoute.
* A conectividade entre subscrições é suportada nos modelos de implementação clássica e do modelo do Resource Manager.
* Depois de mover um circuito ExpressRoute do modelo clássico para o modelo do Azure Resource Manager, pode [migrar as redes virtuais ligadas para o circuito do ExpressRoute](expressroute-migration-classic-resource-manager.md).

## <a name="whats-not-supported"></a>O que não é suportado
Esta secção descreve o que não é suportado para circuitos ExpressRoute:

* Gerir o ciclo de vida de um circuito ExpressRoute do modelo de implementação clássica.
* Suporte de controlo de acesso baseado em funções (Azure RBAC) para o modelo de implementação clássico. Não é possível executar os controlos Azure RBAC para um circuito no modelo clássico de implementação. Qualquer administrador/coadministrador da subscrição pode associar ou desassociar redes virtuais ao circuito.

## <a name="configuration"></a>Configuração
Siga as instruções descritas em [Mover um circuito ExpressRoute do modelo de implementação clássica para o modelo do Resource Manager](expressroute-howto-move-arm.md).

## <a name="next-steps"></a>Passos seguintes
* [Migrar as redes virtuais ligadas ao circuito ExpressRoute a partir do modelo clássico ao modelo do Azure Resource Manager](expressroute-migration-classic-resource-manager.md)
* Para obter informações sobre o fluxo de trabalho, veja [Circuito ExpressRoute aprovisiona fluxos de trabalho e estados de circuitos](expressroute-workflows.md).
* Para configurar a ligação do ExpressRoute:
  
  * [Criar um circuito do ExpressRoute](expressroute-howto-circuit-arm.md)
  * [Configure o encaminhamento](expressroute-howto-routing-arm.md)
  * [Ligar uma rede virtual a um circuito ExpressRoute](expressroute-howto-linkvnet-arm.md)

