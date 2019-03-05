---
title: 'Tutorial: Criar um gateway de aplicação de dimensionamento automático e redundância de zona, com um endereço IP reservado - Azure PowerShell'
description: Neste tutorial, saiba como criar um dimensionamento automático, o gateway de aplicação com redundância de zona com um endereço IP reservado com o Azure PowerShell.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 2/14/2019
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 616a710237c31ef2b4a19c3e1e61838164a78530
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57308572"
---
# <a name="tutorial-create-an-application-gateway-that-improves-web-application-access"></a>Tutorial: Criar um gateway de aplicação que melhora o acesso a aplicações web

Se for um administrador de TI, preocupados com a melhorar o acesso a aplicações web, pode otimizar o seu gateway de aplicação para dimensionar o cliente com base na procura e abrangem várias zonas de disponibilidade. Este tutorial ajuda-o a configurar recursos de Gateway de aplicação do Azure que fazê-lo: dimensionamento automático, a redundância de zona e o reservado VIPs (IP estático). Vai utilizar cmdlets do PowerShell do Azure e o modelo de implementação Azure Resource Manager para resolver o problema.

> [!IMPORTANT] 
> O SKU do gateway de aplicação de dimensionamento automático e com redundância entre zonas está atualmente em pré-visualização pública. Esta pré-visualização é disponibilizada sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Veja os [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter mais informações. 

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um certificado autoassinado
> * Criar uma rede virtual do dimensionamento automático
> * Criar um IP público reservado
> * Configurar a sua infraestrutura de gateway de aplicação
> * Especificar o dimensionamento automático
> * Criar o gateway de aplicação
> * Testar o gateway de aplicação

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Este tutorial exige que execute o Azure PowerShell localmente. Tem de ter versão 1.0.0 do módulo Azure PowerShell ou posterior instalado. Executar `Get-Module -ListAvailable Az` para localizar a versão. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](https://docs.microsoft.com/powershell/azure/install-az-ps). Depois de verificar a versão do PowerShell, execute `Connect-AzAccount` para criar uma ligação ao Azure.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos
Crie um grupo de recursos numa das localizações disponíveis.

```azurepowershell
$location = "East US 2"
$rg = "AppGW-rg"

#Create a new Resource Group
New-AzResourceGroup -Name $rg -Location $location
```

## <a name="create-a-self-signed-certificate"></a>Criar um certificado autoassinado

Para efeitos de produção, deve importar um certificado válido assinado por um fornecedor fidedigno. Neste tutorial, crie um certificado autoassinado com [New-SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate). Pode utilizar [Export-PfxCertificate](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate) com o Thumbprint que foi devolvido para exportar um ficheiro pfx do certificado.

```powershell
New-SelfSignedCertificate `
  -certstorelocation cert:\localmachine\my `
  -dnsname www.contoso.com
```

Deverá ver algo semelhante a este resultado:

```
PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\my

Thumbprint                                Subject
----------                                -------
E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630  CN=www.contoso.com
```

Utilize o thumbprint para criar o ficheiro pfx:

```powershell
$pwd = ConvertTo-SecureString -String "Azure123456!" -Force -AsPlainText

Export-PfxCertificate `
  -cert cert:\localMachine\my\E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630 `
  -FilePath c:\appgwcert.pfx `
  -Password $pwd
```

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

Crie uma rede virtual com uma sub-rede dedicada para um gateway de aplicação de dimensionamento automático. Atualmente, apenas um gateway de aplicação de dimensionamento automático pode ser implementado em cada sub-rede dedicada.

```azurepowershell
#Create VNet with two subnets
$sub1 = New-AzVirtualNetworkSubnetConfig -Name "AppGwSubnet" -AddressPrefix "10.0.0.0/24"
$sub2 = New-AzVirtualNetworkSubnetConfig -Name "BackendSubnet" -AddressPrefix "10.0.1.0/24"
$vnet = New-AzvirtualNetwork -Name "AutoscaleVNet" -ResourceGroupName $rg `
       -Location $location -AddressPrefix "10.0.0.0/16" -Subnet $sub1, $sub2
```

## <a name="create-a-reserved-public-ip"></a>Criar um IP público reservado

Especifique o método de alocação de PublicIPAddress como **estático**. Um VIP de gateway de aplicação de dimensionamento automático só pode ser estático. Os IPs dinâmicos não são suportados. Apenas o SKU PublicIpAddress standard é suportado.

```azurepowershell
#Create static public IP
$pip = New-AzPublicIpAddress -ResourceGroupName $rg -name "AppGwVIP" `
       -location $location -AllocationMethod Static -Sku Standard
```

## <a name="retrieve-details"></a>Obter os detalhes

Obtenha os detalhes de IP, uma sub-rede e o grupo de recursos num objeto local para criar os detalhes de configuração de IP para o gateway de aplicação.

```azurepowershell
$resourceGroup = Get-AzResourceGroup -Name $rg
$publicip = Get-AzPublicIpAddress -ResourceGroupName $rg -name "AppGwVIP"
$vnet = Get-AzvirtualNetwork -Name "AutoscaleVNet" -ResourceGroupName $rg
$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "AppGwSubnet" -VirtualNetwork $vnet
```

## <a name="configure-the-infrastructure"></a>Configurar a infraestrutura

Configure o IP config, configuração IP Front-end, conjunto de back-end, HTTP definições, certificado, porta, serviço de escuta e regra num formato idêntico para o gateway existente do aplicativo padrão. O novo SKU segue o mesmo modelo de objeto do SKU Standard.

```azurepowershell
$ipconfig = New-AzApplicationGatewayIPConfiguration -Name "IPConfig" -Subnet $gwSubnet
$fip = New-AzApplicationGatewayFrontendIPConfig -Name "FrontendIPCOnfig" -PublicIPAddress $publicip
$pool = New-AzApplicationGatewayBackendAddressPool -Name "Pool1" `
       -BackendIPAddresses testbackend1.westus.cloudapp.azure.com, testbackend2.westus.cloudapp.azure.com
$fp01 = New-AzApplicationGatewayFrontendPort -Name "SSLPort" -Port 443
$fp02 = New-AzApplicationGatewayFrontendPort -Name "HTTPPort" -Port 80

$securepfxpwd = ConvertTo-SecureString -String "Azure123456!" -AsPlainText -Force
$sslCert01 = New-AzApplicationGatewaySslCertificate -Name "SSLCert" -Password $securepfxpwd `
            -CertificateFile "c:\appgwcert.pfx"
$listener01 = New-AzApplicationGatewayHttpListener -Name "SSLListener" `
             -Protocol Https -FrontendIPConfiguration $fip -FrontendPort $fp01 -SslCertificate $sslCert01
$listener02 = New-AzApplicationGatewayHttpListener -Name "HTTPListener" `
             -Protocol Http -FrontendIPConfiguration $fip -FrontendPort $fp02

$setting = New-AzApplicationGatewayBackendHttpSettings -Name "BackendHttpSetting1" `
          -Port 80 -Protocol Http -CookieBasedAffinity Disabled
$rule01 = New-AzApplicationGatewayRequestRoutingRule -Name "Rule1" -RuleType basic `
         -BackendHttpSettings $setting -HttpListener $listener01 -BackendAddressPool $pool
$rule02 = New-AzApplicationGatewayRequestRoutingRule -Name "Rule2" -RuleType basic `
         -BackendHttpSettings $setting -HttpListener $listener02 -BackendAddressPool $pool
```

## <a name="specify-autoscale"></a>Especificar o dimensionamento automático

Agora pode especificar a configuração de dimensionamento automático para o gateway de aplicação. São suportados dois tipos de configuração de dimensionamento automático:

* **Modo de capacidade fixo**. Neste modo, o gateway de aplicação não dimensiona automaticamente e opera numa capacidade de Unidade de Escala fixa.

   ```azurepowershell
   $sku = New-AzApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2 -Capacity 2
   ```

* **Modo de dimensionamento automático**. Neste modo, o gateway de aplicação é dimensionado automaticamente com base no padrão de tráfego da aplicação.

   ```azurepowershell
   $autoscaleConfig = New-AzApplicationGatewayAutoscaleConfiguration -MinCapacity 2
   $sku = New-AzApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2
   ```

## <a name="create-the-application-gateway"></a>Criar o gateway de aplicação

Criar o gateway de aplicação e incluem as zonas de redundância e a configuração de dimensionamento automático.

```azurepowershell
$appgw = New-AzApplicationGateway -Name "AutoscalingAppGw" -Zone 1,2,3 `
  -ResourceGroupName $rg -Location $location -BackendAddressPools $pool `
  -BackendHttpSettingsCollection $setting -GatewayIpConfigurations $ipconfig `
  -FrontendIpConfigurations $fip -FrontendPorts $fp01, $fp02 `
  -HttpListeners $listener01, $listener02 -RequestRoutingRules $rule01, $rule02 `
  -Sku $sku -sslCertificates $sslCert01 -AutoscaleConfiguration $autoscaleConfig
```

## <a name="test-the-application-gateway"></a>Testar o gateway de aplicação

Utilize Get-AzPublicIPAddress para obter o endereço IP público do gateway de aplicação. Copie o endereço IP público ou o nome DNS e cole-o na barra de endereço do browser.

`Get-AzPublicIPAddress -ResourceGroupName $rg -Name AppGwVIP`

## <a name="clean-up-resources"></a>Limpar recursos

Primeiro, explore os recursos que foram criados com o gateway de aplicação. Em seguida, quando já não forem necessários, pode utilizar o `Remove-AzResourceGroup` comando para remover o grupo de recursos, o gateway de aplicação e todos os recursos relacionados.

`Remove-AzResourceGroup -Name $rg`

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Criar um gateway de aplicação com regras de encaminhamento com base no caminho de URL](./tutorial-url-route-powershell.md)
