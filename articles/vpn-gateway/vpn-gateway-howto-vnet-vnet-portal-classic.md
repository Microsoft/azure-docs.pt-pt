---
title: 'Criar uma ligação entre VNets: clássico: Portal Azure'
description: Ligue as redes virtuais Azure em conjunto utilizando o PowerShell e o portal Azure.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/08/2020
ms.author: cherylmc
ms.openlocfilehash: 4b1007fe89cf455b6af8ebba00f24e8019ad8013
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92078294"
---
# <a name="configure-a-vnet-to-vnet-connection-classic"></a>Configure uma ligação VNet-to-VNet (clássico)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

Este artigo ajuda-o a criar uma ligação de gateway VPN entre redes virtuais. As redes virtuais podem estar nas mesmas regiões ou em regiões diferentes e pertencer às mesmas subscrições ou a subscrições diferentes. Os passos deste artigo aplicam-se ao modelo de implantação clássico e ao portal Azure. Também pode criar esta configuração ao utilizar uma ferramenta de implementação diferente ou modelo de implementação ao selecionar uma opção diferente da lista seguinte:

> [!div class="op_single_selector"]
> * [Portal do Azure](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [CLI do Azure](vpn-gateway-howto-vnet-vnet-cli.md)
> * [Portal do Azure (clássico)](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Ligue diferentes modelos de implementação - portal do Azure](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Ligue diferentes modelos de implementação - PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
>
>

![Diagrama de conectividade VNet para VNet](./media/vpn-gateway-howto-vnet-vnet-portal-classic/v2vclassic.png)

## <a name="about-vnet-to-vnet-connections"></a>Acerca das ligações VNet a VNet

Ligar uma rede virtual a outra rede virtual (VNet-to-VNet) no modelo clássico de implementação utilizando um gateway VPN é semelhante a ligar uma rede virtual a uma localização no local. Ambos os tipos de conetividade utilizam um gateway de VPN para fornecer um túnel seguro através de IPsec/IKE.

Os VNets que conecta podem ser em diferentes subscrições e diferentes regiões. Pode combinar comunicação VNet com VNet com configurações multi-locais. Este procedimento permite-lhe estabelecer topologias de rede que combinam uma conetividade em vários locais com uma conetividade de rede intervirtual.

![Ligações VNet para VNet](./media/vpn-gateway-howto-vnet-vnet-portal-classic/aboutconnections.png)

### <a name="why-connect-virtual-networks"></a><a name="why"></a>Por que motivo ligar redes virtuais?

Poderá pretender ligar redes virtuais pelos seguintes motivos:

* **Geopresença e georredundância entre várias regiões**

  * Pode configurar a sua própria georreplicação ou sincronização com uma conetividade segura sem passar por pontos finais com acesso à Internet.
  * Com o Azure Load Balancer e a Microsoft ou a tecnologia de clustering de terceiros, pode configurar uma carga de trabalho altamente disponível com geo-redundância em várias regiões do Azure. Um exemplo importante consiste em configurar o SQL Always On com Grupos de Disponibilidade propagando-se em várias regiões do Azure.
* **Aplicações regionais de vários níveis com forte limite de isolamento**

  * Dentro da mesma região, pode configurar aplicações multi-camadas com múltiplos VNets ligados juntamente com um forte isolamento e uma comunicação inter-nível segura.
* **Assinatura cruzada, comunicação inter-organização em Azure**

  * Se tiver várias subscrições do Azure, pode ligar cargas de trabalho de diferentes subscrições de forma segura entre redes virtuais.
  * Para empresas ou prestadores de serviços, pode permitir a comunicação inter-organizacional com tecnologia VPN segura dentro do Azure.

Para obter mais informações sobre ligações de VNet a VNet, veja [Considerações de VNet a VNet](#faq) no final deste artigo.

## <a name="prerequisites"></a>Pré-requisitos

Utilizamos o portal para a maioria dos passos, mas tem de usar o PowerShell para criar as ligações entre os VNets. Não é possível criar as ligações utilizando o portal Azure. [!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

## <a name="step-1---plan-your-ip-address-ranges"></a><a name="plan"></a>Passo 1 - Planear os intervalos de endereços IP

É importante decidir os intervalos que vai usar para configurar as suas redes virtuais. Para esta configuração, deve certificar-se de que nenhuma das gamas VNet se sobrepõe entre si, ou com qualquer uma das redes locais a que se ligam.

A tabela que se segue mostra um exemplo de como definir os seus VNets. Utilize os intervalos apenas como orientação. Anota os intervalos para as suas redes virtuais. Precisa desta informação para mais tarde.

**Exemplo**

| Rede Virtual | Espaço de Endereços | Região | Liga-se ao site de rede local |
|:--- |:--- |:--- |:--- |
| TestVNet1 |TestVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |E.U.A. Leste |VNet4Local<br>(10.41.0.0/16)<br>(10.42.0.0/16) |
| TestVNet4 |TestVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |E.U.A. Oeste |VNet1Local<br>(10.11.0.0/16)<br>(10.12.0.0/16) |

## <a name="step-2---create-the-virtual-networks"></a><a name="vnetvalues"></a>Passo 2 - Criar as redes virtuais

Neste passo, cria-se duas redes virtuais clássicas. Se estiver a utilizar este artigo como exercício, pode utilizar os seguintes valores de exemplo:

**Valores para TestVNet1**

Nome: TestVNet1<br>
Espaço do endereço: 10.11.0.0/16, 10.12.0.0/16 (opcional)<br>
Nome da sub-rede: predefinição<br>
Intervalo de endereços da sub-rede: 10.11.0.0/24<br>
Grupo de recursos: ClassicRG<br>
Localização: E.U.A. Leste<br>
GatewaySubnet: 10.11.1.0/27

**Valores para TestVNet4**

Nome: TestVNet4<br>
Espaço do endereço: 10.41.0.0/16, 10.42.0.0/16 (opcional)<br>
Nome da sub-rede: predefinição<br>
Intervalo de endereços da sub-rede: 10.41.0.1/24<br>
Grupo de recursos: ClassicRG<br>
Localização: E.U.A. Oeste<br>
GatewaySubnet: 10.41.1.0/27

**Ao criar os seus VNets, tenha em mente as seguintes definições:**

* **Espaços de Endereço de Rede Virtual** – Na página De Espaços de Endereço de Rede Virtual, especifique o intervalo de endereços que pretende utilizar para a sua rede virtual. Estes são os endereços IP dinâmicos que serão atribuídos aos VMs e outros casos de função que implementa nesta rede virtual.<br>Os espaços de endereço que seleciona não podem sobrepor-se aos espaços de endereço de qualquer um dos outros VNets ou locais no local a que este VNet irá ligar.

* **Localização** – Quando cria uma rede virtual, associa-a a uma localização Azure (região). Por exemplo, se pretender que os seus VMs que são implantados na sua rede virtual estejam fisicamente localizados nos EUA Ocidentais, selecione essa localização. Não é possível alterar a localização associada à sua rede virtual depois de a criar.

**Depois de criar os seus VNets, pode adicionar as seguintes definições:**

* **Espaço de endereço** – Espaço adicional de endereço não é necessário para esta configuração, mas pode adicionar espaço adicional de endereço após a criação do VNet.

* **Sub-redes** – Não são necessárias sub-redes adicionais para esta configuração, mas é melhor ter os seus VMs numa sub-rede separada das outras instâncias de função.

* **Servidores DNS** – Introduza o nome do servidor DNS e o endereço IP. Esta definição não cria um servidor DNS. Permite-lhe especificar os servidores DNS que pretende utilizar para a resolução de nomes desta rede virtual.

### <a name="to-create-a-classic-virtual-network"></a>Para criar uma rede virtual clássica

[!INCLUDE [basic classic vnet](../../includes/vpn-gateway-vnet-classic.md)]

[!INCLUDE [basic classic DNS](../../includes/vpn-gateway-dns-classic.md)]

## <a name="step-3---configure-the-local-site"></a><a name="localsite"></a>Passo 3 - Configurar o site local

O Azure utiliza as definições especificadas em cada site de rede local para determinar como encaminhar o tráfego entre as VNets. Cada VNet deve apontar para a rede local respetivo para a que pretende encaminhar o tráfego. Você determina o nome que deseja usar para se referir a cada site de rede local. É melhor usar algo descritivo.

Por exemplo, o TestVNet1 conecta-se a um site de rede local que cria chamado 'VNet4Local'. As definições de VNet4Local contêm os prefixos de endereço para TestVNet4.

O site local para cada VNet é o outro VNet. Os seguintes valores de exemplo são utilizados para a nossa configuração:

| Rede Virtual | Espaço de Endereços | Região | Liga-se ao site de rede local |
|:--- |:--- |:--- |:--- |
| TestVNet1 |TestVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |E.U.A. Leste |VNet4Local<br>(10.41.0.0/16)<br>(10.42.0.0/16) |
| TestVNet4 |TestVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |E.U.A. Oeste |VNet1Local<br>(10.11.0.0/16)<br>(10.12.0.0/16) |

1. Localizar o TestVNet1 no portal Azure. Na secção de **ligações VPN** da página, clique em **Gateway**.

    ![Sem porta de entrada](./media/vpn-gateway-howto-vnet-vnet-portal-classic/nogateway.png)
2. Na página **Nova Ligação VPN,** selecione **Site-to-Site**.
3. Clique **no site local** para abrir a página do site local e configurar as definições.
4. Na página **do site local,** diga o seu site local. No nosso exemplo, damos o nome de 'VNet4Local' ao site local.
5. Para **o endereço IP gateway VPN,** pode utilizar qualquer endereço IP que pretenda, desde que esteja num formato válido. Normalmente, você usaria o endereço IP externo real para um dispositivo VPN. Mas, para uma configuração clássica de VNet-vNet, utiliza o endereço IP público que é atribuído ao portal para o seu VNet. Dado que ainda não criou o gateway de rede virtual, especifique qualquer endereço IP público válido como espaço reservado.<br>Não deixe este em branco - não é opcional para esta configuração. Num passo posterior, volte a estas definições e configufique-as com os endereços IP de gateway de rede virtual correspondentes assim que o Azure o gere.
6. Para **o Espaço de Endereço do Cliente,** utilize o espaço de endereço do outro VNet. Consulte o seu exemplo de planeamento. Clique **em OK** para guardar as suas definições e volte à página Nova **Ligação VPN.**

    ![local](./media/vpn-gateway-howto-vnet-vnet-portal-classic/localsite.png)

## <a name="step-4---create-the-virtual-network-gateway"></a><a name="gw"></a>Passo 4 - Criar o portal de rede virtual

Cada rede virtual deve ter uma porta de entrada de rede virtual. As vias de gateway de rede virtual e encriptam o tráfego.

1. Na página **Nova Ligação VPN**, selecione a caixa de verificação **Criar gateway de imediato**.
2. Clique **em Sub-rede, tamanho e tipo de encaminhamento**. Na página de **configuração gateway,** clique na **Sub-rede**.
3. O nome da sub-rede gateway é preenchido automaticamente com o nome exigido 'GatewaySubnet'. A **gama Address** contém os endereços IP que são atribuídos aos serviços de gateway VPN. Algumas configurações permitem uma sub-rede de gateway de /29, mas o melhor é usar um /28 ou /27 para acomodar configurações futuras que podem exigir mais endereços IP para os serviços gateway. Nas nossas definições de exemplo, utilizamos 10.11.1.0/27. Ajuste o espaço do endereço e, em seguida, clique **em OK**.
4. Configure o **tamanho gateway**. Esta definição refere-se ao [Gateway SKU](vpn-gateway-about-vpn-gateway-settings.md#gwsku).
5. Configure o **Tipo de Encaminhamento**. O tipo de encaminhamento para esta configuração deve ser **Dinâmico**. Não pode mudar o tipo de encaminhamento mais tarde, a menos que derrube o portal e crie um novo.
6. Clique em **OK**.
7. Na página **New VPN Connection,** clique **em OK** para começar a criar o gateway de rede virtual. Criar um gateway, muitas vezes, pode demorar 45 minutos ou mais, dependendo do SKU de gateway selecionado.

## <a name="step-5---configure-testvnet4-settings"></a><a name="vnet4settings"></a>Passo 5 - Configurar as definições testVNet4

Repita os passos para [criar um site local](#localsite) e crie o portal de rede virtual para configurar [o](#gw) TestVNet4, substituindo os valores quando necessário. Se o fizer como exercício, utilize os [valores exemplo .](#vnetvalues)

## <a name="step-6---update-the-local-sites"></a><a name="updatelocal"></a>Passo 6 - Atualizar os sites locais

Depois de terem sido criados os seus gateways de rede virtuais para ambos os VNets, tem de ajustar os valores de **endereço IP de gateway de gateway VPN** locais.

|Nome VNet|Site conectado|Endereço IP gateway|
|:--- |:--- |:--- |
|TestVNet1|VNet4Local|Endereço IP de gateway VPN para TestVNet4|
|TestVNet4|VNet1Local|Endereço IP de gateway VPN para TestVNet1|

### <a name="part-1---get-the-virtual-network-gateway-public-ip-address"></a>Parte 1 - Obtenha o endereço IP público de gateway de rede virtual

1. Localize a sua rede virtual no portal Azure.
2. Clique para abrir a página **de visão geral** do VNet. Na página, nas **ligações VPN,** pode ver o endereço IP para o seu gateway de rede virtual.

   ![IP público](./media/vpn-gateway-howto-vnet-vnet-portal-classic/publicIP.png)
3. Copie o endereço IP. Vais usá-lo na próxima secção.
4. Repita estes passos para o TestVNet4

### <a name="part-2---modify-the-local-sites"></a>Parte 2 - Modificar os locais

1. Localize a sua rede virtual no portal Azure.
2. Na página **visão geral** do VNet, clique no site local.

   ![Site local criado](./media/vpn-gateway-howto-vnet-vnet-portal-classic/local.png)
3. Na página **De Ligações VPN site-to-site,** clique no nome do site local que pretende modificar.

   ![Abrir site local](./media/vpn-gateway-howto-vnet-vnet-portal-classic/openlocal.png)
4. Clique no **site local** que pretende modificar.

   ![modificar site](./media/vpn-gateway-howto-vnet-vnet-portal-classic/connections.png)
5. Atualize o **endereço IP do gateway VPN** e clique em **OK** para guardar as definições.

   ![gateway IP](./media/vpn-gateway-howto-vnet-vnet-portal-classic/gwupdate.png)
6. Feche as outras páginas.
7. Repita estes passos para o TestVNet4.

## <a name="step-7---retrieve-values-from-the-network-configuration-file"></a><a name="getvalues"></a>Passo 7 - Recuperar os valores do ficheiro de configuração da rede

[!INCLUDE [retrieve values](../../includes/vpn-gateway-values-classic.md)]

## <a name="step-8---create-the-vpn-gateway-connections"></a><a name="createconnections"></a>Passo 8 - Criar as ligações de gateway VPN

Quando todos os passos anteriores estiverem concluídos, pode definir as teclas pré-partilhadas IPsec/IKE e criar a ligação. Este conjunto de passos utiliza o PowerShell. As ligações VNet-to-VNet para o modelo de implementação clássico não podem ser configuradas no portal Azure.

Nos exemplos, note que a chave partilhada é exatamente a mesma. A chave partilhada deve sempre coincidir. Certifique-se de que substitui os valores nestes exemplos pelos nomes exatos dos seus VNets e Sites de Rede Locais.

1. Criar a ligação da TestVNet1 para a TestVNet4.

   ```powershell
   Set-AzureVNetGatewayKey -VNetName 'Group ClassicRG TestVNet1' `
   -LocalNetworkSiteName '17BE5E2C_VNet4Local' -SharedKey A1b2C3D4
   ```
2. Criar a ligação da TestVNet4 para a TestVNet1.

   ```powershell
   Set-AzureVNetGatewayKey -VNetName 'Group ClassicRG TestVNet4' `
   -LocalNetworkSiteName 'F7F7BFC7_VNet1Local' -SharedKey A1b2C3D4
   ```
3. Aguarde a inicialização das ligações. Uma vez que o gateway tenha sido inicializado, o Estado é 'Bem sucedido'.

   ```
   Error          :
   HttpStatusCode : OK
   Id             :
   Status         : Successful
   RequestId      :
   StatusCode     : OK
   ```

## <a name="vnet-to-vnet-considerations-for-classic-vnets"></a><a name="faq"></a>Considerações VNet-to-VNet para VNet clássicos
* As redes virtuais podem estar nas mesmas subscrições ou diferentes.
* As redes virtuais podem estar nas mesmas regiões ou em regiões (localizações) diferentes do Azure.
* Um serviço de nuvem ou um ponto final de equilíbrio de carga não pode abranger redes virtuais, mesmo que estejam conectados entre si.
* Ligar várias redes virtuais não requer nenhum dispositivo VPN.
* O VNet-to-VNet suporta a ligação de redes virtuais Azure. Não suporta a ligação de máquinas virtuais ou serviços em nuvem que não sejam implantados numa rede virtual.
* O VNet-to-VNet requer gateways dinâmicos de encaminhamento. Os gateways de encaminhamento estático azul não são suportados.
* A conectividade de rede virtual pode ser utilizada em simultâneo com VPNs de vários locais. Há um máximo de 10 túneis VPN para uma rede virtual VPN gateway conectando-se a outras redes virtuais, ou sites no local.
* Os espaços de endereços das redes virtuais e sites de rede local no local não se podem sobrepor. A sobreposição de espaços de endereços fará com que a criação de redes virtuais ou o upload de ficheiros de configuração netcfg falhem.
* Os túneis redundantes entre um par de redes virtuais não são suportados.
* Todos os túneis VPN para o VNet, incluindo P2S VPNs, partilham a largura de banda disponível para o gateway VPN, e o mesmo gateway VPN uptime SLA em Azure.
* O tráfego VNet-to-VNet atravessa a espinha dorsal de Azure.

## <a name="next-steps"></a>Passos seguintes
Verifique as suas ligações. Consulte [verificar uma ligação VPN Gateway](vpn-gateway-verify-connection-resource-manager.md).
