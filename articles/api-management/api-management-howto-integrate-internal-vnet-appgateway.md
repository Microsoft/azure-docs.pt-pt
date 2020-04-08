---
title: Como utilizar a Gestão API em Rede Virtual com Gateway de Aplicação
titleSuffix: Azure API Management
description: Saiba como configurar e configurar a Gestão de API Azure em Rede Virtual Interna com Gateway de Aplicação (WAF) como FrontEnd
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
ms.openlocfilehash: 733f4b74ca7643476586189b36f4e1d3e446968b
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811167"
---
# <a name="integrate-api-management-in-an-internal-vnet-with-application-gateway"></a>Integrar a Gestão da API num VNET interno com Gateway de Aplicação

## <a name="overview"></a><a name="overview"> </a> Visão geral

O serviço de Gestão API pode ser configurado numa Rede Virtual em modo interno, o que o torna acessível apenas a partir da Rede Virtual. O Portal de Aplicações Azure é um serviço PAAS, que fornece um equilíbrio de carga Camada-7. Atua como um serviço de procuração inversa e fornece entre a sua oferta uma Firewall de Aplicação Web (WAF).

Combinar a Gestão API provisionada num VNET interno com o frontend do Gateway de aplicação permite os seguintes cenários:

* Utilize o mesmo recurso de Gestão API para consumo tanto por parte dos consumidores internos como dos consumidores externos.
* Utilize um único recurso de Gestão API e tenha um subconjunto de APIs definido na Gestão aPI disponível para consumidores externos.
* Fornecer uma forma chave-de-mão para mudar o acesso à Gestão API a partir da Internet pública de e para a internet.

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para seguir os passos descritos neste artigo, deve ter:

* Uma subscrição ativa do Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

* Certificados - pfx e cer para o nome de anfitrião da API e pfx para o nome de anfitrião do portal de desenvolvimento.

## <a name="scenario"></a><a name="scenario"> </a> Cenário

Este artigo abrange a utilização de um único serviço de Gestão de API para consumidores internos e externos e fazê-lo funcionar como um único ponto de partida tanto nas instalações como nas APIs em nuvem. Também verá como expor apenas um subconjunto das suas APIs (no exemplo em que são destacadas em verde) para consumo externo utilizando a funcionalidade de encaminhamento disponível no Gateway da Aplicação.

No primeiro exemplo de configuração, todas as suas APIs são geridas apenas a partir da sua Rede Virtual. Os consumidores internos (realçados em laranja) podem aceder a todas as suas APIs internas e externas. O trânsito nunca vai para a internet. A conectividade de alto desempenho é entregue através dos circuitos Da Rota Expresso.

![rota url](./media/api-management-howto-integrate-internal-vnet-appgateway/api-management-howto-integrate-internal-vnet-appgateway.png)

## <a name="before-you-begin"></a><a name="before-you-begin"> </a> Antes de começar

* Verifique se está a utilizar a versão mais recente do Azure PowerShell. Consulte as instruções de instalação na [Instalação Azure PowerShell](/powershell/azure/install-az-ps). 

## <a name="what-is-required-to-create-an-integration-between-api-management-and-application-gateway"></a>O que é necessário para criar uma integração entre a Gestão da API e o Gateway de Aplicações?

* **Piscina de servidorde back-end:** Este é o endereço IP virtual interno do serviço de Gestão API.
* **Definições de piscina de servidor de back-end:** Cada piscina tem configurações como porto, protocolo e afinidade baseada em cookies. Estas definições são aplicadas a todos os servidores dentro da piscina.
* **Porta frontal:** Este é o porto público que é aberto na porta de entrada de candidaturas. O tráfego que o atinge é redirecionado para um dos servidores de back-end.
* **Ouvinte:** O ouvinte tem uma porta frontal, um protocolo (Http ou Https, estes valores são sensíveis a casos) e o nome do certificado TLS/SSL (se configurar a descarga de TLS).
* **Regra:** A regra liga um ouvinte a um conjunto de servidores de back-end.
* **Sonda de saúde personalizada:** O Gateway da aplicação, por padrão, utiliza sondas baseadas em endereçoIP para descobrir quais os servidores do BackendAddressPool ativos. O serviço de Gestão API apenas responde a pedidos com o cabeçalho do anfitrião correto, daí que as sondas padrão falhem. Uma sonda de saúde personalizada precisa de ser definida para ajudar a aplicação gateway determinar que o serviço está vivo e deve encaminhar pedidos.
* **Certificados de domínio personalizados:** Para aceder à Gestão API a partir da internet, é necessário criar um mapeamento CNAME do seu nome de anfitrião para o nome DNS frontal do Gateway de aplicação. Isto garante que o cabeçalho e certificado de nome de anfitrião enviado saqueado para o Application Gateway que é encaminhado para a API Management é um a que a APIM pode reconhecer como válido. Neste exemplo, utilizaremos dois certificados - para o backend e para o portal de desenvolvimento.  

## <a name="steps-required-for-integrating-api-management-and-application-gateway"></a><a name="overview-steps"> </a> Etapas necessárias para integrar API Management and Application Gateway

1. Crie um grupo de recursos para o Resource Manager.
2. Criar uma Rede Virtual, subnet e IP público para o Gateway de Aplicação. Criar outra subnet para a Gestão de API.
3. Crie um serviço de Gestão API dentro da subnet VNET criada acima e certifique-se de que utiliza o modo Interno.
4. Configurar um nome de domínio personalizado no serviço de Gestão API.
5. Crie um objeto de configuração de Gateway de Aplicação.
6. Criar um recurso Application Gateway.
7. Crie um CNAME a partir do nome público dNS do Gateway de Aplicação para o nome de anfitrião proxy da API Management.

## <a name="exposing-the-developer-portal-externally-through-application-gateway"></a>Expondo o portal de desenvolvimento externamente através do Application Gateway

Neste guia vamos também expor o portal de **desenvolvimento** a públicos externos através do Gateway de Aplicação. Requer passos adicionais para criar o ouvinte, sonda, configurações e regras do portal do desenvolvedor. Todos os detalhes são fornecidos nas respetivas etapas.

> [!WARNING]
> Se utilizar a autenticação Azure AD ou de terceiros, por favor, ative a funcionalidade [de afinidade de sessão baseada em cookies](../application-gateway/features.md#session-affinity) no Gateway da Aplicação.

> [!WARNING]
> Para evitar que o Gateway WAF de aplicação quebrou o download da `942200 - "Detects MySQL comment-/space-obfuscated injections and backtick termination"`especificação OpenAPI no portal do desenvolvedor, é necessário desativar a regra da firewall .

## <a name="create-a-resource-group-for-resource-manager"></a>Criar um grupo de recursos para o Resource Manager

### <a name="step-1"></a>Passo 1

Iniciar sessão no Azure

```powershell
Connect-AzAccount
```

Autenticacom as tuas credenciais.

### <a name="step-2"></a>Passo 2

Selecione a subscrição desejada.

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000" # GUID of your Azure subscription
Get-AzSubscription -Subscriptionid $subscriptionId | Select-AzSubscription
```

### <a name="step-3"></a>Passo 3

Crie um novo grupo de recursos (ignore este passo se estiver a utilizar um grupo de recursos existente).

```powershell
$resGroupName = "apim-appGw-RG" # resource group name
$location = "West US"           # Azure region
New-AzResourceGroup -Name $resGroupName -Location $location
```

O Azure Resource Manager requer que todos os grupos de recursos especifiquem uma localização. Isto é utilizado como a localização predefinida para recursos nesse grupo de recursos. Certifique-se de que todos os comandos para criar um gateway de aplicação utilizam o mesmo grupo de recursos.

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Criar uma Rede Virtual e uma subnet para o gateway da aplicação

O exemplo que se segue mostra como criar uma Rede Virtual utilizando o Gestor de Recursos.

### <a name="step-1"></a>Passo 1

Atribuir o intervalo de endereços 10.0.0.0/24 à variável subnet a utilizar para o Gateway de Aplicação enquanto cria uma Rede Virtual.

```powershell
$appgatewaysubnet = New-AzVirtualNetworkSubnetConfig -Name "apim01" -AddressPrefix "10.0.0.0/24"
```

### <a name="step-2"></a>Passo 2

Atribuir a gama de endereços 10.0.1.0/24 à variável subnet a utilizar para gestão de API enquanto cria uma Rede Virtual.

```powershell
$apimsubnet = New-AzVirtualNetworkSubnetConfig -Name "apim02" -AddressPrefix "10.0.1.0/24"
```

### <a name="step-3"></a>Passo 3

Crie uma Rede Virtual chamada **appgwvnet** em grupo de recursos **apim-appGw-RG** para a região dos EUA Ocidentais. Utilize o prefixo 10.0.0.0.0/16 com subredes 10.0.0.0.0/24 e 10.0.1.0/24.

```powershell
$vnet = New-AzVirtualNetwork -Name "appgwvnet" -ResourceGroupName $resGroupName -Location $location -AddressPrefix "10.0.0.0/16" -Subnet $appgatewaysubnet,$apimsubnet
```

### <a name="step-4"></a>Passo 4

Atribuir uma variável de sub-rede para os próximos passos

```powershell
$appgatewaysubnetdata = $vnet.Subnets[0]
$apimsubnetdata = $vnet.Subnets[1]
```

## <a name="create-an-api-management-service-inside-a-vnet-configured-in-internal-mode"></a>Criar um serviço de Gestão API dentro de um VNET configurado em modo interno

O exemplo seguinte mostra como criar um serviço de Gestão API num VNET configurado apenas para acesso interno.

### <a name="step-1"></a>Passo 1

Crie um objeto de Rede Virtual de Gestão API utilizando a rede $apimsubnetdata criada acima.

```powershell
$apimVirtualNetwork = New-AzApiManagementVirtualNetwork -SubnetResourceId $apimsubnetdata.Id
```

### <a name="step-2"></a>Passo 2

Criar um serviço de Gestão API dentro da Rede Virtual.

```powershell
$apimServiceName = "ContosoApi"       # API Management service instance name
$apimOrganization = "Contoso"         # organization name
$apimAdminEmail = "admin@contoso.com" # administrator's email address
$apimService = New-AzApiManagement -ResourceGroupName $resGroupName -Location $location -Name $apimServiceName -Organization $apimOrganization -AdminEmail $apimAdminEmail -VirtualNetwork $apimVirtualNetwork -VpnType "Internal" -Sku "Developer"
```

Após o comando acima ter sucesso, consulte a [Configuração DNS necessária para aceder ao serviço interno de Gestão VNET API](api-management-using-with-internal-vnet.md#apim-dns-configuration) para aceder ao mesmo. Este passo pode demorar mais de meia hora.

## <a name="set-up-a-custom-domain-name-in-api-management"></a>Configurar um nome de domínio personalizado na Gestão API

> [!IMPORTANT]
> O [novo portal](api-management-howto-developer-portal.md) de desenvolvimento também requer a conectividade com o ponto final de gestão da API Management, para além dos passos abaixo.

### <a name="step-1"></a>Passo 1

Inicialize as seguintes variáveis com os detalhes dos certificados com chaves privadas para os domínios. Neste exemplo, iremos `api.contoso.net` `portal.contoso.net`utilizar e .  

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

### <a name="step-2"></a>Passo 2

Crie e detete tese os objetos de configuração do nome de anfitrião para o proxy e para o portal.  

```powershell
$proxyHostnameConfig = New-AzApiManagementCustomHostnameConfiguration -Hostname $gatewayHostname -HostnameType Proxy -PfxPath $gatewayCertPfxPath -PfxPassword $certPwd
$portalHostnameConfig = New-AzApiManagementCustomHostnameConfiguration -Hostname $portalHostname -HostnameType DeveloperPortal -PfxPath $portalCertPfxPath -PfxPassword $certPortalPwd

$apimService.ProxyCustomHostnameConfiguration = $proxyHostnameConfig
$apimService.PortalCustomHostnameConfiguration = $portalHostnameConfig
Set-AzApiManagement -InputObject $apimService
```

> [!NOTE]
> Para configurar a conectividade do portal `-HostnameType DeveloperPortal` de `-HostnameType Portal`desenvolvimento legado que precisa substituir por .

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Criar um endereço IP público para a configuração de front-end

Criar um recurso IP **público IP01** no grupo de recursos.

```powershell
$publicip = New-AzPublicIpAddress -ResourceGroupName $resGroupName -name "publicIP01" -location $location -AllocationMethod Dynamic
```

É atribuído um endereço IP ao gateway de aplicação quando o serviço é iniciado.

## <a name="create-application-gateway-configuration"></a>Criar configuração de gateway de aplicação

Tem de configurar todos os itens de configuração antes de criar o gateway de aplicação. Com os seguintes passos, irá criar os itens de configuração necessários para um recurso do gateway de aplicação.

### <a name="step-1"></a>Passo 1

Criar uma configuração IP de gateway de aplicação chamada **gatewayIP01**. Ao iniciar, o Application Gateway escolhe um endereço IP na subrede configurada e encaminha o tráfego da rede para os endereços IP no conjunto de IPs de back-end. Note que cada instância terá um endereço IP.

```powershell
$gipconfig = New-AzApplicationGatewayIPConfiguration -Name "gatewayIP01" -Subnet $appgatewaysubnetdata
```

### <a name="step-2"></a>Passo 2

Configure a porta IP frontal para o ponto final ip público. Esta porta é a porta a que os utilizadores finais se ligam.

```powershell
$fp01 = New-AzApplicationGatewayFrontendPort -Name "port01"  -Port 443
```

### <a name="step-3"></a>Passo 3

Configure o IP de front-end com o ponto final de IP público.

```powershell
$fipconfig01 = New-AzApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip
```

### <a name="step-4"></a>Passo 4

Configure os certificados para o Gateway de Aplicação, que será usado para desencriptar e encriptar o tráfego que passa.

```powershell
$cert = New-AzApplicationGatewaySslCertificate -Name "cert01" -CertificateFile $gatewayCertPfxPath -Password $certPwd
$certPortal = New-AzApplicationGatewaySslCertificate -Name "cert02" -CertificateFile $portalCertPfxPath -Password $certPortalPwd
```

### <a name="step-5"></a>Passo 5

Crie os ouvintes HTTP para o Gateway de Aplicação. Atribuir-lhes os certificados IP front-end, porta e TLS/SSL.

```powershell
$listener = New-AzApplicationGatewayHttpListener -Name "listener01" -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $cert -HostName $gatewayHostname -RequireServerNameIndication true
$portalListener = New-AzApplicationGatewayHttpListener -Name "listener02" -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $certPortal -HostName $portalHostname -RequireServerNameIndication true
```

### <a name="step-6"></a>Passo 6

Crie sondas personalizadas para `ContosoApi` o ponto final do domínio proxy do serviço api Management. O `/status-0123456789abcdef` caminho é um ponto final de saúde predefinido hospedado em todos os serviços de Gestão API. Despôs-se `api.contoso.net` como um nome de anfitrião de sonda personalizada para o fixar com o certificado TLS/SSL.

> [!NOTE]
> O nome `contosoapi.azure-api.net` de anfitrião é o nome `contosoapi` de anfitrião proxy predefinido configurado quando um serviço nomeado é criado em azure público.
>

```powershell
$apimprobe = New-AzApplicationGatewayProbeConfig -Name "apimproxyprobe" -Protocol "Https" -HostName $gatewayHostname -Path "/status-0123456789abcdef" -Interval 30 -Timeout 120 -UnhealthyThreshold 8
$apimPortalProbe = New-AzApplicationGatewayProbeConfig -Name "apimportalprobe" -Protocol "Https" -HostName $portalHostname -Path "/signin" -Interval 60 -Timeout 300 -UnhealthyThreshold 8
```

### <a name="step-7"></a>Passo 7

Faça o upload do certificado a utilizar nos recursos de reserva ativados pelo TLS. Este é o mesmo certificado que forneceu no Passo 4 acima.

```powershell
$authcert = New-AzApplicationGatewayAuthenticationCertificate -Name "whitelistcert1" -CertificateFile $gatewayCertCerPath
```

### <a name="step-8"></a>Passo 8

Configure as definições de backend HTTP para o Gateway da Aplicação. Isto inclui a definição de um prazo limite para o pedido de backend, após o qual são cancelados. Este valor é diferente do tempo de intervalo da sonda.

```powershell
$apimPoolSetting = New-AzApplicationGatewayBackendHttpSettings -Name "apimPoolSetting" -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimprobe -AuthenticationCertificates $authcert -RequestTimeout 180
$apimPoolPortalSetting = New-AzApplicationGatewayBackendHttpSettings -Name "apimPoolPortalSetting" -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimPortalProbe -AuthenticationCertificates $authcert -RequestTimeout 180
```

### <a name="step-9"></a>Passo 9

Configure um conjunto de endereços IP de back-end chamado **apimbackend** com o endereço IP virtual interno do serviço de Gestão API criado acima.

```powershell
$apimProxyBackendPool = New-AzApplicationGatewayBackendAddressPool -Name "apimbackend" -BackendIPAddresses $apimService.PrivateIPAddresses[0]
```

### <a name="step-10"></a>Passo 10

Crie regras para que o Gateway de Aplicação utilize o encaminhamento básico.

```powershell
$rule01 = New-AzApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType Basic -HttpListener $listener -BackendAddressPool $apimProxyBackendPool -BackendHttpSettings $apimPoolSetting
$rule02 = New-AzApplicationGatewayRequestRoutingRule -Name "rule2" -RuleType Basic -HttpListener $portalListener -BackendAddressPool $apimProxyBackendPool -BackendHttpSettings $apimPoolPortalSetting
```

> [!TIP]
> Altere o -RuleType e o encaminhamento, para restringir o acesso a determinadas páginas do portal de desenvolvimento.

### <a name="step-11"></a>Passo 11

Configure o número de instâncias e tamanho para o Gateway de Aplicação. Neste exemplo, estamos a utilizar o [WAF SKU](../application-gateway/application-gateway-webapplicationfirewall-overview.md) para uma maior segurança do recurso de Gestão API.

```powershell
$sku = New-AzApplicationGatewaySku -Name "WAF_Medium" -Tier "WAF" -Capacity 2
```

### <a name="step-12"></a>Passo 12

Configure o WAF para estar no modo "Prevenção".

```powershell
$config = New-AzApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"
```

## <a name="create-application-gateway"></a>Criar gateway de aplicação

Crie um Gateway de Aplicação com todos os objetos de configuração dos passos anteriores.

```powershell
$appgwName = "apim-app-gw"
$appgw = New-AzApplicationGateway -Name $appgwName -ResourceGroupName $resGroupName -Location $location -BackendAddressPools $apimProxyBackendPool -BackendHttpSettingsCollection $apimPoolSetting, $apimPoolPortalSetting  -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener, $portalListener -RequestRoutingRules $rule01, $rule02 -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $cert, $certPortal -AuthenticationCertificates $authcert -Probes $apimprobe, $apimPortalProbe
```

## <a name="cname-the-api-management-proxy-hostname-to-the-public-dns-name-of-the-application-gateway-resource"></a>CNAME o nome de anfitrião proxy da API Management para o nome público dNS do recurso Application Gateway

Depois de o gateway ser criado, o passo seguinte consiste em configurar o front-end para a comunicação. Ao utilizar um IP público, o Application Gateway requer um nome DNS dns dinamicamente atribuído, que pode não ser fácil de usar.

O nome DNS do Gateway de aplicação deve ser utilizado para criar um registo CNAME que aponte o nome de anfitrião proxy DaPIM (por exemplo, `api.contoso.net` nos exemplos acima) para este nome DNS. Para configurar o registo IP CNAME frontal, recupere os detalhes do Gateway de Aplicação e o seu nome IP/DNS associado utilizando o elemento PublicIPAddress. A utilização de registos A não é recomendada, uma vez que o VIP pode alterar-se no reinício do gateway.

```powershell
Get-AzPublicIpAddress -ResourceGroupName $resGroupName -Name "publicIP01"
```

## <a name="summary"></a><a name="summary"> </a> Resumo
A Azure API Management configurada num VNET fornece uma interface de gateway única para todas as APIs configuradas, quer estejam alojadas nas instalações ou na nuvem. Integrar o Gateway de Aplicações com a Gestão API proporciona a flexibilidade de permitir seletivamente que determinadas APIs sejam acessíveis na Internet, bem como fornecer uma Firewall de aplicação Web como um frontend para a sua instância de Gestão API.

## <a name="next-steps"></a><a name="next-steps"> </a> Próximos passos
* Saiba mais sobre o Portal de Aplicações Azure
  * [Visão geral do Gateway de Aplicações](../application-gateway/application-gateway-introduction.md)
  * [Application Gateway Web Application Firewall](../application-gateway/application-gateway-webapplicationfirewall-overview.md)
  * [Gateway de aplicação usando encaminhamento baseado em caminhos](../application-gateway/application-gateway-create-url-route-arm-ps.md)
* Saiba mais sobre Gestão aPi e VNETs
  * [Utilização da Gestão API disponível apenas no VNET](api-management-using-with-internal-vnet.md)
  * [Utilização da Gestão API em VNET](api-management-using-with-vnet.md)
