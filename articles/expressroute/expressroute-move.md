---
title: 'ExpressRoute: Mover circuitos do clássico para o Azure Resource Manager'
description: Saiba o que significa mover um circuito Azure ExpressRoute do clássico para o modelo de implementação do Azure Resource Manager.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 12/07/2018
ms.author: duau
ms.openlocfilehash: 12ace8dff58d3d747bf17260950d2b91e31b398a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89395167"
---
# <a name="moving-expressroute-circuits-from-the-classic-to-the-resource-manager-deployment-model"></a>Mover circuitos ExpressRoute do modelo de implementação clássica para o modelo do Resource Manager
Este artigo fornece uma descrição geral sobre o que significa mover um circuito Azure ExpressRoute do modelo de implementação clássica Azure Resource Manager.

Pode utilizar um circuito ExpressRoute único para ligar a redes virtuais que são implementadas no modelo de implementação clássica e o modelo do Resource Manager. Um circuito ExpressRoute, independentemente de como é criado, pode agora ligar-se a redes virtuais através de ambos os modelos de implementação.

![Um circuito de ExpressRoute que se liga a redes virtuais através de ambos os modelos de implementação](./media/expressroute-move/expressroute-move-1.png)

## <a name="expressroute-circuits-that-are-created-in-the-classic-deployment-model"></a>Circuitos ExpressRoute criados com o modelo de implementação clássico
Os circuitos ExpressRoute criados com o modelo de implementação clássica têm de ser movidos primeiro para o modelo de implementação Resource Manager para que a conetividade entre o modelo de implementação clássica e o modelo do Resource Manager seja ativada. Não existe perda ou perturbação da conectividade quando uma ligação está a ser movida. Todas as ligações de rede circuito-para-virtual no modelo de implementação clássica (dentro da mesma subscrição e entre subscrições) são preservadas.

Após a mudança ser concluída com êxito, o circuito ExpressRoute tem um aspeto semelhante e funciona exatamente como um circuito ExpressRoute que foi criado no modelo de implementação Resource Manager. Agora, pode criar ligações para redes virtuais no modelo de implementação Resource Manager.

Após a mudança de um circuito ExpressRoute para o modelo de implementação Resource Manager, pode gerir o ciclo de vida do circuito ExpressRoute com apenas o modelo de implementação Resource Manager. Isto significa que pode realizar operações como adicionar/atualizar/eliminar peerings, atualizar propriedades de circuitos (tais como largura de banda, SKU e tipo de faturação) e eliminar circuitos apenas no modelo de implementação Resource Manager. Veja a secção abaixo sobre circuitos criados no modelo de implementação Resource Manager para obter mais detalhes sobre como pode gerir o acesso a ambos os modelos de implementação.

Não é necessário envolver o seu fornecedor de conectividade para efetuar a mudança.

## <a name="expressroute-circuits-that-are-created-in-the-resource-manager-deployment-model"></a>Circuitos ExpressRoute criados no modelo de implementação Resource Manager
Pode ativar os circuitos ExpressRoute criados no modelo de implementação Resource Manager para serem acessíveis a partir de ambos os modelos de implementação. Qualquer circuito ExpressRoute na sua subscrição pode ser ativado para ser acedido a partir de ambos os modelos de implementação.

* Por predefinição, os circuitos ExpressRoute que foram criados no modelo de implementação Resource Manager não têm acesso ao modelo de implementação clássica.
* Por predefinição, os circuitos ExpressRoute que tenham sido transferidos a partir do modelo de implementação clássica para o modelo de implementação Resource Manager são acessíveis a partir de ambos os modelos de implementação.
* Um circuito ExpressRoute tem sempre acesso ao modelo de implementação Resource Manager, independentemente de ter sido criado no modelo de implementação clássica ou do Resource Manager. Isto significa que pode criar ligações a redes virtuais criadas no modelo de implementação Resource Manager seguindo as instruções em [como ligar a redes virtuais](expressroute-howto-linkvnet-arm.md).
* O acesso ao modelo de implementação clássica é controlado pelo parâmetro **allowClassicOperations** no circuito ExpressRoute.

> [!IMPORTANT]
> Todas as quotas documentadas na página [limites de serviços](../azure-resource-manager/management/azure-subscription-service-limits.md) aplicam-se. Por exemplo, um circuito standard pode ter no máximo 10 ligações/links de rede virtual entre o modelo e implementação clássica e o modelo do Source Manager.
> 
> 

## <a name="controlling-access-to-the-classic-deployment-model"></a>Controlar o acesso ao modelo de implementação clássica
Pode ativar um único circuito ExpressRoute para ligar a redes virtuais em ambos os modelos de implementação através da definição do parâmetro **allowClassicOperations** do circuito ExpressRoute.

Quando **allowClassicOperations** é definido como TRUE, pode ligar redes virtuais de ambos os modelos de implementação ao circuito ExpressRoute. Pode ligar a redes virtuais no modelo de implementação clássica consultando as instruções sobre [como ligar redes virtuais no modelo de implementação clássica](expressroute-howto-linkvnet-classic.md). Pode ligar a redes virtuais no modelo de implementação Resource Manager consultando as instruções sobre [como ligar redes virtuais no modelo de implementação Resource Manager](expressroute-howto-linkvnet-arm.md).

Quando **allowClassicOperations** é definido como FALSE, o acesso ao circuito a partir do modelo de implementação clássica é bloqueado. No entanto, todas as ligações de rede virtual no modelo de implementação clássica são preservadas. Neste caso, o circuito ExpressRoute não é visível no modelo de implementação clássica.

## <a name="supported-operations-in-the-classic-deployment-model"></a>Operações suportadas no modelo de implementação clássica
As seguintes operações clássicas são suportadas num circuito ExpressRoute quando **allowClassicOperations** é definido como TRUE:

* Obter informações do circuito ExpressRoute
* Criar/atualizar/obter/eliminar ligações de rede virtual em redes virtuais clássicas
* Criar/atualizar/obter/eliminar autorizações de ligações de rede virtual para conectividade entre subscrições

No entanto, quando **permitir que as Operações Desafetadas** estão definidas para TRUE, não é possível realizar as seguintes operações clássicas:

* Criar/atualizar/obter/eliminar peerings do protocolo BGP (Border Gateway Protocol) para peerings do Azure privado, Azure público e da Microsoft
* Eliminar um circuito do ExpressRoute

## <a name="communication-between-the-classic-and-the-resource-manager-deployment-models"></a>Comunicação entre o modelo de implementação clássica e o modelo do Resource Manager
O circuito ExpressRoute funciona como uma ponte entre o modelo de implementação clássica e o modelo do Resource Manager. O tráfego entre máquinas virtuais nas redes virtuais no modelo de implementação clássica e nas redes virtuais no modelo de implementação Resource Manager flui através do ExpressRoute, se ambas as redes virtuais estiverem ligadas ao mesmo circuito ExpressRoute.

O débito agregado é limitado pela capacidade de débito do gateway de rede virtual. O tráfego não entra na rede do fornecedor de conetividade ou nas suas redes nestes casos. O fluxo de tráfego entre as redes virtuais encontra-se completamente contido dentro da rede da Microsoft.

## <a name="access-to-azure-public-and-microsoft-peering-resources"></a>Acesso aos recursos do peering público do Azure e o peering da Microsoft
Pode continuar a aceder a recursos que geralmente são acessíveis através de peering público do Azure e peering da Microsoft sem qualquer interrupção.  

## <a name="whats-supported"></a>O que é suportado
Esta secção descreve o que é suportado para circuitos ExpressRoute:

* Pode utilizar um circuito ExpressRoute único para aceder a redes virtuais que são implementadas no modelo de implementação clássica e o modelo do Resource Manager.
* Pode mover um circuito ExpressRoute do modelo de implementação clássica para o modelo do Resource Manager. Depois de ser movido, o circuito ExpressRoute tem um aspeto semelhante e funciona como qualquer outro circuito ExpressRoute criado no modelo de implementação Resource Manager.
* Pode mover apenas o circuito ExpressRoute. As ligações de circuito, redes virtuais e os gateways de VPN não podem ser movidos através desta operação.
* Após a mudança de um circuito ExpressRoute para o modelo de implementação Resource Manager, pode gerir o ciclo de vida do circuito ExpressRoute com apenas o modelo de implementação Resource Manager. Isto significa que pode realizar operações como adicionar/atualizar/eliminar peerings, atualizar propriedades de circuitos (tais como largura de banda, SKU e tipo de faturação) e eliminar circuitos apenas no modelo de implementação Resource Manager.
* O circuito ExpressRoute funciona como uma ponte entre o modelo de implementação clássica e o modelo do Resource Manager. O tráfego entre máquinas virtuais nas redes virtuais no modelo de implementação clássica e nas redes virtuais no modelo de implementação Resource Manager flui através do ExpressRoute, se ambas as redes virtuais estiverem ligadas ao mesmo circuito ExpressRoute.
* A conectividade entre subscrições é suportada nos modelos de implementação clássica e do modelo do Resource Manager.
* Depois de mover um circuito ExpressRoute do modelo clássico para o modelo do Azure Resource Manager, pode [migrar as redes virtuais ligadas para o circuito do ExpressRoute](expressroute-migration-classic-resource-manager.md).

## <a name="whats-not-supported"></a>O que não é suportado
Esta secção descreve o que não é suportado para circuitos ExpressRoute:

* Gerir o ciclo de vida de um circuito ExpressRoute do modelo de implementação clássica.
* Suporte para Controlo de Acesso Baseado em Funções (RBAC) para o modelo de implementação clássica. Não pode executar controlos RBAC para um circuito no modelo de implementação clássica. Qualquer administrador/coadministrador da subscrição pode associar ou desassociar redes virtuais ao circuito.

## <a name="configuration"></a>Configuração
Siga as instruções descritas em [Mover um circuito ExpressRoute do modelo de implementação clássica para o modelo do Resource Manager](expressroute-howto-move-arm.md).

## <a name="next-steps"></a>Passos seguintes
* [Migrar as redes virtuais ligadas ao circuito ExpressRoute a partir do modelo clássico ao modelo do Azure Resource Manager](expressroute-migration-classic-resource-manager.md)
* Para obter informações sobre o fluxo de trabalho, veja [Circuito ExpressRoute aprovisiona fluxos de trabalho e estados de circuitos](expressroute-workflows.md).
* Para configurar a ligação do ExpressRoute:
  
  * [Criar um circuito do ExpressRoute](expressroute-howto-circuit-arm.md)
  * [Configure o encaminhamento](expressroute-howto-routing-arm.md)
  * [Ligar uma rede virtual a um circuito ExpressRoute](expressroute-howto-linkvnet-arm.md)

