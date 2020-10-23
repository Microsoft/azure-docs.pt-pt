---
title: 'Tutorial: Criar ligações ExpressRoute utilizando Azure Virtual WAN'
description: Neste tutorial, aprenda a usar a Azure Virtual WAN para criar ligações ExpressRoute a ambientes Azure e no local.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 10/07/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect my corporate on-premises network(s) to my VNets using Virtual WAN and ExpressRoute.
ms.openlocfilehash: 07053c096ce001b322e5f05556bd041519ca9d2e
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92102481"
---
# <a name="tutorial-create-an-expressroute-association-using-azure-virtual-wan"></a>Tutorial: Criar uma associação ExpressRoute usando Azure Virtual WAN

Este tutorial mostra-lhe como usar o WAN Virtual para se conectar aos seus recursos em Azure sobre um circuito ExpressRoute. Para obter mais informações sobre os recursos VIRTUAIS WAN e Virtual WAN, consulte a [Visão Geral do WAN Virtual.](virtual-wan-about.md)

Neste tutorial, vai aprender a:

> [!div class="checklist"]
> * Criar uma WAN Virtual
> * Criar um hub e um portal
> * Ligar uma VNet a um hub
> * Ligue um circuito a um portal de entrada
> * Testar conectividade
> * Alterar o tamanho do gateway
> * Anuncie uma rota predefinida

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar a configuração, verifique se cumpre os seguintes critérios:

* Tem uma rede virtual a que pretende ligar. Verifique se nenhuma das sub-redes das suas redes no local se sobrepõe às redes virtuais a que pretende ligar. Para criar uma rede virtual no portal Azure, consulte o [Quickstart](../virtual-network/quick-create-portal.md).

* A sua rede virtual não tem quaisquer portas de rede virtuais. Se a sua rede virtual tiver um gateway (VPN ou ExpressRoute), deve remover todos os gateways. Esta configuração requer que as redes virtuais estejam ligadas ao gateway do hub Virtual WAN.

* Obtenha um intervalo de endereços IP para a região do seu hub. O hub é uma rede virtual que é criada e usada pela Virtual WAN. O intervalo de endereços que especifica para o hub não pode sobrepor-se a nenhuma das suas redes virtuais existentes a que se conecta. Também não pode sobrepor-se aos intervalos de endereços que liga ao local. Se não estiver familiarizado com os intervalos de endereços IP localizados na configuração da rede no local, coordene com alguém que possa fornecer esses detalhes para si.

* O circuito ExpressRoute deve ser um circuito Premium ou Standard para ligar ao gateway do hub.

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-virtual-wan"></a><a name="openvwan"></a>Criar uma WAN Virtual

Num browser, navegue para o [Portal do Azure](https://portal.azure.com) e inicie sessão com a sua conta do Azure.

1. Navegue para a página Virtual WAN. No portal, clique em **Criar um recurso**. Digite **WAN virtual** na caixa de pesquisa e selecione Enter.
2. Selecione **VIRTUAL WAN** a partir dos resultados. Na página VIRTUAL WAN, clique em **Criar** para abrir a página Create WAN.
3. Na página **Create WAN,** no separador **Básicos,** preencha os seguintes campos:

   ![Criar WAN](./media/virtual-wan-expressroute-portal/createwan.png)

   * **Subscription** (subscrição) - selecione a subscrição que quer utilizar.
   * **Resource Group** (Grupo de Recursos) - crie um novo ou utilize um já existente.
   * **Localização do grupo de recursos** - Escolha uma localização de recurso a partir do dropdown. Uma WAN é um recurso global e não reside numa região específica. Contudo, tem de selecionar uma região para poder gerir e localizar mais facilmente o recurso WAN que criou.
   * **Nome** - Digite o nome que pretende chamar de WAN.
   * **Tipo** - Selecione **Standard**. Não é possível criar um gateway ExpressRoute utilizando o SKU Básico.
4. Depois de terminar de preencher os campos, selecione **Review +Create**.
5. Assim que a validação passar, **selecione Criar** para criar o WAN virtual.

## <a name="create-a-virtual-hub-and-gateway"></a><a name="hub"></a>Criar um hub virtual e gateway

Um hub virtual é uma rede virtual que é criada e usada pela Virtual WAN. Pode conter vários gateways, como VPN e ExpressRoute. Nesta secção, irá criar uma porta de entrada ExpressRoute para o seu hub virtual. Pode criar o gateway quando [criar um novo hub virtual,](#newhub)ou pode criar o gateway num hub [existente](#existinghub) editando-o. 

Os gateways ExpressRoute são aprovisionados em unidades de 2 Gbps. 1 unidade de escala = 2 Gbps com suporte até 10 unidades de escala = 20 Gbps. Leva cerca de 30 minutos para um hub virtual e porta de entrada para criar totalmente.

### <a name="to-create-a-new-virtual-hub-and-a-gateway"></a><a name="newhub"></a>Para criar um novo hub virtual e uma porta de entrada

Criar um novo centro virtual. Uma vez criado um hub, será cobrado pelo centro, mesmo que não anexe nenhum sites.

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-er-hub-include.md)]

### <a name="to-create-a-gateway-in-an-existing-hub"></a><a name="existinghub"></a>Para criar uma porta de entrada num centro existente

Também pode criar uma porta de entrada num hub existente editando-o.

1. Navegue para o centro virtual que pretende editar e selecioná-lo.
2. Na página do **hub virtual editar,** selecione a caixa de verificação **Incluir gateway ExpressRoute**.
3. **Selecione Confirme** para confirmar as suas alterações. Leva cerca de 30 minutos para o hub e os recursos do hub criarem totalmente.

   ![hub existente](./media/virtual-wan-expressroute-portal/edithub.png "editar um hub")

### <a name="to-view-a-gateway"></a>Para ver uma porta de entrada

Uma vez criado um gateway ExpressRoute, pode ver os detalhes do gateway. Navegue até ao centro, selecione **ExpressRoute**e veja o gateway.

![Ver porta de entrada](./media/virtual-wan-expressroute-portal/viewgw.png "ver porta de entrada")

## <a name="connect-your-vnet-to-the-hub"></a><a name="connectvnet"></a>Ligue o seu VNet ao hub

Nesta secção, cria-se a ligação de espreitar entre o seu hub e um VNet. Repita estes passos para cada VNet que queira ligar.

1. Na página da WAN virtual, clique em **Virtual network connection** (Ligação de rede virtual).
2. Na página de ligação da rede virtual, clique em **+Add connection** (+Adicionar ligação).
3. Na página **Add connection** (Adicionar ligação), preencha os seguintes campos:

    * **Connection name** (Nome da ligação) - dê um nome à ligação.
    * **Hubs** - selecione o hub que pretende associar a esta ligação.
    * **Subscription** (Subscrição) - verifique a subscrição.
    * **Virtual network** (Rede virtual) - selecione a rede virtual que pretende ligar a este hub. A rede virtual não pode ter um gateway de rede virtual já existente (nem VPN, nem ExpressRoute).

## <a name="connect-your-circuit-to-the-hub-gateway"></a><a name="connectcircuit"></a>Ligue o seu circuito ao gateway do hub

Uma vez criado o gateway, pode ligar-lhe um [circuito ExpressRoute.](../expressroute/expressroute-howto-circuit-portal-resource-manager.md) Os circuitos ExpressRoute Standard ou Premium que se encontrem em locais suportados pelo ExpressRoute Global Reach podem ligar-se a um gateway Virtual WAN ExpressRoute e usufruir de todas as capacidades de trânsito VIRTUAL WAN (VPN-to-VPN, VPN e expressRoute transit). Os circuitos ExpressRoute Standard e Premium que se encontram em locais não-Globais de Alcance podem ligar-se aos recursos Azure, mas não poderão utilizar as capacidades de trânsito WAN Virtual. O ExpressRoute Local é suportado com hubs Azure Virtual WAN desde que os VNETs falados ligados a um hub VIRTUAL WAN estejam na mesma região que o Virtual WAN Hub.

### <a name="to-connect-the-circuit-to-the-hub-gateway"></a>Para ligar o circuito ao gateway do hub

No portal, aceda à página **Virtual hub -> Connectivity -> ExpressRoute.** Se tiver acesso na sua subscrição a um circuito ExpressRoute, verá o circuito que pretende utilizar na lista de circuitos. Se não vir circuitos, mas tiver sido fornecido uma chave de autorização e circuito de pares URI, pode resgatar e ligar um circuito. Ver [Para ligar redentora de uma chave de autorização](#authkey).

1. Selecione o circuito.
2. Selecione **circuitos de ligação.**

   ![ligar circuitos](./media/virtual-wan-expressroute-portal/cktconnect.png "ligar circuitos")

### <a name="to-connect-by-redeeming-an-authorization-key"></a><a name="authkey"></a>Para ligar redentora de uma chave de autorização

Utilize a chave de autorização e o circuito URI que lhe foi fornecido para ligar.

1. Na página ExpressRoute, clique na **tecla de autorização +Redentor**

   ![O Screenshot mostra o ExpressRoute para um hub virtual com a chave de autorização Redenis selecionada.](./media/virtual-wan-expressroute-portal/redeem.png "resgatar")
2. Na página chave de autorização Redentor, preencha os valores.

   ![resgatar valores-chave](./media/virtual-wan-expressroute-portal/redeemkey2.png "resgatar valores-chave")
3. **Selecione Adicionar** para adicionar a chave.
4. Veja o circuito. Um circuito resgatado apenas mostra o nome (sem o tipo, fornecedor e outras informações) porque está numa subscrição diferente da do utilizador.

## <a name="to-test-connectivity"></a>Para testar a conectividade

Após a ligação do circuito ser estabelecida, o estado de ligação do hub indicará "este hub", o que implica que a ligação seja estabelecida no gateway ExpressRoute. Aguarde aproximadamente 5 minutos antes de testar a conectividade de um cliente por trás do seu circuito ExpressRoute, por exemplo, um VM no VNet que criou anteriormente.

Se tiver sites ligados a uma porta de entrada Virtual WAN VPN no mesmo hub que o gateway ExpressRoute, pode ter conectividade bidirecional entre pontos finais VPN e ExpressRoute. O encaminhamento dinâmico (BGP) é suportado. A ASN dos gateways no hub é fixa e não pode ser editada neste momento.

## <a name="to-change-the-size-of-a-gateway"></a>Para mudar o tamanho de um portal

Se pretender alterar o tamanho do seu gateway ExpressRoute, localize o gateway ExpressRoute dentro do hub e selecione as unidades de escala a partir do dropdown. Poupe o troco. Levará aproximadamente 30 minutos para atualizar o gateway do hub.

![alterar tamanho do gateway](./media/virtual-wan-expressroute-portal/changescale.png "alterar tamanho do gateway")

## <a name="to-advertise-default-route-00000-to-endpoints"></a>Para anunciar a rota padrão 0.0.0.0/0 para pontos finais

Se quiser que o hub virtual Azure anuncie a rota padrão 0.0.0.0/0 para os seus pontos finais ExpressRoute, terá de ativar a rota 'Propagate predefinido'.

1. Selecione a **sua ligação de edição de >->**.

   ![Editar ligação](./media/virtual-wan-expressroute-portal/defaultroute1.png "Editar ligação")

2. **Selecione Ativar** para propagar a rota predefinitiva.

   ![Propagar a rota padrão](./media/virtual-wan-expressroute-portal/defaultroute2.png "Propagar a rota padrão")

## <a name="clean-up-resources"></a><a name="cleanup"></a>Limpar recursos

Quando já não precisar desses recursos, pode utilizar [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) para remover o grupo de recursos e todos os recursos que o mesmo contém. Substitua "myResourceGroup" pelo nome do grupo de recursos e execute o seguinte comando do PowerShell:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Passos seguintes

Em seguida, para saber mais sobre O WAN Virtual, consulte:

> [!div class="nextstepaction"]
> * [FAQ da WAN Virtual](virtual-wan-faq.md)
