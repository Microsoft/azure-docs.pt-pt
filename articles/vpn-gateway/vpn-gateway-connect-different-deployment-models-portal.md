---
title: 'Ligue redes virtuais clássicas a VNets Azure Resource Manager: Portal Microsoft Docs'
description: Passos para ligar VNets clássicos a VNets gestor de recursos usando o Gateway VPN e o portal
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/24/2019
ms.author: cherylmc
ms.openlocfilehash: 2bcd919629eb03581c35a2090d53e451141d94a4
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92487108"
---
# <a name="connect-virtual-networks-from-different-deployment-models-using-the-portal"></a>Conecte redes virtuais de diferentes modelos de implementação usando o portal

Este artigo mostra-lhe como ligar VNets clássicos a VNets resource manager para permitir que os recursos localizados nos modelos de implementação separados se comuniquem entre si. Os passos deste artigo utilizam principalmente o portal Azure, mas também pode criar esta configuração utilizando o PowerShell selecionando o artigo desta lista.

> [!div class="op_single_selector"]
> * [Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
> 
> 

Ligar um VNet clássico a um VNet do Gestor de Recursos é semelhante à ligação de um VNet a uma localização no local. Ambos os tipos de conetividade utilizam um gateway de VPN para fornecer um túnel seguro através de IPsec/IKE. Pode criar uma ligação entre VNets que estão em diferentes subscrições e em diferentes regiões. Também pode ligar VNets que já têm ligações a redes no local, desde que o gateway com o qual tenham sido configurados seja dinâmico ou baseado em rotas. Para obter mais informações sobre ligações de VNet a VNet, consulte [FAQ sobre VNet para VNet](#faq) no final deste artigo. 

Se ainda não tiver um gateway de rede virtual e não quiser criar um, talvez queira considerar ligar os seus VNets utilizando o VNet Peering. O VNet peering não utiliza um gateway de VPN. Para obter mais informações, veja [VNet peering](../virtual-network/virtual-network-peering-overview.md).

### <a name="before-you-begin"></a><a name="before"></a>Antes de começar



* Estes passos pressupõem que ambos os VNets já foram criados. Se estiver a usar este artigo como exercício e não tiver VNets, existem links nos passos para o ajudar a criá-los.
* Verifique se os intervalos de endereços dos VNets não se sobrepõem entre si, nem se sobrepõe a qualquer uma das gamas para outras ligações a que os gateways possam estar ligados.
* Instale os mais recentes cmdlets PowerShell para gestor de recursos e gestão de serviços (clássico). Neste artigo, usamos o portal Azure e o PowerShell. O PowerShell é necessário para criar a ligação do VNet clássico ao VNet do Gestor de Recursos. Para obter mais informações, veja [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/). 

### <a name="example-settings"></a><a name="values"></a>Definições de exemplo

Pode utilizar estes valores para criar um ambiente de teste ou consultá-los para compreender melhor os exemplos neste artigo.

**VNet clássico**

Nome VNet = ClassicVNet <br>
Espaço do endereço = 10.0.0.0/24 <br>
Nome da sub-rede = Sub-rede-1 <br>
Intervalo de endereço de sub-rede = 10.0.0.0/27 <br>
Assinatura = a subscrição que pretende utilizar <br>
Grupo de Recursos = ClassicRG <br>
Localização = Eua Ocidental <br>
GatewaySubnet = 10.0.0.32/28 <br>
Local site = RMVNetLocal <br>

**Gestor de Recursos VNet**

Nome VNet = RMVNet <br>
Espaço de endereço = 192.168.0.0/16 <br>
Grupo de Recursos = RG1 <br>
Localização = Leste dos EUA <br>
Nome da sub-rede = Sub-rede-1 <br>
Intervalo de endereços = 192.168.1.0/24 <br>
GatewaySubnet = 192.168.0.0/26 <br>
Nome de gateway de rede virtual = RMGateway <br>
Tipo de gateway = VPN <br>
Tipo VPN = Baseado em rota <br>
SKU = VpnGw1 <br>
Localização = Leste dos EUA <br>
Rede virtual = RMVNet <br> (associar a porta de entrada VPN a este VNet) Primeira configuração IP = rmgwpip <br> (endereço IP público gateway) Gateway de rede local = ClassicVNetLocal <br>
Nome de conexão = RMtoClassic

### <a name="connection-overview"></a><a name="connectoverview"></a>Visão geral da ligação

Para esta configuração, cria uma ligação de gateway VPN sobre um túnel VPN IPsec/IKE entre as redes virtuais. Certifique-se de que nenhuma das suas gamas VNet se sobrepõe entre si, ou com qualquer uma das redes locais a que se ligam.

A tabela a seguir mostra um exemplo de como os VNets e locais locais são definidos:

| Rede Virtual | Espaço de Endereços | Region | Liga-se ao site de rede local |
|:--- |:--- |:--- |:--- |
| ClassicVNet |(10.0.0.0/24) |E.U.A. Oeste | RMVNetLocal (192.168.0.0/16) |
| RMVNet | (192.168.0.0/16) |E.U.A Leste |ClassicVNetLocal (10.0.0.0/24) |

## <a name="section-1---configure-the-classic-vnet-settings"></a><a name="classicvnet"></a>Secção 1 - Configurar as definições clássicas do VNet

Nesta secção, você cria o VNet clássico, a rede local (site local) e o gateway de rede virtual. As capturas de ecrã são disponibilizadas como exemplos. Certifique-se de que substitui os valores pelos seus ou utilize os valores [exemplo.](#values)

### <a name="1-create-a-classic-vnet"></a>1. <a name="classicvnet"></a> Criar um VNet clássico

Se não tiver um VNet clássico e estiver a executar estes passos como exercício, pode criar um VNet utilizando [este artigo](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) e os valores de definições de [Exemplo](#values) de cima.

Se já tem um VNet com uma porta VPN, verifique se o gateway é Dynamic. Se for Estática, deve primeiro apagar o gateway VPN antes de proceder à [configuração do site local.](#local)

1. Abra ao [portal do Azure](https://ms.portal.azure.com) e inicie sessão com a sua conta do Azure.
2. Clique **+ Crie um recurso** para abrir a página 'Novo'.
3. No campo 'Pesquisar o mercado', escreva 'Rede Virtual'. Se, em vez disso, selecionar Networking -> Virtual Network, não terá a opção de criar um VNet clássico.
4. Localizar 'Rede Virtual' a partir da lista de devoluções e clicar nela para abrir a página 'Rede Virtual'. 
5. Na página de rede virtual, selecione 'Classic' para criar um VNet clássico. Se tomar o padrão aqui, acabará com um VNet gestor de recursos.

### <a name="2-configure-the-local-site"></a>2. <a name="local"></a> Configurar o site local

1. Navegue para **todos os recursos** e localize o **ClassicVNet** na lista.
2. Clique em **Gateway** na secção **Definições** do menu e, em seguida, clique no banner para criar um gateway.
  ![Configurar um gateway de VPN](./media/vpn-gateway-connect-different-deployment-models-portal/gatewaygraphic.png "Configurar um gateway de VPN")
3. Na página **Nova Ligação VPN,** para **tipo de Ligação,** selecione **Site-to-site**.
4. Para **o site local,** clique em **Configurar as definições necessárias**. Isto abre a página **do site Local.**
5. Na página do **site Local,** crie um nome para se referir ao VNet do Gestor de Recursos. Por exemplo, 'RMVNetLocal'.
6. Se o gateway VPN para o Gestor de Recursos VNet já tiver um endereço IP público, utilize o valor para o campo **de endereço IP gateway VPN.** Se estiver a fazer estes passos como exercício, ou ainda não tiver uma porta de entrada de rede virtual para o seu VNet gestor de recursos, pode criar um endereço IP reservado. Certifique-se de que o endereço IP do espaço reservado utiliza um formato válido. Posteriormente, substitui o endereço IP do espaço reservado pelo endereço IP público do gateway de rede virtual Do Gestor de Recursos.
7. Para **o Espaço de Endereço do Cliente,** utilize os [valores](#connectoverview) para os espaços de endereço IP de rede virtual para o VNet do Gestor de Recursos. Esta definição é utilizada para especificar os espaços de endereço para encaminhar para a rede virtual do Gestor de Recursos. No exemplo, usamos 192.168.0.0/16, o intervalo de endereços para o RMVNet.
8. Clique **em OK** para guardar os valores e volte à página Nova **Ligação VPN.**

### <a name="3-create-the-virtual-network-gateway"></a><a name="classicgw"></a>3. Criar o portal de rede virtual

1. Na página **Nova Ligação VPN,** selecione a caixa de verificação **'Criar gateway'.**
2. Clique em **Configuração do gateway opcional** para abrir a página **Configuração do gateway**.

   ![Página de configuração de gateway aberto](./media/vpn-gateway-connect-different-deployment-models-portal/optionalgatewayconfiguration.png "Página de configuração de gateway aberto")
3. Clique **na Sub-rede - Configurar as definições necessárias** para abrir a página **de sub-rede Adicionar.** O **Nome** já está configurado com o valor exigido: **GatewaySubnet**.
4. O **intervalo Address** refere-se ao intervalo para a sub-rede gateway. Embora possa criar uma sub-rede gateway com uma gama de endereços /29 (3 endereços), recomendamos a criação de uma sub-rede de gateway que contenha mais endereços IP. Isto irá acomodar configurações futuras que podem requerer mais endereços IP disponíveis. Se possível, utilize /27 ou /28. Se estiver a utilizar estes passos como exercício, pode consultar os [valores exemplo .](#values) Para este exemplo, utilizamos '10.0.0.32/28'. Clique **em OK** para criar a sub-rede gateway.
5. Na página de **configuração gateway,** **Size** refere-se ao gateway SKU. Selecione o gateway SKU para o seu gateway VPN.
6. Verifique se o **Tipo de Encaminhamento** é **Dinâmico**e, em seguida, clique em **OK** para voltar à página **de Ligação Nova VPN.**
7. Na página **New VPN Connection,** clique **em OK** para começar a criar o seu gateway VPN. A criação de um gateway de VPN pode demorar até 45 minutos a concluir.

### <a name="4-copy-the-virtual-network-gateway-public-ip-address"></a><a name="ip"></a>4. Copiar o endereço IP público da rede virtual

Depois de ter sido criado o gateway de rede virtual, pode ver o endereço IP gateway. 

1. Navegue para o seu VNet clássico e clique em **Overview**.
2. Clique **nas ligações VPN** para abrir a página de ligações VPN. Na página de ligações VPN, pode ver o endereço IP público. Este é o endereço IP público atribuído ao seu gateway de rede virtual. Tome nota do endereço IP. Utilize-o em etapas posteriores quando trabalhar com as definições de configuração de gateway de rede local do Gestor de Recursos. 
3. Pode ver o estado das suas ligações de gateway. Note que o site de rede local que criou está listado como 'Connecting'. O estado mudará depois de ter criado as suas ligações. Pode fechar esta página quando terminar de ver o estado.

## <a name="section-2---configure-the-resource-manager-vnet-settings"></a><a name="rmvnet"></a>Secção 2 - Configurar as definições VNet do Gestor de Recursos

Nesta secção, cria-se o gateway de rede virtual e a porta de entrada de rede local para o seu Gestor de Recursos VNet. As capturas de ecrã são disponibilizadas como exemplos. Certifique-se de que substitui os valores pelos seus ou utilize os valores [exemplo.](#values)

### <a name="1-create-a-virtual-network"></a>1. Criar uma rede virtual

**Valores de exemplo:**

* Nome VNet = RMVNet <br>
* Espaço de endereço = 192.168.0.0/16 <br>
* Grupo de Recursos = RG1 <br>
* Localização = Leste dos EUA <br>
* Nome da sub-rede = Sub-rede-1 <br>
* Intervalo de endereços = 192.168.1.0/24 <br>

Se não tiver um VNet gestor de recursos e estiver a executar estes passos como exercício, crie uma rede virtual com os passos na [Criar uma rede virtual,](../virtual-network/quick-create-portal.md)utilizando os valores de exemplo.

### <a name="2-create-a-virtual-network-gateway"></a><a name="creategw"></a>2. Criar um portal de rede virtual

Neste passo, vai criar o gateway de rede virtual da VNet. Criar um gateway, muitas vezes, pode demorar 45 minutos ou mais, dependendo do SKU de gateway selecionado.

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

**Valores de exemplo:**

* Nome de gateway de rede virtual = RMGateway <br>
* Tipo de gateway = VPN <br>
* Tipo VPN = Baseado em rota <br>
* SKU = VpnGw1 <br>
* Localização = Leste dos EUA <br>
* Rede virtual = RMVNet <br>
* GatewaySubnet = 192.168.0.0/26 <br>
* Primeira configuração IP = rmgwpip <br>

[!INCLUDE [Add gateway](../../includes/vpn-gateway-add-gw-rm-portal-empty.md)]

[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="3-create-a-local-network-gateway"></a><a name="createlng"></a>3. Criar uma porta de entrada de rede local

**Valores de exemplo:** Gateway de rede local = ClassicVNetLocal

| Rede Virtual | Espaço de Endereços | Region | Liga-se ao site de rede local |Endereço IP público gateway|
|:--- |:--- |:--- |:--- |:--- |
| ClassicVNet |(10.0.0.0/24) |E.U.A. Oeste | RMVNetLocal (192.168.0.0/16) |O endereço IP público que é atribuído ao gateway ClassicVNet|
| RMVNet | (192.168.0.0/16) |E.U.A Leste |ClassicVNetLocal (10.0.0.0/24) |O endereço IP público que é atribuído ao gateway RMVNet.|

O gateway de rede local especifica a gama de endereços e o endereço IP público associado ao seu VNet clássico e ao seu gateway de rede virtual. Se estiver a fazer estes passos como exercício, consulte os valores exemplo.

[!INCLUDE [Add local network gateway](../../includes/vpn-gateway-add-local-network-gateway-portal-ip-empty.md)]

## <a name="section-3---modify-the-classic-vnet-local-site-settings"></a><a name="modifylng"></a>Secção 3 - Modificar as configurações clássicas do site local da VNet

Nesta secção, substitui o endereço IP do espaço reservado que utilizou ao especificar as definições do site local, com o endereço IP de gateway VPN do Gestor de Recursos. Esta secção utiliza os cmdlets powershell clássicos (SM).

1. No portal Azure, navegue para a rede virtual clássica.
2. Na página da sua rede virtual, clique **em 'Vista Geral'.**
3. Na secção **de ligações VPN,** clique no nome do seu site local no gráfico.

   ![Ligações VPN](./media/vpn-gateway-connect-different-deployment-models-portal/vpnconnections.png "Ligações VPN")
4. Na página **de ligações VPN site-to-site,** clique no nome do site.

   ![Nome do site](./media/vpn-gateway-connect-different-deployment-models-portal/sitetosite3.png "Nome do site local")
5. Na página de ligação para o site local, clique no nome do site local para abrir a página **do site Local.**

   ![Site aberto local](./media/vpn-gateway-connect-different-deployment-models-portal/openlocal.png "Abrir site local")
6. Na página do **site Local,** substitua o **endereço IP de gateway VPN** pelo endereço IP do gateway Do Gestor de Recursos.

   ![Endereço gateway-ip](./media/vpn-gateway-connect-different-deployment-models-portal/gwipaddress.png "Endereço IP gateway")
7. Clique **em OK** para atualizar o endereço IP.

## <a name="section-4---create-resource-manager-to-classic-connection"></a><a name="RMtoclassic"></a>Secção 4 - Criar Gestor de Recursos para a ligação clássica

Nestes passos, configura a ligação do Gestor de Recursos VNet ao VNet clássico utilizando o portal Azure.

1. Em **todos os recursos,** localize a porta de entrada da rede local. No nosso exemplo, o portal de rede local é **ClassicVNetLocal.**
2. Clique em **Configuração** e verifique se o valor do endereço IP é o gateway VPN para o VNet clássico. Atualizar, se necessário, clique em **Guardar**. Feche a página.
3. Em **Todos os recursos,** clique na porta de entrada da rede local.
4. Clique **em Conexões** para abrir a página 'Ligações'.
5. Na página **'Ligações',** clique **+** para adicionar uma ligação.
6. Na página **de ligação Adicionar,** nomeie a ligação. Por exemplo, 'RMtoClassic'.
7. **Site-to-Site** já está selecionado nesta página.
8. Selecione o gateway de rede virtual que pretende associar a este site.
9. Crie uma **chave partilhada.** Esta chave também é utilizada na ligação que cria do VNet clássico ao VNet Gestor de Recursos. Pode gerar a chave ou inventar uma. No nosso exemplo, usamos 'abc123', mas pode (e deve) usar algo mais complexo.
10. Clique **em OK** para criar a ligação.

## <a name="section-5---create-classic-to-resource-manager-connection"></a><a name="classictoRM"></a>Secção 5 - Criar ligação clássica ao Gestor de Recursos

Nestes passos, configura a ligação do VNet clássico ao VNet Gestor de Recursos. Estes passos requerem PowerShell. Não pode criar esta ligação no portal. Certifique-se de que descarregou e instalou os cmdletes powershell clássicos (SM) e Resource Manager (RM).

### <a name="1-connect-to-your-azure-account"></a>1. Ligue-se à sua conta Azure

Abra a consola PowerShell com direitos elevados e faça login na sua conta Azure. Depois de iniciar sessão, as definições da sua conta são descarregadas para que estejam disponíveis para o Azure PowerShell. O cmdlet seguinte solicita-lhe as credenciais de login para a sua Conta Azure para o modelo de implementação do Gestor de Recursos:

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

Obtenha uma lista das suas assinaturas. Este passo poderá ser necessário ao adicionar os cmdlets de Gestão de Serviços, dependendo da instalação do módulo Azure.

```powershell
Get-AzureSubscription
```

Se tiver mais de uma subscrição, especifique a subscrição que pretende utilizar.

```powershell
Select-AzureSubscription -SubscriptionName "Name of subscription"
```

### <a name="2-view-the-network-configuration-file-values"></a>2. Ver os valores do ficheiro de configuração da rede

Quando cria um VNet no portal Azure, o nome completo que o Azure utiliza não é visível no portal Azure. Por exemplo, um VNet que parece ser chamado de 'ClassicVNet' no portal Azure pode ter um nome muito mais longo no ficheiro de configuração da rede. O nome pode parecer algo como: 'Group ClassicRG ClassicVNet'. Nestes passos, descarrega o ficheiro de configuração da rede e vê os valores.

Crie um diretório no seu computador e, em seguida, exporte o ficheiro de configuração de rede para o diretório. Neste exemplo, o ficheiro de configuração de rede é exportado para C:\AzureNet.

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

Abra o ficheiro com um editor de texto e veja o nome do seu VNet clássico. Utilize os nomes no ficheiro de configuração da rede quando executar os cmdlets PowerShell.

- Os nomes VNet estão listados como **Nome VirtualNetworkSite =**
- Os nomes do site estão listados como **nome LocalNetworkSite=**

### <a name="3-create-the-connection"></a>3. Criar a ligação

Desenrem a tecla partilhada e criem a ligação do VNet clássico ao VNet do Gestor de Recursos. Não é possível definir a chave partilhada utilizando o portal. Certifique-se de que executa estes passos enquanto inicia sessão utilizando a versão clássica dos cmdlets PowerShell. Para tal, utilize **o Add-AzureAccount**. Caso contrário, não poderá definir o '-AzureVNetGatewayKey'.

- Neste exemplo, **-VNetName** é o nome do VNet clássico encontrado no seu ficheiro de configuração de rede. 
- O **-LocalNetworkSiteName** é o nome especificado para o site local, tal como encontrado no seu ficheiro de configuração de rede.
- O **-SharedKey** é um valor que gera e especifica. Por exemplo, usamos *o abc123,* mas pode gerar algo mais complexo. O importante é que o valor que especifica aqui deve ser o mesmo valor especificado ao criar o seu Gestor de Recursos para a ligação clássica.

```powershell
Set-AzureVNetGatewayKey -VNetName "Group ClassicRG ClassicVNet" `
-LocalNetworkSiteName "172B9E16_RMVNetLocal" -SharedKey abc123
```

## <a name="section-6---verify-your-connections"></a><a name="verify"></a>Secção 6 - Verificar as suas ligações

Pode verificar as suas ligações utilizando o portal Azure ou o PowerShell. Ao verificar, poderá ter de esperar um minuto ou dois à medida que a ligação estiver a ser criada. Quando uma ligação é bem sucedida, o estado de conectividade muda de 'Connecting' para 'Connected'.

### <a name="to-verify-the-connection-from-your-classic-vnet-to-your-resource-manager-vnet"></a>Para verificar a ligação do seu VNet clássico ao seu Gestor de Recursos VNet

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

### <a name="to-verify-the-connection-from-your-resource-manager-vnet-to-your-classic-vnet"></a>Para verificar a ligação do seu Gestor de Recursos VNet ao seu VNet clássico

[!INCLUDE [vpn-gateway-verify-connection-portal-rm](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="vnet-to-vnet-faq"></a><a name="faq"></a>FAQ da ligação VNet a VNet

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]
