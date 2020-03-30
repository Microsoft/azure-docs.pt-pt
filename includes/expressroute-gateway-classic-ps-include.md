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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67183993"
---
> [!NOTE]
> Estes exemplos não se aplicam às configurações coexistais S2S/ExpressRoute.
> Para obter mais informações sobre o trabalho com gateways numa configuração coexistuinte, consulte [as ligações coexistentes da Configuração.](../articles/expressroute/expressroute-howto-coexist-classic.md#gw)

## <a name="add-a-gateway"></a>Adicionar um gateway

Quando adiciona uma porta de entrada a uma rede virtual utilizando o modelo de recurso clássico, modifica o ficheiro de configuração da rede imediatamente antes de criar o gateway. Os valores nos exemplos abaixo devem estar presentes no ficheiro para criar uma porta de entrada. Se a sua rede virtual já tinha uma porta de entrada associada, alguns destes valores já estarão presentes. Modifique o ficheiro para refletir os valores abaixo.

### <a name="download-the-network-configuration-file"></a>Descarregue o ficheiro de configuração da rede

1. Descarregue o ficheiro de configuração da rede utilizando os passos no artigo de ficheiro de configuração da [rede.](../articles/virtual-network/virtual-networks-using-network-configuration-file.md) Abra o ficheiro usando um editor de texto.
2. Adicione um site de rede local ao ficheiro. Pode utilizar qualquer prefixo de endereço válido. Pode adicionar qualquer endereço IP válido para o gateway VPN. Os valores de endereço nesta secção não são utilizados para operações expressRoute, mas são necessários para validação de ficheiros. No exemplo, "branch1" é o nome do site. Pode utilizar um nome diferente, mas certifique-se de que utiliza o mesmo valor na secção Gateway do ficheiro.

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
3. Navegue para os Sites VirtualNetwork e modifique os campos.

   * Verifique se a Subnet Gateway existe para a sua rede virtual. Se não, pode adicionar um neste momento. O nome deve ser "GatewaySubnet".
   * Verifique se existe a secção Gateway do ficheiro. Se não, adicione. Isto é necessário para associar a rede virtual ao site da rede local (que representa a rede à qual está a ligar).
   * Verifique se o tipo de ligação = Dedicado. Isto é necessário para ligações ExpressRoute.

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
4. Guarde o ficheiro e carregue-o para o Azure.

### <a name="create-the-gateway"></a>Criar o portal

Use o comando abaixo para criar um portal. Substitua quaisquer valores pelos seus.

```powershell
New-AzureVNetGateway -VNetName "MyAzureVNET" -GatewayType DynamicRouting -GatewaySKU  Standard
```

## <a name="verify-the-gateway-was-created"></a>Verifique se o portal foi criado

Utilize o comando abaixo para verificar se o portal foi criado. Este comando também recupera o ID do portal, que você precisa para outras operações.

```powershell
Get-AzureVNetGateway
```

## <a name="resize-a-gateway"></a>Redimensionar um portal

Há uma série de [SKUs gateway.](../articles/expressroute/expressroute-about-virtual-network-gateways.md) Pode utilizar o seguinte comando para alterar o Gateway SKU a qualquer momento.

> [!IMPORTANT]
> Este comando não funciona para o portal UltraPerformance. Para alterar a sua porta de entrada para um portal UltraPerformance, primeiro remova o gateway ExpressRoute existente e, em seguida, crie um novo portal UltraPerformance. Para desvalorizar o seu portal de entrada a partir de um gateway UltraPerformance, primeiro remova o gateway UltraPerformance e, em seguida, crie um novo gateway.
>
>

```powershell
Resize-AzureVNetGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance
```

## <a name="remove-a-gateway"></a>Remover um gateway

Use o comando abaixo para remover um portal

```powershell
Remove-AzureVnetGateway -GatewayId <Gateway ID>
```