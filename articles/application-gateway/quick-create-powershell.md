---
title: 'Quickstart: Tráfego web direto usando PowerShell'
titleSuffix: Azure Application Gateway
description: Aprenda a usar o Azure PowerShell para criar um Portal de Aplicações Azure que direciona o tráfego web para máquinas virtuais numa piscina de backend.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 04/15/2020
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 3e1ca14d967b0e88ea7eb559fd9962a3824ff9b0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81406218"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway-using-azure-powershell"></a>Quickstart: Tráfego web direto com Portal de Aplicação Azure usando Azure PowerShell

Neste arranque rápido, utiliza-se o Azure PowerShell para criar um portal de aplicação. Depois testa-o para se certificar de que funciona corretamente. 

O gateway da aplicação direciona o tráfego web da aplicação para recursos específicos em um pool backend. Você atribui os ouvintes aos portos, cria regras e adiciona recursos a uma piscina de backend. Por uma questão de simplicidade, este artigo usa uma configuração simples com um IP frontal público, um ouvinte básico para hospedar um único site na porta de aplicação, uma regra básica de encaminhamento de pedidos, e duas máquinas virtuais na piscina de backend.

Também pode completar este quickstart utilizando o [Azure CLI](quick-create-cli.md) ou o [portal Azure](quick-create-portal.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuitamente.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Versão 1.0.0 ou mais tarde do Azure PowerShell](/powershell/azure/install-az-ps) (se executar o Azure PowerShell localmente).

## <a name="connect-to-azure"></a>Ligar ao Azure

Para ligar-se ao `Connect-AzAccount`Azure, corra.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Em Azure, você aloca recursos relacionados a um grupo de recursos. Pode utilizar um grupo de recursos existente ou criar um novo.

Para criar um novo grupo `New-AzResourceGroup` de recursos, utilize o cmdlet: 

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```
## <a name="create-network-resources"></a>Criar recursos de rede

Para que o Azure se comunique entre os recursos que cria, precisa de uma rede virtual.  A sub-rede de gateway de aplicação só pode conter gateways de aplicação. Não são permitidos outros recursos.  Pode criar uma nova sub-rede para o Application Gateway ou utilizar uma existente. Neste exemplo, cria-se duas subredes neste exemplo: uma para o gateway da aplicação e outra para os servidores de backend. Pode configurar o IP Frontend do Gateway de Aplicação para ser público ou privado de acordo com o seu caso de utilização. Neste exemplo, você escolherá um IP Frontend Público.

1. Criar as configurações `New-AzVirtualNetworkSubnetConfig`da sub-rede utilizando .
2. Crie a rede virtual com `New-AzVirtualNetwork`as configurações da sub-rede utilizando . 
3. Crie o endereço `New-AzPublicIpAddress`IP público utilizando . 

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

1. Utilize `New-AzApplicationGatewayIPConfiguration` para criar a configuração que associa a sub-rede que criou com o gateway da aplicação. 
2. Utilize `New-AzApplicationGatewayFrontendIPConfig` para criar a configuração que atribui o endereço IP público que criou anteriormente para o gateway da aplicação. 
3. Utilizar `New-AzApplicationGatewayFrontendPort` para atribuir a porta 80 para aceder ao gateway da aplicação.

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

1. Utilize `New-AzApplicationGatewayBackendAddressPool` para criar a piscina de backend para o gateway de aplicação. A piscina de backend estará vazia por enquanto. Quando criar os NICs do servidor de backend na secção seguinte, irá adicioná-los à piscina de backend.
2. Configure as definições para `New-AzApplicationGatewayBackendHttpSetting`a piscina de backend com .

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

O Azure requer um ouvinte que permita a entrada de aplicação para o encaminhamento do tráfego adequadamente para a piscina de backend. O Azure também requer uma regra para que o ouvinte saiba qual piscina de backend a utilizar para o tráfego de entrada. 

1. Crie um `New-AzApplicationGatewayHttpListener` ouvinte utilizando a configuração frontal e a porta frontal que criou anteriormente. 
2. Utilize `New-AzApplicationGatewayRequestRoutingRule` para criar uma regra chamada *regra 1*. 

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

1. Utilize `New-AzApplicationGatewaySku` para especificar parâmetros para o gateway de aplicação.
2. Utilize `New-AzApplicationGateway` para criar o gateway da aplicação.

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

Agora que criou o Gateway de Aplicação, crie as máquinas virtuais de backend que irão acolher os websites. Backend pode ser composto por NICs, conjuntos de escala de máquinas virtuais, IPs públicos, IPs internos, nomes de domínio totalmente qualificados (FQDN), e back-ends multi-inquilinos como o Azure App Service. Neste exemplo, cria-se duas máquinas virtuais para o Azure utilizar como servidores de backend para o gateway da aplicação. Também instala o IIS nas máquinas virtuais para verificar se o Azure criou com sucesso o portal de aplicação.

#### <a name="create-two-virtual-machines"></a>Criar duas máquinas virtuais

1. Obtenha a configuração de piscina `Get-AzApplicationGatewayBackendAddressPool`de backend Application Gateway recentemente criada com .
2. Criar uma interface `New-AzNetworkInterface`de rede com .
3. Criar uma configuração `New-AzVMConfig`virtual da máquina com .
4. Crie a `New-AzVM`máquina virtual com .

Quando executa a seguinte amostra de código para criar as máquinas virtuais, o Azure pede-lhe credenciais. Insira *o azureuser* para o nome de utilizador e uma palavra-passe:
    
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

Embora o IIS não seja necessário para criar o gateway da aplicação, instalou-o neste arranque rápido para verificar se o Azure criou com sucesso o portal de aplicações. Utilize o IIS para testar o gateway de aplicação:

1. Corra `Get-AzPublicIPAddress` para obter o endereço IP público do gateway de aplicação. 
2. Copie e cole o endereço IP público na barra de endereços do seu navegador. Ao atualizar o navegador, deverá ver o nome da máquina virtual. Uma resposta válida verifica que o gateway da aplicação foi criado com sucesso e pode ligar-se com sucesso ao backend.

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![Testar o gateway de aplicação](./media/quick-create-powershell/application-gateway-iistest.png)


## <a name="clean-up-resources"></a>Limpar recursos

Quando já não precisa dos recursos que criou com o portal de aplicação, elimine o grupo de recursos. Ao eliminar o grupo de recursos, também elimina o gateway da aplicação e todos os seus recursos relacionados. 

Para eliminar o grupo `Remove-AzResourceGroup` de recursos, ligue para o cmdlet:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupAG
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Gerir o tráfego da Web com um gateway de aplicação com o Azure PowerShell](./tutorial-manage-web-traffic-powershell.md)

