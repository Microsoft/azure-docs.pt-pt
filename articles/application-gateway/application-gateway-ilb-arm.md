---
title: Utilização com Balanceador de Carga Interna - Gateway de aplicação Azure
description: Esta página fornece instruções para criar, configurar, iniciar e eliminar um gateway de aplicação do Azure com o balanceador de carga interno (ILB) do Azure Resource Manager
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/13/2019
ms.author: victorh
ms.openlocfilehash: 3d663dc4e2bd860ec9494785ecbf6dbf10a4c5b5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93397761"
---
# <a name="create-an-application-gateway-with-an-internal-load-balancer-ilb"></a>Criar um gateway de aplicação com um equilibrador de carga interno (ILB)

Pode configurar o Application Gateway do Azure com um VIP de acesso à Internet ou com um ponto final interno não exposto à Internet, também conhecido como ponto final do balanceador de carga interno (ILB). Configurar o gateway com um ILB é útil para as aplicações de linha de negócio internas não expostas à Internet. Também é útil para serviços e níveis dentro de uma aplicação multi-nível que se situa numa fronteira de segurança que não está exposta à Internet, mas ainda requer distribuição de carga de robin redondo, stickiness de sessão ou Segurança da Camada de Transporte (TLS), anteriormente conhecida como Secure Sockets Layer (SSL), rescisão.

Este artigo descreve os passos para configurar um gateway de aplicação com um ILB.

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. Instale a versão mais recente do módulo Azure PowerShell seguindo as [instruções de instalação](/powershell/azure/install-az-ps).
2. Vai criar uma rede virtual e uma sub-rede para o Application Gateway. Verifique se a sub-rede não está a ser utilizada por nenhuma máquina virtual ou implementação na nuvem. O Application Gateway tem de constar, por si só, numa sub-rede de rede virtual.
3. Os servidores que irá configurar para utilizar o gateway de aplicação devem existir. Caso contrário, os respetivos pontos finais terão de ser criados na rede virtual ou com um IP/VIP público atribuído.

## <a name="what-is-required-to-create-an-application-gateway"></a>O que é necessário para criar um gateway de aplicação?

* **Conjunto de servidores de back-end:** a lista de endereços IP dos servidores de back-end. Os endereços IP listados devem pertencer à rede virtual, mas numa sub-rede diferente para o gateway de aplicação, ou ser um IP/VIP público.
* **Definições de piscina de servidor de back-end:** Cada piscina tem configurações como porta, protocolo e afinidade baseada em cookies. Estas definições estão associadas a um conjunto e são aplicadas a todos os servidores do referido conjunto.
* **Porta de front-end:** esta porta é a porta pública aberta no gateway de aplicação. O tráfego chega a esta porta, sendo posteriormente redirecionado para um dos servidores de back-end.
* **Serviço de escuta:** o serviço de escuta possui uma porta de front-end, um protocolo (Http ou Https, sensível às maiúsculas e minúsculas) e o nome do certificado SSL (se configurar a descarga de SSL).
* **Regra:** a regra vincula o serviço de escuta e o conjunto de servidores de back-end e define para que conjunto de servidores de back-end o tráfego deve ser direcionado ao chegar a um determinado serviço de escuta. Atualmente, apenas é suportada a regra *básica*. A regra *básica* refere-se à distribuição de carga round robin.

## <a name="create-an-application-gateway"></a>Criar um gateway de aplicação

A diferença entre a utilização do Azure Clássico e do Azure Resource Manager é a ordem pela qual vai criar o gateway de aplicação e os itens que devem ser configurados.
Com o Resource Manager, todos os itens que constituem um gateway de aplicação são configurados individualmente e, em seguida, reunidos para criar o recurso do gateway de aplicação.

Os passos necessários para criar um gateway de aplicação encontram-se descritos abaixo:

1. Criar um grupo de recursos para o Resource Manager
2. Criar uma rede virtual e uma sub-rede para o gateway de aplicação
3. Criar um objeto de configuração do gateway de aplicação
4. Criar um recurso do gateway de aplicação

## <a name="create-a-resource-group-for-resource-manager"></a>Criar um grupo de recursos para o Resource Manager

Não se esqueça de mudar o modo do PowerShell para utilizar o cmdlets do Azure Resource Manager. Para obter mais informações, veja [Using Windows PowerShell with Resource Manager (Usar o Windows PowerShell com o Resource Manager)](../azure-resource-manager/management/manage-resources-powershell.md).

### <a name="step-1"></a>Passo 1

```powershell
Connect-AzAccount
```

### <a name="step-2"></a>Passo 2

Verifique as subscrições da conta.

```powershell
Get-AzSubscription
```

Ser-lhe-á solicitado para autenticar com as suas credenciais.

### <a name="step-3"></a>Passo 3

Escolha qual das subscrições do Azure utilizar.

```powershell
Select-AzSubscription -Subscriptionid "GUID of subscription"
```

### <a name="step-4"></a>Passo 4

Crie um grupo de recursos (ignore este passo se estiver a utilizar um grupo de recursos existente).

```powershell
New-AzResourceGroup -Name appgw-rg -location "West US"
```

O Azure Resource Manager requer que todos os grupos de recursos especifiquem uma localização. Isto é utilizado como a localização predefinida para recursos nesse grupo de recursos. Verifique se todos os comandos para criar um gateway de aplicação utilizam o mesmo grupo de recursos.

No exemplo anterior, criámos um grupo de recursos chamado "appgw-rg" e localização "West US".

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Criar uma rede virtual e uma sub-rede para o gateway de aplicação

O exemplo que se segue mostra como criar uma rede virtual utilizando o Resource Manager:

### <a name="step-1"></a>Passo 1

```powershell
$subnetconfig = New-AzVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24
```

Este passo atribui o intervalo de endereços 10.0.0.0/24 a uma variável de sub-rede a ser usada para criar uma rede virtual.

### <a name="step-2"></a>Passo 2

```powershell
$vnet = New-AzVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnetconfig
```

Este passo cria uma rede virtual chamada "appgwvnet" no grupo de recursos "appgw-rg" para a região oeste dos EUA usando o prefixo 10.0.0.0/16 com sub-rede 10.0.0.0/24.

### <a name="step-3"></a>Passo 3

```powershell
$subnet = $vnet.subnets[0]
```

Este passo atribui o objeto da sub-rede a $subnet variáveis para os próximos passos.

## <a name="create-an-application-gateway-configuration-object"></a>Criar um objeto de configuração do gateway de aplicação

### <a name="step-1"></a>Passo 1

```powershell
$gipconfig = New-AzApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet
```

Este passo cria uma configuração IP de gateway de aplicação chamada "gatewayIP01". Ao iniciar, o Application Gateway escolhe um endereço IP na subrede configurada e encaminha o tráfego da rede para os endereços IP no conjunto de IPs de back-end. Note que cada instância terá um endereço IP.

### <a name="step-2"></a>Passo 2

```powershell
$pool = New-AzApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 10.1.1.8,10.1.1.9,10.1.1.10
```

Este passo configura o conjunto de endereços IP back-end denominado "pool01" com endereços IP "10.1.1.8, 10.1.1.9, 10.1.1.10". Estes endereços são os endereços IP que irão receber o tráfego de rede do ponto final do IP de front-end. Deve substituir os endereços IP acima para adicionar os seus próprios pontos finais do endereço IP da aplicação.

### <a name="step-3"></a>Passo 3

```powershell
$poolSetting = New-AzApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled
```

Este passo configura a definição do gateway de aplicação "poolsetting01" para o tráfego de rede equilibrado de carga na piscina traseira.

### <a name="step-4"></a>Passo 4

```powershell
$fp = New-AzApplicationGatewayFrontendPort -Name frontendport01  -Port 80
```

Este passo configura a porta IP frontal chamada "frontendport01" para o ILB.

### <a name="step-5"></a>Passo 5

```powershell
$fipconfig = New-AzApplicationGatewayFrontendIPConfig -Name fipconfig01 -Subnet $subnet
```

Este passo cria a configuração IP frontal chamada "fipconfig01" e associa-a a um IP privado a partir da sub-rede de rede virtual atual.

### <a name="step-6"></a>Passo 6

```powershell
$listener = New-AzApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp
```

Este passo cria o ouvinte chamado "ouvinte01" e associa a porta frontal à configuração IP frontal.

### <a name="step-7"></a>Passo 7

```powershell
$rule = New-AzApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
```

Este passo cria a regra de encaminhamento do balançador de carga chamada "regra01" que configura o comportamento do equilibrador de carga.

### <a name="step-8"></a>Passo 8

```powershell
$sku = New-AzApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
```

Este passo configura o tamanho da instância do gateway de aplicação.

> [!NOTE]
> O valor predefinido para a Capacidade é 2. Para o Nome Sku, pode escolher entre Standard_Small, Standard_Medium e Standard_Large.

## <a name="create-an-application-gateway-by-using-new-azureapplicationgateway"></a>Criar um gateway de aplicação com o New-AzureApplicationGateway

Cria um gateway de aplicação com todos os itens de configuração dos passos anteriores. Neste exemplo, o gateway de aplicação é designado “appgwtest”.

```powershell
$appgw = New-AzApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku
```

Este passo cria um gateway de aplicação com todos os itens de configuração dos passos anteriores. No exemplo, o gateway de aplicação é designado “appgwtest”.

## <a name="delete-an-application-gateway"></a>Eliminar um gateway de aplicação

Para eliminar um gateway de aplicação, tem de fazer os seguintes passos para:

1. Utilize o cmdlet `Stop-AzApplicationGateway` para parar o gateway.
2. Utilize o cmdlet `Remove-AzApplicationGateway` para remover o gateway.
3. Verifique se o gateway foi removido com o cmdlet `Get-AzureApplicationGateway`.

### <a name="step-1"></a>Passo 1

Obtenha o objeto do gateway de aplicação e associe-o a uma variável “$getgw”.

```powershell
$getgw =  Get-AzApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg
```

### <a name="step-2"></a>Passo 2

Utilize `Stop-AzApplicationGateway` para parar o gateway de aplicação. Esta amostra mostra o `Stop-AzApplicationGateway` cmdlet na primeira linha, seguido da saída.

```powershell
Stop-AzApplicationGateway -ApplicationGateway $getgw  
```

```
VERBOSE: 9:49:34 PM - Begin Operation: Stop-AzureApplicationGateway
VERBOSE: 10:10:06 PM - Completed Operation: Stop-AzureApplicationGateway
Name       HTTP Status Code     Operation ID                             Error
----       ----------------     ------------                             ----
Successful OK                   ce6c6c95-77b4-2118-9d65-e29defadffb8
```

Assim que o gateway de aplicação estiver no estado parado, utilize o cmdlet `Remove-AzApplicationGateway` para remover o serviço.

```powershell
Remove-AzApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Force
```

```
VERBOSE: 10:49:34 PM - Begin Operation: Remove-AzureApplicationGateway
VERBOSE: 10:50:36 PM - Completed Operation: Remove-AzureApplicationGateway
Name       HTTP Status Code     Operation ID                             Error
----       ----------------     ------------                             ----
Successful OK                   055f3a96-8681-2094-a304-8d9a11ad8301
```

> [!NOTE]
> Pode utilizar o comutador **-force** para eliminar a mensagem de confirmação de remoção.

Para verificar se o serviço foi removido, pode utilizar o cmdlet `Get-AzApplicationGateway`. Este passo não é necessário.

```powershell
Get-AzApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg
```

```
VERBOSE: 10:52:46 PM - Begin Operation: Get-AzureApplicationGateway

Get-AzureApplicationGateway : ResourceNotFound: The gateway does not exist.
```

## <a name="next-steps"></a>Passos seguintes

Se pretender configurar a descarga de SSL, veja [Configure an application gateway for SSL offload (Configurar um gateway de aplicação para a descarga de SSL)](./tutorial-ssl-powershell.md).

Se pretender obter mais informações sobre as opções de balanceamento de carga em geral, veja:

* [Balanceador de Carga do Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Gestor de Tráfego do Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)