---
title: 'Quickstart: Tráfego web direto usando PowerShell'
titleSuffix: Azure Application Gateway
description: Neste arranque rápido, aprende-se a usar o Azure PowerShell para criar um Gateway de aplicação Azure que direciona o tráfego web para máquinas virtuais numa piscina de backend.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 01/19/2021
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 8073d1e18b08a6deb0175f8eaf18de382e93e299
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98601847"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway-using-azure-powershell"></a>Quickstart: Tráfego web direto com gateway de aplicação Azure usando Azure PowerShell

Neste arranque rápido, utiliza-se o Azure PowerShell para criar um gateway de aplicações. Em seguida, teste-o para se certificar de que funciona corretamente. 

O gateway de aplicações direciona o tráfego web da aplicação para recursos específicos numa piscina de backend. Você atribui os ouvintes aos portos, cria regras e adiciona recursos a uma piscina de backend. Por uma questão de simplicidade, este artigo utiliza uma configuração simples com um endereço IP frontal público, um ouvinte básico para hospedar um único site no gateway de aplicação, uma regra de encaminhamento de pedidos básicos e duas máquinas virtuais na piscina de backend.

Também pode completar este quickstart utilizando [o Azure CLI](quick-create-cli.md) ou o [portal Azure](quick-create-portal.md).

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Azure PowerShell versão 1.0.0 ou posterior](/powershell/azure/install-az-ps) (se executar a Azure PowerShell localmente).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="connect-to-azure"></a>Ligar ao Azure

Para ligar com Azure, corra `Connect-AzAccount` .

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Em Azure, aloca recursos relacionados a um grupo de recursos. Pode utilizar um grupo de recursos existente ou criar um novo.

Para criar um novo grupo de recursos, utilize o `New-AzResourceGroup` cmdlet: 

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```
## <a name="create-network-resources"></a>Criar recursos de rede

Para que o Azure comunique entre os recursos que cria, precisa de uma rede virtual.  A sub-rede de gateway de aplicação pode conter apenas portais de aplicação. Não são permitidos outros recursos.  Pode criar uma nova sub-rede para o Gateway de Aplicação ou utilizar uma existente. Cria duas sub-redes neste exemplo: uma para o gateway de aplicações e outra para os servidores backend. Pode configurar o endereço IP frontend do Gateway de aplicação para ser público ou privado de acordo com o seu caso de utilização. Neste exemplo, você escolherá um endereço IP de Frontend Público.

1. Crie as configurações da sub-rede utilizando `New-AzVirtualNetworkSubnetConfig` .
2. Crie a rede virtual com as configurações da sub-rede utilizando `New-AzVirtualNetwork` . 
3. Crie o endereço IP público utilizando `New-AzPublicIpAddress` . 
> [!NOTE]
> [As políticas de ponto final do serviço de rede virtual](../virtual-network/virtual-network-service-endpoint-policies-overview.md) não são atualmente suportadas numa sub-rede do Gateway de Aplicação.

```azurepowershell-interactive
$agSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myAGSubnet `
  -AddressPrefix 10.0.1.0/24
$backendSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.2.0/24
New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $agSubnetConfig, $backendSubnetConfig
New-AzPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myAGPublicIPAddress `
  -AllocationMethod Static `
  -Sku Standard
```
## <a name="create-an-application-gateway"></a>Criar um gateway de aplicação

### <a name="create-the-ip-configurations-and-frontend-port"></a>Criar as configurações de IP e a porta de front-end

1. Utilize `New-AzApplicationGatewayIPConfiguration` para criar a configuração que associa a sub-rede que criou com o gateway de aplicações. 
2. Utilize `New-AzApplicationGatewayFrontendIPConfig` para criar a configuração que atribui o endereço IP público que criou anteriormente para o gateway de aplicações. 
3. Utilize `New-AzApplicationGatewayFrontendPort` para atribuir a porta 80 para aceder ao gateway de aplicação.

```azurepowershell-interactive
$vnet   = Get-AzVirtualNetwork -ResourceGroupName myResourceGroupAG -Name myVNet
$subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name myAGSubnet
$pip    = Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress 
$gipconfig = New-AzApplicationGatewayIPConfiguration `
  -Name myAGIPConfig `
  -Subnet $subnet
$fipconfig = New-AzApplicationGatewayFrontendIPConfig `
  -Name myAGFrontendIPConfig `
  -PublicIPAddress $pip
$frontendport = New-AzApplicationGatewayFrontendPort `
  -Name myFrontendPort `
  -Port 80
```

### <a name="create-the-backend-pool"></a>Criar o conjunto de back-end

1. Utilize `New-AzApplicationGatewayBackendAddressPool` para criar a piscina de backend para o gateway de aplicação. A piscina está vazia por enquanto. Quando criar os NICs do servidor de backend na secção seguinte, irá adicioná-los à piscina de backend.
2. Configure as definições para a piscina de backend com `New-AzApplicationGatewayBackendHttpSetting` .

```azurepowershell-interactive
$backendPool = New-AzApplicationGatewayBackendAddressPool `
  -Name myAGBackendPool
$poolSettings = New-AzApplicationGatewayBackendHttpSetting `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 30
```

### <a name="create-the-listener-and-add-a-rule"></a>Criar o serviço de escuta e adicionar uma regra

O Azure requer um ouvinte para ativar o gateway de aplicação para encaminhar o tráfego adequadamente para a piscina de backend. O Azure também requer uma regra para que o ouvinte saiba qual piscina de backend deve usar para o tráfego de entrada. 

1. Crie um ouvinte utilizando `New-AzApplicationGatewayHttpListener` a configuração frontal e a porta frontal que criou anteriormente. 
2. Utilize `New-AzApplicationGatewayRequestRoutingRule` para criar uma regra chamada regra *1*. 

```azurepowershell-interactive
$defaultlistener = New-AzApplicationGatewayHttpListener `
  -Name myAGListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendport
$frontendRule = New-AzApplicationGatewayRequestRoutingRule `
  -Name rule1 `
  -RuleType Basic `
  -HttpListener $defaultlistener `
  -BackendAddressPool $backendPool `
  -BackendHttpSettings $poolSettings
```

### <a name="create-the-application-gateway"></a>Criar o gateway de aplicação

Agora que criou os recursos de apoio necessários, crie o portal de aplicação:

1. Utilize `New-AzApplicationGatewaySku` para especificar os parâmetros para o gateway de aplicação.
2. Utilize `New-AzApplicationGateway` para criar o gateway de aplicação.

```azurepowershell-interactive
$sku = New-AzApplicationGatewaySku `
  -Name Standard_v2 `
  -Tier Standard_v2 `
  -Capacity 2
New-AzApplicationGateway `
  -Name myAppGateway `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -BackendAddressPools $backendPool `
  -BackendHttpSettingsCollection $poolSettings `
  -FrontendIpConfigurations $fipconfig `
  -GatewayIpConfigurations $gipconfig `
  -FrontendPorts $frontendport `
  -HttpListeners $defaultlistener `
  -RequestRoutingRules $frontendRule `
  -Sku $sku
```

### <a name="backend-servers"></a>Servidores back-end

Agora que criou o Application Gateway, crie as máquinas virtuais de backend que irão acolher os websites. Um backend pode ser composto por NICs, conjuntos de escala de máquinas virtuais, endereço IP público, endereço IP interno, nomes de domínio totalmente qualificados (FQDN) e back-ends multi-inquilinos como Azure App Service. 

Neste exemplo, cria-se duas máquinas virtuais para utilizar como servidores de backend para o gateway de aplicações. Também instala o IIS nas máquinas virtuais para verificar se o Azure criou com sucesso o gateway de aplicações.

#### <a name="create-two-virtual-machines"></a>Criar duas máquinas virtuais

1. Obtenha a configuração de pool backend Application Gateway recentemente criada com `Get-AzApplicationGatewayBackendAddressPool` .
2. Criar uma interface de rede com `New-AzNetworkInterface` .
3. Crie uma configuração de máquina virtual com `New-AzVMConfig` .
4. Crie a máquina virtual com `New-AzVM` .

Quando executar a seguinte amostra de código para criar as máquinas virtuais, o Azure pede-lhe credenciais. Introduza um nome de utilizador e uma senha:
    
```azurepowershell-interactive
$appgw = Get-AzApplicationGateway -ResourceGroupName myResourceGroupAG -Name myAppGateway
$backendPool = Get-AzApplicationGatewayBackendAddressPool -Name myAGBackendPool -ApplicationGateway $appgw
$vnet   = Get-AzVirtualNetwork -ResourceGroupName myResourceGroupAG -Name myVNet
$subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name myBackendSubnet
$cred = Get-Credential
for ($i=1; $i -le 2; $i++)
{
  $nic = New-AzNetworkInterface `
    -Name myNic$i `
    -ResourceGroupName myResourceGroupAG `
    -Location EastUS `
    -Subnet $subnet `
    -ApplicationGatewayBackendAddressPool $backendpool
  $vm = New-AzVMConfig `
    -VMName myVM$i `
    -VMSize Standard_DS2_v2
  Set-AzVMOperatingSystem `
    -VM $vm `
    -Windows `
    -ComputerName myVM$i `
    -Credential $cred
  Set-AzVMSourceImage `
    -VM $vm `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest
  Add-AzVMNetworkInterface `
    -VM $vm `
    -Id $nic.Id
  Set-AzVMBootDiagnostic `
    -VM $vm `
    -Disable
  New-AzVM -ResourceGroupName myResourceGroupAG -Location EastUS -VM $vm
  Set-AzVMExtension `
    -ResourceGroupName myResourceGroupAG `
    -ExtensionName IIS `
    -VMName myVM$i `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
    -Location EastUS
}
```

## <a name="test-the-application-gateway"></a>Testar o gateway de aplicação

Embora o IIS não seja obrigado a criar o gateway de aplicações, instalou-o neste quickstart para verificar se o Azure criou com sucesso o gateway de aplicações.

Utilize o IIS para testar o gateway de aplicações:

1. Corra `Get-AzPublicIPAddress` para obter o endereço IP público do gateway de aplicações. 
2. Copie e cole o endereço IP público na barra de endereço do seu navegador. Ao refrescar o navegador, deverá ver o nome da máquina virtual. Uma resposta válida verifica que o gateway de aplicações foi criado com sucesso e pode conectar-se com sucesso com o backend.

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![Testar o gateway de aplicação](./media/quick-create-powershell/application-gateway-iistest.png)


## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não precisar dos recursos que criou com o gateway de aplicações, elimine o grupo de recursos. Quando elimina o grupo de recursos, também elimina o gateway de aplicações e todos os seus recursos relacionados. 

Para eliminar o grupo de recursos, ligue para o `Remove-AzResourceGroup` cmdlet:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupAG
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Gerir o tráfego da Web com um gateway de aplicação com o Azure PowerShell](./tutorial-manage-web-traffic-powershell.md)

