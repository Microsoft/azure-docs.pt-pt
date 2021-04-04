---
title: 'Excluir uma porta de entrada de rede virtual: Azure classic'
description: Elimine um gateway de rede virtual utilizando o PowerShell no modelo clássico de implementação.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/08/2020
ms.author: cherylmc
ms.openlocfilehash: 39585a68c5cddc50cd04e82caca71209270f7b68
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91874123"
---
# <a name="delete-a-virtual-network-gateway-using-powershell-classic"></a>Excluir um gateway de rede virtual usando PowerShell (clássico)

> [!div class="op_single_selector"]
> * [Gestor de Recursos - Portal Azure](vpn-gateway-delete-vnet-gateway-portal.md)
> * [Resource Manager – PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [Clássico - PowerShell](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
>

Este artigo ajuda-o a eliminar uma porta de entrada VPN no modelo clássico de implementação utilizando o PowerShell. Depois de eliminado o gateway de rede virtual, modifique o ficheiro de configuração da rede para remover elementos que já não está a utilizar.

## <a name="step-1-connect-to-azure"></a><a name="connect"></a>Passo 1: Ligar ao Azure

### <a name="1-install-the-latest-powershell-cmdlets"></a>1. Instale os mais recentes cmdlets PowerShell.

[!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

### <a name="2-connect-to-your-azure-account"></a>2. Ligue-se à sua conta Azure.

Abra a consola do PowerShell com direitos elevados e ligue-se à sua conta. Utilize o exemplo seguinte para o ajudar na ligação:

1. Abra a sua consola PowerShell com direitos elevados.
2. Ligar à sua conta. Utilize o exemplo seguinte para o ajudar na ligação:

   ```powershell
   Add-AzureAccount
   ```

## <a name="step-2-export-and-view-the-network-configuration-file"></a><a name="export"></a>Passo 2: Exportar e ver o ficheiro de configuração da rede

Crie um diretório no seu computador e, em seguida, exporte o ficheiro de configuração de rede para o diretório. Utilize este ficheiro para visualizar as informações de configuração atuais e também para modificar a configuração da rede.

Neste exemplo, o ficheiro de configuração de rede é exportado para C:\AzureNet.

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

Abra o ficheiro com um editor de texto e veja o nome do seu VNet clássico. Quando cria um VNet no portal Azure, o nome completo que o Azure utiliza não é visível no portal. Por exemplo, um VNet que parece ser nomeado 'ClassicVNet1' no portal Azure, pode ter um nome muito mais longo no ficheiro de configuração da rede. O nome pode parecer algo como: 'Group ClassicRG1 ClassicVNet1'. Os nomes da rede virtual estão listados como **'VirtualNetworkSite name ='**. Utilize os nomes no ficheiro de configuração da rede quando executar os cmdlets PowerShell.

## <a name="step-3-delete-the-virtual-network-gateway"></a><a name="delete"></a>Passo 3: Apagar o portal de rede virtual

Quando elimina um gateway de rede virtual, todas as ligações ao VNet através do gateway estão desligadas. Se tiver clientes P2S ligados ao VNet, eles serão desligados sem aviso prévio.

Este exemplo elimina o gateway de rede virtual. Certifique-se de que utiliza o nome completo da rede virtual a partir do ficheiro de configuração da rede.

```powershell
Remove-AzureVNetGateway -VNetName "Group ClassicRG1 ClassicVNet1"
```

Se for bem sucedido, o retorno mostra:

```
Status : Successful
```

## <a name="step-4-modify-the-network-configuration-file"></a><a name="modify"></a>Passo 4: Modificar o ficheiro de configuração da rede

Quando elimina um gateway de rede virtual, o cmdlet não modifica o ficheiro de configuração da rede. É necessário modificar o ficheiro para remover os elementos que já não estão a ser utilizados. As seguintes secções ajudam-no a modificar o ficheiro de configuração de rede que descarregou.

### <a name="local-network-site-references"></a><a name="lnsref"></a>Referências do site de rede local

Para remover informações de referência do site, escou alterações de configuração para **ConnectionsToLocalNetwork/LocalNetworkSiteRef**. A remoção de uma referência local do site aciona o Azure para apagar um túnel. Dependendo da configuração que criou, pode não ter uma **SiteNetworkSiteRef** listada.

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

### <a name="local-network-sites"></a><a name="lns"></a>Sites de rede locais

Remova quaisquer locais que já não esteja a usar. Dependendo da configuração que criou, é possível que não tenha uma **SiteNetworkSite** listada.

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

Neste exemplo, removemos apenas o Site3.

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

### <a name="client-addresspool"></a><a name="clientaddresss"></a>Resposta ao clientePool

Se tiver uma ligação P2S com o seu VNet, terá uma **VPNClientAddressPool**. Remova as piscinas de endereço do cliente que correspondem ao gateway de rede virtual que apagou.

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

### <a name="gatewaysubnet"></a><a name="gwsub"></a>GatewaySubnet

Elimine o **GatewaySubnet** que corresponde ao VNet.

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

## <a name="step-5-upload-the-network-configuration-file"></a><a name="upload"></a>Passo 5: Carregar o ficheiro de configuração da rede

Guarde as alterações e carreja o ficheiro de configuração da rede para OZure. Certifique-se de que altera o caminho do ficheiro conforme necessário para o seu ambiente.

```powershell
Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
```

Se for bem sucedido, o retorno mostra algo semelhante a este exemplo:

```
OperationDescription        OperationId                      OperationStatus                                                
--------------------        -----------                      ---------------                                           
Set-AzureVNetConfig         e0ee6e66-9167-cfa7-a746-7casb9   Succeeded
```
