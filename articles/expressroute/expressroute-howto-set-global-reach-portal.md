---
title: 'Azure ExpressRoute: Configure Global Reach usando o portal Azure'
description: Este artigo ajuda-o a ligar os circuitos ExpressRoute em conjunto para fazer uma rede privada entre as suas redes no local e ativar o Global Reach utilizando o portal Azure.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 01/11/2021
ms.author: duau
ms.openlocfilehash: 8366978d50875389ce872c2d1402f0defa2a7371
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2021
ms.locfileid: "99539354"
---
# <a name="configure-expressroute-global-reach-using-the-azure-portal"></a>Configurar ExpressRoute Global Reach usando o portal Azure

Este artigo ajuda-o a configurar o ExpressRoute Global Reach usando o PowerShell. Para mais informações, consulte [ExpressRoute Global Reach](expressroute-global-reach.md).

 ## <a name="before-you-begin"></a>Antes de começar

Antes de iniciar a configuração, confirme os seguintes critérios:

* Compreende o circuito ExpressRoute que fornece [fluxos de trabalho.](expressroute-workflows.md)
* Os seus circuitos ExpressRoute estão num estado aprovisionado.
* O olhar privado Azure está configurado nos seus circuitos ExpressRoute.
* Se pretender executar o PowerShell localmente, verifique se a versão mais recente do Azure PowerShell está instalada no seu computador.

## <a name="identify-circuits"></a>Identificar circuitos

1. Num browser, navegue para o [Portal do Azure](https://portal.azure.com) e inicie sessão com a sua conta do Azure.

2. Identifique os circuitos ExpressRoute que pretende utilizar. Você pode ativar expressRoute Global Reach entre o espreitamento privado de qualquer dois circuitos ExpressRoute, desde que estejam localizados nos países/regiões apoiados. Os circuitos devem ser criados em diferentes locais de observação. 

   * Se a sua subscrição possuir ambos os circuitos, pode escolher qualquer circuito para executar a configuração nas seguintes secções.
   * Se os dois circuitos estiverem em diferentes subscrições do Azure, precisa de autorização de uma subscrição da Azure. Em seguida, passa-se a chave de autorização quando executar o comando de configuração na outra subscrição do Azure.

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/expressroute-circuit-global-reach-list.png" alt-text="Lista de circuitos ExpressRoute":::

## <a name="enable-connectivity"></a>Ativar a conectividade

Ativar a conectividade entre as suas redes no local. Existem conjuntos separados de instruções para circuitos que estão na mesma subscrição do Azure, e circuitos que são diferentes subscrições.

### <a name="expressroute-circuits-in-the-same-azure-subscription"></a>Circuitos ExpressRoute na mesma subscrição do Azure

1. Selecione a configuração de espreitamento **privado Azure.** 

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/expressroute-circuit-private-peering.png" alt-text="Visão geral do olho expressoroute":::

1. Selecione a caixa de verificação **Enable Global Reach** e, em seguida, selecione Add Global **Reach** para abrir a página de configuração Add *Global Reach.*

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/private-peering-enable-global-reach.png" alt-text="Permitir o alcance global a partir do espreitamento privado":::

1. Na página de configuração *Add Global Reach,* dê um nome a esta configuração. Selecione o *circuito ExpressRoute* a que pretende ligar este circuito e entrar num **IPv4 /29** para a *sub-rede Global Reach*. Utilizamos endereços IP nesta sub-rede para estabelecer conectividade entre os dois circuitos ExpressRoute. Não utilize os endereços nesta sub-rede nas suas redes virtuais Azure ou na sua rede no local. **Selecione Adicionar** para adicionar o circuito à configuração de peering privada.

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/add-global-reach-configuration.png" alt-text="Página de configuração do Alcance Global":::

1. **Selecione Guardar** para completar a configuração De Alcance Global. Quando a operação terminar, terá conectividade entre as suas duas redes no local através dos dois circuitos ExpressRoute.

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/save-private-peering-configuration.png" alt-text="Salvar configuração de espreitamento privado":::

### <a name="expressroute-circuits-in-different-azure-subscriptions"></a>Circuitos ExpressRoute em diferentes subscrições do Azure

Se os dois circuitos não estiverem na mesma assinatura Azure, vai precisar de autorização. Na configuração seguinte, a autorização é gerada a partir da subscrição do circuito 2. A chave de autorização é então passada para o circuito 1.

1. Gere uma chave de autorização.

   :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/create-authorization-expressroute-circuit.png" alt-text="Gerar chave de autorização"::: 

   Tome nota da identificação do circuito do circuito 2 e da chave de autorização.

1. Selecione a configuração de espreitamento **privado Azure.** 

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/expressroute-circuit-private-peering.png" alt-text="Visão geral do circuito 1":::

1. Selecione a caixa de verificação **Enable Global Reach** e, em seguida, selecione Add Global **Reach** para abrir a página de configuração Add *Global Reach.*

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/private-peering-enable-global-reach.png" alt-text="Permitir o alcance global a partir do circuito 1":::

1. Na página de configuração *Add Global Reach,* dê um nome a esta configuração. Verifique a caixa **de autorização Reden.** Introduza a **Chave de Autorização** e o ID do circuito **ExpressRoute** gerado e obtido no Passo 1. Em seguida, forneça um **IPv4 /29** para a *sub-rede Global Reach*. Utilizamos endereços IP nesta sub-rede para estabelecer conectividade entre os dois circuitos ExpressRoute. Não utilize os endereços nesta sub-rede nas suas redes virtuais Azure ou na sua rede no local. **Selecione Adicionar** para adicionar o circuito à configuração de peering privada.

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/add-global-reach-configuration-with-authorization.png" alt-text="Adicionar Alcance Global com chave de autorização":::

1. **Selecione Guardar** para completar a configuração De Alcance Global. Quando a operação terminar, terá conectividade entre as suas duas redes no local através dos dois circuitos ExpressRoute.

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/save-private-peering-configuration.png" alt-text="Salvar configuração de espreitamento privado no circuito 1":::

## <a name="verify-the-configuration"></a>Verificar a configuração

Verifique a configuração De Alcance Global selecionando *o espreitamento privado* sob a configuração do circuito ExpressRoute. Quando configurado corretamente, a sua configuração deve ser a seguinte:

:::image type="content" source="./media/expressroute-howto-set-global-reach-portal/verify-global-reach-configuration.png" alt-text="Verificar configuração de Alcance Global":::

## <a name="disable-connectivity"></a>Desativar a conectividade

Tem duas opções quando se trata de desativar o Global Reach. Para desativar a conectividade entre todos os circuitos, desmarque **o Enable Global Reach** para desativar a conectividade entre todos os circuitos. Para desativar a conectividade entre um circuito individual, selecione o botão de exclusão ao lado do *nome Global Reach* para remover a conectividade entre eles. Em seguida, **selecione Guardar** para concluir a operação.

:::image type="content" source="./media/expressroute-howto-set-global-reach-portal/disable-global-reach-configuration.png" alt-text="Desativar a configuração do Alcance Global":::

Após a conclusão da operação, já não tem conectividade entre a rede no local através dos circuitos ExpressRoute.

## <a name="next-steps"></a>Passos seguintes
1. [Saiba mais sobre o ExpressRoute Global Reach](expressroute-global-reach.md)
2. [Verificar conectividade ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
3. [Ligue um circuito ExpressRoute a uma rede virtual Azure](expressroute-howto-linkvnet-arm.md)
