---
title: Use a WAN virtual do Azure para criar conexões de ExpressRoute com o Azure e ambientes locais | Microsoft Docs
description: Neste tutorial, saiba como usar a WAN virtual do Azure para criar conexões de ExpressRoute ao Azure e a ambientes locais.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 10/24/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect my corporate on-premises network(s) to my VNets using Virtual WAN and ExpressRoute.
ms.openlocfilehash: 8ad86280eab3041667bf9d1713ae2b4bc82a4c9e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491464"
---
# <a name="tutorial-create-an-expressroute-association-using-azure-virtual-wan"></a>Tutorial: criar uma associação de ExpressRoute usando a WAN virtual do Azure

Este tutorial mostra como usar a WAN virtual para se conectar aos seus recursos no Azure por um circuito do ExpressRoute. Para obter mais informações sobre WAN virtual e recursos de WAN virtual, consulte [visão geral da WAN virtual](virtual-wan-about.md).

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma WAN Virtual
> * Criar um Hub e um gateway
> * Ligar uma VNet a um hub
> * Conectar um circuito a um gateway de Hub
> * Testar conectividade
> * Alterar um tamanho de gateway
> * Anunciar uma rota padrão

## <a name="before-you-begin"></a>Antes de começar

Antes de iniciar a configuração, verifique se cumpre os seguintes critérios:

* Você tem uma rede virtual à qual deseja se conectar. Verifique se nenhuma das sub-redes de suas redes locais se sobrepõe às redes virtuais às quais você deseja se conectar. Para criar uma rede virtual no portal do Azure, consulte o guia de [início rápido](../virtual-network/quick-create-portal.md).

* Sua rede virtual não tem nenhum gateway de rede virtual. Se sua rede virtual tiver um gateway (VPN ou ExpressRoute), você deverá remover todos os gateways. Essa configuração requer que as redes virtuais estejam conectadas ao gateway do Hub WAN virtual.

* Obtenha um intervalo de endereços IP para a região do seu hub. O Hub é uma rede virtual que é criada e usada pela WAN virtual. O intervalo de endereços especificado para o Hub não pode se sobrepor a nenhuma das redes virtuais existentes às quais você se conecta. Também não se pode sobrepor aos intervalos de endereços a que se ligue no local. Se você não estiver familiarizado com os intervalos de endereços IP localizados em sua configuração de rede local, coordene com alguém que possa fornecer esses detalhes para você.

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="openvwan"></a>Criar uma WAN virtual

Num browser, navegue para o [Portal do Azure](https://portal.azure.com) e inicie sessão com a sua conta do Azure.

1. Navegue até a página WAN virtual. No portal, clique em **Criar um recurso**. Digite **Wan virtual** na caixa de pesquisa e selecione Enter.
2. Selecione **Wan virtual** nos resultados. Na página WAN virtual, clique em **criar** para abrir a página Criar Wan.
3. Na página **criar Wan** , na guia **noções básicas** , preencha os seguintes campos:

   ![Criar WAN](./media/virtual-wan-expressroute-portal/createwan.png)

   * **Subscription** (subscrição) - selecione a subscrição que quer utilizar.
   * **Resource Group** (Grupo de Recursos) - crie um novo ou utilize um já existente.
   * **Local do grupo de recursos** -escolha um local de recurso no menu suspenso. Uma WAN é um recurso global e não reside numa região específica. Contudo, tem de selecionar uma região para poder gerir e localizar mais facilmente o recurso WAN que criou.
   * **Nome** -digite o nome que você deseja chamar para sua Wan.
   * **Tipo** -selecione **padrão**. Você não pode criar um gateway de ExpressRoute usando o SKU básico.
4. Depois de concluir o preenchimento dos campos, selecione **revisar + criar**.
5. Depois que a validação for aprovada, selecione **criar** para criar a WAN virtual.

## <a name="hub"></a>Criar um gateway e um hub virtual

Um hub virtual é uma rede virtual que é criada e usada pela WAN virtual. Ele pode conter vários gateways, como VPN e ExpressRoute. Nesta seção, você criará um gateway de ExpressRoute para o seu hub virtual. Você pode criar o gateway ao [criar um novo hub virtual](#newhub)ou pode criar o gateway em um [Hub existente](#existinghub) editando-o. 

Os gateways de ExpressRoute são provisionados em unidades de 2 Gbps. 1 unidade de escala = 2 Gbps com suporte para até 10 unidades de escala = 20 Gbps. Leva cerca de 30 minutos para que um gateway e um hub virtual sejam totalmente criados.

### <a name="newhub"></a>Para criar um novo hub virtual e um gateway

Crie um novo hub virtual. Depois que um hub for criado, você será cobrado pelo Hub, mesmo se não anexar nenhum site.

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-er-hub-include.md)]

### <a name="existinghub"></a>Para criar um gateway em um Hub existente

Você também pode criar um gateway em um Hub existente editando-o.

1. Navegue até o Hub virtual que você deseja editar e selecione-o.
2. Na página **Editar Hub virtual** , marque a caixa de seleção **incluir gateway de ExpressRoute**.
3. Selecione **confirmar** para confirmar suas alterações. Leva cerca de 30 minutos para que os recursos de Hub e Hub sejam totalmente criados.

   ![Hub existente](./media/virtual-wan-expressroute-portal/edithub.png "editar um hub")

### <a name="to-view-a-gateway"></a>Para exibir um gateway

Depois de criar um gateway de ExpressRoute, você poderá exibir detalhes do gateway. Navegue até o Hub, selecione **ExpressRoute**e exiba o gateway.

![Exibir gateway](./media/virtual-wan-expressroute-portal/viewgw.png "Exibir gateway")

## <a name="connectvnet"></a>Conectar sua VNet ao Hub

Nesta seção, você cria a conexão de emparelhamento entre o Hub e uma VNet. Repita estes passos para cada VNet que queira ligar.

1. Na página da WAN virtual, clique em **Virtual network connection** (Ligação de rede virtual).
2. Na página de ligação da rede virtual, clique em **+Add connection** (+Adicionar ligação).
3. Na página **Add connection** (Adicionar ligação), preencha os seguintes campos:

    * **Connection name** (Nome da ligação) - dê um nome à ligação.
    * **Hubs** - selecione o hub que pretende associar a esta ligação.
    * **Subscription** (Subscrição) - verifique a subscrição.
    * **Virtual network** (Rede virtual) - selecione a rede virtual que pretende ligar a este hub. A rede virtual não pode ter um gateway de rede virtual já existente (nem VPN, nem ExpressRoute).

## <a name="connectcircuit"></a>Conectar seu circuito ao gateway do Hub

Depois que o gateway é criado, você pode conectar um [circuito do ExpressRoute](../expressroute/expressroute-howto-circuit-portal-resource-manager.md) a ele. Observe que os circuitos do ExpressRoute Premium que estão em locais de ExpressRoute Alcance Global com suporte podem se conectar a um gateway de ExpressRoute de WAN virtual.

### <a name="to-connect-the-circuit-to-the-hub-gateway"></a>Para conectar o circuito ao gateway do Hub

No portal, vá para a página **> conectividade do Hub virtual – > ExpressRoute** . Se você tiver acesso em sua assinatura a um circuito do ExpressRoute, você verá o circuito que deseja usar na lista de circuitos. Se você não vir nenhum circuito, mas tiver sido fornecido com uma chave de autorização e um URI de circuito de mesmo nível, poderá resgatar e conectar um circuito. Consulte [para se conectar ao resgatar uma chave de autorização](#authkey).

1. Selecione o circuito.
2. Selecione **conectar circuito (s)** .

   ![conectar circuitos](./media/virtual-wan-expressroute-portal/cktconnect.png "conectar circuitos")

### <a name="authkey"></a>Para se conectar, resgatando uma chave de autorização

Use a chave de autorização e o URI de circuito que você forneceu para se conectar.

1. Na página ExpressRoute, clique em **+ resgatar chave de autorização**

   ![trocar](./media/virtual-wan-expressroute-portal/redeem.png "trocar")
2. Na página resgatar a chave de autorização, preencha os valores.

   ![resgatar valores de chave](./media/virtual-wan-expressroute-portal/redeemkey2.png "resgatar valores de chave")
3. Selecione **Adicionar** para adicionar a chave.
4. Exibir o circuito. Um circuito resgatado mostra apenas o nome (sem o tipo, o provedor e outras informações) porque ele está em uma assinatura diferente daquela do usuário.

## <a name="to-test-connectivity"></a>Para testar a conectividade

Depois que a conexão de circuito for estabelecida, o status de conexão do Hub indicará ' este hub ', indicando que a conexão é estabelecida com o gateway de ExpressRoute do Hub. Aguarde aproximadamente 5 minutos antes de testar a conectividade de um cliente atrás do circuito do ExpressRoute, por exemplo, uma VM na VNet que você criou anteriormente.

Se você tiver sites conectados a um gateway de VPN de WAN virtual no mesmo Hub que o gateway de ExpressRoute, poderá ter conectividade bidirecional entre os pontos de extremidade VPN e ExpressRoute. Há suporte para o roteamento dinâmico (BGP). O ASN dos gateways no Hub é fixo e não pode ser editado no momento.

## <a name="to-change-the-size-of-a-gateway"></a>Para alterar o tamanho de um gateway

Se você quiser alterar o tamanho do gateway do ExpressRoute, localize o gateway do ExpressRoute dentro do Hub e selecione as unidades de escala na lista suspensa. Salve sua alteração. Levará aproximadamente 30 minutos para atualizar o gateway do Hub.

![alterar o tamanho do gateway](./media/virtual-wan-expressroute-portal/changescale.png "alterar o tamanho do gateway")

## <a name="to-advertise-default-route-00000-to-endpoints"></a>Para anunciar a rota padrão 0.0.0.0/0 para pontos de extremidade

Se você quiser que o Hub virtual do Azure Anuncie a rota padrão 0.0.0.0/0 aos pontos de extremidade do ExpressRoute, será necessário habilitar "propagar a rota padrão".

1. Selecione seu **circuito->...-> editar conexão**.

   ![Editar conexão](./media/virtual-wan-expressroute-portal/defaultroute1.png "Editar conexão")

2. Selecione **habilitar** para propagar a rota padrão.

   ![Propagar rota padrão](./media/virtual-wan-expressroute-portal/defaultroute2.png "Propagar rota padrão")

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a WAN Virtual, veja a página [Virtual WAN Overview](virtual-wan-about.md) (Descrição Geral da WAN Virtual).