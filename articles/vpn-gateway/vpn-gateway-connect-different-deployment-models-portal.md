---
title: 'Ligue redes virtuais clássicas a VNets gestorde recursos do Azure: Portal [ Portal ] Microsoft Docs'
description: Passos para ligar VNets clássicos a VNets gestor de recursos usando VPN Gateway e o portal
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: cherylmc
ms.openlocfilehash: 5e64cb2db2bd16a881334779a1c6f1ef19296da2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77152028"
---
# <a name="connect-virtual-networks-from-different-deployment-models-using-the-portal"></a>Conecte redes virtuais de diferentes modelos de implementação usando o portal

Este artigo mostra-lhe como ligar VNets clássicos a VNets gestorde recursos para permitir que os recursos localizados nos modelos de implementação separados se comuniquem entre si. Os passos deste artigo utilizam principalmente o portal Azure, mas também pode criar esta configuração utilizando o PowerShell selecionando o artigo desta lista.

> [!div class="op_single_selector"]
> * [Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
> 
> 

Ligar um VNet clássico a um VNet gestor de recursos é semelhante a ligar um VNet a uma localização no local no local. Ambos os tipos de conetividade utilizam um gateway de VPN para fornecer um túnel seguro através de IPsec/IKE. Pode criar uma ligação entre VNets que estão em diferentes subscrições e em diferentes regiões. Também pode ligar VNets que já têm ligações a redes no local, desde que o portal com o que foram configurados seja dinâmico ou baseado em rotas. Para obter mais informações sobre ligações de VNet a VNet, consulte [FAQ sobre VNet para VNet](#faq) no final deste artigo. 

Se ainda não tem um portal de rede virtual e não quer criar um, pode considerar em vez disso ligar os seus VNets utilizando o VNet Peering. O VNet peering não utiliza um gateway de VPN. Para obter mais informações, veja [VNet peering](../virtual-network/virtual-network-peering-overview.md).

### <a name="before-you-begin"></a><a name="before"></a>Antes de começar



* Estes passos assumem que ambos os VNets já foram criados. Se está a usar este artigo como exercício e não tem VNets, existem links nos passos para ajudá-lo a criá-los.
* Verifique se as gamas de endereços para os VNets não se sobrepõem entre si, ou sobrepõem-se a qualquer uma das gamas para outras ligações às quais os portões possam estar ligados.
* Instale os mais recentes cmdlets PowerShell tanto para O Gestor de Recursos como para gestão de serviços (clássico). Neste artigo, utilizamos tanto o portal Azure como o PowerShell. A PowerShell é necessária para criar a ligação desde o Clássico VNet até ao Gestor de Recursos VNet. Para obter mais informações, veja [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/overview). 

### <a name="example-settings"></a><a name="values"></a>Definições de exemplo

Pode utilizar estes valores para criar um ambiente de teste ou consultá-los para compreender melhor os exemplos neste artigo.

**VNet clássico**

Nome VNet = ClassicVNet <br>
Espaço de endereço = 10.0.0.0/24 <br>
Nome da sub-rede = Subnet-1 <br>
Intervalo de endereços de sub-rede = 10.0.0.0/27 <br>
Subscrição = a subscrição que pretende utilizar <br>
Grupo de Recursos = ClassicRG <br>
Localização = Oeste DOS EUA <br>
GatewaySubnet = 10.0.0.32/28 <br>
Local site = RMVNetLocal <br>

**Gestor de Recursos VNet**

Nome VNet = RMVNet <br>
Espaço de endereço = 192.168.0.0/16 <br>
Grupo de Recursos = RG1 <br>
Localização = Leste dos EUA <br>
Nome da sub-rede = Subnet-1 <br>
Intervalo de endereços = 192.168.1.0/24 <br>
GatewaySubnet = 192.168.0.0/26 <br>
Nome de gateway de rede virtual = RMGateway <br>
Tipo de gateway = VPN <br>
Tipo VPN = Baseado em Rota <br>
SKU = VpnGw1 <br>
Localização = Leste dos EUA <br>
Rede virtual = RMVNet <br> (associar a porta de entrada VPN a este VNet) Primeira configuração IP = rmgwpip <br> (endereço IP público de gateway) Gateway de rede local = ClassicVNetLocal <br>
Nome de ligação = RMtoClassic

### <a name="connection-overview"></a><a name="connectoverview"></a>Visão geral da ligação

Para esta configuração, cria uma ligação de gateway VPN sobre um túnel IPsec/IKE VPN entre as redes virtuais. Certifique-se de que nenhuma das suas gamas VNet se sobrepõe entre si, ou com qualquer uma das redes locais a que se ligam.

A tabela que se segue mostra um exemplo de VNets e locais definidos:

| Rede Virtual | Espaço de Endereços | Região | Conecta-se ao site da rede local |
|:--- |:--- |:--- |:--- |
| ClassicVNet |(10.0.0.0/24) |E.U.A. Oeste | RMVNetLocal (192.168.0.0/16) |
| RMVNet | (192.168.0.0/16) |E.U.A. Leste |ClassicVNetLocal (10.0.0.0/24) |

## <a name="section-1---configure-the-classic-vnet-settings"></a><a name="classicvnet"></a>Secção 1 - Configure as definições clássicas de VNet

Nesta secção, você cria o clássico VNet, a rede local (site local) e o gateway da rede virtual. As capturas de ecrã são disponibilizadas como exemplos. Certifique-se de substituir os valores por si próprio, ou utilizar os valores [exemplo.](#values)

### <a name="1-create-a-classic-vnet"></a>1. <a name="classicvnet"> </a>Criar uma VNet clássica

Se não tiver um VNet clássico e estiver a executar estes passos como exercício, pode criar um VNet utilizando [este artigo](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) e os valores de definições do [Exemplo](#values) de cima.

Se já tem um VNet com um gateway VPN, verifique se o gateway é Dynamic. Se estiver estático, tem primeiro de eliminar o gateway VPN antes de proceder à [configuração do site local](#local).

1. Abra ao [portal do Azure](https://ms.portal.azure.com) e inicie sessão com a sua conta do Azure.
2. Clique **+ Crie um recurso** para abrir a página 'New'.
3. No campo 'Search the marketplace', escreva 'Rede Virtual'. Se em vez disso, selecione Networking -> Rede Virtual, não terá a opção de criar um VNet clássico.
4. Localize a 'Rede Virtual' a partir da lista devolvida e clique nela para abrir a página da Rede Virtual. 
5. Na página de rede virtual, selecione 'Classic' para criar um VNet clássico. Se tomar o padrão aqui, acabará com um Gestor de Recursos VNet.

### <a name="2-configure-the-local-site"></a>2. <a name="local"> </a>Configure o local

1. Navegue para **todos os recursos** e localize o **ClassicVNet** na lista.
2. Clique em **Gateway** na secção **Definições** do menu e, em seguida, clique no banner para criar um gateway.
  ![Configurar um gateway de VPN](./media/vpn-gateway-connect-different-deployment-models-portal/gatewaygraphic.png "Configurar um gateway de VPN")
3. Na nova página de **Ligação VPN,** para **tipo de Ligação,** selecione **Site-a-site**.
4. Para **o site Local,** clique **em Configurar as definições necessárias**. Isto abre a página do **site Local.**
5. Na página do **site Local,** crie um nome para se referir ao Gestor de Recursos VNet. Por exemplo, 'RMVNetLocal'.
6. Se o gateway VPN para o Gestor de Recursos VNet já tiver um endereço IP público, utilize o valor para o campo de endereçoIP de **gateway VPN.** Se estiver a fazer estes passos como exercício, ou ainda não tiver uma porta de entrada de rede virtual para o seu Gestor de Recursos VNet, pode configurar um endereço IP de espaço reservado. Certifique-se de que o endereço IP do espaço reservado utiliza um formato válido. Posteriormente, substitui o endereço IP do espaço reservado pelo endereço IP público do portal de rede virtual Do Gestor de Recursos.
7. Para o Espaço de Endereço do **Cliente,** utilize os [valores](#connectoverview) para os espaços de endereço IP da rede virtual para o Gestor de Recursos VNet. Esta definição é utilizada para especificar os espaços de endereço saque para a rede virtual do Gestor de Recursos. No exemplo, utilizamos 192.168.0.0/16, o intervalo de endereços para o RMVNet.
8. Clique em **OK** para guardar os valores e volte à página **de Ligação VPN Nova.**

### <a name="3-create-the-virtual-network-gateway"></a><a name="classicgw"></a>3. Criar o portal da rede virtual

1. Na página **new VPN Connection,** selecione o **gateway Create imediatamente** checkbox.
2. Clique em **Configuração do gateway opcional** para abrir a página **Configuração do gateway**.

   ![Página de configuração de gateway aberta](./media/vpn-gateway-connect-different-deployment-models-portal/optionalgatewayconfiguration.png "Página de configuração de gateway aberta")
3. Clique em **Subnet - Configure as definições necessárias** para abrir a página **de sub-rede Adicionar.** O **Nome** já está configurado com o valor exigido: **GatewaySubnet**.
4. A **gama 'Endereço'** refere-se ao intervalo da sub-rede gateway. Embora possa criar uma sub-rede de gateway com uma gama de endereços /29 (3 endereços), recomendamos a criação de uma sub-rede de gateway que contenha mais endereços IP. Isto acomodará futuras configurações que poderão necessitar de endereços IP mais disponíveis. Se possível, utilize /27 ou /28. Se estiver a usar estes passos como exercício, pode consultar os [valores exemplo](#values). Para este exemplo, utilizamos '10.0.0.32/28'. Clique **em OK** para criar a sub-rede gateway.
5. Na página de **configuração gateway,** **Size** refere-se ao gateway SKU. Selecione o gateway SKU para o seu gateway VPN.
6. Verifique se o **Tipo de Encaminhamento** é **Dinâmico**e, em seguida, clique em **OK** para voltar à nova página de **Ligação VPN.**
7. Na página **new VPN Connection,** clique em **OK** para começar a criar o seu gateway VPN. A criação de um gateway de VPN pode demorar até 45 minutos a concluir.

### <a name="4-copy-the-virtual-network-gateway-public-ip-address"></a><a name="ip"></a>4. Copiar o endereço IP público da rede virtual

Após a criação do gateway da rede virtual, pode visualizar o endereço IP gateway. 

1. Navegue para o seu VNet clássico e clique em **Visão Geral**.
2. Clique em **ligações VPN** para abrir a página de ligações VPN. Na página de ligações VPN, pode ver o endereço IP público. Este é o endereço IP público atribuído ao seu portal de rede virtual. Tome nota do endereço IP. Usa-o em etapas posteriores quando trabalha com as definições de configuração de gateway de rede local do Gestor de Recursos. 
3. Pode ver o estado das suas ligações de gateway. Note que o site de rede local que criou está listado como 'Connecting'. O estado mudará depois de ter criado as suas ligações. Pode fechar esta página quando terminar de visualizar o estado.

## <a name="section-2---configure-the-resource-manager-vnet-settings"></a><a name="rmvnet"></a>Secção 2 - Configure as definições vNet do Gestor de Recursos

Nesta secção, cria o portal de rede virtual e o portal de rede local para o seu Gestor de Recursos VNet. As capturas de ecrã são disponibilizadas como exemplos. Certifique-se de substituir os valores por si próprio, ou utilizar os valores [exemplo.](#values)

### <a name="1-create-a-virtual-network"></a>1. Criar uma rede virtual

**Valores de exemplo:**

* Nome VNet = RMVNet <br>
* Espaço de endereço = 192.168.0.0/16 <br>
* Grupo de Recursos = RG1 <br>
* Localização = Leste dos EUA <br>
* Nome da sub-rede = Subnet-1 <br>
* Intervalo de endereços = 192.168.1.0/24 <br>

Se não tiver um VNet gestor de recursos e estiver a executar estes passos como exercício, crie uma rede virtual com os passos em [Criar uma rede virtual](../virtual-network/quick-create-portal.md), utilizando os valores de exemplo.

### <a name="2-create-a-virtual-network-gateway"></a><a name="creategw"></a>2. Criar um portal de rede virtual

Neste passo, vai criar o gateway de rede virtual da VNet. Criar um gateway, muitas vezes, pode demorar 45 minutos ou mais, dependendo do SKU de gateway selecionado.

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

**Valores de exemplo:**

* Nome de gateway de rede virtual = RMGateway <br>
* Tipo de gateway = VPN <br>
* Tipo VPN = Baseado em Rota <br>
* SKU = VpnGw1 <br>
* Localização = Leste dos EUA <br>
* Rede virtual = RMVNet <br>
* GatewaySubnet = 192.168.0.0/26 <br>
* Primeira configuração IP = rmgwpip <br>

[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="3-create-a-local-network-gateway"></a><a name="createlng"></a>3. Criar um portal de rede local

**Valores de exemplo:** Gateway de rede local = ClassicVNetLocal

| Rede Virtual | Espaço de Endereços | Região | Conecta-se ao site da rede local |Endereço IP público gateway|
|:--- |:--- |:--- |:--- |:--- |
| ClassicVNet |(10.0.0.0/24) |E.U.A. Oeste | RMVNetLocal (192.168.0.0/16) |O endereço IP público que é atribuído ao gateway ClassicVNet|
| RMVNet | (192.168.0.0/16) |E.U.A. Leste |ClassicVNetLocal (10.0.0.0/24) |O endereço IP público que é atribuído ao gateway RMVNet.|

O portal de rede local especifica a gama de endereços e o endereço IP público associado ao seu Clássico VNet e ao seu portal de rede virtual. Se estiver a fazer estes passos como exercício, consulte os valores do Exemplo.

[!INCLUDE [vpn-gateway-add-lng-rm-portal](../../includes/vpn-gateway-add-lng-rm-portal-include.md)]

## <a name="section-3---modify-the-classic-vnet-local-site-settings"></a><a name="modifylng"></a>Secção 3 - Modificar as configurações clássicas do site local VNet

Nesta secção, substitui o endereço IP do espaço reservado que utilizou ao especificar as definições do site local, com o endereço IP do Gateway VPN do Gestor de Recursos. Esta secção utiliza os cmdlets clássicos (SM) PowerShell.

1. No portal Azure, navegue para a rede virtual clássica.
2. Na página da sua rede virtual, clique em **Visão Geral**.
3. Na secção de **ligações VPN,** clique no nome do seu site local no gráfico.

   ![Ligações VPN](./media/vpn-gateway-connect-different-deployment-models-portal/vpnconnections.png "Ligações VPN")
4. Na página de **ligações VPN site-to-site,** clique no nome do site.

   ![Nome do site](./media/vpn-gateway-connect-different-deployment-models-portal/sitetosite3.png "Nome do site local")
5. Na página de ligação do seu site local, clique no nome do site local para abrir a página do **site local.**

   ![Local aberto](./media/vpn-gateway-connect-different-deployment-models-portal/openlocal.png "Abrir local")
6. Na página do **site Local,** substitua o endereço IP de **gateway VPN** pelo endereço IP do gateway Do Gestor de Recursos.

   ![Gateway-ip-address](./media/vpn-gateway-connect-different-deployment-models-portal/gwipaddress.png "Endereço IP gateway")
7. Clique **em OK** para atualizar o endereço IP.

## <a name="section-4---create-resource-manager-to-classic-connection"></a><a name="RMtoclassic"></a>Secção 4 - Criar Gestor de Recursos para ligação clássica

Nestes passos, configura a ligação do Gestor de Recursos VNet ao clássico VNet utilizando o portal Azure.

1. Em **Todos os recursos,** localize o portal da rede local. No nosso exemplo, o portal de rede local é **ClassicVNetLocal**.
2. Clique na **Configuração** e verifique se o valor do endereço IP é o gateway VPN para o Clássico VNet. Atualizar, se necessário, clique em **Guardar**. Feche a página.
3. Em **Todos os recursos,** clique na porta de entrada da rede local.
4. Clique em **Ligações** para abrir a página Ligações.
5. Na página **Ligações,** clique **+** para adicionar uma ligação.
6. Na página de **ligação Adicionar,** diga a ligação. Por exemplo, 'RMtoClassic'.
7. **O site-to-site** já está selecionado nesta página.
8. Selecione o portal de rede virtual que pretende associar a este site.
9. Criar uma **chave partilhada.** Esta chave também é usada na ligação que cria si do Clássico VNet ao Gestor de Recursos VNet. Pode gerar a chave ou inventar uma. No nosso exemplo, usamos 'abc123', mas pode (e deve) usar algo mais complexo.
10. Clique **em OK** para criar a ligação.

## <a name="section-5---create-classic-to-resource-manager-connection"></a><a name="classictoRM"></a>Secção 5 - Criar conexão clássica para gestor de recursos

Nestes passos, configura a ligação do clássico VNet ao Gestor de Recursos VNet. Estes passos requerem PowerShell. Não se pode criar esta ligação no portal. Certifique-se de que descarregou e instalou os cmdlets clássicos (SM) e PowerShell (RM) PowerShell.

### <a name="1-connect-to-your-azure-account"></a>1. Ligue-se à sua conta Azure

Abra a consola PowerShell com direitos elevados e faça login na sua conta Azure. Após o início do início, as definições da sua conta são descarregadas para que estejam disponíveis para o Azure PowerShell. O seguinte cmdlet solicita-lhe as credenciais de login para a sua Conta Azure para o modelo de implementação do Gestor de Recursos:

```powershell
Connect-AzAccount
```

Obtenha uma lista das suas subscrições do Azure.

```powershell
Get-AzSubscription
```

Se tiver mais de uma subscrição, especifique a subscrição que pretende utilizar.

```powershell
Select-AzSubscription -SubscriptionName "Name of subscription"
```

Em seguida, faça login para utilizar os cmdlets clássicos powerShell (Gestão de Serviços). Utilize o seguinte comando para adicionar a sua conta Azure para o modelo de implementação clássico:

```powershell
Add-AzureAccount
```

Obtenha uma lista das suas assinaturas. Este passo pode ser necessário ao adicionar os cmdlets de Gestão de Serviço, dependendo da instalação do módulo Azure.

```powershell
Get-AzureSubscription
```

Se tiver mais de uma subscrição, especifique a subscrição que pretende utilizar.

```powershell
Select-AzureSubscription -SubscriptionName "Name of subscription"
```

### <a name="2-view-the-network-configuration-file-values"></a>2. Ver os valores dos ficheiros de configuração da rede

Quando se cria um VNet no portal Azure, o nome completo que o Azure utiliza não é visível no portal Azure. Por exemplo, um VNet que parece ser chamado de 'ClassicVNet' no portal Azure pode ter um nome muito mais longo no ficheiro de configuração da rede. O nome pode parecer algo como: 'Group ClassicRG ClassicVNet'. Nestes passos, você descarrega o ficheiro de configuração da rede e vê os valores.

Crie um diretório no seu computador e, em seguida, exporte o ficheiro de configuração de rede para o diretório. Neste exemplo, o ficheiro de configuração de rede é exportado para C:\AzureNet.

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

Abra o ficheiro com um editor de texto e veja o nome para o seu VNet clássico. Utilize os nomes no ficheiro de configuração da rede ao executar os seus cmdlets PowerShell.

- Os nomes vNet estão listados como **nome VirtualNetworkSite =**
- Os nomes do site estão listados como **nome LocalNetworkSite=**

### <a name="3-create-the-connection"></a>3. Criar a ligação

Detete a chave partilhada e crie a ligação do Clássico VNet ao Gestor de Recursos VNet. Não é possível definir a chave partilhada utilizando o portal. Certifique-se de que executa estes passos enquanto inicia a utilização da versão clássica dos cmdlets PowerShell. Para isso, utilize **add-azureAccount**. Caso contrário, não poderá definir o '-AzureVNetGatewayKey'.

- Neste exemplo, **-VNetName** é o nome do VNet clássico como encontrado no seu ficheiro de configuração de rede. 
- O **-SiteName localé** é o nome especificado para o site local, tal como se encontra no ficheiro de configuração da rede.
- O **-SharedKey** é um valor que gera e especifica. Para este exemplo, usamos *o abc123,* mas pode gerar algo mais complexo. O importante é que o valor que especifica aqui deve ser o mesmo valor que especificou ao criar o seu Gestor de Recursos para uma ligação clássica.

```powershell
Set-AzureVNetGatewayKey -VNetName "Group ClassicRG ClassicVNet" `
-LocalNetworkSiteName "172B9E16_RMVNetLocal" -SharedKey abc123
```

## <a name="section-6---verify-your-connections"></a><a name="verify"></a>Secção 6 - Verifique as suas ligações

Pode verificar as suas ligações utilizando o portal Azure ou powerShell. Ao verificar, poderá ter de esperar um minuto ou dois à medida que a ligação está a ser criada. Quando uma ligação é bem sucedida, o estado de conectividade muda de 'Connecting' para 'Connected'.

### <a name="to-verify-the-connection-from-your-classic-vnet-to-your-resource-manager-vnet"></a>Para verificar a ligação do seu VNet clássico ao seu Gestor de Recursos VNet

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

### <a name="to-verify-the-connection-from-your-resource-manager-vnet-to-your-classic-vnet"></a>Para verificar a ligação do seu Gestor de Recursos VNet ao seu VNet clássico

[!INCLUDE [vpn-gateway-verify-connection-portal-rm](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="vnet-to-vnet-faq"></a><a name="faq"></a>FAQ da ligação VNet a VNet

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]
