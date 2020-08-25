---
title: Tutorial - Criar e modificar um circuito com o ExpressRoute
description: Neste tutorial, aprenda a criar, providenciar, verificar, atualizar, eliminar e desprovisionar um circuito ExpressRoute.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/20/2018
ms.author: cherylmc
ms.openlocfilehash: 686ac8013879eff8adc4476d56119bbb4a169900
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/25/2020
ms.locfileid: "74813127"
---
# <a name="tutorial-create-and-modify-an-expressroute-circuit"></a>Tutorial: Criar e modificar um circuito ExpressRoute

> [!div class="op_single_selector"]
> * [Portal do Azure](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [CLI do Azure](howto-circuit-cli.md)
> * [Modelo Azure Resource Manager](expressroute-howto-circuit-resource-manager-template.md)
> * [Vídeo - Portal Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (clássico)](expressroute-howto-circuit-classic.md)
>

Este artigo ajuda-o a criar um circuito ExpressRoute utilizando o portal Azure e o modelo de implementação do Azure Resource Manager. Também pode verificar o estado, atualizar, excluir ou desprovisionar um circuito.

## <a name="before-you-begin"></a>Before you begin

* Reveja os [pré-requisitos](expressroute-prerequisites.md) e [fluxos de trabalho](expressroute-workflows.md) antes de iniciar a configuração.
* Certifique-se de que tem acesso ao [portal Azure.](https://portal.azure.com)
* Certifique-se de que tem permissões para criar novos recursos de rede. Contacte o administrador da sua conta se não tiver as permissões certas.
* Pode [ver um vídeo](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit) antes de começar para entender melhor os passos.

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>Criar e providenciar um circuito ExpressRoute

### <a name="1-sign-in-to-the-azure-portal"></a>1. Inscreva-se no portal Azure

Num browser, navegue para o [Portal do Azure](https://portal.azure.com) e inicie sessão com a sua conta do Azure.

### <a name="2-create-a-new-expressroute-circuit"></a>2. Criar um novo circuito ExpressRoute

> [!IMPORTANT]
> O seu circuito ExpressRoute é faturado a partir do momento em que uma chave de serviço é emitida. Certifique-se de que efetua esta operação quando o fornecedor de conectividade estiver pronto para o fornecimento do circuito.

Pode criar um circuito ExpressRoute selecionando a opção de criar um novo recurso. 

1. No menu do portal do Azure ou a partir da **Home Page**, selecione **Criar um recurso**. **Selecione Networking**  >  **ExpressRoute,** como mostra a seguinte imagem:

   ![Criar um circuito do ExpressRoute](./media/expressroute-howto-circuit-portal-resource-manager/create-an-expressroute-circuit.png)

2. Depois de clicar no **ExpressRoute,** verá a página do **circuito Create ExpressRoute.** Quando estiver a preencher os valores desta página, certifique-se de que especifica o nível SKU correto (Standard, ou Premium) e o modelo de faturação de contadores de dados (Ilimitado ou Medido).

   ![Configure o nível SKU e a medição de dados](./media/expressroute-howto-circuit-portal-resource-manager/createcircuit.png)

   * **Tier** determina se está ativado um padrão ExpressRoute ou um addon premium ExpressRoute. Pode especificar **standard** para obter o SKU ou **Premium** padrão para o suplemento premium.
   * **A medição de dados** determina o tipo de faturação. Pode especificar **medido** para um plano de dados medido e **ilimitado** para um plano de dados ilimitado. Note que pode alterar o tipo de faturação de **Medido para** **Ilimitado**.

     > [!IMPORTANT]
     > Não é possível alterar o tipo de **Ilimitado** para **Medido.**

   * **O Local de Observação** é o local físico onde está a espreitar com a Microsoft.

     > [!IMPORTANT]
     > A Localização De Pares indica a [localização física](expressroute-locations.md) onde está a espreitar com a Microsoft. Isto **não** está ligado à propriedade "Localização", que se refere à geografia onde está localizado o Fornecedor de Recursos da Rede Azure. Embora não estejam relacionados, é uma boa prática escolher um Fornecedor de Recursos de Rede geograficamente próximo da Localização De Pares do circuito.

### <a name="3-view-the-circuits-and-properties"></a>3. Ver os circuitos e propriedades

**Ver todos os circuitos**

Pode ver todos os circuitos que criou selecionando **Todos os recursos** no menu do lado esquerdo.

![Ver circuitos](./media/expressroute-howto-circuit-portal-resource-manager/listresource.png)

**Ver as propriedades**

Pode ver as propriedades do circuito selecionando-as. Na página **'Vista Geral'** do seu circuito, a tecla de serviço aparece no campo de chaves de serviço. Tem de copiar a chave de serviço para o seu circuito e passá-la ao prestador de serviços para completar o processo de fornecimento. A chave de serviço de circuito é específica do seu circuito.

![Ver propriedades](./media/expressroute-howto-circuit-portal-resource-manager/servicekey1.png)

### <a name="4-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>4. Envie a chave de serviço para o seu fornecedor de conectividade para o provisionamento

Nesta página, **o estado do Fornecedor** fornece informações sobre o estado atual de prestação do lado do prestador de serviços. **O estado do circuito** fornece o estado do lado da Microsoft. Para obter mais informações sobre estados de provisão de circuitos, consulte o artigo [Workflows.](expressroute-workflows.md#expressroute-circuit-provisioning-states)

Quando se cria um novo circuito ExpressRoute, o circuito encontra-se no seguinte estado:

Estatuto do fornecedor: Não previsto<BR>
Estado do circuito: Ativado

![Iniciar o processo de provisionamento](./media/expressroute-howto-circuit-portal-resource-manager/status.png)

O circuito muda para o seguinte estado quando o fornecedor de conectividade está em processo de permitir-lhe:

Estatuto do fornecedor: Provisionamento<BR>
Estado do circuito: Ativado

Para poder utilizar um circuito ExpressRoute, deve estar no seguinte estado:

Estatuto do fornecedor: Provisionado<BR>
Estado do circuito: Ativado

### <a name="5-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>5. Verifique periodicamente o estado e o estado da chave do circuito

Pode ver as propriedades do circuito em que está interessado ao selecioná-lo. Verifique o **estado do Fornecedor** e certifique-se de que se mudou para **Provisionado** antes de continuar.

![Estado do circuito e do fornecedor](./media/expressroute-howto-circuit-portal-resource-manager/provisioned.png)

### <a name="6-create-your-routing-configuration"></a>6. Crie a sua configuração de encaminhamento

Para obter instruções passo a passo, consulte o artigo de configuração do [roteamento do circuito ExpressRoute](expressroute-howto-routing-portal-resource-manager.md) para criar e modificar os pares de circuitos.

> [!IMPORTANT]
> Estas instruções aplicam-se apenas aos circuitos criados com prestadores de serviços que oferecem serviços de conectividade de camada 2. Se estiver a utilizar um prestador de serviços que oferece serviços geridos de camada 3 (normalmente um IP VPN, como MPLS), o seu fornecedor de conectividade configura e gere o encaminhamento para si.

### <a name="7-link-a-virtual-network-to-an-expressroute-circuit"></a>7. Ligue uma rede virtual a um circuito ExpressRoute

Em seguida, ligue uma rede virtual ao seu circuito ExpressRoute. Utilize as redes virtuais de ligação ao artigo [dos circuitos ExpressRoute](expressroute-howto-linkvnet-arm.md) quando trabalhar com o modelo de implementação do Gestor de Recursos.

## <a name="getting-the-status-of-an-expressroute-circuit"></a><a name="status"></a>Obtenção do estatuto de circuito ExpressRoute

Pode ver o estado de um circuito selecionando-o e visualizando a página 'Vista Geral'.

## <a name="modifying-an-expressroute-circuit"></a><a name="modify"></a>Modificação de um circuito ExpressRoute

Pode modificar certas propriedades de um circuito ExpressRoute sem afetar a conectividade. Pode modificar a largura de banda, SKU, modelo de faturação e permitir operações clássicas na página **Configuração.** Para obter informações sobre limites e limitações, consulte as [FAQ ExpressRoute](expressroute-faqs.md).

Pode executar as seguintes tarefas sem tempo de inatividade:

* Ative ou desative um addon ExpressRoute Premium para o seu circuito ExpressRoute.
* Aumente a largura de banda do seu circuito ExpressRoute, desde que haja capacidade disponível na porta.

  > [!IMPORTANT]
  > A redução da largura de banda de um circuito não é suportada.

* Altere o plano de medição de *Dados Medidos* para *Dados Ilimitados*.

  > [!IMPORTANT]
  > A alteração do plano de medição de Dados Ilimitados para Dados Medidos não é suportada.

* Pode ativar e desativar *permitir operações clássicas.*
  > [!IMPORTANT]
  > Poderá ter de recriar o circuito ExpressRoute se não houver capacidade inadequada na porta existente. Não é possível atualizar o circuito se não houver capacidade adicional disponível nesse local.
  >
  > Embora possa atualizar perfeitamente a largura de banda, não pode reduzir a largura de banda de um circuito ExpressRoute sem interrupções. A redução da largura de banda requer que desprovisione o circuito ExpressRoute e, em seguida, reprovisione um novo circuito ExpressRoute.
  >
  > A desativação da operação de complemento Premium pode falhar se estiver a utilizar recursos superiores aos permitidos para o circuito padrão.

Para modificar um circuito ExpressRoute, clique em **Configuração**.

![Circuito de modificação](./media/expressroute-howto-circuit-portal-resource-manager/modify-circuit-configuration.png)

## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a><a name="delete"></a>Deprovisioning and deleting an ExpressRoute circuit (Desaprovisionar e eliminar um circuito do ExpressRoute)

Pode eliminar o seu circuito ExpressRoute selecionando o ícone **de eliminação.** Tenha em atenção as seguintes informações:

* Tem de desassociar todas as redes virtuais do circuito do ExpressRoute. Se esta operação falhar, verifique se alguma rede virtual está ligada ao circuito.
* Se o estado de prestação do serviço de circuito ExpressRoute for **Provisioning** ou **Provisioned,** deve trabalhar com o seu prestador de serviços para desprovisionar o circuito do seu lado. Continuamos a reservar recursos e a cobrar até que o prestador de serviços complete a desprovisionamento do circuito e nos notifique.
* Se o prestador de serviços tiver desprovisionado o circuito (o estado de prestação do prestador de serviços está definido para **Não aprovisionado),** pode apagar o circuito. Isto interrompe a faturação do circuito.

## <a name="next-steps"></a>Passos seguintes

Depois de criar o seu circuito, continue com os seguintes passos:

* [Crie e modifique o encaminhamento para o seu circuito ExpressRoute](expressroute-howto-routing-portal-resource-manager.md)
* [Ligue a sua rede virtual ao circuito ExpressRoute](expressroute-howto-linkvnet-arm.md)
