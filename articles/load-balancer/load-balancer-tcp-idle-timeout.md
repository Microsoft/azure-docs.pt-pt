---
title: Configurar o tempo limite de inatividade de TCP de Balanceador de carga no Azure
titlesuffix: Azure Load Balancer
description: Configurar o tempo limite de inatividade de TCP de Balanceador de carga
services: load-balancer
documentationcenter: na
author: kumudd
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 0c57eec4d739da13d98099a6b2f01fbf0ad0051c
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57857543"
---
# <a name="configure-tcp-idle-timeout-settings-for-azure-load-balancer"></a>Configurar as definições de tempo limite de inatividade de TCP para o Balanceador de carga do Azure

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Na configuração predefinida, o Balanceador de carga do Azure tem uma definição de tempo limite de inatividade de 4 minutos. Se um período de inatividade é maior do que o valor de tempo limite, não há nenhuma garantia de que a sessão TCP ou HTTP é mantida entre o cliente e o seu serviço cloud.

Quando a conexão é fechada, a aplicação cliente pode receber a seguinte mensagem de erro: "A ligação subjacente foi fechada: Uma ligação que foi poderá demorar a ser mantido ativo foi fechada pelo servidor."

Uma prática comum é usar uma ligação keep-alive de TCP. Esta prática mantém a ligação ativa durante um período mais longo. Para obter mais informações, consulte estes [exemplos de .NET](https://msdn.microsoft.com/library/system.net.servicepoint.settcpkeepalive.aspx). Com a ligação keep-alive ativada, pacotes são enviados durante os períodos de inatividade na ligação. Estes pacotes de keep-alive Certifique-se de que nunca é atingido o valor de tempo limite de inatividade e a ligação é mantida por um longo período.

Esta definição funciona para apenas ligações de entrada. Para evitar perder a ligação, tem de configurar a ligação keep-alive de TCP com um intervalo menor do que a definição de tempo limite de inatividade ou aumentar o valor de tempo limite de inatividade. Para suportar tais cenários, adicionámos suporte para um tempo de limite de inatividade configurável. Agora pode defini-lo para uma duração de 4 a 30 minutos.

TCP keep-alive funciona bem para cenários em que a vida útil da bateria não é uma restrição. Não é recomendado para aplicativos móveis. Utilizar uma ligação keep-alive de um aplicativo móvel de TCP pode drenar a bateria do dispositivo mais rápido.

![Tempo limite TCP](./media/load-balancer-tcp-idle-timeout/image1.png)

As secções seguintes descrevem como alterar as definições de tempo limite de inatividade em máquinas virtuais e serviços em nuvem.

## <a name="configure-the-tcp-timeout-for-your-instance-level-public-ip-to-15-minutes"></a>Configurar o tempo limite TCP para o seu IP público de nível de instância para 15 minutos

```powershell
Set-AzurePublicIP -PublicIPName webip -VM MyVM -IdleTimeoutInMinutes 15
```

`IdleTimeoutInMinutes` é opcional. Se não estiver definido, o tempo limite predefinido é de 4 minutos. O intervalo de tempo limite aceitável é de 4 a 30 minutos.

## <a name="set-the-idle-timeout-when-creating-an-azure-endpoint-on-a-virtual-machine"></a>Definir o tempo limite de inatividade durante a criação de um ponto final do Azure numa máquina virtual

Para alterar a definição de tempo limite para um ponto de extremidade, utilize o seguinte:

```powershell
Get-AzureVM -ServiceName "mySvc" -Name "MyVM1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 8080 -IdleTimeoutInMinutes 15| Update-AzureVM
```

Para obter a configuração de tempo limite de inatividade, utilize o seguinte comando:

    PS C:\> Get-AzureVM -ServiceName "MyService" -Name "MyVM" | Get-AzureEndpoint
    VERBOSE: 6:43:50 PM - Completed Operation: Get Deployment
    LBSetName : MyLoadBalancedSet
    LocalPort : 80
    Name : HTTP
    Port : 80
    Protocol : tcp
    Vip : 65.52.xxx.xxx
    ProbePath :
    ProbePort : 80
    ProbeProtocol : tcp
    ProbeIntervalInSeconds : 15
    ProbeTimeoutInSeconds : 31
    EnableDirectServerReturn : False
    Acl : {}
    InternalLoadBalancerName :
    IdleTimeoutInMinutes : 15

## <a name="set-the-tcp-timeout-on-a-load-balanced-endpoint-set"></a>Definir o tempo limite TCP num conjunto de ponto final com balanceamento de carga

Se os pontos finais fazem parte de um conjunto de ponto final com balanceamento de carga, o tempo limite TCP deve ser definido no conjunto de ponto final com balanceamento de carga. Por exemplo:

```powershell
Set-AzureLoadBalancedEndpoint -ServiceName "MyService" -LBSetName "LBSet1" -Protocol tcp -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 -IdleTimeoutInMinutes 15
```

## <a name="change-timeout-settings-for-cloud-services"></a>Alterar as definições de tempo limite para os serviços cloud

Pode utilizar o Azure SDK para atualizar o seu serviço cloud. Verifique as definições de ponto de extremidade para serviços em nuvem no ficheiro. csdef. A atualizar o tempo limite TCP para a implementação de um serviço em nuvem requer uma atualização de implementação. Uma exceção é se o tempo limite TCP é especificado apenas para um IP público. Definições de IP públicas estão no ficheiro. cscfg e atualizá-las por meio de atualização da implementação e atualização.

As alterações. csdef para definições de ponto final são:

```xml
<WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
    <Endpoints>
    <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" idleTimeoutInMinutes="tcp-timeout" />
    </Endpoints>
</WorkerRole>
```

As alterações. cscfg para a definição de tempo limite nos IPs públicos são:

```xml
<NetworkConfiguration>
    <VirtualNetworkSite name="VNet"/>
    <AddressAssignments>
    <InstanceAddress roleName="VMRolePersisted">
    <PublicIPs>
        <PublicIP name="public-ip-name" idleTimeoutInMinutes="timeout-in-minutes"/>
    </PublicIPs>
    </InstanceAddress>
    </AddressAssignments>
</NetworkConfiguration>
```

## <a name="rest-api-example"></a>Exemplo de REST API

Pode configurar o tempo de limite de inatividade de TCP ao utilizar a API de gestão do serviço. Certifique-se de que o `x-ms-version` cabeçalho está definido para a versão `2014-06-01` ou posterior. Atualize a configuração das especificado com balanceamento de carga pontos finais de entrada em todas as máquinas virtuais numa implementação.

### <a name="request"></a>Pedir

    POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>

### <a name="response"></a>Resposta

```xml
<LoadBalancedEndpointList xmlns="http://schemas.microsoft.com/windowsazure" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
    <InputEndpoint>
    <LoadBalancedEndpointSetName>endpoint-set-name</LoadBalancedEndpointSetName>
    <LocalPort>local-port-number</LocalPort>
    <Port>external-port-number</Port>
    <LoadBalancerProbe>
        <Path>path-of-probe</Path>
        <Port>port-assigned-to-probe</Port>
        <Protocol>probe-protocol</Protocol>
        <IntervalInSeconds>interval-of-probe</IntervalInSeconds>
        <TimeoutInSeconds>timeout-for-probe</TimeoutInSeconds>
    </LoadBalancerProbe>
    <LoadBalancerName>name-of-internal-loadbalancer</LoadBalancerName>
    <Protocol>endpoint-protocol</Protocol>
    <IdleTimeoutInMinutes>15</IdleTimeoutInMinutes>
    <EnableDirectServerReturn>enable-direct-server-return</EnableDirectServerReturn>
    <EndpointACL>
        <Rules>
        <Rule>
            <Order>priority-of-the-rule</Order>
            <Action>permit-rule</Action>
            <RemoteSubnet>subnet-of-the-rule</RemoteSubnet>
            <Description>description-of-the-rule</Description>
        </Rule>
        </Rules>
    </EndpointACL>
    </InputEndpoint>
</LoadBalancedEndpointList>
```

## <a name="next-steps"></a>Passos Seguintes

[Descrição geral do Balanceador de carga interno](load-balancer-internal-overview.md)

[Começar a configurar um balanceador de carga com acesso à Internet](load-balancer-get-started-internet-arm-ps.md)

[Configurar um modo de distribuição de balanceador de carga](load-balancer-distribution-mode.md)
