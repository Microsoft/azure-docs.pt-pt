---
title: Acesso, ingestão e operação do seu cluster Azure Data Explorer na sua rede virtual
description: Conectividade, ingestão, criação de clustere e operação do seu cluster Azure Data Explorer na sua rede virtual
author: basaba
ms.author: basaba
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/24/2020
ms.openlocfilehash: b50b971a3b1980ad35a1a939bdf25f1c9e6ac7ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241663"
---
# <a name="troubleshoot-access-ingestion-and-operation-of-your-azure-data-explorer-cluster-in-your-virtual-network"></a>Acesso, ingestão e operação do seu cluster Azure Data Explorer na sua rede virtual

Nesta secção aprende-se a resolver problemas de conectividade, operacional e de criação de clusters para um cluster que é implantado na sua [Rede Virtual.](/azure/virtual-network/virtual-networks-overview)

## <a name="access-issues"></a>Questões de acesso

Se tiver algum problema ao aceder ao cluster utilizando o ponto final público (cluster.region.kusto.windows.net) ou privado (private-cluster.region.kusto.windows.net) e suspeitar que está relacionado com a configuração da rede virtual, execute os seguintes passos para problemas resolver a questão.

### <a name="check-tcp-connectivity"></a>Verifique a conectividade TCP

O primeiro passo inclui a verificação da conectividade TCP utilizando o Windows ou o Sistema Linux.

# <a name="windows"></a>[Windows](#tab/windows)

   1. Baixe o [TCping](https://www.elifulkerson.com/projects/tcping.php) para a máquina que liga ao cluster.
   2. Ping o destino da máquina de origem utilizando o seguinte comando:

    ```cmd
     C:\> tcping -t yourcluster.kusto.windows.net 443 
    
     ** Pinging continuously.  Press control-c to stop **
    
     Probing 1.2.3.4:443/tcp - Port is open - time=100.00ms
     ```

# <a name="linux"></a>[Linux](#tab/linux)

   1. Instale o *netcat* na máquina que liga ao cluster

    ```bash
    $ apt-get install netcat
     ```

   2. Ping o destino da máquina de origem utilizando o seguinte comando:

     ```bash
     $ netcat -z -v yourcluster.kusto.windows.net 443
    
     Connection to yourcluster.kusto.windows.net 443 port [tcp/https] succeeded!
     ```
---

Se o teste não for bem sucedido, proceda aos seguintes passos. Se o teste for bem sucedido, o problema não se deve a um problema de conectividade TCP. Vá a [questões operacionais](#cluster-creation-and-operations-issues) para resolver ainda mais os problemas.

### <a name="check-the-network-security-group-nsg"></a>Consulte o Grupo de Segurança da Rede (NSG)

   Verifique se o Grupo de Segurança da [Rede](/azure/virtual-network/security-overview) (NSG) ligado à subnet do cluster, tem uma regra de entrada que permite o acesso a partir do IP da máquina cliente para a porta 443.

### <a name="check-route-table"></a>Ver tabela de rotas

   Se a sub-rede do cluster tiver uma configuração de túnel de força para firewall (subnet com uma tabela de [rota](/azure/virtual-network/virtual-networks-udr-overview) que contenha a rota padrão '0.0.0.0/0'), certifique-se de que o endereço IP da máquina tem uma rota com o próximo tipo de [lúpulo](/azure/virtual-network/virtual-networks-udr-overview) para virtualNetwork/Internet. Esta rota é necessária para evitar problemas de rotas assimétricas.

## <a name="ingestion-issues"></a>Problemas de ingestão

Se estiver com problemas de ingestão e suspeitar que está relacionado com a configuração da rede virtual, execute os seguintes passos.

### <a name="check-ingestion-health"></a>Verificar a saúde da ingestão

Verifique se as métricas de [ingestão](/azure/data-explorer/using-metrics#ingestion-health-and-performance-metrics) de cluster indicam um estado saudável.

### <a name="check-security-rules-on-data-source-resources"></a>Verifique as regras de segurança dos recursos de origem de dados

Se as métricas indicarem que nenhum evento foi processado a partir da fonte de dados (*Events processou* métrica para Hubs de Evento/IoT), certifique-se de que os recursos de origem de dados (Event Hub ou Storage) permitem o acesso a partir da subnet do cluster nas regras de firewall ou pontos finais de serviço.

### <a name="check-security-rules-configured-on-clusters-subnet"></a>Verifique as regras de segurança configuradas na subnet do cluster

Certifique-se de que a subnet do cluster tem regras nsg, UDR e firewall corretamente configuradas. Além disso, testar a conectividade da rede para todos os pontos finais dependentes. 

## <a name="cluster-creation-and-operations-issues"></a>Problemas de criação de clusters e operações

Se estiver a experimentar problemas de criação de clusters ou de operação e suspeitar que está relacionado com a configuração da rede virtual, siga estes passos para resolver o problema.

### <a name="diagnose-the-virtual-network-with-the-rest-api"></a>Diagnosticar a rede virtual com a API REST

O [ARMClient](https://chocolatey.org/packages/ARMClient) é usado para chamar o REST API usando powerShell. 

1. Iniciar sessão com a ARMClient

   ```powerShell
   armclient login
   ```

1. Invocar operação de diagnóstico

    ```powershell
    $subscriptionId = '<subscription id>'
    $clusterName = '<name of cluster>'
    $resourceGroupName = '<resource group name>'
    $apiversion = '2019-11-09'
    
    armclient post "https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Kusto/clusters/$clusterName/diagnoseVirtualNetwork?api-version=$apiversion" -verbose
    ```

1. Verifique a resposta

    ```powershell
    HTTP/1.1 202 Accepted
    ...
    Azure-AsyncOperation: https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Kusto/locations/{location}/operationResults/{operation-id}?api-version=2019-11-09
    ...
    ```

1. Aguarde a conclusão da operação

    ```powershell
    armclient get https://management.azure.com/subscriptions/$subscriptionId/providers/Microsoft.Kusto/locations/{location}/operationResults/{operation-id}?api-version=2019-11-09
    
    {
      "id": "/subscriptions/{subscription-id}/providers/Microsoft.Kusto/locations/{location}/operationresults/{operation-id}",
      "name": "{operation-name}",
      "status": "[Running/Failed/Completed]",
      "startTime": "{start-time}",
      "endTime": "{end-time}",
      "properties": {...}
    }
    ```
    
   Aguarde até que a propriedade do *estado* mostre *concluído,* então o campo *de propriedades* deve mostrar:

    ```powershell
    {
      "id": "/subscriptions/{subscription-id}/providers/Microsoft.Kusto/locations/{location}/operationresults/{operation-id}",
      "name": "{operation-name}",
      "status": "Completed",
      "startTime": "{start-time}",
      "endTime": "{end-time}",
      "properties": {
        "Findings": [...]
      }
    }
    ```

Se a propriedade *Deteção* mostrar um resultado vazio, significa que todos os testes de rede passaram e nenhuma ligação é quebrada. Se o seguinte erro for mostrado, a dependência de *saída '{dependencyName}:{porta}' pode não estar satisfeita (Saída)*, o cluster não pode alcançar os pontos finais do serviço dependente. Proceda com os seguintes passos.

### <a name="check-network-security-group-nsg"></a>Verificar O Grupo de Segurança da Rede (NSG)

Certifique-se de que o Grupo de Segurança da [Rede](/azure/virtual-network/security-overview) está configurado corretamente de acordo com as instruções em [Dependências para a implantação vnet](/azure/data-explorer/vnet-deployment#dependencies-for-vnet-deployment)

### <a name="check-route-table"></a>Ver tabela de rotas

Se a sub-rede do cluster tiver um túnel de força configurado para firewall (sub-rede com uma tabela de [rotas](/azure/virtual-network/virtual-networks-udr-overview) que contenha a rota padrão '0.0.0.0/0') certifique-se de que os [endereços IP](vnet-deployment.md#azure-data-explorer-management-ip-addresses)de gestão ) e os [endereços IP](vnet-deployment.md#health-monitoring-addresses) de monitorização da saúde têm uma rota com internet [tipo de lúpulo seguinte](/azure/virtual-network/virtual-networks-udr-overview##next-hop-types-across-azure-tools) *,* e [prefixo](/azure/virtual-network/virtual-networks-udr-overview#how-azure-selects-a-route) de endereço de origem para *'management-ip/32'* e *'health-monitoring-ip/32'.* Esta rota necessária para evitar problemas de rotas assimétricas.

### <a name="check-firewall-rules"></a>Verifique as regras da firewall

Se forçar o tráfego de saída da sub-rede de túneis a uma firewall, certifique-se de que todas as dependências FQDN (por exemplo, *.blob.core.windows.net*) são permitidas na configuração da firewall, tal como descrito na [fixação](/azure/data-explorer/vnet-deployment#securing-outbound-traffic-with-firewall)do tráfego de saída com firewall .
