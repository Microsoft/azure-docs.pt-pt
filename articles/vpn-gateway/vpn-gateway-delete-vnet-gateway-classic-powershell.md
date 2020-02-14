---
title: 'Eliminar um portal de rede virtual: Azure classic'
description: Elimine um portal de rede virtual utilizando o PowerShell no modelo de implementação clássico.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: cherylmc
ms.openlocfilehash: e7283f5e28edc6f7beaad3a2743aa155f6ea6e14
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198654"
---
# <a name="delete-a-virtual-network-gateway-using-powershell-classic"></a>Eliminar um portal de rede virtual utilizando o PowerShell (clássico)

> [!div class="op_single_selector"]
> * [Resource Manager - portal do Azure](vpn-gateway-delete-vnet-gateway-portal.md)
> * [Resource Manager – PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [Clássico - PowerShell](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
>

Este artigo ajuda-o a eliminar um gateway VPN no modelo de implementação clássico utilizando o PowerShell. Depois de o portal de rede virtual ter sido eliminado, modifique o ficheiro de configuração da rede para remover elementos que já não está a utilizar.

## <a name="connect"></a>Passo 1: Ligar a Azure

### <a name="1-install-the-latest-powershell-cmdlets"></a>1. Instale os mais recentes cmdlets PowerShell.

[!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

### <a name="2-connect-to-your-azure-account"></a>2. Ligue-se à sua conta Azure.

Abra a consola do PowerShell com direitos elevados e ligue-se à sua conta. Utilize o exemplo seguinte para o ajudar na ligação:

1. Abra a consola PowerShell com direitos elevados. Para mudar para a gestão do serviço, utilize este comando:

   ```powershell
   azure config mode asm
   ```
2. Ligar à sua conta. Utilize o exemplo seguinte para o ajudar na ligação:

   ```powershell
   Add-AzureAccount
   ```

## <a name="export"></a>Passo 2: Exportar e ver o ficheiro de configuração da rede

Crie um diretório no seu computador e, em seguida, exporte o ficheiro de configuração de rede para o diretório. Utiliza este ficheiro para visualizar as informações de configuração atuais e também para modificar a configuração da rede.

Neste exemplo, o ficheiro de configuração de rede é exportado para C:\AzureNet.

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

Abra o ficheiro com um editor de texto e veja o nome para o seu VNet clássico. Quando se cria um VNet no portal Azure, o nome completo que o Azure utiliza não é visível no portal. Por exemplo, um VNet que parece ser chamado de 'ClassicVNet1' no portal Azure, pode ter um nome muito mais longo no ficheiro de configuração da rede. O nome pode parecer algo como: 'Grupo ClassicRG1 ClassicVNet1'. Os nomes de rede virtuais são listados como **'VirtualNetworkSite name ='** . Utilize os nomes no ficheiro de configuração da rede ao executar os seus cmdlets PowerShell.

## <a name="delete"></a>Passo 3: Eliminar o gateway da rede virtual

Ao eliminar um portal de rede virtual, todas as ligações ao VNet através do portal estão desligadas. Se tiver clientes P2S ligados ao VNet, serão desligados sem aviso prévio.

Este exemplo elimina o portal da rede virtual. Certifique-se de que utiliza o nome completo da rede virtual a partir do ficheiro de configuração da rede.

```powershell
Remove-AzureVNetGateway -VNetName "Group ClassicRG1 ClassicVNet1"
```

Se for bem sucedido, o retorno mostra:

```
Status : Successful
```

## <a name="modify"></a>Passo 4: Modificar o ficheiro de configuração da rede

Ao eliminar um portal de rede virtual, o cmdlet não modifica o ficheiro de configuração da rede. É necessário modificar o ficheiro para remover os elementos que já não estão a ser utilizados. As seguintes secções ajudam-no a modificar o ficheiro de configuração da rede que descarregou.

### <a name="lnsref"></a>Referências do site da rede local

Para remover as informações de referência do site, faça alterações de configuração em **ConexõesToLocalNetwork/LocalNetworkSiteRef**. Remover uma referência local aciona o Azure para apagar um túnel. Dependendo da configuração que criou, pode não ter uma lista **localNetworkSiteRef.**

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

### <a name="lns"></a>Sites de rede locais

Remova quaisquer sites locais que já não esteja a utilizar. Dependendo da configuração que criou, é possível que não tenha um **Site local** listado.

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

### <a name="clientaddresss"></a>AddressPool do cliente

Se tiver uma ligação P2S ao seu VNet, terá um **VPNClientAddressPool**. Remova as piscinas de endereços do cliente que correspondem ao portal de rede virtual que apagou.

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

Elimine a **GatewaySubnet** que corresponde à VNet.

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

## <a name="upload"></a>Passo 5: Carregar o ficheiro de configuração da rede

Guarde as suas alterações e carregue o ficheiro de configuração da rede para o Azure. Certifique-se de que altera o caminho dos ficheiros conforme necessário para o seu ambiente.

```powershell
Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
```

Se for bem sucedido, o retorno mostra algo semelhante a este exemplo:

```
OperationDescription        OperationId                      OperationStatus                                                
--------------------        -----------                      ---------------                                           
Set-AzureVNetConfig         e0ee6e66-9167-cfa7-a746-7casb9   Succeeded
```
