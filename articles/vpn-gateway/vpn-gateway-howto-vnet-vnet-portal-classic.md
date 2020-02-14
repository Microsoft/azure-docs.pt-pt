---
title: 'Criar uma ligação entre VNets: clássico: portal Azure'
description: Conecte as redes virtuais Azure em conjunto utilizando o PowerShell e o portal Azure.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 02/12/2020
ms.author: cherylmc
ms.openlocfilehash: 63c6329ad62289cd127902c1438073b28fc8683e
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77201854"
---
# <a name="configure-a-vnet-to-vnet-connection-classic"></a>Configure uma ligação VNet-to-VNet (clássica)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

Este artigo ajuda-o a criar uma ligação de gateway VPN entre redes virtuais. As redes virtuais podem estar nas mesmas regiões ou em regiões diferentes e pertencer às mesmas subscrições ou a subscrições diferentes. Os passos deste artigo aplicam-se ao modelo clássico de implantação e ao portal Azure. Também pode criar esta configuração ao utilizar uma ferramenta de implementação diferente ou modelo de implementação ao selecionar uma opção diferente da lista seguinte:

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

Ligar uma rede virtual a outra rede virtual (VNet-to-VNet) no modelo de implementação clássico utilizando um gateway VPN é semelhante à ligação de uma rede virtual a uma localização no local. Ambos os tipos de conetividade utilizam um gateway de VPN para fornecer um túnel seguro através de IPsec/IKE.

Os VNets que liga podem estar em diferentes subscrições e regiões diferentes. Pode combinar a comunicação VNet com a VNet com configurações multi-locais. Este procedimento permite-lhe estabelecer topologias de rede que combinam uma conetividade em vários locais com uma conetividade de rede intervirtual.

![Ligações VNet a VNet](./media/vpn-gateway-howto-vnet-vnet-portal-classic/aboutconnections.png)

### <a name="why"></a>Por que motivo ligar redes virtuais?

Poderá pretender ligar redes virtuais pelos seguintes motivos:

* **Geopresença e georredundância entre várias regiões**

  * Pode configurar a sua própria georreplicação ou sincronização com uma conetividade segura sem passar por pontos finais com acesso à Internet.
  * Com o Azure Load Balancer e a Microsoft ou tecnologia de clustering de terceiros, pode configurar uma carga de trabalho altamente disponível com geo-redundância em várias regiões do Azure. Um exemplo importante consiste em configurar o SQL Always On com Grupos de Disponibilidade propagando-se em várias regiões do Azure.
* **Aplicações regionais de vários níveis com forte limite de isolamento**

  * Dentro da mesma região, pode configurar aplicações de vários níveis com múltiplos VNets ligados juntamente com um forte isolamento e uma comunicação inter-nível segura.
* **Assinatura cruzada, comunicação inter-organização em Azure**

  * Se tiver várias subscrições do Azure, pode ligar cargas de trabalho de diferentes subscrições entre redes virtuais.
  * Para empresas ou prestadores de serviços, pode permitir a comunicação inter-organização com tecnologia VPN segura dentro do Azure.

Para obter mais informações sobre ligações de VNet a VNet, veja [Considerações de VNet a VNet](#faq) no final deste artigo.

### <a name="powershell"></a>Trabalhar com a Azure PowerShell

Utilizamos o portal para a maior parte dos passos, mas tem de usar o PowerShell para criar as ligações entre os VNets. Não é possível criar as ligações utilizando o portal Azure. [!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

## <a name="plan"></a>Passo 1 - Planear os intervalos de endereços IP

É importante decidir as gamas que vai usar para configurar as suas redes virtuais. Para esta configuração, deve certificar-se de que nenhuma das suas gamas VNet se sobrepõe entre si, ou com qualquer uma das redes locais a que se ligam.

A tabela que se segue mostra um exemplo de como definir os seus VNets. Utilize as gamas apenas como orientação. Escreva os intervalos para as suas redes virtuais. Precisa desta informação para passos posteriores.

**Exemplo**

| Rede Virtual | Espaço de endereço | Região | Conecta-se ao site da rede local |
|:--- |:--- |:--- |:--- |
| TestVNet1 |TestVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |E.U.A. Leste |VNet4Local<br>(10.41.0.0/16)<br>(10.42.0.0/16) |
| TestVNet4 |TestVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |E.U.A. Oeste |VNet1Local<br>(10.11.0.0/16)<br>(10.12.0.0/16) |

## <a name="vnetvalues"></a>Passo 2 - Criar as redes virtuais

Crie duas redes virtuais no [portal Azure.](https://portal.azure.com) Para os passos para criar redes virtuais clássicas, consulte [Criar uma rede virtual clássica.](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) 

Ao utilizar o portal para criar uma rede virtual clássica, tem de navegar para a página da rede virtual utilizando os seguintes passos, caso contrário não aparece a opção de criar uma rede virtual clássica:

1. Clique na página '+' para abrir a página 'New'.
2. No campo 'Search the marketplace', escreva 'Rede Virtual'. Se em vez disso, selecione Networking -> Rede Virtual, não terá a opção de criar um VNet clássico.
3. Localize a 'Rede Virtual' a partir da lista devolvida e clique nela para abrir a página da Rede Virtual. 
4. Na página de rede virtual, selecione 'Classic' para criar um VNet clássico. 

Se estiver a usar este artigo como exercício, pode utilizar os seguintes valores de exemplo:

**Valores para TestVNet1**

Nome: TestVNet1<br>
Espaço de endereço: 10.11.0.0/16, 10.12.0.0/16 (opcional)<br>
Nome da sub-rede: padrão<br>
Intervalo de endereços de sub-rede: 10.11.0.1/24<br>
Grupo de recursos: ClassicRG<br>
Localização: E.U.A. Leste<br>
GatewaySubnet: 10.11.1.0/27

**Valores para TestVNet4**

Nome: TestVNet4<br>
Espaço de endereço: 10.41.0.0/16, 10.42.0.0/16 (opcional)<br>
Nome da sub-rede: padrão<br>
Intervalo de endereços de sub-rede: 10.41.0.1/24<br>
Grupo de recursos: ClassicRG<br>
Localização: E.U.A. Oeste<br>
GatewaySubnet: 10.41.1.0/27

**Ao criar os seus VNets, tenha em mente as seguintes definições:**

* Espaços de **Endereços** de Rede Virtual – Na página Espaços de Endereços de Rede Virtual, especifique a gama de endereços que pretende utilizar para a sua rede virtual. Estes são os endereços IP dinâmicos que serão atribuídos aos VMs e outras instâncias de função que você implementa para esta rede virtual.<br>Os espaços de endereço que selecionar não podem sobrepor-se aos espaços de endereço para qualquer um dos outros VNets ou locais no local a que este VNet se ligará.

* **Localização** – Quando se cria uma rede virtual, associa-se-a a uma localização Azure (região). Por exemplo, se quiser que os seus VMs que estão implantados na sua rede virtual estejam fisicamente localizados nos EUA Ocidentais, selecione essa localização. Não pode alterar a localização associada à sua rede virtual depois de a criar.

**Depois de criar os seus VNets, pode adicionar as seguintes definições:**

* Espaço de **endereços** – Espaço de endereço adicional não é necessário para esta configuração, mas pode adicionar espaço adicional de endereço após a criação do VNet.

* **Subredes** – Não são necessárias subredes adicionais para esta configuração, mas é melhor que os seus VMs se parem com as outras instâncias de função.

* **Servidores DNS** – Introduza o nome do servidor DNS e o endereço IP. Esta definição não cria um servidor DNS. Permite-lhe especificar os servidores DNS que pretende utilizar para a resolução de nomes desta rede virtual.

Nesta secção, configura o tipo de ligação, o site local e cria o portal.

## <a name="localsite"></a>Passo 3 - Configure o site local

O Azure utiliza as definições especificadas em cada site da rede local para determinar como encaminhar o tráfego entre os VNets. Cada VNet deve apontar para a respetiva rede local para a sua desejando encaminhar o tráfego. Determina o nome que pretende utilizar para se referir a cada site da rede local. É melhor usar algo descritivo.

Por exemplo, o TestVNet1 conecta-se a um site de rede local que cria chamado 'VNet4Local'. As definições para VNet4Local contêm os prefixos de endereço para TestVNet4.

O site local para cada VNet é o outro VNet. Os seguintes valores exemplo são usados para a nossa configuração:

| Rede Virtual | Espaço de endereço | Região | Conecta-se ao site da rede local |
|:--- |:--- |:--- |:--- |
| TestVNet1 |TestVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |E.U.A. Leste |VNet4Local<br>(10.41.0.0/16)<br>(10.42.0.0/16) |
| TestVNet4 |TestVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |E.U.A. Oeste |VNet1Local<br>(10.11.0.0/16)<br>(10.12.0.0/16) |

1. Localize o TestVNet1 no portal Azure. Na secção de **ligações VPN** da página, clique em **Gateway**.

    ![Sem porta de entrada](./media/vpn-gateway-howto-vnet-vnet-portal-classic/nogateway.png)
2. Na nova página de **Ligação VPN,** selecione **Site-a-Site**.
3. Clique no **site local** para abrir a página do site local e configurar as definições.
4. Na página do **site Local,** nomeie o seu site local. No nosso exemplo, nomeamos o site local 'VNet4Local'.
5. Para o endereço IP de **gateway VPN,** pode utilizar qualquer endereço IP que pretenda, desde que se desloque num formato válido. Normalmente, utilizaria o endereço IP externo real para um dispositivo VPN. Mas, para uma configuração clássica VNet-to-VNet, você usa o endereço IP público que é atribuído à porta de entrada para o seu VNet. Dado que ainda não criou o portal de rede virtual, especifice qualquer endereço IP público válido como espaço reservado.<br>Não deixe isto em branco- não é opcional para esta configuração. Num passo posterior, volte a estas definições e configure-as com os correspondentes endereços IP de gateway de rede virtual assim que o Azure o gere.
6. Para o Espaço de Endereço do **Cliente,** utilize o espaço de endereço do outro VNet. Consulte o seu exemplo de planeamento. Clique **em OK** para guardar as suas definições e volte à página de **Ligação VPN Nova.**

    ![local](./media/vpn-gateway-howto-vnet-vnet-portal-classic/localsite.png)

## <a name="gw"></a>Passo 4 - Criar o portal da rede virtual

Cada rede virtual deve ter um portal de rede virtual. As rotas de gateway da rede virtual e encriptam o tráfego.

1. Na página **Nova Ligação VPN**, selecione a caixa de verificação **Criar gateway de imediato**.
2. Clique em **Subnet, tamanho e tipo de encaminhamento**. Na página de **configuração gateway,** clique em **Subnet**.
3. O nome da sub-rede gateway é preenchido automaticamente com o nome exigido 'GatewaySubnet'. A **gama De endereços** contém os endereços IP que são atribuídos aos serviços de gateway VPN. Algumas configurações permitem uma sub-rede de gateway de /29, mas é melhor usar uma /28 ou /27 para acomodar configurações futuras que podem exigir mais endereços IP para os serviços de gateway. Nas nossas definições de exemplo, utilizamos 10.11.1.1.0/27. Ajuste o espaço de endereço e, em seguida, clique **OK**.
4. Configure o tamanho do **gateway**. Esta definição refere-se ao [Gateway SKU](vpn-gateway-about-vpn-gateway-settings.md#gwsku).
5. Configure o **tipo de encaminhamento**. O tipo de encaminhamento para esta configuração deve ser **Dinâmico**. Não pode mudar o tipo de encaminhamento mais tarde, a menos que derrube a porta de entrada e crie um novo.
6. Clique em **OK**.
7. Na página **new VPN Connection,** clique em **OK** para começar a criar o gateway de rede virtual. Criar um gateway, muitas vezes, pode demorar 45 minutos ou mais, dependendo do SKU de gateway selecionado.

## <a name="vnet4settings"></a>Passo 5 - Configurar as definições testVNet4

Repita os passos para [criar um site local](#localsite) e criar o portal de rede [virtual](#gw) para configurar o TestVNet4, substituindo os valores quando necessário. Se estiver a fazer isto como exercício, utilize os [valores exemplo.](#vnetvalues)

## <a name="updatelocal"></a>Passo 6 - Atualizar os sites locais

Depois de terem sido criados os seus gateways de rede virtual para ambos os VNets, deve ajustar os valores de endereço IP de **gateway VPN** dos sites locais.

|Nome VNet|Site conectado|Endereço IP gateway|
|:--- |:--- |:--- |
|TestVNet1|VNet4Local|Endereço IP de gateway VPN para TestVNet4|
|TestVNet4|VNet1Local|Endereço IP de gateway VPN para TestVNet1|

### <a name="part-1---get-the-virtual-network-gateway-public-ip-address"></a>Parte 1 - Obtenha o endereço IP público de gateway da rede virtual

1. Localize a sua rede virtual no portal Azure.
2. Clique para abrir a página **de visão geral** da VNet. Na página, em **ligações VPN,** pode visualizar o endereço IP para o seu portal de rede virtual.

   ![IP público](./media/vpn-gateway-howto-vnet-vnet-portal-classic/publicIP.png)
3. Copie o endereço IP. Vai usá-lo na próxima secção.
4. Repita estes passos para TestVNet4

### <a name="part-2---modify-the-local-sites"></a>Parte 2 - Modificar os locais

1. Localize a sua rede virtual no portal Azure.
2. Na página de **Visão Geral** da VNet, clique no site local.

   ![Site local criado](./media/vpn-gateway-howto-vnet-vnet-portal-classic/local.png)
3. Na página de **Ligações VPN site-to-site,** clique no nome do site local que pretende modificar.

   ![Abrir local](./media/vpn-gateway-howto-vnet-vnet-portal-classic/openlocal.png)
4. Clique no **site Local** que pretende modificar.

   ![modificar site](./media/vpn-gateway-howto-vnet-vnet-portal-classic/connections.png)
5. Atualize o endereço IP do **gateway VPN** e clique em **OK** para guardar as definições.

   ![gateway IP](./media/vpn-gateway-howto-vnet-vnet-portal-classic/gwupdate.png)
6. Feche as outras páginas.
7. Repita estes passos para testVNet4.

## <a name="getvalues"></a>Passo 7 - Recuperar valores do ficheiro de configuração da rede

Quando cria VNets clássicos no portal Azure, o nome que vê não é o nome completo que utiliza para o PowerShell. Por exemplo, um VNet que parece ser chamado **TestVNet1** no portal, pode ter um nome muito mais longo no ficheiro de configuração da rede. O nome pode parecer algo como: **Grupo ClassicRG TestVNet1**. Quando cria as suas ligações, é importante utilizar os valores que vê no ficheiro de configuração da rede.

Nos seguintes passos, irá ligar-se à sua conta Azure e descarregar e ver o ficheiro de configuração da rede para obter os valores necessários para as suas ligações.

1. Descarregue e instale a versão mais recente dos cmdlets PowerShell (Direção de Serviços Azure) (SM). Para mais informações, consulte [Trabalhar com a Azure PowerShell](#powershell).

2. Abra a consola PowerShell com direitos elevados. Utilize os seguintes exemplos para o ajudar a ligar. Tem de executar estes comandos localmente utilizando o módulo de gestão de serviços PowerShell. Para mudar para a gestão do serviço, utilize este comando:

   ```powershell
   azure config mode asm
   ```
3. Ligar à sua conta. Utilize o exemplo seguinte para o ajudar na ligação:

   ```powershell
   Add-AzureAccount
   ```
4. Verifique as subscrições da conta.

   ```powershell
   Get-AzureSubscription
   ```
5. Se tiver mais do que uma subscrição, selecione a subscrição que pretende utilizar.

   ```powershell
   Select-AzureSubscription -SubscriptionId "Replace_with_your_subscription_ID"
   ```
6. Exportar e ver o ficheiro de configuração da rede. Crie um diretório no seu computador e, em seguida, exporte o ficheiro de configuração de rede para o diretório. Neste exemplo, o ficheiro de configuração da rede é exportado para **C:\AzureNet**.

   ```powershell
   Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
   ```
7. Abra o ficheiro com um editor de texto e veja os nomes dos seus VNets e sites. Estes nomes serão os nomes que usarquando criar as suas ligações.<br>Os nomes vNet estão listados como **nome VirtualNetworkSite =**<br>Os nomes do site estão listados como **nome LocalNetworkSiteRef =**

## <a name="createconnections"></a>Passo 8 - Criar as ligações de gateway VPN

Quando todos os passos anteriores estiverem concluídos, pode definir as teclas pré-partilhadas IPsec/IKE e criar a ligação. Este conjunto de passos utiliza powerShell. As ligações VNet-to-VNet para o modelo de implementação clássico não podem ser configuradas no portal Azure.

Nos exemplos, note que a chave partilhada é exatamente a mesma. A chave partilhada deve corresponder sempre. Certifique-se de substituir os valores nestes exemplos pelos nomes exatos para os seus VNets e Sites de Rede Locais.

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
3. Aguarde que as ligações ininicializem. Uma vez inicializada a porta de entrada, o Estado é "Bem sucedido".

   ```
   Error          :
   HttpStatusCode : OK
   Id             :
   Status         : Successful
   RequestId      :
   StatusCode     : OK
   ```

## <a name="faq"></a>Considerações VNet-to-VNet para VNets clássicos
* As redes virtuais podem estar nas mesmas ou diferentes subscrições.
* As redes virtuais podem estar nas mesmas regiões ou em regiões (localizações) diferentes do Azure.
* Um serviço de nuvem ou um ponto final de equilíbrio de carga não pode abranger redes virtuais, mesmo que estejam ligadas entre si.
* Ligar várias redes virtuais não requer nenhum dispositivo VPN.
* A VNet-to-VNet suporta a ligação das Redes Virtuais Azure. Não suporta a ligação de máquinas virtuais ou serviços na nuvem que não sejam implantados numa rede virtual.
* VNet-to-VNet requer gateways de encaminhamento dinâmicos. Os portões de encaminhamento estático azul não são suportados.
* A conectividade de rede virtual pode ser utilizada em simultâneo com VPNs de vários locais. Existe um máximo de 10 túneis VPN para uma rede virtual vpN gateway conectando-se a outras redes virtuais, ou sites no local.
* Os espaços de endereços das redes virtuais e sites de rede local no local não se podem sobrepor. A sobreposição de espaços de endereços fará com que a criação de redes virtuais ou o upload de ficheiros de configuração netcfg falhem.
* Os túneis redundantes entre um par de redes virtuais não são suportados.
* Todos os túneis VPN para o VNet, incluindo VPNs P2S, partilham a largura de banda disponível para o gateway VPN, e o mesmo Gateway VPN uptime SLA em Azure.
* O tráfego VNet-to-VNet atravessa a espinha dorsal azure.

## <a name="next-steps"></a>Passos seguintes
Verifique as suas ligações. Ver [Verificar uma ligação VPN Gateway](vpn-gateway-verify-connection-resource-manager.md).
