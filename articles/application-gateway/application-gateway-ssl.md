---
title: Descarregamento de SSL usando o PowerShell-Aplicativo Azure gateway
description: Este artigo fornece instruções para criar um gateway de aplicativo com descarregamento SSL usando o modelo de implantação clássico do Azure
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/13/2019
ms.author: victorh
ms.openlocfilehash: c456a0856adb0d36349b5f96ba0ab8bab3eec5c9
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74047910"
---
# <a name="configure-an-application-gateway-for-ssl-offload-by-using-the-classic-deployment-model"></a>Configurar um gateway de aplicativo para descarregamento SSL usando o modelo de implantação clássico

> [!div class="op_single_selector"]
> * [Portal do Azure](application-gateway-ssl-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-ssl-arm.md)
> * [PowerShell clássico do Azure](application-gateway-ssl.md)
> * [CLI do Azure](application-gateway-ssl-cli.md)

Pode configurar o Azure Application Gateway para terminar a sessão SSL (Secure Sockets Layer) no gateway para evitar tarefas dispendiosas de desencriptação de SSL que ocorrem no farm Web. A descarga de SSL simplifica ainda a configuração do servidor de front-end e a gestão da aplicação Web.

## <a name="before-you-begin"></a>Antes de começar

1. Instale a versão mais recente dos cmdlets Azure PowerShell com o Instalador de Plataforma Web. Pode transferir e instalar a versão mais recente a partir da secção **Windows PowerShell** da página [Transferências](https://azure.microsoft.com/downloads/).
2. Verifique se a rede virtual funciona com uma sub-rede válida. Verifique se a sub-rede não está a ser utilizada por nenhuma máquina virtual ou implementação na nuvem. O gateway de aplicação tem de constar, por si só, numa sub-rede de rede virtual.
3. Os servidores que você configura para usar o gateway de aplicativo devem existir ou ter seus pontos de extremidade que são criados na rede virtual ou com um endereço IP público ou VIP (endereço IP virtual) atribuído.

Para configurar o descarregamento SSL em um gateway de aplicativo, conclua as seguintes etapas na ordem listada:

1. [Criar um gateway de aplicativo](#create-an-application-gateway)
2. [Carregar certificados SSL](#upload-ssl-certificates)
3. [Configurar o gateway](#configure-the-gateway)
4. [Definir a configuração do gateway](#set-the-gateway-configuration)
5. [Iniciar o gateway](#start-the-gateway)
6. [Verificar o status do gateway](#verify-the-gateway-status)

## <a name="create-an-application-gateway"></a>Para criar um gateway de aplicação

Para criar o gateway, insira o cmdlet `New-AzureApplicationGateway`, substituindo os valores pelos seus próprios. Neste ponto, ainda não é contabilizada a faturação do gateway. A faturação passará a ser contabilizada num passo posterior, quando o gateway tiver sido iniciado com êxito.

```powershell
New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")
```

Para validar que o gateway foi criado, você pode inserir o cmdlet `Get-AzureApplicationGateway`.

No exemplo, **Description**, **InstanceCount**e **GatewaySize** são parâmetros opcionais. O valor padrão para **InstanceCount** é **2**, com um valor máximo de **10**. O valor padrão para **GatewaySize** é **médio**. Pequeno e grande são outros valores disponíveis. **VirtualIPs** e **DnsName** são mostrados como em branco, pois o gateway ainda não foi iniciado. Esses valores são criados depois que o gateway está em estado de execução.

```powershell
Get-AzureApplicationGateway AppGwTest
```

## <a name="upload-ssl-certificates"></a>Carregar certificados SSL

Insira `Add-AzureApplicationGatewaySslCertificate` para carregar o certificado do servidor no formato PFX no gateway de aplicativo. O nome do certificado é um nome escolhido pelo usuário e deve ser exclusivo no gateway de aplicativo. Esse certificado é referenciado por esse nome em todas as operações de gerenciamento de certificado no gateway de aplicativo.

O exemplo a seguir mostra o cmdlet. Substitua os valores no exemplo pelo seu próprio.

```powershell
Add-AzureApplicationGatewaySslCertificate  -Name AppGwTest -CertificateName GWCert -Password <password> -CertificateFile <full path to pfx file>
```

Em seguida, valide o upload do certificado. Insira o cmdlet `Get-AzureApplicationGatewayCertificate`.

O exemplo a seguir mostra o cmdlet na primeira linha, seguido pela saída:

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
> A senha do certificado deve ter entre 4 e 12 caracteres compostos por letras ou números. Caracteres especiais não são aceitos.

## <a name="configure-the-gateway"></a>Configurar o gateway

Uma configuração de gateway de aplicativo consiste em vários valores. Os valores podem ser vinculados em conjunto para construir a configuração.

Os valores são:

* **Pool de servidores back-end**: a lista de endereços IP dos servidores back-end. Os endereços IP listados devem pertencer à sub-rede da rede virtual ou devem ser um endereço IP público ou VIP.
* **Configurações de pool de servidores back-end**: cada pool tem configurações como porta, protocolo e afinidade baseada em cookie. Estas definições estão associadas a um conjunto e são aplicadas a todos os servidores do referido conjunto.
* **Porta de front-end**: essa porta é a porta pública que é aberta no gateway de aplicativo. O tráfego chega a esta porta, sendo posteriormente redirecionado para um dos servidores de back-end.
* **Ouvinte**: o ouvinte tem uma porta de front-end, um protocolo (http ou HTTPS; esses valores diferenciam maiúsculas de minúsculas) e o nome do certificado SSL (se estiver configurando um descarregamento SSL).
* **Regra**: a regra associa o ouvinte e o pool de servidores back-end e define a qual pool de servidores back-end o tráfego será direcionado quando ele atingir um ouvinte específico. Atualmente, apenas é suportada a regra *básica*. A regra *básica* refere-se à distribuição de carga round robin.

**Notas de configuração adicionais**

Para a configuração de certificados SSL, o protocolo em **HttpListener** deverá passar para **Https** (sensível às maiúsculas e minúsculas). Adicione o elemento **SslCert** a **HttpListener** com o valor definido com o mesmo nome usado na seção [carregar certificados SSL](#upload-ssl-certificates) . A porta de front-end deve ser atualizada para **443**.

**Para habilitar a afinidade baseada em cookie**: você pode configurar um gateway de aplicativo para garantir que uma solicitação de uma sessão de cliente sempre seja direcionada para a mesma VM no Web farm. Para fazer isso, insira um cookie de sessão que permita ao gateway direcionar o tráfego adequadamente. Para ativar a afinidade com base em cookies, defina **CookieBasedAffinity** como **Ativado** no elemento **BackendHttpSettings**.

Você pode construir sua configuração criando um objeto de configuração ou usando um arquivo XML de configuração.
Para construir sua configuração usando um arquivo XML de configuração, insira o seguinte exemplo:


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

Em seguida, configure o gateway de aplicação. Você pode inserir o cmdlet `Set-AzureApplicationGatewayConfig` com um objeto de configuração ou um arquivo XML de configuração.

```powershell
Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile D:\config.xml
```

## <a name="start-the-gateway"></a>Iniciar o gateway

Depois que o gateway tiver sido configurado, insira o cmdlet `Start-AzureApplicationGateway` para iniciar o gateway. A faturação de um gateway de aplicação é contabilizada depois de o gateway ser iniciado com êxito.

> [!NOTE]
> O cmdlet `Start-AzureApplicationGateway` pode levar de 15-20 minutos para ser concluído.
>
>

```powershell
Start-AzureApplicationGateway AppGwTest
```

## <a name="verify-the-gateway-status"></a>Verificar o estado do gateway

Insira o cmdlet `Get-AzureApplicationGateway` para verificar o status do gateway. Se `Start-AzureApplicationGateway` teve êxito na etapa anterior, o **estado** deve estar **em execução**e **VirtualIPs** e **DnsName** devem ter entradas válidas.

Este exemplo mostra um gateway de aplicativo que está ativo, em execução e pronto para assumir o tráfego:

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

Para obter mais informações sobre opções de balanceamento de carga em geral, consulte:

* [Azure Load Balancer](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Gestor de Tráfego do Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)
