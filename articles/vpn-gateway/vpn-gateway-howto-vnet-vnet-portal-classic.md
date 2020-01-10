---
title: 'Crie uma conexão entre VNets: clássico: portal do Azure'
description: Conecte as redes virtuais do Azure usando o PowerShell e o portal do Azure.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 02/14/2018
ms.author: cherylmc
ms.openlocfilehash: 8ebfe1bc2e578fa85f209f4dd67a00535e619c7e
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/10/2020
ms.locfileid: "75834617"
---
# <a name="configure-a-vnet-to-vnet-connection-classic"></a>Configurar uma conexão VNet a VNet (clássica)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

Este artigo ajuda você a criar uma conexão de gateway de VPN entre redes virtuais. As redes virtuais podem estar nas mesmas regiões ou em regiões diferentes e pertencer às mesmas subscrições ou a subscrições diferentes. As etapas neste artigo se aplicam ao modelo de implantação clássico e ao portal do Azure. Também pode criar esta configuração ao utilizar uma ferramenta de implementação diferente ou modelo de implementação ao selecionar uma opção diferente da lista seguinte:

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

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="about-vnet-to-vnet-connections"></a>Acerca das ligações VNet a VNet

Conectar uma rede virtual a outra rede virtual (VNet a VNet) no modelo de implantação clássico usando um gateway de VPN é semelhante a conectar uma rede virtual a um local do site local. Ambos os tipos de conetividade utilizam um gateway de VPN para fornecer um túnel seguro através de IPsec/IKE.

O VNets que você conecta pode estar em diferentes assinaturas e regiões diferentes. Você pode combinar a comunicação VNet com VNet com configurações multissite. Este procedimento permite-lhe estabelecer topologias de rede que combinam uma conetividade em vários locais com uma conetividade de rede intervirtual.

![Conexões VNet com VNet](./media/vpn-gateway-howto-vnet-vnet-portal-classic/aboutconnections.png)

### <a name="why"></a>Por que motivo ligar redes virtuais?

Poderá pretender ligar redes virtuais pelos seguintes motivos:

* **Geopresença e georredundância entre várias regiões**

  * Pode configurar a sua própria georreplicação ou sincronização com uma conetividade segura sem passar por pontos finais com acesso à Internet.
  * Com a Azure Load Balancer e a tecnologia de clustering da Microsoft ou de terceiros, você pode configurar a carga de trabalho altamente disponível com redundância geográfica em várias regiões do Azure. Um exemplo importante consiste em configurar o SQL Always On com Grupos de Disponibilidade propagando-se em várias regiões do Azure.
* **Aplicativos de várias camadas regionais com limite de isolamento forte**

  * Na mesma região, você pode configurar aplicativos de várias camadas com vários VNets conectados, com isolamento forte e comunicação entre camadas segura.
* **Assinatura cruzada, comunicação entre organizações no Azure**

  * Se você tiver várias assinaturas do Azure, poderá conectar cargas de trabalho de assinaturas diferentes em conjunto com segurança entre redes virtuais.
  * Para empresas ou provedores de serviços, você pode habilitar a comunicação entre organizações com a tecnologia VPN segura no Azure.

Para obter mais informações sobre ligações de VNet a VNet, veja [Considerações de VNet a VNet](#faq) no final deste artigo.

### <a name="before-you-begin"></a>Antes de começar

Antes de iniciar este exercício, baixe e instale a versão mais recente dos cmdlets do PowerShell do gerenciamento de serviços do Azure (SM). Para obter mais informações, veja [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/overview). Usamos o portal para a maioria das etapas, mas você deve usar o PowerShell para criar as conexões entre o VNets. Você não pode criar as conexões usando o portal do Azure.

## <a name="plan"></a>Passo 1 - Planear os intervalos de endereços IP

É importante decidir os intervalos que você usará para configurar suas redes virtuais. Para essa configuração, você deve certificar-se de que nenhum dos seus intervalos de VNet se sobreponham entre si ou com qualquer uma das redes locais às quais eles se conectam.

A tabela a seguir mostra um exemplo de como definir seu VNets. Use os intervalos apenas como uma diretriz. Anote os intervalos para suas redes virtuais. Você precisa dessas informações para etapas posteriores.

**Exemplo**

| Rede Virtual | Espaço de Endereços | Região | Conecta-se ao site de rede local |
|:--- |:--- |:--- |:--- |
| TestVNet1 |TestVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |Este dos E.U.A. |VNet4Local<br>(10.41.0.0/16)<br>(10.42.0.0/16) |
| TestVNet4 |TestVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |Oeste dos E.U.A. |VNet1Local<br>(10.11.0.0/16)<br>(10.12.0.0/16) |

## <a name="vnetvalues"></a>Etapa 2 – criar as redes virtuais

Crie duas redes virtuais na [portal do Azure](https://portal.azure.com). Para obter as etapas para criar redes virtuais clássicas, consulte [criar uma rede virtual clássica](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). 

Ao usar o portal para criar uma rede virtual clássica, você deve navegar até a página de rede virtual usando as etapas a seguir, caso contrário, a opção para criar uma rede virtual clássica não aparecerá:

1. Clique em ' + ' para abrir a página ' novo '.
2. No campo ' Pesquisar no Marketplace ', digite ' rede virtual '. Se, em vez disso, selecione rede-> redes virtuais, você não terá a opção de criar uma VNet clássica.
3. Localize ' rede virtual ' na lista retornada e clique nela para abrir a página rede virtual. 
4. Na página rede virtual, selecione "clássico" para criar uma VNet clássica. 

Se você estiver usando este artigo como um exercício, poderá usar os seguintes valores de exemplo:

**Valores para TestVNet1**

Nome: TestVNet1<br>
Espaço de endereço: 10.11.0.0/16, 10.12.0.0/16 (opcional)<br>
Nome da sub-rede: padrão<br>
Intervalo de endereços da sub-rede: 10.11.0.1/24<br>
Grupo de recursos: ClassicRG<br>
Localização: EUA Leste<br>
GatewaySubnet: 10.11.1.0/27

**Valores para TestVNet4**

Nome: TestVNet4<br>
Espaço de endereço: 10.41.0.0/16, 10.42.0.0/16 (opcional)<br>
Nome da sub-rede: padrão<br>
Intervalo de endereços da sub-rede: 10.41.0.1/24<br>
Grupo de recursos: ClassicRG<br>
Localização: EUA Oeste<br>
GatewaySubnet: 10.41.1.0/27

**Ao criar seu VNets, tenha em mente as seguintes configurações:**

* **Espaços de endereço de rede virtual** – na página espaços de endereço de rede virtual, especifique o intervalo de endereços que você deseja usar para sua rede virtual. Esses são os endereços IP dinâmicos que serão atribuídos às VMs e outras instâncias de função que você implantar nessa rede virtual.<br>Os espaços de endereço selecionados não podem se sobrepor aos espaços de endereço de qualquer um dos outros locais VNets ou local aos quais essa VNet se conectará.

* **Local** – quando você cria uma rede virtual, você a associa a um local do Azure (região). Por exemplo, se você quiser que suas VMs implantadas em sua rede virtual estejam localizadas fisicamente no oeste dos EUA, selecione esse local. Você não pode alterar o local associado à sua rede virtual depois de criá-lo.

**Depois de criar seu VNets, você pode adicionar as seguintes configurações:**

* **Espaço de endereço** – espaço de endereço adicional não é necessário para essa configuração, mas você pode adicionar espaço de endereço adicional depois de criar a VNet.

* **Sub-redes** – sub-redes adicionais não são necessárias para essa configuração, mas talvez você queira ter suas VMs em uma sub-rede separada das outras instâncias de função.

* **Servidores DNS** – Insira o nome do servidor DNS e o endereço IP. Esta definição não cria um servidor DNS. Permite-lhe especificar os servidores DNS que pretende utilizar para a resolução de nomes desta rede virtual.

Nesta seção, você configurará o tipo de conexão, o site local e criará o gateway.

## <a name="localsite"></a>Etapa 3 – configurar o site local

O Azure usa as configurações especificadas em cada site de rede local para determinar como rotear o tráfego entre o VNets. Cada VNet deve apontar para a respectiva rede local para a qual você deseja rotear o tráfego. Você determina o nome que deseja usar para se referir a cada site de rede local. É melhor usar algo descritivo.

Por exemplo, TestVNet1 se conecta a um site de rede local que você cria com o nome ' VNet4Local '. As configurações para VNet4Local contêm os prefixos de endereço para TestVNet4.

O site local para cada VNet é a outra VNet. Os seguintes valores de exemplo são usados para nossa configuração:

| Rede Virtual | Espaço de Endereços | Região | Conecta-se ao site de rede local |
|:--- |:--- |:--- |:--- |
| TestVNet1 |TestVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |Este dos E.U.A. |VNet4Local<br>(10.41.0.0/16)<br>(10.42.0.0/16) |
| TestVNet4 |TestVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |Oeste dos E.U.A. |VNet1Local<br>(10.11.0.0/16)<br>(10.12.0.0/16) |

1. Localize TestVNet1 no portal do Azure. Na seção **conexões VPN** da página, clique em **Gateway**.

    ![Sem gateway](./media/vpn-gateway-howto-vnet-vnet-portal-classic/nogateway.png)
2. Na página **nova conexão VPN** , selecione **site a site**.
3. Clique em **site local** para abrir a página do site local e defina as configurações.
4. Na página **site local** , nomeie seu site local. Em nosso exemplo, nomeamos o site local ' VNet4Local '.
5. Para o **endereço IP do gateway de VPN**, você pode usar qualquer endereço IP desejado, desde que ele esteja em um formato válido. Normalmente, você usaria o endereço IP externo real para um dispositivo VPN. Mas, para uma configuração clássica de VNet para VNet, você usa o endereço IP público que é atribuído ao gateway para sua VNet. Considerando que você ainda não criou o gateway de rede virtual, especifique qualquer endereço IP público válido como um espaço reservado.<br>Não deixe em branco – não é opcional para essa configuração. Em uma etapa posterior, volte para essas configurações e configure-as com os endereços IP de gateway de rede virtual correspondentes depois que o Azure o gerar.
6. Para **espaço de endereço de cliente**, use o espaço de endereço da outra VNet. Consulte seu exemplo de planejamento. Clique em **OK** para salvar as configurações e voltar para a página **nova conexão VPN** .

    ![site local](./media/vpn-gateway-howto-vnet-vnet-portal-classic/localsite.png)

## <a name="gw"></a>Etapa 4 – criar o gateway de rede virtual

Cada rede virtual deve ter um gateway de rede virtual. O gateway de rede virtual roteia e criptografa o tráfego.

1. Na página **Nova Ligação VPN**, selecione a caixa de verificação **Criar gateway de imediato**.
2. Clique em **sub-rede, tamanho e tipo de roteamento**. Na página **configuração do gateway** , clique em **sub-rede**.
3. O nome da sub-rede de gateway é preenchido automaticamente com o nome necessário ' GatewaySubnet '. O **intervalo de endereços** contém os endereços IP que são alocados para os serviços de gateway de VPN. Algumas configurações permitem uma sub-rede de gateway de/29, mas é melhor usar uma/28 ou/27 para acomodar configurações futuras que podem exigir mais endereços IP para os serviços de gateway. Em nossas configurações de exemplo, usamos 10.11.1.0/27. Ajuste o espaço de endereço e clique em **OK**.
4. Configure o **tamanho do gateway**. Essa configuração refere-se ao [SKU do gateway](vpn-gateway-about-vpn-gateway-settings.md#gwsku).
5. Configure o **tipo de roteamento**. O tipo de roteamento para essa configuração deve ser **dinâmico**. Você não pode alterar o tipo de roteamento posteriormente, a menos que você subdividir o gateway e crie um novo.
6. Clique em **OK**.
7. Na página **nova conexão VPN** , clique em **OK** para começar a criar o gateway de rede virtual. Criar um gateway, muitas vezes, pode demorar 45 minutos ou mais, dependendo do SKU de gateway selecionado.

## <a name="vnet4settings"></a>Etapa 5 – definir as configurações de TestVNet4

Repita as etapas para [criar um site local](#localsite) e [criar o gateway de rede virtual](#gw) para configurar o TestVNet4, substituindo os valores quando necessário. Se você estiver fazendo isso como um exercício, use os [valores de exemplo](#vnetvalues).

## <a name="updatelocal"></a>Etapa 6 – atualizar os sites locais

Depois que os gateways de rede virtual tiverem sido criados para ambos os VNets, você deverá ajustar os valores de **endereço IP do gateway de VPN** dos sites locais.

|Nome da VNet|Site conectado|Endereço IP do gateway|
|:--- |:--- |:--- |
|TestVNet1|VNet4Local|Endereço IP do gateway de VPN para TestVNet4|
|TestVNet4|VNet1Local|Endereço IP do gateway de VPN para TestVNet1|

### <a name="part-1---get-the-virtual-network-gateway-public-ip-address"></a>Parte 1-obter o endereço IP público do gateway de rede virtual

1. Localize sua rede virtual no portal do Azure.
2. Clique para abrir a página **visão geral** da VNet. Na página, em **conexões VPN**, você pode exibir o endereço IP do seu gateway de rede virtual.

   ![IP público](./media/vpn-gateway-howto-vnet-vnet-portal-classic/publicIP.png)
3. Copie o endereço IP. Você o usará na próxima seção.
4. Repita essas etapas para TestVNet4

### <a name="part-2---modify-the-local-sites"></a>Parte 2-modificar os sites locais

1. Localize sua rede virtual no portal do Azure.
2. Na página **visão geral** da VNet, clique no site local.

   ![Site local criado](./media/vpn-gateway-howto-vnet-vnet-portal-classic/local.png)
3. Na página **conexões VPN site a site** , clique no nome do site local que você deseja modificar.

   ![Abrir site local](./media/vpn-gateway-howto-vnet-vnet-portal-classic/openlocal.png)
4. Clique no **site local** que você deseja modificar.

   ![modificar site](./media/vpn-gateway-howto-vnet-vnet-portal-classic/connections.png)
5. Atualize o **endereço IP do gateway de VPN** e clique em **OK** para salvar as configurações.

   ![IP do gateway](./media/vpn-gateway-howto-vnet-vnet-portal-classic/gwupdate.png)
6. Feche as outras páginas.
7. Repita essas etapas para TestVNet4.

## <a name="getvalues"></a>Etapa 7 – recuperar valores do arquivo de configuração de rede

Quando você cria VNets clássicas no portal do Azure, o nome que você vê não é o nome completo que você usa para o PowerShell. Por exemplo, uma VNet que parece ser nomeada **TestVNet1** no portal, pode ter um nome muito mais longo no arquivo de configuração de rede. O nome pode ser semelhante a: **Group ClassicRG TestVNet1**. Ao criar suas conexões, é importante usar os valores que você vê no arquivo de configuração de rede.

Nas etapas a seguir, você se conectará à sua conta do Azure e baixará e exibirá o arquivo de configuração de rede para obter os valores necessários para suas conexões.

1. Baixe e instale a versão mais recente dos cmdlets do PowerShell do SM (gerenciamento de serviços do Azure). Para obter mais informações, veja [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/overview).

2. Abra a consola do PowerShell com direitos elevados e ligue-se à sua conta. Utilize o exemplo seguinte para o ajudar na ligação:

   ```powershell
   Connect-AzAccount
   ```

   Verifique as subscrições da conta.

   ```powershell
   Get-AzSubscription
   ```

   Se tiver mais do que uma subscrição, selecione a subscrição que pretende utilizar.

   ```powershell
   Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
   ```

   Em seguida, utilize o cmdlet seguinte para adicionar a sua subscrição do Azure para o PowerShell para o modelo de implementação clássica.

   ```powershell
   Add-AzureAccount
   ```
3. Exporte e exiba o arquivo de configuração de rede. Crie um diretório no seu computador e, em seguida, exporte o ficheiro de configuração de rede para o diretório. Neste exemplo, o arquivo de configuração de rede é exportado para **C:\AzureNet**.

   ```powershell
   Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
   ```
4. Abra o arquivo com um editor de texto e exiba os nomes de seus VNets e sites. Esse será o nome que você usa ao criar suas conexões.<br>Os nomes de VNet são listados como **VirtualNetworkSite Name =**<br>Os nomes de site são listados como **LocalNetworkSiteRef Name =**

## <a name="createconnections"></a>Etapa 8 – criar as conexões de gateway de VPN

Quando todas as etapas anteriores forem concluídas, você poderá definir as chaves pré-compartilhadas IPsec/IKE e criar a conexão. Esse conjunto de etapas usa o PowerShell. As conexões VNet a VNet para o modelo de implantação clássico não podem ser configuradas no portal do Azure.

Nos exemplos, observe que a chave compartilhada é exatamente a mesma. A chave compartilhada deve sempre corresponder. Certifique-se de substituir os valores nesses exemplos pelos nomes exatos para seus sites de rede local e VNets.

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
3. Aguarde até que as conexões sejam inicializadas. Depois que o gateway for inicializado, o status será ' êxito '.

   ```
   Error          :
   HttpStatusCode : OK
   Id             :
   Status         : Successful
   RequestId      :
   StatusCode     : OK
   ```

## <a name="faq"></a>Considerações de VNet para VNet para VNets clássicas
* As redes virtuais podem estar na mesma assinatura ou em assinaturas diferentes.
* As redes virtuais podem estar nas mesmas regiões ou em regiões (localizações) diferentes do Azure.
* Um serviço cloud ou um ponto final de balanceamento de carga não pode abranger várias redes virtuais, mesmo se estiverem ligadas em conjunto.
* A conexão de várias redes virtuais juntas não exige nenhum dispositivo VPN.
* A VNet a VNet dá suporte à conexão de redes virtuais do Azure. Ele não dá suporte à conexão de máquinas virtuais ou serviços de nuvem que não estão implantados em uma rede virtual.
* VNet a VNet requer gateways de roteamento dinâmico. Não há suporte para gateways de roteamento estático do Azure.
* A conectividade de rede virtual pode ser utilizada em simultâneo com VPNs de vários locais. Há um máximo de 10 túneis VPN para um gateway de VPN de rede virtual conectando-se a outras redes virtuais ou a sites locais.
* Os espaços de endereços das redes virtuais e sites de rede local no local não se podem sobrepor. Os espaços de endereço sobrepostos causarão a falha na criação de redes virtuais ou no carregamento de arquivos de configuração do netcfg.
* Os túneis redundantes entre um par de redes virtuais não são suportados.
* Todos os túneis de VPN para a VNet, incluindo VPNs P2S, compartilham a largura de banda disponível para o gateway de VPN e o mesmo SLA de tempo de atividade de gateway de VPN no Azure.
* O tráfego de VNet para VNet viaja pelo backbone do Azure.

## <a name="next-steps"></a>Passos seguintes
Verifique as suas ligações. Consulte [verificar uma conexão de gateway de VPN](vpn-gateway-verify-connection-resource-manager.md).
