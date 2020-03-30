---
title: 'Ligue redes virtuais clássicas a VNets Do Gestor de Recursos Azure: PowerShell'
description: Crie uma ligação VPN entre VNets clássicos e VNets gestor de recursos usando VPN Gateway e PowerShell.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/17/2018
ms.author: cherylmc
ms.openlocfilehash: 1dc0eec6178420976181b05a059e9f8b4859ec2a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77152011"
---
# <a name="connect-virtual-networks-from-different-deployment-models-using-powershell"></a>Ligar redes virtuais a partir de modelos de implementação diferentes com o PowerShell

Este artigo ajuda-o a ligar VNets clássicos a VNets gestor de recursos para permitir que os recursos localizados nos modelos de implementação separados se comuniquem entre si. Os passos neste artigo utilizam o PowerShell, mas também pode criar esta configuração utilizando o portal Azure, selecionando o artigo desta lista.

> [!div class="op_single_selector"]
> * [Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
> 
> 

Ligar um VNet clássico a um VNet gestor de recursos é semelhante a ligar um VNet a uma localização no local no local. Ambos os tipos de conetividade utilizam um gateway de VPN para fornecer um túnel seguro através de IPsec/IKE. Pode criar uma ligação entre VNets que estão em diferentes subscrições e em diferentes regiões. Também pode ligar VNets que já têm ligações a redes no local, desde que o portal com o que foram configurados seja dinâmico ou baseado em rotas. Para obter mais informações sobre ligações de VNet a VNet, consulte [FAQ sobre VNet para VNet](#faq) no final deste artigo. 

Se ainda não tem um portal de rede virtual e não quer criar um, pode considerar em vez disso ligar os seus VNets utilizando o VNet Peering. O VNet peering não utiliza um gateway de VPN. Para obter mais informações, veja [VNet peering](../virtual-network/virtual-network-peering-overview.md).

## <a name="before-you-begin"></a><a name="before"></a>Antes de começar

Os seguintes passos passam por você através das definições necessárias para configurar uma porta de entrada dinâmica ou baseada em rotas para cada VNet e criar uma ligação VPN entre os gateways. Esta configuração não suporta gateways estáticos ou baseados em políticas.

### <a name="prerequisites"></a><a name="pre"></a>Pré-requisitos

* Ambos os VNets já foram criados. Se precisar de criar uma rede virtual de gestor de recursos, consulte Criar um grupo de [recursos e uma rede virtual.](../virtual-network/quick-create-powershell.md#create-a-resource-group-and-a-virtual-network) Para criar uma rede virtual clássica, consulte [Criar um VNet clássico.](https://docs.microsoft.com/azure/virtual-network/create-virtual-network-classic)
* As gamas de endereços para os VNets não se sobrepõem entre si, nem se sobrepõem a qualquer uma das gamas para outras ligações às quais os portões possam estar ligados.
* Instalou os mais recentes cmdlets PowerShell. Consulte como instalar e configurar o [Azure PowerShell](/powershell/azure/overview) para obter mais informações. Certifique-se de que instala tanto os cmdlets de Gestão de Serviços (SM) como o Gestor de Recursos (RM). 

### <a name="example-settings"></a><a name="exampleref"></a>Definições de exemplo

Pode utilizar estes valores para criar um ambiente de teste ou consultá-los para compreender melhor os exemplos neste artigo.

**Configurações vnet clássicas**

Nome VNet = ClassicVNet <br>
Localização = Oeste DOS EUA <br>
Espaços de Endereço seleções virtuais = 10.0.0.0/24 <br>
Subnet-1 = 10.0.0.0/27 <br>
GatewaySubnet = 10.0.0.32/29 <br>
Nome da rede local = RMVNetLocal <br>
GatewayType = DynamicRouting

**Definições vNet do Gestor de Recursos**

Nome VNet = RMVNet <br>
Grupo de Recursos = RG1 <br>
Espaços de endereço IP da rede virtual = 192.168.0.0/16 <br>
Subnet-1 = 192.168.1.0/24 <br>
GatewaySubnet = 192.168.0.0/26 <br>
Localização = Leste dos EUA <br>
Gateway nome IP público = gwpip <br>
Gateway de rede local = ClassicVNetLocal <br>
Nome de Gateway de Rede Virtual = RMGateway <br>
Configuração de endereço IP gateway = gwipconfig

## <a name="section-1---configure-the-classic-vnet"></a><a name="createsmgw"></a>Secção 1 - Configure o clássico VNet
### <a name="1-download-your-network-configuration-file"></a>1. Descarregue o seu ficheiro de configuração de rede
1. Inicie sessão na sua conta Azure na consola PowerShell com direitos elevados. O seguinte cmdlet solicita-lhe as credenciais de login para a sua Conta Azure. Após iniciar sessão, são transferidas as definições da conta para que fiquem disponíveis para o Azure PowerShell. Os cmdlets clássicos de Gestão de Serviços (SM) Azure PowerShell são utilizados nesta secção.

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
2. Exporte o seu ficheiro de configuração de rede Azure executando o seguinte comando. Pode alterar a localização do ficheiro para exportar para um local diferente, se necessário.

   ```azurepowershell
   Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
   ```
3. Abra o ficheiro .xml que descarregou para editá-lo. Para obter um exemplo do ficheiro de configuração da rede, consulte o Esquema de [Configuração](https://msdn.microsoft.com/library/jj157100.aspx)da Rede .

### <a name="2-verify-the-gateway-subnet"></a>2. Verificar a sub-rede de gateway
No elemento **VirtualNetworkSites,** adicione uma sub-rede de gateway ao seu VNet se ainda não tiver sido criada. Ao trabalhar com o ficheiro de configuração da rede, a sub-rede de gateway DEVE ser denominada "GatewaySubnet" ou Azure não pode reconhecê-lo e usá-lo como uma sub-rede de gateway.

[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

**Exemplo:**

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

### <a name="3-add-the-local-network-site"></a>3. Adicione o site da rede local
O site de rede local que adiciona representa o RM VNet ao qual pretende ligar. Adicione um elemento **LocalNetworkSites** ao ficheiro se um já não existir. Nesta altura da configuração, o VPNGatewayAddress pode ser qualquer endereço IP público válido porque ainda não criámos a porta de entrada para o Gestor de Recursos VNet. Assim que criarmos o gateway, substituímos este endereço IP do espaço reservado pelo endereço IP público correto que foi atribuído ao gateway RM.

    <LocalNetworkSites>
      <LocalNetworkSite name="RMVNetLocal">
        <AddressSpace>
          <AddressPrefix>192.168.0.0/16</AddressPrefix>
        </AddressSpace>
        <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>
      </LocalNetworkSite>
    </LocalNetworkSites>

### <a name="4-associate-the-vnet-with-the-local-network-site"></a>4. Associar a VNet ao site da rede local
Nesta secção, especificamos o site da rede local a que pretende ligar o VNet. Neste caso, foi o Gestor de Recursos VNet que referiu anteriormente. Certifique-se de que os nomes coincidem. Este passo não cria uma porta de entrada. Especifica a rede local a que o portal se ligará.

        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="RMVNetLocal">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>

### <a name="5-save-the-file-and-upload"></a>5. Guardar o ficheiro e carregar
Guarde o ficheiro e, em seguida, importe-o para Azure executando o seguinte comando. Certifique-se de que altera o caminho dos ficheiros conforme necessário para o seu ambiente.

```azurepowershell
Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
```

Verá um resultado semelhante mostrando que a importação foi bem sucedida.

        OperationDescription        OperationId                      OperationStatus                                                
        --------------------        -----------                      ---------------                                                
        Set-AzureVNetConfig        e0ee6e66-9167-cfa7-a746-7casb9    Succeeded 

### <a name="6-create-the-gateway"></a>6. Criar o portal

Antes de executar este exemplo, consulte o ficheiro de configuração da rede que descarregou para os nomes exatos que o Azure espera ver. O ficheiro de configuração da rede contém os valores das suas redes virtuais clássicas. Por vezes, os nomes dos VNets clássicos são alterados no ficheiro de configuração da rede ao criar configurações vNet clássicas no portal Azure devido às diferenças nos modelos de implementação. Por exemplo, se usou o portal Azure para criar um VNet clássico chamado 'Classic VNet' e o criou num grupo de recursos chamado 'ClassicRG', o nome que está contido no ficheiro de configuração da rede é convertido para 'Group ClassicRG Classic VNet'. Ao especificar o nome de um VNet que contenha espaços, utilize aspas em torno do valor.


Use o seguinte exemplo para criar um gateway de encaminhamento dinâmico:

```azurepowershell
New-AzureVNetGateway -VNetName ClassicVNet -GatewayType DynamicRouting
```

Pode verificar o estado do portal utilizando o cmdlet **Get-AzureVNetGateway.**

## <a name="section-2---configure-the-rm-vnet-gateway"></a><a name="creatermgw"></a>Secção 2 - Configure o gateway RM VNet



Os pré-requisitos assumem que já criou um RM VNet. Neste passo, cria-se uma porta de entrada VPN para o RM VNet. Não inicie estes passos até ter recuperado o endereço IP público para o portal clássico da VNet. 

1. Inscreva-se na sua conta Azure na consola PowerShell. O seguinte cmdlet solicita-lhe as credenciais de login para a sua Conta Azure. Após a sessão, as definições da sua conta são descarregadas para que estejam disponíveis para o Azure PowerShell. Pode utilizar opcionalmente a funcionalidade "Try It" para lançar o Azure Cloud Shell no navegador.

   Se utilizar a Casca de Nuvem Azure, ignore o seguinte cmdlet:

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
2. Crie um gateway de rede local. Numa rede virtual, o gateway de rede local refere-se normalmente à sua localização no local. Neste caso, o portal de rede local refere-se ao seu VNet Clássico. Dê-lhe um nome pelo qual o Azure pode referir-se a ele, e também especificar o prefixo do espaço de endereço. O Azure utiliza o prefixo do endereço IP que especificar para identificar o tráfego a enviar para a sua localização no local. Se precisar de ajustar a informação aqui mais tarde, antes de criar o seu portal, pode modificar os valores e executar novamente a amostra.
   
   **-O nome** é o nome que pretende atribuir para se referir ao portal da rede local.<br>
   **-AddressPrefix** é o Espaço de Endereço para o seu VNet clássico.<br>
   **-GatewayIpAddress** é o endereço IP público da porta de entrada clássica da VNet. Certifique-se de que altera o seguinte texto da amostra "n.n.n.n" para refletir o endereço IP correto.<br>

   ```azurepowershell-interactive
   New-AzLocalNetworkGateway -Name ClassicVNetLocal `
   -Location "West US" -AddressPrefix "10.0.0.0/24" `
   -GatewayIpAddress "n.n.n.n" -ResourceGroupName RG1
   ```
3. Solicite um endereço IP público para ser atribuído à porta de entrada da rede virtual para o Gestor de Recursos VNet. Não é possível especificar o endereço IP que pretende utilizar. O endereço IP é dinamicamente atribuído ao gateway da rede virtual. No entanto, não significa que o endereço IP é alterado. A única altura em que o endereço IP de gateway da rede virtual muda é quando o gateway é eliminado e recriado. Não muda em redimensionamento, reposição ou outramanutenção/upgrade interno do gateway.

   Neste passo, também definimos uma variável que é usada num passo posterior.

   ```azurepowershell-interactive
   $ipaddress = New-AzPublicIpAddress -Name gwpip `
   -ResourceGroupName RG1 -Location 'EastUS' `
   -AllocationMethod Dynamic
   ```

4. Verifique se a sua rede virtual tem uma sub-rede de gateway. Se não existir uma sub-rede de gateway, adicione uma. Certifique-se de que a sub-rede gateway é chamada *GatewaySubnet*.
5. Recupere a sub-rede utilizada para o portal executando o seguinte comando. Neste passo, também estabelecemos uma variável a ser usada no próximo passo.
   
   **-O nome** é o nome do seu Gestor de Recursos VNet.<br>
   **-ResourceGroupName** é o grupo de recursos com o que o VNet está associado. A sub-rede gateway já deve existir para este VNet e deve ser chamada *GatewaySubnet* para funcionar corretamente.<br>

   ```azurepowershell-interactive
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name GatewaySubnet `
   -VirtualNetwork (Get-AzVirtualNetwork -Name RMVNet -ResourceGroupName RG1)
   ``` 

6. Crie a configuração de endereço IP gateway. A configuração do gateway define a sub-rede e o endereço IP público a utilizar. Utilize o exemplo seguinte para criar a configuração do gateway.

   Neste passo, os parâmetros **-SubnetId** e **-PublicIpAddressId** devem ser passados a propriedade id da subnet e objetos de endereço IP, respectivamente. Não pode usar uma corda simples. Estas variáveis são definidas no passo para solicitar um IP público e o passo para recuperar a sub-rede.

   ```azurepowershell-interactive
   $gwipconfig = New-AzVirtualNetworkGatewayIpConfig `
   -Name gwipconfig -SubnetId $subnet.id `
   -PublicIpAddressId $ipaddress.id
   ```
7. Crie o portal de rede virtual do Gestor de Recursos executando o seguinte comando. O `-VpnType` deve ser *Baseado na Rota.* Pode levar 45 minutos ou mais para a porta de entrada criar.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1 `
   -Location "EastUS" -GatewaySKU Standard -GatewayType Vpn `
   -IpConfigurations $gwipconfig `
   -EnableBgp $false -VpnType RouteBased
   ```
8. Copie o endereço IP público uma vez criado o gateway VPN. Usa-o quando configura as definições de rede locais para o seu VNet Clássico. Pode utilizar o seguinte cmdlet para recuperar o endereço IP público. O endereço IP público está listado na devolução como *IpAddress*.

   ```azurepowershell-interactive
   Get-AzPublicIpAddress -Name gwpip -ResourceGroupName RG1
   ```

## <a name="section-3---modify-the-classic-vnet-local-site-settings"></a><a name="localsite"></a>Secção 3 - Modificar as configurações clássicas do site local VNet

Nesta secção, trabalha-se com o clássico VNet. Substitua o endereço IP do espaço reservado que utilizou ao especificar as definições do site local que serão utilizadas para ligar ao gateway VNet do Gestor de Recursos. Como está a trabalhar com o clássico VNet, utilize o PowerShell instalado localmente no seu computador, e não com o Azure Cloud Shell TryIt.

1. Exportar o ficheiro de configuração da rede.

   ```azurepowershell
   Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
   ```
2. Utilizando um editor de texto, modifique o valor para VPNGatewayAddress. Substitua o endereço IP do espaço reservado pelo endereço IP público do gateway do Gestor de Recursos e, em seguida, guarde as alterações.

   ```
   <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>
   ```
3. Importar o ficheiro de configuração de rede modificado para o Azure.

   ```azurepowershell
   Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
   ```

## <a name="section-4---create-a-connection-between-the-gateways"></a><a name="connect"></a>Secção 4 - Criar uma ligação entre os portões
Criar uma ligação entre os gateways requer PowerShell. Poderá ter de adicionar a sua Conta Azure para utilizar a versão clássica dos cmdlets PowerShell. Para isso, utilize **add-azureAccount**.

1. Na consola PowerShell, desloque a sua chave partilhada. Antes de executar os cmdlets, consulte o ficheiro de configuração da rede que descarregou para os nomes exatos que o Azure espera ver. Ao especificar o nome de um VNet que contenha espaços, utilize marcas únicas em torno do valor.<br><br>No exemplo seguinte, **-VNetName** é o nome do clássico VNet e **-LocalNetworkSiteName** é o nome especificado para o site da rede local. O **-SharedKey** é um valor que gera e especifica. No exemplo, usamos 'abc123', mas pode gerar e usar algo mais complexo. O importante é que o valor que especifica aqui deve ser o mesmo valor que especifica no próximo passo quando criar a sua ligação. O retorno deve mostrar **Status: Bem-sucedido**.

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

## <a name="section-5---verify-your-connections"></a><a name="verify"></a>Secção 5 - Verifique as suas ligações

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
