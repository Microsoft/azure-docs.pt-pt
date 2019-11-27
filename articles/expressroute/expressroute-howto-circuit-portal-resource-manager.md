---
title: Tutorial – criar e modificar um circuito com o ExpressRoute
description: Neste tutorial, saiba como criar, provisionar, verificar, atualizar, excluir e desprovisionar um circuito do ExpressRoute.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/20/2018
ms.author: cherylmc
ms.openlocfilehash: 7327031a7cd05674e9823f21601aab34c859f540
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2019
ms.locfileid: "74423561"
---
# <a name="tutorial-create-and-modify-an-expressroute-circuit"></a>Tutorial: criar e modificar um circuito do ExpressRoute

> [!div class="op_single_selector"]
> * [Portal do Azure](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [CLI do Azure](howto-circuit-cli.md)
> * [Modelo do Azure Resource Manager](expressroute-howto-circuit-resource-manager-template.md)
> * [portal do Azure de vídeo](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (clássico)](expressroute-howto-circuit-classic.md)
>

Este artigo ajuda-o a criar um circuito ExpressRoute com o portal do Azure e o modelo de implementação Azure Resource Manager. Também pode verificar o estado, atualizar, eliminar ou desaprovisionar um circuito.

## <a name="before-you-begin"></a>Antes de começar

* Examine os [pré-requisitos](expressroute-prerequisites.md) e os [fluxos de trabalho](expressroute-workflows.md) antes de começar a configuração.
* Verifique se você tem acesso ao [portal do Azure](https://portal.azure.com).
* Certifique-se de que tem permissões para criar novos recursos de rede. Se não tiver as permissões corretas, contacte o administrador de conta.
* Você pode [exibir um vídeo](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit) antes de começar a fim de entender melhor as etapas.

## <a name="create"></a>Criar e provisionar um circuito do ExpressRoute

### <a name="1-sign-in-to-the-azure-portal"></a>1. entre no portal do Azure

Num browser, navegue para o [Portal do Azure](https://portal.azure.com) e inicie sessão com a sua conta do Azure.

### <a name="2-create-a-new-expressroute-circuit"></a>2. criar um novo circuito do ExpressRoute

> [!IMPORTANT]
> O circuito de ExpressRoute é cobrado a partir do momento numa que chave de serviço é emitida. Certifique-se de que efetue esta operação quando o fornecedor de conectividade estiver pronto para aprovisionar o circuito.

1. Pode criar um circuito do ExpressRoute, selecionando a opção para criar um novo recurso. Clique em **criar um recurso** > **rede** > **ExpressRoute**, conforme mostrado na imagem a seguir:

   ![Criar um circuito do ExpressRoute](./media/expressroute-howto-circuit-portal-resource-manager/createcircuit1.png)
2. Depois de clicar em **ExpressRoute**, você verá a página **criar circuito do ExpressRoute** . Quando estiver preenchendo os valores nesta página, certifique-se de que especifica a camada de SKU correta (Standard ou Premium) e dados de medição de modelo de faturação (sem limite ou ilimitados).

   ![Configurar o escalão de SKU e, em seguida, medição de dados](./media/expressroute-howto-circuit-portal-resource-manager/createcircuit.png)

   * **Camada** determina se um complemento do expressroute Standard ou do expressroute Premium está habilitado. Você pode especificar **Standard** para obter o SKU Standard ou **Premium** para o complemento Premium.
   * A **medição de dados** determina o tipo de cobrança. Você pode especificar o **medidor** para um plano de dados limitado e **ilimitado** para um plano de dados ilimitado. Observe que você pode alterar o tipo de cobrança de **limitado** para **ilimitado**.

     > [!IMPORTANT]
     > Não é possível alterar o tipo de **ilimitado** para **limitado**.

   * O **local de emparelhamento** é o local físico em que você está emparelhando com a Microsoft.

     > [!IMPORTANT]
     > O local de emparelhamento indica o [local físico](expressroute-locations.md) em que você está emparelhando com a Microsoft. Isso **não** está vinculado à propriedade "Location", que se refere à geografia onde o provedor de recursos de rede do Azure está localizado. Embora não estejam relacionados, é uma boa prática para escolher um fornecedor de recursos de rede geograficamente próximo a localização de Peering do circuito.

### <a name="3-view-the-circuits-and-properties"></a>3. exibir os circuitos e as propriedades

**Exibir todos os circuitos**

Você pode exibir todos os circuitos que criou selecionando **todos os recursos** no menu do lado esquerdo.

![Circuitos do Vista](./media/expressroute-howto-circuit-portal-resource-manager/listresource.png)

**Exibir as propriedades**

Pode ver as propriedades do circuito ao selecioná-la. Na página **visão geral** do circuito, a chave de serviço aparece no campo chave de serviço. Tem de copiar a chave de serviço para o seu circuito e passá-la para baixo para o fornecedor de serviços para concluir o processo de aprovisionamento. A chave de serviço de circuito é específica para o seu circuito.

![Ver propriedades](./media/expressroute-howto-circuit-portal-resource-manager/servicekey1.png)

### <a name="4-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>4. enviar a chave de serviço para o provedor de conectividade para provisionamento

Nessa página, **status do provedor** fornece informações sobre o estado atual do provisionamento no lado do provedor de serviço. O **status do circuito** fornece o estado no lado da Microsoft. Para obter mais informações sobre os Estados de provisionamento de circuito, consulte o artigo [fluxos de trabalho](expressroute-workflows.md#expressroute-circuit-provisioning-states) .

Quando cria um novo circuito do ExpressRoute, o circuito está no estado seguinte:

Estado do fornecedor: não aprovisionado<BR>
Circuito do Estado: ativada

![Iniciar o processo de aprovisionamento](./media/expressroute-howto-circuit-portal-resource-manager/status.png)

O circuito muda para o seguinte estado quando o fornecedor de conectividade está no processo de ativá-la para:

Estado do fornecedor: aprovisionamento<BR>
Circuito do Estado: ativada

Para poder ser capaz de utilizar um circuito do ExpressRoute, tem de estar no seguinte estado:

Estado do fornecedor: aprovisionado<BR>
Circuito do Estado: ativada

### <a name="5-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>5. verificar periodicamente o status e o estado da chave de circuito

Pode ver as propriedades do circuito que tem interesse, selecioná-la. Verifique o **status do provedor** e certifique-se de que ele foi movido para **provisionado** antes de continuar.

![Estado do circuito e fornecedor](./media/expressroute-howto-circuit-portal-resource-manager/provisioned.png)

### <a name="6-create-your-routing-configuration"></a>6. criar sua configuração de roteamento

Para obter as instruções passo a passo, consulte o artigo [configuração de roteamento de circuito do ExpressRoute](expressroute-howto-routing-portal-resource-manager.md) para criar e modificar emparelhamentos de circuito.

> [!IMPORTANT]
> Estas instruções aplicam-se apenas aos circuitos criados com fornecedores de serviços que oferecem serviços de conectividade de 2 de camada. Se estiver a utilizar um fornecedor de serviços que oferece geridos de camada 3 serviços (normalmente uma VPN de IP, como MPLS), o seu fornecedor de conectividade configura e gere o encaminhamento por si.

### <a name="7-link-a-virtual-network-to-an-expressroute-circuit"></a>7. vincular uma rede virtual a um circuito do ExpressRoute

Em seguida, ligar uma rede virtual para o seu circuito do ExpressRoute. Use o artigo [vinculando redes virtuais a circuitos do ExpressRoute](expressroute-howto-linkvnet-arm.md) ao trabalhar com o modelo de implantação do Resource Manager.

## <a name="status"></a>Obtendo o status de um circuito do ExpressRoute

Pode ver o estado de um circuito ao selecioná-lo e ver a página de descrição geral.

## <a name="modify"></a>Modificando um circuito do ExpressRoute

Pode modificar algumas propriedades de um circuito do ExpressRoute sem afetar a conectividade. Você pode modificar a largura de banda, SKU, modelo de cobrança e permitir operações clássicas na página de **configuração** . Para obter informações sobre limites e limitações, consulte as [perguntas frequentes sobre o ExpressRoute](expressroute-faqs.md).

Pode efetuar as seguintes tarefas sem tempo de inatividade:

* Ativar ou desativar um suplemento ExpressRoute Premium para o seu circuito do ExpressRoute.
* Aumente a largura de banda do circuito do ExpressRoute, desde que haja capacidade na porta.

  > [!IMPORTANT]
  > Desatualização de largura de banda de um circuito não é suportada.

* Altere o plano de medição de *dados limitados* para *dados ilimitados*.

  > [!IMPORTANT]
  > Não é suportada a alteração do plano de medição de dados ilimitados para dados limitados.

* Você pode habilitar e desabilitar *permitir operações clássicas*.
  > [!IMPORTANT]
  > Poderá ter de recriar o circuito do ExpressRoute, se houver a capacidade inadequada na porta existente. Não é possível atualizar o circuito se não existir nenhuma capacidade adicional disponível naquele local.
  >
  > Embora pode atualizar facilmente a largura de banda, não é possível reduzir a largura de banda de um circuito do ExpressRoute sem interrupção. Desatualização de largura de banda exige que desaprovisionar o circuito do ExpressRoute e, em seguida, reaprovisionar um novo circuito do ExpressRoute.
  >
  > Desativar a operação de suplemento Premium pode falhar se estiver a utilizar os recursos que são maiores que o que é permitido para o circuito standard.

Para modificar um circuito do ExpressRoute, clique em **configuração**.

![Modificar o circuito](./media/expressroute-howto-circuit-portal-resource-manager/modifycircuit.png)

## <a name="delete"></a>Desprovisionamento e exclusão de um circuito do ExpressRoute

Você pode excluir o circuito do ExpressRoute selecionando o ícone **excluir** . Tenha em atenção as seguintes informações:

* Tem de desassociar todas as redes virtuais do circuito do ExpressRoute. Se esta operação falhar, verifique se quaisquer redes virtuais são ligadas ao circuito.
* Se o estado de provisionamento do provedor de serviço de circuito do **ExpressRoute for Provisionando** ou **provisionado** , você deverá trabalhar com seu provedor de serviços para desprovisionar o circuito no lado deles. Continuamos a reservar recursos e a faturação é que o fornecedor de serviços seja concluída o circuito de desaprovisionamento e notifica-nos.
* Se o provedor de serviços tiver desprovisionado o circuito (o estado de provisionamento do provedor de serviços estiver definido como **não provisionado**), você poderá excluir o circuito. Isto interrompe a faturação do circuito.

## <a name="next-steps"></a>Passos seguintes

Depois de criar o seu circuito, continue com os seguintes passos seguintes:

* [Criar e modificar o roteamento para o circuito do ExpressRoute](expressroute-howto-routing-portal-resource-manager.md)
* [Vincular sua rede virtual ao circuito do ExpressRoute](expressroute-howto-linkvnet-arm.md)
