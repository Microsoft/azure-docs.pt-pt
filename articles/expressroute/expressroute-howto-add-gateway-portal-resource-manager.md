---
title: 'Azure ExpressRoute: Adicione uma porta de entrada a um VNet: portal'
description: Este artigo acompanha-o através da adição de uma porta de entrada de rede virtual a um VNet vNet já criado para o ExpressRoute utilizando o portal Azure.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 12/06/2018
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 87b656f0ef999b3b15a89476f5cba4c4fcfc2b1e
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79264833"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-the-azure-portal"></a>Configurar um gateway de rede virtual para o ExpressRoute com o portal do Azure
> [!div class="op_single_selector"]
> * [Resource Manager - portal do Azure](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager – PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Clássico - PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Vídeo - Portal Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

Este artigo orienta-o pelos passos para adicionar um gateway de rede virtual para uma VNet já existente. Este artigo orienta-o pelos passos para adicionar, redimensionar e remover um gateway de rede virtual (VNet) para uma VNet já existente. Os passos para esta configuração são especificamente para VNets que foram criadas utilizando o modelo de implementação do Resource Manager que será utilizado uma configuração de ExpressRoute. Para obter mais informações sobre gateways de rede virtual e configurações de configuração de gateway para ExpressRoute, consulte [sobre gateways de rede virtual para ExpressRoute](expressroute-about-virtual-network-gateways.md). 


## <a name="before-beginning"></a>Antes de começar

Os passos para esta tarefa utilizam uma VNet com base nos valores na lista de referência de configuração seguinte. Podemos utilizar esta lista em nossa passos de exemplo. Pode copiar a lista para utilizar como uma referência, substituindo os valores pelos seus próprios.

**Lista de referência de configuração**

* Nome da rede virtual = "TestVNet"
* Espaço de endereços de rede virtual = 192.168.0.0/16
* Nome da sub-rede = "FrontEnd" 
    * Espaço de endereços da sub-rede = "192.168.1.0/24"
* Resource Group = "TestRG"
* Localização = "Este dos E.U.A."
* Nome da sub-rede gateway: "GatewaySubnet" Deve sempre nomear uma sub-rede *gatewaySubnet*.
    * Espaço de endereços de sub-rede de gateway = "192.168.200.0/26"
* Nome do gateway = "ERGW"
* Nome IP público do gateway = "MyERGWVIP"
* Tipo de gateway "ExpressRoute" de = deste tipo é necessário para uma configuração de ExpressRoute.

Pode ver um [vídeo](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network) destes passos antes de iniciar a sua configuração.

## <a name="create-the-gateway-subnet"></a>Criar a sub-rede de gateway

1. No [portal](https://portal.azure.com), navegue para a rede virtual do Gestor de Recursos para a qual pretende criar um gateway de rede virtual.
2. Na secção **Definições** do painel da VNet, clique em **Sub-redes** para expandir o painel Sub-redes.
3. No painel **Sub-redes**, clique em **+Sub-rede do gateway** para abrir o painel **Adicionar sub-rede**. 
   
    ![Adicione a sub-rede gateway](./media/expressroute-howto-add-gateway-portal-resource-manager/addgwsubnet.png "Adicionar a sub-rede do gateway")


4. O **Nome** da sub-rede é preenchido automaticamente com o valor "GatewaySubnet". Este valor é obrigatório para que o Azure reconheça a sub-rede como a sub-rede do gateway. Ajuste os valores de **Intervalo de endereços** de preenchimento automático de modo a corresponder aos seus requisitos de configuração. Recomendamos que crie uma sub-rede de gateway com/27 ou superior (/ 26, / 25, etc.). Em seguida, clique em **OK** para salvar os valores e criar a sub-rede gateway.

    ![Adicionar a sub-rede](./media/expressroute-howto-add-gateway-portal-resource-manager/addsubnetgw.png "Adicionar a sub-rede")

## <a name="create-the-virtual-network-gateway"></a>Criar o gateway de rede virtual

1. No portal, do lado esquerdo, clique em **+** e escreva "Gateway de Rede Virtual" na pesquisa. Localize **Gateway de rede virtual** no resultado da pesquisa e clique na entrada. No painel do**Gateway de rede virtual**, clique em **Criar** na parte superior do painel. É aberto o painel **Criar gateway de rede virtual**.
2. No painel **Criar gateway de rede virtual**, preencha os valores para o gateway de rede virtual.

    ![Criar campos de lâminas de gateway de rede virtual](./media/expressroute-howto-add-gateway-portal-resource-manager/gw.png "Campos do painel Criar gateway de rede virtual")
3. **Nome**: dê um nome ao gateway. Não é o mesmo que atribuir nome a uma sub-rede de gateway. É o nome do objeto de gateway que está a criar.
4. **Tipo de gateway**: Selecione **ExpressRoute**.
5. **SKU**: selecione o SKU de gateway na lista pendente.
6. **Localização**: ajuste o campo **Localização** para apontar para a localização em que a sua rede virtual se encontra. Se a localização não está a apontar para a região onde reside a rede virtual, a rede virtual não é apresentada na lista pendente "Escolher uma rede virtual".
7. Escolha a rede virtual à qual pretende adicionar este gateway. Clique em **Rede virtual** para abrir o painel **Escolher uma rede virtual**. Selecione a VNet. Se não vir o VNet, confirme que o campo **Localização** aponta para a região na qual a rede virtual está localizada.
9. Escolha um endereço IP público. Clique em **Endereço IP público** para abrir o painel **Escolher endereço IP público**. Clique em **+Criar Novo** para abrir o painel **Escolher endereço IP público**. Introduza um nome para o seu endereço IP público. Este painel cria um objeto de endereço IP público ao qual um endereço IP público vai ser atribuído dinamicamente. Clique em **OK** para guardar as alterações a este painel.
10. **Subscrição**: verifique se está selecionada a subscrição correta.
11. **Grupo de recursos**: esta definição é determinada pela Rede Virtual que selecionar.
12. Não ajuste a **Localização** depois de especificar as definições anteriores.
13. Verifique as definições. Se pretender que o gateway apareça no dashboard, pode selecionar **Afixar ao dashboard** na parte inferior do painel.
14. Clique em **Criar** para começar a criar o gateway. As definições são validadas e o gateway será implementado. Criar gateway de rede virtual pode demorar até 45 minutos a concluir.

## <a name="next-steps"></a>Passos seguintes
Depois de ter criado o gateway de VNet, pode ligar a VNet a um circuito do ExpressRoute. Consulte [a Ligação de uma Rede Virtual a um circuito ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md).
