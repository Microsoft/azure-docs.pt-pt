---
title: 'Início rápido: tráfego direto da Web usando o PowerShell'
titleSuffix: Azure Application Gateway
description: Saiba como usar Azure PowerShell para criar um gateway de Aplicativo Azure que direciona o tráfego da Web para máquinas virtuais em um pool de back-end.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 11/14/2019
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 9c3fac7aecaf37b5822ad6e8c655867f6f2c683c
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74872711"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway-using-azure-powershell"></a>Início rápido: tráfego direto da Web com gateway de Aplicativo Azure usando Azure PowerShell

Este guia de início rápido mostra como usar Azure PowerShell para criar rapidamente um gateway de aplicativo.  Depois de criar o gateway de aplicativo, você o testará para verificar se ele está funcionando corretamente. Com o gateway de Aplicativo Azure, você direciona o tráfego da Web do aplicativo para recursos específicos atribuindo ouvintes a portas, criando regras e adicionando recursos a um pool de back-end. Para simplificar, este artigo usa uma configuração simples com um IP de front-end público, um ouvinte básico para hospedar um único site nesse gateway de aplicativo, duas máquinas virtuais usadas para o pool de back-end e uma regra de roteamento de solicitação básica.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Pré-requisitos

### <a name="azure-powershell-module"></a>Módulo do Azure PowerShell

Se você optar por instalar e usar Azure PowerShell localmente, este tutorial exigirá o módulo Azure PowerShell versão 1.0.0 ou posterior.

1. Para localizar a versão, execute `Get-Module -ListAvailable Az`. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). 
2. Para criar uma conexão com o Azure, execute `Login-AzAccount`.

### <a name="resource-group"></a>Grupo de recursos

No Azure, você aloca recursos relacionados a um grupo de recursos. Você pode usar um grupo de recursos existente ou criar um novo. Neste exemplo, você criará um novo grupo de recursos usando o cmdlet [New-AzResourceGroup](/powershell/module/Az.resources/new-Azresourcegroup) da seguinte maneira: 

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```

### <a name="required-network-resources"></a>Recursos de rede necessários

Para que o Azure se comunique entre os recursos que você cria, ele precisa de uma rede virtual.  A sub-rede do gateway de aplicativo pode conter somente gateways de aplicativo. Nenhum outro recurso é permitido.  Você pode criar uma nova sub-rede para o gateway de aplicativo ou usar uma existente. Neste exemplo, você cria duas sub-redes neste exemplo: uma para o gateway de aplicativo e outra para os servidores de back-end. Você pode configurar o IP de front-end do gateway de aplicativo para ser público ou privado de acordo com seu caso de uso. Neste exemplo, você escolherá um IP de front-end público.

1. Crie as configurações de sub-rede chamando [New-AzVirtualNetworkSubnetConfig](/powershell/module/Az.network/new-Azvirtualnetworksubnetconfig).
2. Crie a rede virtual com as configurações de sub-rede chamando [New-AzVirtualNetwork](/powershell/module/Az.network/new-Azvirtualnetwork). 
3. Crie o endereço IP público chamando [New-AzPublicIpAddress](/powershell/module/Az.network/new-Azpublicipaddress). 

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

1. Use [New-AzApplicationGatewayIPConfiguration](/powershell/module/Az.network/new-Azapplicationgatewayipconfiguration) para criar a configuração que associa a sub-rede que você criou com o gateway de aplicativo. 
2. Use [New-AzApplicationGatewayFrontendIPConfig](/powershell/module/Az.network/new-Azapplicationgatewayfrontendipconfig) para criar a configuração que atribui o endereço IP público que você criou anteriormente para o gateway de aplicativo. 
3. Use [New-AzApplicationGatewayFrontendPort](/powershell/module/Az.network/new-Azapplicationgatewayfrontendport) para atribuir a porta 80 para acessar o gateway de aplicativo.

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

1. Use [New-AzApplicationGatewayBackendAddressPool](/powershell/module/Az.network/new-Azapplicationgatewaybackendaddresspool) para criar o pool de back-end para o gateway de aplicativo. O pool de back-end estará vazio por enquanto e, ao criar as NICs do servidor back-end na próxima seção, você as adicionará ao pool de back-end.
2. Defina as configurações para o pool de back-end com [New-AzApplicationGatewayBackendHttpSetting](/powershell/module/Az.network/new-Azapplicationgatewaybackendhttpsetting).

```azurepowershell-interactive
$address1 = Get-AzNetworkInterface -ResourceGroupName myResourceGroupAG -Name myNic1
$address2 = Get-AzNetworkInterface -ResourceGroupName myResourceGroupAG -Name myNic2
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

O Azure requer um ouvinte para habilitar o gateway de aplicativo para rotear o tráfego adequadamente para o pool de back-end. O Azure também requer uma regra para que o ouvinte saiba qual pool de back-end deve ser usado para tráfego de entrada. 

1. Crie um ouvinte usando [New-AzApplicationGatewayHttpListener](/powershell/module/Az.network/new-Azapplicationgatewayhttplistener) com a configuração front-end e a porta de front-end que você criou anteriormente. 
2. Use [New-AzApplicationGatewayRequestRoutingRule](/powershell/module/Az.network/new-Azapplicationgatewayrequestroutingrule) para criar uma regra chamada *rule1*. 

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

Agora que você criou os recursos de suporte necessários, crie o gateway de aplicativo:

1. Use [New-AzApplicationGatewaySku](/powershell/module/Az.network/new-Azapplicationgatewaysku) para especificar parâmetros para o gateway de aplicativo.
2. Use [New-AzApplicationGateway](/powershell/module/Az.network/new-Azapplicationgateway) para criar o gateway de aplicativo.

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

Agora que você criou o gateway de aplicativo, crie as máquinas virtuais de back-end que hospedarão os sites. O back-end pode ser composto por NICs, conjuntos de dimensionamento de máquinas virtuais, IPs públicos, IPs internos, FQDN (nomes de domínio totalmente qualificados) e back-ends de vários locatários como Azure App serviço. Neste exemplo, você cria duas máquinas virtuais para o Azure usar como servidores de back-end para o gateway de aplicativo. Você também instala o IIS nas máquinas virtuais para verificar se o Azure criou com êxito o gateway de aplicativo.

#### <a name="create-two-virtual-machines"></a>Criar duas máquinas virtuais

1. Obtenha a configuração do pool de back-end do gateway de aplicativo criado recentemente com [Get-AzApplicationGatewayBackendAddressPool](/powershell/module/Az.network/get-Azapplicationgatewaybackendaddresspool)
2. Crie uma interface de rede com [New-AzNetworkInterface](/powershell/module/Az.network/new-Aznetworkinterface). 
3. Crie uma configuração de máquina virtual com [New-AzVMConfig](/powershell/module/Az.compute/new-Azvmconfig).
4. Crie a máquina virtual com [New-AzVM](/powershell/module/Az.compute/new-Azvm).

Quando você executa o exemplo de código a seguir para criar as máquinas virtuais, o Azure solicita suas credenciais. Introduza *azureuser* para o nome de utilizador e *Azure123456!* para a senha:
    
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

Embora o IIS não seja necessário para criar o gateway de aplicativo, você o instalou neste guia de início rápido para verificar se o Azure criou o gateway de aplicativo com êxito. Use o IIS para testar o gateway de aplicativo:

1. Execute [Get-AzPublicIPAddress](/powershell/module/Az.network/get-Azpublicipaddress) para obter o endereço IP público do gateway de aplicativo. 
2. Copie e cole o endereço IP público na barra de endereços do seu navegador. Ao atualizar o navegador, você deverá ver o nome da máquina virtual. Uma resposta válida verifica se o gateway de aplicativo foi criado com êxito e se pode se conectar com êxito com o back-end.

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![Testar o gateway de aplicação](./media/quick-create-powershell/application-gateway-iistest.png)


## <a name="clean-up-resources"></a>Limpar recursos

Quando você não precisar mais dos recursos que criou com o gateway de aplicativo, remova o grupo de recursos. Removendo o grupo de recursos, você também remove o gateway de aplicativo e todos os seus recursos relacionados. 

Para remover o grupo de recursos, chame o cmdlet [Remove-AzResourceGroup](/powershell/module/Az.resources/remove-Azresourcegroup) da seguinte maneira:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupAG
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Gerir o tráfego da Web com um gateway de aplicação com o Azure PowerShell](./tutorial-manage-web-traffic-powershell.md)

