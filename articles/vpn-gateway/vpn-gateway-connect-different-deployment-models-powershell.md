---
title: 'Conectar redes virtuais clássicas a Azure Resource Manager VNets: PowerShell'
description: Crie uma conexão VPN entre o VNets clássico e o VNets do Resource Manager usando o gateway de VPN e o PowerShell.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/17/2018
ms.author: cherylmc
ms.openlocfilehash: 1c11539460f1ef65f8cea3d36f1a017661133355
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/10/2020
ms.locfileid: "75833967"
---
# <a name="connect-virtual-networks-from-different-deployment-models-using-powershell"></a>Ligar redes virtuais a partir de modelos de implementação diferentes com o PowerShell

Este artigo ajuda você a conectar o VNets clássico ao VNets do Resource Manager para permitir que os recursos localizados nos modelos de implantação separados se comuniquem entre si. As etapas neste artigo usam o PowerShell, mas você também pode criar essa configuração usando o portal do Azure selecionando o artigo na lista.

> [!div class="op_single_selector"]
> * [Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
> 
> 

A conexão de uma vnet clássica a uma VNet do Resource Manager é semelhante à conexão de uma VNet a um site local. Ambos os tipos de conetividade utilizam um gateway de VPN para fornecer um túnel seguro através de IPsec/IKE. Você pode criar uma conexão entre VNets que estão em assinaturas diferentes e em regiões diferentes. Você também pode conectar VNets que já têm conexões com redes locais, desde que o gateway com o qual foram configuradas seja dinâmico ou baseado em rota. Para obter mais informações sobre ligações de VNet a VNet, consulte [FAQ sobre VNet para VNet](#faq) no final deste artigo. 

Se você ainda não tiver um gateway de rede virtual e não quiser criar um, convém considerar a conexão do VNets usando o emparelhamento VNet. O VNet peering não utiliza um gateway de VPN. Para obter mais informações, veja [VNet peering](../virtual-network/virtual-network-peering-overview.md).

## <a name="before"></a>Antes de começar

As etapas a seguir orientam você pelas configurações necessárias para configurar um gateway dinâmico ou baseado em rota para cada VNet e criar uma conexão VPN entre os gateways. Essa configuração não dá suporte a gateways estáticos ou baseados em políticas.

### <a name="pre"></a>Pré-requisitos

* Ambos os VNets já foram criados. Se você precisar criar uma rede virtual do Resource Manager, consulte [criar um grupo de recursos e uma rede virtual](../virtual-network/quick-create-powershell.md#create-a-resource-group-and-a-virtual-network). Para criar uma rede virtual clássica, consulte [criar uma VNet clássica](https://docs.microsoft.com/azure/virtual-network/create-virtual-network-classic).
* Os intervalos de endereços para os VNets não se sobrepõem entre si ou sobrepõem-se a qualquer um dos intervalos para outras conexões às quais os gateways podem estar conectados.
* Você instalou os cmdlets do PowerShell mais recentes. Consulte [como instalar e configurar Azure PowerShell](/powershell/azure/overview) para obter mais informações. Certifique-se de instalar o gerenciamento de serviços (SM) e os cmdlets do Gerenciador de recursos (RM). 

### <a name="exampleref"></a>Definições de exemplo

Pode utilizar estes valores para criar um ambiente de teste ou consultá-los para compreender melhor os exemplos neste artigo.

**Configurações da VNet clássica**

Nome da VNet = ClassicVNet <br>
Localização = oeste dos EUA <br>
Espaços de endereço de rede virtual = 10.0.0.0/24 <br>
Sub-rede-1 = 10.0.0.0/27 <br>
GatewaySubnet = 10.0.0.32/29 <br>
Nome da rede local = RMVNetLocal <br>
Gatewaytype = DynamicRouting

**Configurações de VNet do Resource Manager**

Nome da VNet = RMVNet <br>
Grupo de recursos = RG1 <br>
Espaços de endereço IP da rede virtual = 192.168.0.0/16 <br>
Subnet-1 = 192.168.1.0/24 <br>
GatewaySubnet = 192.168.0.0/26 <br>
Localização = leste dos EUA <br>
Nome do IP público do gateway = gwpip <br>
Gateway de rede local = ClassicVNetLocal <br>
Nome do gateway de rede virtual = RMGateway <br>
Configuração de endereçamento IP do gateway = gwipconfig

## <a name="createsmgw"></a>Seção 1-configurar a VNet clássica
### <a name="1-download-your-network-configuration-file"></a>1. baixar o arquivo de configuração de rede
1. Faça logon em sua conta do Azure no console do PowerShell com direitos elevados. O cmdlet a seguir solicita as credenciais de logon para sua conta do Azure. Após iniciar sessão, são transferidas as definições da conta para que fiquem disponíveis para o Azure PowerShell. Os cmdlets de Azure PowerShell do SM (gerenciamento de serviço clássico) são usados nesta seção.

   ```azurepowershell
   Add-AzureAccount
   ```

   Obtenha sua assinatura do Azure.

   ```azurepowershell
   Get-AzureSubscription
   ```

   Se tiver mais do que uma subscrição, selecione a subscrição que pretende utilizar.

   ```azurepowershell
   Select-AzureSubscription -SubscriptionName "Name of subscription"
   ```
2. Exporte o arquivo de configuração de rede do Azure executando o comando a seguir. Você pode alterar o local do arquivo para exportar para um local diferente, se necessário.

   ```azurepowershell
   Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
   ```
3. Abra o arquivo. XML que você baixou para editá-lo. Para obter um exemplo do arquivo de configuração de rede, consulte o [esquema de configuração de rede](https://msdn.microsoft.com/library/jj157100.aspx).

### <a name="2-verify-the-gateway-subnet"></a>2. Verifique a sub-rede do gateway
No elemento **VirtualNetworkSites** , adicione uma sub-rede de gateway à sua VNet se uma já não tiver sido criada. Ao trabalhar com o arquivo de configuração de rede, a sub-rede de gateway deve ser nomeada "GatewaySubnet" ou o Azure não pode reconhecê-lo e usá-lo como uma sub-rede de gateway.

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
O site de rede local que você adiciona representa a VNet do RM à qual você deseja se conectar. Adicione um elemento **LocalNetworkSites** ao arquivo se ainda não existir um. Neste ponto da configuração, o VPNGatewayAddress pode ser qualquer endereço IP público válido, pois ainda não criamos o gateway para a VNet do Resource Manager. Depois de criarmos o gateway, substituimos esse endereço IP de espaço reservado pelo endereço IP público correto que foi atribuído ao gateway do RM.

    <LocalNetworkSites>
      <LocalNetworkSite name="RMVNetLocal">
        <AddressSpace>
          <AddressPrefix>192.168.0.0/16</AddressPrefix>
        </AddressSpace>
        <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>
      </LocalNetworkSite>
    </LocalNetworkSites>

### <a name="4-associate-the-vnet-with-the-local-network-site"></a>4. associar a VNet ao site de rede local
Nesta seção, especificamos o site de rede local ao qual você deseja conectar a VNet. Nesse caso, é a VNet do Resource Manager que você referenciou anteriormente. Verifique se os nomes correspondem. Essa etapa não cria um gateway. Ele especifica a rede local à qual o gateway se conectará.

        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="RMVNetLocal">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>

### <a name="5-save-the-file-and-upload"></a>5. Salve o arquivo e carregue-o
Salve o arquivo e importe-o para o Azure executando o comando a seguir. Certifique-se de alterar o caminho do arquivo conforme necessário para o seu ambiente.

```azurepowershell
Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
```

Você verá um resultado semelhante mostrando que a importação foi bem-sucedida.

        OperationDescription        OperationId                      OperationStatus                                                
        --------------------        -----------                      ---------------                                                
        Set-AzureVNetConfig        e0ee6e66-9167-cfa7-a746-7casb9    Succeeded 

### <a name="6-create-the-gateway"></a>6. criar o gateway

Antes de executar este exemplo, consulte o arquivo de configuração de rede que você baixou para os nomes exatos que o Azure espera ver. O arquivo de configuração de rede contém os valores para suas redes virtuais clássicas. Às vezes, os nomes para VNets clássicas são alterados no arquivo de configuração de rede ao criar configurações de VNet clássicas na portal do Azure devido às diferenças nos modelos de implantação. Por exemplo, se você usou o portal do Azure para criar uma VNet clássica denominada "VNet clássica" e a criou em um grupo de recursos chamado "ClassicRG", o nome contido no arquivo de configuração de rede será convertido em "Group ClassicRG Classic VNet". Ao especificar o nome de uma VNet que contém espaços, use aspas em volta do valor.


Use o exemplo a seguir para criar um gateway de roteamento dinâmico:

```azurepowershell
New-AzureVNetGateway -VNetName ClassicVNet -GatewayType DynamicRouting
```

Você pode verificar o status do gateway usando o cmdlet **Get-AzureVNetGateway** .

## <a name="creatermgw"></a>Seção 2-configurar o gateway de VNet do RM

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Os pré-requisitos pressupõem que você já criou uma VNet do RM. Nesta etapa, você cria um gateway de VPN para a VNet do RM. Não inicie essas etapas até que você tenha recuperado o endereço IP público para o gateway da VNet clássica. 

1. Entre em sua conta do Azure no console do PowerShell. O cmdlet a seguir solicita as credenciais de logon para sua conta do Azure. Depois de entrar, suas configurações de conta são baixadas para que estejam disponíveis para Azure PowerShell. Opcionalmente, você pode usar o recurso "Experimente" para iniciar o Azure Cloud Shell no navegador.

   Se você usar Azure Cloud Shell, ignore o seguinte cmdlet:

   ```azurepowershell
   Connect-AzAccount
   ``` 
   Para verificar se você está usando a assinatura correta, execute o seguinte cmdlet:  

   ```azurepowershell-interactive
   Get-AzSubscription
   ```
   
   Se você tiver mais de uma assinatura, especifique a assinatura que deseja usar.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName "Name of subscription"
   ```
2. Crie um gateway de rede local. Numa rede virtual, o gateway de rede local refere-se normalmente à sua localização no local. Nesse caso, o gateway de rede local se refere à sua VNet clássica. Dê a ele um nome pelo qual o Azure possa fazer referência a ele e especifique também o prefixo de espaço de endereço. O Azure utiliza o prefixo do endereço IP que especificar para identificar o tráfego a enviar para a sua localização no local. Se precisar ajustar as informações aqui mais tarde, antes de criar o gateway, você poderá modificar os valores e executar o exemplo novamente.
   
   **-Name** é o nome que você deseja atribuir para se referir ao gateway de rede local.<br>
   **-AddressPrefix** é o espaço de endereço para sua VNet clássica.<br>
   **-GatewayIpAddress** é o endereço IP público do gateway da VNet clássica. Certifique-se de alterar o seguinte texto de exemplo "n. n. n. n" para refletir o endereço IP correto.<br>

   ```azurepowershell-interactive
   New-AzLocalNetworkGateway -Name ClassicVNetLocal `
   -Location "West US" -AddressPrefix "10.0.0.0/24" `
   -GatewayIpAddress "n.n.n.n" -ResourceGroupName RG1
   ```
3. Solicite um endereço IP público a ser alocado para o gateway de rede virtual para a VNet do Resource Manager. Não é possível especificar o endereço IP que pretende utilizar. O endereço IP é alocado dinamicamente para o gateway de rede virtual. No entanto, não significa que o endereço IP é alterado. A única vez em que o endereço IP do gateway de rede virtual é alterado é quando o gateway é excluído e recriado. Ele não é alterado em redimensionamento, redefinição ou outras manutenções/atualizações internas do gateway.

   Nesta etapa, também definimos uma variável que é usada em uma etapa posterior.

   ```azurepowershell-interactive
   $ipaddress = New-AzPublicIpAddress -Name gwpip `
   -ResourceGroupName RG1 -Location 'EastUS' `
   -AllocationMethod Dynamic
   ```

4. Verifique se sua rede virtual tem uma sub-rede de gateway. Se não existir nenhuma sub-rede de gateway, adicione uma. Verifique se a sub-rede de gateway é nomeada *GatewaySubnet*.
5. Recupere a sub-rede usada para o gateway executando o comando a seguir. Nesta etapa, também definimos uma variável a ser usada na próxima etapa.
   
   **-Name** é o nome da sua VNet do Resource Manager.<br>
   **-ResourceGroupName** é o grupo de recursos ao qual a VNet está associada. A sub-rede de gateway já deve existir para essa VNet e deve ser nomeada *GatewaySubnet* para funcionar corretamente.<br>

   ```azurepowershell-interactive
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name GatewaySubnet `
   -VirtualNetwork (Get-AzVirtualNetwork -Name RMVNet -ResourceGroupName RG1)
   ``` 

6. Crie a configuração de endereçamento IP do gateway. A configuração do gateway define a sub-rede e o endereço IP público a utilizar. Utilize o exemplo seguinte para criar a configuração do gateway.

   Nesta etapa, os parâmetros **-subnetid** e **-PublicIpAddressId** devem ser passados como a propriedade ID da sub-rede e os objetos de endereço IP, respectivamente. Você não pode usar uma cadeia de caracteres simples. Essas variáveis são definidas na etapa para solicitar um IP público e a etapa para recuperar a sub-rede.

   ```azurepowershell-interactive
   $gwipconfig = New-AzVirtualNetworkGatewayIpConfig `
   -Name gwipconfig -SubnetId $subnet.id `
   -PublicIpAddressId $ipaddress.id
   ```
7. Crie o gateway de rede virtual do Resource Manager executando o comando a seguir. O `-VpnType` deve ser *RouteBased*. Pode levar de 45 minutos ou mais para que o gateway seja criado.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1 `
   -Location "EastUS" -GatewaySKU Standard -GatewayType Vpn `
   -IpConfigurations $gwipconfig `
   -EnableBgp $false -VpnType RouteBased
   ```
8. Copie o endereço IP público depois que o gateway de VPN tiver sido criado. Você o usa quando define as configurações de rede local para sua VNet clássica. Você pode usar o cmdlet a seguir para recuperar o endereço IP público. O endereço IP público é listado no retorno como *IPAddress*.

   ```azurepowershell-interactive
   Get-AzPublicIpAddress -Name gwpip -ResourceGroupName RG1
   ```

## <a name="localsite"></a>Seção 3-modificar as configurações do site local da VNet clássica

Nesta seção, você trabalhará com a VNet clássica. Substitua o endereço IP do espaço reservado que você usou ao especificar as configurações do site local que serão usadas para se conectar ao gateway de VNet do Resource Manager. Como você está trabalhando com a VNet clássica, use o PowerShell instalado localmente em seu computador, não o Azure Cloud Shell TryIt.

1. Exporte o arquivo de configuração de rede.

   ```azurepowershell
   Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
   ```
2. Usando um editor de texto, modifique o valor de VPNGatewayAddress. Substitua o endereço IP do espaço reservado pelo endereço IP público do gateway do Resource Manager e salve as alterações.

   ```
   <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>
   ```
3. Importe o arquivo de configuração de rede modificado para o Azure.

   ```azurepowershell
   Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
   ```

## <a name="connect"></a>Seção 4-criar uma conexão entre os gateways
A criação de uma conexão entre os gateways requer o PowerShell. Talvez seja necessário adicionar sua conta do Azure para usar a versão clássica dos cmdlets do PowerShell. Para fazer isso, use **Add-AzureAccount**.

1. No console do PowerShell, defina sua chave compartilhada. Antes de executar os cmdlets, consulte o arquivo de configuração de rede que você baixou para obter os nomes exatos que o Azure espera ver. Ao especificar o nome de uma VNet que contém espaços, use aspas simples ao seu valor.<br><br>No exemplo a seguir, **-VNetName** é o nome da VNet clássica e **-LocalNetworkSiteName** é o nome que você especificou para o site de rede local. O **-SharedKey** é um valor que você gera e especifica. No exemplo, usamos ' abc123 ', mas você pode gerar e usar algo mais complexo. O importante é que o valor especificado aqui deve ser o mesmo valor que você especificar na próxima etapa ao criar a conexão. O retorno deve mostrar **status: êxito**.

   ```azurepowershell
   Set-AzureVNetGatewayKey -VNetName ClassicVNet `
   -LocalNetworkSiteName RMVNetLocal -SharedKey abc123
   ```
2. Crie a conexão VPN executando os seguintes comandos:
   
   Defina as variáveis.

   ```azurepowershell-interactive
   $vnet01gateway = Get-AzLocalNetworkGateway -Name ClassicVNetLocal -ResourceGroupName RG1
   $vnet02gateway = Get-AzVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1
   ```
   
   Crie a ligação. Observe que **-ConnectionType** é IPsec, não Vnet2Vnet.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGatewayConnection -Name RM-Classic -ResourceGroupName RG1 `
   -Location "East US" -VirtualNetworkGateway1 `
   $vnet02gateway -LocalNetworkGateway2 `
   $vnet01gateway -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
   ```

## <a name="verify"></a>Seção 5-verificar suas conexões

### <a name="to-verify-the-connection-from-your-classic-vnet-to-your-resource-manager-vnet"></a>Para verificar a conexão de sua VNet clássica com sua VNet do Resource Manager

#### <a name="powershell"></a>PowerShell

[!INCLUDE [vpn-gateway-verify-connection-ps-classic](../../includes/vpn-gateway-verify-connection-ps-classic-include.md)]

#### <a name="azure-portal"></a>Portal do Azure

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]


### <a name="to-verify-the-connection-from-your-resource-manager-vnet-to-your-classic-vnet"></a>Para verificar a conexão de sua VNet do Resource Manager com sua VNet clássica

#### <a name="powershell"></a>PowerShell

[!INCLUDE [vpn-gateway-verify-ps-rm](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

#### <a name="azure-portal"></a>Portal do Azure

[!INCLUDE [vpn-gateway-verify-connection-portal-rm](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="faq"></a>FAQ da ligação VNet a VNet

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]
