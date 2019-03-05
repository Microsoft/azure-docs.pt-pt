---
title: Criar uma sonda personalizada - Gateway de aplicação do Azure - PowerShell | Documentos da Microsoft
description: Saiba como criar uma sonda personalizada para o Gateway de aplicação com o PowerShell no Resource Manager
services: application-gateway
documentationcenter: na
author: vhorne
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.assetid: 68feb660-7fa4-4f69-a7e4-bdd7bdc474db
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: victorh
ms.openlocfilehash: b5ea7822d96e45d61ecef384d15d78e6a75fa46c
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57310738"
---
# <a name="create-a-custom-probe-for-azure-application-gateway-by-using-powershell-for-azure-resource-manager"></a>Criar uma sonda personalizada para o Gateway de aplicação do Azure com o PowerShell para o Azure Resource Manager

> [!div class="op_single_selector"]
> * [Portal do Azure](application-gateway-create-probe-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-probe-ps.md)
> * [Azure Classic PowerShell](application-gateway-create-probe-classic-ps.md)

Neste artigo, é possível adicionar uma sonda personalizada para um gateway de aplicação existente com o PowerShell. As pesquisas personalizadas são úteis para aplicações que têm uma página de verificação de estado de funcionamento específico ou para aplicativos que não fornecem uma resposta com êxito na aplicação web predefinida.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-an-application-gateway-with-a-custom-probe"></a>Criar um gateway de aplicação com uma sonda personalizada

### <a name="sign-in-and-create-resource-group"></a>Iniciar sessão e criar o grupo de recursos

1. Utilize `Connect-AzAccount` para autenticar.

  ```powershell
  Connect-AzAccount
  ```

1. Obter as subscrições para a conta.

  ```powershell
  Get-AzSubscription
  ```

1. Escolha qual das subscrições do Azure utilizar.

  ```powershell
  Select-AzSubscription -Subscriptionid '{subscriptionGuid}'
  ```

1. Crie um grupo de recursos. Pode ignorar este passo se tiver um grupo de recursos existente.

  ```powershell
  New-AzResourceGroup -Name appgw-rg -Location 'West US'
  ```

O Azure Resource Manager requer que todos os grupos de recursos especifiquem uma localização, que é utilizada como a localização predefinida para os recursos nesse grupo de recursos. Certifique-se de que todos os comandos para criar um gateway de aplicação utilizam o mesmo grupo de recursos.

No exemplo anterior, criámos um grupo de recursos chamado **appgw-RG** numa localização **E.U.A. oeste**.

### <a name="create-a-virtual-network-and-a-subnet"></a>Crie uma rede virtual e uma sub-rede

O exemplo seguinte cria uma rede virtual e uma sub-rede para o gateway de aplicação. Gateway de aplicação requer sua própria sub-rede, para utilização. Por esse motivo, a sub-rede criada para o gateway de aplicação deve ser menor do que o espaço de endereços da VNET para permitir a outras sub-redes ser criada e utilizada.

```powershell
# Assign the address range 10.0.0.0/24 to a subnet variable to be used to create a virtual network.
$subnet = New-AzVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

# Create a virtual network named appgwvnet in resource group appgw-rg for the West US region using the prefix 10.0.0.0/16 with subnet 10.0.0.0/24.
$vnet = New-AzVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $subnet

# Assign a subnet variable for the next steps, which create an application gateway.
$subnet = $vnet.Subnets[0]
```

### <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Criar um endereço IP público para a configuração de front-end

Crie um recurso de IP público **publicIP01** no grupo de recursos **appgw-rg** para a região E.U.A. Oeste. Este exemplo utiliza um endereço IP público para o endereço IP Front-end do gateway de aplicação.  Gateway de aplicação requer o endereço IP público para ter um nome DNS criado dinamicamente, portanto, o `-DomainNameLabel` não é possível especificar durante a criação do endereço IP público.

```powershell
$publicip = New-AzPublicIpAddress -ResourceGroupName appgw-rg -Name publicIP01 -Location 'West US' -AllocationMethod Dynamic
```

### <a name="create-an-application-gateway"></a>Criar um gateway de aplicação

Configurar a todos os itens de configuração antes de criar o gateway de aplicação. O exemplo seguinte cria os itens de configuração que são necessários para um recurso de gateway de aplicação.

| **Componente** | **Descrição** |
|---|---|
| **Configuração de IP do gateway** | Uma configuração de IP para um gateway de aplicação.|
| **Conjunto back-end** | Um conjunto de endereços IP, FQDNS ou NICs são para os servidores de aplicações que alojam a aplicação web|
| **Sonda de estado de funcionamento** | Uma sonda personalizada utilizada para monitorizar o estado de funcionamento dos membros do agrupamento de back-end|
| **Definições de HTTP** | Uma coleção de definições, incluindo, porta, protocolo, com base no cookie de afinidade, sonda e tempo limite.  Estas definições determinam a forma como o tráfego é encaminhado para os membros do conjunto de back-end|
| **Porta de front-end** | A porta que o gateway de aplicação escuta tráfego no|
| **Serviço de escuta** | Uma combinação de um protocolo, a configuração de IP de front-end e a porta de front-end. Este é o que escuta pedidos recebidos.
|**Regra**| Rotas o tráfego para o back-end adequado com base nas definições de HTTP.|

```powershell
# Creates a application gateway Frontend IP configuration named gatewayIP01
$gipconfig = New-AzApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

#Creates a back-end IP address pool named pool01 with IP addresses 134.170.185.46, 134.170.188.221, 134.170.185.50.
$pool = New-AzApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221, 134.170.185.50

# Creates a probe that will check health at http://contoso.com/path/path.htm
$probe = New-AzApplicationGatewayProbeConfig -Name probe01 -Protocol Http -HostName 'contoso.com' -Path '/path/path.htm' -Interval 30 -Timeout 120 -UnhealthyThreshold 8

# Creates the backend http settings to be used. This component references the $probe created in the previous command.
$poolSetting = New-AzApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 80

# Creates a frontend port for the application gateway to listen on port 80 that will be used by the listener.
$fp = New-AzApplicationGatewayFrontendPort -Name frontendport01 -Port 80

# Creates a frontend IP configuration. This associates the $publicip variable defined previously with the front-end IP that will be used by the listener.
$fipconfig = New-AzApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

# Creates the listener. The listener is a combination of protocol and the frontend IP configuration $fipconfig and frontend port $fp created in previous steps.
$listener = New-AzApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

# Creates the rule that routes traffic to the backend pools.  In this example we create a basic rule that uses the previous defined http settings and backend address pool.  It also associates the listener to the rule
$rule = New-AzApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

# Sets the SKU of the application gateway, in this example we create a small standard application gateway with 2 instances.
$sku = New-AzApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

# The final step creates the application gateway with all the previously defined components.
$appgw = New-AzApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location 'West US' -BackendAddressPools $pool -Probes $probe -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku
```

## <a name="add-a-probe-to-an-existing-application-gateway"></a>Adicionar uma pesquisa a um gateway de aplicação existente

O fragmento de código seguinte adiciona uma sonda para um gateway de aplicação existente.

```powershell
# Load the application gateway resource into a PowerShell variable by using Get-AzApplicationGateway.
$getgw =  Get-AzApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

# Create the probe object that will check health at http://contoso.com/path/path.htm
$getgw = Add-AzApplicationGatewayProbeConfig -ApplicationGateway $getgw -Name probe01 -Protocol Http -HostName 'contoso.com' -Path '/path/custompath.htm' -Interval 30 -Timeout 120 -UnhealthyThreshold 8

# Set the backend HTTP settings to use the new probe
$getgw = Set-AzApplicationGatewayBackendHttpSettings -ApplicationGateway $getgw -Name $getgw.BackendHttpSettingsCollection.name -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 120

# Save the application gateway with the configuration changes
Set-AzApplicationGateway -ApplicationGateway $getgw
```

## <a name="remove-a-probe-from-an-existing-application-gateway"></a>Remover uma sonda de um gateway de aplicação existente

O seguinte trecho de código remove uma sonda de um gateway de aplicação existente.

```powershell
# Load the application gateway resource into a PowerShell variable by using Get-AzApplicationGateway.
$getgw =  Get-AzApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

# Remove the probe from the application gateway configuration object
$getgw = Remove-AzApplicationGatewayProbeConfig -ApplicationGateway $getgw -Name $getgw.Probes.name

# Set the backend HTTP settings to remove the reference to the probe. The backend http settings now use the default probe
$getgw = Set-AzApplicationGatewayBackendHttpSettings -ApplicationGateway $getgw -Name $getgw.BackendHttpSettingsCollection.name -Port 80 -Protocol http -CookieBasedAffinity Disabled

# Save the application gateway with the configuration changes
Set-AzApplicationGateway -ApplicationGateway $getgw
```

## <a name="get-application-gateway-dns-name"></a>Obter nome de DNS de gateway de aplicação

Depois de o gateway ser criado, o passo seguinte consiste em configurar o front-end para a comunicação. Ao utilizar um IP público, o gateway de aplicação requer um nome DNS dinamicamente atribuído, que não é amigável. Para garantir que os utilizadores finais alcançam o gateway de aplicação, pode utilizar-se um registo CNAME para apontar para o ponto final público do gateway de aplicação. [Configurar um nome de domínio personalizado no Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). Para o fazer, obtenha detalhes sobre o gateway de aplicação e o seu nome DNS/IP associado com o elemento PublicIPAddress ligado ao gateway de aplicação. O nome DNS do gateway de aplicação deve servir para criar um registo CNAME, que aponta as duas aplicações Web para este nome DNS. Não é recomendada a utilização de registos A, uma vez que o VIP pode ser alterado no reinício do gateway de aplicação.

```powershell
Get-AzPublicIpAddress -ResourceGroupName appgw-RG -Name publicIP01
```

```
Name                     : publicIP01
ResourceGroupName        : appgw-RG
Location                 : westus
Id                       : /subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/publicIPAddresses/publicIP01
Etag                     : W/"00000d5b-54ed-4907-bae8-99bd5766d0e5"
ResourceGuid             : 00000000-0000-0000-0000-000000000000
ProvisioningState        : Succeeded
Tags                     : 
PublicIpAllocationMethod : Dynamic
IpAddress                : xx.xx.xxx.xx
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : {
                                "Id": "/subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/applicationGateways/appgwtest/frontendIP
                            Configurations/frontend1"
                            }
DnsSettings              : {
                                "Fqdn": "00000000-0000-xxxx-xxxx-xxxxxxxxxxxx.cloudapp.net"
                            }
```

## <a name="next-steps"></a>Passos Seguintes

Saiba como configurar a descarga de SSL, visite a página: [Configurar a descarga de SSL](application-gateway-ssl-arm.md)

