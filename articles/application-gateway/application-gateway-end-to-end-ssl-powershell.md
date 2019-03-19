---
title: Configurar SSL ponto a ponto com o Gateway de aplicação do Azure
description: Este artigo descreve como configurar o SSL ponto a ponto com o Gateway de aplicação do Azure com o PowerShell
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 1/10/2019
ms.author: victorh
ms.openlocfilehash: 3da9982d1af886a4329ddc77a7b297e9e285453e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58101555"
---
# <a name="configure-end-to-end-ssl-by-using-application-gateway-with-powershell"></a>Configurar SSL ponto a ponto com o Gateway de aplicação com o PowerShell

## <a name="overview"></a>Descrição geral

O Gateway de aplicação do Azure suporta a encriptação de ponto-a-ponto de tráfego. Gateway de aplicação termina a ligação SSL no gateway de aplicação. O gateway, em seguida, aplica-se as regras de encaminhamento para o tráfego, encripta novamente o pacote e reencaminha o pacote para o servidor de back-end apropriado com base em regras de encaminhamento definidas. Qualquer resposta do servidor Web atravessa o mesmo processo para o utilizador final.

Gateway de aplicação suporta a definir as opções de SSL personalizadas. Ele também oferece suporte a desativar as seguintes versões de protocolo: **TLSv1.0**, **TLSv1.1**, e **TLSv1.2**, bem a definir quais conjuntos de cifras para utilizar e a ordem de preferência. Para saber mais sobre as opções de SSL configuráveis, consulte a [descrição geral da política SSL](application-gateway-SSL-policy-overview.md).

> [!NOTE]
> SSL 2.0 e o SSL 3.0 estão desativados por predefinição e não podem ser ativada. Eles são considerados não seguros e não podem ser utilizados com o Gateway de aplicação.

![imagem do cenário][scenario]

## <a name="scenario"></a>Cenário

Neste cenário, irá aprender a criar um gateway de aplicação utilizando o SSL ponto a ponto com o PowerShell.

Neste cenário irão:

* Crie um grupo de recursos chamado **appgw-rg**.
* Criar uma rede virtual denominada **appgwvnet** com um espaço de endereços de **10.0.0.0/16**.
* Criar duas sub-redes chamadas **appgwsubnet** e **appsubnet**.
* Crie uma pequena aplicação gateway suporte ponto a ponto a encriptação SSL que versões de protocolo SSL de limites e a conjuntos de cifras.

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para configurar o SSL ponto a ponto com um gateway de aplicação, é necessário para o gateway de um certificado e certificados são necessários para os servidores de back-end. O certificado do gateway é utilizado para derivar uma chave simétrica de acordo com a especificação de protocolo SSL. A chave simétrica é utilizada, em seguida, encriptar e desencriptar o tráfego enviado para o gateway. O certificado de gateway tem de estar no formato Personal Information Exchange (PFX). Esse formato de arquivo permite-lhe exportar a chave privada que é necessário pelo gateway de aplicação para efetuar a encriptação e desencriptação de tráfego.

Para encriptação SSL de ponto-a-ponto, o back-end tem de estar na lista de permissões com o gateway de aplicação. Carregue o certificado público dos servidores de back-end para o gateway de aplicação. A adicionar o certificado garante que o gateway de aplicação comunica apenas com instâncias de back-end conhecidas. Isso protege ainda mais a comunicação de ponto-a-ponto.

O processo de configuração é descrito nas seções a seguir.

## <a name="create-the-resource-group"></a>Criar o grupo de recursos

Esta secção explica como criar um grupo de recursos que contém o gateway de aplicação.


1. Inicie sessão na sua conta do Azure.

   ```powershell
   Connect-AzAccount
   ```


2. Selecione a subscrição a utilizar para este cenário.

   ```powershell
   Select-Azsubscription -SubscriptionName "<Subscription name>"
   ```


3. Crie um grupo de recursos. (Ignore este passo se estiver a utilizar um grupo de recursos existente.)

   ```powershell
   New-AzResourceGroup -Name appgw-rg -Location "West US"
   ```

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Criar uma rede virtual e uma sub-rede para o gateway de aplicação

O exemplo seguinte cria uma rede virtual e duas sub-redes. Uma sub-rede é utilizada para armazenar o gateway de aplicação. A outra sub-rede é utilizada para os back-ends que alojam a aplicação web.


1. Atribua um intervalo de endereços da sub-rede a utilizar para o gateway de aplicação.

   ```powershell
   $gwSubnet = New-AzVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24
   ```

   > [!NOTE]
   > Sub-redes configuradas para um gateway de aplicação devem ser redimensionados adequadamente. Um gateway de aplicação pode ser configurado para até 10 instâncias. Cada instância terá um endereço IP da sub-rede. Muito pequeno de uma sub-rede pode afetar negativamente aumentar horizontalmente um gateway de aplicação.
   > 
   > 

2. Atribua um intervalo de endereços a ser utilizado para o conjunto de endereços de back-end.

   ```powershell
   $nicSubnet = New-AzVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24
   ```

3. Crie uma rede virtual com as sub-redes definidas nos passos anteriores.

   ```powershell
   $vnet = New-AzvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet
   ```

4. Obter o recurso de rede virtual e os recursos de sub-rede a utilizar nos passos que se seguem.

   ```powershell
   $vnet = Get-AzvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg
   $gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name 'appgwsubnet' -VirtualNetwork $vnet
   $nicSubnet = Get-AzVirtualNetworkSubnetConfig -Name 'appsubnet' -VirtualNetwork $vnet
   ```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Criar um endereço IP público para a configuração de front-end

Crie um recurso IP público a utilizar para o gateway de aplicação. Este endereço IP público é utilizado em um dos passos que se seguem.

```powershell
$publicip = New-AzPublicIpAddress -ResourceGroupName appgw-rg -Name 'publicIP01' -Location "West US" -AllocationMethod Dynamic
```

> [!IMPORTANT]
> Gateway de aplicação não suporta a utilização de um endereço IP público criado com uma etiqueta de domínio definido. É suportado apenas um endereço IP público com uma etiqueta de domínio criada dinamicamente. Se necessitar de um nome amigável do DNS para o gateway de aplicação, recomendamos que utilize um registo CNAME com um alias.

## <a name="create-an-application-gateway-configuration-object"></a>Criar um objeto de configuração do gateway de aplicação

Todos os itens de configuração são definidos antes de criar o gateway de aplicação. Com os seguintes passos, irá criar os itens de configuração necessários para um recurso do gateway de aplicação.

1. Crie uma configuração de IP do gateway de aplicação. Esta definição configura o que, das sub-redes, utiliza o gateway de aplicação. Quando o gateway de aplicação é iniciado, ele escolhe um endereço IP da sub-rede configurada e rotas de tráfego de rede para os endereços IP no conjunto de IP de back-end. Note que cada instância terá um endereço IP.

   ```powershell
   $gipconfig = New-AzApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet
   ```


2. Crie uma configuração de IP Front-end. Esta definição é mapeada um endereço IP privado ou público para o front-end do gateway de aplicação. O passo seguinte associa o endereço IP público no passo anterior com a configuração de IP Front-end.

   ```powershell
   $fipconfig = New-AzApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip
   ```

3. Configure o conjunto de endereços IP de back-end com os endereços IP dos servidores web de back-end. Estes endereços são os endereços IP que recebem o tráfego de rede que vem do ponto final do IP do front-end. Substitua os endereços IP no exemplo pelos seus próprios pontos de extremidade do endereço IP do aplicativo.

   ```powershell
   $pool = New-AzApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 1.1.1.1, 2.2.2.2, 3.3.3.3
   ```

   > [!NOTE]
   > Um nome de domínio completamente qualificado (FQDN) também é um valor válido para utilizar em vez de um endereço IP para os servidores de back-end. Ativá-la utilizando o **- BackendFqdns** mudar. 


4. Configure a porta IP de front-end para o ponto de final IP público. Esta porta é a porta que os utilizadores finais que se ligam a.

   ```powershell
   $fp = New-AzApplicationGatewayFrontendPort -Name 'port01'  -Port 443
   ```

5. Configure o certificado para o gateway de aplicação. Este certificado é utilizado para desencriptar e reencriptar o tráfego no gateway de aplicação.

   ```powershell
   $passwd = ConvertTo-SecureString  <certificate file password> -AsPlainText -Force 
   $cert = New-AzApplicationGatewaySSLCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password $passwd 
   ```

   > [!NOTE]
   > Este exemplo configura o certificado utilizado para a ligação SSL. O certificado tem de estar no formato. pfx e a palavra-passe deve ter entre 4 e 12 carateres.

6. Crie o serviço de escuta HTTP para o gateway de aplicação. Atribua a configuração de IP Front-end, porta e certificado SSL para usar.

   ```powershell
   $listener = New-AzApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SSLCertificate $cert
   ```

7. Carregue o certificado a ser utilizado nos recursos do conjunto de back-end habilitado por SSL.

   > [!NOTE]
   > A sonda predefinida obtém a chave pública do *predefinição* enlace de SSL de endereço IP e compara o valor da chave público que recebe o valor de chave pública fornecer aqui o back-end. 
   > 
   > Se estiver a utilizar os cabeçalhos de anfitrião e indicação de nome de servidor (SNI) no back-end, a chave pública obtida pode não ser o site pretendido para os fluxos de tráfego. Se estiver em dúvida, visite https://127.0.0.1/ nos servidores de back-end para confirmar a qual o certificado é utilizado para o *predefinição* enlace SSL. Utilize a chave pública do que a solicitação nesta secção. Se estiver a utilizar cabeçalhos de host e SNI com ligações de HTTPS e não receber uma resposta e o certificado de uma solicitação de navegador manual para https://127.0.0.1/ nos servidores de back-end, tem de configurar um enlace de SSL padrão neles. Se fizer isso, as pesquisas falharem e o back-end não está na lista de permissões.

   ```powershell
   $authcert = New-AzApplicationGatewayAuthenticationCertificate -Name 'whitelistcert1' -CertificateFile C:\users\gwallace\Desktop\cert.cer
   ```

   > [!NOTE]
   > O certificado fornecido neste passo deve ser a chave pública do certificado. pfx presente no back-end. Exportar o certificado (não o certificado de raiz) instalado no servidor de back-end no formato de afirmação, evidência e Raciocinando (CER) e utilizá-lo neste passo. Este passo coloca o back-end com o gateway de aplicação.

   Se estiver a utilizar o SKU do Gateway de aplicação v2, em seguida, crie um certificado de raiz fidedigna em vez de um certificado de autenticação. Para obter mais informações, consulte [descrição geral do SSL ponto a ponto com o Gateway de aplicação](ssl-overview.md#end-to-end-ssl-with-the-v2-sku):

   ```powershell
   $trustedRootCert01 = New-AzApplicationGatewayTrustedRootCertificate -Name "test1" -CertificateFile  <path to root cert file>
   ```

8. Configure as definições de HTTP para o application gateway back-end. Atribua o certificado que carregou no passo anterior para as definições de HTTP.

   ```powershell
   $poolSetting = New-AzApplicationGatewayBackendHttpSettings -Name 'setting01' -Port 443 -Protocol Https -CookieBasedAffinity Enabled -AuthenticationCertificates $authcert
   ```

   Para o SKU do Gateway de aplicação v2, utilize o seguinte comando:

   ```powershell
   $poolSetting01 = New-AzApplicationGatewayBackendHttpSettings -Name “setting01” -Port 443 -Protocol Https -CookieBasedAffinity Disabled -TrustedRootCertificate $trustedRootCert01 -HostName "test1"
   ```

9. Crie uma regra de encaminhamento do Balanceador de carga que configura o comportamento do Balanceador de carga. Neste exemplo, é criada uma regra básica de round robin.

   ```powershell
   $rule = New-AzApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
   ```

10. Configure o tamanho da instância do gateway de aplicação. Os tamanhos disponíveis são **padrão\_pequenas**, **padrão\_média**, e **padrão\_grande**.  Para a capacidade, os valores disponíveis são **1** através de **10**.

    ```powershell
    $sku = New-AzApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
    ```

    > [!NOTE]
    > Uma contagem de instâncias de 1 pode ser escolhida para fins de teste. É importante saber que qualquer número de instâncias em duas instâncias não é abrangido pelo SLA e, portanto, não é recomendado. Gateways pequenos estão a ser utilizados para teste de desenvolvimento e não para fins de produção.

11. Configure a política SSL para ser utilizado no gateway de aplicação. Gateway de aplicação suporta a capacidade de definir uma versão mínima para versões de protocolo SSL.

    Os seguintes valores são uma lista das versões de protocolo que podem ser definidos:

    - **TLSV1_0**
    - **TLSV1_1**
    - **TLSV1_2**
    
    O exemplo seguinte define a versão do protocolo mínimo **TLSv1_2** e permite **TLS\_ECDHE\_ECDSA\_WITH\_AES\_128\_GCM\_SHA256**, **TLS\_ECDHE\_ECDSA\_WITH\_AES\_256\_GCM\_SHA384**, e **TLS\_RSA\_WITH\_AES\_128\_GCM\_SHA256** apenas.

    ```powershell
    $SSLPolicy = New-AzApplicationGatewaySSLPolicy -MinProtocolVersion TLSv1_2 -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256"
    ```

## <a name="create-the-application-gateway"></a>Criar o gateway de aplicação

Com todos os passos anteriores, crie o gateway de aplicação. A criação do gateway é um processo que demora muito tempo para ser executado.

```powershell
$appgw = New-AzApplicationGateway -Name appgateway -SSLCertificates $cert -ResourceGroupName "appgw-rg" -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SSLPolicy $SSLPolicy -AuthenticationCertificates $authcert -Verbose
```

## <a name="limit-ssl-protocol-versions-on-an-existing-application-gateway"></a>Limitar as versões de protocolo SSL num gateway de aplicação existente

Os passos anteriores demorou a criar uma aplicação com SSL de ponta a ponta e determinadas versões de protocolo SSL a desativar. O exemplo seguinte desativa determinadas políticas SSL num gateway de aplicação existente.

1. Obter o gateway de aplicação para atualizar.

   ```powershell
   $gw = Get-AzApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG
   ```

2. Defina uma política SSL. No exemplo a seguir **TLSv1.0** e **TLSv1.1** são desativadas e os conjuntos de cifras **TLS\_ECDHE\_ECDSA\_WITH\_ AES\_128\_GCM\_SHA256**, **TLS\_ECDHE\_ECDSA\_WITH\_AES\_256\_GCM\_SHA384**, e **TLS\_RSA\_WITH\_AES\_128\_GCM\_SHA256** são o único são permitidos.

   ```powershell
   Set-AzApplicationGatewaySSLPolicy -MinProtocolVersion TLSv1_2 -PolicyType Custom -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256" -ApplicationGateway $gw

   ```

3. Por fim, atualize o gateway. Neste último passo é uma tarefa de execução longa. Quando tiver terminado, o SSL ponto a ponto está configurada no gateway de aplicação.

   ```powershell
   $gw | Set-AzApplicationGateway
   ```

## <a name="get-an-application-gateway-dns-name"></a>Obter um nome de DNS de gateway de aplicação

Depois do gateway ser criado, a próxima etapa é configurar o front-end para a comunicação. Gateway de aplicação requer um nome DNS dinamicamente atribuído quando utilizar um IP público, que não é amigável. Para garantir que os utilizadores finais alcançam o gateway de aplicação, pode utilizar um registo CNAME para apontar para o ponto final público do gateway de aplicação. Para obter mais informações, consulte [configurar um nome de domínio personalizado no Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). 

Para configurar um alias, obter os detalhes de gateway de aplicação e o respetivo nome IP/DNS associado ao utilizar o **PublicIPAddress** elemento ligado ao gateway de aplicação. Utilize o nome DNS do gateway de aplicação para criar um registo CNAME que aponta as duas aplicações web para este nome DNS. Nós não recomendamos a utilização de registos, porque o VIP pode alterar no reinício do gateway de aplicação.

```powershell
Get-AzPublicIpAddress -ResourceGroupName appgw-RG -Name publicIP01
```

```
Name                     : publicIP01
ResourceGroupName        : appgw-RG
Location                 : westus
Id                       : /subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/publicIPAddresses/publicIP01
Etag                     : W/"00000d5b-54ed-4907-bae8-99bd5766d0e5"
ResourceGuid             : 00000000-0000-0000-0000-000000000000
ProvisioningState        : Succeeded
Tags                     : 
PublicIpAllocationMethod : Dynamic
IpAddress                : xx.xx.xxx.xx
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : {
                                "Id": "/subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/applicationGateways/appgwtest/frontendIP
                            Configurations/frontend1"
                            }
DnsSettings              : {
                                "Fqdn": "00000000-0000-xxxx-xxxx-xxxxxxxxxxxx.cloudapp.net"
                            }
```

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre a segurança das suas aplicações web com Firewall de aplicações Web através do Gateway de aplicação de proteção, consulte a [descrição geral de firewall de aplicações Web](application-gateway-webapplicationfirewall-overview.md).

[scenario]: ./media/application-gateway-end-to-end-SSL-powershell/scenario.png
