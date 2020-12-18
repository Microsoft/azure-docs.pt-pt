---
title: 'Azure ExpressRoute: Repor os olhos dos circuitos utilizando o portal Azure'
description: Aprenda a desativar e a ativar os seus conhecimentos de um circuito Azure ExpressRoute utilizando o portal Azure.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 11/30/2020
ms.author: duau
ms.openlocfilehash: 432ecedbbb8965926499380eb1165fdf43018426
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/18/2020
ms.locfileid: "97680278"
---
# <a name="reset-expressroute-circuit-peerings-by-using-the-azure-portal"></a>Repor os olhos no circuito ExpressRoute utilizando o portal Azure

Este artigo descreve como desativar e permitir espreitar um circuito Azure ExpressRoute utilizando o portal Azure. Quando desativa um espreitamento, a sessão do Protocolo de Gateway de Fronteira (BGP) para a ligação primária e secundária do seu circuito ExpressRoute é encerrada. Quando ativa um espreitamento, a sessão de BGP tanto na ligação primária como na ligação secundária do seu circuito ExpressRoute é restaurada.

> [!Note]
> A primeira vez que configurar os seus pares no seu circuito ExpressRoute, os seus pares são ativados por defeito.

A reposição dos seus pares ExpressRoute pode ser útil nos seguintes cenários:

* Estás a testar o teu projeto de recuperação de desastres e implementação. Por exemplo, assuma que tem dois circuitos ExpressRoute. Pode desativar os olhos de um circuito e forçar o tráfego da rede a utilizar o outro circuito.

* Pretende ativar a Deteção bidirecional de encaminhamento (BFD) no Azure private peering ou microsoft peering. Se o seu circuito ExpressRoute foi criado antes de 1 de agosto de 2018, em Azure private peering ou antes de 10 de janeiro de 2020, no espreitamento da Microsoft, o BFD não foi ativado por padrão. Reinicie o espreitamento para ativar o BFD.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

A partir de um navegador, vá ao [portal Azure](https://portal.azure.com)e, em seguida, inscreva-se na sua conta Azure.

## <a name="reset-a-peering"></a>Reinicie um olhar

Pode redefinir o espreitamento da Microsoft e o Azure privado a espreitar num circuito ExpressRoute de forma independente.

1. Escolha o circuito que pretende mudar.

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/expressroute-circuit-list.png" alt-text="Screenshot que mostra a escolha de um circuito na lista de circuitos ExpressRoute.":::

1. Escolha a configuração de espreitar que pretende reiniciar.

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/expressroute-circuit.png" alt-text="Screenshot que mostra escolher um olhar no circuito ExpressRoute.":::

1. Limpe a caixa **de verificação "Ativar o peering"** e, em seguida, selecione **'Guardar** para desativar a configuração de espreitar'.

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/disable-peering.png" alt-text="Screenshot que mostra a limpeza da caixa de verificação Enable Peering.":::

1. Selecione a caixa **de verificação "Ativar o peering"** e, em seguida, selecione **'Guardar** para voltar a ativar a configuração de espreitar'.

## <a name="next-steps"></a>Passos seguintes

Para resolver problemas com o ExpressRoute, consulte os seguintes artigos:

* [Verificar a conectividade do ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
* [Resolver problemas de desempenho da rede](expressroute-troubleshooting-network-performance.md)
