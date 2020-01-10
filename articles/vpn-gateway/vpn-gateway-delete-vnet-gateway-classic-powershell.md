---
title: 'Excluir um gateway de rede virtual: clássico do Azure'
description: Exclua um gateway de rede virtual usando o PowerShell no modelo de implantação clássico.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 05/11/2017
ms.author: cherylmc
ms.openlocfilehash: 0ff8e42cecb705e57ce85c92e84a0ad9b78929a5
ms.sourcegitcommit: 5b073caafebaf80dc1774b66483136ac342f7808
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75778503"
---
# <a name="delete-a-virtual-network-gateway-using-powershell-classic"></a>Excluir um gateway de rede virtual usando o PowerShell (clássico)

> [!div class="op_single_selector"]
> * [Resource Manager - portal do Azure](vpn-gateway-delete-vnet-gateway-portal.md)
> * [Resource Manager – PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [Clássica – PowerShell](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
>

Este artigo ajuda você a excluir um gateway de VPN no modelo de implantação clássico usando o PowerShell. Depois que o gateway de rede virtual tiver sido excluído, modifique o arquivo de configuração de rede para remover os elementos que você não está mais usando.

## <a name="connect"></a>Etapa 1: conectar-se ao Azure

### <a name="1-install-the-latest-powershell-cmdlets"></a>1. Instale os cmdlets mais recentes do PowerShell.

Baixe e instale a versão mais recente dos cmdlets do PowerShell do SM (gerenciamento de serviços do Azure). Para obter mais informações, veja [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/overview).

### <a name="2-connect-to-your-azure-account"></a>2. Conecte-se à sua conta do Azure. 

Abra a consola do PowerShell com direitos elevados e ligue-se à sua conta. Utilize o exemplo seguinte para o ajudar na ligação:

```powershell
Add-AzureAccount
```

## <a name="export"></a>Etapa 2: exportar e exibir o arquivo de configuração de rede

Crie um diretório no seu computador e, em seguida, exporte o ficheiro de configuração de rede para o diretório. Você usa esse arquivo para exibir as informações de configuração atuais e também para modificar a configuração de rede.

Neste exemplo, o ficheiro de configuração de rede é exportado para C:\AzureNet.

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

Abra o arquivo com um editor de texto e exiba o nome da sua VNet clássica. Quando você cria uma VNet no portal do Azure, o nome completo que o Azure usa não é visível no Portal. Por exemplo, uma VNet que parece ser denominada ' ClassicVNet1 ' na portal do Azure, pode ter um nome muito mais longo no arquivo de configuração de rede. O nome pode ser semelhante a: ' Group ClassicRG1 ClassicVNet1 '. Os nomes de rede virtual são listados como **' VirtualNetworkSite Name = '** . Use os nomes no arquivo de configuração de rede ao executar os cmdlets do PowerShell.

## <a name="delete"></a>Etapa 3: excluir o gateway de rede virtual

Quando você exclui um gateway de rede virtual, todas as conexões com a VNet por meio do gateway são desconectadas. Se você tiver clientes P2S conectados à VNet, eles serão desconectados sem aviso.

Este exemplo exclui o gateway de rede virtual. Certifique-se de usar o nome completo da rede virtual do arquivo de configuração de rede.

```powershell
Remove-AzureVNetGateway -VNetName "Group ClassicRG1 ClassicVNet1"
```

Se for bem-sucedido, o retorno mostrará:

```
Status : Successful
```

## <a name="modify"></a>Etapa 4: modificar o arquivo de configuração de rede

Quando você exclui um gateway de rede virtual, o cmdlet não modifica o arquivo de configuração de rede. Você precisa modificar o arquivo para remover os elementos que não estão mais sendo usados. As seções a seguir ajudam a modificar o arquivo de configuração de rede que você baixou.

### <a name="lnsref"></a>Referências de site de rede local

Para remover informações de referência do site, faça alterações de configuração em **ConnectionsToLocalNetwork/LocalNetworkSiteRef**. A remoção de uma referência de site local dispara o Azure para excluir um túnel. Dependendo da configuração que você criou, talvez você não tenha um **LocalNetworkSiteRef** listado.

```
<Gateway>
   <ConnectionsToLocalNetwork>
     <LocalNetworkSiteRef name="D1BFC9CB_Site2">
       <Connection type="IPsec" />
     </LocalNetworkSiteRef>
   </ConnectionsToLocalNetwork>
 </Gateway>
```

Exemplo:

```
<Gateway>
   <ConnectionsToLocalNetwork>
   </ConnectionsToLocalNetwork>
 </Gateway>
```

### <a name="lns"></a>Sites de rede local

Remova os sites locais que você não está mais usando. Dependendo da configuração que você criou, é possível que você não tenha um **LocalNetworkSite** listado.

```
<LocalNetworkSites>
  <LocalNetworkSite name="Site1">
    <AddressSpace>
      <AddressPrefix>192.168.0.0/16</AddressPrefix>
    </AddressSpace>
    <VPNGatewayAddress>5.4.3.2</VPNGatewayAddress>
  </LocalNetworkSite>
  <LocalNetworkSite name="Site3">
    <AddressSpace>
      <AddressPrefix>192.168.0.0/16</AddressPrefix>
    </AddressSpace>
    <VPNGatewayAddress>57.179.18.164</VPNGatewayAddress>
  </LocalNetworkSite>
 </LocalNetworkSites>
```

Neste exemplo, removemos apenas Site3.

```
<LocalNetworkSites>
  <LocalNetworkSite name="Site1">
    <AddressSpace>
      <AddressPrefix>192.168.0.0/16</AddressPrefix>
    </AddressSpace>
    <VPNGatewayAddress>5.4.3.2</VPNGatewayAddress>
  </LocalNetworkSite>
 </LocalNetworkSites>
```

### <a name="clientaddresss"></a>AddressPool do cliente

Se você tiver uma conexão P2S com sua VNet, terá um **VPNClientAddressPool**. Remova os pools de endereços do cliente que correspondem ao gateway de rede virtual que você excluiu.

```
<Gateway>
    <VPNClientAddressPool>
      <AddressPrefix>10.1.0.0/24</AddressPrefix>
    </VPNClientAddressPool>
  <ConnectionsToLocalNetwork />
 </Gateway>
```

Exemplo:

```
<Gateway>
  <ConnectionsToLocalNetwork />
 </Gateway>
```

### <a name="gwsub"></a>GatewaySubnet

Exclua o **GatewaySubnet** que corresponde à VNet.

```
<Subnets>
   <Subnet name="FrontEnd">
     <AddressPrefix>10.11.0.0/24</AddressPrefix>
   </Subnet>
   <Subnet name="GatewaySubnet">
     <AddressPrefix>10.11.1.0/29</AddressPrefix>
   </Subnet>
 </Subnets>
```

Exemplo:

```
<Subnets>
   <Subnet name="FrontEnd">
     <AddressPrefix>10.11.0.0/24</AddressPrefix>
   </Subnet>
 </Subnets>
```

## <a name="upload"></a>Etapa 5: carregar o arquivo de configuração de rede

Salve as alterações e carregue o arquivo de configuração de rede no Azure. Certifique-se de alterar o caminho do arquivo conforme necessário para o seu ambiente.

```powershell
Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
```

Se for bem-sucedido, o retorno mostrará algo semelhante a este exemplo:

```
OperationDescription        OperationId                      OperationStatus                                                
--------------------        -----------                      ---------------                                           
Set-AzureVNetConfig         e0ee6e66-9167-cfa7-a746-7casb9   Succeeded
```
