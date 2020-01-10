---
title: Como usar o gerenciamento de API na rede virtual com o gateway de aplicativo
titleSuffix: Azure API Management
description: Saiba como instalar e configurar o gerenciamento de API do Azure na rede virtual interna com o gateway de aplicativo (WAF) como front-end
services: api-management
documentationcenter: ''
author: solankisamir
manager: kjoshi
editor: vlvinogr
ms.assetid: a8c982b2-bca5-4312-9367-4a0bbc1082b1
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: sasolank
ms.openlocfilehash: 4e4d4c69eb51e0058d3b6b561b5167051079bf89
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75442694"
---
# <a name="integrate-api-management-in-an-internal-vnet-with-application-gateway"></a>Integrar o gerenciamento de API em uma VNET interna com o gateway de aplicativo

## <a name="overview"></a> Visão geral

O serviço de gerenciamento de API pode ser configurado em um modo interno de Entrada na Rede virtual, o que o torna acessível somente de dentro da rede virtual. Aplicativo Azure gateway é um serviço PAAS, que fornece um balanceador de carga de camada 7. Ele atua como um serviço de proxy reverso e fornece sua oferta de um WAF (firewall do aplicativo Web).

A combinação do gerenciamento de API provisionado em uma VNET interna com o front-end do gateway de aplicativo habilita os seguintes cenários:

* Use o mesmo recurso de gerenciamento de API para consumo por consumidores internos e consumidores externos.
* Use um único recurso de gerenciamento de API e tenha um subconjunto de APIs definidas no gerenciamento de API disponível para consumidores externos.
* Forneça uma maneira alternativa para alternar o acesso ao gerenciamento de API da Internet pública e ativar e desativar.

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para seguir as etapas descritas neste artigo, você deve ter:

* Uma subscrição ativa do Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

* Certificados-PFX e cer para o nome de host da API e pfx para o nome de host do portal do desenvolvedor.

## <a name="scenario"></a> Cenário do

Este artigo aborda como usar um único serviço de gerenciamento de API para consumidores internos e externos e fazer com que ele atue como um único front-end para APIs locais e na nuvem. Você também verá como expor apenas um subconjunto de suas APIs (no exemplo, elas são realçadas em verde) para consumo externo usando a funcionalidade de roteamento disponível no gateway de aplicativo.

No primeiro exemplo de configuração, todas as suas APIs são gerenciadas somente de dentro de sua rede virtual. Os consumidores internos (destacados em laranja) podem acessar todas as suas APIs internas e externas. O tráfego nunca sai para a Internet. A conectividade de alto desempenho é fornecida por meio de circuitos de rota expressa.

![rota de URL](./media/api-management-howto-integrate-internal-vnet-appgateway/api-management-howto-integrate-internal-vnet-appgateway.png)

## <a name="before-you-begin"></a> Antes de começar

* Verifique se está a utilizar a versão mais recente do Azure PowerShell. Consulte as instruções de instalação em [instalar Azure PowerShell](/powershell/azure/install-az-ps). 

## <a name="what-is-required-to-create-an-integration-between-api-management-and-application-gateway"></a>O que é necessário para criar uma integração entre o gerenciamento de API e o gateway de aplicativo?

* **Pool de servidores back-end:** Esse é o endereço IP virtual interno do serviço de gerenciamento de API.
* **Definições do conjunto de servidores de back-end:** cada conjunto tem definições como a porta, o protocolo e a afinidade com base em cookies. Essas configurações são aplicadas a todos os servidores no pool.
* **Porta de front-end:** Essa é a porta pública que é aberta no gateway de aplicativo. O tráfego que o está atingindo é redirecionado para um dos servidores back-end.
* **Serviço de escuta:** o serviço de escuta tem uma porta de front-end, um protocolo (Http ou Https; estes valores são sensíveis às maiúsculas e minúsculas) e o nome do certificado SSL (se configurar a descarga de SSL).
* **Regra:** A regra associa um ouvinte a um pool de servidores back-end.
* **Investigação de integridade personalizada:** Por padrão, o gateway de aplicativo usa investigações baseadas em endereço IP para descobrir quais servidores no BackendAddressPool estão ativos. O serviço de gerenciamento de API responde apenas às solicitações com o cabeçalho de host correto, portanto, as investigações padrão falham. Uma investigação de integridade personalizada precisa ser definida para ajudar o gateway de aplicativo a determinar que o serviço está ativo e deve encaminhar solicitações.
* **Certificados de domínio personalizados:** Para acessar o gerenciamento de API da Internet, você precisa criar um mapeamento CNAME de seu nome de host para o DNS de front-end do gateway de aplicativo. Isso garante que o cabeçalho do nome do host e o certificado enviados ao gateway de aplicativo que é encaminhado para o gerenciamento de API é um APIM que pode reconhecer como válido. Neste exemplo, usaremos dois certificados – para o back-end e para o portal do desenvolvedor.  

## <a name="overview-steps"></a> Etapas necessárias para integrar o gerenciamento de API e o gateway de aplicativo

1. Crie um grupo de recursos para o Resource Manager.
2. Crie uma rede virtual, uma sub-rede e um IP público para o gateway de aplicativo. Crie outra sub-rede para o gerenciamento de API.
3. Crie um serviço de gerenciamento de API dentro da sub-rede VNET criada acima e certifique-se de usar o modo interno.
4. Configure um nome de domínio personalizado no serviço de gerenciamento de API.
5. Crie um objeto de configuração do gateway de aplicativo.
6. Criar um recurso de gateway de aplicativo.
7. Crie um CNAME a partir do nome DNS público do gateway de aplicativo para o nome de host do proxy de gerenciamento de API.

## <a name="exposing-the-developer-portal-externally-through-application-gateway"></a>Expondo o portal do desenvolvedor externamente por meio do gateway de aplicativo

Neste guia, também vamos expor o **portal do desenvolvedor** para públicos externos por meio do gateway de aplicativo. Ele requer etapas adicionais para criar o ouvinte, investigação, configurações e regras do portal do desenvolvedor. Todos os detalhes são fornecidos nas respectivas etapas.

> [!WARNING]
> Se você usar o Azure AD ou autenticação de terceiros, habilite o recurso de [afinidade de sessão baseada em cookie](https://docs.microsoft.com/azure/application-gateway/overview#session-affinity) no gateway de aplicativo.

> [!WARNING]
> Para impedir que o WAF do gateway de aplicativo quebre o download da especificação do OpenAPI no portal do desenvolvedor, você precisa desabilitar a regra de firewall `942200 - "Detects MySQL comment-/space-obfuscated injections and backtick termination"`.

## <a name="create-a-resource-group-for-resource-manager"></a>Criar um grupo de recursos para o Resource Manager

### <a name="step-1"></a>Passo 1

Iniciar sessão no Azure

```powershell
Connect-AzAccount
```

Autentique com suas credenciais.

### <a name="step-2"></a>Passo 2

Selecione a assinatura desejada.

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000" # GUID of your Azure subscription
Get-AzSubscription -Subscriptionid $subscriptionId | Select-AzSubscription
```

### <a name="step-3"></a>Passo 3

Crie um novo grupo de recursos (ignore este passo se estiver a utilizar um grupo de recursos existente).

```powershell
$resGroupName = "apim-appGw-RG" # resource group name
$location = "West US"           # Azure region
New-AzResourceGroup -Name $resGroupName -Location $location
```

O Azure Resource Manager requer que todos os grupos de recursos especifiquem uma localização. que é utilizada como a localização predefinida para os recursos nesse grupo de recursos. Verifique se todos os comandos para criar um gateway de aplicativo usam o mesmo grupo de recursos.

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Criar uma rede virtual e uma sub-rede para o gateway de aplicativo

O exemplo a seguir mostra como criar uma rede virtual usando o Gerenciador de recursos.

### <a name="step-1"></a>Passo 1

Atribua o intervalo de endereços 10.0.0.0/24 à variável de sub-rede a ser usada para o gateway de aplicativo ao criar uma rede virtual.

```powershell
$appgatewaysubnet = New-AzVirtualNetworkSubnetConfig -Name "apim01" -AddressPrefix "10.0.0.0/24"
```

### <a name="step-2"></a>Passo 2

Atribua o intervalo de endereços 10.0.1.0/24 à variável de sub-rede a ser usada para gerenciamento de API durante a criação de uma rede virtual.

```powershell
$apimsubnet = New-AzVirtualNetworkSubnetConfig -Name "apim02" -AddressPrefix "10.0.1.0/24"
```

### <a name="step-3"></a>Passo 3

Crie uma rede virtual chamada **appgwvnet** no grupo de recursos **APIM-appGw-RG** para a região oeste dos EUA. Use o prefixo 10.0.0.0/16 com sub-redes 10.0.0.0/24 e 10.0.1.0/24.

```powershell
$vnet = New-AzVirtualNetwork -Name "appgwvnet" -ResourceGroupName $resGroupName -Location $location -AddressPrefix "10.0.0.0/16" -Subnet $appgatewaysubnet,$apimsubnet
```

### <a name="step-4"></a>Passo 4

Atribuir uma variável de sub-rede para as próximas etapas

```powershell
$appgatewaysubnetdata = $vnet.Subnets[0]
$apimsubnetdata = $vnet.Subnets[1]
```

## <a name="create-an-api-management-service-inside-a-vnet-configured-in-internal-mode"></a>Criar um serviço de gerenciamento de API dentro de uma VNET configurada no modo interno

O exemplo a seguir mostra como criar um serviço de gerenciamento de API em uma VNET configurada somente para acesso interno.

### <a name="step-1"></a>Passo 1

Crie um objeto de rede virtual de gerenciamento de API usando a sub-rede $apimsubnetdata criada acima.

```powershell
$apimVirtualNetwork = New-AzApiManagementVirtualNetwork -SubnetResourceId $apimsubnetdata.Id
```

### <a name="step-2"></a>Passo 2

Crie um serviço de gerenciamento de API dentro da rede virtual.

```powershell
$apimServiceName = "ContosoApi"       # API Management service instance name
$apimOrganization = "Contoso"         # organization name
$apimAdminEmail = "admin@contoso.com" # administrator's email address
$apimService = New-AzApiManagement -ResourceGroupName $resGroupName -Location $location -Name $apimServiceName -Organization $apimOrganization -AdminEmail $apimAdminEmail -VirtualNetwork $apimVirtualNetwork -VpnType "Internal" -Sku "Developer"
```

Depois que o comando acima for executado com sucesso, consulte a [configuração de DNS necessária para acessar o serviço de gerenciamento de API VNET interno](api-management-using-with-internal-vnet.md#apim-dns-configuration) para acessá-lo. Esta etapa pode levar mais de meia hora.

## <a name="set-up-a-custom-domain-name-in-api-management"></a>Configurar um nome de domínio personalizado no gerenciamento de API

### <a name="step-1"></a>Passo 1

Inicialize as variáveis a seguir com os detalhes dos certificados com chaves privadas para os domínios. Neste exemplo, usaremos `api.contoso.net` e `portal.contoso.net`.  

```powershell
$gatewayHostname = "api.contoso.net"                 # API gateway host
$portalHostname = "portal.contoso.net"               # API developer portal host
$gatewayCertCerPath = "C:\Users\Contoso\gateway.cer" # full path to api.contoso.net .cer file
$gatewayCertPfxPath = "C:\Users\Contoso\gateway.pfx" # full path to api.contoso.net .pfx file
$portalCertPfxPath = "C:\Users\Contoso\portal.pfx"   # full path to portal.contoso.net .pfx file
$gatewayCertPfxPassword = "certificatePassword123"   # password for api.contoso.net pfx certificate
$portalCertPfxPassword = "certificatePassword123"    # password for portal.contoso.net pfx certificate

$certPwd = ConvertTo-SecureString -String $gatewayCertPfxPassword -AsPlainText -Force
$certPortalPwd = ConvertTo-SecureString -String $portalCertPfxPassword -AsPlainText -Force
```

### <a name="step-2"></a>Passo 2

Crie e defina os objetos de configuração do nome do host para o proxy e para o Portal.  

```powershell
$proxyHostnameConfig = New-AzApiManagementCustomHostnameConfiguration -Hostname $gatewayHostname -HostnameType Proxy -PfxPath $gatewayCertPfxPath -PfxPassword $certPwd
$portalHostnameConfig = New-AzApiManagementCustomHostnameConfiguration -Hostname $portalHostname -HostnameType DeveloperPortal -PfxPath $portalCertPfxPath -PfxPassword $certPortalPwd

$apimService.ProxyCustomHostnameConfiguration = $proxyHostnameConfig
$apimService.PortalCustomHostnameConfiguration = $portalHostnameConfig
Set-AzApiManagement -InputObject $apimService
```

> [!NOTE]
> Para configurar a conectividade herdada do portal do desenvolvedor, você precisa substituir `-HostnameType DeveloperPortal` por `-HostnameType Portal`.

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Criar um endereço IP público para a configuração de front-end

Crie um recurso de IP público **publicIP01** no grupo de recursos.

```powershell
$publicip = New-AzPublicIpAddress -ResourceGroupName $resGroupName -name "publicIP01" -location $location -AllocationMethod Dynamic
```

É atribuído um endereço IP ao gateway de aplicação quando o serviço é iniciado.

## <a name="create-application-gateway-configuration"></a>Criar configuração do gateway de aplicativo

Tem de configurar todos os itens de configuração antes de criar o gateway de aplicação. Com os seguintes passos, irá criar os itens de configuração necessários para um recurso do gateway de aplicação.

### <a name="step-1"></a>Passo 1

Crie uma configuração de IP do gateway de aplicação com o nome **gatewayIP01**. Ao iniciar, o Application Gateway escolhe um endereço IP na subrede configurada e encaminha o tráfego da rede para os endereços IP no conjunto de IPs de back-end. Note que cada instância terá um endereço IP.

```powershell
$gipconfig = New-AzApplicationGatewayIPConfiguration -Name "gatewayIP01" -Subnet $appgatewaysubnetdata
```

### <a name="step-2"></a>Passo 2

Configure a porta de IP de front-end para o ponto de extremidade de IP público. Essa porta é a porta à qual os usuários finais se conectam.

```powershell
$fp01 = New-AzApplicationGatewayFrontendPort -Name "port01"  -Port 443
```

### <a name="step-3"></a>Passo 3

Configure o IP de front-end com o ponto final de IP público.

```powershell
$fipconfig01 = New-AzApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip
```

### <a name="step-4"></a>Passo 4

Configure os certificados para o gateway de aplicativo, que serão usados para descriptografar e criptografar novamente o tráfego transmitido.

```powershell
$cert = New-AzApplicationGatewaySslCertificate -Name "cert01" -CertificateFile $gatewayCertPfxPath -Password $certPwd
$certPortal = New-AzApplicationGatewaySslCertificate -Name "cert02" -CertificateFile $portalCertPfxPath -Password $certPortalPwd
```

### <a name="step-5"></a>Passo 5

Crie os ouvintes HTTP para o gateway de aplicativo. Atribua a configuração de IP de front-end, a porta e os certificados SSL a eles.

```powershell
$listener = New-AzApplicationGatewayHttpListener -Name "listener01" -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $cert -HostName $gatewayHostname -RequireServerNameIndication true
$portalListener = New-AzApplicationGatewayHttpListener -Name "listener02" -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $certPortal -HostName $portalHostname -RequireServerNameIndication true
```

### <a name="step-6"></a>Passo 6

Crie investigações personalizadas para o serviço de gerenciamento de API `ContosoApi` ponto de extremidade de domínio de proxy. O caminho `/status-0123456789abcdef` é um ponto de extremidade de integridade padrão hospedado em todos os serviços de gerenciamento de API. Defina `api.contoso.net` como um nome de host de investigação personalizado para protegê-lo com o certificado SSL.

> [!NOTE]
> O nome de host `contosoapi.azure-api.net` é o nome de host de proxy padrão configurado quando um serviço chamado `contosoapi` é criado no Azure público.
>

```powershell
$apimprobe = New-AzApplicationGatewayProbeConfig -Name "apimproxyprobe" -Protocol "Https" -HostName $gatewayHostname -Path "/status-0123456789abcdef" -Interval 30 -Timeout 120 -UnhealthyThreshold 8
$apimPortalProbe = New-AzApplicationGatewayProbeConfig -Name "apimportalprobe" -Protocol "Https" -HostName $portalHostname -Path "/signin" -Interval 60 -Timeout 300 -UnhealthyThreshold 8
```

### <a name="step-7"></a>Passo 7

Carregue o certificado a ser usado nos recursos do pool de back-end habilitado para SSL. Esse é o mesmo certificado que você forneceu na etapa 4 acima.

```powershell
$authcert = New-AzApplicationGatewayAuthenticationCertificate -Name "whitelistcert1" -CertificateFile $gatewayCertCerPath
```

### <a name="step-8"></a>Passo 8

Defina as configurações de back-end HTTP para o gateway de aplicativo. Isso inclui a definição de um limite de tempo limite para a solicitação de back-end, após o qual elas são canceladas. Esse valor é diferente do tempo limite de investigação.

```powershell
$apimPoolSetting = New-AzApplicationGatewayBackendHttpSettings -Name "apimPoolSetting" -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimprobe -AuthenticationCertificates $authcert -RequestTimeout 180
$apimPoolPortalSetting = New-AzApplicationGatewayBackendHttpSettings -Name "apimPoolPortalSetting" -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimPortalProbe -AuthenticationCertificates $authcert -RequestTimeout 180
```

### <a name="step-9"></a>Passo 9

Configure um pool de endereços IP de back-end chamado **apimbackend** com o endereço IP virtual interno do serviço de gerenciamento de API criado acima.

```powershell
$apimProxyBackendPool = New-AzApplicationGatewayBackendAddressPool -Name "apimbackend" -BackendIPAddresses $apimService.PrivateIPAddresses[0]
```

### <a name="step-10"></a>Etapa 10

Crie regras para que o gateway de aplicativo use o roteamento básico.

```powershell
$rule01 = New-AzApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType Basic -HttpListener $listener -BackendAddressPool $apimProxyBackendPool -BackendHttpSettings $apimPoolSetting
$rule02 = New-AzApplicationGatewayRequestRoutingRule -Name "rule2" -RuleType Basic -HttpListener $portalListener -BackendAddressPool $apimProxyBackendPool -BackendHttpSettings $apimPoolPortalSetting
```

> [!TIP]
> Altere o-RuleType e o Routing para restringir o acesso a determinadas páginas do portal do desenvolvedor.

### <a name="step-11"></a>Etapa 11

Configure o número de instâncias e o tamanho do gateway de aplicativo. Neste exemplo, estamos usando o [SKU WAF](../application-gateway/application-gateway-webapplicationfirewall-overview.md) para aumentar a segurança do recurso de gerenciamento de API.

```powershell
$sku = New-AzApplicationGatewaySku -Name "WAF_Medium" -Tier "WAF" -Capacity 2
```

### <a name="step-12"></a>Etapa 12

Configure WAF para estar no modo de "prevenção".

```powershell
$config = New-AzApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"
```

## <a name="create-application-gateway"></a>Criar gateway de aplicativo

Crie um gateway de aplicativo com todos os objetos de configuração das etapas anteriores.

```powershell
$appgwName = "apim-app-gw"
$appgw = New-AzApplicationGateway -Name $appgwName -ResourceGroupName $resGroupName -Location $location -BackendAddressPools $apimProxyBackendPool -BackendHttpSettingsCollection $apimPoolSetting, $apimPoolPortalSetting  -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener, $portalListener -RequestRoutingRules $rule01, $rule02 -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $cert, $certPortal -AuthenticationCertificates $authcert -Probes $apimprobe, $apimPortalProbe
```

## <a name="cname-the-api-management-proxy-hostname-to-the-public-dns-name-of-the-application-gateway-resource"></a>CNAME do nome de host do proxy de gerenciamento de API para o nome DNS público do recurso de gateway de aplicativo

Depois de o gateway ser criado, o passo seguinte consiste em configurar o front-end para a comunicação. Ao usar um IP público, o gateway de aplicativo requer um nome DNS atribuído dinamicamente, o que pode não ser fácil de usar.

O nome DNS do gateway de aplicativo deve ser usado para criar um registro CNAME que aponta o nome de host do proxy APIM (por exemplo, `api.contoso.net` nos exemplos acima) para esse nome DNS. Para configurar o registro CNAME de IP de front-end, recupere os detalhes do gateway de aplicativo e seu nome DNS/IP associado usando o elemento PublicIPAddress. O uso de registros A não é recomendado, pois o VIP pode ser alterado na reinicialização do gateway.

```powershell
Get-AzPublicIpAddress -ResourceGroupName $resGroupName -Name "publicIP01"
```

## <a name="summary"></a> Resumo
O gerenciamento de API do Azure configurado em uma VNET fornece uma única interface de gateway para todas as APIs configuradas, estejam elas hospedadas localmente ou na nuvem. A integração do gateway de aplicativo ao gerenciamento de API fornece a flexibilidade de habilitar seletivamente as APIs específicas para que possam ser acessadas na Internet, bem como fornecer um firewall do aplicativo Web como um front-end para sua instância de gerenciamento de API.

## <a name="next-steps"></a> Próximas etapas
* Saiba mais sobre o gateway de Aplicativo Azure
  * [Visão geral do gateway de aplicativo](../application-gateway/application-gateway-introduction.md)
  * [Firewall do aplicativo Web do gateway de aplicativo](../application-gateway/application-gateway-webapplicationfirewall-overview.md)
  * [Gateway de aplicativo usando roteamento baseado em caminho](../application-gateway/application-gateway-create-url-route-arm-ps.md)
* Saiba mais sobre o gerenciamento de API e o VNETs
  * [Usando o gerenciamento de API disponível somente na VNET](api-management-using-with-internal-vnet.md)
  * [Usando o gerenciamento de API na VNET](api-management-using-with-vnet.md)
