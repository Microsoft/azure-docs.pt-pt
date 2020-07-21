---
title: Sobre o upgrade da largura de banda do circuito. Azure ExpressRoute
description: Neste artigo, aprenda as melhores práticas para a modernização da largura de banda do circuito ExpressRoute
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: cherylmc
ms.openlocfilehash: a8f5aaa7b2a054aa31198779414387cebf0f0fbd
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86537042"
---
# <a name="about-upgrading-expressroute-circuit-bandwidth"></a>Sobre a modernização da largura de banda do circuito ExpressRoute

O ExpressRoute permite uma conectividade dedicada e privada à rede global da Microsoft. A conectividade é facilitada pela rede de um parceiro ExpressRoute, ou por uma ligação direta aos dispositivos Microsoft Enterprise Edge (MSEE). Uma vez configurada e testada a conectividade física, pode ativar a conectividade camada 2 e camada-3 criando um circuito ExpressRoute e configurando o seu espremiamento.

## <a name="upgrade-circuit-bandwidth"></a><a name="upgrade"></a>Largura de banda de circuito de upgrade

Para atualizar a largura de banda do circuito, o parceiro ExpressRoute Direct ou ExpressRoute necessita de ter [largura de banda suficiente](#considerations) para que a atualização tenha sucesso.

Se a capacidade estiver disponível, pode atualizar o circuito utilizando os seguintes métodos:

* [Portal do Azure](expressroute-howto-circuit-portal-resource-manager.md#modify)
* [PowerShell](expressroute-howto-circuit-arm.md#modify)
* [CLI do Azure](howto-circuit-cli.md#modify)

## <a name="capacity-considerations"></a><a name="considerations"></a>Considerações de capacidade

### <a name="insufficient-expressroute-partner-bandwidth"></a><a name="bandwidth"></a>Largura de banda parceira ExpressRoute insuficiente

Se o parceiro ExpressRoute não tiver capacidade suficiente, é necessário criar um novo circuito, configurado para a largura de banda desejada. Para manter a conectividade, não apague o circuito antigo até que o circuito recém-criado seja provisionado, o espreitamento foi configurado e (no que diz respeito ao espreitamento privado) foi provisionado o objeto de ligação ao gateway de rede virtual ExpressRoute.

Se o seu parceiro ExpressRoute não tiver capacidade disponível suficiente, tem de solicitar capacidade adicional no local de observação pretendido. Uma vez a nova capacidade, pode utilizar os passos contidos nos artigos na secção de largura de banda do [circuito de atualização](#upgrade) para criar um novo circuito, configurar a conectividade e eliminar o circuito antigo.


### <a name="insufficient-expressroute-direct-bandwidth"></a><a name="bandwidth"></a>Largura de banda ExpressRoute Direct insuficiente

Se o ExpressRoute Direct não tiver capacidade suficiente, pode eliminar circuitos associados ao recurso ExpressRoute Direct que já não são necessários, ou criar um novo recurso ExpressRoute Direct. Para obter orientações sobre a gestão do recurso ExpressRoute Direct, consulte [como configurar o ExpressRoute Direct](how-to-expressroute-direct-portal.md).

## <a name="next-steps"></a>Próximos passos

* [Criar e modificar um circuito](expressroute-howto-circuit-portal-resource-manager.md)
* [Criar e modificar a configuração de peering](expressroute-howto-routing-portal-resource-manager.md)
* [Ligar uma rede virtual a um circuito ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md)
