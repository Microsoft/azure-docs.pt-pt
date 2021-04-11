---
title: 'Tutorial: Azure ExpressRoute - Adicione uma porta de entrada a um VNet (portal Azure)'
description: Este tutorial acompanha-o através da adição de uma porta de entrada de rede virtual a um VNet for ExpressRoute utilizando o portal Azure.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 03/03/2021
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: e70b41dfba03b7439313350746724c6f8e90bc34
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105564777"
---
# <a name="tutorial-configure-a-virtual-network-gateway-for-expressroute-using-the-azure-portal"></a>Tutorial: Configurar uma porta de rede virtual para ExpressRoute usando o portal Azure
> [!div class="op_single_selector"]
> * [Gestor de Recursos - Portal Azure](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager – PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Clássico - PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Vídeo - Portal Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 

Este tutorial leva-o através dos passos para adicionar uma porta de entrada de rede virtual para um VNet pré-existente. Este artigo percorre os passos para adicionar, redimensionar e remover uma porta de entrada de rede virtual (VNet) para um VNet pré-existente. Os passos para esta configuração são especificamente para VNets que foram criados usando o modelo de implementação de Gestor de Recursos que será usado numa configuração ExpressRoute. Para obter mais informações sobre gateways de rede virtuais e configurações de configuração de gateways para ExpressRoute, consulte [Sobre gateways de rede virtuais para ExpressRoute](expressroute-about-virtual-network-gateways.md). 

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> - Crie uma sub-rede de gateway.
> - Criar gateway de rede virtual.

## <a name="prerequisites"></a>Pré-requisitos

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

> [!IMPORTANT]
> O apoio iPv6 ao peering privado está atualmente em **Visualização Pública**. Se quiser ligar a sua rede virtual a um circuito ExpressRoute com o espresto privado com base no IPv6, certifique-se de que a sua rede virtual é dupla pilha e segue as diretrizes para o [IPv6 para Azure VNet](../virtual-network/ipv6-overview.md).
> 
> 

## <a name="create-the-gateway-subnet"></a>Criar a sub-rede de gateway

1. No [portal,](https://portal.azure.com)navegue para a rede virtual Do Gestor de Recursos para a qual pretende criar um gateway de rede virtual.
1. Na secção **Definições** do seu VNet, selecione **Subnets** para expandir as definições da Sub-rede.
1. Nas definições de **Subnetas,** selecione **+ sub-rede Gateway** para adicionar uma sub-rede gateway. 
   
    :::image type="content" source="./media/expressroute-howto-add-gateway-portal-resource-manager/add-gateway-subnet.png" alt-text="Adicionar a sub-rede do gateway":::

1. O **Nome** da sub-rede é preenchido automaticamente com o valor "GatewaySubnet". Este valor é obrigatório para que o Azure reconheça a sub-rede como a sub-rede do gateway. Ajuste os valores de **gama de endereços** preenchidos automaticamente de modo a corresponder aos seus requisitos de configuração. Recomendamos a criação de uma sub-rede de gateway com um /27 ou maior (/26, /25, e assim por diante.).

    Se estiver a utilizar uma rede virtual de pilha dupla e planeie utilizar o peering privado baseado no IPv6 sobre o ExpressRoute, clique em **Adicionar espaço de endereço IP6** e inserir valores de intervalo de **endereços IPv6.**

    Em seguida, selecione **OK** para guardar os valores e criar a sub-rede gateway.

    :::image type="content" source="./media/expressroute-howto-add-gateway-portal-resource-manager/add-subnet-gateway.png" alt-text="Adicionar a sub-rede":::

## <a name="create-the-virtual-network-gateway"></a>Criar o gateway de rede virtual

1. No portal, no lado esquerdo, selecione **Criar um recurso** e digite 'Virtual Network Gateway' em busca. Localize **o gateway de rede virtual** na devolução da pesquisa e selecione a entrada. Na página **de gateway de rede Virtual,** selecione **Criar**.
1. Na página **de gateway de rede virtual,** insira ou selecione estas definições:

    | Definição | Valor |
    | --------| ----- |
    | Subscrição | Verifique se a subscrição correta está selecionada. |
    | Grupo de Recursos | O grupo de recursos será automaticamente escolhido assim que selecionar a rede virtual. | 
    | Name | Diga o seu portal. Isto não é o mesmo que nomear uma sub-rede de gateway. É o nome do objeto de porta de entrada que estás a criar.|
    | Region | Altere o campo **Região** para indicar o local onde está localizada a sua rede virtual. Se a localização não estiver a apontar para a região onde está a sua rede virtual, a rede virtual não aparecerá no dropdown 'Escolha uma rede virtual'. |
    | Tipo de gateway | Selecione **ExpressRoute**|
    | SKU | Selecione o gateway SKU a partir do dropdown. |
    | Rede virtual | Selecione *TestVNet*. |
    | Endereço IP público | Selecione **Criar novo**.|
    | Nome do endereço IP público | Forneça um nome para o endereço IP público. |

    > [!IMPORTANT]
    > Se planeia utilizar o peering privado baseado no IPv6 sobre o ExpressRoute, certifique-se de selecionar um AZ SKU (ErGw1AZ, ErGw2AZ, ErGw3AZ) para **SKU**.
    > 
    > 

1. Selecione **'Rever + Criar'** e, em seguida, **criar** para começar a criar o gateway. As definições são validadas e o gateway será implementado. A criação de uma porta de rede virtual pode demorar até 45 minutos para ser concluída.

    :::image type="content" source="./media/expressroute-howto-add-gateway-portal-resource-manager/gateway.png" alt-text="Criar campos de página de gateway de rede virtual":::

## <a name="clean-up-resources"></a>Limpar os recursos

Se já não necessitar do gateway ExpressRoute, localize o gateway no grupo de recursos de rede virtual e selecione **Delete**. Certifique-se de que o portal não tem nenhuma ligação a um circuito.

:::image type="content" source="./media/expressroute-howto-add-gateway-portal-resource-manager/delete-gateway.png" alt-text="Apagar gateway de rede virtual":::

## <a name="next-steps"></a>Passos seguintes
Depois de criar o gateway VNet, pode ligar o seu VNet a um circuito ExpressRoute. 

> [!div class="nextstepaction"]
> [Ligue uma rede virtual a um circuito ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md)