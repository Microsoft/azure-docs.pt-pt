---
title: 'Adicione um gateway a uma VNet do Azure para o ExpressRoute: Portal | Microsoft Docs'
description: Este artigo orienta-o processo de adicionar um gateway de rede virtual a uma VNet do Resource Manager já criada para o ExpressRoute.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 12/06/2018
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 68376751a3c673b2d89d028312f992aec40d4dee
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57534621"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-the-azure-portal"></a>Configurar um gateway de rede virtual para o ExpressRoute com o portal do Azure
> [!div class="op_single_selector"]
> * [Resource Manager - portal do Azure](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager – PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Clássica – PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Vídeo - portal do Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

Este artigo orienta-o pelos passos para adicionar um gateway de rede virtual para uma VNet já existente. Este artigo orienta-o pelos passos para adicionar, redimensionar e remover um gateway de rede virtual (VNet) para uma VNet já existente. Os passos para esta configuração são especificamente para VNets que foram criadas utilizando o modelo de implementação do Resource Manager que será utilizado uma configuração de ExpressRoute. Para obter mais informações sobre gateways de rede virtual e as definições de configuração do gateway para o ExpressRoute, veja [sobre os gateways de rede virtual para o ExpressRoute](expressroute-about-virtual-network-gateways.md). 


## <a name="before-beginning"></a>Antes de começar

Os passos para esta tarefa utilizam uma VNet com base nos valores na lista de referência de configuração seguinte. Podemos utilizar esta lista em nossa passos de exemplo. Pode copiar a lista para utilizar como uma referência, substituindo os valores pelos seus próprios.

**Lista de referência de configuração**

* Nome da rede virtual = "TestVNet"
* Espaço de endereços de rede virtual = 192.168.0.0/16
* Nome da sub-rede = "FrontEnd" 
    * Espaço de endereços da sub-rede = "192.168.1.0/24"
* Resource Group = "TestRG"
* Localização = "Este dos E.U.A."
* Nome da sub-rede de gateway: "GatewaySubnet", deve nomeá sempre uma sub-rede de gateway *GatewaySubnet*.
    * Espaço de endereços de sub-rede de gateway = "192.168.200.0/26"
* Nome do gateway = "ERGW"
* Nome IP público do gateway = "MyERGWVIP"
* Tipo de gateway "ExpressRoute" de = deste tipo é necessário para uma configuração de ExpressRoute.

Pode ver um [vídeo](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network) dos seguintes passos antes de iniciar a configuração.

## <a name="create-the-gateway-subnet"></a>Criar a sub-rede de gateway

1. No [portal](https://portal.azure.com), navegue para a rede virtual do Gestor de Recursos para a qual pretende criar um gateway de rede virtual.
2. Na secção **Definições** do painel da VNet, clique em **Sub-redes** para expandir o painel Sub-redes.
3. No painel **Sub-redes**, clique em **+Sub-rede do gateway** para abrir o painel **Adicionar sub-rede**. 
   
    ![Add the gateway subnet](./media/expressroute-howto-add-gateway-portal-resource-manager/addgwsubnet.png "Add the gateway subnet")


4. O **Nome** da sub-rede é preenchido automaticamente com o valor "GatewaySubnet". Este valor é obrigatório para que o Azure reconheça a sub-rede como a sub-rede do gateway. Ajuste os valores de **Intervalo de endereços** de preenchimento automático de modo a corresponder aos seus requisitos de configuração. Recomendamos que crie uma sub-rede de gateway com/27 ou superior (/ 26, / 25, etc.). Em seguida, clique em **OK** para guardar os valores e criar a sub-rede do gateway.

    ![Adding the subnet](./media/expressroute-howto-add-gateway-portal-resource-manager/addsubnetgw.png "Adding the subnet")

## <a name="create-the-virtual-network-gateway"></a>Criar o gateway de rede virtual

1. No portal, do lado esquerdo, clique em **+** e escreva "Gateway de Rede Virtual" na pesquisa. Localize **Gateway de rede virtual** no resultado da pesquisa e clique na entrada. No painel do**Gateway de rede virtual**, clique em **Criar** na parte superior do painel. É aberto o painel **Criar gateway de rede virtual**.
2. No painel **Criar gateway de rede virtual**, preencha os valores para o gateway de rede virtual.

    ![Create virtual network gateway blade fields](./media/expressroute-howto-add-gateway-portal-resource-manager/gw.png "Create virtual network gateway blade fields")
3. **Nome**: Nome do seu gateway. Não é o mesmo que atribuir nome a uma sub-rede de gateway. É o nome do objeto de gateway que está a criar.
4. **Tipo de gateway**: Selecione **ExpressRoute**.
5. **SKU**: Selecione o SKU de gateway na lista pendente.
6. **Localização**: Ajuste o campo **Localização** para apontar para a localização em que a sua rede virtual se encontra. Se a localização não está a apontar para a região onde reside a rede virtual, a rede virtual não é apresentada na lista pendente "Escolher uma rede virtual".
7. Escolha a rede virtual à qual pretende adicionar este gateway. Clique em **Rede virtual** para abrir o painel **Escolher uma rede virtual**. Selecione a VNet. Se não vir o VNet, confirme que o campo **Localização** aponta para a região na qual a rede virtual está localizada.
9. Escolha um endereço IP público. Clique em **Endereço IP público** para abrir o painel **Escolher endereço IP público**. Clique em **+Criar Novo** para abrir o painel **Escolher endereço IP público**. Introduza um nome para o seu endereço IP público. Este painel cria um objeto de endereço IP público ao qual um endereço IP público vai ser atribuído dinamicamente. Clique em **OK** para guardar as alterações a este painel.
10. **Subscrição**: Certifique-se de que a subscrição correta está selecionada.
11. **Grupo de recursos**: Esta definição é determinada através da rede Virtual que selecionou.
12. Não ajuste a **Localização** depois de especificar as definições anteriores.
13. Verifique as definições. Se pretender que o gateway apareça no dashboard, pode selecionar **Afixar ao dashboard** na parte inferior do painel.
14. Clique em **Criar** para começar a criar o gateway. As definições são validadas e o gateway será implementado. Criar gateway de rede virtual pode demorar até 45 minutos a concluir.

## <a name="next-steps"></a>Passos Seguintes
Depois de ter criado o gateway de VNet, pode ligar a VNet a um circuito do ExpressRoute. Ver [ligar uma rede Virtual a um circuito de ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md).
