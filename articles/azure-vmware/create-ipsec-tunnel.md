---
title: Criar um túnel IPSec em Azure VMware Solution
description: Saiba como criar um hub WAN virtual para estabelecer um túnel IPSec em Azure VMware Solutions.
ms.topic: how-to
ms.date: 10/02/2020
ms.openlocfilehash: 63318b9fdd0de5e0ce102fafe332f40f595f38f1
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2020
ms.locfileid: "94357849"
---
# <a name="create-an-ipsec-tunnel-into-azure-vmware-solution"></a>Criar um túnel IPSec em Azure VMware Solution

Neste artigo, vamos percorrer os passos para estabelecer um túnel VPN (IPsec IKEv1 e IKEv2) que termina no hub virtual WAN da Microsoft Azure. Criaremos um hub Azure Virtual WAN e uma porta de entrada VPN com um endereço IP público anexado a ele. Em seguida, criaremos um gateway Azure ExpressRoute e estabeleceremos um ponto final de Solução VMware Azure. Também veremos os detalhes de permitir uma configuração de VPN baseada em políticas no local. 

## <a name="topology"></a>Topologia

![VPN arquitetura de túnel local-a-local.](media/create-ipsec-tunnel/vpn-s2s-tunnel-architecture.png)

O hub Azure Virtual contém o gateway ExpressRoute da Solução VMware Azure e o portal VPN local. Liga um dispositivo VPN no local com um ponto final Azure VMware Solution.

## <a name="before-you-begin"></a>Antes de começar

Para criar o túnel VPN local-local, você precisará criar um endereço IP virado para o público terminando num dispositivo VPN no local.

## <a name="create-a-virtual-wan-hub"></a>Criar um hub VIRTUAL WAN

1. No portal Azure, procure no **VIRTUAL WANS**. Selecione **+Adicionar**. A página Create WAN abre.  

2. Introduza os campos necessários na página **Create WAN** e, em seguida, selecione Review **+ Create**.
   
   | Campo | Valor |
   | --- | --- |
   | **Subscrição** | O valor é pré-povoado com a subscrição pertencente ao grupo de recursos. |
   | **Grupo de recursos** | O WAN Virtual é um recurso global e não se limita a uma região específica.  |
   | **Localização do grupo de recursos** | Para criar o hub VIRTUAL WAN, é necessário definir uma localização para o grupo de recursos.  |
   | **Name** |   |
   | **Tipo** | Selecione **Standard** , que permitirá mais do que apenas o tráfego de gateway VPN.  |


    :::image type="content" source="media/create-ipsec-tunnel/create-wan.png" alt-text="Criar WAN.":::

3. No portal Azure, selecione o WAN Virtual que criou no passo anterior, selecione **Criar hub virtual,** insira os campos necessários e, em seguida, selecione **Seguinte: Site para site**. 

   | Campo | Valor |
   | --- | --- |
   | **Região** | A seleção de uma região é necessária do ponto de vista da gestão.  |
   | **Name** |    |
   | **Espaço de endereços privados do Hub** | Introduza a sub-rede utilizando um `/24` (mínimo).  |

    :::image type="content" source="media/create-ipsec-tunnel/create-virtual-hub.png" alt-text="Criar hub virtual.":::

4. No **separador Site-to-site,** defina o gateway site-to-site definindo a produção agregada a partir da queda das unidades de **escala Gateway.** 

   >[!TIP]
   >Uma unidade de escala = 500 Mbps. As unidades de escala estão em pares para redundância, cada uma suportando 500 Mbps.
  
5. No **separador ExpressRoute,** crie um gateway ExpressRoute. 

   >[!TIP]
   >O valor de uma unidade de escala é de 2 Gbps. 

    Leva aproximadamente 30 minutos para criar cada centro. 

## <a name="create-a-vpn-site"></a>Criar um site VPN 

1. Nos **recursos recentes** no portal Azure, selecione o WAN virtual que criou na secção anterior.

2. Na **visão geral** do hub virtual, selecione **Connectivity**  >  **VPN (Site-to-site)** e, em seguida, selecione **Criar novo site VPN**.


    :::image type="content" source="media/create-ipsec-tunnel/create-vpn-site-basics.png" alt-text="Crie o site VPN.":::  
 
3. No **separador Básicos, insira** os campos necessários e, em seguida, selecione **Seguinte : Links**. 

   | Campo | Valor |
   | --- | --- |
   | **Região** | A mesma região especificada na secção anterior.  |
   | **Name** |  |
   | **Fornecedor de dispositivos** |  |
   | **Protocolo BGP** | Definir para **permitir** garantir tanto a Solução VMware Azure como os servidores no local publicitam as suas rotas através do túnel. Se desativados, as sub-redes que precisam de ser publicitadas devem ser mantidas manualmente. Se as sub-redes não forem perdidas, o HCX deixará de formar a malha de serviço. Para mais informações, consulte  [Sobre o BGP com o Azure VPN Gateway](../vpn-gateway/vpn-gateway-bgp-overview.md). |
   | **Espaço de endereço privado**  | Introduza o bloco CIDR no local.  É usado para encaminhar todo o tráfego para o local através do túnel.  O bloco CIDR só é necessário se não ativar o BGP. |
   | **Ligar a** |   |

4. No separador **Links,** preencha os campos necessários e selecione **Review + create**. Especificar os nomes de link e fornecedor permite-lhe distinguir entre qualquer número de gateways que possam eventualmente ser criados como parte do hub. BGP e número de sistema autónomo (ASN) devem ser únicos dentro da sua organização.
 
## <a name="optional-defining-a-vpn-site-for-policy-based-vpn-site-to-site-tunnels"></a>(Opcional) Definição de um site VPN para túneis locais VPN baseados em políticas

Esta secção aplica-se apenas às VPNs baseadas em políticas. As configurações VPN baseadas em políticas (ou estáticas, baseadas em rotas) são impulsionadas por capacidades de dispositivos VPN no local na maioria dos casos. Exigem que as redes de Solução VMware Azure sejam especificadas no local e a Azure VMware Solution. Para a Solução VMware Azure com um hub Azure Virtual WAN, não é possível selecionar *nenhuma* rede. Em vez disso, tem de especificar todas as gamas de hubs virtuais WAN da Solução VMware Azure VMware. Estes intervalos de hub são usados para especificar o domínio de encriptação do túnel VPN base de política no ponto final do local. O lado da Solução VMware Azure apenas requer que o indicador de seletor de tráfego baseado em políticas seja ativado. 

1. No portal Azure, aceda ao seu hub Virtual WAN; em **Conectividade,** selecione **VPN (Site a site)**.

2. Selecione o nome do seu site VPN e, em seguida, a elipse (...) na extrema-direita; em seguida, **selecione a ligação VPN para este hub**.
 
    :::image type="content" source="media/create-ipsec-tunnel/edit-vpn-section-to-this-hub.png" alt-text="Edite a ligação VPN a este centro." lightbox="media/create-ipsec-tunnel/edit-vpn-section-to-this-hub.png":::

3. Edite a ligação entre o site VPN e o hub e, em seguida, **selecione Guardar**.
   - Internet Protocol Security (IPSec), selecione **Custom.**
   - Utilize o seletor de tráfego baseado em políticas, **selecione Ative**
   - Especificar os detalhes para **a Fase 1** e **fase 2 do IKE(ipsec)**. 
 
    :::image type="content" source="media/create-ipsec-tunnel/edit-vpn-connection.png" alt-text="Editar ligação VPN"::: 
 
    Os seus seletores de tráfego ou sub-redes que fazem parte do domínio de encriptação baseado em políticas devem ser:
    
    - O centro virtual wan /24
    - A nuvem privada Azure VMware Solution /22
    - A rede virtual Azure conectada (se presente)

## <a name="connect-your-vpn-site-to-the-hub"></a>Ligue o seu site VPN ao centro

1. Verifique a caixa ao lado do nome do seu site VPN (ver **o site VPN** anterior para a imagem do site) e, em seguida, selecione **os sites Connect VPN**. No campo **de chaves pré-partilhado,** introduza a chave previamente definida para o ponto final no local. Se não tiver uma chave previamente definida, pode deixar este campo em branco e uma chave será gerada automaticamente para si. 
 
    Só ativa a **Rota Padrão propagada** se estiver a implantar uma firewall no centro e é o próximo salto para ligações através desse túnel.

    Selecione **Connect** (Ligar). Um ecrã de estado de ligação mostrará o estado da criação do túnel.

2. Vá ao panorama geral da WAN. Abra a página do site VPN e descarregue o ficheiro de configuração VPN para aplicá-lo no ponto final no local.  

3. Agora vamos remendar a Solução Azure VMware ExpressRoute no centro virtual WAN. (Este passo requer primeiro a criação da sua nuvem privada.)

    Aceda à secção de **Conectividade** da nuvem privada Azure VMware Solution. No **separador ExpressRoute,** selecione **+ Solicite uma chave de autorização.** Nomeie-o e **selecione Criar**. (Pode levar cerca de 30 segundos para criar a chave.) Copie o ID ExpressRoute e a chave de autorização. 

    :::image type="content" source="media/create-ipsec-tunnel/express-route-connectivity.png" alt-text="Copiar ID da rota expresso e chave de autorização.":::

    > [!NOTE]
    > A chave de autorização desaparecerá passado algum tempo, por isso copie-a assim que aparecer.

4. Em seguida, ligaremos a Azure VMware Solution e a porta de entrada VPN juntos no hub Virtual WAN. No portal Azure, abra o WAN Virtual que criou anteriormente. Selecione o hub Virtual WAN criado e, em seguida, selecione **ExpressRoute** no painel esquerdo. Selecione **+ Chave de autorização Redentor**.

    :::image type="content" source="media/create-ipsec-tunnel/redeem-authorization-key.png" alt-text="Resgatar a chave de autorização.":::

    Cole a chave de autorização no campo chave de autorização e o ID ExpressRoute no campo **URI do circuito peer.** Certifique-se de que **selecione associar automaticamente este circuito ExpressRoute ao hub.** **Selecione Adicionar** para estabelecer o link. 

5. Para testar a sua ligação, [Crie um segmento NSX-T](./tutorial-nsx-t-network-segment.md) e provisa um VM na rede. Teste colocando os pontos finais da Solução VMware no local e no Azure VMware.
