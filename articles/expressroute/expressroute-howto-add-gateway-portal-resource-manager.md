---
title: 'Azure ExpressRoute: Adicione uma porta de entrada a um VNet: portal'
description: Este artigo acompanha-o através da adição de uma porta de entrada de rede virtual a um VNet de Gestor de Recursos já criado para o ExpressRoute utilizando o portal Azure.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 12/06/2018
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 06f7e5d28017ee618adfeeec52c6f1226e1ae82c
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89396365"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-the-azure-portal"></a>Configurar uma porta de rede virtual para o ExpressRoute utilizando o portal Azure
> [!div class="op_single_selector"]
> * [Gestor de Recursos - Portal Azure](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager – PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Clássico - PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Vídeo - Portal Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

Este artigo percorre os passos para adicionar uma porta de entrada de rede virtual para um VNet pré-existente. Este artigo percorre os passos para adicionar, redimensionar e remover uma porta de entrada de rede virtual (VNet) para um VNet pré-existente. Os passos para esta configuração são especificamente para VNets que foram criados usando o modelo de implementação de Gestor de Recursos que será usado numa configuração ExpressRoute. Para obter mais informações sobre gateways de rede virtuais e configurações de configuração de gateways para ExpressRoute, consulte [Sobre gateways de rede virtuais para ExpressRoute](expressroute-about-virtual-network-gateways.md). 


## <a name="before-beginning"></a>Antes de começar

Os passos para esta tarefa utilizam um VNet com base nos valores da seguinte lista de referência de configuração. Utilizamos esta lista nos nossos passos de exemplo. Pode copiar a lista para usar como referência, substituindo os valores pelos seus.

**Lista de referência de configuração**

* Nome da rede virtual = "TestvNet"
* Espaço de endereço de rede virtual = 192.168.0.0/16
* Nome da sub-rede = "FrontEnd" 
    * Espaço de endereço da sub-rede = "192.168.1.0/24"
* Grupo de Recursos = "TestRG"
* Localização = "Leste DOS EUA"
* Nome da sub-rede Gateway: "GatewaySubnet" Deve sempre nomear uma sub-rede *gateway GatewaySubnet*.
    * Espaço de endereço da Sub-rede Gateway = "192.168.200.0/26"
* Nome gateway = "ERGW"
* Gateway Public IP Name = "MyERGWVIP"
* Tipo gateway = "ExpressRoute" Este tipo é necessário para uma configuração ExpressRoute.

Pode ver um [vídeo](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network) destes passos antes de iniciar a sua configuração.

## <a name="create-the-gateway-subnet"></a>Criar a sub-rede de gateway

1. No [portal,](https://portal.azure.com)navegue para a rede virtual Do Gestor de Recursos para a qual pretende criar um gateway de rede virtual.
2. Na secção **Definições** do painel da VNet, clique em **Sub-redes** para expandir o painel Sub-redes.
3. No painel **Sub-redes**, clique em **+Sub-rede do gateway** para abrir o painel **Adicionar sub-rede**. 
   
    ![Adicionar a sub-rede do gateway](./media/expressroute-howto-add-gateway-portal-resource-manager/addgwsubnet.png "Adicionar a sub-rede do gateway")


4. O **Nome** da sub-rede é preenchido automaticamente com o valor "GatewaySubnet". Este valor é obrigatório para que o Azure reconheça a sub-rede como a sub-rede do gateway. Ajuste os valores de **Intervalo de endereços** de preenchimento automático de modo a corresponder aos seus requisitos de configuração. Recomendamos a criação de uma sub-rede de gateway com um /27 ou maior (/26, /25, etc.). Em seguida, clique **em OK** para guardar os valores e criar a sub-rede gateway.

    ![Adicionar a sub-rede](./media/expressroute-howto-add-gateway-portal-resource-manager/addsubnetgw.png "Adicionar a sub-rede")

## <a name="create-the-virtual-network-gateway"></a>Criar o gateway de rede virtual

1. No portal, no lado esquerdo, clique **+** e escreva 'Virtual Network Gateway' em busca. Localize **Gateway de rede virtual** no resultado da pesquisa e clique na entrada. No painel do**Gateway de rede virtual**, clique em **Criar** na parte superior do painel. É aberto o painel **Criar gateway de rede virtual**.
2. No painel **Criar gateway de rede virtual**, preencha os valores para o gateway de rede virtual.

    ![Campos do painel Criar gateway de rede virtual](./media/expressroute-howto-add-gateway-portal-resource-manager/gw.png "Campos do painel Criar gateway de rede virtual")
3. **Nome**: dê um nome ao gateway. Não é o mesmo que atribuir nome a uma sub-rede de gateway. É o nome do objeto de gateway que está a criar.
4. **Tipo gateway**: Selecione **ExpressRoute**.
5. **SKU**: selecione o SKU de gateway na lista pendente.
6. **Localização**: ajuste o campo **Localização** para apontar para a localização em que a sua rede virtual se encontra. Se a localização não está a apontar para a região onde reside a rede virtual, a rede virtual não é apresentada na lista pendente "Escolher uma rede virtual".
7. Escolha a rede virtual à qual pretende adicionar este gateway. Clique em **Rede virtual** para abrir o painel **Escolher uma rede virtual**. Selecione a VNet. Se não vir o seu VNet, certifique-se de que o campo **Localização** está a apontar para a região em que a sua rede virtual está localizada.
9. Escolha um endereço IP público. Clique em **Endereço IP público** para abrir o painel **Escolher endereço IP público**. Clique em **+Criar Novo** para abrir o painel **Escolher endereço IP público**. Introduza um nome para o seu endereço IP público. Este painel cria um objeto de endereço IP público ao qual um endereço IP público vai ser atribuído dinamicamente. Clique em **OK** para guardar as alterações a este painel.
10. **Subscrição**: verifique se está selecionada a subscrição correta.
11. **Grupo de recursos**: esta definição é determinada pela Rede Virtual que selecionar.
12. Não ajuste a **Localização** depois de especificar as definições anteriores.
13. Verifique as definições. Se pretender que o gateway apareça no dashboard, pode selecionar **Afixar ao dashboard** na parte inferior do painel.
14. Clique em **Criar** para começar a criar o gateway. As definições são validadas e o gateway será implementado. A criação de uma porta de rede virtual pode demorar até 45 minutos para ser concluída.

## <a name="next-steps"></a>Próximos passos
Depois de ter criado o gateway VNet, pode ligar o seu VNet a um circuito ExpressRoute. Consulte [a Ligação de uma Rede Virtual a um circuito ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md).
