---
title: Configure a autenticação mútua no Gateway de Aplicações Azure através do PowerShell
description: Saiba como configurar uma Porta de Aplicação para ter autenticação mútua através do PowerShell
services: application-gateway
author: mscatyao
ms.service: application-gateway
ms.topic: how-to
ms.date: 04/02/2021
ms.author: caya
ms.openlocfilehash: 95534760c09ca9e1f7f09d6079886216127c7eb0
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2021
ms.locfileid: "106231511"
---
# <a name="configure-mutual-authentication-with-application-gateway-through-powershell-preview"></a>Configure a autenticação mútua com o Gateway de Aplicação através do PowerShell (Preview)
Este artigo descreve como utilizar o PowerShell para configurar a autenticação mútua no seu Gateway de Aplicação. Autenticação mútua significa que o Application Gateway autentica o cliente que envia o pedido utilizando o certificado de cliente que envia para o Gateway de Aplicações. 

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Este artigo requer a versão 1.0 ou mais tarde do módulo Azure PowerShell. Executar `Get-Module -ListAvailable Az` para localizar a versão. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar o PowerShell localmente, também precisa de correr `Login-AzAccount` para criar uma ligação com o Azure.

## <a name="before-you-begin"></a>Antes de começar

Para configurar a autenticação mútua com um Gateway de aplicação, precisa de um certificado de cliente para fazer o upload para o gateway. O certificado de cliente será usado para validar o certificado que o cliente irá apresentar à Application Gateway. Para efeitos de teste, pode utilizar um certificado auto-assinado. No entanto, isto não é aconselhável para cargas de trabalho de produção, porque são mais difíceis de gerir e não são completamente seguros.

Para saber mais, especialmente sobre que tipo de certificados de cliente pode carregar, consulte [visão geral da autenticação mútua com o Application Gateway.](./mutual-authentication-overview.md#certificates-supported-for-mutual-authentication)

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Primeiro crie um novo grupo de recursos na sua subscrição. 

```azurepowershell
$resourceGroup = New-AzResourceGroup -Name $rgname -Location $location -Tags @{ testtag = "APPGw tag"}
```
## <a name="create-a-virtual-network"></a>Criar uma rede virtual

Implemente uma rede virtual para o seu Gateway de Aplicações a ser implantado.

```azurepowershell
$gwSubnet = New-AzVirtualNetworkSubnetConfig -Name $gwSubnetName -AddressPrefix 10.0.0.0/24
$vnet = New-AzVirtualNetwork -Name $vnetName -ResourceGroupName $rgname -Location $location -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet
$vnet = Get-AzVirtualNetwork -Name $vnetName -ResourceGroupName $rgname
$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name $gwSubnetName -VirtualNetwork $vnet
```

## <a name="create-a-public-ip"></a>Criar um IP público

Crie um IP público para utilizar com o seu Gateway de Aplicações. 

```azurepowershell
$publicip = New-AzPublicIpAddress -ResourceGroupName $rgname -name $publicIpName -location $location -AllocationMethod Static -sku Standard
```

## <a name="create-the-application-gateway-ip-configuration"></a>Criar a configuração IP do Gateway de Aplicação

Crie as configurações IP e a porta frontal. 

```azurepowershell
$gipconfig = New-AzApplicationGatewayIPConfiguration -Name $gipconfigname -Subnet $gwSubnet
$fipconfig = New-AzApplicationGatewayFrontendIPConfig -Name $fipconfigName -PublicIPAddress $publicip
$port = New-AzApplicationGatewayFrontendPort -Name $frontendPortName  -Port 443
```

## <a name="configure-frontend-ssl"></a>Configure frontend SSL 

Configure os certificados SSL para o seu Gateway de Aplicação.

```azurepowershell
$password = ConvertTo-SecureString "P@ssw0rd" -AsPlainText -Force
$sslCertPath = $basedir + "/ScenarioTests/Data/ApplicationGatewaySslCert1.pfx"
$sslCert = New-AzApplicationGatewaySslCertificate -Name $sslCertName -CertificateFile $sslCertPath -Password $password
```

## <a name="configure-client-authentication"></a>Configurar a autenticação de cliente 

Configure a autenticação do cliente no seu Gateway de Aplicações. Para obter mais informações sobre como extrair cadeias de certificados de ca de cliente fidedignos para usar aqui, consulte [como extrair cadeias de certificados de cliente confiável](./mutual-authentication-certificate-management.md).

> [!IMPORTANT]
> Certifique-se de que carrega toda a cadeia de certificados ca do cliente num ficheiro e apenas uma cadeia por ficheiro.  

> [!NOTE]
> Recomendamos a utilização de TLS 1.2 com autenticação mútua, uma vez que o TLS 1.2 será mandatado no futuro. 

```azurepowershell
$clientCertFilePath = $basedir + "/ScenarioTests/Data/TrustedClientCertificate.cer"
$trustedClient01 = New-AzApplicationGatewayTrustedClientCertificate -Name $trustedClientCert01Name -CertificateFile $clientCertFilePath
$sslPolicy = New-AzApplicationGatewaySslPolicy -PolicyType Predefined -PolicyName "AppGwSslPolicy20170401S"
$clientAuthConfig = New-AzApplicationGatewayClientAuthConfiguration -VerifyClientCertIssuerDN
$sslProfile01 = New-AzApplicationGatewaySslProfile -Name $sslProfile01Name -SslPolicy $sslPolicy -ClientAuthConfiguration $clientAuthConfig -TrustedClientCertificates $trustedClient01
$listener = New-AzApplicationGatewayHttpListener -Name $listenerName -Protocol Https -SslCertificate $sslCert -FrontendIPConfiguration $fipconfig -FrontendPort $port -SslProfile $sslProfile01
```

## <a name="configure-the-backend-pool-and-settings"></a>Configure a piscina de backend e as configurações

Configurar a piscina de backend e as definições para o seu Gateway de Aplicações. Opcionalmente, crie o certificado de raiz fidedigno de backend para encriptação SSL de ponta a ponta.  

```azurepowershell
$certFilePath = $basedir + "/ScenarioTests/Data/ApplicationGatewayAuthCert.cer"
$trustedRoot = New-AzApplicationGatewayTrustedRootCertificate -Name $trustedRootCertName -CertificateFile $certFilePath
$pool = New-AzApplicationGatewayBackendAddressPool -Name $poolName -BackendIPAddresses www.microsoft.com, www.bing.com
$poolSetting = New-AzApplicationGatewayBackendHttpSettings -Name $poolSettingName -Port 443 -Protocol Https -CookieBasedAffinity Enabled -PickHostNameFromBackendAddress -TrustedRootCertificate $trustedRoot
```

## <a name="configure-the-rule"></a>Configurar a regra

Estabeleça uma regra sobre o seu Gateway de Aplicação.

```azurepowershell
$rule = New-AzApplicationGatewayRequestRoutingRule -Name $ruleName -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
```

## <a name="set-up-default-ssl-policy-for-future-listeners"></a>Configurar a política SSL predefinida para futuros ouvintes

Criou uma política específica de SSL para o ouvinte enquanto cria a autenticação mútua. Neste passo, pode configurar opcionalmente a política SSL padrão para futuros ouvintes que criar. 

```azurepowershell
$sslPolicyGlobal = New-AzApplicationGatewaySslPolicy -PolicyType Predefined -PolicyName "AppGwSslPolicy20170401"
```

## <a name="create-the-application-gateway"></a>Criar o Gateway de Aplicações

Utilizando tudo o que criámos acima, implemente o seu Gateway de Aplicação.

```azurepowershell
$sku = New-AzApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2
$appgw = New-AzApplicationGateway -Name $appgwName -ResourceGroupName $rgname -Zone 1,2 -Location $location -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $port -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SslPolicy $sslPolicyGlobal -TrustedRootCertificate $trustedRoot -AutoscaleConfiguration $autoscaleConfig -TrustedClientCertificates $trustedClient01 -SslProfiles $sslProfile01 -SslCertificates $sslCert
```

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não for necessário, remova o grupo de recursos, o gateway de aplicação e todos os recursos relacionados utilizando [o Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

```azurepowershell
Remove-AzResourceGroup -Name $rgname
```

## <a name="renew-expired-client-ca-certificates"></a>Renovar certificados de CA de cliente caducado

Caso o certificado de CA do seu cliente tenha expirado, pode atualizar o certificado no seu gateway através dos seguintes passos: 

1. Iniciar sessão no Azure
    ```azurepowershell
    Connect-AzAccount
    Select-AzSubscription -Subscription "<sub name>"
    ```
2. Obtenha a configuração do Gateway de Aplicação
    ```azurepowershell
    $gateway = Get-AzApplicationGateway -Name "<gateway-name>" -ResourceGroupName "<resource-group-name>"
    ```
3. Remova o certificado de cliente fidedigno do gateway 
    ```azurepowershell
    Remove-AzApplicationGatewayTrustedClientCertificate -Name "<name-of-client-certificate>" -ApplicationGateway $gateway
    ``` 
4. Adicione o novo certificado no portal 
    ```azurepowershell
    Add-AzApplicationGatewayTrustedClientCertificate -ApplicationGateway $gateway -Name "<name-of-new-cert>" -CertificateFile "<path-to-certificate-file>"
    ```
5. Atualize o portal com o novo certificado 
    ```azurepowershell
    Set-AzApplicationGateway -ApplicationGateway $gateway
    ```

## <a name="next-steps"></a>Passos seguintes

- [Gerir o tráfego da Web com um gateway de aplicação com a CLI do Azure](./tutorial-manage-web-traffic-cli.md)