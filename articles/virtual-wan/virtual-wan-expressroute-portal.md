---
title: Tutorial - Create ExpressRoute connections using Azure Virtual WAN
description: In this tutorial, learn how to use Azure Virtual WAN to create ExpressRoute connections to Azure and on-premises environments.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 10/24/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect my corporate on-premises network(s) to my VNets using Virtual WAN and ExpressRoute.
ms.openlocfilehash: f9277fae00471bf67682015e017ae6dfa351ad65
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422874"
---
# <a name="tutorial-create-an-expressroute-association-using-azure-virtual-wan"></a>Tutorial: Create an ExpressRoute association using Azure Virtual WAN

This tutorial shows you how to use Virtual WAN to connect to your resources in Azure over an ExpressRoute circuit. For more information about Virtual WAN and Virtual WAN resources, see the [Virtual WAN Overview](virtual-wan-about.md).

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma WAN Virtual
> * Create a hub and a gateway
> * Ligar uma VNet a um hub
> * Connect a circuit to a hub gateway
> * Testar conectividade
> * Change a gateway size
> * Advertise a default route

## <a name="before-you-begin"></a>Antes de começar

Antes de iniciar a configuração, verifique se cumpre os seguintes critérios:

* You have a virtual network that you want to connect to. Verify that none of the subnets of your on-premises networks overlap with the virtual networks that you want to connect to. To create a virtual network in the Azure portal, see the [Quickstart](../virtual-network/quick-create-portal.md).

* Your virtual network does not have any virtual network gateways. If your virtual network has a gateway (either VPN or ExpressRoute), you must remove all gateways. This configuration requires that virtual networks are connected instead, to the Virtual WAN hub gateway.

* Obtenha um intervalo de endereços IP para a região do seu hub. The hub is a virtual network that is created and used by Virtual WAN. The address range that you specify for the hub cannot overlap with any of your existing virtual networks that you connect to. Também não se pode sobrepor aos intervalos de endereços a que se ligue no local. If you are unfamiliar with the IP address ranges located in your on-premises network configuration, coordinate with someone who can provide those details for you.

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="openvwan"></a>Create a virtual WAN

Num browser, navegue para o [Portal do Azure](https://portal.azure.com) e inicie sessão com a sua conta do Azure.

1. Navigate to the Virtual WAN page. No portal, clique em **Criar um recurso**. Type **Virtual WAN** into the search box and select Enter.
2. Select **Virtual WAN** from the results. On the Virtual WAN page, click **Create** to open the Create WAN page.
3. On the **Create WAN** page, on the **Basics** tab, fill in the following fields:

   ![Criar WAN](./media/virtual-wan-expressroute-portal/createwan.png)

   * **Subscription** (subscrição) - selecione a subscrição que quer utilizar.
   * **Resource Group** (Grupo de Recursos) - crie um novo ou utilize um já existente.
   * **Resource group location** - Choose a resource location from the dropdown. Uma WAN é um recurso global e não reside numa região específica. Contudo, tem de selecionar uma região para poder gerir e localizar mais facilmente o recurso WAN que criou.
   * **Name** - Type the name that you want to call your WAN.
   * **Type** - Select **Standard**. You can't create an ExpressRoute gateway using the Basic SKU.
4. After you finish filling out the fields, select **Review +Create**.
5. Once validation passes, select **Create** to create the virtual WAN.

## <a name="hub"></a>Create a virtual hub and gateway

A virtual hub is a virtual network that is created and used by Virtual WAN. It can contain various gateways, such as VPN and ExpressRoute. In this section, you will create an ExpressRoute gateway for your virtual hub. You can either create the gateway when you [create a new virtual hub](#newhub), or you can create the gateway in an [existing hub](#existinghub) by editing it. 

ExpressRoute gateways are provisioned in units of 2 Gbps. 1 scale unit = 2 Gbps with support up to 10 scale units = 20 Gbps. It takes about 30 minutes for a virtual hub and gateway to fully create.

### <a name="newhub"></a>To create a new virtual hub and a gateway

Create a new virtual hub. Once a hub is created, you'll be charged for the hub, even if you don't attach any sites.

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-er-hub-include.md)]

### <a name="existinghub"></a>To create a gateway in an existing hub

You can also create a gateway in an existing hub by editing it.

1. Navigate to the virtual hub that you want to edit and select it.
2. On the **Edit virtual hub** page, select the checkbox **Include ExpressRoute gateway**.
3. Select **Confirm** to confirm your changes. It takes about 30 minutes for the hub and hub resources to fully create.

   ![existing hub](./media/virtual-wan-expressroute-portal/edithub.png "edit a hub")

### <a name="to-view-a-gateway"></a>To view a gateway

Once you have created an ExpressRoute gateway, you can view gateway details. Navigate to the hub, select **ExpressRoute**, and view the gateway.

![View gateway](./media/virtual-wan-expressroute-portal/viewgw.png "view gateway")

## <a name="connectvnet"></a>Connect your VNet to the hub

In this section, you create the peering connection between your hub and a VNet. Repita estes passos para cada VNet que queira ligar.

1. Na página da WAN virtual, clique em **Virtual network connection** (Ligação de rede virtual).
2. Na página de ligação da rede virtual, clique em **+Add connection** (+Adicionar ligação).
3. Na página **Add connection** (Adicionar ligação), preencha os seguintes campos:

    * **Connection name** (Nome da ligação) - dê um nome à ligação.
    * **Hubs** - selecione o hub que pretende associar a esta ligação.
    * **Subscription** (Subscrição) - verifique a subscrição.
    * **Virtual network** (Rede virtual) - selecione a rede virtual que pretende ligar a este hub. The virtual network cannot have an already existing virtual network gateway (neither VPN, nor ExpressRoute).

## <a name="connectcircuit"></a>Connect your circuit to the hub gateway

Once the gateway is created, you can connect an [ExpressRoute circuit](../expressroute/expressroute-howto-circuit-portal-resource-manager.md) to it. Note that ExpressRoute Premium circuits that are in ExpressRoute Global Reach-supported locations can connect to a Virtual WAN ExpressRoute gateway.

### <a name="to-connect-the-circuit-to-the-hub-gateway"></a>To connect the circuit to the hub gateway

In the portal, go to the **Virtual hub -> Connectivity -> ExpressRoute** page. If you have access in your subscription to an ExpressRoute circuit, you will see the circuit you want to use in the list of circuits. If you don’t see any circuits, but have been provided with an authorization key and peer circuit URI, you can redeem and connect a circuit. See [To connect by redeeming an authorization key](#authkey).

1. Select the circuit.
2. Select **Connect circuit(s)** .

   ![connect circuits](./media/virtual-wan-expressroute-portal/cktconnect.png "connect circuits")

### <a name="authkey"></a>To connect by redeeming an authorization key

Use the authorization key and circuit URI you were provided in order to connect.

1. On the ExpressRoute page, click **+Redeem authorization key**

   ![redeem](./media/virtual-wan-expressroute-portal/redeem.png "redeem")
2. On the Redeem authorization key page, fill in the values.

   ![redeem key values](./media/virtual-wan-expressroute-portal/redeemkey2.png "redeem key values")
3. Select **Add** to add the key.
4. View the circuit. A redeemed circuit only shows the name (without the type, provider and other information) because it is in a different subscription than that of the user.

## <a name="to-test-connectivity"></a>To test connectivity

After the circuit connection is established, the hub connection status will indicate 'this hub', implying the connection is established to the hub ExpressRoute gateway. Wait approximately 5 minutes before you test connectivity from a client behind your ExpressRoute circuit, for example, a VM in the VNet that you created earlier.

If you have sites connected to a Virtual WAN VPN gateway in the same hub as the ExpressRoute gateway, you can have bidirectional connectivity between VPN and ExpressRoute end points. Dynamic routing (BGP) is supported. The ASN of the gateways in the hub is fixed and cannot be edited at this time.

## <a name="to-change-the-size-of-a-gateway"></a>To change the size of a gateway

If you want to change the size of your ExpressRoute gateway, locate the ExpressRoute gateway inside the hub, and select the scale units from the dropdown. Save your change. It will take approximately 30 minutes to update the hub gateway.

![change gateway size](./media/virtual-wan-expressroute-portal/changescale.png "change gateway size")

## <a name="to-advertise-default-route-00000-to-endpoints"></a>To advertise default route 0.0.0.0/0 to endpoints

If you would like the Azure virtual hub to advertise the default route 0.0.0.0/0 to your ExpressRoute end points, you will need to enable 'Propagate default route'.

1. Select your **Circuit ->…-> Edit connection**.

   ![Edit connection](./media/virtual-wan-expressroute-portal/defaultroute1.png "Edit connection")

2. Select **Enable** to propagate the default route.

   ![Propagate default route](./media/virtual-wan-expressroute-portal/defaultroute2.png "Propagate default route")

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a WAN Virtual, veja a página [Virtual WAN Overview](virtual-wan-about.md) (Descrição Geral da WAN Virtual).