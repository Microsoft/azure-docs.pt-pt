---
title: Criar um túnel IPSec em Azure VMware Solution
description: Saiba como criar um hub WAN virtual para estabelecer um túnel IPSec em Azure VMware Solutions.
ms.topic: how-to
ms.date: 10/02/2020
ms.openlocfilehash: 21df674862b65ef6573a8a3fcfd7538b1053f04e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103491857"
---
# <a name="create-an-ipsec-tunnel-into-azure-vmware-solution"></a>Criar um túnel IPSec em Azure VMware Solution

Neste artigo, vamos percorrer os passos para estabelecer um túnel VPN (IPsec IKEv1 e IKEv2) que termina no hub virtual WAN da Microsoft Azure. Criaremos um hub Azure Virtual WAN e uma porta de entrada VPN com um endereço IP público anexado a ele. Em seguida, criaremos um gateway Azure ExpressRoute e estabeleceremos um ponto final de Solução VMware Azure. Também veremos os detalhes de permitir uma configuração de VPN baseada em políticas no local. 

## <a name="topology"></a>Topologia

![Diagrama mostrando arquitetura de túnel local-a-local da VPN.](media/create-ipsec-tunnel/vpn-s2s-tunnel-architecture.png)

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
   | **Nome** |   |
   | **Tipo** | Selecione **Standard**, que permitirá mais do que apenas o tráfego de gateway VPN.  |

   :::image type="content" source="media/create-ipsec-tunnel/create-wan.png" alt-text="Screenshot mostrando a página Create WAN no portal Azure.":::

3. No portal Azure, selecione o WAN Virtual que criou no passo anterior, selecione **Criar hub virtual,** insira os campos necessários e, em seguida, selecione **Seguinte: Site para site**. 

   | Campo | Valor |
   | --- | --- |
   | **Região** | A seleção de uma região é necessária do ponto de vista da gestão.  |
   | **Nome** |    |
   | **Espaço de endereços privados do Hub** | Introduza a sub-rede utilizando um `/24` (mínimo).  |

   :::image type="content" source="media/create-ipsec-tunnel/create-virtual-hub.png" alt-text="Screenshot mostrando a página do hub virtual Create.":::

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

   :::image type="content" source="media/create-ipsec-tunnel/create-vpn-site-basics.png" alt-text="Screenshot da página 'Vista Geral' para o centro virtual, com VPN (site-to-site) e Criar um novo site VPN selecionado.":::  
 
3. No **separador Básicos, insira** os campos necessários e, em seguida, selecione **Seguinte : Links**. 

   | Campo | Valor |
   | --- | --- |
   | **Região** | A mesma região especificada na secção anterior.  |
   | **Nome** |  |
   | **Fornecedor do dispositivo** |  |
   | **Protocolo BGP** | Definir para **permitir** garantir tanto a Solução VMware Azure como os servidores no local publicitam as suas rotas através do túnel. Se desativados, as sub-redes que precisam de ser publicitadas devem ser mantidas manualmente. Se as sub-redes não forem perdidas, o HCX deixará de formar a malha de serviço. Para mais informações, consulte  [Sobre o BGP com o Azure VPN Gateway](../vpn-gateway/vpn-gateway-bgp-overview.md). |
   | **Espaço de endereço privado**  | Introduza o bloco CIDR no local.  É usado para encaminhar todo o tráfego para o local através do túnel.  O bloco CIDR só é necessário se não ativar o BGP. |
   | **Ligar a** |   |

4. No separador **Links,** preencha os campos necessários e selecione **Review + create**. Especificar os nomes de link e fornecedor permite-lhe distinguir entre qualquer número de gateways que possam eventualmente ser criados como parte do hub. BGP e número de sistema autónomo (ASN) devem ser únicos dentro da sua organização.
 
## <a name="optional-defining-a-vpn-site-for-policy-based-vpn-site-to-site-tunnels"></a>(Opcional) Definição de um site VPN para túneis locais VPN baseados em políticas

Esta secção aplica-se apenas às VPNs baseadas em políticas. As configurações VPN baseadas em políticas (ou estáticas, baseadas em rotas) são impulsionadas por capacidades de dispositivos VPN no local na maioria dos casos. Exigem que as redes de Solução VMware Azure sejam especificadas no local e a Azure VMware Solution. Para a Solução VMware Azure com um hub Azure Virtual WAN, não é possível selecionar *nenhuma* rede. Em vez disso, tem de especificar todas as gamas de hubs virtuais WAN da Solução VMware Azure VMware. Estes intervalos de hub são usados para especificar o domínio de encriptação do túnel VPN base de política no ponto final do local. O lado da Solução VMware Azure apenas requer que o indicador de seletor de tráfego baseado em políticas seja ativado. 

1. No portal Azure, aceda ao seu hub Virtual WAN. Em **Conectividade,** selecione **VPN (Site a site)**.

2. Selecione o nome do site VPN, a elipse (...) na extrema-direita e, em seguida, **edite a ligação VPN a este hub**.
 
   :::image type="content" source="media/create-ipsec-tunnel/edit-vpn-section-to-this-hub.png" alt-text="Screenshot da página em Azure para o site do hub VIRTUAL WAN mostrando uma elipse selecionada para aceder à ligação Editar VPN a este hub." lightbox="media/create-ipsec-tunnel/edit-vpn-section-to-this-hub.png":::

3. Edite a ligação entre o site VPN e o hub e, em seguida, **selecione Guardar**.
   - Internet Protocol Security (IPSec), selecione **Custom.**
   - Utilize o seletor de tráfego baseado em políticas, **selecione Ative**
   - Especificar os detalhes para **a Fase 1** e **fase 2 do IKE(ipsec)**. 
 
   :::image type="content" source="media/create-ipsec-tunnel/edit-vpn-connection.png" alt-text="Screenshot da página de ligação editar VPN."::: 
 
   Os seus seletores de tráfego ou sub-redes que fazem parte do domínio de encriptação baseado em políticas devem ser:
    
   - O centro virtual wan /24
   - A nuvem privada Azure VMware Solution /22
   - A rede virtual Azure conectada (se presente)

## <a name="connect-your-vpn-site-to-the-hub"></a>Ligue o seu site VPN ao centro

1. Selecione o nome do site VPN e, em seguida, selecione **os sites Connect VPN**. 
1. No campo **de chaves pré-partilhado,** introduza a chave previamente definida para o ponto final no local. 

   >[!TIP]
   >Se não tiver uma chave previamente definida, pode deixar este campo em branco. Uma chave é gerada automaticamente para si. 
 
   >[!IMPORTANT]
   >Só ativa a **Rota Padrão propagada** se estiver a implantar uma firewall no centro e é o próximo salto para ligações através desse túnel.

1. Selecione **Ligar**. Um ecrã de estado de ligação mostra o estado da criação do túnel.

2. Vá ao panorama virtual do WAN e abra a página do site VPN para descarregar o ficheiro de configuração VPN para o ponto final no local.  

3. Remeja a Solução Azure VMware ExpressRoute no hub VIRTUAL WAN. Este passo requer primeiro a criação da sua nuvem privada.

   [!INCLUDE [request-authorization-key](includes/request-authorization-key.md)]

4. Link Azure VMware Solution e a porta de entrada VPN juntos no hub Virtual WAN. 
   1. No portal Azure, abra o WAN Virtual que criou anteriormente. 
   1. Selecione o hub Virtual WAN criado e, em seguida, selecione **ExpressRoute** no painel esquerdo. 
   1. Selecione **+ Chave de autorização Redentor**.

      :::image type="content" source="media/create-ipsec-tunnel/redeem-authorization-key.png" alt-text="Screenshot da página ExpressRoute para a nuvem privada, com a chave de autorização Redentor selecionada.":::

   1. Cole a chave de autorização no campo chave de autorização.
   1. Depois do ExpressRoute ID no campo URI do **circuito peer.** 
   1. Selecione **associar automaticamente este circuito ExpressRoute ao hub.** 
   1. **Selecione Adicionar** para estabelecer o link. 

5. Teste a sua ligação [criando um segmento NSX-T](./tutorial-nsx-t-network-segment.md) e fornecendo um VM na rede. Ping tanto no local como nos pontos finais da Azure VMware Solution.
