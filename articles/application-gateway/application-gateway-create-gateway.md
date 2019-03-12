---
title: Criar, iniciar ou eliminar um gateway de aplicação | Microsoft Docs
description: Esta página fornece instruções para criar, configurar, iniciar e eliminar um gateway de aplicação do Azure
documentationcenter: na
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
ms.assetid: 577054ca-8368-4fbf-8d53-a813f29dc3bc
ms.service: application-gateway
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 07/31/2017
ms.author: victorh
ms.openlocfilehash: 60ea054f6a3305e523e0843690bf58f7998cf0bb
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57783216"
---
# <a name="create-start-or-delete-an-application-gateway-with-powershell"></a>Criar, iniciar ou eliminar um gateway de aplicação com o PowerShell 

> [!div class="op_single_selector"]
> * [Portal do Azure](application-gateway-create-gateway-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-gateway-arm.md)
> * [Azure Classic PowerShell](application-gateway-create-gateway.md)
> * [Modelo do Azure Resource Manager](application-gateway-create-gateway-arm-template.md)
> * [CLI do Azure](application-gateway-create-gateway-cli.md)

O Application Gateway do Azure é um balanceador de carga de 7 camadas. Fornece ativação pós-falha, pedidos HTTP de encaminhamento de desempenho entre diversos servidores, quer estejam na nuvem ou no local. O Gateway de Aplicação proporciona muitas funcionalidades de Controlador de Entrega de Aplicação (ADC), incluindo o balanceamento de carga HTTP, afinidade por sessões com base em cookies, descarga do SSL (Secure Sockets Layer), sondas de estado de funcionamento personalizadas, suporte multilocal e muitas outras. Para encontrar uma lista completa das funcionalidades suportadas, visite [Descrição geral do Gateway de Aplicação](application-gateway-introduction.md)

Este artigo descreve os passos para criar, configurar, iniciar e eliminar um gateway de aplicação.

## <a name="before-you-begin"></a>Antes de começar

1. Instale a versão mais recente dos cmdlets Azure PowerShell com o Instalador de Plataforma Web. Pode transferir e instalar a versão mais recente a partir da secção **Windows PowerShell** da página [Transferências](https://azure.microsoft.com/downloads/).
2. Se tiver uma rede virtual existente, selecione uma subrede vazia existente ou crie uma nova subrede na sua rede virtual existente apenas para utilização pelo gateway de aplicação. Não pode implementar o gateway de aplicação numa rede virtual diferente da dos recursos que pretende implementar por trás do gateway de aplicação, a menos que se utilize o vnet peering. Para obter mais informações, visite [Vnet Peering](../virtual-network/virtual-network-peering-overview.md)
3. Verifique se a rede virtual funciona com uma sub-rede válida. Verifique se a sub-rede não está a ser utilizada por nenhuma máquina virtual ou implementação na nuvem. O gateway de aplicação tem de constar, por si só, numa sub-rede de rede virtual.
4. Os servidores que irá configurar para utilizar o gateway de aplicação devem existir. Caso contrário, os respetivos pontos finais terão de ser criados na rede virtual ou com um IP/VIP público atribuído.

## <a name="what-is-required-to-create-an-application-gateway"></a>O que é necessário para criar um gateway de aplicação?

Quando utiliza o comando `New-AzureApplicationGateway` para criar o gateway de aplicação, nenhuma configuração é definida nesta fase, pelo que o recurso recém-criado terá de ser configurado com o XML ou um objeto de configuração.

Os valores são:

* **Agrupamento de servidores de back-end:** A lista de endereços IP dos servidores de back-end. Os endereços IP listados devem pertencer à sub-rede da rede virtual ou ter um IP/VIP público.
* **Definições de agrupamento de servidores de back-end:** Cada conjunto tem definições como a porta, protocolo e a afinidade com base no cookie. Estas definições estão associadas a um conjunto e são aplicadas a todos os servidores do referido conjunto.
* **Porta de front-end:** Esta porta é a porta pública aberta no gateway de aplicação. O tráfego chega a esta porta, sendo posteriormente redirecionado para um dos servidores de back-end.
* **Serviço de escuta:** O serviço de escuta possui uma porta de front-end, um protocolo (Http ou Https; estes valores diferenciam maiúsculas de minúsculas) e o nome do certificado SSL (se configurar o SSL de descarga).
* **Regra:** A regra vincula o serviço de escuta e o conjunto de servidores de back-end e define para que conjunto de servidores de back-end o tráfego deve ser direcionado ao chegar a um determinado serviço de escuta.

## <a name="create-an-application-gateway"></a>Criar um gateway de aplicação

Para criar um gateway de aplicação:

1. Crie um recurso do gateway de aplicação.
2. Crie um ficheiro XML de configuração ou um objeto de configuração.
3. Confirme a configuração para o recurso de gateway de aplicação criado recentemente.

> [!NOTE]
> Se tiver de configurar uma sonda personalizada para o gateway de aplicação, veja [Create an application gateway with custom probes by using PowerShell (Criar um gateway de aplicação com sondas personalizadas com o PowerShell)](application-gateway-create-probe-classic-ps.md). Veja [monitorização das sondas personalizadas e do estado de funcionamento](application-gateway-probe-overview.md) para obter mais informações.

![Exemplo de cenário][scenario]

### <a name="create-an-application-gateway-resource"></a>Criar um recurso do gateway de aplicação

Para criar o gateway, utilize o cmdlet `New-AzureApplicationGateway`, substituindo os valores pelos seus próprios valores. Neste ponto, ainda não é contabilizada a faturação do gateway. A faturação passará a ser contabilizada num passo posterior, quando o gateway tiver sido iniciado com êxito.

O exemplo que se segue cria um gateway de aplicação com uma rede virtual denominada “testvnet1” e uma sub-rede denominada “subnet-1”:

```powershell
New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")
```

*Descrição*, *InstanceCount* e *GatewaySize* são parâmetros opcionais.

Para validar a criação do gateway, pode utilizar o cmdlet `Get-AzureApplicationGateway`.

```powershell
Get-AzureApplicationGateway AppGwTest
```

```
Name          : AppGwTest
Description   :
VnetName      : testvnet1
Subnets       : {Subnet-1}
InstanceCount : 2
GatewaySize   : Medium
State         : Stopped
VirtualIPs    : {}
DnsName       :
```

> [!NOTE]
> O valor predefinido para *InstanceCount* é 2, com um valor máximo de 10. O valor predefinido para *GatewaySize* é Médio. Pode escolher entre Pequeno, Médio e Grande.

*VirtualIPs* e *DnsName* são apresentados em branco, porque o gateway ainda não foi iniciado. Estes valores são criados assim que o gateway estiver em execução.

## <a name="configure-the-application-gateway"></a>Configurar o gateway de aplicação

Pode configurar o gateway de aplicação com XML ou um objeto de configuração.

### <a name="configure-the-application-gateway-by-using-xml"></a>Configurar o gateway de aplicação com XML

No exemplo que se segue, vai utilizar um ficheiro XML para configurar todas as definições do gateway de aplicação e consolidá-las no recurso do gateway de aplicação.  

#### <a name="step-1"></a>Passo 1

Copie o seguinte texto para o Bloco de Notas.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationGatewayConfiguration xmlns:i="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
    <FrontendPorts>
        <FrontendPort>
            <Name>(name-of-your-frontend-port)</Name>
            <Port>(port number)</Port>
        </FrontendPort>
    </FrontendPorts>
    <BackendAddressPools>
        <BackendAddressPool>
            <Name>(name-of-your-backend-pool)</Name>
            <IPAddresses>
                <IPAddress>(your-IP-address-for-backend-pool)</IPAddress>
                <IPAddress>(your-second-IP-address-for-backend-pool)</IPAddress>
            </IPAddresses>
        </BackendAddressPool>
    </BackendAddressPools>
    <BackendHttpSettingsList>
        <BackendHttpSettings>
            <Name>(backend-setting-name-to-configure-rule)</Name>
            <Port>80</Port>
            <Protocol>[Http|Https]</Protocol>
            <CookieBasedAffinity>Enabled</CookieBasedAffinity>
        </BackendHttpSettings>
    </BackendHttpSettingsList>
    <HttpListeners>
        <HttpListener>
            <Name>(name-of-the-listener)</Name>
            <FrontendPort>(name-of-your-frontend-port)</FrontendPort>
            <Protocol>[Http|Https]</Protocol>
        </HttpListener>
    </HttpListeners>
    <HttpLoadBalancingRules>
        <HttpLoadBalancingRule>
            <Name>(name-of-load-balancing-rule)</Name>
            <Type>basic</Type>
            <BackendHttpSettings>(backend-setting-name-to-configure-rule)</BackendHttpSettings>
            <Listener>(name-of-the-listener)</Listener>
            <BackendAddressPool>(name-of-your-backend-pool)</BackendAddressPool>
        </HttpLoadBalancingRule>
    </HttpLoadBalancingRules>
</ApplicationGatewayConfiguration>
```

Edite os valores entre parêntesis dos itens de configuração. Guarde o ficheiro com a extensão .xml.

> [!IMPORTANT]
> O item do protocolo Http ou Https é sensível às maiúsculas e minúsculas.

O exemplo que se segue mostra como utilizar um ficheiro de configuração para configurar o gateway de aplicação. O exemplo faz o balanceamento de carga do tráfego HTTP na porta pública 80 e envia o tráfego de rede para a porta de back-end 80 entre dois endereços IP.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationGatewayConfiguration xmlns:i="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
    <FrontendPorts>
        <FrontendPort>
            <Name>FrontendPort1</Name>
            <Port>80</Port>
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
            <Protocol>Http</Protocol>
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

#### <a name="step-2"></a>Passo 2

Em seguida, configure o gateway de aplicação. Utilize o cmdlet `Set-AzureApplicationGatewayConfig` com um ficheiro XML de configuração.

```powershell
Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile "D:\config.xml"
```

### <a name="configure-the-application-gateway-by-using-a-configuration-object"></a>Configurar o gateway de aplicação com um objeto de configuração

O exemplo que se segue mostra como configurar o gateway de aplicação com objetos de configuração. Todos os itens de configuração devem ser configurados individualmente e, em seguida, adicionados a um objeto de configuração do gateway de aplicação. Depois de criar o objeto de configuração, utilize o comando `Set-AzureApplicationGateway` para consolidar a configuração no recurso de gateway de aplicação criado anteriormente.

> [!NOTE]
> Antes de atribuir um valor a cada objeto de configuração, tem de indicar qual o tipo de objeto utilizado pelo PowerShell para o armazenamento. A primeira linha para criar os itens individuais define que `Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model(object name)` são utilizados.

#### <a name="step-1"></a>Passo 1

Crie todos os itens de configuração individuais.

Crie o IP de front-end, conforme mostrado no exemplo seguinte.

```powershell
$fip = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendIPConfiguration
$fip.Name = "fip1"
$fip.Type = "Private"
$fip.StaticIPAddress = "10.0.0.5"
```

Crie a porta de front-end, conforme mostrado no exemplo seguinte.

```powershell
$fep = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendPort
$fep.Name = "fep1"
$fep.Port = 80
```

Crie o conjunto de servidores de back-end.

Defina os endereços IP que são adicionados ao conjunto de servidores de back-end, conforme mostrado no exemplo seguinte.

```powershell
$servers = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendServerCollection
$servers.Add("10.0.0.1")
$servers.Add("10.0.0.2")
```

Utilize o objeto $server para adicionar os valores ao objeto do conjunto de back-end ($pool).

```powershell
$pool = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendAddressPool
$pool.BackendServers = $servers
$pool.Name = "pool1"
```

Crie a definição do conjunto de servidores de back-end.

```powershell
$setting = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendHttpSettings
$setting.Name = "setting1"
$setting.CookieBasedAffinity = "enabled"
$setting.Port = 80
$setting.Protocol = "http"
```

Crie o serviço de escuta.

```powershell
$listener = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpListener
$listener.Name = "listener1"
$listener.FrontendPort = "fep1"
$listener.FrontendIP = "fip1"
$listener.Protocol = "http"
$listener.SslCert = ""
```

Crie a regra.

```powershell
$rule = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpLoadBalancingRule
$rule.Name = "rule1"
$rule.Type = "basic"
$rule.BackendHttpSettings = "setting1"
$rule.Listener = "listener1"
$rule.BackendAddressPool = "pool1"
```

#### <a name="step-2"></a>Passo 2

Atribua todos os itens individuais de configuração a um objeto de configuração do gateway de aplicação ($appgwconfig).

Adicione o IP de front-end à configuração.

```powershell
$appgwconfig = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.ApplicationGatewayConfiguration
$appgwconfig.FrontendIPConfigurations = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendIPConfiguration]"
$appgwconfig.FrontendIPConfigurations.Add($fip)
```

Adicione a porta de front-end à configuração.

```powershell
$appgwconfig.FrontendPorts = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendPort]"
$appgwconfig.FrontendPorts.Add($fep)
```
Adicione o conjunto de servidores de front-end à configuração.

```powershell
$appgwconfig.BackendAddressPools = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendAddressPool]"
$appgwconfig.BackendAddressPools.Add($pool)
```

Adicione a definição do conjunto de front-end à configuração.

```powershell
$appgwconfig.BackendHttpSettingsList = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendHttpSettings]"
$appgwconfig.BackendHttpSettingsList.Add($setting)
```

Adicione o serviço de escuta à configuração.

```powershell
$appgwconfig.HttpListeners = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpListener]"
$appgwconfig.HttpListeners.Add($listener)
```

Adicione a regra à configuração.

```powershell
$appgwconfig.HttpLoadBalancingRules = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpLoadBalancingRule]"
$appgwconfig.HttpLoadBalancingRules.Add($rule)
```

### <a name="step-3"></a>Passo 3
Consolide o objeto de configuração para o recurso de gateway de aplicação com `Set-AzureApplicationGatewayConfig`.

```powershell
Set-AzureApplicationGatewayConfig -Name AppGwTest -Config $appgwconfig
```

## <a name="start-the-gateway"></a>Iniciar o gateway

Depois de configurar o gateway, utilize o cmdlet `Start-AzureApplicationGateway` para iniciar o gateway. A faturação de um gateway de aplicação é contabilizada depois de o gateway ser iniciado com êxito.

> [!NOTE]
> O cmdlet `Start-AzureApplicationGateway` poderá demorar até 15-20 minutos a concluir.

```powershell
Start-AzureApplicationGateway AppGwTest
```

## <a name="verify-the-gateway-status"></a>Verificar o estado do gateway

Utilize o cmdlet `Get-AzureApplicationGateway` para verificar o estado do gateway. Se o comando `Start-AzureApplicationGateway` tiver sido concluído com êxito no passo anterior, o *Estado* deve ser Em Execução e os valores *Vip* e *DnsName* deverão ter entradas válidas.

O exemplo que se segue mostra um gateway de aplicação ativo, em execução e pronto a passar o tráfego para `http://<generated-dns-name>.cloudapp.net`.

```powershell
Get-AzureApplicationGateway AppGwTest
```

```
VERBOSE: 8:09:28 PM - Begin Operation: Get-AzureApplicationGateway
VERBOSE: 8:09:30 PM - Completed Operation: Get-AzureApplicationGateway
Name          : AppGwTest
Description   :
VnetName      : testvnet1
Subnets       : {Subnet-1}
InstanceCount : 2
GatewaySize   : Medium
State         : Running
Vip           : 138.91.170.26
DnsName       : appgw-1b8402e8-3e0d-428d-b661-289c16c82101.cloudapp.net
```

## <a name="delete-the-application-gateway"></a>Eliminar o gateway de aplicação

Para eliminar o gateway de aplicação:

1. Utilize o cmdlet `Stop-AzureApplicationGateway` para parar o gateway.
2. Utilize o cmdlet `Remove-AzureApplicationGateway` para remover o gateway.
3. Verifique se o gateway foi removido com o cmdlet `Get-AzureApplicationGateway`.

O exemplo que se segue mostra o cmdlet `Stop-AzureApplicationGateway` na primeira linha, seguido pela saída.

```powershell
Stop-AzureApplicationGateway AppGwTest
```

```
VERBOSE: 9:49:34 PM - Begin Operation: Stop-AzureApplicationGateway
VERBOSE: 10:10:06 PM - Completed Operation: Stop-AzureApplicationGateway
Name       HTTP Status Code     Operation ID                             Error
----       ----------------     ------------                             ----
Successful OK                   ce6c6c95-77b4-2118-9d65-e29defadffb8
```

Assim que o gateway de aplicação estiver no estado parado, utilize o cmdlet `Remove-AzureApplicationGateway` para remover o serviço.

```powershell
Remove-AzureApplicationGateway AppGwTest
```

```
VERBOSE: 10:49:34 PM - Begin Operation: Remove-AzureApplicationGateway
VERBOSE: 10:50:36 PM - Completed Operation: Remove-AzureApplicationGateway
Name       HTTP Status Code     Operation ID                             Error
----       ----------------     ------------                             ----
Successful OK                   055f3a96-8681-2094-a304-8d9a11ad8301
```

Para verificar se o serviço foi removido, pode utilizar o cmdlet `Get-AzureApplicationGateway`. Este passo não é necessário.

```powershell
Get-AzureApplicationGateway AppGwTest
```

```
VERBOSE: 10:52:46 PM - Begin Operation: Get-AzureApplicationGateway

Get-AzureApplicationGateway : ResourceNotFound: The gateway does not exist.
.....
```

## <a name="next-steps"></a>Passos Seguintes

Se pretender configurar a descarga de SSL, veja [Configure an application gateway for SSL offload (Configurar um gateway de aplicação para a descarga de SSL)](application-gateway-ssl.md).

Se pretender configurar um gateway de aplicação para utilizar com um balanceador de carga interno, veja [Create an application gateway with an internal load balancer (ILB) (Criar um gateway de aplicação com um balanceador de carga interno (ILB))](application-gateway-ilb.md).

Se pretender obter mais informações sobre as opções de balanceamento de carga em geral, veja:

* [Azure Load Balancer](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Gestor de Tráfego do Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)

[scenario]: ./media/application-gateway-create-gateway/scenario.png
