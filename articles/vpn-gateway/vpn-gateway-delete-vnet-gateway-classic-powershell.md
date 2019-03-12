---
title: 'Elimine um gateway de rede virtual: PowerShell: Azure clássico | Documentos da Microsoft'
description: Elimine um gateway de rede virtual com o PowerShell no modelo de implementação clássica.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/11/2017
ms.author: cherylmc
ms.openlocfilehash: ca014e4f5fbc4a5695dbc5fedc85826c71a2a906
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57767775"
---
# <a name="delete-a-virtual-network-gateway-using-powershell-classic"></a>Eliminar um gateway de rede virtual com o PowerShell (clássico)

> [!div class="op_single_selector"]
> * [Resource Manager - portal do Azure](vpn-gateway-delete-vnet-gateway-portal.md)
> * [Resource Manager – PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [Clássica – PowerShell](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
>

Este artigo ajuda-o a eliminar um gateway VPN no modelo de implementação clássica com o PowerShell. Depois do gateway de rede virtual tiver sido eliminado, modificar o ficheiro de configuração de rede para remover os elementos que já não está a utilizar.

## <a name="connect"></a>Passo 1: Ligar ao Azure

### <a name="1-install-the-latest-powershell-cmdlets"></a>1. Instale os cmdlets do PowerShell mais recente.

Baixe e instale a versão mais recente dos cmdlets do PowerShell de gestão de serviço do Azure (SM). Para obter mais informações, veja [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/overview).

### <a name="2-connect-to-your-azure-account"></a>2. Ligar à sua conta do Azure. 

Abra a consola do PowerShell com direitos elevados e ligue-se à sua conta. Utilize o exemplo seguinte para o ajudar na ligação:

```powershell
Add-AzureAccount
```

## <a name="export"></a>Passo 2: Exportar e ver o ficheiro de configuração de rede

Crie um diretório no seu computador e, em seguida, exporte o ficheiro de configuração de rede para o diretório. Utilize este ficheiro para ambos os ver as informações de configuração atual e também para modificar a configuração de rede.

Neste exemplo, o ficheiro de configuração de rede é exportado para C:\AzureNet.

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

Abra o ficheiro com um editor de texto e ver o nome para a sua VNet clássica. Quando cria uma VNet no portal do Azure, o nome completo, que utiliza o Azure não é visível no portal. Por exemplo, uma VNet que aparece para ter o nome "ClassicVNet1" no portal do Azure, pode ter um nome muito mais tempo no ficheiro de configuração de rede. O nome pode ser algo como: 'Grupo ClassicRG1 ClassicVNet1'. Nomes de rede virtual são apresentados como **' VirtualNetworkSite name ='**. Utilize os nomes no ficheiro de configuração de rede ao executar os cmdlets do PowerShell.

## <a name="delete"></a>Passo 3: Eliminar o gateway de rede virtual

Quando eliminar um gateway de rede virtual, todas as ligações à VNet através do gateway estão ligado à Internet. Se tiver clientes de P2S ligados à VNet, irá ser desconectados sem aviso.

Este exemplo elimina o gateway de rede virtual. Certifique-se utilizar o nome completo da rede virtual a partir do ficheiro de configuração de rede.

```powershell
Remove-AzureVNetGateway -VNetName "Group ClassicRG1 ClassicVNet1"
```

Se tiver êxito, o retorno mostra:

```
Status : Successful
```

## <a name="modify"></a>Passo 4: Modificar o ficheiro de configuração de rede

Quando eliminar um gateway de rede virtual, o cmdlet não modifica o ficheiro de configuração de rede. Terá de modificar o ficheiro para remover os elementos que já não estão a ser utilizados. As seções a seguir ajudarão a modificar o ficheiro de configuração de rede que transferiu.

### <a name="lnsref"></a>Referências de Site de rede local

Para remover informações de referência do site, faça as alterações de configuração **ConnectionsToLocalNetwork/LocalNetworkSiteRef**. Remover um acionadores de referência do local site do Azure para eliminar um túnel. Dependendo da configuração que criou, pode não ter uma **LocalNetworkSiteRef** listados.

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

Remova todos os sites locais que já não estiver a utilizar. Dependendo da configuração que criou, é possível que não tem um **LocalNetworkSite** listados.

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

Neste exemplo, removemos Site3 apenas.

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

### <a name="clientaddresss"></a>O conjunto de endereços do cliente

Se tivesse uma ligação P2S a VNet, será necessário um **VPNClientAddressPool**. Remova os conjuntos de endereços de cliente que correspondem ao gateway de rede virtual que eliminou.

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

Eliminar a **GatewaySubnet** que corresponde à VNet.

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

## <a name="upload"></a>Passo 5: Carregue o ficheiro de configuração de rede

Guardar as alterações e carregar o ficheiro de configuração de rede para o Azure. Certifique-se de que alterar o caminho de ficheiro para o seu ambiente, conforme necessário.

```powershell
Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
```

Se tiver êxito, o retorno mostra algo semelhante a este exemplo:

```
OperationDescription        OperationId                      OperationStatus                                                
--------------------        -----------                      ---------------                                           
Set-AzureVNetConfig         e0ee6e66-9167-cfa7-a746-7casb9   Succeeded
```
