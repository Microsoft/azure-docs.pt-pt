---
title: Início Rápido - Direcionar tráfego da Web com o Gateway de Aplicação do Azure - Azure PowerShell | Microsoft Docs
description: Saiba como utilizar o Azure PowerShell para criar um Gateway de Aplicação do Azure que direciona o tráfego da Web para máquinas virtuais num conjunto de back-end.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 1/11/2019
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: de1162be381a905a3b04fbf7ae7dec59eb7d209a
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57549600"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-powershell"></a>Início rápido: Tráfego da web direto com o Gateway de aplicação do Azure - Azure PowerShell

Este guia de introdução mostra-lhe como utilizar o portal do Azure para criar rapidamente um gateway de aplicação.  Depois de criar o gateway de aplicação,, em seguida, testá-lo para se certificar de que está a funcionar corretamente. Com o Gateway de aplicação do Azure, direcionar o tráfego da web de aplicação a recursos específicos, a atribuição de serviços de escuta a portas, criação de regras e adicionar recursos a um agrupamento de back-end. Para simplificar, este artigo utiliza uma configuração simples com um IP público de front-end, um serviço de escuta básico para alojar um site único neste gateway de aplicação, duas máquinas virtuais utilizadas para o conjunto de back-end e uma regra de encaminhamento de pedido básico.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

## <a name="prerequisites"></a>Pré-requisitos

### <a name="azure-powershell-module"></a>Módulo do Azure PowerShell

Se optar por instalar e utilizar o Azure PowerShell localmente, este tutorial requer o Azure PowerShell versão 1.0.0 do módulo ou posterior.

1. Para localizar a versão, execute `Get-Module -ListAvailable Az`. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). 
2. Para criar uma ligação com o Azure, execute `Login-AzAccount`.

### <a name="resource-group"></a>Grupo de recursos

No Azure, alocar recursos relacionados a um grupo de recursos. Pode utilizar um grupo de recursos existente ou crie um novo. Neste exemplo, iremos criar um novo grupo de recursos, utilizando o [New-AzResourceGroup](/powershell/module/Az.resources/new-Azresourcegroup) cmdlet da seguinte forma: 

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```

### <a name="required-network-resources"></a>Recursos de rede necessários

Para o Azure comunicar entre os recursos que criar, ele precisa de uma rede virtual.  A sub-rede do gateway de aplicação pode conter apenas os gateways de aplicação. Não existem outros recursos são permitidos.  Pode criar uma nova sub-rede de Gateway de aplicação ou utilize um já existente. Neste exemplo, vai criar duas sub-redes, neste exemplo: um para o gateway de aplicação e outro para os servidores de back-end. Pode configurar o IP de front-end do Gateway de aplicação para ser públicas ou privadas, de acordo com o seu caso de utilização. Neste exemplo, vamos escolher um IP público de front-end.

1. Criar as configurações de sub-rede chamando [New-AzVirtualNetworkSubnetConfig](/powershell/module/Az.network/new-Azvirtualnetworksubnetconfig).
2. Criar a rede virtual com as configurações de sub-rede chamando [New-AzVirtualNetwork](/powershell/module/Az.network/new-Azvirtualnetwork). 
3. Criar o endereço IP público ao chamar [New-AzPublicIpAddress](/powershell/module/Az.network/new-Azpublicipaddress). 

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
  -AllocationMethod Dynamic
```
### <a name="backend-servers"></a>Servidores de back-end

Back-end pode ser composto de NICs, conjuntos de dimensionamento de máquinas virtuais, IPs públicos, nomes de IPs interno, de domínio completamente qualificado (FQDN) e back-ends de multi-inquilino, como o serviço de aplicações do Azure. Neste exemplo, vai criar duas máquinas virtuais do Azure utilizar como servidores de back-end para o gateway de aplicação. Também é instalar o IIS nas máquinas virtuais para verificar se o Azure criada com êxito o gateway de aplicação.

#### <a name="create-two-virtual-machines"></a>Criar duas máquinas virtuais

1. Criar uma interface de rede com [New-AzNetworkInterface](/powershell/module/Az.network/new-Aznetworkinterface). 
2. Crie uma configuração de máquina virtual com [New-AzVMConfig](/powershell/module/Az.compute/new-Azvmconfig).
3. Criar a máquina virtual com [New-AzVM](/powershell/module/Az.compute/new-Azvm).

Ao executar o exemplo de código seguinte para criar as máquinas virtuais, o Azure pede-lhe as credenciais. Introduza *azureuser* para o nome de utilizador e *Azure123456!* a palavra-passe:
    
```azurepowershell-interactive
$vnet   = Get-AzVirtualNetwork -ResourceGroupName myResourceGroupAG -Name myVNet
$subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name myBackendSubnet
$cred = Get-Credential
for ($i=1; $i -le 2; $i++)
{
  $nic = New-AzNetworkInterface `
    -Name myNic$i `
    -ResourceGroupName myResourceGroupAG `
    -Location EastUS `
    -SubnetId $subnet.Id
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
  Set-AzVMBootDiagnostics `
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

## <a name="create-an-application-gateway"></a>Criar um gateway de aplicação

### <a name="create-the-ip-configurations-and-frontend-port"></a>Criar as configurações de IP e a porta de front-end

1. Uso [New-AzApplicationGatewayIPConfiguration](/powershell/module/Az.network/new-Azapplicationgatewayipconfiguration) para criar a configuração que associa a sub-rede que criou com o gateway de aplicação. 
2. Uso [New-AzApplicationGatewayFrontendIPConfig](/powershell/module/Az.network/new-Azapplicationgatewayfrontendipconfig) para criar a configuração que atribui o endereço IP público que criou anteriormente para o gateway de aplicação. 
3. Uso [New-AzApplicationGatewayFrontendPort](/powershell/module/Az.network/new-Azapplicationgatewayfrontendport) para atribuir a porta 80 para acessar o gateway de aplicação.

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

1. Uso [New-AzApplicationGatewayBackendAddressPool](/powershell/module/Az.network/new-Azapplicationgatewaybackendaddresspool) para criar o conjunto de back-end para o gateway de aplicação. 
2. Configurar as definições para o conjunto de back-end com [New-AzApplicationGatewayBackendHttpSettings](/powershell/module/Az.network/new-Azapplicationgatewaybackendhttpsettings).

```azurepowershell-interactive
$address1 = Get-AzNetworkInterface -ResourceGroupName myResourceGroupAG -Name myNic1
$address2 = Get-AzNetworkInterface -ResourceGroupName myResourceGroupAG -Name myNic2
$backendPool = New-AzApplicationGatewayBackendAddressPool `
  -Name myAGBackendPool `
  -BackendIPAddresses $address1.ipconfigurations[0].privateipaddress, $address2.ipconfigurations[0].privateipaddress
$poolSettings = New-AzApplicationGatewayBackendHttpSettings `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 120
```

### <a name="create-the-listener-and-add-a-rule"></a>Criar o serviço de escuta e adicionar uma regra

O Azure requer um serviço de escuta ativar o gateway de aplicação para encaminhar o tráfego adequadamente para o conjunto de back-end. O Azure também requer uma regra para o serviço de escuta de saber qual conjunto de back-end a utilizar para tráfego de entrada. 

1. Criar um serviço de escuta com o [New-AzApplicationGatewayHttpListener](/powershell/module/Az.network/new-Azapplicationgatewayhttplistener) com a configuração de front-end e porta de front-end que criou anteriormente. 
2. Uso [New-AzApplicationGatewayRequestRoutingRule](/powershell/module/Az.network/new-Azapplicationgatewayrequestroutingrule) para criar uma regra com o nome *rule1*. 

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

Agora que criou os recursos de suporte necessários, crie o gateway de aplicação:

1. Uso [New-AzApplicationGatewaySku](/powershell/module/Az.network/new-Azapplicationgatewaysku) para especificar parâmetros para o gateway de aplicação.
2. Uso [New-AzApplicationGateway](/powershell/module/Az.network/new-Azapplicationgateway) para criar o gateway de aplicação.

```azurepowershell-interactive
$sku = New-AzApplicationGatewaySku `
  -Name Standard_Medium `
  -Tier Standard `
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

## <a name="test-the-application-gateway"></a>Testar o gateway de aplicação

Embora o IIS não é necessário para criar o gateway de aplicação, a instalou neste início rápido para verificar se o Azure criada com êxito o gateway de aplicação. Utilize o IIS para testar o gateway de aplicação:

1. Execute [Get-AzPublicIPAddress](/powershell/module/Az.network/get-Azpublicipaddress) para obter o endereço IP público do gateway de aplicação. 
2. Copie e cole o endereço IP público na barra de endereço do seu browser. Quando atualizar o navegador, deverá ver o nome da máquina virtual. Uma resposta válida verifica se o gateway de aplicação foi criado com êxito e é capaz de ligar com êxito com o back-end.

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![Testar o gateway de aplicação](./media/quick-create-powershell/application-gateway-iistest.png)


## <a name="clean-up-resources"></a>Limpar recursos

Quando já não precisar dos recursos que criou com o gateway de aplicação, remova o grupo de recursos. Ao remover o grupo de recursos, também remover o gateway de aplicação e todos os respetivos recursos relacionados. 

Para remover o grupo de recursos, chamar o [Remove-AzResourceGroup](/powershell/module/Az.resources/remove-Azresourcegroup) cmdlet da seguinte forma:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupAG
```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Gerir o tráfego da Web com um gateway de aplicação com o Azure PowerShell](./tutorial-manage-web-traffic-powershell.md)

