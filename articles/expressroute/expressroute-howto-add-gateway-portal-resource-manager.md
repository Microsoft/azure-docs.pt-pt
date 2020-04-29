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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79264833"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-the-azure-portal"></a>Configure um portal de rede virtual para expressRoute utilizando o portal Azure
> [!div class="op_single_selector"]
> * [Gestor de Recursos - Portal Azure](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager – PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Clássico - PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Vídeo - Portal Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

Este artigo percorre-o através dos degraus para adicionar uma porta de entrada de rede virtual para um VNet pré-existente. Este artigo percorre-o através dos passos para adicionar, redimensionar e remover uma porta de entrada de rede virtual (VNet) para um VNet pré-existente. Os passos para esta configuração são especificamente para VNets que foram criados usando o modelo de implementação do Gestor de Recursos que será usado numa configuração ExpressRoute. Para obter mais informações sobre gateways de rede virtual e configurações de configuração de gateway para ExpressRoute, consulte [sobre gateways de rede virtual para ExpressRoute](expressroute-about-virtual-network-gateways.md). 


## <a name="before-beginning"></a>Antes de começar

Os passos para esta tarefa utilizam um VNet com base nos valores da lista de referência de configuração seguinte. Usamos esta lista nos nossos passos de exemplo. Pode copiar a lista para usar como referência, substituindo os valores por si próprio.

**Lista de referência de configuração**

* Nome da rede virtual = "TestVNet"
* Espaço de endereço da Rede Virtual = 192.168.0.0/16
* Nome da sub-rede = "FrontEnd" 
    * Espaço de endereço de sub-rede = "192.168.1.0/24"
* Grupo de Recursos = "TestRG"
* Localização = "Leste dos EUA"
* Nome da sub-rede gateway: "GatewaySubnet" Deve sempre nomear uma sub-rede *gatewaySubnet*.
    * Espaço de endereço gateway subnet = "192.168.200.0/26"
* Nome gateway = "ERGW"
* Gateway Nome IP público = "MyERGWVIP"
* Tipo gateway = "ExpressRoute" Este tipo é necessário para uma configuração ExpressRoute.

Pode ver um [vídeo](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network) destes passos antes de iniciar a sua configuração.

## <a name="create-the-gateway-subnet"></a>Criar a sub-rede de gateway

1. No [portal,](https://portal.azure.com)navegue para a rede virtual do Gestor de Recursos para a qual pretende criar um portal de rede virtual.
2. Na secção **Definições** do painel da VNet, clique em **Sub-redes** para expandir o painel Sub-redes.
3. No painel **Sub-redes**, clique em **+Sub-rede do gateway** para abrir o painel **Adicionar sub-rede**. 
   
    ![Adicionar a sub-rede do gateway](./media/expressroute-howto-add-gateway-portal-resource-manager/addgwsubnet.png "Adicionar a sub-rede do gateway")


4. O **Nome** da sub-rede é preenchido automaticamente com o valor "GatewaySubnet". Este valor é obrigatório para que o Azure reconheça a sub-rede como a sub-rede do gateway. Ajuste os valores de **Intervalo de endereços** de preenchimento automático de modo a corresponder aos seus requisitos de configuração. Recomendamos a criação de uma sub-rede de porta de entrada com uma /27 ou maior (/26, /25, etc.). Em seguida, clique em **OK** para salvar os valores e criar a sub-rede gateway.

    ![Adicionar a sub-rede](./media/expressroute-howto-add-gateway-portal-resource-manager/addsubnetgw.png "Adicionar a sub-rede")

## <a name="create-the-virtual-network-gateway"></a>Criar o gateway de rede virtual

1. No portal, no lado esquerdo, clique **+** e digite 'Virtual Network Gateway' em busca. Localize **Gateway de rede virtual** no resultado da pesquisa e clique na entrada. No painel do**Gateway de rede virtual**, clique em **Criar** na parte superior do painel. É aberto o painel **Criar gateway de rede virtual**.
2. No painel **Criar gateway de rede virtual**, preencha os valores para o gateway de rede virtual.

    ![Campos do painel Criar gateway de rede virtual](./media/expressroute-howto-add-gateway-portal-resource-manager/gw.png "Campos do painel Criar gateway de rede virtual")
3. **Nome**: dê um nome ao gateway. Não é o mesmo que atribuir nome a uma sub-rede de gateway. É o nome do objeto de gateway que está a criar.
4. **Tipo de gateway**: Selecione **ExpressRoute**.
5. **SKU**: selecione o SKU de gateway na lista pendente.
6. **Localização**: ajuste o campo **Localização** para apontar para a localização em que a sua rede virtual se encontra. Se a localização não está a apontar para a região onde reside a rede virtual, a rede virtual não é apresentada na lista pendente "Escolher uma rede virtual".
7. Escolha a rede virtual à qual pretende adicionar este gateway. Clique em **Rede virtual** para abrir o painel **Escolher uma rede virtual**. Selecione a VNet. Se não vir o seu VNet, certifique-se de que o campo **de localização** está a apontar para a região em que a sua rede virtual está localizada.
9. Escolha um endereço IP público. Clique em **Endereço IP público** para abrir o painel **Escolher endereço IP público**. Clique em **+Criar Novo** para abrir o painel **Escolher endereço IP público**. Introduza um nome para o seu endereço IP público. Este painel cria um objeto de endereço IP público ao qual um endereço IP público vai ser atribuído dinamicamente. Clique em **OK** para guardar as alterações a este painel.
10. **Subscrição**: verifique se está selecionada a subscrição correta.
11. **Grupo de recursos**: esta definição é determinada pela Rede Virtual que selecionar.
12. Não ajuste a **Localização** depois de especificar as definições anteriores.
13. Verifique as definições. Se pretender que o gateway apareça no dashboard, pode selecionar **Afixar ao dashboard** na parte inferior do painel.
14. Clique em **Criar** para começar a criar o gateway. As definições são validadas e o gateway será implementado. A criação de gateway de rede virtual pode levar até 45 minutos para ser concluída.

## <a name="next-steps"></a>Passos seguintes
Depois de ter criado o portal VNet, pode ligar o seu VNet a um circuito ExpressRoute. Consulte [a Ligação de uma Rede Virtual a um circuito ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md).
