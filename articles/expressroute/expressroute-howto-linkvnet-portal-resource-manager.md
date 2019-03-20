---
title: 'Ligar uma rede virtual a um circuito - ExpressRoute: Portal do Azure | Documentos da Microsoft'
description: Ligar uma rede virtual a um circuito do ExpressRoute do Azure. Instruções passo a passo.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 66c43556e698063b95ce1c3787458e40c9d71504
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58079473"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-the-portal"></a>Ligar uma rede virtual a um circuito do ExpressRoute com o portal
> [!div class="op_single_selector"]
> * [Portal do Azure](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [CLI do Azure](howto-linkvnet-cli.md)
> * [Vídeo - portal do Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (clássico)](expressroute-howto-linkvnet-classic.md)
> 

Este artigo ajuda-o a criar uma ligação para ligar uma rede virtual a um circuito do ExpressRoute do Azure no portal do Azure. As redes virtuais que ligar ao seu circuito do ExpressRoute do Azure podem ser na mesma subscrição ou podem ser parte de outra subscrição.

## <a name="before-you-begin"></a>Antes de começar

* Reveja os [pré-requisitos](expressroute-prerequisites.md), [requisitos de encaminhamento](expressroute-routing.md), e [fluxos de trabalho](expressroute-workflows.md) antes de iniciar a configuração.

* Deve ter um circuito ExpressRoute ativo.
  * Siga as instruções para [criar um circuito do ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) e ter o circuito ativado pelo seu fornecedor de conectividade.
  * Certifique-se de que tem o peering privado do Azure configurado para o seu circuito. Consulte a [configurar encaminhamento](expressroute-howto-routing-portal-resource-manager.md) artigo para obter instruções de encaminhamento.
  * Certifique-se de que o peering privado do Azure está configurado e o peering de BGP entre a rede e a Microsoft está ativo, para que pode habilitar a conectividade de ponto-a-ponto.
  * Certifique-se de que tem uma rede virtual e um gateway de rede virtual criada e totalmente aprovisionado. Siga as instruções para [criar um gateway de rede virtual para o ExpressRoute](expressroute-howto-add-gateway-resource-manager.md). Um gateway de rede virtual para o ExpressRoute utiliza o GatewayType "ExpressRoute", não VPN.

* Pode ligar até 10 redes virtuais a um circuito do ExpressRoute standard. Todas as redes virtuais tem de ser na mesma região geopolítica quando utilizar um circuito de ExpressRoute standard.

* Pode ser associada uma VNet única para até quatro circuitos do ExpressRoute. Utilize o processo abaixo para criar um novo objeto de ligação para cada circuito de ExpressRoute que está a ligar. Os circuitos do ExpressRoute podem ser na mesma subscrição, subscrições diferentes ou uma combinação de ambos.

* Pode ligar uma rede virtual fora da região geopolítica do circuito ExpressRoute ou ligar um grande número de redes virtuais para o seu circuito do ExpressRoute, se tiver ativado o suplemento ExpressRoute premium. Verifique os [FAQ](expressroute-faqs.md) para obter mais detalhes sobre o suplemento premium.

* Pode [ver um vídeo](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit) antes de começar a compreender melhor os passos.

## <a name="connect-a-vnet-to-a-circuit---same-subscription"></a>Ligar uma rede virtual a um circuito - mesma subscrição

> [!NOTE]
> Informações de configuração de BGP não aparecerá se o fornecedor de camada 3 configurado seu peerings. Se o seu circuito está no Estado aprovisionado, deve ser capaz de criar ligações.
>

### <a name="to-create-a-connection"></a>Para criar uma ligação

1. Certifique-se de que o circuito do ExpressRoute e o peering privado do Azure foram configurados com êxito. Siga as instruções em [criar um circuito do ExpressRoute](expressroute-howto-circuit-arm.md) e [configurar encaminhamento](expressroute-howto-routing-arm.md). O circuito de ExpressRoute deve ter um aspeto semelhante à imagem seguinte:

   ![Captura de ecrã de circuito de ExpressRoute](./media/expressroute-howto-linkvnet-portal-resource-manager/routing1.png)
2. Agora pode iniciar o aprovisionamento de uma ligação para ligar o gateway de rede virtual para o seu circuito do ExpressRoute. Clique em **conexão** > **Add** para abrir o **adicionar ligação** página e, em seguida, configure os valores.

   ![Adicionar captura de ecrã da ligação](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub1.png)
3. Depois da ligação foi configurada com êxito, o objeto de ligação irá mostrar as informações para a ligação.

   ![Captura de ecrã de objeto de ligação](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub2.png)

## <a name="connect-a-vnet-to-a-circuit---different-subscription"></a>Ligar uma rede virtual a um circuito - subscrição diferente

Pode partilhar um circuito do ExpressRoute em várias subscrições. A figura abaixo mostra um simples esquemática funciona como a partilha dos circuitos do ExpressRoute em várias subscrições.

![Conectividade entre subscrições](./media/expressroute-howto-linkvnet-portal-resource-manager/cross-subscription.png)

- Cada uma das nuvens menores na cloud da grande é utilizada para representar as subscrições que pertencem a diferentes departamentos dentro de uma organização.
- Cada um dos departamentos dentro da organização pode utilizar a sua própria subscrição para implementar os seus serviços, mas podem partilhar um único circuito de ExpressRoute para ligar a voltar à sua rede no local.
- Um único departamento (neste exemplo: IT) pode ser proprietário do circuito do ExpressRoute. Outras subscrições dentro da organização podem utilizar o circuito do ExpressRoute e autorizações associadas ao circuito, incluindo assinaturas vinculadas a outras inscrições de contrato Enterprise e inquilinos do Azure Active Directory.

  > [!NOTE]
  > Custos de largura de banda e de conectividade para o circuito dedicado serão aplicados para o proprietário do circuito ExpressRoute. Todas as redes virtuais partilham a mesma largura de banda.
  >
  >

### <a name="administration---about-circuit-owners-and-circuit-users"></a>Administração - sobre os proprietários de circuito e os utilizadores do circuito

O "proprietário do circuito' é um utilizador autorizado de energia do recurso de circuito do ExpressRoute. O proprietário do circuito pode criar autorizações que podem ser resgatadas por "utilizadores do circuito". Os utilizadores do circuito são proprietários de gateways de rede virtual que não estão na mesma subscrição que o circuito do ExpressRoute. Os utilizadores do circuito podem resgatar autorizações (uma autorização por rede virtual).

O proprietário do circuito tem o poder para modificar e revogar autorizações em qualquer altura. Revogar um resultados de autorização em todas as conexões de ligação a ser eliminados da subscrição cujo acesso foi revogado.

### <a name="circuit-owner-operations"></a>Operações de proprietário do circuito

**Para criar uma autorização de conexão**

O proprietário do circuito cria uma autorização. Isso resulta na criação de uma chave de autorização que pode ser utilizada por um utilizador do circuito para ligar os seus gateways de rede virtual para o circuito do ExpressRoute. Uma autorização é válida para apenas uma ligação.

> [!NOTE]
> Cada ligação requer uma autorização separada.
>

1. Na página do ExpressRoute, clique em **autorizações** e, em seguida, escreva um **nome** da autorização e clique em **guardar**.

   ![Autorizações](./media/expressroute-howto-linkvnet-portal-resource-manager/authorization.png)
2. Depois da configuração é guardada, copie os **ID de recurso** e o **chave de autorização**.

   ![Chave de autorização](./media/expressroute-howto-linkvnet-portal-resource-manager/authkey.png)

**Para eliminar uma autorização de conexão**

Pode eliminar uma ligação ao selecionar o **eliminar** ícone na página para a sua ligação.

### <a name="circuit-user-operations"></a>Operações de utilizador do circuito

O utilizador de circuito tem o ID de recurso e uma chave de autorização do proprietário do circuito.

**Para resgatar uma autorização de conexão**

1. Clique nas **+ novo** botão.

   ![Clique em novo](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection1.png)
2. Procure **"Ligação"** no Marketplace, selecione-a e clique em **criar**.

   ![Pesquisa para a ligação](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection2.png)
3. Certifique-se de que o **tipo de ligação** está definido como "ExpressRoute".
4. Preencha os detalhes, em seguida, clique em **OK** na página de noções básicas.

   ![Página de noções básicas](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection3.png)
5. Na **definições** página, selecione a **gateway de rede Virtual** e verificar o **resgatar autorização** caixa de verificação.
6. Introduza o **chave de autorização** e o **emparelhamento de URI do circuito** e dê um nome de ligação. Clique em **OK**.

   ![Página de definições](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection4.png)
7. Reveja as informações a **resumo** página e clique em **OK**.

**Para liberar uma autorização de conexão**

Pode libertar uma autorização ao eliminar a ligação que ligue o circuito do ExpressRoute para a rede virtual.

## <a name="delete-a-connection-to-unlink-a-vnet"></a>Eliminar uma ligação ao desassociar uma VNet

Pode eliminar uma ligação e desassociar a VNet a um circuito do ExpressRoute, selecionando o **eliminar** ícone na página para a sua ligação.

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações acerca do ExpressRoute, veja as [FAQs do ExpressRoute](expressroute-faqs.md).
