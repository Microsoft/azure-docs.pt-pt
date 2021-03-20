---
title: 'Quickstart: Criar e modificar um circuito com o portal ExpressRoute - Azure'
description: Neste arranque rápido, aprende-se a criar, providenciar, verificar, atualizar, eliminar e desprovisionar um circuito ExpressRoute utilizando o portal Azure.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: quickstart
ms.date: 10/21/2020
ms.author: duau
ms.openlocfilehash: b0fd844021e7398e061072d7939b782616580c1c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96185203"
---
# <a name="quickstart-create-and-modify-an-expressroute-circuit"></a>Quickstart: Criar e modificar um circuito ExpressRoute

Este quickstart mostra-lhe como criar um circuito ExpressRoute utilizando o portal Azure e o modelo de implementação do Azure Resource Manager. Também pode verificar o estado, atualizar, excluir ou desprovisionar um circuito.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Reveja os [pré-requisitos](expressroute-prerequisites.md) e [fluxos de trabalho](expressroute-workflows.md) antes de iniciar a configuração.
* Pode [ver um vídeo](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit) antes de começar a entender melhor os passos.

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>Criar e providenciar um circuito ExpressRoute

### <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Num browser, navegue para o [Portal do Azure](https://portal.azure.com) e inicie sessão com a sua conta do Azure.

### <a name="create-a-new-expressroute-circuit"></a>Criar um novo circuito ExpressRoute

> [!IMPORTANT]
> O seu circuito ExpressRoute é faturado a partir do momento em que uma chave de serviço é emitida. Certifique-se de que efetua esta operação quando o fornecedor de conectividade estiver pronto para o fornecimento do circuito.

Pode criar um circuito ExpressRoute selecionando a opção de criar um novo recurso. 

1. No menu do portal do Azure, selecione **Criar um recurso**. **Selecione Networking**  >  **ExpressRoute,** como mostra a seguinte imagem:

    :::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/create-expressroute-circuit-menu.png" alt-text="Criar um circuito do ExpressRoute":::

2. Depois de selecionar **ExpressRoute,** verá a página **Create ExpressRoute.** Forneça o **Grupo de Recursos,** **Região** e  **Nome** para o circuito. Em seguida, selecione **Seguinte: Configuração >**.

    :::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-create-basic.png" alt-text="Configure o grupo de recursos e a região":::

3. Quando estiver a preencher os valores desta página, certifique-se de que especifica o nível SKU correto (Local, Standard ou Premium) e o modelo de faturação de contador de dados (Ilimitado ou Medido).

    :::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-create-configuration.png" alt-text="Configure o circuito":::
    
    * **O tipo de porta** determina se está a ligar-se a um prestador de serviços ou diretamente à rede global da Microsoft num local de observação.
    * **Criar novos ou importar a partir de clássicos** determina se um novo circuito está a ser criado ou se está a migrar um circuito clássico para o Azure Resource Manager.
    * **O fornecedor** é o fornecedor de serviços de internet a quem irá solicitar o seu serviço.
    * **O Local de Observação** é o local físico onde está a espreitar com a Microsoft.

    > [!IMPORTANT]
    > A Localização De Pares indica a [localização física](expressroute-locations.md) onde está a espreitar com a Microsoft. Isto **não** está ligado à propriedade "Localização", que se refere à geografia onde está localizado o Fornecedor de Recursos da Rede Azure. Embora não estejam relacionados, é uma boa prática escolher um Fornecedor de Recursos de Rede geograficamente próximo da Localização De Pares do circuito.

    * **A SKU** determina se está ativado um padrão ExpressRoute local, ExpressRoute ou um addon premium ExpressRoute. Você pode especificar **Local** para obter o SKU local, **Standard** para obter o SKU padrão ou **Premium** para o suplemento premium. Pode alterar o SKU para ativar o complemento premium.
    > [!IMPORTANT]
    > Não é possível alterar o SKU de **Standard/Premium** para **Local.**
    
    * **O modelo de faturação** determina o tipo de faturação. Pode especificar **medido** para um plano de dados medido e **ilimitado** para um plano de dados ilimitado. Pode alterar o tipo de faturação de **Medido para** **Ilimitado**.

    > [!IMPORTANT]
    > Não é possível alterar o tipo de **Ilimitado** para **Medido.**

    * **Permitir a operação clássica** permitirá que redes virtuais clássicas estejam ligados ao circuito.

### <a name="view-the-circuits-and-properties"></a>Ver os circuitos e propriedades

**Ver todos os circuitos**

Pode ver todos os circuitos que criou selecionando **Todos os serviços > circuitos ExpressRoute > > no** menu do lado esquerdo.

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-menu.png" alt-text="Menu de circuito expressroute":::

Todos os circuitos Expressroute criados na subscrição aparecerão aqui.

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-list.png" alt-text="Lista de circuitos expressroute":::

**Ver as propriedades**

Pode ver as propriedades do circuito selecionando-as. Na página **'Vista Geral'** do seu circuito, a tecla de serviço aparece no campo de chaves de serviço. Consulte a chave de serviço para o seu circuito e forneça-a ao prestador de serviços para completar o processo de fornecimento. A chave de serviço é específica do seu circuito.

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-overview.png" alt-text="Ver propriedades":::

### <a name="send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>Envie a chave de serviço para o seu fornecedor de conectividade para provisionamento

Nesta página, o **estado do Fornecedor** dá-lhe o estado atual de prestação do lado do prestador de serviços. **O estado do circuito** fornece-lhe o estado do lado da Microsoft. Para obter mais informações sobre estados de provisão de circuitos, consulte o artigo [Workflows.](expressroute-workflows.md#expressroute-circuit-provisioning-states)

Quando se cria um novo circuito ExpressRoute, o circuito encontra-se no seguinte estado:

Estatuto do fornecedor: **Não previsto**<BR>
Estado do circuito: **Ativado**

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-overview-provisioning-state.png" alt-text="Inicia o processo de provisionamento":::

O circuito muda para o seguinte estado quando o fornecedor de conectividade está atualmente a permitir-lhe:

Estatuto do fornecedor: **Provisionamento**<BR>
Estado do circuito: **Ativado**

Para utilizar o circuito ExpressRoute, deve estar no seguinte estado:

Estatuto do fornecedor: **Provisionado**<BR>
Estado do circuito: **Ativado**

### <a name="periodically-check-the-status-and-the-state-of-the-circuit-key"></a>Verifique periodicamente o estado e o estado da chave do circuito

Pode ver as propriedades do circuito em que está interessado ao selecioná-lo. Verifique o **estado do Fornecedor** e certifique-se de que se mudou para **Provisionado** antes de continuar.

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/provisioned.png" alt-text="Estado do circuito e do fornecedor":::

### <a name="create-your-routing-configuration"></a>Crie a sua configuração de encaminhamento

Para obter instruções passo a passo, consulte o artigo de configuração do [roteamento do circuito ExpressRoute](expressroute-howto-routing-portal-resource-manager.md) para criar e modificar os pares de circuitos.

> [!IMPORTANT]
> Estas instruções aplicam-se apenas aos circuitos criados com prestadores de serviços que oferecem serviços de conectividade de camada 2. Se estiver a utilizar um prestador de serviços que oferece serviços geridos de camada 3 (normalmente um IP VPN, como MPLS), o seu fornecedor de conectividade configura e gere o encaminhamento para si.

### <a name="link-a-virtual-network-to-an-expressroute-circuit"></a>Ligar uma rede virtual a um circuito ExpressRoute

Em seguida, ligue uma rede virtual ao seu circuito ExpressRoute. Utilize as redes virtuais de ligação ao artigo [dos circuitos ExpressRoute](expressroute-howto-linkvnet-arm.md) quando trabalhar com o modelo de implementação do Gestor de Recursos.

## <a name="getting-the-status-of-an-expressroute-circuit"></a><a name="status"></a>Obtenção do estatuto de circuito ExpressRoute

Pode ver o estado de um circuito selecionando-o e visualizando a página 'Vista Geral'.

## <a name="modifying-an-expressroute-circuit"></a><a name="modify"></a>Modificação de um circuito ExpressRoute

Pode modificar certas propriedades de um circuito ExpressRoute sem afetar a conectividade. Pode modificar a largura de banda, SKU, modelo de faturação e permitir operações clássicas na página **Configuração.** Para obter informações sobre limites e limitações, consulte as [FAQ ExpressRoute](expressroute-faqs.md).

Pode fazer as seguintes tarefas sem tempo de inatividade:

* Ative ou desative um addon ExpressRoute Premium para o seu circuito ExpressRoute.

> [!IMPORTANT]
  > A alteração do SKU de **Standard/Premium** para **Local** não é suportada.

* Aumente a largura de banda do seu circuito ExpressRoute, desde que haja capacidade disponível na porta.

  > [!IMPORTANT]
  > A redução da largura de banda de um circuito não é suportada.

* Altere o plano de medição de *Dados Medidos* para *Dados Ilimitados*.

  > [!IMPORTANT]
  > A alteração do plano de medição de **Dados Ilimitados** para **Dados Medidos** não é suportada.

* Pode ativar e desativar *permitir operações clássicas.*
  > [!IMPORTANT]
  > Poderá ter de recriar o circuito ExpressRoute se não houver capacidade inadequada na porta existente. Não é possível atualizar o circuito se não houver capacidade adicional disponível nesse local.
  >
  > Embora possa atualizar perfeitamente a largura de banda, não pode reduzir a largura de banda de um circuito ExpressRoute sem interrupções. A redução da largura de banda requer que desprovisione o circuito ExpressRoute e, em seguida, reprovisione um novo circuito ExpressRoute.
  >
  > A desativação da operação de complemento Premium pode falhar se estiver a utilizar recursos superiores aos permitidos para o circuito padrão.

Para modificar um circuito ExpressRoute, **selecione Configuração**.

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-configuration.png" alt-text="Circuito de modificação":::

## <a name="deprovisioning-an-expressroute-circuit"></a><a name="delete"></a>Desprovisionamento de um circuito ExpressRoute

Se o estado de prestação do serviço de circuito ExpressRoute for **Provisioning** ou **Provisioned,** deve trabalhar com o seu prestador de serviços para desprovisionar o circuito do seu lado. Continuamos a reservar recursos e a cobrar até que o prestador de serviços complete a desprovisionamento do circuito e nos notifique.

> [!NOTE]
>* Deve desvincular *todas as redes virtuais* do circuito ExpressRoute antes de desprovisionar. Se esta operação falhar, verifique se alguma rede virtual está ligada ao circuito.
>* Se o prestador de serviços tiver desprovisionado o circuito (o estado de prestação do prestador de serviços está definido para **Não aprovisionado),** pode apagar o circuito. Isto interrompe a faturação do circuito.


## <a name="clean-up-resources"></a>Limpar os recursos

Pode eliminar o seu circuito ExpressRoute selecionando o ícone **Eliminar.** Certifique-se de que o estado do fornecedor não é *previsto* antes de prosseguir.

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-delete.png" alt-text="Excluir circuito":::

## <a name="next-steps"></a>Passos seguintes

Depois de criar o seu circuito, continue com o seguinte passo:

> [!div class="nextstepaction"]
> [Crie e modifique o encaminhamento para o seu circuito ExpressRoute](expressroute-howto-routing-portal-resource-manager.md)
