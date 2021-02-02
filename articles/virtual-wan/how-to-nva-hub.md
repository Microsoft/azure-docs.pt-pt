---
title: 'Azure Virtual WAN: Criar um aparelho virtual de rede (NVA) no hub'
description: Saiba como implantar um aparelho virtual de rede no hub VIRTUAL WAN.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to create a Network Virtual Appliance (NVA) in my Virtual WAN hub.
ms.openlocfilehash: 1f80da4014d4b6b27155db17d32800e54b51f40d
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/02/2021
ms.locfileid: "99258070"
---
# <a name="how-to-create-a-network-virtual-appliance-in-an-azure-virtual-wan-hub-preview"></a>Como criar um aparelho virtual de rede num hub Azure Virtual WAN (Pré-visualização)

Este artigo mostra-lhe como utilizar o WAN Virtual para ligar aos seus recursos em Azure através de um **Aparelho Virtual de Rede** (NVA) em Azure. Este tipo de ligação requer um dispositivo VPN localizado no local que tenha um endereço IP público com acesso exterior atribuído ao mesmo. Para mais informações sobre o VIRTUAL WAN, consulte o [O que é O WAN Virtual?](virtual-wan-about.md)

Os passos deste artigo ajudam-no a criar um aparelho virtual **Barracuda CloudGen WAN** Network no centro virtual WAN. Para completar este exercício, tem de ter um Dispositivo barracuda cloud premise (CPE) e uma licença para o aparelho Barracuda CloudGen WAN que você implanta no centro antes de começar.

Para documentação de implementação da **Cisco SD-WAN** dentro do Azure Virtual WAN - Consulte [Cisco Cloud OnRamp para Multi-Cloud](https://www.cisco.com/c/en/us/td/docs/routers/sdwan/configuration/cloudonramp/ios-xe-17/cloud-onramp-book-xe/cloud-onramp-multi-cloud.html#Cisco_Concept.dita_c61e0e7a-fff8-4080-afee-47b81e8df701). 

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar a configuração, verifique se cumpre os seguintes critérios:

* Obtenha uma licença para o seu gateway Barracuda CloudGen WAN. Para saber mais sobre como fazê-lo, consulte a [Documentação Barracuda CloudGen WAN](https://www.barracuda.com/products/cloudgenwan)

* Tem uma rede virtual a que pretende ligar. Verifique se nenhuma das sub-redes das suas redes no local se sobrepõe às redes virtuais a que pretende ligar. Para criar uma rede virtual no portal Azure, consulte o [Quickstart](../virtual-network/quick-create-portal.md).

* A sua rede virtual não tem quaisquer portas de rede virtuais. Se a sua rede virtual tiver um gateway (VPN ou ExpressRoute), deve remover todos os gateways. Esta configuração requer que as redes virtuais estejam ligadas ao gateway do hub Virtual WAN.

* Obtenha um intervalo de endereços IP para a região do seu hub. O hub é uma rede virtual que é criada e usada pela Virtual WAN. O intervalo de endereços que especifica para o hub não pode sobrepor-se a nenhuma das suas redes virtuais existentes a que se conecta. Também não se pode sobrepor aos intervalos de endereços a que se ligue no local. Se não estiver familiarizado com os intervalos de endereços IP localizados na configuração da rede no local, coordene com alguém que possa fornecer esses detalhes para si.

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-virtual-wan"></a><a name="openvwan"></a>Criar uma WAN Virtual

[!INCLUDE [Create virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="create-a-hub"></a><a name="hub"></a>Criar um hub

Um hub é uma rede virtual que pode conter gateways para o site-para-site, ExpressRoute, ponto a local ou funcionalidade de Aparelho Virtual de Rede. Depois de criar o hub, vai ser cobrado pelo hub, mesmo que não anexe quaisquer sites. Se optar por criar uma porta VPN site-to-site, leva 30 minutos para criar a porta VPN site-to-site no centro virtual. Ao contrário do site-para-site, ExpressRoute ou ponto a local, o hub deve ser criado primeiro antes de poder colocar um Aparelho Virtual de Rede no hub VNet.

1. Localize o WAN virtual que criou. Na página **VIRTUAL WAN,** na secção **Conectividade,** selecione **Hubs**.
1. Na página **Hubs,** selecione +New Hub para abrir a página **do hub virtual Create.**

   :::image type="content" source="./media/how-to-nva-hub/vwan-hub.png" alt-text="Informações básicas":::
1. No **separador 'Criar** página de centro virtual **Basics',** complete os seguintes campos:

   **Detalhes do projeto**

   * Região (anteriormente designada por Localização)
   * Name
   * Espaço de endereço privado hub. O espaço mínimo de endereço é /24 para criar um hub, o que implica que qualquer coisa varia entre /25 e /32 produzirá um erro durante a criação. Azure Virtual WAN, sendo um serviço gerido pela Microsoft, cria as sub-redes apropriadas no centro virtual para os diferentes gateways/serviços. (Por exemplo: Aparelhos Virtuais de Rede, gateways VPN, gateways ExpressRoute, Gateways VPN/User-to-site, Firewall, Encaminhamento, etc.). Não é necessário que o utilizador planeie explicitamente o espaço de endereço de sub-rede para os serviços no hub Virtual, porque a Microsoft faz isso como parte do serviço.
1. Selecione **Review + Criar** para validar.
1. Selecione **Criar** para criar o hub.

## <a name="create-the-network-virtual-appliance-in-the-hub"></a>Criar o aparelho virtual de rede no centro

Neste passo, irá criar um Aparelho Virtual de Rede no centro. O procedimento para cada NVA será diferente para cada produto de cada parceiro NVA. Para este exemplo, estamos a criar um Barracuda CloudGen WAN Gateway.

1. Localize o hub WAN virtual que criou no passo anterior e abra-o.

   :::image type="content" source="./media/how-to-nva-hub/nva-hub.png" alt-text="Hub virtual":::
1. Encontre o azulejo virtual da rede e selecione o link **Criar.**
1. Na lâmina do **aparelho virtual de rede,** selecione **Barracuda CloudGen WAN** e, em seguida, selecione o botão **Criar.**

   :::image type="content" source="./media/how-to-nva-hub/select-nva.png" alt-text="Selecione NVA":::
1. Isto irá levá-lo à oferta do Azure Marketplace para o gateway Barracuda CloudGen WAN. Leia os termos e, em seguida, selecione o botão **Criar** quando estiver pronto.

   :::image type="content" source="./media/how-to-nva-hub/barracuda-create-basics.png" alt-text="Básicos barracuda NVA":::
1. Na página **Basics,** terá de fornecer as seguintes informações:

   * **Subscrição** - Escolha a subscrição utilizada para implantar o WAN virtual e o hub.
   * **Grupo de Recursos** - Escolha o mesmo Grupo de Recursos que usou para implantar o WAN virtual e o hub.
   * **Região** - Escolha a mesma Região em que o seu recurso de hub Virtual está localizado.
   * **Nome da aplicação** - Barracuda NextGen WAN é uma aplicação gerida. Escolha um nome que facilite a identificação deste recurso, pois é assim que será chamado quando aparecer na sua subscrição.
   * **Grupo de Recursos Geridos** - Este é o nome do Grupo de Recursos Geridos no qual a Barracuda irá mobilizar recursos que são geridos por eles. O nome deve ser pré-povoado para isto.
1. Selecione o **seguinte: Botão de gateway CloudGen WAN.**

   :::image type="content" source="./media/how-to-nva-hub/barracuda-cloudgen-wan.png" alt-text="CloudGen WAN Gateway":::
1. Forneça aqui as seguintes informações:

   * **Virtual WAN Hub** - O centro virtual WAN em que pretende implantar este NVA.
   * **Unidades de Infraestrutura NVA** - Indique o número de Unidades de Infraestruturas NVA com as quais pretende implantar este NVA. Escolha a quantidade de capacidade de largura de banda agregada que pretende fornecer em todos os sites de ramificação que estarão conectando-se a este hub através deste NVA.
   * **Token** - Barracuda requer que você forneça um token de autenticação aqui para se identificar como um utilizador registado deste produto. Vai precisar de obter isto de Barracuda.
1. Selecione o botão **'Rever e criar'** para prosseguir.
1. Nesta página, será solicitado que aceite os termos do acordo de Acesso Co-Admin. Esta é uma norma com aplicações geridas onde o Editor terá acesso a alguns recursos nesta implementação. Verifique os **termos e condições acima da** caixa e, em seguida, selecione **Criar**.

## <a name="connect-the-vnet-to-the-hub"></a><a name="vnet"></a>Ligue o VNet ao hub

[!INCLUDE [Connect](../../includes/virtual-wan-connect-vnet-hub-include.md)]

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre o WAN Virtual, consulte a página [O que é Virtual WAN?](virtual-wan-about.md)
* Para saber mais sobre NVAs num hub VIRTUAL WAN, consulte [Sobre o Aparelho Virtual de Rede no hub VIRTUAL WAN (Preview)](about-nva-hub.md).
