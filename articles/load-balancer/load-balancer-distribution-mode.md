---
title: Configurar o modo de distribuição de Balanceador de carga do Azure
titleSuffix: Azure Load Balancer
description: Neste artigo, comece Configurando o modo de distribuição para Azure Load Balancer para dar suporte à afinidade de IP de origem.
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
ms.openlocfilehash: ddccd02e7157792d942309ae4f74933322f246f9
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225365"
---
# <a name="configure-the-distribution-mode-for-azure-load-balancer"></a>Configurar o modo de distribuição para o Balanceador de carga do Azure

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="hash-based-distribution-mode"></a>Modo de distribuição baseados em hash

O modo de distribuição padrão para Azure Load Balancer é um hash de cinco tuplas. 

A tupla é composta pelo:
* **IP de origem**
* **Porta de origem**
* **IP de destino**
* **Porta de destino**
* **Tipo de protocolo**

O hash é usado para mapear o tráfego para os servidores disponíveis. O algoritmo fornece adesão somente dentro de uma sessão de transporte. Os pacotes que estão na mesma sessão são direcionados para o mesmo IP do datacenter por trás do ponto de extremidade com balanceamento de carga. Quando o cliente inicia uma nova sessão do mesmo IP de origem, a porta de origem é alterada e faz com que o tráfego vá para um ponto de extremidade de datacenter diferente.

![Modo de distribuição baseado em hash de cinco tuplas](./media/load-balancer-distribution-mode/load-balancer-distribution.png)

## <a name="source-ip-affinity-mode"></a>Modo de afinidade do IP de origem

O balanceador de carga também pode ser configurado usando o modo de distribuição de afinidade de IP de origem. Esse modo de distribuição é também conhecido como afinidade de sessão ou a afinidade do IP de cliente. O modo usa um hash de duas tuplas (IP de origem e IP de destino) ou de três tuplas (IP de origem, IP de destino e tipo de protocolo) para mapear o tráfego para os servidores disponíveis. Ao usar a afinidade de IP de origem, as conexões iniciadas no mesmo computador cliente vão para o mesmo ponto de extremidade de datacenter.

A figura a seguir ilustra uma configuração de duas tuplas. Observe como as duas tuplas são executadas por meio do balanceador de carga para a máquina virtual 1 (VM1). VM1, em seguida, cópia de segurança, VM2 e VM3.

![Modo de distribuição de afinidade de sessão de duas tuplas](./media/load-balancer-distribution-mode/load-balancer-session-affinity.png)

Modo de afinidade do IP de origem resolve uma incompatibilidade entre o Balanceador de carga do Azure e o Gateway de ambiente de trabalho remoto (Gateway de RD). Ao utilizar este modo, pode criar um farm de servidores de Gateway de RD num serviço cloud único.

Outro cenário de caso de uso é o carregamento de suporte de dados. O carregamento de dados ocorre por meio de UDP, mas o plano de controlo é alcançado através de TCP:

* Um cliente inicia uma sessão TCP para o endereço público com balanceamento de carga e é direcionado para um DIP específico. O canal fica ativo para monitorizar o estado de funcionamento da ligação.
* Uma nova sessão UDP do mesmo computador cliente é iniciada para o mesmo ponto de extremidade público com balanceamento de carga. A ligação é direcionada para o mesmo ponto final do DIP como a ligação de TCP anterior. O carregamento de suporte de dados pode ser executado em alto débito, mantendo um canal de controlo através de TCP.

> [!NOTE]
> Quando altera um conjunto com balanceamento de carga através da remoção ou adicionar uma máquina virtual, a distribuição de pedidos de cliente é recalculada. Não é possível dependem novas ligações de clientes existentes para acabar no mesmo servidor. Além disso, IP de origem a utilizar o modo de distribuição de afinidade pode causar uma distribuição desiguais de tráfego. Os clientes que são executadas por trás de proxies podem ser vistos como um aplicativo de cliente exclusivos.

## <a name="configure-source-ip-affinity-settings"></a>Configurar definições de afinidade do IP de origem

### <a name="azure-portal"></a>Portal do Azure

Você pode alterar a configuração do modo de distribuição modificando a regra de balanceamento de carga no Portal.

1. Entre no portal do Azure e localize o grupo de recursos que contém o balanceador de carga que você deseja alterar clicando em **grupos de recursos**.
2. Na tela Visão geral do balanceador de carga, clique em **regras de balanceamento de carga** em **configurações**.
3. Na tela regras de balanceamento de carga, clique na regra de balanceamento de carga em que você deseja alterar o modo de distribuição.
4. Sob a regra, o modo de distribuição é alterado alterando a caixa suspensa de **persistência da sessão** .  As seguintes opções estão disponíveis:
    
    * **Nenhum (baseado em hash)** – especifica que as solicitações sucessivas do mesmo cliente podem ser tratadas por qualquer máquina virtual.
    * **IP do cliente (afinidade de IP de origem 2-tupla)** – especifica que as solicitações sucessivas do mesmo endereço IP do cliente serão tratadas pela mesma máquina virtual.
    * **IP do cliente e protocolo (afinidade de IP de origem 3-tupla)** – especifica que solicitações sucessivas do mesmo endereço IP do cliente e combinação de protocolos serão manipuladas pela mesma máquina virtual.

5. Escolha o modo de distribuição e, em seguida, clique em **salvar**.

### <a name="azure-powershell"></a>Azure PowerShell

Para máquinas virtuais implantadas com o Gerenciador de recursos, use o PowerShell para alterar as configurações de distribuição do balanceador de carga em uma regra de balanceamento de carga existente. O seguinte comando atualiza o modo de distribuição: 

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name MyLb -ResourceGroupName MyLbRg
$lb.LoadBalancingRules[0].LoadDistribution = 'sourceIp'
Set-AzLoadBalancer -LoadBalancer $lb
```

Para máquinas virtuais clássicas, utilize o Azure PowerShell para alterar as definições de distribuição. Adicionar um ponto de final do Azure a uma máquina virtual e configurar o modo de distribuição de Balanceador de carga:

```azurepowershell-interactive
Get-AzureVM -ServiceName mySvc -Name MyVM1 | Add-AzureEndpoint -Name HttpIn -Protocol TCP -PublicPort 80 -LocalPort 8080 –LoadBalancerDistribution sourceIP | Update-AzureVM
```

Defina o valor do elemento `LoadBalancerDistribution` para a quantidade de balanceamento de carga necessária. Especifique sourceIP para balanceamento de carga de duas tuplas (IP de origem e IP de destino). Especifique sourceIPProtocol para o balanceamento de carga de três tuplas (IP de origem, IP de destino e tipo de protocolo). Especifique nenhum para o comportamento padrão do balanceamento de carga de cinco tuplas.

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

Quando o elemento `LoadBalancerDistribution` não está presente, Azure Load Balancer usa o algoritmo padrão de cinco tuplas.

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

Utilize o modelo de implementação clássica do Azure para alterar uma configuração de implementação existente. Adicione o cabeçalho `x-ms-version` e defina o valor para a versão 2014-09-01 ou posterior.

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

Conforme descrito anteriormente, defina o elemento `LoadBalancerDistribution` como sourceIP para afinidade de duas tuplas, sourceIPProtocol para afinidade de três tuplas ou nenhum para nenhuma afinidade (afinidade de cinco tuplas).

#### <a name="response"></a>Resposta

    HTTP/1.1 202 Accepted
    Cache-Control: no-cache
    Content-Length: 0
    Server: 1.0.6198.146 (rd_rdfe_stable.141015-1306) Microsoft-HTTPAPI/2.0
    x-ms-servedbyregion: ussouth2
    x-ms-request-id: 9c7bda3e67c621a6b57096323069f7af
    Date: Thu, 16 Oct 2014 22:49:21 GMT

## <a name="next-steps"></a>Passos seguintes

* [Visão geral do Load Balancer interno do Azure](load-balancer-internal-overview.md)
* [Introdução à configuração de um balanceador de carga voltado para a Internet](load-balancer-get-started-internet-arm-ps.md)
* [Configurar definições de tempo limite TCP inativo para o balanceador de carga](load-balancer-tcp-idle-timeout.md)
