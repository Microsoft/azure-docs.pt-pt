---
title: Configure Modo de distribuição de balanceadores de carga Azure
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77023536"
---
# <a name="configure-the-distribution-mode-for-azure-load-balancer"></a>Configure o modo de distribuição para o Equilíbrio de Carga Azure

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="hash-based-distribution-mode"></a>Modo de distribuição baseado em hash

O modo de distribuição predefinido do Balanceador de Carga do Azure é um hash de cinco cadeias. 

O tuple é composto pelo:
* **Fonte IP**
* **Porta-fonte**
* **Destino IP**
* **Porto de destino**
* **Tipo de protocolo**

O hash é usado para mapear o tráfego para os servidores disponíveis. O algoritmo só proporciona a destreza dentro de uma sessão de transporte. Os pacotes que estão na mesma sessão são direcionados para o mesmo DATAcenter IP atrás do ponto final equilibrado. Quando o cliente inicia uma nova sessão a partir da mesma fonte IP, a porta de origem muda e faz com que o tráfego vá para um ponto final diferente do datacenter.

![Modo de distribuição baseado em hash de cinco tuple](./media/load-balancer-distribution-mode/load-balancer-distribution.png)

## <a name="source-ip-affinity-mode"></a>Modo de afinidade IP fonte

O equilibrador de carga também pode ser configurado utilizando o modo de distribuição de afinidade IP fonte. O modo de distribuição também é conhecido como afinidade de sessão ou afinidade de IP de cliente. O modo utiliza um hash de duas tuple (IP de origem e destino) ou três tuple (IP de origem, IP de destino e tipo de protocolo) para mapear o tráfego para os servidores disponíveis. Utilizando a afinidade IP fonte, as ligações que são iniciadas a partir do mesmo computador cliente vão para o mesmo ponto final do datacenter.

A figura que se segue ilustra uma configuração de dois túnicas. Note como o estuque de duas vezes passa através do equilibrador de carga para a máquina virtual 1 (VM1). VM1 é então apoiado por VM2 e VM3.

![Modo de distribuição de afinidade de sessão de dois tuple](./media/load-balancer-distribution-mode/load-balancer-session-affinity.png)

O modo de afinidade IP source resolve uma incompatibilidade entre o Equilíbrio de Carga Azure e o Gateway do Ambiente de Trabalho Remoto (Gateway RD). Ao utilizar este modo, pode construir uma quinta RD Gateway num único serviço em nuvem.

Outro cenário de uso é o upload dos meios de comunicação. O upload de dados ocorre através da UDP, mas o plano de controlo é alcançado através do TCP:

* Um cliente inicia uma sessão de TCP para o endereço público equilibrado e é direcionado para um DIP específico. O canal é deixado ativo para monitorizar a saúde da ligação.
* Uma nova sessão uDP do mesmo computador cliente é iniciada para o mesmo ponto final público equilibrado em carga. A ligação é direcionada para o mesmo ponto final dip que a ligação TCP anterior. O upload do meio de dados pode ser executado em alta entrada, mantendo um canal de controlo através do TCP.

> [!NOTE]
> Quando um conjunto equilibrado em carga muda removendo ou adicionando uma máquina virtual, a distribuição dos pedidos do cliente é recomputada. Não pode depender de novas ligações de clientes existentes para acabar no mesmo servidor. Além disso, a utilização do modo de distribuição de afinidade IP de origem pode causar uma distribuição desigual do tráfego. Os clientes que correm atrás de proxies podem ser vistos como uma aplicação única do cliente.

## <a name="configure-source-ip-affinity-settings"></a>Configurar definições de afinidade IP de origem

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

Para máquinas virtuais clássicas, utilize o Azure PowerShell para alterar as definições de distribuição. Adicione um ponto final Azure a uma máquina virtual e configure o modo de distribuição do equilíbrio de carga:

```azurepowershell-interactive
Get-AzureVM -ServiceName mySvc -Name MyVM1 | Add-AzureEndpoint -Name HttpIn -Protocol TCP -PublicPort 80 -LocalPort 8080 –LoadBalancerDistribution sourceIP | Update-AzureVM
```

Detete o `LoadBalancerDistribution` valor do elemento para a quantidade de equilíbrio de carga necessária. Especifique o equilíbrio de carga de duas tuple (IP de origem e IP de destino). Especifique o protocolo de origem IPProtocol para o equilíbrio de carga de três tuple (IP de origem, ip de destino e tipo de protocolo). Especifique nenhum para o comportamento padrão do equilíbrio de carga de cinco tuplés.

Recupere uma configuração do modo de distribuição do equilíbrio de carga de ponto final utilizando estas definições:

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

Quando `LoadBalancerDistribution` o elemento não está presente, o Azure Load Balancer usa o algoritmo padrão de cinco tuples.

### <a name="configure-distribution-mode-on-load-balanced-endpoint-set"></a>Configure o modo de distribuição no conjunto de pontofinal equilibrado em carga

Quando os pontos finais fazem parte de um conjunto de pontofinal equilibrado, o modo de distribuição deve ser configurado no conjunto de pontofinal equilibrado:

```azurepowershell-interactive
Set-AzureLoadBalancedEndpoint -ServiceName MyService -LBSetName LBSet1 -Protocol TCP -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 –LoadBalancerDistribution sourceIP
```

### <a name="configure-distribution-mode-for-cloud-services-endpoints"></a>Configure o modo de distribuição para pontos finais dos Serviços cloud

Utilize o Azure SDK para .NET 2.5 para atualizar o seu serviço na nuvem. As definições de ponto final para serviços de nuvem são feitas no ficheiro .csdef. Para atualizar o modo de distribuição do equilíbrio de carga para uma implementação dos Serviços cloud, é necessária uma atualização de implementação.

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

O exemplo seguinte mostra como reconfigurar o modo de distribuição do equilíbrio de carga para um conjunto determinado equilibrado em carga numa implantação. 

### <a name="change-distribution-mode-for-deployed-load-balanced-set"></a>Alterar o modo de distribuição para conjunto equilibrado em carga

Utilize o modelo de implementação clássico do Azure para alterar uma configuração de implementação existente. Adicione `x-ms-version` o cabeçalho e detetete o valor para a versão 2014-09-01 ou posterior.

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

Como descrito anteriormente, `LoadBalancerDistribution` deset o elemento para fonte IP para afinidade de dois tuple, sourceIPProtocol para afinidade de três tuple, ou nenhum para nenhuma afinidade (afinidade de cinco tuple).

#### <a name="response"></a>Resposta

    HTTP/1.1 202 Accepted
    Cache-Control: no-cache
    Content-Length: 0
    Server: 1.0.6198.146 (rd_rdfe_stable.141015-1306) Microsoft-HTTPAPI/2.0
    x-ms-servedbyregion: ussouth2
    x-ms-request-id: 9c7bda3e67c621a6b57096323069f7af
    Date: Thu, 16 Oct 2014 22:49:21 GMT

## <a name="next-steps"></a>Passos seguintes

* [Visão geral do equilíbrio de carga interna azure](load-balancer-internal-overview.md)
* [Começar com a configuração de um equilibrador de carga virado para a Internet](quickstart-create-standard-load-balancer-powershell.md)
* [Configurar definições de tempo limite TCP inativo para o balanceador de carga](load-balancer-tcp-idle-timeout.md)
