---
title: Configurar o PowerShell clássico - Gateway de aplicação do Azure - a descarga de SSL | Documentos da Microsoft
description: Este artigo fornece instruções para criar um gateway de aplicação com SSL offload, utilizando o modelo de implementação clássica do Azure
documentationcenter: na
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
ms.assetid: 63f28d96-9c47-410e-97dd-f5ca1ad1b8a4
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: victorh
ms.openlocfilehash: 89a88d79b6b93a233dbd4f335d0eb449e49d5289
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "62122205"
---
# <a name="configure-an-application-gateway-for-ssl-offload-by-using-the-classic-deployment-model"></a>Configurar um gateway de aplicação para descarga de SSL com o modelo de implementação clássica

> [!div class="op_single_selector"]
> * [Portal do Azure](application-gateway-ssl-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-ssl-arm.md)
> * [O Azure PowerShell clássico](application-gateway-ssl.md)
> * [CLI do Azure](application-gateway-ssl-cli.md)

Pode configurar o Azure Application Gateway para terminar a sessão SSL (Secure Sockets Layer) no gateway para evitar tarefas dispendiosas de desencriptação de SSL que ocorrem no farm Web. A descarga de SSL simplifica ainda a configuração do servidor de front-end e a gestão da aplicação Web.

## <a name="before-you-begin"></a>Antes de começar

1. Instale a versão mais recente dos cmdlets Azure PowerShell com o Instalador de Plataforma Web. Pode transferir e instalar a versão mais recente a partir da secção **Windows PowerShell** da página [Transferências](https://azure.microsoft.com/downloads/).
2. Verifique se a rede virtual funciona com uma sub-rede válida. Verifique se a sub-rede não está a ser utilizada por nenhuma máquina virtual ou implementação na nuvem. O gateway de aplicação tem de constar, por si só, numa sub-rede de rede virtual.
3. Os servidores que configurar para utilizar o gateway de aplicação tem de existir ou ter os respetivos pontos finais que são criados na rede virtual ou com um endereço IP público ou endereço IP virtual (VIP) atribuído.

Para configurar a descarga de SSL num gateway de aplicação, execute os seguintes passos pela ordem listada:

1. [Criar um gateway de aplicação](#create-an-application-gateway)
2. [Carregar certificados SSL](#upload-ssl-certificates)
3. [Configurar o gateway](#configure-the-gateway)
4. [Definir a configuração do gateway](#set-the-gateway-configuration)
5. [Iniciar o gateway](#start-the-gateway)
6. [Verificar o estado do gateway](#verify-the-gateway-status)

## <a name="create-an-application-gateway"></a>Criar um gateway de aplicação

Para criar o gateway, introduza o `New-AzureApplicationGateway` cmdlet, substituindo os valores pelos seus próprios. Neste ponto, ainda não é contabilizada a faturação do gateway. A faturação passará a ser contabilizada num passo posterior, quando o gateway tiver sido iniciado com êxito.

```powershell
New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")
```

Para validar a criação do gateway, pode introduzir o `Get-AzureApplicationGateway` cmdlet.

No exemplo, **Descrição**, **InstanceCount**, e **GatewaySize** são parâmetros opcionais. O valor predefinido para **InstanceCount** é **2**, com um valor máximo de **10**. O valor predefinido para **GatewaySize** é **médio**. Pequenos e grandes são outros valores disponíveis. **VirtualIPs** e **DnsName** são apresentados em branco, porque o gateway ainda não foi iniciado. Estes valores são criados após o gateway está em execução.

```powershell
Get-AzureApplicationGateway AppGwTest
```

## <a name="upload-ssl-certificates"></a>Carregar certificados SSL

Introduza `Add-AzureApplicationGatewaySslCertificate` para carregar o certificado de servidor no formato PFX para o gateway de aplicação. O nome do certificado é um nome de utilizador-escolhido e tem de ser exclusivo dentro do gateway de aplicação. Este certificado é referido com este nome em todas as operações de gestão de certificado no gateway de aplicação.

O exemplo a seguir mostra o cmdlet. Substitua os valores do exemplo pelos seus próprios.

```powershell
Add-AzureApplicationGatewaySslCertificate  -Name AppGwTest -CertificateName GWCert -Password <password> -CertificateFile <full path to pfx file>
```

Em seguida, valide o carregamento do certificado. Introduza o `Get-AzureApplicationGatewayCertificate` cmdlet.

O exemplo a seguir mostra o cmdlet na primeira linha, seguida pela saída:

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
> A palavra-passe do certificado tem de ter entre 4 e 12 carateres constituídos por letras ou números. Não são aceites carateres especiais.

## <a name="configure-the-gateway"></a>Configurar o gateway

Uma configuração de gateway de aplicação é composta por vários valores. Os valores que podem ser vinculados em conjunto para construir a configuração.

Os valores são:

* **Agrupamento de servidores de back-end**: A lista de endereços IP dos servidores de back-end. Os endereços IP listados devem pertencer à sub-rede da rede virtual ou devem ser um endereço IP ou VIP público.
* **As definições do agrupamento de servidores de back-end**: Cada conjunto tem definições como a porta, protocolo e a afinidade com base no cookie. Estas definições estão associadas a um conjunto e são aplicadas a todos os servidores do referido conjunto.
* **Porta de front-end**: Esta porta é a porta pública aberta no gateway de aplicação. O tráfego chega a esta porta, sendo posteriormente redirecionado para um dos servidores de back-end.
* **Serviço de escuta**: O serviço de escuta possui uma porta de front-end, um protocolo (Http ou Https; estes valores diferenciam maiúsculas de minúsculas) e o nome do certificado SSL (se configurar a descarga de SSL).
* **Regra**: A regra vincula o serviço de escuta e o conjunto de servidores de back-end e define para que conjunto de servidores de back-end para direcionar o tráfego para quando chegar a um determinado serviço de escuta. Atualmente, apenas é suportada a regra *básica*. A regra *básica* refere-se à distribuição de carga round robin.

**Notas de configuração adicionais**

Para a configuração de certificados SSL, o protocolo em **HttpListener** deverá passar para **Https** (sensível às maiúsculas e minúsculas). Adicionar a **SslCert** elemento **HttpListener** com o valor definido para o mesmo nome utilizado no [certificados SSL carregar](#upload-ssl-certificates) secção. A porta de front-end deve ser atualizada para **443**.

**Para ativar a afinidade com base no cookie**: Pode configurar um gateway de aplicação para se certificar de que um pedido de uma sessão de cliente é sempre direcionado para a mesma VM no web farm. Para tal, inserir um cookie de sessão que permite ao gateway direcionar adequadamente o tráfego. Para ativar a afinidade com base em cookies, defina **CookieBasedAffinity** como **Ativado** no elemento **BackendHttpSettings**.

Pode construir sua configuração através da criação de um objeto de configuração ou utilizando um ficheiro XML de configuração.
Para construir sua configuração utilizando um ficheiro XML de configuração, introduza o seguinte exemplo:


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

Em seguida, configure o gateway de aplicação. Pode introduzir o `Set-AzureApplicationGatewayConfig` cmdlet com um objeto de configuração ou um ficheiro XML de configuração.

```powershell
Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile D:\config.xml
```

## <a name="start-the-gateway"></a>Iniciar o gateway

Depois de configurar o gateway, introduza o `Start-AzureApplicationGateway` cmdlet para iniciar o gateway. A faturação de um gateway de aplicação é contabilizada depois de o gateway ser iniciado com êxito.

> [!NOTE]
> O `Start-AzureApplicationGateway` cmdlet pode demorar 15 a 20 minutos a concluir.
>
>

```powershell
Start-AzureApplicationGateway AppGwTest
```

## <a name="verify-the-gateway-status"></a>Verificar o estado do gateway

Introduza o `Get-AzureApplicationGateway` cmdlet para verificar o estado do gateway. Se `Start-AzureApplicationGateway` foi concluída com êxito no passo anterior, o **estado** deve ser **executar**e a **VirtualIPs** e **DnsName** deve ter entradas válidas.

Este exemplo mostra um gateway de aplicação que estiver em execução, em execução e pronto a passar o tráfego:

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

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre balanceamento de carga em geral, consulte as opções:

* [Azure Load Balancer](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Gestor de Tráfego do Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)
