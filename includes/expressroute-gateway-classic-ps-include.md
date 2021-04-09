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
ms.openlocfilehash: c0c05eb88df27ba9e71c0f61bad75457d893a7ce
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "95557201"
---
> [!NOTE]
> Estes exemplos não se aplicam às configurações de coexistência S2S/ExpressRoute.
> Para obter mais informações sobre o trabalho com gateways numa configuração coexistindo, consulte [as conexões coexistindo Configure.](../articles/expressroute/expressroute-howto-coexist-classic.md#gw)

## <a name="add-a-gateway"></a>Adicionar um gateway

Quando adiciona um gateway a uma rede virtual utilizando o modelo clássico de recursos, modifica o ficheiro de configuração da rede diretamente antes de criar o gateway. Os valores nos exemplos abaixo devem estar presentes no ficheiro para criar um gateway. Se a sua rede virtual já tinha um portal associado, alguns destes valores já estarão presentes. Modifique o ficheiro para refletir os valores abaixo.

### <a name="download-the-network-configuration-file"></a>Descarregue o ficheiro de configuração da rede

1. Descarregue o ficheiro de configuração da rede utilizando os passos no artigo [de ficheiro de configuração de rede.](/previous-versions/azure/virtual-network/virtual-networks-using-network-configuration-file) Abra o ficheiro utilizando um editor de texto.
2. Adicione um site de rede local ao ficheiro. Pode utilizar qualquer prefixo de endereço válido. Pode adicionar qualquer endereço IP válido para o gateway VPN. Os valores de endereço nesta secção não são utilizados para operações ExpressRoute, mas são necessários para validação de ficheiros. No exemplo, "branch1" é o nome do site. Pode usar um nome diferente, mas certifique-se de que utiliza o mesmo valor na secção Gateway do ficheiro.

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
3. Navegue nos VirtualNetworkSites e modifique os campos.

   * Verifique se existe a sub-rede Gateway para a sua rede virtual. Se não, pode adicionar um neste momento. O nome deve ser "GatewaySubnet".
   * Verifique se a secção Gateway do ficheiro existe. Se não, adicione. Isto é necessário para associar a rede virtual ao site de rede local (que representa a rede à qual está a ligar).
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
4. Guarde o ficheiro e faça o upload para o Azure.

### <a name="create-the-gateway"></a>Criar o portal

Use o comando abaixo para criar um portal. Substitua quaisquer valores pelos seus próprios valores.

```powershell
New-AzureVNetGateway -VNetName "MyAzureVNET" -GatewayType DynamicRouting -GatewaySKU  Standard
```

## <a name="verify-the-gateway-was-created"></a>Verifique se o portal foi criado

Utilize o comando abaixo para verificar se o gateway foi criado. Este comando também recupera o ID do gateway, que você precisa para outras operações.

```powershell
Get-AzureVNetGateway
```

## <a name="resize-a-gateway"></a>Resize a gateway (Redimensionar um gateway)

Há uma série de [SKUs gateway.](../articles/expressroute/expressroute-about-virtual-network-gateways.md) Pode utilizar o seguinte comando para alterar o Gateway SKU a qualquer momento.

> [!IMPORTANT]
> Este comando não funciona para o Portal UltraPerformance. Para alterar a sua porta de entrada para um gateway UltraPerformance, primeiro remova o gateway ExpressRoute existente e, em seguida, crie um novo gateway UltraPerformance. Para desclassificar o seu portal a partir de um gateway UltraPerformance, primeiro remova o gateway UltraPerformance e, em seguida, crie um novo portal.
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