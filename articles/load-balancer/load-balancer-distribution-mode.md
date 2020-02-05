---
title: Configurar o modo de distribuição de Balanceador de carga do Azure
titleSuffix: Azure Load Balancer
description: Neste artigo, começar a configurar o modo de distribuição do Azure Load Balancer para suportar fonte de afinidade IP.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/19/2019
ms.author: allensu
ms.openlocfilehash: 5c50186692438be5d0922cd329c28e665310e5c2
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77023536"
---
# <a name="configure-the-distribution-mode-for-azure-load-balancer"></a>Configurar o modo de distribuição para o Balanceador de carga do Azure

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="hash-based-distribution-mode"></a>Modo de distribuição baseados em hash

O modo de distribuição predefinido para o Azure Load Balancer é um hash de cinco tuple. 

O tuple é composto pelo:
* **Fonte IP**
* **Porta de origem**
* **Destino IP**
* **Porta de destino**
* **Tipo de protocolo**

O hash é usado para mapear o tráfego para os servidores disponíveis. O algoritmo só proporciona a destreza dentro de uma sessão de transporte. Os pacotes que estão na mesma sessão são direcionados para o mesmo DATAcenter IP atrás do ponto final equilibrado. Quando o cliente inicia uma nova sessão a partir da mesma fonte IP, a porta de origem muda e faz com que o tráfego vá para um ponto final diferente do datacenter.

![Modo de distribuição baseado em hash de cinco tuple](./media/load-balancer-distribution-mode/load-balancer-distribution.png)

## <a name="source-ip-affinity-mode"></a>Modo de afinidade do IP de origem

O equilibrador de carga também pode ser configurado utilizando o modo de distribuição de afinidade IP fonte. Esse modo de distribuição é também conhecido como afinidade de sessão ou a afinidade do IP de cliente. O modo utiliza um hash de duas tuple (IP de origem e destino) ou três tuple (IP de origem, IP de destino e tipo de protocolo) para mapear o tráfego para os servidores disponíveis. Utilizando a afinidade IP fonte, as ligações que são iniciadas a partir do mesmo computador cliente vão para o mesmo ponto final do datacenter.

A figura que se segue ilustra uma configuração de dois túnicas. Note como o estuque de duas vezes passa através do equilibrador de carga para a máquina virtual 1 (VM1). VM1, em seguida, cópia de segurança, VM2 e VM3.

![Modo de distribuição de afinidade de sessão de dois tuple](./media/load-balancer-distribution-mode/load-balancer-session-affinity.png)

Modo de afinidade do IP de origem resolve uma incompatibilidade entre o Balanceador de carga do Azure e o Gateway de ambiente de trabalho remoto (Gateway de RD). Ao utilizar este modo, pode criar um farm de servidores de Gateway de RD num serviço cloud único.

Outro cenário de caso de uso é o carregamento de suporte de dados. O carregamento de dados ocorre por meio de UDP, mas o plano de controlo é alcançado através de TCP:

* Um cliente inicia uma sessão de TCP para o endereço público equilibrado e é direcionado para um DIP específico. O canal fica ativo para monitorizar o estado de funcionamento da ligação.
* Uma nova sessão uDP do mesmo computador cliente é iniciada para o mesmo ponto final público equilibrado em carga. A ligação é direcionada para o mesmo ponto final do DIP como a ligação de TCP anterior. O carregamento de suporte de dados pode ser executado em alto débito, mantendo um canal de controlo através de TCP.

> [!NOTE]
> Quando altera um conjunto com balanceamento de carga através da remoção ou adicionar uma máquina virtual, a distribuição de pedidos de cliente é recalculada. Não é possível dependem novas ligações de clientes existentes para acabar no mesmo servidor. Além disso, IP de origem a utilizar o modo de distribuição de afinidade pode causar uma distribuição desiguais de tráfego. Os clientes que são executadas por trás de proxies podem ser vistos como um aplicativo de cliente exclusivos.

## <a name="configure-source-ip-affinity-settings"></a>Configurar definições de afinidade do IP de origem

### <a name="azure-portal"></a>Portal do Azure

Pode alterar a configuração do modo de distribuição modificando a regra de equilíbrio de carga no portal.

1. Inscreva-se no portal Azure e localize o Grupo de Recursos que contém o equilibrador de carga que pretende alterar clicando em **Grupos de Recursos**.
2. No ecrã de visão geral do equilíbrio de carga, clique nas **regras de equilíbrio de carga** em **Definições**.
3. No ecrã de regras de equilíbrio de carga, clique na regra de equilíbrio de carga que pretende alterar o modo de distribuição.
4. Sob a regra, o modo de distribuição é alterado alterando a caixa de descida de **persistência da Sessão.**  Estão disponíveis as seguintes opções:
    
    * **Nenhum (à base de hash)** - Especifica que os pedidos sucessivos do mesmo cliente podem ser tratados por qualquer máquina virtual.
    * **IP cliente (fonte IP affinity 2-tuple)** - Especifica que os pedidos sucessivos do mesmo endereço IP do cliente serão tratados pela mesma máquina virtual.
    * **IP do cliente e protocolo (fonte IP affinity 3-tuple)** - Especifica que os pedidos sucessivos do mesmo endereço IP do cliente e combinação de protocolo saem tratados pela mesma máquina virtual.

5. Escolha o modo de distribuição e, em seguida, clique em **Guardar**.

### <a name="azure-powershell"></a>Azure PowerShell

Para máquinas virtuais implantadas com O Gestor de Recursos, utilize o PowerShell para alterar as definições de distribuição do equilíbrio de carga numa regra de equilíbrio de carga existente. O seguinte comando atualiza o modo de distribuição: 

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name MyLb -ResourceGroupName MyLbRg
$lb.LoadBalancingRules[0].LoadDistribution = 'sourceIp'
Set-AzLoadBalancer -LoadBalancer $lb
```

Para máquinas virtuais clássicas, utilize o Azure PowerShell para alterar as definições de distribuição. Adicionar um ponto de final do Azure a uma máquina virtual e configurar o modo de distribuição de Balanceador de carga:

```azurepowershell-interactive
Get-AzureVM -ServiceName mySvc -Name MyVM1 | Add-AzureEndpoint -Name HttpIn -Protocol TCP -PublicPort 80 -LocalPort 8080 –LoadBalancerDistribution sourceIP | Update-AzureVM
```

Detete o valor do elemento `LoadBalancerDistribution` para a quantidade de equilíbrio de carga necessária. Especifique o equilíbrio de carga de duas tuple (IP de origem e IP de destino). Especifique o protocolo de origem IPProtocol para o equilíbrio de carga de três tuple (IP de origem, ip de destino e tipo de protocolo). Especifique nenhum para o comportamento padrão do equilíbrio de carga de cinco tuplés.

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

Quando o elemento `LoadBalancerDistribution` não está presente, o Azure Load Balancer usa o algoritmo padrão de cinco tuples.

### <a name="configure-distribution-mode-on-load-balanced-endpoint-set"></a>Configurar o modo de distribuição no conjunto de pontos finais Balanceados de carga

Quando os pontos finais fazem parte de um conjunto de ponto final com balanceamento de carga, o modo de distribuição tem de ser configurado no conjunto de ponto final com balanceamento de carga:

```azurepowershell-interactive
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

Como descrito anteriormente, detetete o elemento `LoadBalancerDistribution` para fonteIP para afinidade de dois tuple, sourceIPProtocol para afinidade de três tuple, ou nenhum para nenhuma afinidade (afinidade de cinco tuple).

#### <a name="response"></a>Resposta

    HTTP/1.1 202 Accepted
    Cache-Control: no-cache
    Content-Length: 0
    Server: 1.0.6198.146 (rd_rdfe_stable.141015-1306) Microsoft-HTTPAPI/2.0
    x-ms-servedbyregion: ussouth2
    x-ms-request-id: 9c7bda3e67c621a6b57096323069f7af
    Date: Thu, 16 Oct 2014 22:49:21 GMT

## <a name="next-steps"></a>Passos seguintes

* [Descrição geral do Balanceador de carga interno do Azure](load-balancer-internal-overview.md)
* [Introdução ao configurar um balanceador de carga com acesso à internet](quickstart-create-standard-load-balancer-powershell.md)
* [Configurar definições de tempo limite TCP inativo para o balanceador de carga](load-balancer-tcp-idle-timeout.md)
