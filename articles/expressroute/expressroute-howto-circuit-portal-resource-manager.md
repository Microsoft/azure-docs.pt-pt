---
title: Tutorial - Criar e modificar um circuito com a ExpressRoute
description: Neste tutorial, aprenda a criar, fornecer, verificar, atualizar, excluir e desprovisionar um circuito ExpressRoute.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/20/2018
ms.author: cherylmc
ms.openlocfilehash: 686ac8013879eff8adc4476d56119bbb4a169900
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "74813127"
---
# <a name="tutorial-create-and-modify-an-expressroute-circuit"></a>Tutorial: Criar e modificar um circuito ExpressRoute

> [!div class="op_single_selector"]
> * [Portal do Azure](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [CLI do Azure](howto-circuit-cli.md)
> * [Modelo de Gestor de Recursos Azure](expressroute-howto-circuit-resource-manager-template.md)
> * [Vídeo - Portal Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (clássico)](expressroute-howto-circuit-classic.md)
>

Este artigo ajuda-o a criar um circuito ExpressRoute utilizando o portal Azure e o modelo de implementação do Gestor de Recursos Azure. Também pode verificar o estado, atualizar, excluir ou desfornecer um circuito.

## <a name="before-you-begin"></a>Antes de começar

* Reveja os [pré-requisitos](expressroute-prerequisites.md) e [os fluxos de trabalho](expressroute-workflows.md) antes de iniciar a configuração.
* Certifique-se de que tem acesso ao [portal Azure.](https://portal.azure.com)
* Certifique-se de que tem permissões para criar novos recursos de networking. Contacte o administrador da sua conta se não tiver as permissões certas.
* Pode [ver um vídeo](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit) antes de começar para entender melhor os passos.

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>Criar e fornecer um circuito ExpressRoute

### <a name="1-sign-in-to-the-azure-portal"></a>1. Inscreva-se no portal Azure

Num browser, navegue para o [Portal do Azure](https://portal.azure.com) e inicie sessão com a sua conta do Azure.

### <a name="2-create-a-new-expressroute-circuit"></a>2. Criar um novo circuito ExpressRoute

> [!IMPORTANT]
> O circuito ExpressRoute é faturado a partir do momento em que uma chave de serviço é emitida. Certifique-se de que executa esta operação quando o fornecedor de conectividade estiver pronto para fornecer o circuito.

Pode criar um circuito ExpressRoute selecionando a opção de criar um novo recurso. 

1. No menu do portal do Azure ou a partir da **Home Page**, selecione **Criar um recurso**. Selecione **Rede** > **ExpressRoute,** como mostrado na seguinte imagem:

   ![Criar um circuito do ExpressRoute](./media/expressroute-howto-circuit-portal-resource-manager/create-an-expressroute-circuit.png)

2. Depois de clicar no **ExpressRoute,** verá a página do **circuito Create ExpressRoute.** Quando estiver a preencher os valores nesta página, certifique-se de que especifica o nível SKU correto (Standard, ou Premium) e o modelo de faturação de medição de dados (Ilimitado ou Medido).

   ![Configure o nível SKU e a medição de dados](./media/expressroute-howto-circuit-portal-resource-manager/createcircuit.png)

   * **O tier** determina se está ativado um padrão ExpressRoute ou um add-on premium ExpressRoute. Pode especificar **standard** para obter o SKU ou **Premium** padrão para o complemento premium.
   * **A medição de dados** determina o tipo de faturação. Pode especificar **o Medidor** para um plano de dados medido e **ilimitado** para um plano de dados ilimitado. Note que pode alterar o tipo de faturação de **Medido** para **Ilimitado**.

     > [!IMPORTANT]
     > Não se pode mudar o tipo de **Ilimitado** para **Medido.**

   * **Localização de observação** é o local físico onde você está olhando com a Microsoft.

     > [!IMPORTANT]
     > O Local de Observação indica a [localização física](expressroute-locations.md) onde está a espreitar com a Microsoft. Isto **não** está ligado à propriedade "Localização", que se refere à geografia onde está localizado o Fornecedor de Recursos da Rede Azure. Embora não estejam relacionados, é uma boa prática escolher um Fornecedor de Recursos de Rede geograficamente próximo da Localização de Peering do circuito.

### <a name="3-view-the-circuits-and-properties"></a>3. Ver os circuitos e propriedades

**Ver todos os circuitos**

Pode ver todos os circuitos que criou selecionando **Todos os recursos** no menu do lado esquerdo.

![Ver circuitos](./media/expressroute-howto-circuit-portal-resource-manager/listresource.png)

**Ver as propriedades**

Pode ver as propriedades do circuito selecionando-o. Na página **'Visão Geral'** do seu circuito, a chave de serviço aparece no campo da chave de serviço. Deve copiar a chave de serviço para o seu circuito e passá-la ao prestador de serviços para completar o processo de provisionamento. A chave de serviço de circuito é específica do seu circuito.

![Ver propriedades](./media/expressroute-howto-circuit-portal-resource-manager/servicekey1.png)

### <a name="4-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>4. Envie a chave de serviço ao seu fornecedor de conectividade para o fornecimento

Nesta página, o estatuto do **Fornecedor** fornece informações sobre o estado atual de prestação do serviço-prestador. **O estado** do circuito fornece o estado do lado da Microsoft. Para obter mais informações sobre os estados de fornecimento de circuitos, consulte o artigo [Workflows.](expressroute-workflows.md#expressroute-circuit-provisioning-states)

Quando cria um novo circuito ExpressRoute, o circuito encontra-se no seguinte estado:

Estado do prestador: Não previsto<BR>
Estado do circuito: Ativado

![Iniciar processo de provisionamento](./media/expressroute-howto-circuit-portal-resource-manager/status.png)

O circuito muda para o seguinte estado quando o fornecedor de conectividade está em processo de habilitação para si:

Estado do prestador: Provisionamento<BR>
Estado do circuito: Ativado

Para poder utilizar um circuito ExpressRoute, deve estar no seguinte estado:

Estado do prestador: Provisionado<BR>
Estado do circuito: Ativado

### <a name="5-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>5. Verifique periodicamente o estado e o estado da chave do circuito

Pode ver as propriedades do circuito em que está interessado selecionando-o. Verifique o **estado do Fornecedor** e certifique-se de que se mudou para o **Provisioned** antes de continuar.

![Estado do circuito e do prestador](./media/expressroute-howto-circuit-portal-resource-manager/provisioned.png)

### <a name="6-create-your-routing-configuration"></a>6. Crie a sua configuração de encaminhamento

Para obter instruções passo a passo, consulte o artigo de configuração de [encaminhamento](expressroute-howto-routing-portal-resource-manager.md) do circuito ExpressRoute para criar e modificar os pares de circuitos.

> [!IMPORTANT]
> Estas instruções aplicam-se apenas a circuitos que são criados com prestadores de serviços que oferecem serviços de conectividade camada 2. Se estiver a utilizar um prestador de serviços que oferece serviços geridos de camada 3 (tipicamente um VPN IP, como o MPLS), o seu fornecedor de conectividade configura e gere o encaminhamento para si.

### <a name="7-link-a-virtual-network-to-an-expressroute-circuit"></a>7. Ligar uma rede virtual a um circuito ExpressRoute

Em seguida, ligue uma rede virtual ao circuito ExpressRoute. Utilize as redes virtuais de ligação ao artigo dos [circuitos ExpressRoute](expressroute-howto-linkvnet-arm.md) quando trabalhar com o modelo de implementação do Gestor de Recursos.

## <a name="getting-the-status-of-an-expressroute-circuit"></a><a name="status"></a>Obtenção do estatuto de circuito ExpressRoute

Pode visualizar o estado de um circuito selecionando-o e visualizando a página 'Overview'.

## <a name="modifying-an-expressroute-circuit"></a><a name="modify"></a>Modificar um circuito ExpressRoute

Pode modificar certas propriedades de um circuito ExpressRoute sem afetar a conectividade. Pode modificar a largura de banda, SKU, modelo de faturação e permitir operações clássicas na página **de Configuração.** Para obter informações sobre limites e limitações, consulte o [ExpressRoute FAQ](expressroute-faqs.md).

Pode executar as seguintes tarefas sem tempo de inatividade:

* Ative ou desative um complemento ExpressRoute Premium para o seu circuito ExpressRoute.
* Aumente a largura de banda do seu circuito ExpressRoute, desde que haja capacidade disponível na porta.

  > [!IMPORTANT]
  > Não é suportada a degradação da largura de banda de um circuito.

* Alterar o plano de medição de *Dados Medidos* para *Dados Ilimitados*.

  > [!IMPORTANT]
  > Não é suportada a alteração do plano de medição de Dados Ilimitados para Dados Medidos.

* Pode ativar e desativar *permitir operações clássicas.*
  > [!IMPORTANT]
  > Poderá ter de recriar o circuito ExpressRoute se houver uma capacidade inadequada na porta existente. Não é possível atualizar o circuito se não houver capacidade adicional disponível nesse local.
  >
  > Embora possa atualizar perfeitamente a largura de banda, não é possível reduzir a largura de banda de um circuito ExpressRoute sem interrupções. A degradação da largura de banda requer que desprovisionie o circuito ExpressRoute e, em seguida, reforte um novo circuito ExpressRoute.
  >
  > Desativar a operação de addon Premium pode falhar se estiver a utilizar recursos superiores aos permitidos para o circuito padrão.

Para modificar um circuito ExpressRoute, clique na **Configuração**.

![Modificar circuito](./media/expressroute-howto-circuit-portal-resource-manager/modify-circuit-configuration.png)

## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a><a name="delete"></a>Deprovisioning and deleting an ExpressRoute circuit (Desaprovisionar e eliminar um circuito do ExpressRoute)

Pode eliminar o circuito ExpressRoute selecionando o ícone **de exclusão.** Tenha em atenção as seguintes informações:

* Tem de desassociar todas as redes virtuais do circuito do ExpressRoute. Se esta operação falhar, verifique se alguma rede virtual está ligada ao circuito.
* Se o estado de provisionamento do prestador de serviços do circuito ExpressRoute estiver **provisionamento** ou **provisionado,** deve trabalhar com o seu prestador de serviços para desprovisionar o circuito do seu lado. Continuamos a reservar recursos e a cobrar até que o prestador de serviços complete a desprovisionamento do circuito e nos notifique.
* Se o prestador de serviços tiver desprovisionado o circuito (o estado de provisionamento do prestador de serviços está definido para **Não provisionado),** pode eliminar o circuito. Isto interrompe a faturação do circuito.

## <a name="next-steps"></a>Passos seguintes

Depois de criar o seu circuito, continue com os seguintes passos:

* [Crie e modifique o encaminhamento para o seu circuito ExpressRoute](expressroute-howto-routing-portal-resource-manager.md)
* [Ligue a sua rede virtual ao circuito ExpressRoute](expressroute-howto-linkvnet-arm.md)
