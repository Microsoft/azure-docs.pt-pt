---
title: 'Criar uma ligação entre VNets: clássico: Portal Azure'
description: Ligue as redes virtuais Azure em conjunto utilizando o PowerShell e o portal Azure.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/15/2020
ms.author: cherylmc
ms.openlocfilehash: 0d81e0474d898ffee7f128c0bcea61f077c3d758
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92103225"
---
# <a name="configure-a-vnet-to-vnet-connection-classic"></a>Configure uma ligação VNet-to-VNet (clássico)

Este artigo ajuda-o a criar uma ligação de gateway VPN entre redes virtuais. As redes virtuais podem estar nas mesmas regiões ou em regiões diferentes e pertencer às mesmas subscrições ou a subscrições diferentes.

:::image type="content" source="./media/vpn-gateway-howto-vnet-vnet-portal-classic/v2vclassic.png" alt-text="Diagrama mostrando arquitetura clássica VNet-to-VNet":::

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

Os passos deste artigo aplicam-se ao modelo de implantação clássico e ao portal Azure. Também pode criar esta configuração ao utilizar uma ferramenta de implementação diferente ou modelo de implementação ao selecionar uma opção diferente da lista seguinte:

> [!div class="op_single_selector"]
> * [Clássico](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Resource Manager](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [Ligue VNets em diferentes modelos de implementação](vpn-gateway-connect-different-deployment-models-portal.md)
>
>

## <a name="about-vnet-to-vnet-connections"></a>Acerca das ligações VNet a VNet

Ligar uma rede virtual a outra rede virtual (VNet-to-VNet) no modelo clássico de implementação utilizando um gateway VPN é semelhante a ligar uma rede virtual a uma localização no local. Ambos os tipos de conetividade utilizam um gateway de VPN para fornecer um túnel seguro através de IPsec/IKE.

Os VNets que conecta podem ser em diferentes subscrições e diferentes regiões. Pode combinar comunicação VNet com VNet com configurações multi-locais. Este procedimento permite-lhe estabelecer topologias de rede que combinam uma conetividade em vários locais com uma conetividade de rede intervirtual.

:::image type="content" source="./media/vpn-gateway-howto-vnet-vnet-portal-classic/aboutconnections.png" alt-text="Diagrama mostrando ligações":::

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

Utilizamos o portal para a maioria dos passos, mas tem de usar o PowerShell para criar as ligações entre os VNets. Não é possível criar as ligações utilizando o portal Azure porque não há forma de especificar a chave partilhada no portal. [!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

## <a name="planning"></a><a name="planning"></a>Planeamento

É importante decidir os intervalos que vai usar para configurar as suas redes virtuais. Para esta configuração, deve certificar-se de que nenhuma das gamas VNet se sobrepõe entre si, ou com qualquer uma das redes locais a que se ligam.

### <a name="vnets"></a><a name="vnet"></a>VNets

Para este exercício, utilizamos os seguintes valores de exemplo:

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
Intervalo de endereços da sub-rede: 10.41.0.0/24<br>
Grupo de recursos: ClassicRG<br>
Localização: E.U.A. Oeste<br>
GatewaySubnet: 10.41.1.0/27

### <a name="connections"></a><a name="plan"></a>Ligações

A tabela que se segue mostra um exemplo de como irá ligar os seus VNets. Utilize os intervalos apenas como orientação. Anota os intervalos para as suas redes virtuais. Precisa desta informação para mais tarde.

Neste exemplo, o TestVNet1 conecta-se a um site de rede local que cria chamado 'VNet4Local'. As definições de VNet4Local contêm os prefixos de endereço para TestVNet4.
O site local para cada VNet é o outro VNet. Os seguintes valores de exemplo são utilizados para a nossa configuração:

**Exemplo**

| Rede Virtual | Espaço de Endereços | Localização | Liga-se ao site de rede local |
|:--- |:--- |:--- |:--- |
| TestVNet1 |TestVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |E.U.A. Leste |SiteVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |
| TestVNet4 |TestVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |E.U.A. Oeste |SiteVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |

## <a name="create-virtual-networks"></a><a name="vnetvalues"></a>Criar redes virtuais

Neste passo, cria-se duas redes virtuais clássicas, TestVNet1 e TestVNet4. Se estiver a utilizar este artigo como exercício, utilize os [valores](#vnet)do exemplo .

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

## <a name="configure-sites-and-gateways"></a><a name="localsite"></a>Configurar sites e gateways

O Azure utiliza as definições especificadas em cada site de rede local para determinar como encaminhar o tráfego entre as VNets. Cada VNet deve apontar para a rede local respetivo para a que pretende encaminhar o tráfego. Você determina o nome que deseja usar para se referir a cada site de rede local. É melhor usar algo descritivo.

Por exemplo, o TestVNet1 conecta-se a um site de rede local que cria chamado 'VNet4Local'. As definições de VNet4Local contêm os prefixos de endereço para TestVNet4.

Tenha em mente, o site local para cada VNet é o outro VNet.

| Rede Virtual | Espaço de Endereços | Localização | Liga-se ao site de rede local |
|:--- |:--- |:--- |:--- |
| TestVNet1 |TestVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |E.U.A. Leste |SiteVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |
| TestVNet4 |TestVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |E.U.A. Oeste |SiteVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |

### <a name="to-configure-a-site"></a><a name="site"></a>Para configurar um site

O site local refere-se, normalmente, à sua localização no local. Contém o endereço IP do dispositivo VPN para o qual irá criar uma ligação e os intervalos de endereços IP que serão encaminhados através do gateway de VPN para o dispositivo VPN.

1. Na página para o seu VNet, em **Definições,** selecione **ligações site-to-site**.
1. Na página de ligações Site-to-site, selecione **+ Adicionar**.
1. No **Configure uma ligação VPN e** página de gateway, para **tipo de ligação,** deixe **o Site-site** selecionado.

   * **Endereço IP do gateway de VPN:** este é o endereço IP público do dispositivo VPN para a rede no local. Para este exercício, pode colocar um endereço falso porque ainda não tem o endereço IP para o gateway VPN para o outro site. Por exemplo, 5.4.3.2. Mais tarde, uma vez configurado o portal para o outro VNet, pode ajustar este valor.

   * **Espaço de endereço do cliente:** Enuense as gamas de endereços IP que deseja encaminhada para o outro VNet através deste gateway. Pode adicionar vários intervalos de espaço de endereços. Certifique-se de que os intervalos que especifica aqui não se sobrepõem aos intervalos de outras redes às quais a rede virtual se liga ou aos intervalos de endereços da própria rede virtual.
1. Na parte inferior da página, NÃO selecione Review + create. Em vez disso, **selecione Seguinte: Gateway>**.

### <a name="to-configure-a-virtual-network-gateway"></a><a name="sku"></a>Para configurar uma porta de rede virtual

1. Na página **Gateway,** selecione os seguintes valores:

   * **Tamanho:** Esta é a porta de entrada SKU que usa para criar o seu portal de rede virtual. Os gateways de VPN clássicos utilizam os SKUs de gateway antigo (legados). Para obter mais informações sobre os SKU de gateway legados, veja [Trabalhar com SKUs de gateway de rede virtual (antigos)](vpn-gateway-about-skus-legacy.md). Pode selecionar **Standard** para este exercício.

   * **Tipo de encaminhamento:** Selecione o tipo de encaminhamento para o seu gateway. Também é conhecido como o tipo de VPN. É importante selecionar o tipo correto porque não é possível converter o gateway de um tipo para outro. O dispositivo VPN tem de ser compatível com o tipo de encaminhamento que selecionar. Para obter mais informações sobre o Tipo de Encaminhamento, consulte [sobre as definições do gateway VPN](vpn-gateway-about-vpn-gateway-settings.md#vpntype). Poderá ver artigos que fazem referência aos tipos de VPN 'RouteBased' e 'PolicyBased'. 'Dynamic' corresponde a 'RouteBased' e 'Static' corresponde a 'PolicyBased'. Para esta configuração, selecione **Dynamic**.

   * **Sub-rede gateway:** O tamanho da sub-rede de gateway que especifica depende da configuração do gateway VPN que pretende criar. Embora seja possível criar uma sub-rede de gateway tão pequena como /29, recomendamos que utilize /27 ou /28. Desta forma, estará a criar uma sub-rede maior que inclui mais endereços. Utilizar uma sub-rede de gateway maior permite que os endereços IP suficientes suportem possíveis configurações futuras.

1. Selecione **Rever + criar** na parte inferior da página para validar as suas definições. Selecione **Criar** para implementar. Pode levar até 45 minutos para criar um gateway de rede virtual, dependendo do gateway SKU que selecionou.
1. Pode começar a seguir para o próximo passo enquanto este portal está a criar.

### <a name="configure-testvnet4-settings"></a>Configurar definições testVNet4

Repita os passos para [Criar um site e porta de entrada](#localsite) para configurar o TestVNet4, substituindo os valores quando necessário. Se o fizer como exercício, utilize os [valores](#planning)do exemplo .

## <a name="update-local-sites"></a><a name="updatelocal"></a>Atualizar sites locais

Depois de terem sido criados os seus gateways de rede virtuais para ambos os VNets, tem de ajustar as propriedades do site local para **o endereço IP do gateway VPN**.

|Nome VNet|Site conectado|Endereço IP gateway|
|:--- |:--- |:--- |
|TestVNet1|VNet4Local|Endereço IP de gateway VPN para TestVNet4|
|TestVNet4|VNet1Local|Endereço IP de gateway VPN para TestVNet1|

### <a name="part-1---get-the-virtual-network-gateway-public-ip-address"></a>Parte 1 - Obtenha o endereço IP público de gateway de rede virtual

1. Navegue para o seu VNet indo ao **grupo de Recursos** e selecionando a rede virtual.
1. Na página da sua rede virtual, no painel **Essentials** à direita, localize o **endereço IP gateway** e copie para a área de transferência.

### <a name="part-2---modify-the-local-site-properties"></a>Parte 2 - Modificar as propriedades do site local

1. Nas ligações Site-to-site, selecione a ligação. Por exemplo, SiteVNet4.
1. Na página **Propriedades** para a ligação Site-a-Local, selecione **Editar site local**.
1. No campo de **endereço IP de gateway VPN,** cole o endereço IP de gateway VPN copiado na secção anterior.
1. Selecione **OK**.
1. O campo é atualizado no sistema. Também pode utilizar este método para adicionar um endereço IP adicional que pretende encaminhar para este site.

### <a name="part-3---repeat-steps-for-the-other-vnet"></a>Parte 3 - Repetir passos para o outro VNet

Repita os passos para testVNet4.

## <a name="retrieve-configuration-values"></a><a name="getvalues"></a>Recuperar valores de configuração

[!INCLUDE [retrieve values](../../includes/vpn-gateway-values-classic.md)]

## <a name="create-connections"></a><a name="createconnections"></a>Criar ligações

Quando todos os passos anteriores estiverem concluídos, pode definir as teclas pré-partilhadas IPsec/IKE e criar a ligação. Este conjunto de passos utiliza o PowerShell. As ligações VNet-para-VNet para o modelo de implementação clássico não podem ser configuradas no portal Azure porque a chave partilhada não pode ser especificada no portal.

Nos exemplos, note que a chave partilhada é exatamente a mesma. A chave partilhada deve sempre coincidir. Certifique-se de que substitui os valores nestes exemplos pelos nomes exatos dos seus VNets e Sites de Rede Locais.

1. Criar a ligação da TestVNet1 para a TestVNet4. Certifique-se de alterar os valores.

   ```powershell
   Set-AzureVNetGatewayKey -VNetName 'Group ClassicRG TestVNet1' `
   -LocalNetworkSiteName 'value for _VNet4Local' -SharedKey A1b2C3D4
   ```
2. Criar a ligação da TestVNet4 para a TestVNet1.

   ```powershell
   Set-AzureVNetGatewayKey -VNetName 'Group ClassicRG TestVNet4' `
   -LocalNetworkSiteName 'value for _VNet1Local' -SharedKey A1b2C3D4
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

## <a name="faq-and-considerations"></a><a name="faq"></a>PERGUNTAS Frequentes e considerações

Estas considerações aplicam-se a redes virtuais clássicas e gateways de rede virtuais clássicos.

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
