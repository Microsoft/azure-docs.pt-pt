---
title: Configurar o modo de distribuição de Balanceador de carga do Azure
titlesuffix: Azure Load Balancer
description: Como configurar o modo de distribuição de Balanceador de carga do Azure suportar a afinidade do IP de origem.
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: ea8396b95b45188b9a629da5147bf5246df908f9
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2019
ms.locfileid: "56674468"
---
# <a name="configure-the-distribution-mode-for-azure-load-balancer"></a>Configurar o modo de distribuição para o Balanceador de carga do Azure

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="hash-based-distribution-mode"></a>Modo de distribuição baseados em hash

O modo de distribuição predefinido para o Balanceador de carga do Azure é um hash de 5 cadeias de identificação. A cadeia de identificação é composta pelo IP de origem, porta de origem, IP de destino, porta de destino e o tipo de protocolo. O hash é utilizado para mapear o tráfego para os servidores disponíveis e o algoritmo fornece persistência apenas dentro de uma sessão de transporte. Pacotes que estão na mesma sessão são direcionados para a mesma instância de IP (DIP) do Centro de dados por trás do ponto final com balanceamento de carga. Quando o cliente inicia uma nova sessão do mesmo IP de origem, a porta de origem é alterado e faz com que o tráfego Ir para um ponto de extremidade diferente do DIP.

![modo de distribuição com base em hash de 5 cadeias de identificação](./media/load-balancer-distribution-mode/load-balancer-distribution.png)

## <a name="source-ip-affinity-mode"></a>Modo de afinidade do IP de origem

Também pode ser configurado o Balanceador de carga utilizando o modo de distribuição de afinidade IP de origem. Esse modo de distribuição é também conhecido como afinidade de sessão ou a afinidade do IP de cliente. O modo utiliza uma 2 cadeias de identificação (IP de origem e destino IP) ou 3 cadeias de identificação (IP de origem, IP de destino e protocolo escreva) de hash para mapear o tráfego para os servidores disponíveis. Ao utilizar a afinidade do IP de origem, ligações sejam iniciadas a partir do mesmo computador de cliente, vá para o mesmo ponto de final do DIP.

A figura seguinte ilustra uma configuração de 2 cadeias de identificação. Observe como cadeias de identificação 2 é executado através do Balanceador de carga para a máquina virtual 1 (VM1 a). VM1, em seguida, cópia de segurança, VM2 e VM3.

![modo de distribuição de afinidade de sessão de 2 cadeias de identificação](./media/load-balancer-distribution-mode/load-balancer-session-affinity.png)

Modo de afinidade do IP de origem resolve uma incompatibilidade entre o Balanceador de carga do Azure e o Gateway de ambiente de trabalho remoto (Gateway de RD). Ao utilizar este modo, pode criar um farm de servidores de Gateway de RD num serviço cloud único.

Outro cenário de caso de uso é o carregamento de suporte de dados. O carregamento de dados ocorre por meio de UDP, mas o plano de controlo é alcançado através de TCP:

* Um cliente inicia uma sessão TCP para o endereço com balanceamento de carga público e é direcionado para um DIP específico. O canal fica ativo para monitorizar o estado de funcionamento da ligação.
* Uma nova sessão UDP no mesmo computador cliente é iniciada para o mesmo com balanceamento de carga ponto final público. A ligação é direcionada para o mesmo ponto final do DIP como a ligação de TCP anterior. O carregamento de suporte de dados pode ser executado em alto débito, mantendo um canal de controlo através de TCP.

> [!NOTE]
> Quando altera um conjunto com balanceamento de carga através da remoção ou adicionar uma máquina virtual, a distribuição de pedidos de cliente é recalculada. Não é possível dependem novas ligações de clientes existentes para acabar no mesmo servidor. Além disso, IP de origem a utilizar o modo de distribuição de afinidade pode causar uma distribuição desiguais de tráfego. Os clientes que são executadas por trás de proxies podem ser vistos como um aplicativo de cliente exclusivos.

## <a name="configure-source-ip-affinity-settings"></a>Configurar definições de afinidade do IP de origem

Para máquinas virtuais implementadas com o Resource Manager, utilize o PowerShell para alterar as definições de distribuição de Balanceador de carga numa regra de balanceamento de carga existente. Este procedimento atualiza o modo de distribuição: 

```powershell
$lb = Get-AzLoadBalancer -Name MyLb -ResourceGroupName MyLbRg
$lb.LoadBalancingRules[0].LoadDistribution = 'sourceIp'
Set-AzLoadBalancer -LoadBalancer $lb
```

Para máquinas virtuais clássicas, utilize o Azure PowerShell para alterar as definições de distribuição. Adicionar um ponto de final do Azure a uma máquina virtual e configurar o modo de distribuição de Balanceador de carga:

```powershell
Get-AzureVM -ServiceName mySvc -Name MyVM1 | Add-AzureEndpoint -Name HttpIn -Protocol TCP -PublicPort 80 -LocalPort 8080 –LoadBalancerDistribution sourceIP | Update-AzureVM
```

Defina o valor do `LoadBalancerDistribution` elemento para a quantidade desejada de balanceamento de carga. Especifique sourceIP para balanceamento de carga de 2 cadeias de identificação (IP de origem e destino IP). Especifique o sourceIPProtocol para 3 cadeias de identificação (IP de origem, IP de destino e protocolo escreva) balanceamento de carga. Especifique nenhum para o comportamento padrão do balanceamento de carga de 5 cadeias de identificação.

Obter uma configuração de modo de distribuição de Balanceador de carga de ponto final com estas definições:

    PS C:\> Get-AzureVM –ServiceName MyService –Name MyVM | Get-AzureEndpoint

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
    LoadBalancerDistribution : sourceIP

Quando o `LoadBalancerDistribution` elemento não está presente, o Balanceador de carga do Azure utiliza o algoritmo de 5 cadeias de identificação predefinida.

### <a name="configure-distribution-mode-on-load-balanced-endpoint-set"></a>Configurar o modo de distribuição no conjunto de pontos finais Balanceados de carga

Quando os pontos finais fazem parte de um conjunto de ponto final com balanceamento de carga, o modo de distribuição tem de ser configurado no conjunto de ponto final com balanceamento de carga:

```azurepowershell
Set-AzureLoadBalancedEndpoint -ServiceName MyService -LBSetName LBSet1 -Protocol TCP -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 –LoadBalancerDistribution sourceIP
```

### <a name="configure-distribution-mode-for-cloud-services-endpoints"></a>Configurar o modo de distribuição para pontos de extremidade de serviços Cloud

Utilize o Azure SDK para .NET 2.5 para atualizar o seu serviço cloud. As definições de ponto de extremidade para serviços em nuvem são efetuadas no ficheiro. csdef. Para atualizar o modo de distribuição de Balanceador de carga para uma implementação de serviços Cloud, uma atualização de implementação é necessária.

Eis um exemplo de alterações. csdef para definições de ponto final:

```xml
<WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
    <Endpoints>
    <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" loadBalancerDistribution="sourceIP" />
    </Endpoints>
</WorkerRole>
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

## <a name="api-example"></a>Exemplo de API

O exemplo seguinte mostra como reconfigurar o modo de distribuição de Balanceador de carga para um conjunto com balanceamento de carga especificado numa implementação. 

### <a name="change-distribution-mode-for-deployed-load-balanced-set"></a>Alterar o modo de distribuição para o conjunto com balanceamento de carga implementado

Utilize o modelo de implementação clássica do Azure para alterar uma configuração de implementação existente. Adicionar o `x-ms-version` cabeçalho e defina o valor para a versão 2014-09-01 ou posterior.

#### <a name="request"></a>Pedir

    POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>?comp=UpdateLbSet   x-ms-version: 2014-09-01
    Content-Type: application/xml

    <LoadBalancedEndpointList xmlns="http://schemas.microsoft.com/windowsazure" xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
      <InputEndpoint>
        <LoadBalancedEndpointSetName> endpoint-set-name </LoadBalancedEndpointSetName>
        <LocalPort> local-port-number </LocalPort>
        <Port> external-port-number </Port>
        <LoadBalancerProbe>
          <Port> port-assigned-to-probe </Port>
          <Protocol> probe-protocol </Protocol>
          <IntervalInSeconds> interval-of-probe </IntervalInSeconds>
          <TimeoutInSeconds> timeout-for-probe </TimeoutInSeconds>
        </LoadBalancerProbe>
        <Protocol> endpoint-protocol </Protocol>
        <EnableDirectServerReturn> enable-direct-server-return </EnableDirectServerReturn>
        <IdleTimeoutInMinutes>idle-time-out</IdleTimeoutInMinutes>
        <LoadBalancerDistribution>sourceIP</LoadBalancerDistribution>
      </InputEndpoint>
    </LoadBalancedEndpointList>

Como anteriormente descrito, defina o `LoadBalancerDistribution` elemento para sourceIP para a afinidade de 2 cadeias de identificação, sourceIPProtocol para a afinidade de cadeias de identificação 3 ou nenhum para sem afinidade (afinidade de 5 cadeias de identificação).

#### <a name="response"></a>Resposta

    HTTP/1.1 202 Accepted
    Cache-Control: no-cache
    Content-Length: 0
    Server: 1.0.6198.146 (rd_rdfe_stable.141015-1306) Microsoft-HTTPAPI/2.0
    x-ms-servedbyregion: ussouth2
    x-ms-request-id: 9c7bda3e67c621a6b57096323069f7af
    Date: Thu, 16 Oct 2014 22:49:21 GMT

## <a name="next-steps"></a>Passos Seguintes

* [Descrição geral do Balanceador de carga interno do Azure](load-balancer-internal-overview.md)
* [Introdução ao configurar um balanceador de carga com acesso à internet](load-balancer-get-started-internet-arm-ps.md)
* [Configurar definições de tempo limite TCP inativo para o balanceador de carga](load-balancer-tcp-idle-timeout.md)
