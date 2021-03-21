---
title: HTTP para reorientação HTTPS usando PowerShell - Azure Application Gateway
description: Saiba como criar um gateway de aplicações com tráfego redirecionado de HTTP para HTTPS usando a Azure PowerShell.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 09/28/2020
ms.author: victorh
ms.openlocfilehash: 86eaa645cd6a81b9180d1241695240a71aa8202d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93397268"
---
# <a name="create-an-application-gateway-with-http-to-https-redirection-using-azure-powershell"></a>Criar um gateway de aplicações com HTTP para https redirecionamento usando Azure PowerShell

Pode utilizar o Azure PowerShell para criar um [gateway de aplicação](overview.md) com um certificado de rescisão TLS/SSL. Uma regra de encaminhamento é utilizada para redirecionar o tráfego HTTPS para a porta HTTPS no seu gateway de aplicações. Neste exemplo, também cria uma [balança de máquina virtual definida](../virtual-machine-scale-sets/overview.md) para o pool backend do gateway de aplicações que contém duas instâncias de máquina virtual. 

Neste artigo, vai aprender a:

* Criar um certificado autoassinado
* Configurar uma rede
* Criar um gateway de aplicação com o certificado
* Adicione uma regra de ouvinte e redirecionamento
* Criar um conjunto de dimensionamento de máquinas virtuais com o conjunto de back-end predefinido

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Este tutorial requer a versão 1.0 ou mais tarde do módulo Azure PowerShell. Executar `Get-Module -ListAvailable Az` para localizar a versão. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Para executar os comandos neste tutorial, também precisa correr `Login-AzAccount` para criar uma ligação com Azure.

## <a name="create-a-self-signed-certificate"></a>Criar um certificado autoassinado

Para uso de produção, deve importar um certificado válido assinado por um fornecedor de confiança. Neste tutorial, crie um certificado autoassinado com [New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate). Pode utilizar [Export-PfxCertificate](/powershell/module/pkiclient/export-pfxcertificate) com o Thumbprint que foi devolvido para exportar um ficheiro pfx do certificado.

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

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos. Criar um grupo de recursos Azure chamado *myResourceGroupAG* usando [o New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). 

```powershell
New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```

## <a name="create-network-resources"></a>Criar recursos de rede

Crie as configurações da sub-rede para *myBackendSubnet* e *myAGSubnet* utilizando [a New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig). Crie a rede virtual chamada *myVNet* utilizando [a New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) com as configurações da sub-rede. E, finalmente, crie o endereço IP público chamado *myAGPublicIPAddress* usando [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress). Estes recursos são utilizados para fornecer conectividade de rede ao gateway de aplicação e aos respetivos recursos associados.

```powershell
$backendSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.1.0/24
$agSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myAGSubnet `
  -AddressPrefix 10.0.2.0/24
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $backendSubnetConfig, $agSubnetConfig
$pip = New-AzPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myAGPublicIPAddress `
  -AllocationMethod Dynamic
```

## <a name="create-an-application-gateway"></a>Criar um gateway de aplicação

### <a name="create-the-ip-configurations-and-frontend-port"></a>Criar as configurações de IP e a porta de front-end

*Associe a myAGSubnet* que criou anteriormente para o gateway de aplicações utilizando [a New-AzApplicationGatewayIPConfiguration](/powershell/module/az.network/new-azapplicationgatewayipconfiguration). Atribua *myAGPublicIPAddress* ao gateway de aplicações utilizando [New-AzApplicationGatewayFrontendIPConfig](/powershell/module/az.network/new-azapplicationgatewayfrontendipconfig). E depois pode criar a porta HTTPS utilizando [o New-AzApplicationGatewayFrontendPort](/powershell/module/az.network/new-azapplicationgatewayfrontendport).

```powershell
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet
$subnet=$vnet.Subnets[0]
$gipconfig = New-AzApplicationGatewayIPConfiguration `
  -Name myAGIPConfig `
  -Subnet $subnet
$fipconfig = New-AzApplicationGatewayFrontendIPConfig `
  -Name myAGFrontendIPConfig `
  -PublicIPAddress $pip
$frontendPort = New-AzApplicationGatewayFrontendPort `
  -Name myFrontendPort `
  -Port 443
```

### <a name="create-the-backend-pool-and-settings"></a>Criar o conjunto e as definições de back-end

Crie o pool backend denominado *appGatewayBackendPool* para o gateway de aplicações utilizando [New-AzApplicationGatewayBackendAddressPool](/powershell/module/az.network/new-azapplicationgatewaybackendaddresspool). Configure as definições para o pool backend utilizando [new-AzApplicationGatewayBackendHttpSettings](/powershell/module/az.network/new-azapplicationgatewaybackendhttpsetting).

```powershell
$defaultPool = New-AzApplicationGatewayBackendAddressPool `
  -Name appGatewayBackendPool 
$poolSettings = New-AzApplicationGatewayBackendHttpSettings `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 120
```

### <a name="create-the-default-listener-and-rule"></a>Criar o serviço de escuta e a regra predefinidos

É necessário um serviço de escuta para permitir ao gateway de aplicação encaminhar o tráfego adequadamente para o conjunto de back-end. Neste exemplo, vai criar um serviço de escuta básico que escuta o tráfego HTTPS no URL de raiz. 

Crie um objeto de certificado utilizando [o New-AzApplicationGatewaySslCertificate](/powershell/module/az.network/new-azapplicationgatewaysslcertificate) e, em seguida, crie um ouvinte chamado *appGatewayHttpListener* usando [New-AzApplicationGatewayHttpListener](/powershell/module/az.network/new-azapplicationgatewayhttplistener) com a configuração frontal, porta frontal e certificado que criou anteriormente. É necessária uma regra para o serviço de escuta saber qual o conjunto de back-end a utilizar para o tráfego de entrada. Crie uma regra básica chamada *regra1* utilizando [New-AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/new-azapplicationgatewayrequestroutingrule).

```powershell
$pwd = ConvertTo-SecureString `
  -String "Azure123456!" `
  -Force `
  -AsPlainText
$cert = New-AzApplicationGatewaySslCertificate `
  -Name "appgwcert" `
  -CertificateFile "c:\appgwcert.pfx" `
  -Password $pwd
$defaultListener = New-AzApplicationGatewayHttpListener `
  -Name appGatewayHttpListener `
  -Protocol Https `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendPort `
  -SslCertificate $cert
$frontendRule = New-AzApplicationGatewayRequestRoutingRule `
  -Name rule1 `
  -RuleType Basic `
  -HttpListener $defaultListener `
  -BackendAddressPool $defaultPool `
  -BackendHttpSettings $poolSettings
```

### <a name="create-the-application-gateway"></a>Criar o gateway de aplicação

Agora que criou os recursos de suporte necessários, especifique os parâmetros para a porta de aplicação chamada *myAppGateway* usando [New-AzApplicationGatewaySku](/powershell/module/az.network/new-azapplicationgatewaysku), e, em seguida, criá-lo usando [New-AzApplicationGateway](/powershell/module/az.network/new-azapplicationgateway) com o certificado.

```powershell
$sku = New-AzApplicationGatewaySku `
  -Name Standard_Medium `
  -Tier Standard `
  -Capacity 2
$appgw = New-AzApplicationGateway `
  -Name myAppGateway `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -BackendAddressPools $defaultPool `
  -BackendHttpSettingsCollection $poolSettings `
  -FrontendIpConfigurations $fipconfig `
  -GatewayIpConfigurations $gipconfig `
  -FrontendPorts $frontendPort `
  -HttpListeners $defaultListener `
  -RequestRoutingRules $frontendRule `
  -Sku $sku `
  -SslCertificates $cert
```

## <a name="add-a-listener-and-redirection-rule"></a>Adicione uma regra de ouvinte e redirecionamento

### <a name="add-the-http-port"></a>Adicione a porta HTTP

Adicione a porta HTTP ao gateway de aplicações utilizando [Add-AzApplicationGatewayFrontendPort](/powershell/module/az.network/add-azapplicationgatewayfrontendport).

```powershell
$appgw = Get-AzApplicationGateway `
  -Name myAppGateway `
  -ResourceGroupName myResourceGroupAG
Add-AzApplicationGatewayFrontendPort `
  -Name httpPort  `
  -Port 80 `
  -ApplicationGateway $appgw
```

### <a name="add-the-http-listener"></a>Adicione o ouvinte HTTP

Adicione o ouvinte HTTP nomeado *myListener* ao gateway de aplicações utilizando [Add-AzApplicationGatewayHttpListener](/powershell/module/az.network/add-azapplicationgatewayhttplistener).

```powershell
$fipconfig = Get-AzApplicationGatewayFrontendIPConfig `
  -Name myAGFrontendIPConfig `
  -ApplicationGateway $appgw
$fp = Get-AzApplicationGatewayFrontendPort `
  -Name httpPort `
  -ApplicationGateway $appgw
Add-AzApplicationGatewayHttpListener `
  -Name myListener `
  -Protocol Http `
  -FrontendPort $fp `
  -FrontendIPConfiguration $fipconfig `
  -ApplicationGateway $appgw
```

### <a name="add-the-redirection-configuration"></a>Adicione a configuração de redirecionamento

Adicione a configuração de reorientação HTTPS à porta de aplicação utilizando [a configuração de Add-AzApplicationGatewayRedirectConfiguration](/powershell/module/az.network/add-azapplicationgatewayredirectconfiguration).

```powershell
$defaultListener = Get-AzApplicationGatewayHttpListener `
  -Name appGatewayHttpListener `
  -ApplicationGateway $appgw
Add-AzApplicationGatewayRedirectConfiguration -Name httpToHttps `
  -RedirectType Permanent `
  -TargetListener $defaultListener `
  -IncludePath $true `
  -IncludeQueryString $true `
  -ApplicationGateway $appgw
```

### <a name="add-the-routing-rule"></a>Adicione a regra de encaminhamento

Adicione a regra de encaminhamento com a configuração de redireccionamento para o gateway de aplicações utilizando [Add-AzApplicationGatewayRequestRoutrule](/powershell/module/az.network/add-azapplicationgatewayrequestroutingrule).

```powershell
$myListener = Get-AzApplicationGatewayHttpListener `
  -Name myListener `
  -ApplicationGateway $appgw
$redirectConfig = Get-AzApplicationGatewayRedirectConfiguration `
  -Name httpToHttps `
  -ApplicationGateway $appgw
Add-AzApplicationGatewayRequestRoutingRule `
  -Name rule2 `
  -RuleType Basic `
  -HttpListener $myListener `
  -RedirectConfiguration $redirectConfig `
  -ApplicationGateway $appgw
Set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="create-a-virtual-machine-scale-set"></a>Criar um conjunto de dimensionamento de máquinas virtuais

Neste exemplo, vai criar um conjunto de dimensionamento de máquinas virtuais para fornecer servidores para o conjunto de back-end no gateway de aplicação. Pode atribuir o conjunto de dimensionamento ao conjunto de back-end quando configurar as definições de IP.

```powershell
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway
$backendPool = Get-AzApplicationGatewayBackendAddressPool `
  -Name appGatewayBackendPool `
  -ApplicationGateway $appgw
$ipConfig = New-AzVmssIpConfig `
  -Name myVmssIPConfig `
  -SubnetId $vnet.Subnets[1].Id `
  -ApplicationGatewayBackendAddressPoolsId $backendPool.Id
$vmssConfig = New-AzVmssConfig `
  -Location eastus `
  -SkuCapacity 2 `
  -SkuName Standard_DS2 `
  -UpgradePolicyMode Automatic
Set-AzVmssStorageProfile $vmssConfig `
  -ImageReferencePublisher MicrosoftWindowsServer `
  -ImageReferenceOffer WindowsServer `
  -ImageReferenceSku 2016-Datacenter `
  -ImageReferenceVersion latest `
  -OsDiskCreateOption FromImage
Set-AzVmssOsProfile $vmssConfig `
  -AdminUsername azureuser `
  -AdminPassword "Azure123456!" `
  -ComputerNamePrefix myvmss
Add-AzVmssNetworkInterfaceConfiguration `
  -VirtualMachineScaleSet $vmssConfig `
  -Name myVmssNetConfig `
  -Primary $true `
  -IPConfiguration $ipConfig
New-AzVmss `
  -ResourceGroupName myResourceGroupAG `
  -Name myvmss `
  -VirtualMachineScaleSet $vmssConfig
```

### <a name="install-iis"></a>Instalar o IIS

```powershell
$publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1"); 
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }
$vmss = Get-AzVmss -ResourceGroupName myResourceGroupAG -VMScaleSetName myvmss
Add-AzVmssExtension -VirtualMachineScaleSet $vmss `
  -Name "customScript" `
  -Publisher "Microsoft.Compute" `
  -Type "CustomScriptExtension" `
  -TypeHandlerVersion 1.8 `
  -Setting $publicSettings
Update-AzVmss `
  -ResourceGroupName myResourceGroupAG `
  -Name myvmss `
  -VirtualMachineScaleSet $vmss
```

## <a name="test-the-application-gateway"></a>Testar o gateway de aplicação

Pode utilizar [o Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress) para obter o endereço IP público do gateway de aplicações. Copie o endereço IP público e cole-o na barra de endereço do browser. Por exemplo, http://52.170.203.149

```powershell
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![Aviso de segurança](./media/redirect-http-to-https-powershell/application-gateway-secure.png)

Para aceitar o aviso de segurança se usou um certificado auto-assinado, selecione **Detalhes** e, em seguida, **vá para a página web**. O site IIS protegido é apresentado como no exemplo seguinte:

![Testar o URL base no gateway de aplicação](./media/redirect-http-to-https-powershell/application-gateway-iistest.png)

## <a name="next-steps"></a>Passos seguintes

- [Reescrever cabeçalhos HTTP e URL com Gateway de aplicação](rewrite-http-headers-url.md)