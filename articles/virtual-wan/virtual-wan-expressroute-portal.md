---
title: Tutorial - Criar ligações ExpressRoute usando o Azure Virtual WAN
description: Neste tutorial, aprenda a usar o Azure Virtual WAN para criar ligações ExpressRoute a ambientes Azure e no local.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 02/13/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect my corporate on-premises network(s) to my VNets using Virtual WAN and ExpressRoute.
ms.openlocfilehash: 35ca071cd8495611f0f350511ef9406f82c5be23
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "77209431"
---
# <a name="tutorial-create-an-expressroute-association-using-azure-virtual-wan"></a>Tutorial: Criar uma associação ExpressRoute usando o Azure Virtual WAN

Este tutorial mostra-lhe como usar o Virtual WAN para se conectar aos seus recursos em Azure sobre um circuito ExpressRoute. Para obter mais informações sobre os recursos virtuais wan e wan virtual, consulte a [visão geral virtual wan](virtual-wan-about.md).

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma WAN Virtual
> * Criar um hub e um portal
> * Ligar uma VNet a um hub
> * Ligue um circuito a um portal de hub
> * Testar conectividade
> * Alterar o tamanho do gateway
> * Anuncie uma rota padrão

## <a name="before-you-begin"></a>Antes de começar

Antes de iniciar a configuração, verifique se cumpre os seguintes critérios:

* Tem uma rede virtual a que se quer ligar. Verifique se nenhuma das subredes das suas redes no local se sobrepõe às redes virtuais a que pretende ligar. Para criar uma rede virtual no portal Azure, consulte o [Quickstart](../virtual-network/quick-create-portal.md).

* A sua rede virtual não dispõe de gateways de rede virtuais. Se a sua rede virtual tiver um portal (VPN ou ExpressRoute), deve remover todos os gateways. Esta configuração requer que as redes virtuais estejam ligadas ao portal virtual WAN hub.

* Obtenha um intervalo de endereços IP para a região do seu hub. O hub é uma rede virtual que é criada e usada pela Virtual WAN. O intervalo de endereços que especifica para o hub não pode sobrepor-se a nenhuma das suas redes virtuais existentes a que se liga. Também não se pode sobrepor aos intervalos de endereços a que se ligue no local. Se não estiver familiarizado com as gamas de endereços IP localizadas na configuração da sua rede no local, coordene com alguém que possa fornecer esses detalhes para si.

* O circuito ExpressRoute deve ser um circuito Premium para ligar ao portal do hub.

* Se não tiver uma subscrição Azure, crie uma [conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="create-a-virtual-wan"></a><a name="openvwan"></a>Criar uma WAN Virtual

Num browser, navegue para o [Portal do Azure](https://portal.azure.com) e inicie sessão com a sua conta do Azure.

1. Navegue para a página Virtual WAN. No portal, clique em **Criar um recurso**. Digite o **WAN virtual** na caixa de pesquisa e selecione Enter.
2. Selecione **Virtual WAN** a partir dos resultados. Na página WAN Virtual, clique em **Criar** para abrir a página Create WAN.
3. Na página **Create WAN,** no separador **Basics,** preencha os seguintes campos:

   ![Criar WAN](./media/virtual-wan-expressroute-portal/createwan.png)

   * **Subscription** (subscrição) - selecione a subscrição que quer utilizar.
   * **Resource Group** (Grupo de Recursos) - crie um novo ou utilize um já existente.
   * **Localização** do grupo de recursos - Escolha uma localização de recursos a partir da queda. Uma WAN é um recurso global e não reside numa região específica. Contudo, tem de selecionar uma região para poder gerir e localizar mais facilmente o recurso WAN que criou.
   * **Nome** - Digite o nome a que pretende chamar wan.
   * **Tipo** - Selecione **Standard**. Não é possível criar um gateway ExpressRoute utilizando o Basic SKU.
4. Depois de terminar de preencher os campos, selecione **Review +Create**.
5. Uma vez que a validação passe, selecione **Criar** para criar o WAN virtual.

## <a name="create-a-virtual-hub-and-gateway"></a><a name="hub"></a>Criar um hub virtual e gateway

Um hub virtual é uma rede virtual que é criada e usada pela Virtual WAN. Pode conter vários gateways, tais como VPN e ExpressRoute. Nesta secção, irá criar um portal ExpressRoute para o seu hub virtual. Pode criar o portal quando [criar um novo hub virtual,](#newhub)ou pode criar a porta de entrada num hub [existente](#existinghub) editando-o. 

Os gateways ExpressRoute são provisionados em unidades de 2 Gbps. 1 unidade de escala = 2 Gbps com suporte até 10 unidades de escala = 20 Gbps. Leva cerca de 30 minutos para um centro virtual e porta de entrada para criar totalmente.

### <a name="to-create-a-new-virtual-hub-and-a-gateway"></a><a name="newhub"></a>Para criar um novo centro virtual e uma porta de entrada

Criar um novo centro virtual. Uma vez criado um hub, será cobrado pelo hub, mesmo que não anexe nenhum sítio.

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-er-hub-include.md)]

### <a name="to-create-a-gateway-in-an-existing-hub"></a><a name="existinghub"></a>Para criar uma porta de entrada em um centro existente

Você também pode criar uma porta de entrada em um hub existente editando-o.

1. Navegue para o centro virtual que pretende editar e selecionar.
2. Na página do **hub virtual Editar,** selecione a caixa de verificação Incluir gateway **ExpressRoute**.
3. Selecione **Confirmar** para confirmar as suas alterações. Leva cerca de 30 minutos para que os recursos do hub e do hub criem totalmente.

   ![hub existente](./media/virtual-wan-expressroute-portal/edithub.png "editar um centro")

### <a name="to-view-a-gateway"></a>Para ver um portal

Uma vez criado um gateway ExpressRoute, pode ver detalhes do gateway. Navegue até ao centro, selecione **ExpressRoute,** e veja o portal.

![Ver gateway](./media/virtual-wan-expressroute-portal/viewgw.png "ver porta de entrada")

## <a name="connect-your-vnet-to-the-hub"></a><a name="connectvnet"></a>Ligue o seu VNet ao centro

Nesta secção, cria-se a ligação de pares entre o seu hub e um VNet. Repita estes passos para cada VNet que queira ligar.

1. Na página da WAN virtual, clique em **Virtual network connection** (Ligação de rede virtual).
2. Na página de ligação da rede virtual, clique em **+Add connection** (+Adicionar ligação).
3. Na página **Add connection** (Adicionar ligação), preencha os seguintes campos:

    * **Connection name** (Nome da ligação) - dê um nome à ligação.
    * **Hubs** - selecione o hub que pretende associar a esta ligação.
    * **Subscription** (Subscrição) - verifique a subscrição.
    * **Virtual network** (Rede virtual) - selecione a rede virtual que pretende ligar a este hub. A rede virtual não pode ter um portal de rede virtual já existente (nem VPN, nem ExpressRoute).

## <a name="connect-your-circuit-to-the-hub-gateway"></a><a name="connectcircuit"></a>Ligue o seu circuito ao portal do hub

Assim que o portal for criado, pode ligar-lhe um [circuito ExpressRoute.](../expressroute/expressroute-howto-circuit-portal-resource-manager.md) Os circuitos ExpressRoute Premium que se encontram em locais suportados pelo ExpressRoute Global Reach podem ligar-se a um gateway Virtual WAN ExpressRoute.

### <a name="to-connect-the-circuit-to-the-hub-gateway"></a>Para ligar o circuito ao portal do hub

No portal, vá à página **de Conectividade -> Conectividade -> ExpressRoute.** Se tiver acesso na sua subscrição a um circuito ExpressRoute, verá o circuito que pretende utilizar na lista de circuitos. Se não vir circuitos, mas tiver sido fornecido com uma chave de autorização e circuito de pares URI, pode resgatar e ligar um circuito. Consulte [Para ligar redimindo uma chave](#authkey)de autorização .

1. Selecione o circuito.
2. Selecione Circuitos de **Ligação .**

   ![ligar circuitos](./media/virtual-wan-expressroute-portal/cktconnect.png "ligar circuitos")

### <a name="to-connect-by-redeeming-an-authorization-key"></a><a name="authkey"></a>Para ligar redimindo uma chave de autorização

Utilize a chave de autorização e o circuito URI que lhe foi fornecido para se ligar.

1. Na página ExpressRoute, clique na **tecla de autorização +Redeem**

   ![resgatar](./media/virtual-wan-expressroute-portal/redeem.png "resgatar")
2. Na página-chave de autorização Da Redeem, preencha os valores.

   ![resgatar valores-chave](./media/virtual-wan-expressroute-portal/redeemkey2.png "resgatar valores-chave")
3. Selecione **Adicionar** para adicionar a chave.
4. Veja o circuito. Um circuito resgatado apenas mostra o nome (sem o tipo, fornecedor e outras informações) porque se encontra numa subscrição diferente da do utilizador.

## <a name="to-test-connectivity"></a>Para testar a conectividade

Após a ligação do circuito, o estado de ligação do hub indicará "este hub", o que implica que a ligação está estabelecida ao portal do hub ExpressRoute. Aguarde aproximadamente 5 minutos antes de testar a conectividade de um cliente por detrás do seu circuito ExpressRoute, por exemplo, um VM no VNet que criou anteriormente.

Se tiver sites ligados a uma porta de entrada Virtual WAN VPN no mesmo centro que o gateway ExpressRoute, pode ter conectividade bidirecional entre os pontos finais VPN e ExpressRoute. O encaminhamento dinâmico (BGP) é suportado. A ASN dos portões no centro é fixa e não pode ser editada neste momento.

## <a name="to-change-the-size-of-a-gateway"></a>Para mudar o tamanho de um portal

Se quiser alterar o tamanho do seu gateway ExpressRoute, localize o gateway ExpressRoute dentro do hub e selecione as unidades de escala a partir da queda. Poupe o seu troco. Levará aproximadamente 30 minutos para atualizar o portal do hub.

![mudar tamanho gateway](./media/virtual-wan-expressroute-portal/changescale.png "mudar tamanho gateway")

## <a name="to-advertise-default-route-00000-to-endpoints"></a>Para anunciar a rota padrão 0.0.0.0/0 para pontos finais

Se quiser que o hub virtual Azure anuncie a rota padrão 0.0.0.0/0 para os seus pontos finais ExpressRoute, terá de ativar a 'Rota padrão propagate'.

1. Selecione a **ligação Circuit ->...-> Editar**.

   ![Editar ligação](./media/virtual-wan-expressroute-portal/defaultroute1.png "Editar ligação")

2. **Selecione Ativar** para propagar a rota predefinida.

   ![Propagação rota padrão](./media/virtual-wan-expressroute-portal/defaultroute2.png "Propagação rota padrão")

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a WAN Virtual, veja a página [Virtual WAN Overview](virtual-wan-about.md) (Descrição Geral da WAN Virtual).
