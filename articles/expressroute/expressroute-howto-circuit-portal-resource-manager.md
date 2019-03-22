---
title: 'Criar e modificar um circuito do ExpressRoute - portal: Azure | Microsoft Docs'
description: Criar, aprovisionar, certifique-se, atualizar, eliminar e desaprovisionar um circuito do ExpressRoute.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 10/20/2018
ms.author: cherylmc;ganesr
ms.custom: seodec18
ms.openlocfilehash: 16f3ad1aa037dca2e7b8c3e68ae952c27b952711
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58103031"
---
# <a name="create-and-modify-an-expressroute-circuit"></a>Criar e modificar um circuito do ExpressRoute

> [!div class="op_single_selector"]
> * [Portal do Azure](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [CLI do Azure](howto-circuit-cli.md)
> * [Vídeo - portal do Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (clássico)](expressroute-howto-circuit-classic.md)
>

Este artigo ajuda-o a criar um circuito ExpressRoute com o portal do Azure e o modelo de implementação Azure Resource Manager. Também pode verificar o estado, atualizar, eliminar ou desaprovisionar um circuito.

## <a name="before-you-begin"></a>Antes de começar

* Reveja os [pré-requisitos](expressroute-prerequisites.md) e [fluxos de trabalho](expressroute-workflows.md) antes de iniciar a configuração.
* Certifique-se de que tem acesso para o [portal do Azure](https://portal.azure.com).
* Certifique-se de que tem permissões para criar novos recursos de rede. Se não tiver as permissões corretas, contacte o administrador de conta.
* Pode [ver um vídeo](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit) antes de iniciar para compreender melhor os passos.

## <a name="create"></a>Criar e aprovisionar um circuito do ExpressRoute

### <a name="1-sign-in-to-the-azure-portal"></a>1. Iniciar sessão no portal do Azure

Num browser, navegue para o [Portal do Azure](https://portal.azure.com) e inicie sessão com a sua conta do Azure.

### <a name="2-create-a-new-expressroute-circuit"></a>2. Criar um novo circuito do ExpressRoute

> [!IMPORTANT]
> O circuito de ExpressRoute é cobrado a partir do momento numa que chave de serviço é emitida. Certifique-se de que efetue esta operação quando o fornecedor de conectividade estiver pronto para aprovisionar o circuito.

1. Pode criar um circuito do ExpressRoute, selecionando a opção para criar um novo recurso. Clique em **criar um recurso** > **rede** > **ExpressRoute**, conforme mostrado na imagem seguinte:

   ![Criar um circuito do ExpressRoute](./media/expressroute-howto-circuit-portal-resource-manager/createcircuit1.png)
2. Depois de clicar em **ExpressRoute**, verá o **circuito do ExpressRoute criar** página. Quando estiver preenchendo os valores nesta página, certifique-se de que especifica a camada de SKU correta (Standard ou Premium) e dados de medição de modelo de faturação (sem limite ou ilimitados).

   ![Configurar o escalão de SKU e, em seguida, medição de dados](./media/expressroute-howto-circuit-portal-resource-manager/createcircuit.png)

   * **Escalão** determina se um padrão de ExpressRoute ou de um suplemento premium do ExpressRoute está ativado. Pode especificar **padrão** para obter o SKU standard ou **Premium** para o suplemento premium.
   * **Dados de medição** determina o tipo de faturação. Pode especificar **ilimitados** de um plano de dados limitados e **ilimitado** para um plano de dados ilimitados. Tenha em atenção que é possível alterar o tipo de faturação de **ilimitados** ao **ilimitado**.

     > [!IMPORTANT]
     > Não é possível alterar o tipo de **ilimitado** ao **ilimitados**.

   * **Localização de peering** é a localização física onde são peering com a Microsoft.

     > [!IMPORTANT]
     > A localização de Peering indica a [localização física](expressroute-locations.md) onde são peering com a Microsoft. Isto é **não** ligada à propriedade de "Localização", que se refere a geografia onde está localizado o fornecedor de recursos de rede do Azure. Embora não estejam relacionados, é uma boa prática para escolher um fornecedor de recursos de rede geograficamente próximo a localização de Peering do circuito.

### <a name="3-view-the-circuits-and-properties"></a>3. Ver as propriedades e os circuitos

**Ver todos os circuitos**

Pode ver todos os circuitos que criou, selecionando **todos os recursos** no menu da esquerda.

![Circuitos do Vista](./media/expressroute-howto-circuit-portal-resource-manager/listresource.png)

**Ver as propriedades**

Pode ver as propriedades do circuito ao selecioná-la. Sobre o **descrição geral** página para o seu circuito, a chave de serviço é apresentado no campo de chave de serviço. Tem de copiar a chave de serviço para o seu circuito e passá-la para baixo para o fornecedor de serviços para concluir o processo de aprovisionamento. A chave de serviço de circuito é específica para o seu circuito.

![Ver propriedades](./media/expressroute-howto-circuit-portal-resource-manager/servicekey1.png)

### <a name="4-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>4. Enviar a chave de serviço para o seu fornecedor de conectividade para o aprovisionamento

Nesta página, **estado do fornecedor** fornece informações sobre o estado atual do aprovisionamento no lado do fornecedor de serviços. **Estado de circuito** fornece o estado do lado da Microsoft. Para obter mais informações sobre Estados de aprovisionamento do circuito, consulte a [fluxos de trabalho](expressroute-workflows.md#expressroute-circuit-provisioning-states) artigo.

Quando cria um novo circuito do ExpressRoute, o circuito está no estado seguinte:

Estado do fornecedor: Não aprovisionado<BR>
Estado do circuito: Ativado

![Iniciar o processo de aprovisionamento](./media/expressroute-howto-circuit-portal-resource-manager/status.png)

O circuito muda para o seguinte estado quando o fornecedor de conectividade está no processo de ativá-la para:

Estado do fornecedor: Aprovisionamento<BR>
Estado do circuito: Ativado

Para poder ser capaz de utilizar um circuito do ExpressRoute, tem de estar no seguinte estado:

Estado do fornecedor: Aprovisionado<BR>
Estado do circuito: Ativado

### <a name="5-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>5. Verificar periodicamente o estado e o estado da chave de circuito

Pode ver as propriedades do circuito que tem interesse, selecioná-la. Verifique os **estado do fornecedor** e certifique-se de que foi movido para **aprovisionado** antes de continuar.

![Estado do circuito e fornecedor](./media/expressroute-howto-circuit-portal-resource-manager/provisioned.png)

### <a name="6-create-your-routing-configuration"></a>6. Criar a configuração do encaminhamento

Para obter instruções passo a passo, consulte a [configuração do encaminhamento do circuito de ExpressRoute](expressroute-howto-routing-portal-resource-manager.md) artigo para criar e modificar os peerings de circuito.

> [!IMPORTANT]
> Estas instruções aplicam-se apenas aos circuitos criados com fornecedores de serviços que oferecem serviços de conectividade de 2 de camada. Se estiver a utilizar um fornecedor de serviços que oferece geridos de camada 3 serviços (normalmente uma VPN de IP, como MPLS), o seu fornecedor de conectividade configura e gere o encaminhamento por si.

### <a name="7-link-a-virtual-network-to-an-expressroute-circuit"></a>7. Ligar uma rede virtual a um circuito ExpressRoute

Em seguida, ligar uma rede virtual para o seu circuito do ExpressRoute. Utilize o [ligar redes virtuais para circuitos do ExpressRoute](expressroute-howto-linkvnet-arm.md) artigo quando trabalha com o modelo de implementação do Resource Manager.

## <a name="status"></a>Ao obter o estado de um circuito do ExpressRoute

Pode ver o estado de um circuito ao selecioná-lo e ver a página de descrição geral.

## <a name="modify"></a>Modificar um circuito do ExpressRoute

Pode modificar algumas propriedades de um circuito do ExpressRoute sem afetar a conectividade. Pode modificar o modelo de faturação de largura de banda, SKU e permitir operações clássicas no **configuração** página. Para obter informações sobre limites e limitações, consulte a [FAQ do ExpressRoute](expressroute-faqs.md). 

Pode efetuar as seguintes tarefas sem tempo de inatividade:

* Ativar ou desativar um suplemento ExpressRoute Premium para o seu circuito do ExpressRoute.
* Aumente a largura de banda do circuito do ExpressRoute, desde que haja capacidade na porta.

  > [!IMPORTANT]
  > Desatualização de largura de banda de um circuito não é suportada.

* Alterar o plano de medição da *dados limitados* ao *dados ilimitados*.

  > [!IMPORTANT]
  > Não é suportada a alteração do plano de medição de dados ilimitados para dados limitados.

* Pode ativar e desativar *permitir operações clássicas*.
  > [!IMPORTANT]
  > Poderá ter de recriar o circuito do ExpressRoute, se houver a capacidade inadequada na porta existente. Não é possível atualizar o circuito se não existir nenhuma capacidade adicional disponível naquele local.
  > 
  > Embora pode atualizar facilmente a largura de banda, não é possível reduzir a largura de banda de um circuito do ExpressRoute sem interrupção. Desatualização de largura de banda exige que desaprovisionar o circuito do ExpressRoute e, em seguida, reaprovisionar um novo circuito do ExpressRoute.
  > 
  > Desativar a operação de suplemento Premium pode falhar se estiver a utilizar os recursos que são maiores que o que é permitido para o circuito standard.

Para modificar um circuito do ExpressRoute, clique em **configuração**.

![Modificar o circuito](./media/expressroute-howto-circuit-portal-resource-manager/modifycircuit.png)

## <a name="delete"></a>Desaprovisionar e eliminar um circuito do ExpressRoute

Pode eliminar o seu circuito do ExpressRoute, selecionando o **eliminar** ícone. Tenha em atenção as seguintes informações:

* Tem de desassociar todas as redes virtuais do circuito do ExpressRoute. Se esta operação falhar, verifique se quaisquer redes virtuais são ligadas ao circuito.
* Se for o fornecedor de serviços de circuito do ExpressRoute estado de aprovisionamento **aprovisionamento** ou **aprovisionado** deve trabalhar com o fornecedor de serviços para desaprovisionar o circuito no seu lado. Continuamos a reservar recursos e a faturação é que o fornecedor de serviços seja concluída o circuito de desaprovisionamento e notifica-nos.
* Se o fornecedor de serviços tiver desaprovisionado o circuito (fornecedor de serviços de estado de aprovisionamento está definido como **não aprovisionado**), pode eliminar o circuito. Isto interrompe a faturação do circuito.

## <a name="next-steps"></a>Passos Seguintes

Depois de criar o seu circuito, continue com os seguintes passos seguintes:

* [Criar e modificar o encaminhamento para o seu circuito do ExpressRoute](expressroute-howto-routing-portal-resource-manager.md)
* [Ligar a rede virtual para o seu circuito do ExpressRoute](expressroute-howto-linkvnet-arm.md)
