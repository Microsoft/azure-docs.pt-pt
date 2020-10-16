---
title: 'Ligue redes virtuais clássicas a VNets Azure Resource Manager: PowerShell'
description: Crie uma ligação VPN entre VNets clássicos e VNets gestor de recursos usando VPN Gateway e PowerShell.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/17/2018
ms.author: cherylmc
ms.openlocfilehash: 2c9b8a769dec1a2aa461a34203c98a228cf71d16
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87082057"
---
# <a name="connect-virtual-networks-from-different-deployment-models-using-powershell"></a>Ligar redes virtuais a partir de modelos de implementação diferentes com o PowerShell

Este artigo ajuda-o a ligar VNets clássicos a VNets do Gestor de Recursos para permitir que os recursos localizados nos modelos de implementação separados se comuniquem entre si. Os passos deste artigo utilizam o PowerShell, mas também pode criar esta configuração utilizando o portal Azure selecionando o artigo desta lista.

> [!div class="op_single_selector"]
> * [Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
> 
> 

Ligar um VNet clássico a um VNet do Gestor de Recursos é semelhante à ligação de um VNet a uma localização no local. Ambos os tipos de conetividade utilizam um gateway de VPN para fornecer um túnel seguro através de IPsec/IKE. Pode criar uma ligação entre VNets que estão em diferentes subscrições e em diferentes regiões. Também pode ligar VNets que já têm ligações a redes no local, desde que o gateway com o qual tenham sido configurados seja dinâmico ou baseado em rotas. Para obter mais informações sobre ligações de VNet a VNet, consulte [FAQ sobre VNet para VNet](#faq) no final deste artigo. 

Se ainda não tiver um gateway de rede virtual e não quiser criar um, talvez queira considerar ligar os seus VNets utilizando o VNet Peering. O VNet peering não utiliza um gateway de VPN. Para obter mais informações, veja [VNet peering](../virtual-network/virtual-network-peering-overview.md).

## <a name="before-you-begin"></a><a name="before"></a>Antes de começar

Os passos seguintes acompanham-no através das definições necessárias para configurar um gateway dinâmico ou baseado em rotas para cada VNet e criar uma ligação VPN entre os gateways. Esta configuração não suporta gateways estáticos ou baseados em políticas.

### <a name="prerequisites"></a><a name="pre"></a>Pré-requisitos

* Ambos os VNets já foram criados. Se precisar de criar uma rede virtual de gestor de recursos, consulte [criar um grupo de recursos e uma rede virtual.](../virtual-network/quick-create-powershell.md#create-a-resource-group-and-a-virtual-network) Para criar uma rede virtual clássica, consulte [Criar um VNet clássico.](https://docs.microsoft.com/azure/virtual-network/create-virtual-network-classic)
* Os intervalos de endereços para os VNets não se sobrepõem entre si, nem se sobrepõem a qualquer uma das gamas para outras ligações a que os gateways podem estar ligados.
* Instalou os mais recentes cmdlets PowerShell. Veja [como instalar e configurar a Azure PowerShell](/powershell/azure/) para obter mais informações. Certifique-se de que instala tanto os cmdlets de Gestão de Serviços (SM) como os cmdlets do Gestor de Recursos (RM). 

### <a name="example-settings"></a><a name="exampleref"></a>Definições de exemplo

Pode utilizar estes valores para criar um ambiente de teste ou consultá-los para compreender melhor os exemplos neste artigo.

**Definições clássicas do VNet**

Nome VNet = ClassicVNet <br>
Localização = Eua Ocidental <br>
Espaços de Endereço de Rede Virtual = 10.0.0.0/24 <br>
Sub-rede-1 = 10.0.0.0/27 <br>
GatewaySubnet = 10.0.0.32/29 <br>
Nome da rede local = RMVNetLocal <br>
GatewayType = Dinamagem dinâmica

**Definições VNet do Gestor de Recursos**

VNet Name = RMVNet <br>
Grupo de Recursos = RG1 <br>
Espaços de endereço IP de rede virtual = 192.168.0.0/16 <br>
Sub-rede-1 = 192.168.1.0/24 <br>
GatewaySubnet = 192.168.0.0/26 <br>
Localização = Leste dos EUA <br>
Gateway nome IP público = gwpip <br>
Gateway de rede local = ClassicVNetLocal <br>
Nome de Gateway de rede virtual = RMGateway <br>
Gateway IP configuração de endereçamento = gwipconfig

## <a name="section-1---configure-the-classic-vnet"></a><a name="createsmgw"></a>Secção 1 - Configurar o clássico VNet
### <a name="1-download-your-network-configuration-file"></a>1. Descarregue o seu ficheiro de configuração de rede
1. Faça login na sua conta Azure na consola PowerShell com direitos elevados. O cmdlet seguinte solicita-lhe as credenciais de login da sua Conta Azure. Após iniciar sessão, são transferidas as definições da conta para que fiquem disponíveis para o Azure PowerShell. Nesta secção são utilizados os cmdlets Azure PowerShell (Gestão de Serviços clássicos).

   ```azurepowershell
   Add-AzureAccount
   ```

   Obtenha a sua assinatura Azure.

   ```azurepowershell
   Get-AzureSubscription
   ```

   Se tiver mais do que uma subscrição, selecione a subscrição que pretende utilizar.

   ```azurepowershell
   Select-AzureSubscription -SubscriptionName "Name of subscription"
   ```
2. Exporte o seu ficheiro de configuração da rede Azure executando o seguinte comando. Pode alterar a localização do ficheiro para exportar para um local diferente, se necessário.

   ```azurepowershell
   Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
   ```
3. Abra o ficheiro .xml que descarregou para editá-lo. Para um exemplo do ficheiro de configuração da rede, consulte o [Esquema de Configuração da Rede](https://msdn.microsoft.com/library/jj157100.aspx).

### <a name="2-verify-the-gateway-subnet"></a>2. Verificar a sub-rede gateway
No elemento **VirtualNetworkSites,** adicione uma sub-rede de gateway ao seu VNet se ainda não tiver sido criada uma. Ao trabalhar com o ficheiro de configuração da rede, a sub-rede gateway DEVE ser denominada "GatewaySubnet" ou Azure não pode reconhecê-lo e usá-lo como sub-rede de gateway.

[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

**Exemplo:**

```xml
<VirtualNetworkSites>
  <VirtualNetworkSite name="ClassicVNet" Location="West US">
    <AddressSpace>
      <AddressPrefix>10.0.0.0/24</AddressPrefix>
    </AddressSpace>
    <Subnets>
      <Subnet name="Subnet-1">
        <AddressPrefix>10.0.0.0/27</AddressPrefix>
      </Subnet>
      <Subnet name="GatewaySubnet">
        <AddressPrefix>10.0.0.32/29</AddressPrefix>
      </Subnet>
    </Subnets>
  </VirtualNetworkSite>
</VirtualNetworkSites>
```

### <a name="3-add-the-local-network-site"></a>3. Adicione o site da rede local
O site de rede local que adiciona representa o RM VNet ao qual pretende ligar. Adicione um elemento **LocalNetworkSites** ao ficheiro se já não existir. Neste ponto da configuração, o VPNGatewayAddress pode ser qualquer endereço IP público válido porque ainda não criámos o portal para o Gestor de Recursos VNet. Assim que criarmos o gateway, substituímos este endereço IP do espaço reservado pelo endereço IP público correto que foi atribuído ao gateway RM.

```xml
<LocalNetworkSites>
  <LocalNetworkSite name="RMVNetLocal">
    <AddressSpace>
      <AddressPrefix>192.168.0.0/16</AddressPrefix>
    </AddressSpace>
    <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>
  </LocalNetworkSite>
</LocalNetworkSites>
```

### <a name="4-associate-the-vnet-with-the-local-network-site"></a>4. Associe o VNet ao site de rede local
Nesta secção, especificamos o site de rede local a que pretende ligar o VNet. Neste caso, é o Gestor de Recursos VNet que referiu anteriormente. Certifique-se de que os nomes coincidem. Este passo não cria uma porta de entrada. Especifica a rede local a que o portal se ligará.

```xml
<Gateway>
  <ConnectionsToLocalNetwork>
    <LocalNetworkSiteRef name="RMVNetLocal">
      <Connection type="IPsec" />
    </LocalNetworkSiteRef>
  </ConnectionsToLocalNetwork>
</Gateway>
```

### <a name="5-save-the-file-and-upload"></a>5. Guarde o ficheiro e faça o upload
Guarde o ficheiro e, em seguida, importe-o para Azure executando o seguinte comando. Certifique-se de que altera o caminho do ficheiro conforme necessário para o seu ambiente.

```azurepowershell
Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
```

Verá um resultado semelhante mostrando que a importação foi bem sucedida.

```output
OperationDescription        OperationId                      OperationStatus                                                
--------------------        -----------                      ---------------                                                
Set-AzureVNetConfig        e0ee6e66-9167-cfa7-a746-7casb9    Succeeded 
```

### <a name="6-create-the-gateway"></a>6. Criar o portal

Antes de executar este exemplo, consulte o ficheiro de configuração da rede que descarregou para obter os nomes exatos que a Azure espera ver. O ficheiro de configuração da rede contém os valores das suas redes virtuais clássicas. Por vezes, os nomes dos VNets clássicos são alterados no ficheiro de configuração da rede ao criar definições clássicas de VNet no portal Azure devido às diferenças nos modelos de implementação. Por exemplo, se usou o portal Azure para criar um VNet clássico chamado 'Classic VNet' e o criou num grupo de recursos chamado 'ClassicRG', o nome que está contido no ficheiro de configuração da rede é convertido para 'Group ClassicRG Classic VNet'. Ao especificar o nome de um VNet que contenha espaços, utilize aspas em torno do valor.


Utilize o seguinte exemplo para criar um gateway de encaminhamento dinâmico:

```azurepowershell
New-AzureVNetGateway -VNetName ClassicVNet -GatewayType DynamicRouting
```

Pode verificar o estado do gateway utilizando o cmdlet **Get-AzureVNetGateway.**

## <a name="section-2---configure-the-rm-vnet-gateway"></a><a name="creatermgw"></a>Secção 2 - Configurar o gateway RM VNet



Os pré-requisitos pressupõem que já criou um RM VNet. Neste passo, cria-se uma porta de entrada VPN para o RM VNet. Não inicie estes passos até ter recuperado o endereço IP público para o gateway clássico do VNet. 

1. Inscreva-se na sua conta Azure na consola PowerShell. O cmdlet seguinte solicita-lhe as credenciais de login da sua Conta Azure. Depois de iniciar sessão, as definições da sua conta são descarregadas para que estejam disponíveis para a Azure PowerShell. Pode utilizar opcionalmente a função "Try It" para lançar o Azure Cloud Shell no navegador.

   Se utilizar a Azure Cloud Shell, ignore o seguinte cmdlet:

   ```azurepowershell
   Connect-AzAccount
   ``` 
   Para verificar se está a utilizar a subscrição certa, execute o seguinte cmdlet:  

   ```azurepowershell-interactive
   Get-AzSubscription
   ```
   
   Se tiver mais de uma subscrição, especifique a subscrição que pretende utilizar.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName "Name of subscription"
   ```
2. Crie um gateway de rede local. Numa rede virtual, o gateway de rede local refere-se normalmente à sua localização no local. Neste caso, o portal de rede local refere-se ao seu VNet Clássico. Dê-lhe um nome pelo qual Azure pode se referir a ele, e também especificar o prefixo do espaço de endereço. O Azure utiliza o prefixo do endereço IP que especificar para identificar o tráfego a enviar para a sua localização no local. Se precisar de ajustar a informação aqui mais tarde, antes de criar o seu gateway, pode modificar os valores e executar novamente a amostra.
   
   **-O nome** é o nome que pretende atribuir para se referir ao portal de rede local.<br>
   **-AddressPrefix** é o Espaço de Endereço para o seu VNet clássico.<br>
   **-GatewayIpAddress** é o endereço IP público do gateway clássico da VNet. Certifique-se de alterar o seguinte texto de amostra "n.n.n.n" para refletir o endereço IP correto.<br>

   ```azurepowershell-interactive
   New-AzLocalNetworkGateway -Name ClassicVNetLocal `
   -Location "West US" -AddressPrefix "10.0.0.0/24" `
   -GatewayIpAddress "n.n.n.n" -ResourceGroupName RG1
   ```
3. Solicite um endereço IP público a ser alocado à porta de entrada de rede virtual para o VNet do Gestor de Recursos. Não é possível especificar o endereço IP que pretende utilizar. O endereço IP é dinamicamente atribuído ao gateway de rede virtual. No entanto, não significa que o endereço IP é alterado. A única vez que o endereço IP do gateway de rede virtual muda é quando o gateway é eliminado e recriado. Não muda através do redimensionamento, reposição ou outras manutenção/atualizações internas do gateway.

   Neste passo, também definimos uma variável que é usada num passo posterior.

   ```azurepowershell-interactive
   $ipaddress = New-AzPublicIpAddress -Name gwpip `
   -ResourceGroupName RG1 -Location 'EastUS' `
   -AllocationMethod Dynamic
   ```

4. Verifique se a sua rede virtual tem uma sub-rede de gateway. Se não existir uma sub-rede de gateway, adicione uma. Certifique-se de que a sub-rede gateway se chama *GatewaySubnet*.
5. Recupere a sub-rede utilizada para o portal executando o seguinte comando. Neste passo, também definimos uma variável a ser usada no passo seguinte.
   
   **-Nome** é o nome do seu Gestor de Recursos VNet.<br>
   **-ResourceGroupName** é o grupo de recursos a que o VNet está associado. A sub-rede gateway já deve existir para este VNet e deve ser denominada *GatewaySubnet* para funcionar corretamente.<br>

   ```azurepowershell-interactive
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name GatewaySubnet `
   -VirtualNetwork (Get-AzVirtualNetwork -Name RMVNet -ResourceGroupName RG1)
   ``` 

6. Crie a configuração de endereço IP gateway. A configuração do gateway define a sub-rede e o endereço IP público a utilizar. Utilize o exemplo seguinte para criar a configuração do gateway.

   Neste passo, os parâmetros **-SubnetId** e **-PublicIpAddressId** devem ser passados a propriedade de id a partir da sub-rede e objetos de endereço IP, respectivamente. Não pode usar uma corda simples. Estas variáveis são definidas no passo para solicitar um PI público e o passo para recuperar a sub-rede.

   ```azurepowershell-interactive
   $gwipconfig = New-AzVirtualNetworkGatewayIpConfig `
   -Name gwipconfig -SubnetId $subnet.id `
   -PublicIpAddressId $ipaddress.id
   ```
7. Crie o gateway de rede virtual do Gestor de Recursos executando o seguinte comando. O `-VpnType` deve ser *RouteBased*. Pode levar 45 minutos ou mais para a porta de entrada criar.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1 `
   -Location "EastUS" -GatewaySKU Standard -GatewayType Vpn `
   -IpConfigurations $gwipconfig `
   -EnableBgp $false -VpnType RouteBased
   ```
8. Copie o endereço IP público assim que o gateway VPN tiver sido criado. Utilize-o quando configurar as definições de rede locais para o seu VNet Clássico. Pode utilizar o cmdlet seguinte para recuperar o endereço IP público. O endereço IP público está listado na devolução como *IpAddress*.

   ```azurepowershell-interactive
   Get-AzPublicIpAddress -Name gwpip -ResourceGroupName RG1
   ```

## <a name="section-3---modify-the-classic-vnet-local-site-settings"></a><a name="localsite"></a>Secção 3 - Modificar as configurações clássicas do site local da VNet

Nesta secção, trabalha com o clássico VNet. Substitua o endereço IP do espaço reservado que utilizou ao especificar as definições do site local que serão usadas para ligar ao gateway VNet do Gestor de Recursos. Como está a trabalhar com o VNet clássico, use o PowerShell instalado localmente no seu computador, não com o Azure Cloud Shell TryIt.

1. Exporte o ficheiro de configuração da rede.

   ```azurepowershell
   Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
   ```
2. Utilizando um editor de texto, modifique o valor para VPNGatewayAddress. Substitua o endereço IP do espaço reservado pelo endereço IP público do gateway do Gestor de Recursos e, em seguida, guarde as alterações.

   ```
   <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>
   ```
3. Importe o ficheiro de configuração de rede modificado para o Azure.

   ```azurepowershell
   Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
   ```

## <a name="section-4---create-a-connection-between-the-gateways"></a><a name="connect"></a>Secção 4 - Criar uma ligação entre os gateways
Criar uma ligação entre os gateways requer PowerShell. Poderá ter de adicionar a sua Conta Azure para utilizar a versão clássica dos cmdlets PowerShell. Para tal, utilize **o Add-AzureAccount**.

1. Na consola PowerShell, desaça a sua chave partilhada. Antes de executar os cmdlets, consulte o ficheiro de configuração de rede que descarregou para obter os nomes exatos que a Azure espera ver. Ao especificar o nome de um VNet que contenha espaços, utilize aspas únicas em torno do valor.<br><br>Em exemplo, **-VNetName** é o nome do clássico VNet e **-LocalNetworkSiteName** é o nome especificado para o site da rede local. O **-SharedKey** é um valor que gera e especifica. No exemplo, usamos 'abc123', mas pode gerar e usar algo mais complexo. O importante é que o valor que especifica aqui deve ser o mesmo valor que especifica no passo seguinte quando criar a sua ligação. O retorno deve mostrar **Status: Bem sucedido**.

   ```azurepowershell
   Set-AzureVNetGatewayKey -VNetName ClassicVNet `
   -LocalNetworkSiteName RMVNetLocal -SharedKey abc123
   ```
2. Criar a ligação VPN executando os seguintes comandos:
   
   Defina as variáveis.

   ```azurepowershell-interactive
   $vnet01gateway = Get-AzLocalNetworkGateway -Name ClassicVNetLocal -ResourceGroupName RG1
   $vnet02gateway = Get-AzVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1
   ```
   
   Crie a ligação. Note que o **-ConnectionType** é IPsec, não Vnet2Vnet.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGatewayConnection -Name RM-Classic -ResourceGroupName RG1 `
   -Location "East US" -VirtualNetworkGateway1 `
   $vnet02gateway -LocalNetworkGateway2 `
   $vnet01gateway -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
   ```

## <a name="section-5---verify-your-connections"></a><a name="verify"></a>Secção 5 - Verificar as suas ligações

### <a name="to-verify-the-connection-from-your-classic-vnet-to-your-resource-manager-vnet"></a>Para verificar a ligação do seu VNet clássico ao seu Gestor de Recursos VNet

#### <a name="powershell"></a>PowerShell

[!INCLUDE [vpn-gateway-verify-connection-ps-classic](../../includes/vpn-gateway-verify-connection-ps-classic-include.md)]

#### <a name="azure-portal"></a>Portal do Azure

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]


### <a name="to-verify-the-connection-from-your-resource-manager-vnet-to-your-classic-vnet"></a>Para verificar a ligação do seu Gestor de Recursos VNet ao seu VNet clássico

#### <a name="powershell"></a>PowerShell

[!INCLUDE [vpn-gateway-verify-ps-rm](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

#### <a name="azure-portal"></a>Portal do Azure

[!INCLUDE [vpn-gateway-verify-connection-portal-rm](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="vnet-to-vnet-faq"></a><a name="faq"></a>FAQ da ligação VNet a VNet

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]
