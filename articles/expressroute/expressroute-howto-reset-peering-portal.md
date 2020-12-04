---
title: 'Azure ExpressRoute: Reiniciar o circuito a espreitar utilizando o portal Azure'
description: Saiba como desativar e ativar os seus conhecimentos de um circuito Azure ExpressRoute utilizando o portal Azure.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 11/30/2020
ms.author: duau
ms.openlocfilehash: d4d6b0b0cce4f5304f7c5790ef2bda05633be52f
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96582990"
---
# <a name="reset-expressroute-circuit-peerings-use-the-azure-portal"></a>Os pares de circuitos ExpressRoute repostos usam o portal Azure

Este artigo descreve como desativar e permitir espreitar um circuito ExpressRoute utilizando o portal Azure. Quando desativar um espreitamento, a sessão de BGP para a ligação primária e secundária do seu circuito ExpressRoute será encerrada. Perderá a conectividade através deste olhar para a Microsoft. Quando ativar um espreitamento, a sessão de BGP tanto na ligação primária como na ligação secundária do seu circuito ExpressRoute será apresentada. Vais recuperar a conectividade através deste olhar para a Microsoft. Pode ativar e desativar o Microsoft Peering e o Azure Private Peering num circuito ExpressRoute de forma independente. A primeira vez que configurar os seus pares no seu circuito ExpressRoute, os seus pares são ativados por defeito.

Existem alguns cenários em que poderá achar útil repor os seus seus olhos ExpressRoute.
* Teste o seu design de recuperação de desastres e implementação. Por exemplo, tem dois circuitos ExpressRoute. Pode desativar os olhos de um circuito e forçar o tráfego da rede a falhar no outro circuito.
* Ativar a Deteção bidirecional de encaminhamento (BFD) no Azure Private Peering ou microsoft Peering do seu circuito ExpressRoute. O BFD é ativado por padrão no Azure Private Peering se o seu circuito ExpressRoute for criado após 1 de agosto de 2018 e no Microsoft Peering. Se o seu circuito ExpressRoute for criado depois de 10 de janeiro de 2020. Se o seu circuito foi criado antes disso, o BFD não estava habilitado. Pode ativar o BFD desativando o espreitamento e reenando-o. 

### <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Num browser, navegue para o [Portal do Azure](https://portal.azure.com) e inicie sessão com a sua conta do Azure.

## <a name="reset-a-peering"></a>Reinicie um olhar

1. Selecione o circuito que deseja que pretenda fazer alterações na configuração de espreitar.

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/expressroute-circuit-list.png" alt-text="Lista de circuitos ExpressRoute":::

1. Selecione a configuração de peering que deseja ativar ou desativar.

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/expressroute-circuit.png" alt-text="Visão geral do circuito ExpressRoute":::

1. Desmarque **Ativar o Peering** e selecione **Guardar** para desativar a configuração de peering.

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/disable-peering.png" alt-text="Desativar o seu olhar privado":::

1. Pode ativar o espreitamento novamente verificando **Ativar o Peering** e selecionar **Guardar**.

## <a name="next-steps"></a>Passos seguintes
Se precisar de ajuda para resolver problemas com um problema expressRoute, confira os seguintes artigos:
* [Verificar a conectividade do ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
* [Resolver problemas de desempenho da rede](expressroute-troubleshooting-network-performance.md)
