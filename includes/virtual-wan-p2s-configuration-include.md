---
author: cherylmc
ms.author: cherylmc
ms.date: 02/23/2021
ms.service: virtual-wan
ms.topic: include
ms.openlocfilehash: 567c0bb75c30a1f0ccdcde7ec1b0f04f5d6e54c5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102048267"
---
[!INCLUDE [Portal feature rollout](virtual-wan-portal-feature-rollout.md)]

1. Navegue para **Todos os recursos** e selecione o WAN virtual que criou e, em seguida, selecione as **configurações VPN** do utilizador a partir do menu à esquerda.
1. Na página de **configurações VPN do utilizador,** selecione **+Criar config VPN** do utilizador no topo da página para abrir a nova página de **configuração VPN** do utilizador.

   :::image type="content" source="media/virtual-wan-p2s-configuration/user-vpn.png" alt-text="Screenshot da página de configurações VPN do utilizador.":::

1. No separador **Básicos,** em **detalhes de Instância,** insira o **Nome** que pretende atribuir à sua configuração VPN.
1. Para o **tipo de túnel**, a partir da queda, selecione o tipo de túnel que deseja. As opções do tipo túnel são: **IKEv2 VPN,** **OpenVPN,** **e OpenVpn e IkeV2**.
1. Utilize os seguintes passos que correspondem ao tipo de túnel que selecionou. Depois de especificados todos os valores, clique em **Rever + criar** e, em seguida, **criar** para criar a configuração.

   **IKEv2 VPN**

   * **Requisitos:** Ao selecionar o tipo de túnel **IKEv2,** vê uma mensagem a direcionar-se para selecionar um método de autenticação. Para o IKEv2, pode especificar apenas um método de autenticação. Pode escolher o Certificado Azure, o Diretório Ativo Azure ou a autenticação baseada no RADIUS.

   * **Parâmetros personalizados IPSec:** Para personalizar os parâmetros para a Fase 1 e fase 2 do IKE, alternar o interruptor IPsec para **Custom** e selecionar os valores dos parâmetros. Para obter mais informações sobre parâmetros personalizáveis, consulte o artigo [IPsec personalizado.](../articles/virtual-wan/point-to-site-ipsec.md)

     :::image type="content" source="media/virtual-wan-p2s-configuration/custom.png" alt-text="Screenshot do IPsec switch para personalizado.":::

   * **Autenticação:** Navegue para o mecanismo de autenticação que pretende utilizar clicando em **Seguinte** na parte inferior da página para avançar para o método de autenticação, ou clique no separador apropriado no topo da página. Alterne o interruptor para **Sim** para selecionar o método.

     Neste exemplo, é selecionada a autenticação RADIUS. Para a autenticação baseada no RADIUS, pode fornecer um endereço IP do servidor RADIUS secundário e um segredo do servidor.

     :::image type="content" source="media/virtual-wan-p2s-configuration/ike-radius.png" alt-text="Imagem de IKE.":::

   **OpenVPN**

   * **Requisitos:** Ao selecionar o tipo de túnel **OpenVPN,** vê uma mensagem a direcionar-se para selecionar um mecanismo de autenticação. Se o OpenVPN for selecionado como o tipo de túnel, poderá especificar vários métodos de autenticação. Pode escolher qualquer subconjunto de Certificado Azure, Diretório Ativo Azure ou autenticação baseada em RADIUS. Para a autenticação baseada no RADIUS, pode fornecer um endereço IP do servidor RADIUS secundário e um segredo do servidor.

   * **Autenticação:** Navegue para o(s) método de autenticação que pretende utilizar clicando em **Seguinte** na parte inferior da página para avançar para o método de autenticação, ou clique no separador apropriado no topo da página.
   Para cada método que pretende selecionar, altere o interruptor para **Sim** e introduza os valores apropriados.

     Neste exemplo, o Azure Ative Directory é selecionado.

     :::image type="content" source="media/virtual-wan-p2s-configuration/openvpn.png" alt-text="Screenshot da página OpenVPN.":::
