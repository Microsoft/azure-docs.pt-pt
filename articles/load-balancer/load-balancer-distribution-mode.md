---
title: Configurar o modo de distribuição do balanceador de carga Azure
titleSuffix: Azure Load Balancer
description: Neste artigo, começa a configurar o modo de distribuição do Azure Load Balancer para suportar a afinidade IP de origem.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/19/2019
ms.author: allensu
ms.openlocfilehash: d6995073ba7d283a30f7580456cd61573752f7cc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89051394"
---
# <a name="configure-the-distribution-mode-for-azure-load-balancer"></a>Configure o modo de distribuição para o Balançador de Carga Azure

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="hash-based-distribution-mode"></a>Modo de distribuição baseado em haxixe

O modo de distribuição predefinido do Balanceador de Carga do Azure é um hash de cinco cadeias. 

A tuple é composta por:
* **IP de origem**
* **Porta de origem**
* **IP de destino**
* **Porta de destino**
* **Tipo de protocolo**

O haxixe é usado para mapear o tráfego para os servidores disponíveis. O algoritmo só proporciona adesão dentro de uma sessão de transporte. Os pacotes que estão na mesma sessão são direcionados para o mesmo DATAcenter IP atrás do ponto final equilibrado de carga. Quando o cliente inicia uma nova sessão a partir da mesma fonte IP, a porta de origem muda e faz com que o tráfego vá para um ponto final diferente do datacenter.

![Modo de distribuição baseado em haxixe de cinco tuples](./media/load-balancer-distribution-mode/load-balancer-distribution.png)

## <a name="source-ip-affinity-mode"></a>Modo de afinidade IP de origem

O equilibrador de carga também pode ser configurado utilizando o modo de distribuição de afinidade IP de origem. O modo de distribuição também é conhecido como afinidade de sessão ou afinidade de IP de cliente. O modo utiliza um haxixe de dois tuples (IP de origem e destino IP) ou três tuple (fonte IP, destino IP e tipo de protocolo) para mapear o tráfego para os servidores disponíveis. Utilizando a afinidade IP de origem, as ligações que são iniciadas a partir do mesmo computador cliente vão para o mesmo ponto final do datacenter.

A seguinte figura ilustra uma configuração de dois tuples. Note como o dois tuple passa pelo equilibrador de carga para a máquina virtual 1 (VM1). VM1 é então apoiado por VM2 e VM3.

![Modo de distribuição de afinidade de sessão de dois tuple](./media/load-balancer-distribution-mode/load-balancer-session-affinity.png)

O modo de afinidade IP de origem resolve uma incompatibilidade entre o Balançador de Carga Azure e o Gateway de Ambiente de Trabalho Remoto (Gateway RD). Ao utilizar este modo, pode construir uma quinta RD Gateway num único serviço de nuvem.

Outro cenário de uso é o upload dos media. O upload de dados acontece através da UDP, mas o plano de controlo é alcançado através da TCP:

* Um cliente inicia uma sessão de TCP para o endereço público equilibrado e é direcionado para um DIP específico. O canal é deixado ativo para monitorizar a saúde da ligação.
* Uma nova sessão de UDP do mesmo computador cliente é iniciada para o mesmo ponto final público equilibrado em carga. A ligação é direcionada para o mesmo ponto final DIP que a ligação TCP anterior. O upload de mídia pode ser executado em alta produção, mantendo um canal de controlo através de TCP.

> [!NOTE]
> Quando um conjunto equilibrado de carga muda removendo ou adicionando uma máquina virtual, a distribuição dos pedidos do cliente é recomputação. Não pode depender de novas ligações de clientes existentes para acabar no mesmo servidor. Além disso, a utilização do modo de distribuição de afinidade IP de origem pode causar uma distribuição desigual do tráfego. Os clientes que correm atrás de proxies podem ser vistos como uma aplicação única para clientes.

## <a name="configure-source-ip-affinity-settings"></a>Configurar definições de afinidade IP de fonte de origem

### <a name="azure-portal"></a>Portal do Azure

Pode alterar a configuração do modo de distribuição modificando a regra de equilíbrio de carga no portal.

1. Inscreva-se no portal Azure e localize o Grupo de Recursos que contém o equilibrador de carga que pretende alterar clicando em **Grupos de Recursos**.
2. No ecrã geral do balançador de carga, clique nas **regras de equilíbrio de carga** em **Definições**.
3. No ecrã das regras de equilíbrio de carga, clique na regra de equilíbrio de carga que deseja alterar o modo de distribuição.
4. De acordo com a regra, o modo de distribuição é alterado alterando a caixa de queda de persistência da **Sessão.**  Estão disponíveis as seguintes opções:
    
    * **Nenhum (baseado em haxixe)** - Especifica que os pedidos sucessivos do mesmo cliente podem ser tratados por qualquer máquina virtual.
    * **Computador do Cliente (fonte de afinidade IP 2-tuple)** - Especifica que os pedidos sucessivos do mesmo endereço IP do cliente serão tratados pela mesma máquina virtual.
    * **Computador e protocolo do Cliente (fonte de afinidade IP 3-tuple)** - Especifica que os pedidos sucessivos a partir do mesmo endereço IP do cliente e combinação de protocolos serão tratados pela mesma máquina virtual.

5. Escolha o modo de distribuição e, em seguida, clique em **Guardar**.

### <a name="azure-powershell"></a>Azure PowerShell

Para máquinas virtuais implantadas com o Gestor de Recursos, utilize o PowerShell para alterar as definições de distribuição do balanceador de carga numa regra de equilíbrio de carga existente. O seguinte comando atualiza o modo de distribuição: 

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name MyLb -ResourceGroupName MyLbRg
$lb.LoadBalancingRules[0].LoadDistribution = 'sourceIp'
Set-AzLoadBalancer -LoadBalancer $lb
```

Para máquinas virtuais clássicas, utilize o Azure PowerShell para alterar as definições de distribuição. Adicione um ponto final Azure a uma máquina virtual e configuure o modo de distribuição do balanceador de carga:

```azurepowershell-interactive
Get-AzureVM -ServiceName mySvc -Name MyVM1 | Add-AzureEndpoint -Name HttpIn -Protocol TCP -PublicPort 80 -LocalPort 8080 –LoadBalancerDistribution sourceIP | Update-AzureVM
```

Desabrague o valor do `LoadBalancerDistribution` elemento para a quantidade de equilíbrio de carga necessária. Especifique a fonteIP para o equilíbrio de carga de dois tuple (IP de origem e destino IP). Especifique a fonteIPProtocol para o equilíbrio de carga de três tuple (IP de origem, destino IP e tipo de protocolo). Especifique nenhum para o comportamento padrão de equilíbrio de carga de cinco tuples.

Recupere uma configuração do modo de distribuição do balanceador de carga de ponto final utilizando estas definições:

```azurepowershell
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
```

Quando o `LoadBalancerDistribution` elemento não está presente, o Azure Load Balancer utiliza o algoritmo padrão de cinco tuples.

### <a name="configure-distribution-mode-on-load-balanced-endpoint-set"></a>Modo de distribuição de configuração no conjunto de ponto final equilibrado de carga

Quando os pontos finais fazem parte de um conjunto de pontos finais equilibrados em carga, o modo de distribuição deve ser configurado no conjunto de pontos finais equilibrados de carga:

```azurepowershell-interactive
Set-AzureLoadBalancedEndpoint -ServiceName MyService -LBSetName LBSet1 -Protocol TCP -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 –LoadBalancerDistribution sourceIP
```

### <a name="configure-distribution-mode-for-cloud-services-endpoints"></a>Modo de distribuição de configuração para pontos finais dos Serviços cloud

Utilize o Azure SDK para .NET 2.5 para atualizar o seu serviço na nuvem. As definições de ponto final para serviços cloud são feitas no ficheiro .csdef. Para atualizar o modo de distribuição do balanceador de carga para uma implementação de Serviços cloud, é necessária uma atualização de implementação.

Aqui está um exemplo de alterações .csdef para definições de ponto final:

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

## <a name="api-example"></a>Exemplo da API

O exemplo a seguir mostra como reconfigurar o modo de distribuição do balançador de carga para um conjunto equilibrado de carga especificado numa implantação. 

### <a name="change-distribution-mode-for-deployed-load-balanced-set"></a>Alterar o modo de distribuição para conjunto equilibrado de carga implantado

Utilize o modelo de implantação clássica Azure para alterar uma configuração de implementação existente. Adicione o `x-ms-version` cabeçalho e desa couuse o valor para a versão 2014-09-01 ou mais tarde.

#### <a name="request"></a>Pedir

```http
POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>?comp=UpdateLbSet   x-ms-version: 2014-09-01
Content-Type: application/xml
```

```xml
<LoadBalancedEndpointList xmlns="http://schemas.microsoft.com/windowsazure" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
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
```

Como descrito anteriormente, defina o `LoadBalancerDistribution` elemento para a afinidade de dois tuples, fonteIPProtocol para afinidade de três tuples, ou nenhum para afinidade (afinidade de cinco tuples).

#### <a name="response"></a>Resposta

```http
HTTP/1.1 202 Accepted
Cache-Control: no-cache
Content-Length: 0
Server: 1.0.6198.146 (rd_rdfe_stable.141015-1306) Microsoft-HTTPAPI/2.0
x-ms-servedbyregion: ussouth2
x-ms-request-id: 9c7bda3e67c621a6b57096323069f7af
Date: Thu, 16 Oct 2014 22:49:21 GMT
```

## <a name="next-steps"></a>Passos seguintes

* [Visão geral do balançador interno de carga Azure](load-balancer-internal-overview.md)
* [Começar com a configuração de um equilibrador de carga virado para a Internet](quickstart-load-balancer-standard-public-powershell.md)
* [Configurar definições de tempo limite TCP inativo para o balanceador de carga](load-balancer-tcp-idle-timeout.md)
