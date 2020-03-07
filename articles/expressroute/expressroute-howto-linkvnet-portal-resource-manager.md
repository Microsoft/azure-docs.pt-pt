---
title: 'ExpressRoute: Ligue um VNet a um circuito: Portal Azure'
description: Ligue um VNet a um circuito Azure ExpressRoute. Instruções passo a passo.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 4c7a24ad692086398059d1afd48c8927e9d18582
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78384275"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-the-portal"></a>Ligar uma rede virtual a um circuito do ExpressRoute com o portal
> [!div class="op_single_selector"]
> * [Portal do Azure](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [CLI do Azure](howto-linkvnet-cli.md)
> * [Vídeo - Portal Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (clássico)](expressroute-howto-linkvnet-classic.md)
> 

Este artigo ajuda-o a criar uma ligação para ligar uma rede virtual a um circuito do ExpressRoute do Azure no portal do Azure. As redes virtuais que ligar ao seu circuito do ExpressRoute do Azure podem ser na mesma subscrição ou podem ser parte de outra subscrição.

## <a name="before-you-begin"></a>Antes de começar

* Reveja os [pré-requisitos,](expressroute-prerequisites.md) [os requisitos de encaminhamento](expressroute-routing.md)e [os fluxos de trabalho](expressroute-workflows.md) antes de iniciar a configuração.

* Deve ter um circuito ExpressRoute ativo.
  * Siga as instruções para [criar um circuito ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) e tenha o circuito ativado pelo seu fornecedor de conectividade.
  * Certifique-se de que tem o peering privado do Azure configurado para o seu circuito. Consulte o [Create e modifique](expressroute-howto-routing-portal-resource-manager.md) o olhar para um artigo de circuito ExpressRoute para obter instruções de observação e encaminhamento.
  * Certifique-se de que o peering privado do Azure está configurado e o peering de BGP entre a rede e a Microsoft está ativo, para que pode habilitar a conectividade de ponto-a-ponto.
  * Certifique-se de que tem uma rede virtual e um gateway de rede virtual criada e totalmente aprovisionado. Siga as instruções para criar uma porta de entrada de rede virtual para a [ExpressRoute](expressroute-howto-add-gateway-resource-manager.md). Um gateway de rede virtual para o ExpressRoute utiliza o GatewayType "ExpressRoute", não VPN.

* Pode ligar até 10 redes virtuais a um circuito do ExpressRoute standard. Todas as redes virtuais tem de ser na mesma região geopolítica quando utilizar um circuito de ExpressRoute standard.

* Pode ser associada uma VNet única para até quatro circuitos do ExpressRoute. Utilize o processo abaixo para criar um novo objeto de ligação para cada circuito de ExpressRoute que está a ligar. Os circuitos do ExpressRoute podem ser na mesma subscrição, subscrições diferentes ou uma combinação de ambos.

* Pode ligar uma rede virtual fora da região geopolítica do circuito ExpressRoute ou ligar um grande número de redes virtuais para o seu circuito do ExpressRoute, se tiver ativado o suplemento ExpressRoute premium. Verifique as [FAQ](expressroute-faqs.md) para obter mais detalhes sobre o complemento premium.

* Pode [ver um vídeo](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit) antes de começar a entender melhor os passos.

## <a name="connect-a-vnet-to-a-circuit---same-subscription"></a>Ligar uma rede virtual a um circuito - mesma subscrição

> [!NOTE]
> Informações de configuração de BGP não aparecerá se o fornecedor de camada 3 configurado seu peerings. Se o seu circuito está no Estado aprovisionado, deve ser capaz de criar ligações.
>

### <a name="to-create-a-connection"></a>Para criar uma ligação

1. Certifique-se de que o circuito do ExpressRoute e o peering privado do Azure foram configurados com êxito. Siga as instruções em [Criar um circuito ExpressRoute](expressroute-howto-circuit-arm.md) e [criar e modificar o esparnoção de um circuito ExpressRoute](expressroute-howto-routing-arm.md). O circuito de ExpressRoute deve ter um aspeto semelhante à imagem seguinte:

   [![Screenshot do circuito ExpressRoute](./media/expressroute-howto-linkvnet-portal-resource-manager/routing1.png "Ver Circuito")](./media/expressroute-howto-linkvnet-portal-resource-manager/routing1-exp.png#lightbox)
2. Agora pode iniciar o aprovisionamento de uma ligação para ligar o gateway de rede virtual para o seu circuito do ExpressRoute. Clique em **Ligação** > **Adicionar** para abrir a página de **ligação Adicionar** e, em seguida, configurar os valores.

   [![Adicionar screenshot de ligação](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub1.png "Adicionar Screenshot de Ligação")](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub1-exp.png#lightbox)
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

**Para criar uma autorização de ligação**

O proprietário do circuito cria uma autorização. Isso resulta na criação de uma chave de autorização que pode ser utilizada por um utilizador do circuito para ligar os seus gateways de rede virtual para o circuito do ExpressRoute. Uma autorização é válida para apenas uma ligação.

> [!NOTE]
> Cada ligação requer uma autorização separada.
>

1. Na página ExpressRoute, clique em **Autorizações** e, em seguida, digite um **nome** para a autorização e clique em **Guardar**.

   ![Autorizações](./media/expressroute-howto-linkvnet-portal-resource-manager/authorization.png)
2. Uma vez que a configuração seja guardada, copie o ID de **recurso** e a **chave de autorização**.

   ![Chave de autorização](./media/expressroute-howto-linkvnet-portal-resource-manager/authkey.png)

**Para eliminar uma autorização de ligação**

Pode eliminar uma ligação selecionando o ícone **Eliminar** na página para a sua ligação.

### <a name="circuit-user-operations"></a>Operações de utilizador do circuito

O utilizador de circuito tem o ID de recurso e uma chave de autorização do proprietário do circuito.

**Para resgatar uma autorização de ligação**

1. Clique no botão **+Novo.**

   ![Clique em novo](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection1.png)
2. Procure **"Connection"** no Mercado, selecione-o e clique em **Criar**.

   ![Pesquisa para a ligação](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection2.png)
3. Certifique-se de que o tipo de **Ligação** está definido para "ExpressRoute".
4. Preencha os detalhes e, em seguida, clique **OK** na página Basics.

   ![Página de noções básicas](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection3.png)
5. Na página **Definições,** selecione o gateway da **rede Virtual** e verifique a caixa de verificação de **autorização Da Redeem.**
6. Introduza a **chave de autorização** e o circuito peer **URI** e dê à ligação um nome. Clique em **OK**. O **Circuito Peer URI** é o ID de recurso do circuito ExpressRoute (que pode encontrar no painel de definição de propriedades do Circuito ExpressRoute).

   ![Página de definições](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection4.png)
7. Reveja as informações na página **Resumo** e clique em **OK**.

**Para libertar uma autorização de ligação**

Pode libertar uma autorização ao eliminar a ligação que ligue o circuito do ExpressRoute para a rede virtual.

## <a name="delete-a-connection-to-unlink-a-vnet"></a>Eliminar uma ligação ao desassociar uma VNet

Pode eliminar uma ligação e desligar o VNet a um circuito ExpressRoute selecionando o ícone **Delete** na página para a sua ligação.

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações acerca do ExpressRoute, veja as [FAQs do ExpressRoute](expressroute-faqs.md).
