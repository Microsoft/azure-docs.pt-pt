---
title: Configure a política TLS usando powerShell
titleSuffix: Azure Application Gateway
description: Este artigo fornece instruções para configurar a Política de TLS sobre gateway de aplicação azure
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: 3804059fdd818f10663d14bde72da2c6773fa53f
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81312679"
---
# <a name="configure-tls-policy-versions-and-cipher-suites-on-application-gateway"></a>Configure versões políticas tLS e suítes cifra sintetizadas no Application Gateway

Saiba como configurar versões políticas TLS/SSL e suítes cifra no Application Gateway. Pode selecionar a partir de uma lista de políticas predefinidas que contêm diferentes configurações de versões políticas TLS e suítes cifras habilitadas. Também tem a capacidade de definir uma política personalizada de [TLS](#configure-a-custom-tls-policy) com base nos seus requisitos.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="get-available-tls-options"></a>Obtenha opções TLS disponíveis

O `Get-AzApplicationGatewayAvailableSslOptions` cmdlet fornece uma listagem de políticas pré-definidas disponíveis, suítes de cifra disponíveis e versões protocolares que podem ser configuradas. O exemplo que se segue mostra uma saída de exemplo de execução do cmdlet.

```
DefaultPolicy: AppGwSslPolicy20150501
PredefinedPolicies:
    /subscriptions/147a22e9-2356-4e56-b3de-1f5842ae4a3b/resourceGroups//providers/Microsoft.Network/ApplicationGatewayAvailableSslOptions/default/Applic
ationGatewaySslPredefinedPolicy/AppGwSslPolicy20150501
    /subscriptions/147a22e9-2356-4e56-b3de-1f5842ae4a3b/resourceGroups//providers/Microsoft.Network/ApplicationGatewayAvailableSslOptions/default/Applic
ationGatewaySslPredefinedPolicy/AppGwSslPolicy20170401
    /subscriptions/147a22e9-2356-4e56-b3de-1f5842ae4a3b/resourceGroups//providers/Microsoft.Network/ApplicationGatewayAvailableSslOptions/default/Applic
ationGatewaySslPredefinedPolicy/AppGwSslPolicy20170401S

AvailableCipherSuites:
    TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
    TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
    TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
    TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
    TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
    TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
    TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
    TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
    TLS_DHE_RSA_WITH_AES_256_CBC_SHA
    TLS_DHE_RSA_WITH_AES_128_CBC_SHA
    TLS_RSA_WITH_AES_256_GCM_SHA384
    TLS_RSA_WITH_AES_128_GCM_SHA256
    TLS_RSA_WITH_AES_256_CBC_SHA256
    TLS_RSA_WITH_AES_128_CBC_SHA256
    TLS_RSA_WITH_AES_256_CBC_SHA
    TLS_RSA_WITH_AES_128_CBC_SHA
    TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
    TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
    TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
    TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
    TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
    TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
    TLS_DHE_DSS_WITH_AES_256_CBC_SHA256
    TLS_DHE_DSS_WITH_AES_128_CBC_SHA256
    TLS_DHE_DSS_WITH_AES_256_CBC_SHA
    TLS_DHE_DSS_WITH_AES_128_CBC_SHA
    TLS_RSA_WITH_3DES_EDE_CBC_SHA
    TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA

AvailableProtocols:
    TLSv1_0
    TLSv1_1
    TLSv1_2
```

## <a name="list-pre-defined-tls-policies"></a>Lista de Políticas TLS pré-definidas

O gateway da aplicação vem com três políticas pré-definidas que podem ser usadas. O `Get-AzApplicationGatewaySslPredefinedPolicy` cmdlet recupera estas políticas. Cada política tem diferentes versões protocolares e suítes cifras habilitadas. Estas políticas pré-definidas podem ser usadas para configurar rapidamente uma política de TLS no seu gateway de aplicação. Por padrão, **o AppGwSslPolicy20150501** é selecionado se não for definida nenhuma política específica de TLS.

A saída seguinte é `Get-AzApplicationGatewaySslPredefinedPolicy`um exemplo de execução .

```
Name: AppGwSslPolicy20150501
MinProtocolVersion: TLSv1_0
CipherSuites:
    TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
    TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
    TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
    TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
    TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
    TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
    TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
    TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
    TLS_DHE_RSA_WITH_AES_256_CBC_SHA
    TLS_DHE_RSA_WITH_AES_128_CBC_SHA
    TLS_RSA_WITH_AES_256_GCM_SHA384
 ...
Name: AppGwSslPolicy20170401
MinProtocolVersion: TLSv1_1
CipherSuites:
    TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
    TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
    TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
    TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
    TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
    TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
    TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
...
```

## <a name="configure-a-custom-tls-policy"></a>Configure uma política personalizada de TLS

Ao configurar uma política personalizada de TLS, passa pelos seguintes parâmetros: PolicyType, MinProtocolVersion, CipherSuite e ApplicationGateway. Se tentar passar outros parâmetros, obtém um erro ao criar ou atualizar o Gateway da Aplicação. 

O exemplo que se segue define uma política de TLS personalizada num gateway de aplicação. Define a versão protocolo `TLSv1_1` mínima e permite as seguintes suítes cifra:

* TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
* TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256

> [!IMPORTANT]
> TLS_RSA_WITH_AES_256_CBC_SHA256 deve ser selecionado ao configurar uma política de TLS personalizada. O gateway de aplicação utiliza esta suite de cifra para gestão de backend. Você pode usá-lo em combinação com quaisquer outras suites, mas esta deve ser selecionada também. 

```powershell
# get an application gateway resource
$gw = Get-AzApplicationGateway -Name AdatumAppGateway -ResourceGroup AdatumAppGatewayRG

# set the TLS policy on the application gateway
Set-AzApplicationGatewaySslPolicy -ApplicationGateway $gw -PolicyType Custom -MinProtocolVersion TLSv1_1 -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256"

# validate the TLS policy locally
Get-AzApplicationGatewaySslPolicy -ApplicationGateway $gw

# update the gateway with validated TLS policy
Set-AzApplicationGateway -ApplicationGateway $gw
```

## <a name="create-an-application-gateway-with-a-pre-defined-tls-policy"></a>Criar um portal de aplicação com uma política de TLS pré-definida

Ao configurar uma política DeTLS predefinida, passa pelos seguintes parâmetros: PolicyType, PolicyName e ApplicationGateway. Se tentar passar outros parâmetros, obtém um erro ao criar ou atualizar o Gateway da Aplicação.

O exemplo seguinte cria uma nova porta de aplicação com uma política de TLS pré-definida.

```powershell
# Create a resource group
$rg = New-AzResourceGroup -Name ContosoRG -Location "East US"

# Create a subnet for the application gateway
$subnet = New-AzVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

# Create a virtual network with a 10.0.0.0/16 address space
$vnet = New-AzVirtualNetwork -Name appgwvnet -ResourceGroupName $rg.ResourceGroupName -Location "East US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet

# Retrieve the subnet object for later use
$subnet = $vnet.Subnets[0]

# Create a public IP address
$publicip = New-AzPublicIpAddress -ResourceGroupName $rg.ResourceGroupName -name publicIP01 -location "East US" -AllocationMethod Dynamic

# Create an ip configuration object
$gipconfig = New-AzApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

# Create a backend pool for backend web servers
$pool = New-AzApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50

# Define the backend http settings to be used.
$poolSetting = New-AzApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Enabled

# Create a new port for TLS
$fp = New-AzApplicationGatewayFrontendPort -Name frontendport01  -Port 443

# Upload an existing pfx certificate for TLS offload
$password = ConvertTo-SecureString -String "P@ssw0rd" -AsPlainText -Force
$cert = New-AzApplicationGatewaySslCertificate -Name cert01 -CertificateFile C:\folder\contoso.pfx -Password $password

# Create a frontend IP configuration for the public IP address
$fipconfig = New-AzApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

# Create a new listener with the certificate, port, and frontend ip.
$listener = New-AzApplicationGatewayHttpListener -Name listener01  -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert

# Create a new rule for backend traffic routing
$rule = New-AzApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

# Define the size of the application gateway
$sku = New-AzApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

# Configure the TLS policy to use a different pre-defined policy
$policy = New-AzApplicationGatewaySslPolicy -PolicyType Predefined -PolicyName AppGwSslPolicy20170401S

# Create the application gateway.
$appgw = New-AzApplicationGateway -Name appgwtest -ResourceGroupName $rg.ResourceGroupName -Location "East US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SslCertificates $cert -SslPolicy $policy
```

## <a name="update-an-existing-application-gateway-with-a-pre-defined-tls-policy"></a>Atualizar um gateway de aplicação existente com uma política de TLS pré-definida

Para definir uma política personalizada de TLS, passe os seguintes parâmetros: **PolicyType**, **MinProtocolVersion,** **CipherSuite**, e **ApplicationGateway**. Para definir uma política De TLS predefinida, passe os seguintes parâmetros: **PolicyType**, **PolicyName**, e **ApplicationGateway**. Se tentar passar outros parâmetros, obtém um erro ao criar ou atualizar o Gateway da Aplicação.

No exemplo seguinte, existem amostras de código tanto para a Política Personalizada como para a Política Predefinida. Não comente a política que pretende usar.

```powershell
# You have to change these parameters to match your environment.
$AppGWname = "YourAppGwName"
$RG = "YourResourceGroupName"

$AppGw = get-Azapplicationgateway -Name $AppGWname -ResourceGroupName $RG

# Choose either custom policy or predefined policy and uncomment the one you want to use.

# TLS Custom Policy
# Set-AzApplicationGatewaySslPolicy -PolicyType Custom -MinProtocolVersion TLSv1_2 -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_RSA_WITH_AES_128_CBC_SHA256" -ApplicationGateway $AppGw

# TLS Predefined Policy
# Set-AzApplicationGatewaySslPolicy -PolicyType Predefined -PolicyName "AppGwSslPolicy20170401S" -ApplicationGateway $AppGW

# Update AppGW
# The TLS policy options are not validated or updated on the Application Gateway until this cmdlet is executed.
$SetGW = Set-AzApplicationGateway -ApplicationGateway $AppGW
```

## <a name="next-steps"></a>Passos seguintes

Visite a visão geral do Gateway de [Aplicações](application-gateway-redirect-overview.md) para aprender a redirecionar o tráfego HTTP para um ponto final HTTPS.
