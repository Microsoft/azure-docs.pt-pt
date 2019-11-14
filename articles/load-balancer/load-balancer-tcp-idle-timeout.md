---
title: Configurar o tempo limite de TCP ocioso do balanceador de carga no Azure
titleSuffix: Azure Load Balancer
description: Neste artigo, saiba como configurar Azure Load Balancer tempo limite de ociosidade de TCP.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: allensu
ms.openlocfilehash: 530bfbe85a564b3dd517e14df819586dee332a78
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74076973"
---
# <a name="configure-tcp-idle-timeout-settings-for-azure-load-balancer"></a>Definir configurações de tempo limite de ociosidade de TCP para Azure Load Balancer

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Em sua configuração padrão, Azure Load Balancer tem uma configuração de tempo limite de ociosidade de 4 minutos. Se um período de inatividade for maior do que o valor de tempo limite, não haverá garantia de que a sessão TCP ou HTTP seja mantida entre o cliente e o serviço de nuvem.

Quando a conexão é fechada, o aplicativo cliente pode receber a seguinte mensagem de erro: "a conexão subjacente foi fechada: uma conexão que deveria ser mantida ativa foi fechada pelo servidor".

Uma prática comum é usar um TCP Keep-Alive. Essa prática mantém a conexão ativa por um período mais longo. Para obter mais informações, consulte estes [exemplos do .net](https://msdn.microsoft.com/library/system.net.servicepoint.settcpkeepalive.aspx). Com keep-alive habilitado, os pacotes são enviados durante períodos de inatividade na conexão. Esses pacotes Keep-Alive garantem que o valor de tempo limite de ociosidade nunca seja atingido e a conexão seja mantida por um longo período.

Essa configuração funciona apenas para conexões de entrada. Para evitar a perda da conexão, você deve configurar o TCP Keep-Alive com um intervalo menor que a configuração de tempo limite de ociosidade ou aumentar o valor de tempo limite de ociosidade. Para dar suporte a esses cenários, adicionamos suporte para um tempo limite de ociosidade configurável. Agora você pode defini-lo por uma duração de 4 a 30 minutos.

O TCP Keep-Alive funciona bem para cenários em que a vida útil da bateria não é uma restrição. Não é recomendável para aplicativos móveis. Usar um TCP Keep-Alive em um aplicativo móvel pode drenar a bateria do dispositivo mais rapidamente.

![Tempo limite de TCP](./media/load-balancer-tcp-idle-timeout/image1.png)

As seções a seguir descrevem como alterar as configurações de tempo limite de ociosidade em máquinas virtuais e serviços de nuvem.

## <a name="configure-the-tcp-timeout-for-your-instance-level-public-ip-to-15-minutes"></a>Configurar o tempo limite de TCP para o IP público em nível de instância para 15 minutos

```powershell
Set-AzurePublicIP -PublicIPName webip -VM MyVM -IdleTimeoutInMinutes 15
```

`IdleTimeoutInMinutes` é opcional. Se não estiver definido, o tempo limite padrão será de 4 minutos. O intervalo de tempo limite aceitável é de 4 a 30 minutos.

## <a name="set-the-idle-timeout-when-creating-an-azure-endpoint-on-a-virtual-machine"></a>Definir o tempo limite de ociosidade ao criar um ponto de extremidade do Azure em uma máquina virtual

Para alterar a configuração de tempo limite de um ponto de extremidade, use o seguinte:

```powershell
Get-AzureVM -ServiceName "mySvc" -Name "MyVM1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 8080 -IdleTimeoutInMinutes 15| Update-AzureVM
```

Para recuperar sua configuração de tempo limite de ociosidade, use o seguinte comando:

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

## <a name="set-the-tcp-timeout-on-a-load-balanced-endpoint-set"></a>Definir o tempo limite de TCP em um conjunto de pontos de extremidade com balanceamento de carga

Se os pontos de extremidade fizerem parte de um conjunto de pontos de extremidades com balanceamento de carga, o tempo limite de TCP deverá ser definido no conjunto de pontos de extremidade com balanceamento de carga. Por exemplo:

```powershell
Set-AzureLoadBalancedEndpoint -ServiceName "MyService" -LBSetName "LBSet1" -Protocol tcp -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 -IdleTimeoutInMinutes 15
```

## <a name="change-timeout-settings-for-cloud-services"></a>Alterar configurações de tempo limite para serviços de nuvem

Você pode usar o SDK do Azure para atualizar seu serviço de nuvem. Você faz configurações de ponto de extremidade para serviços de nuvem no arquivo. csdef. A atualização do tempo limite de TCP para a implantação de um serviço de nuvem requer uma atualização de implantação. Uma exceção é se o tempo limite de TCP é especificado somente para um IP público. As configurações de IP público estão no arquivo. cscfg e você pode atualizá-las por meio da atualização e atualização de implantação.

As alterações de. csdef para configurações de ponto de extremidade são:

```xml
<WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
    <Endpoints>
    <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" idleTimeoutInMinutes="tcp-timeout" />
    </Endpoints>
</WorkerRole>
```

As alterações de. cscfg para a configuração de tempo limite em IPs públicos são:

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

## <a name="rest-api-example"></a>Exemplo de API REST

Você pode configurar o tempo limite de ociosidade de TCP usando a API de gerenciamento de serviços. Verifique se o cabeçalho de `x-ms-version` está definido como versão `2014-06-01` ou posterior. Atualize a configuração dos pontos de extremidade de entrada com balanceamento de carga especificados em todas as máquinas virtuais em uma implantação.

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

## <a name="next-steps"></a>Passos seguintes

[Visão geral do balanceador de carga interno](load-balancer-internal-overview.md)

[Introdução à configuração de um balanceador de carga voltado para a Internet](load-balancer-get-started-internet-arm-ps.md)

[Configurar um modo de distribuição de balanceador de carga](load-balancer-distribution-mode.md)
