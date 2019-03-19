---
title: 'Ligar redes virtuais clássicas a VNets do Resource Manager do Azure: PowerShell | Microsoft Docs'
description: Crie uma ligação VPN entre VNets clássicas e do VNets do Resource Manager com o Gateway de VPN e o PowerShell.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/17/2018
ms.author: cherylmc
ms.openlocfilehash: cf7726d017afd579b1eb227ec0fd3b9710395de6
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58082267"
---
# <a name="connect-virtual-networks-from-different-deployment-models-using-powershell"></a>Ligar redes virtuais a partir de modelos de implementação diferentes com o PowerShell

Este artigo ajuda-o a ligar VNets clássicas a vnets do Resource Manager para permitir que os recursos localizados nos modelos de implantação à parte para comunicar entre si. Os passos neste artigo utilizam o PowerShell, mas também pode criar esta configuração através do portal do Azure ao selecionar o artigo desta lista.

> [!div class="op_single_selector"]
> * [Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
> 
> 

Ligar uma VNet clássica a uma VNet do Resource Manager é semelhante à ligação VNet a uma localização do site no local. Ambos os tipos de conetividade utilizam um gateway de VPN para fornecer um túnel seguro através de IPsec/IKE. Pode criar uma ligação entre VNets que estão em subscrições diferentes e em regiões diferentes. Também pode ligar VNets que já tenham ligações a redes no local, desde que o gateway que tenham sido configuradas com é dinâmico ou baseado na rota. Para obter mais informações sobre ligações de VNet a VNet, consulte [FAQ sobre VNet para VNet](#faq) no final deste artigo. 

Se ainda não tiver um gateway de rede virtual e não pretender criar uma, pode querer em vez disso, considere ligar às VNets utilização o VNet Peering. O VNet peering não utiliza um gateway de VPN. Para obter mais informações, veja [VNet peering](../virtual-network/virtual-network-peering-overview.md).

## <a name="before"></a>Antes de começar

Os passos seguintes explicam as definições necessárias para configurar um gateway dinâmico ou baseado na rota para cada VNet e criar uma ligação VPN entre os gateways. Esta configuração não suporta gateways estáticos ou baseada em políticas.

### <a name="pre"></a>Pré-requisitos

* Ambas as VNets já foram criadas. Se precisar de criar uma rede virtual do resource manager, consulte [criar um grupo de recursos e uma rede virtual](../virtual-network/quick-create-powershell.md#create-a-resource-group-and-a-virtual-network). Para criar uma rede virtual clássica, veja [criar uma VNet clássica](https://docs.microsoft.com/azure/virtual-network/create-virtual-network-classic).
* Os intervalos de endereços para as VNets não se sobrepõem entre si, ou se sobrepõe a qualquer um dos intervalos de outras ligações que os gateways podem ser ligados a.
* Ter instalado os cmdlets do PowerShell mais recente. Ver [como instalar e configurar o Azure PowerShell](/powershell/azure/overview) para obter mais informações. Certifique-se de que instalar a gestão de serviço (SM) e os cmdlets do Gestor de recursos (RM). 

### <a name="exampleref"></a>Definições de exemplo

Pode utilizar estes valores para criar um ambiente de teste ou consultá-los para compreender melhor os exemplos neste artigo.

**Definições da VNet clássicas**

Nome da VNet = ClassicVNet <br>
Localização = E.U.A. oeste <br>
Espaços de endereços de rede virtual = 10.0.0.0/24 <br>
Subnet-1 = 10.0.0.0/27 <br>
GatewaySubnet = 10.0.0.32/29 <br>
Nome de rede local = RMVNetLocal <br>
GatewayType = DynamicRouting

**Definições da VNet do Resource Manager**

Nome da VNet = RMVNet <br>
Grupo de recursos = RG1 <br>
Espaços de endereços IP de rede virtual = 192.168.0.0/16 <br>
Subnet-1 = 192.168.1.0/24 <br>
GatewaySubnet = 192.168.0.0/26 <br>
Localização = E.U.A. leste <br>
Nome IP público do gateway = gwpip <br>
Gateway de rede local = ClassicVNetLocal <br>
Nome do Gateway de rede virtual = RMGateway <br>
Configuração de endereçamento de IP do gateway = gwipconfig

## <a name="createsmgw"></a>Secção 1 - configurar a VNet clássica
### <a name="1-download-your-network-configuration-file"></a>1. Transfira o ficheiro de configuração de rede
1. Inicie sessão na sua conta do Azure na consola do PowerShell com direitos elevados. O seguinte cmdlet pede-lhe as credenciais de início de sessão para a sua conta do Azure. Após iniciar sessão, são transferidas as definições da conta para que fiquem disponíveis para o Azure PowerShell. Os cmdlets de gestão de serviço (SM) do Azure PowerShell clássicos são utilizados nesta secção.

   ```azurepowershell
   Add-AzureAccount
   ```

   Obtenha a sua subscrição do Azure.

   ```azurepowershell
   Get-AzureSubscription
   ```

   Se tiver mais do que uma subscrição, selecione a subscrição que pretende utilizar.

   ```azurepowershell
   Select-AzureSubscription -SubscriptionName "Name of subscription"
   ```
2. Exporte o ficheiro de configuração de rede do Azure ao executar o comando seguinte. Pode alterar a localização do ficheiro a exportar para uma localização diferente, se necessário.

   ```azurepowershell
   Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
   ```
3. Abra o ficheiro. XML que transferiu a editá-lo. Para obter um exemplo do ficheiro de configuração de rede, consulte a [esquema de configuração de rede](https://msdn.microsoft.com/library/jj157100.aspx).

### <a name="2-verify-the-gateway-subnet"></a>2. Certifique-se a sub-rede do gateway
Na **VirtualNetworkSites** elemento, adicione uma sub-rede de gateway para a VNet se ainda não já foi criado. Ao trabalhar com o ficheiro de configuração de rede, a sub-rede de gateway deve ter o nome "GatewaySubnet" ou o Azure não é possível reconhecer e utilizá-la como uma sub-rede de gateway.

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

### <a name="3-add-the-local-network-site"></a>3. Adicionar o site de rede local
Adicionar o site de rede local representa a VNet de RM para o qual pretende ligar. Adicionar uma **LocalNetworkSites** elemento ao arquivo se ainda não existir. Neste momento na configuração, o VPNGatewayAddress pode ser qualquer endereço IP público válido porque ainda não criamos o gateway na vnet do Resource Manager. Depois de criarmos o gateway, substituímos este endereço IP do marcador de posição com o endereço IP público correto que lhe foi atribuído ao RM gateway.

    <LocalNetworkSites>
      <LocalNetworkSite name="RMVNetLocal">
        <AddressSpace>
          <AddressPrefix>192.168.0.0/16</AddressPrefix>
        </AddressSpace>
        <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>
      </LocalNetworkSite>
    </LocalNetworkSites>

### <a name="4-associate-the-vnet-with-the-local-network-site"></a>4. Associar a VNet com o site de rede local
Nesta secção, podemos especificar o site de rede local que pretende ligar a VNet. Neste caso, é a VNet do Resource Manager que mencionadas anteriormente. Certifique-se de que os nomes correspondem. Este passo não cria um gateway. Especifica que o gateway irá ligar à rede local.

        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="RMVNetLocal">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>

### <a name="5-save-the-file-and-upload"></a>5. Guarde o ficheiro e carregar
Guarde o ficheiro e, em seguida, importá-lo para o Azure ao executar o comando seguinte. Certifique-se de que alterar o caminho de ficheiro para o seu ambiente, conforme necessário.

```azurepowershell
Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
```

Verá um resultado similar ao que mostra que a importação foi concluída com êxito.

        OperationDescription        OperationId                      OperationStatus                                                
        --------------------        -----------                      ---------------                                                
        Set-AzureVNetConfig        e0ee6e66-9167-cfa7-a746-7casb9    Succeeded 

### <a name="6-create-the-gateway"></a>6. Criar o gateway

Antes de executar este exemplo, consulte o ficheiro de configuração rede que transferiu para os nomes de exatos que o Azure espera ver. O ficheiro de configuração de rede contém os valores de suas redes virtuais clássicas. Por vezes, os nomes para VNets clássicas são alterados no ficheiro de configuração de rede durante a criação de definições da VNet clássicas no portal do Azure devido a diferenças nos modelos de implementação. Por exemplo, se utilizou o portal do Azure para criar um clássico VNet com o nome "Rede virtual clássica" e criou um grupo de recursos com o nome 'ClassicRG', o nome que está contido no ficheiro de configuração de rede é convertido para "Grupo ClassicRG VNet clássica". Ao especificar o nome de uma VNet que contenha espaços, utilize o valor entre aspas.


Utilize o exemplo a seguir para criar um gateway de encaminhamento dinâmico:

```azurepowershell
New-AzureVNetGateway -VNetName ClassicVNet -GatewayType DynamicRouting
```

Pode verificar o estado do gateway com o **Get-AzureVNetGateway** cmdlet.

## <a name="creatermgw"></a>Secção 2 - configurar o gateway de VNet do RM

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Os pré-requisitos partem do princípio de que já criou uma VNet do RM. Neste passo, vai criar um gateway de VPN para a VNet RM. Não inicie estes passos até depois de ter obtido o endereço IP público para o gateway da VNet clássica. 

1. Inicie sessão na sua conta do Azure na consola do PowerShell. O seguinte cmdlet pede-lhe as credenciais de início de sessão para a sua conta do Azure. Depois de iniciar sessão, as definições da conta são transferidas para que fiquem disponíveis para o Azure PowerShell. Opcionalmente, pode utilizar a funcionalidade "Experimente-lo" para iniciar o Azure Cloud Shell no browser.

   Se utilizar o Azure Cloud Shell, ignore o seguinte cmdlet:

   ```azurepowershell
   Connect-AzAccount
   ``` 
   Para verificar que está a utilizar a subscrição correta, execute o seguinte cmdlet:  

   ```azurepowershell-interactive
   Get-AzSubscription
   ```
   
   Se tiver mais de uma subscrição, especifique a subscrição que pretende utilizar.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName "Name of subscription"
   ```
2. Crie um gateway de rede local. Numa rede virtual, o gateway de rede local refere-se normalmente à sua localização no local. Neste caso, o gateway de rede local refere-se a sua VNet clássica. Atribua um nome pelo qual Azure pode fazer referência a ele e especificar também o prefixo de espaço de endereço. O Azure utiliza o prefixo do endereço IP que especificar para identificar o tráfego a enviar para a sua localização no local. Se precisar de ajustar as informações aqui mais tarde, antes de criar o gateway, pode modificar os valores e executar o exemplo novamente.
   
   **-Nome** é o nome que pretende atribuir a referir-se para o gateway de rede local.<br>
   **-AddressPrefix** é o espaço de endereços da vnet clássica.<br>
   **-GatewayIpAddress** é o endereço IP público do gateway da VNet clássica. Certifique-se de que alterar o texto de exemplo seguintes "n.n.n.n" para refletir o endereço IP correto.<br>

   ```azurepowershell-interactive
   New-AzLocalNetworkGateway -Name ClassicVNetLocal `
   -Location "West US" -AddressPrefix "10.0.0.0/24" `
   -GatewayIpAddress "n.n.n.n" -ResourceGroupName RG1
   ```
3. Pedir um endereço IP público a ser alocada para o gateway de rede virtual na vnet do Resource Manager. Não é possível especificar o endereço IP que pretende utilizar. O endereço IP é dinamicamente atribuído ao gateway de rede virtual. No entanto, não significa que o endereço IP é alterado. O único momento em que as alterações de endereço IP de gateway de rede virtual é quando o gateway é eliminado e recriado. Não é alterado ao redimensionar, repor, ou outra manutenção/atualização interna do gateway.

   Neste passo, iremos também definir uma variável que é utilizada num passo posterior.

   ```azurepowershell-interactive
   $ipaddress = New-AzPublicIpAddress -Name gwpip `
   -ResourceGroupName RG1 -Location 'EastUS' `
   -AllocationMethod Dynamic
   ```

4. Certifique-se de que a rede virtual tem uma sub-rede de gateway. Não se existir nenhuma sub-rede de gateway, adicione um. Certifique-se de que a sub-rede do gateway com o nome *GatewaySubnet*.
5. Obter a sub-rede utilizada para o gateway ao executar o comando seguinte. Neste passo, iremos também definir uma variável a ser utilizado no próximo passo.
   
   **-Nome** é o nome da sua VNet do Resource Manager.<br>
   **-ResourceGroupName** é o grupo de recursos que a VNet está associada. A sub-rede do gateway tem de existir para esta VNet e deve ser nomeada *GatewaySubnet* funcione corretamente.<br>

   ```azurepowershell-interactive
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name GatewaySubnet `
   -VirtualNetwork (Get-AzVirtualNetwork -Name RMVNet -ResourceGroupName RG1)
   ``` 

6. Crie a configuração de endereçamento de IP de gateway. A configuração do gateway define a sub-rede e o endereço IP público a utilizar. Utilize o exemplo seguinte para criar a configuração do gateway.

   Neste passo, o **- SubnetId** e **- PublicIpAddressId** parâmetros têm de ser transmitidos a propriedade de id de sub-rede bem como os objetos de endereço IP, respectivamente. Não é possível utilizar uma cadeia de caracteres simples. Estas variáveis são definidas no passo para pedir um IP público e o passo para recuperar a sub-rede.

   ```azurepowershell-interactive
   $gwipconfig = New-AzVirtualNetworkGatewayIpConfig `
   -Name gwipconfig -SubnetId $subnet.id `
   -PublicIpAddressId $ipaddress.id
   ```
7. Crie o gateway de rede virtual do Resource Manager ao executar o comando seguinte. O `-VpnType` tem de ser *RouteBased*. Pode demorar 45 minutos ou mais para criar o gateway.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1 `
   -Location "EastUS" -GatewaySKU Standard -GatewayType Vpn `
   -IpConfigurations $gwipconfig `
   -EnableBgp $false -VpnType RouteBased
   ```
8. Copie o endereço IP público depois de criado o gateway VPN. Usá-lo quando configurar as definições de rede local para a sua VNet clássica. Pode utilizar o cmdlet seguinte para obter o endereço IP público. O endereço IP público está listado no retorno como *IpAddress*.

   ```azurepowershell-interactive
   Get-AzPublicIpAddress -Name gwpip -ResourceGroupName RG1
   ```

## <a name="localsite"></a>Secção 3 - modifique as definições de local site VNet clássicas

Nesta secção, vai trabalhar com a VNet clássica. Substitua o endereço IP do marcador de posição que utilizou ao especificar as definições de local site que serão utilizadas para ligar ao gateway de VNet do Resource Manager. Como está trabalhando com a VNet clássica, utilize o PowerShell instalado localmente para o seu computador, não o TryIt de Shell do Azure na Cloud.

1. Exporte o ficheiro de configuração de rede.

   ```azurepowershell
   Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
   ```
2. Com um editor de texto, modifique o valor para VPNGatewayAddress. Substitua o endereço IP do marcador de posição pelo endereço IP público do gateway do Resource Manager e, em seguida, guarde as alterações.

   ```
   <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>
   ```
3. Importe o ficheiro de configuração de rede modificado para o Azure.

   ```azurepowershell
   Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
   ```

## <a name="connect"></a>Secção 4 - criar uma ligação entre os gateways
Criar uma ligação entre os gateways requer o PowerShell. Terá de adicionar a sua conta do Azure para utilizar a versão clássica dos cmdlets do PowerShell. Para tal, utilize **Add-AzureAccount**.

1. Na consola do PowerShell, defina a chave partilhada. Antes de executar os cmdlets, consulte o ficheiro de configuração rede que transferiu para os nomes de exatos que o Azure espera ver. Ao especificar o nome de uma VNet que contenha espaços, utilize plicas à volta o valor.<br><br>No exemplo seguinte, **- VNetName** é o nome da VNet clássica e **- LocalNetworkSiteName** é o nome que especificou para o site de rede local. O **- SharedKey** é um valor que geram e especificar. No exemplo, utilizámos 'abc123', mas pode gerar e utilizar algo mais complexo. O importante é que o valor que especificar aqui tem de ser o mesmo valor que especificou no passo seguinte, quando criar a ligação. O retorno deve mostrar **Estado: Com êxito**.

   ```azurepowershell
   Set-AzureVNetGatewayKey -VNetName ClassicVNet `
   -LocalNetworkSiteName RMVNetLocal -SharedKey abc123
   ```
2. Crie a ligação VPN ao executar os seguintes comandos:
   
   Defina as variáveis.

   ```azurepowershell-interactive
   $vnet01gateway = Get-AzLocalNetworkGateway -Name ClassicVNetLocal -ResourceGroupName RG1
   $vnet02gateway = Get-AzVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1
   ```
   
   Crie a ligação. Tenha em atenção que o **- ConnectionType** é IPsec, não Vnet2Vnet.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGatewayConnection -Name RM-Classic -ResourceGroupName RG1 `
   -Location "East US" -VirtualNetworkGateway1 `
   $vnet02gateway -LocalNetworkGateway2 `
   $vnet01gateway -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
   ```

## <a name="verify"></a>Secção 5 - Verifique as suas ligações

### <a name="to-verify-the-connection-from-your-classic-vnet-to-your-resource-manager-vnet"></a>Para verificar a ligação da sua VNet clássica a VNet do Resource Manager

#### <a name="powershell"></a>PowerShell

[!INCLUDE [vpn-gateway-verify-connection-ps-classic](../../includes/vpn-gateway-verify-connection-ps-classic-include.md)]

#### <a name="azure-portal"></a>Portal do Azure

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]


### <a name="to-verify-the-connection-from-your-resource-manager-vnet-to-your-classic-vnet"></a>Para verificar a ligação da sua VNet do Resource Manager para a VNet clássica

#### <a name="powershell"></a>PowerShell

[!INCLUDE [vpn-gateway-verify-ps-rm](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

#### <a name="azure-portal"></a>Portal do Azure

[!INCLUDE [vpn-gateway-verify-connection-portal-rm](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="faq"></a>FAQ da ligação VNet a VNet

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]
