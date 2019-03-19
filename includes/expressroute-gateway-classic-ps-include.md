---
title: incluir ficheiro
description: incluir ficheiro
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 12/13/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 70ac106995324c758bde942d12191a01e3457e6e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58125320"
---
> [!NOTE]
> Estes exemplos não se aplicam ao S2S/ExpressRoute coexistir configurações.
> Para obter mais informações sobre como trabalhar com gateways numa configuração coexist, consulte [configurar ligações coexistentes.](../articles/expressroute/expressroute-howto-coexist-classic.md#gw)

## <a name="add-a-gateway"></a>Adicionar um gateway

Quando adiciona um gateway a uma rede virtual com o modelo clássico de recursos, é modificar o ficheiro de configuração de rede diretamente antes de criar o gateway. Os valores nos exemplos abaixo tem de estar presentes no arquivo para criar um gateway. Se a rede virtual tinha anteriormente um gateway associado a si, alguns destes valores serão já estar presentes. Modificar o ficheiro para refletir os valores abaixo.

### <a name="download-the-network-configuration-file"></a>Transfira o ficheiro de configuração de rede

1. Transfira o ficheiro de configuração de rede utilizando os passos em [o ficheiro de configuração de rede](../articles/virtual-network/virtual-networks-using-network-configuration-file.md) artigo. Abra o ficheiro com um editor de texto.
2. Adicione um site de rede local para o ficheiro. Pode usar qualquer prefixo de endereço válido. Pode adicionar qualquer endereço IP válido para o gateway VPN. Os valores de endereço nesta secção não são utilizados para operações do ExpressRoute, mas são necessários para a validação do arquivo. No exemplo, "branch1" é o nome do site. Pode utilizar um nome diferente, mas certifique-se de que utilize o mesmo valor da secção de Gateway do ficheiro.

   ```
   <VirtualNetworkConfiguration>
    <Dns />
    <LocalNetworkSites>
      <LocalNetworkSite name="branch1">
        <AddressSpace>
          <AddressPrefix>165.3.1.0/27</AddressPrefix>
        </AddressSpace>
        <VPNGatewayAddress>3.2.1.4</VPNGatewayAddress>
    </LocalNetworkSite>
   ```
3. Navegue para o VirtualNetworkSites e modificar os campos.

   * Certifique-se de que a sub-rede do Gateway existe para a rede virtual. Se não existir, pode adicionar um neste momento. O nome tem de ser "GatewaySubnet".
   * Verifique se que existe a secção de Gateway do ficheiro. Se não, adicioná-lo. Isto é necessário para associar a rede virtual com o site de rede local (que representa a rede à qual está a ligar).
   * Certifique-se de que a ligação digitar = dedicado. Isto é necessário para ligações do ExpressRoute.

   ```
   </LocalNetworkSites>
    <VirtualNetworkSites>
      <VirtualNetworkSite name="myAzureVNET" Location="East US">
        <AddressSpace>
          <AddressPrefix>10.0.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="default">
            <AddressPrefix>10.0.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.0.1.0/27</AddressPrefix>
          </Subnet>
        </Subnets>
        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="branch1">
              <Connection type="Dedicated" />
            </LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>
      </VirtualNetworkSite>
    </VirtualNetworkSites>
   </VirtualNetworkConfiguration>
   </NetworkConfiguration>
   ```
4. Guarde o ficheiro e carregá-lo para o Azure.

### <a name="create-the-gateway"></a>Criar o gateway

Utilize o comando abaixo para criar um gateway. Substitua os valores pelos seus próprios.

```powershell
New-AzureVNetGateway -VNetName "MyAzureVNET" -GatewayType DynamicRouting -GatewaySKU  Standard
```

## <a name="verify-the-gateway-was-created"></a>Certifique-se de que a criação do gateway

Utilize o comando abaixo para verificar que o gateway foi criado. Este comando obtém também o ID de gateway, o que precisa para outras operações.

```powershell
Get-AzureVNetGateway
```

## <a name="resize-a-gateway"></a>Redimensionar um gateway

Existem diversas [SKUs de Gateway](../articles/expressroute/expressroute-about-virtual-network-gateways.md). Pode utilizar o seguinte comando para alterar o SKU de Gateway em qualquer altura.

> [!IMPORTANT]
> Este comando não funcionar para o gateway de UltraPerformance. Para alterar o gateway para um gateway de UltraPerformance, remova primeiro o gateway do ExpressRoute existente e, em seguida, crie um novo gateway de UltraPerformance. Para mudar o gateway de um gateway de UltraPerformance, para remover primeiro o gateway de UltraPerformance e, em seguida, crie um novo gateway.
>
>

```powershell
Resize-AzureVNetGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance
```

## <a name="remove-a-gateway"></a>Remover um gateway

Utilize o comando abaixo para remover um gateway

```powershell
Remove-AzureVnetGateway -GatewayId <Gateway ID>
```