---
title: Criar um túnel IPSec em Azure VMware Solution
description: Saiba como estabelecer um túnel local-local VPN (IPsec IKEv1 e IKEv2) em Azure VMware Solutions.
ms.topic: how-to
ms.date: 03/23/2021
ms.openlocfilehash: 280ffdd3fec77208d5b49c8e624b7b22bca1daaf
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105027046"
---
# <a name="create-an-ipsec-tunnel-into-azure-vmware-solution"></a>Criar um túnel IPSec em Azure VMware Solution

Neste artigo, vamos percorrer os passos para estabelecer um túnel VPN (IPsec IKEv1 e IKEv2) que termina no hub virtual WAN da Microsoft Azure. O hub contém o gateway Azure VMware Solution ExpressRoute e o portal VPN local-to-site. Liga um dispositivo VPN no local com um ponto final Azure VMware Solution.

:::image type="content" source="media/create-ipsec-tunnel/vpn-s2s-tunnel-architecture.png" alt-text="Diagrama mostrando arquitetura de túnel local-a-local da VPN." border="false":::

Neste como, você vai:
- Crie um hub Azure Virtual WAN e uma porta de entrada VPN com um endereço IP público anexado a ele. 
- Crie um gateway Azure ExpressRoute e estabeleça um ponto final de Solução VMware Azure. 
- Ativar uma configuração VPN baseada em políticas no local. 

## <a name="prerequisites"></a>Pré-requisitos
Você deve ter um endereço IP virado para o público terminando em um dispositivo VPN no local.

## <a name="step-1-create-an-azure-virtual-wan"></a>Passo 1. Criar um Azure Virtual WAN

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="step-2-create-a-virtual-wan-hub-and-gateway"></a>Passo 2. Crie um hub e gateway virtual WAN

>[!TIP]
>Também pode [criar uma porta de entrada num hub existente.](../virtual-wan/virtual-wan-expressroute-portal.md#existinghub)

1. Selecione o WAN Virtual criado no passo anterior.

1. **Selecione Criar o hub virtual,** insira os campos necessários e, em seguida, selecione **Seguinte: Site para site**. 

   Introduza a sub-rede utilizando um `/24` (mínimo).

   :::image type="content" source="media/create-ipsec-tunnel/create-virtual-hub.png" alt-text="Screenshot mostrando a página do hub virtual Create.":::

4. Selecione o **separador Site-to-site,** defina o gateway site-to-site definindo a produção agregada a partir da queda das unidades de **escala Gateway.** 

   >[!TIP]
   >As unidades de escala estão em pares para redundância, cada uma suportando 500 Mbps (uma unidade de escala = 500 Mbps). 
  
   :::image type="content" source="../../includes/media/virtual-wan-tutorial-hub-include/site-to-site.png" alt-text="Screenshot mostrando os detalhes do site-para-local.":::

5. Selecione o **separador ExpressRoute,** crie um gateway ExpressRoute. 

   :::image type="content" source="../../includes/media/virtual-wan-tutorial-er-hub-include/hub2.png" alt-text="Screenshot das definições ExpressRoute.":::

   >[!TIP]
   >O valor de uma unidade de escala é de 2 Gbps. 

    Leva aproximadamente 30 minutos para criar cada centro. 

## <a name="step-3-create-a-site-to-site-vpn"></a>Passo 3. Criar uma VPN site a site

1. No portal Azure, selecione o WAN virtual que criou anteriormente.

2. Na **visão geral** do hub virtual, selecione **Connectivity**  >  **VPN (Site-to-site)**  >  **Crie um novo site VPN**.

   :::image type="content" source="media/create-ipsec-tunnel/create-vpn-site-basics.png" alt-text="Screenshot da página 'Vista Geral' para o centro virtual, com VPN (site-to-site) e Criar um novo site VPN selecionado.":::  
 
3. No separador **Básicos, insira** os campos necessários. 

   :::image type="content" source="media/create-ipsec-tunnel/create-vpn-site-basics2.png" alt-text="Screenshot do separador Basics para o novo site VPN.":::  

   1. Desa estada o **Protocolo de Gateway de Fronteira** para **Ativar**.  Quando ativado, garante que tanto a Azure VMware Solution como os servidores no local anunciam as suas rotas através do túnel. Se desativados, as sub-redes que precisam de ser publicitadas devem ser mantidas manualmente. Se as sub-redes não forem perdidas, o HCX deixará de formar a malha de serviço. Para mais informações, consulte  [Sobre o BGP com o Azure VPN Gateway](../vpn-gateway/vpn-gateway-bgp-overview.md).
   
   1. Para o **espaço de endereço privado,** insira o bloco CIDR no local. É usado para encaminhar todo o tráfego para o local através do túnel. O bloco CIDR só é necessário se não ativar o BGP.

1. Selecione **Seguinte : Links** e complete os campos necessários. Especificar os nomes de link e fornecedor permite-lhe distinguir entre qualquer número de gateways que possam eventualmente ser criados como parte do hub. BGP e número de sistema autónomo (ASN) devem ser únicos dentro da sua organização.

   :::image type="content" source="media/create-ipsec-tunnel/create-vpn-site-links.png" alt-text="Screenshot que mostra detalhes de ligação.":::

1. Selecione **Rever + criar**. 

1. Navegue para o centro virtual que deseja e desmarca a **associação Hub** para ligar o seu site VPN ao centro.
 
   :::image type="content" source="../../includes/media/virtual-wan-tutorial-site-include/connect.png" alt-text="Screenshot que mostra o painel de Sites Conectados para O HUB Virtual pronto para chaves pré-partilhadas e configurações associadas.":::   

## <a name="step-4-optional-create-policy-based-vpn-site-to-site-tunnels"></a>Passo 4: (Opcional) Criar túneis VPN baseados em políticas

>[!IMPORTANT]
>Trata-se de um passo facultativo e aplica-se apenas às VPNs baseadas em políticas. 

As configurações VPN baseadas em políticas requerem que as redes de Solução VMware Azure sejam especificadas no local e as redes de Solução VMware Azure devem ser especificadas, incluindo as gamas de hub.  Estes intervalos de hub especificam o domínio de encriptação do túnel VPN baseado na política no ponto final.  O lado da Solução VMware Azure apenas requer que o indicador de seletor de tráfego baseado em políticas seja ativado. 

1. No portal Azure, aceda ao seu hub Virtual WAN. Em **Conectividade,** selecione **VPN (Site a site)**.

2. Selecione o nome do site VPN, a elipse (...) na extrema-direita e, em seguida, **edite a ligação VPN a este hub**.
 
   :::image type="content" source="media/create-ipsec-tunnel/edit-vpn-section-to-this-hub.png" alt-text="Screenshot da página em Azure para o site do hub VIRTUAL WAN mostrando uma elipse selecionada para aceder à ligação Editar VPN a este hub." lightbox="media/create-ipsec-tunnel/edit-vpn-section-to-this-hub.png":::

3. Edite a ligação entre o site VPN e o hub e, em seguida, **selecione Guardar**.
   - Internet Protocol Security (IPSec), selecione **Custom.**
   - Utilize o seletor de tráfego baseado em políticas, **selecione Ative**
   - Especificar os detalhes para **a Fase 1** e **fase 2 do IKE(ipsec)**. 
 
   :::image type="content" source="media/create-ipsec-tunnel/edit-vpn-connection.png" alt-text="Screenshot da página de ligação editar VPN."::: 
 
   Os seus seletores de tráfego ou sub-redes que fazem parte do domínio de encriptação baseado em políticas devem ser:
    
   - Hub VIRTUAL WAN `/24`
   - Nuvem privada Azure VMware Solution `/22`
   - Rede virtual Connected Azure (se presente)

## <a name="step-5-connect-your-vpn-site-to-the-hub"></a>Passo 5. Ligue o seu site VPN ao centro

1. Selecione o nome do site VPN e, em seguida, selecione **os sites Connect VPN**. 

1. No campo **de chaves pré-partilhado,** introduza a chave previamente definida para o ponto final no local. 

   >[!TIP]
   >Se não tiver uma chave previamente definida, pode deixar este campo em branco. Uma chave é gerada automaticamente para si. 

   :::image type="content" source="../../includes/media/virtual-wan-tutorial-connect-vpn-site-include/connect.png" alt-text="Screenshot que mostra o painel de Sites Conectados para O HUB Virtual pronto para uma chave pré-partilhada e configurações associadas. "::: 

1. Se estiver a implantar uma firewall no centro e for o próximo salto, desloque a opção **Propagate Predefinido** rota para **ativar**. 

   Quando ativado, o hub Virtual WAN propaga-se a uma ligação apenas se o hub já tiver aprendido a rota padrão ao implantar uma firewall no centro ou se outro site conectado tiver feito um túnel forçado. A rota padrão não tem origem no hub Virtual WAN.  

1. Selecione **Ligar**. Após alguns minutos, o site mostra o estado de ligação e conectividade.

   :::image type="content" source="../../includes/media/virtual-wan-tutorial-connect-vpn-site-include/status.png" alt-text="Screenshot que mostra uma ligação site-a-local e estado de conectividade." lightbox="../../includes/media/virtual-wan-tutorial-connect-vpn-site-include/status.png":::

1. [Descarregue o ficheiro de configuração VPN](../virtual-wan/virtual-wan-site-to-site-portal.md#device) para o ponto final no local.  

3. Remeja a Solução Azure VMware ExpressRoute no hub VIRTUAL WAN. 

   >[!IMPORTANT]
   >Primeiro tem de ter uma nuvem privada criada antes de poder remendar a plataforma. 

   [!INCLUDE [request-authorization-key](includes/request-authorization-key.md)]

4. Link Azure VMware Solution e a porta de entrada VPN juntos no hub Virtual WAN. Utilizará a chave de autorização e o ExpressRoute ID (circuito de pares URI) do passo anterior.

   1. Selecione o seu gateway ExpressRoute e, em seguida, **selecione a tecla de autorização Redentor**.

      :::image type="content" source="media/create-ipsec-tunnel/redeem-authorization-key.png" alt-text="Screenshot da página ExpressRoute para a nuvem privada, com a chave de autorização Redentor selecionada.":::

   1. Cole a chave de autorização no campo **Chave de Autorização.**
   1. Cole o ExpressRoute ID no campo **URI do circuito Peer.** 
   1. Selecione **associar automaticamente este circuito ExpressRoute à caixa de verificação do hub.** 
   1. **Selecione Adicionar** para estabelecer o link. 

5. Teste a sua ligação [criando um segmento NSX-T](./tutorial-nsx-t-network-segment.md) e fornecendo um VM na rede. Ping tanto no local como nos pontos finais da Azure VMware Solution.
