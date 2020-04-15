---
title: Descarregamento tLS usando PowerShell - Gateway de aplicação Azure
description: Este artigo fornece instruções para criar um portal de aplicação com a descarga tLS utilizando o modelo de implementação clássico azure
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/13/2019
ms.author: victorh
ms.openlocfilehash: 2ead16b61784b8073d50b7e0e6079805a1e48e9b
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81312329"
---
# <a name="configure-an-application-gateway-for-tls-offload-by-using-the-classic-deployment-model"></a>Configure uma porta de aplicação para descarregar TLS utilizando o modelo de implementação clássico

> [!div class="op_single_selector"]
> * [Portal do Azure](application-gateway-ssl-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-ssl-arm.md)
> * [PowerShell clássico azure](application-gateway-ssl.md)
> * [CLI do Azure](application-gateway-ssl-cli.md)

O Portal de Aplicações Azure pode ser configurado para acabar com a Segurança da Camada de Transporte (TLS), anteriormente conhecida como Secure Sockets Layer (SSL), sessão na porta de entrada para evitar tarefas dispendiosas de desencriptação do TLS que acontecem na quinta web. A descarga de TLS também simplifica a configuração e gestão do servidor frontal da aplicação web.

## <a name="before-you-begin"></a>Antes de começar

1. Instale a versão mais recente dos cmdlets Azure PowerShell com o Instalador de Plataforma Web. Pode transferir e instalar a versão mais recente a partir da secção **Windows PowerShell** da página [Transferências](https://azure.microsoft.com/downloads/).
2. Verifique se a rede virtual funciona com uma sub-rede válida. Verifique se a sub-rede não está a ser utilizada por nenhuma máquina virtual ou implementação na nuvem. O gateway de aplicação tem de constar, por si só, numa sub-rede de rede virtual.
3. Os servidores que configurapara utilizar o gateway da aplicação devem existir ou ter os seus pontos finais criados quer na rede virtual, quer com um endereço IP público ou endereço IP virtual (VIP) atribuído.

Para configurar a descarga de TLS num gateway de aplicação, complete os seguintes passos na ordem listada:

1. [Criar um portal de aplicação](#create-an-application-gateway)
2. [Carregar certificados TLS/SSL](#upload-tlsssl-certificates)
3. [Configurar o gateway](#configure-the-gateway)
4. [Definir a configuração do gateway](#set-the-gateway-configuration)
5. [Iniciar o gateway](#start-the-gateway)
6. [Verificar o estado do gateway](#verify-the-gateway-status)

## <a name="create-an-application-gateway"></a>Criar um gateway de aplicação

Para criar o portal, introduza o `New-AzureApplicationGateway` cmdlet, substituindo os valores por si próprio. Neste ponto, ainda não é contabilizada a faturação do gateway. A faturação passará a ser contabilizada num passo posterior, quando o gateway tiver sido iniciado com êxito.

```powershell
New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")
```

Para validar que o portal foi `Get-AzureApplicationGateway` criado, pode entrar no cmdlet.

Na amostra, **Descrição,** **Contagem de Casos**e **GatewaySize** são parâmetros opcionais. O valor predefinido para **O Caso Count** é **2**, com um valor máximo de **10**. O valor predefinido para **GatewaySize** é **Médio**. Pequenos e Grandes são outros valores disponíveis. **VirtualIPs** e **DnsName** são mostrados em branco, porque o gateway ainda não começou. Estes valores são criados após a entrada em funcionamento.

```powershell
Get-AzureApplicationGateway AppGwTest
```

## <a name="upload-tlsssl-certificates"></a>Carregar certificados TLS/SSL

Introduza `Add-AzureApplicationGatewaySslCertificate` para carregar o certificado de servidor em formato PFX para o gateway da aplicação. O nome do certificado é um nome escolhido pelo utilizador e deve ser único dentro do gateway da aplicação. Este certificado é referido por este nome em todas as operações de gestão de certificados no gateway da aplicação.

A amostra que se segue mostra o cmdlet. Substitua os valores da amostra com os seus.

```powershell
Add-AzureApplicationGatewaySslCertificate  -Name AppGwTest -CertificateName GWCert -Password <password> -CertificateFile <full path to pfx file>
```

Em seguida, valide o carregamento do certificado. Introduza `Get-AzureApplicationGatewayCertificate` o cmdlet.

A amostra seguinte mostra o cmdlet na primeira linha, seguido da saída:

```powershell
Get-AzureApplicationGatewaySslCertificate AppGwTest
```

```
VERBOSE: 5:07:54 PM - Begin Operation: Get-AzureApplicationGatewaySslCertificate
VERBOSE: 5:07:55 PM - Completed Operation: Get-AzureApplicationGatewaySslCertificate
Name           : SslCert
SubjectName    : CN=gwcert.app.test.contoso.com
Thumbprint     : AF5ADD77E160A01A6......EE48D1A
ThumbprintAlgo : sha1RSA
State..........: Provisioned
```

> [!NOTE]
> A palavra-passe do certificado deve ser entre 4 a 12 caracteres compostos por letras ou números. Personagens especiais não são aceites.

## <a name="configure-the-gateway"></a>Configurar o gateway

Uma configuração de gateway de aplicação consiste em múltiplos valores. Os valores podem ser ligados para construir a configuração.

Os valores são:

* **Piscina de servidorde back-end**: A lista de endereços IP dos servidores de back-end. Os endereços IP listados devem pertencer à subnet da rede virtual ou devem ser um endereço IP ou VIP público.
* **Definições de piscina de servidor de back-end**: Cada piscina tem configurações como porta, protocolo e afinidade baseada em cookies. Estas definições estão associadas a um conjunto e são aplicadas a todos os servidores do referido conjunto.
* **Porta frontal**: Este porto é o porto público que é aberto na porta de aplicação. O tráfego chega a esta porta, sendo posteriormente redirecionado para um dos servidores de back-end.
* **Ouvinte**: O ouvinte tem uma porta frontal, um protocolo (Http ou Https; estes valores são sensíveis a casos) e o nome do certificado TLS/SSL (se configurar uma descarga TLS).
* **Regra**: A regra liga o ouvinte e o conjunto de servidores de back-end e define qual o conjunto de servidores de back-end para direcionar o tráfego para quando atinge um ouvinte particular. Atualmente, apenas é suportada a regra *básica*. A regra *básica* refere-se à distribuição de carga round robin.

**Notas de configuração adicionais**

Para a configuração dos certificados TLS/SSL, o protocolo em **HttpListener** deve mudar para **Https** (sensível a casos). Adicione o elemento **SslCert** ao **HttpListener** com o valor definido para o mesmo nome utilizado na secção [de certificados Upload TLS/SSL.](#upload-tlsssl-certificates) A porta frontal deve ser atualizada para **443**.

Para ativar a **afinidade baseada em cookies**: Pode configurar um portal de aplicação para garantir que um pedido de uma sessão de cliente é sempre direcionado para o mesmo VM na quinta web. Para isso, insira um cookie de sessão que permita que o portal direcione o tráfego de forma adequada. Para ativar a afinidade com base em cookies, defina **CookieBasedAffinity** como **Ativado** no elemento **BackendHttpSettings**.

Pode construir a sua configuração através da criação de um objeto de configuração ou utilizando um ficheiro XML de configuração.
Para construir a sua configuração utilizando um ficheiro XML de configuração, introduza a seguinte amostra:


```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationGatewayConfiguration xmlns:i="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
    <FrontendIPConfigurations />
    <FrontendPorts>
        <FrontendPort>
            <Name>FrontendPort1</Name>
            <Port>443</Port>
        </FrontendPort>
    </FrontendPorts>
    <BackendAddressPools>
        <BackendAddressPool>
            <Name>BackendPool1</Name>
            <IPAddresses>
                <IPAddress>10.0.0.1</IPAddress>
                <IPAddress>10.0.0.2</IPAddress>
            </IPAddresses>
        </BackendAddressPool>
    </BackendAddressPools>
    <BackendHttpSettingsList>
        <BackendHttpSettings>
            <Name>BackendSetting1</Name>
            <Port>80</Port>
            <Protocol>Http</Protocol>
            <CookieBasedAffinity>Enabled</CookieBasedAffinity>
        </BackendHttpSettings>
    </BackendHttpSettingsList>
    <HttpListeners>
        <HttpListener>
            <Name>HTTPListener1</Name>
            <FrontendPort>FrontendPort1</FrontendPort>
            <Protocol>Https</Protocol>
            <SslCert>GWCert</SslCert>
        </HttpListener>
    </HttpListeners>
    <HttpLoadBalancingRules>
        <HttpLoadBalancingRule>
            <Name>HttpLBRule1</Name>
            <Type>basic</Type>
            <BackendHttpSettings>BackendSetting1</BackendHttpSettings>
            <Listener>HTTPListener1</Listener>
            <BackendAddressPool>BackendPool1</BackendAddressPool>
        </HttpLoadBalancingRule>
    </HttpLoadBalancingRules>
</ApplicationGatewayConfiguration>
```

## <a name="set-the-gateway-configuration"></a>Definir a configuração do gateway

Em seguida, configure o gateway de aplicação. Pode introduzir `Set-AzureApplicationGatewayConfig` o cmdlet com um objeto de configuração ou um ficheiro XML de configuração.

```powershell
Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile D:\config.xml
```

## <a name="start-the-gateway"></a>Iniciar o gateway

Depois de configurar o portal, introduza o `Start-AzureApplicationGateway` cmdlet para iniciar o portal. A faturação de um gateway de aplicação é contabilizada depois de o gateway ser iniciado com êxito.

> [!NOTE]
> O `Start-AzureApplicationGateway` cmdlet pode levar 15-20 minutos para terminar.
>
>

```powershell
Start-AzureApplicationGateway AppGwTest
```

## <a name="verify-the-gateway-status"></a>Verificar o estado do gateway

Introduza `Get-AzureApplicationGateway` o cmdlet para verificar o estado do portal. Se `Start-AzureApplicationGateway` tiver sido bem sucedido no passo anterior, o **Estado** deve estar **em execução**, e os **VirtualIPs** e **DnsName** devem ter entradas válidas.

Esta amostra mostra um portal de aplicação que está em funcionamento, em execução e pronto para tomar tráfego:

```powershell
Get-AzureApplicationGateway AppGwTest
```

```
Name          : AppGwTest2
Description   :
VnetName      : testvnet1
Subnets       : {Subnet-1}
InstanceCount : 2
GatewaySize   : Medium
State         : Running
VirtualIPs    : {23.96.22.241}
DnsName       : appgw-4c960426-d1e6-4aae-8670-81fd7a519a43.cloudapp.net
```

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre as opções de equilíbrio de carga em geral, consulte:

* [Equilibrador de carga Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Gestor de Tráfego Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)
