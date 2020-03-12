---
title: Implemente o Azure Data Explorer na sua Rede Virtual
description: Saiba como implantar o Azure Data Explorer na sua Rede Virtual
author: basaba
ms.author: basaba
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/31/2019
ms.openlocfilehash: 5a2731e26ba4f371177cf2ae649f0695f27e6304
ms.sourcegitcommit: be53e74cd24bbabfd34597d0dcb5b31d5e7659de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2020
ms.locfileid: "79096763"
---
# <a name="deploy-azure-data-explorer-into-your-virtual-network"></a>Implemente o Azure Data Explorer na sua Rede Virtual

Este artigo explica os recursos que estão presentes quando se implanta um cluster Azure Data Explorer numa Rede Virtual Azure personalizada. Esta informação irá ajudá-lo a implantar um cluster numa subrede na sua Rede Virtual (VNet). Para obter mais informações sobre redes virtuais Azure, consulte o que é a [Rede Virtual Azure?](/azure/virtual-network/virtual-networks-overview)

   ![diagrama vnet](media/vnet-deployment/vnet-diagram.png)

O Azure Data Explorer suporta a implementação de um cluster numa subrede na sua Rede Virtual (VNet). Esta capacidade permite::

* Aplique as regras do Grupo de Segurança da [Rede](/azure/virtual-network/security-overview) (NSG) sobre o tráfego de cluster do Azure Data Explorer.
* Ligue a sua rede no local à subnet do cluster Azure Data Explorer.
* Proteja as suas fontes de ligação de dados ( Hub de[Eventos](/azure/event-hubs/event-hubs-about) e Grelha de [Eventos)](/azure/event-grid/overview)com [pontos finais](/azure/virtual-network/virtual-network-service-endpoints-overview)de serviço .

## <a name="access-your-azure-data-explorer-cluster-in-your-vnet"></a>Aceda ao seu cluster Azure Data Explorer no seu VNet

Pode aceder ao seu cluster Azure Data Explorer utilizando os seguintes endereços IP para cada serviço (serviços de gestão de motores e dados):

* **IP privado**: Utilizado para aceder ao cluster dentro do VNet.
* **IP público**: Utilizado para aceder ao cluster de fora da VNet para gestão e monitorização, e como endereço de origem para ligações de saída iniciadas a partir do cluster.

São criados os seguintes registos DNS para aceder ao serviço: 

* `[clustername].[geo-region].kusto.windows.net` (motor) `ingest-[clustername].[geo-region].kusto.windows.net` (gestão de dados) são mapeados para o IP público para cada serviço. 

* `private-[clustername].[geo-region].kusto.windows.net` (motor) `private-ingest-[clustername].[geo-region].kusto.windows.net` (gestão de dados) estão mapeados para o IP privado para cada serviço.

## <a name="plan-subnet-size-in-your-vnet"></a>Planeie o tamanho da subnet no seu VNet

O tamanho da sub-rede utilizada para alojar um cluster do Azure Data Explorer não pode ser alterado após a implantação da subneta. No seu VNet, o Azure Data Explorer utiliza um endereço IP privado para cada VM e dois endereços IP privados para os equilibradores internos de carga (gestão de motores e dados). A rede Azure também utiliza cinco endereços IP para cada sub-rede. O Azure Data Explorer disponibiliza dois VMs para o serviço de gestão de dados. Os VMs do serviço de motor são provisionados por capacidade de escala de configuração do utilizador.

O número total de endereços IP:

| Utilização | Número de endereços |
| --- | --- |
| Serviço de motor | 1 por instância |
| Serviço de gestão de dados | 2 |
| Equilibradores de carga internos | 2 |
| Endereços reservados azure | 5 |
| **Total** | **#engine_instances + 9** |

> [!IMPORTANT]
> O tamanho da subrede deve ser planeado com antecedência, uma vez que não pode ser alterado após a implantação do Azure Data Explorer. Portanto, a reserva precisava do tamanho da subnet em conformidade.

## <a name="service-endpoints-for-connecting-to-azure-data-explorer"></a>Pontos finais de serviço para ligação ao Azure Data Explorer

[Os Pontos Finais do Serviço Azure](/azure/virtual-network/virtual-network-service-endpoints-overview) permitem-lhe assegurar os seus recursos multi-inquilinos Azure à sua rede virtual.
A implementação do cluster Azure Data Explorer na sua subnet permite-lhe configurar ligações de dados com [o Event Hub](/azure/event-hubs/event-hubs-about) ou a Event [Grid,](/azure/event-grid/overview) limitando os recursos subjacentes à subnet do Azure Data Explorer.

> [!NOTE]
> Ao utilizar a configuração eventGrid com [armazenamento](/azure/storage/common/storage-introduction) e [Event Hub], a conta de armazenamento utilizada na subscrição pode ser bloqueada com pontos finais de serviço para a subnet do Azure Data Explorer, permitindo serviços de plataforma Azure fidedignos na [configuração](/azure/storage/common/storage-network-security)da firewall, mas o Event Hub não consegue ativar o Service Endpoint uma vez que não suporta serviços de plataforma Azure fidedignos. [Azure platform services](/azure/event-hubs/event-hubs-service-endpoints)

## <a name="dependencies-for-vnet-deployment"></a>Dependências para implantação vnet

### <a name="network-security-groups-configuration"></a>Configuração de Grupos de Segurança de Rede

[Os Grupos de Segurança da Rede (NSG)](/azure/virtual-network/security-overview) fornecem a capacidade de controlar o acesso à rede dentro de um VNet. O Azure Data Explorer pode ser acedido com dois pontos finais: HTTPs (443) e TDS (1433). As seguintes regras de NSG devem ser configuradas para permitir o acesso a estes pontos finais para gestão, monitorização e bom funcionamento do seu cluster.

#### <a name="inbound-nsg-configuration"></a>Configuração NSG de entrada

| **Utilizar**   | **De**   | **Para**   | **Protocolo**   |
| --- | --- | --- | --- |
| Gestão  |[Endereços de gestão ADX](#azure-data-explorer-management-ip-addresses)/AzureDataExplorerManagement(ServiceTag) | Sub-rede ADX:443  | TCP  |
| Monitorização do estado de funcionamento  | [Endereços de monitorização da saúde ADX](#health-monitoring-addresses)  | Sub-rede ADX:443  | TCP  |
| Comunicação interna ADX  | Subnet ADX: Todas as portas  | Sub-rede ADX:Todas as portas  | Todos  |
| Permitir a entrada do equilibrador de carga Azure (sonda de saúde)  | AzureLoadBalancer  | Subnet ADX:80.443  | TCP  |

#### <a name="outbound-nsg-configuration"></a>Configuração de NSG de saída

| **Utilizar**   | **De**   | **Para**   | **Protocolo**   |
| --- | --- | --- | --- |
| Dependência do armazenamento azure  | Subnet ADX  | Armazenamento:443  | TCP  |
| Dependência do Lago de Dados Azure  | Subnet ADX  | AzureDataLake:443  | TCP  |
| Ingestão e monitorização de serviços eventHub  | Subnet ADX  | EventHub:443.5671  | TCP  |
| Publicar Métricas  | Subnet ADX  | AzureMonitor:443 | TCP  |
| Download de configuração do Monitor Azure  | Subnet ADX  | [Endereços finais de configuração do Monitor Azure](#azure-monitor-configuration-endpoint-addresses):443 | TCP  |
| Diretório Ativo (se aplicável) | Subnet ADX | AzureActiveDirectory:443 | TCP |
| Autoridade de certificação | Subnet ADX | Internet:80 | TCP |
| Comunicação interna  | Subnet ADX  | Subnet ADX:Todas as portas  | Todos  |
| Portas que são utilizadas para plugins `sql\_request` e `http\_request`  | Subnet ADX  | Internet:Personalizado  | TCP  |

### <a name="relevant-ip-addresses"></a>Endereços IP relevantes

#### <a name="azure-data-explorer-management-ip-addresses"></a>Endereços IP de gestão de dados Azure Data Explorer

| Região | Endereços |
| --- | --- |
| Austrália Central | 20.37.26.134 |
| Austrália Central2 | 20.39.99.177 |
| Leste da Austrália | 40.82.217.84 |
| Austrália Sudeste | 20.40.161.39 |
| BrasilSul | 191.233.25.183 |
| Canadá Central | 40.82.188.208 |
| Leste do Canadá | 40.80.255.12 |
| Índia Central | 40.81.249.251 |
| E.U.A. Central | 40.67.188.68 |
| EUA Centrais EUA | 40.89.56.69 |
| Ásia Leste | 20.189.74.103 |
| E.U.A. Leste | 52.224.146.56 |
| E.U.A. Leste 2 | 52.232.230.201 |
| LESTE US2 EUAP | 52.253.226.110 |
| França Central | 40.66.57.91 |
| Sul de França | 40.82.236.24 |
| Leste do Japão | 20.43.89.90 |
| Oeste do Japão | 40.81.184.86 |
| Coreia do Sul Central | 40.82.156.149 |
| Sul da Coreia do Sul | 40.80.234.9 |
| E.U.A. Centro-Norte | 40.81.45.254 |
| Europa do Norte | 52.142.91.221 |
| África do Sul Norte | 102.133.129.138 |
| África do Sul Ocidental | 102.133.0.97 |
| E.U.A. Centro-Sul | 20.45.3.60 |
| Ásia Sudeste | 40.119.203.252 |
| Sul da Índia | 40.81.72.110 |
| Sul do Reino Unido | 40.81.154.254 |
| Oeste do Reino Unido | 40.81.122.39 |
| E.U.A. Centro-Oeste | 52.159.55.120 |
| Europa Ocidental | 51.145.176.215 |
| Oeste da Índia | 40.81.88.112 |
| E.U.A. Oeste | 13.64.38.225 |
| E.U.A. Oeste 2 | 40.90.219.23 |

#### <a name="health-monitoring-addresses"></a>Endereços de monitorização da saúde

| Região | Endereços |
| --- | --- |
| Austrália Central | 191.239.64.128 |
| Austrália Central 2 | 191.239.64.128 |
| Leste da Austrália | 191.239.64.128 |
| Austrália Sudeste | 191.239.160.47 |
| Sul do Brasil | 23.98.145.105 |
| Canadá Central | 168.61.212.201 |
| Leste do Canadá | 168.61.212.201 |
| Índia Central | 23.99.5.162 |
| E.U.A. Central | 168.61.212.201 |
| EUA Centrais EUA | 168.61.212.201 |
| Ásia Leste | 168.63.212.33 |
| E.U.A. Leste | 137.116.81.189 |
| E.U.A. Leste 2 | 137.116.81.189 |
| LESTE DOS EUA 2 EUAP | 137.116.81.189 |
| França Central | 23.97.212.5 |
| Sul de França | 23.97.212.5 |
| Leste do Japão | 138.91.19.129 |
| Oeste do Japão | 138.91.19.129 |
| Coreia do Sul Central | 138.91.19.129 |
| Sul da Coreia do Sul | 138.91.19.129 |
| E.U.A. Centro-Norte | 23.96.212.108 |
| Europa do Norte | 191.235.212.69 
| África do Sul Norte | 104.211.224.189 |
| África do Sul Ocidental | 104.211.224.189 |
| E.U.A. Centro-Sul | 23.98.145.105 |
| Sul da Índia | 23.99.5.162 |
| Ásia Sudeste | 168.63.173.234 |
| Sul do Reino Unido | 23.97.212.5 |
| Oeste do Reino Unido | 23.97.212.5 |
| E.U.A. Centro-Oeste | 168.61.212.201 |
| Europa Ocidental | 23.97.212.5 |
| Oeste da Índia | 23.99.5.162 |
| E.U.A. Oeste | 23.99.5.162 |
| E.U.A.Oeste 2 | 23.99.5.162 |    

#### <a name="azure-monitor-configuration-endpoint-addresses"></a>Endereços finais de configuração do Monitor Azure

| Região | Endereços |
| --- | --- |
| Austrália Central | 52.148.86.165 |
| Austrália Central 2 | 52.148.86.165 |
| Austrália Leste | 52.148.86.165 |
| Austrália Sudeste | 52.148.86.165 |
| Brasil Sul | 13.68.89.19 |
| Central do Canadá | 13.90.43.231 |
| Canadá Leste | 13.90.43.231 |
| Índia Central | 13.71.25.187 |
| Centro dos EUA | 52.173.95.68 |
| EUA Centrais EUA | 13.90.43.231 |
| Ásia Oriental | 13.75.117.221 |
| Leste dos EUA | 13.90.43.231 |
| Leste dos EUA 2 | 13.68.89.19 |    
| LESTE DOS EUA 2 EUAP | 13.68.89.19 |
| França Central | 52.174.4.112 |
| França Sul | 52.174.4.112 |
| Japão Leste | 13.75.117.221 |
| Oeste do Japão | 13.75.117.221 |
| Coreia Central | 13.75.117.221 |
| Coreia do Sul | 13.75.117.221 |
| Centro-Norte dos EUA | 52.162.240.236 |
| Norte da Europa | 52.169.237.246 |
| África do Sul Norte | 13.71.25.187 |
| África do Sul Ocidental | 13.71.25.187 |
| Centro-Sul dos EUA | 13.84.173.99 |
| Índia do Sul | 13.71.25.187 |
| Sudeste Asiático | 52.148.86.165 |
| Reino Unido Sul | 52.174.4.112 |
| Reino Unido Oeste | 52.169.237.246 |
| E.U.A. Centro-Oeste | 52.161.31.69 |
| Europa Ocidental | 52.174.4.112 |
| Índia Ocidental | 13.71.25.187 |
| Oeste dos EUA | 40.78.70.148 |
| Oeste dos EUA 2 | 52.151.20.103 |

## <a name="expressroute-setup"></a>Configuração ExpressRoute

Utilize o ExpressRoute para ligar na rede de instalações à Rede Virtual Azure. Uma configuração comum é anunciar a rota padrão (0.0.0.0/0) através da sessão do Protocolo de Gateway de Fronteira (BGP). Isto obriga a que o tráfego que sai da Rede Virtual seja encaminhado para a rede de instalação do cliente que pode deixar cair o tráfego, fazendo com que os fluxos de saída se rompam. Para ultrapassar este padrão, a Rota Definida pelo [Utilizador (UDR)](/azure/virtual-network/virtual-networks-udr-overview#user-defined) (0.0.0.0/0) pode ser configurada e o próximo salto será a *Internet*. Uma vez que a UDR tem precedência sobre o BGP, o tráfego será destinado à Internet.

## <a name="securing-outbound-traffic-with-firewall"></a>Garantir o tráfego de saída com firewall

Se pretender assegurar o tráfego de saída utilizando [o Firewall Azure](/azure/firewall/overview) ou qualquer aparelho virtual para limitar os nomes de domínio, devem ser permitidos na firewall os seguintes nomes de domínio totalmente qualificados (FQDN).

```
prod.warmpath.msftcloudes.com:443
production.diagnostics.monitoring.core.windows.net:443
graph.windows.net:443
*.update.microsoft.com:443
shavamanifestcdnprod1.azureedge.net:443
login.live.com:443
wdcp.microsoft.com:443
login.microsoftonline.com:443
azureprofilerfrontdoor.cloudapp.net:443
*.core.windows.net:443
*.servicebus.windows.net:443
shoebox2.metrics.nsatc.net:443
prod-dsts.dsts.core.windows.net:443
ocsp.msocsp.com:80
*.windowsupdate.com:80
ocsp.digicert.com:80
go.microsoft.com:80
dmd.metaservices.microsoft.com:80
www.msftconnecttest.com:80
crl.microsoft.com:80
www.microsoft.com:80
adl.windows.com:80
crl3.digicert.com:80
```

Também é necessário definir a tabela de [rotas](/azure/virtual-network/virtual-networks-udr-overview) na subnet com os [endereços](#azure-data-explorer-management-ip-addresses) de gestão e os endereços de [monitorização da saúde](#health-monitoring-addresses) com a próxima *Internet* hop para prevenir problemas de rotas assimétricas.

Por exemplo, para a região **dos EUA Ocidentais,** devem ser definidos os seguintes UDRs:

| Nome | Prefixo do endereço | Próximo Hop |
| --- | --- | --- |
| ADX_Management | 13.64.38.225/32 | Internet |
| ADX_Monitoring | 23.99.5.162/32 | Internet |

## <a name="deploy-azure-data-explorer-cluster-into-your-vnet-using-an-azure-resource-manager-template"></a>Implemente o cluster Azure Data Explorer no seu VNet usando um modelo de Gestor de Recursos Azure

Para implantar o cluster Do Explorador de Dados Azure na sua rede virtual, utilize o [cluster Deploy Azure Data Explorer no seu](https://azure.microsoft.com/resources/templates/101-kusto-vnet/) modelo vNet Azure Resource Manager.

Este modelo cria o cluster, rede virtual, subnet, grupo de segurança de rede e endereços IP públicos.

## <a name="troubleshooting"></a>Resolução de problemas

Nesta secção aprende-se a resolver problemas de conectividade, operacional e de criação de clusters para um cluster que é implantado na sua [Rede Virtual.](/azure/virtual-network/virtual-networks-overview)

### <a name="access-issues"></a>Questões de acesso

Se tiver algum problema ao aceder ao cluster utilizando o ponto final público (cluster.region.kusto.windows.net) ou privado (private-cluster.region.kusto.windows.net) e suspeitar que está relacionado com a configuração da rede virtual, execute os seguintes passos para problemas resolver a questão.

#### <a name="check-tcp-connectivity"></a>Verifique a conectividade TCP

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

#### <a name="check-the-network-security-group-nsg"></a>Consulte o Grupo de Segurança da Rede (NSG)

   Verifique se o Grupo de Segurança da [Rede](/azure/virtual-network/security-overview) (NSG) ligado à subnet do cluster, tem uma regra de entrada que permite o acesso a partir do IP da máquina cliente para a porta 443.

#### <a name="check-route-table"></a>Ver tabela de rotas

   Se a sub-rede do cluster tiver uma configuração de túnel de força para firewall (subnet com uma tabela de [rota](/azure/virtual-network/virtual-networks-udr-overview) que contenha a rota padrão '0.0.0.0/0'), certifique-se de que o endereço IP da máquina tem uma rota com o próximo tipo de [lúpulo](/azure/virtual-network/virtual-networks-udr-overview) para virtualNetwork/Internet. Isto é necessário para evitar problemas de rotas assimétricas.

### <a name="ingestion-issues"></a>Problemas de ingestão

Se estiver com problemas de ingestão e suspeitar que está relacionado com a configuração da rede virtual, execute os seguintes passos.

#### <a name="check-ingestion-health"></a>Verificar a saúde da ingestão

    Check that the [cluster ingestion metrics](/azure/data-explorer/using-metrics#ingestion-health-and-performance-metrics) indicate a healthy state.

#### <a name="check-security-rules-on-data-source-resources"></a>Verifique as regras de segurança dos recursos de origem de dados

Se as métricas indicarem que nenhum evento foi processado a partir da métrica de origem de dados (*Eventos processados* (para Hubs de Evento/IoT), certifique-se de que os recursos de origem de dados (Event Hub ou Storage) permitem o acesso a partir da subnet do cluster nas regras de firewall ou pontos finais de serviço.

#### <a name="check-security-rules-configured-on-clusters-subnet"></a>Verifique as regras de segurança configuradas na subnet do cluster

Certifique-se de que a subnet do cluster tem regras nsg, UDR e firewall corretamente configuradas. Além disso, testar a conectividade da rede para todos os pontos finais dependentes. 

### <a name="cluster-creation-and-operations-issues"></a>Problemas de criação de clusters e operações

Se estiver a experimentar problemas de criação de clusters ou de operação e suspeitar que está relacionado com a configuração da rede virtual, siga estes passos para resolver o problema.

#### <a name="diagnose-the-virtual-network-with-the-rest-api"></a>Diagnosticar a rede virtual com a API REST

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

Se a propriedade *Deteção* mostrar um resultado vazio, significa que todos os testes de rede passaram e nenhuma ligação é quebrada. Se mostrar um erro do seguinte modo: Dependência *de saída '{dependencyName}:{porta}' pode não estar satisfeito (Outbound)* , o cluster não pode alcançar os pontos finais do serviço dependente. Proceda com os seguintes passos para resolver problemas.

#### <a name="check-network-security-group-nsg"></a>Verificar O Grupo de Segurança da Rede (NSG)

Certifique-se de que o Grupo de Segurança da [Rede](/azure/virtual-network/security-overview) está configurado corretamente de acordo com as instruções em [Dependências para a implantação vnet](/azure/data-explorer/vnet-deployment#dependencies-for-vnet-deployment)

#### <a name="check-route-table"></a>Ver tabela de rotas

Se a sub-rede do cluster tiver um túnel de força configurado para firewall (sub-rede com uma tabela de [rotas](/azure/virtual-network/virtual-networks-udr-overview) que contenha a rota padrão '0.0.0.0/0') certifique-se de que os [endereços IP](#azure-data-explorer-management-ip-addresses) de gestão e os [endereços IP](#health-monitoring-addresses) de monitorização da saúde têm uma rota com internet [tipo de lúpulo seguinte](/azure/virtual-network/virtual-networks-udr-overview##next-hop-types-across-azure-tools) *,* e [prefixo](/azure/virtual-network/virtual-networks-udr-overview#how-azure-selects-a-route) de endereço de origem para *"management-ip/32"* e *"health-monitoring-ip/32".* Isto é necessário para evitar problemas de rotas assimétricas.

#### <a name="check-firewall-rules"></a>Verifique as regras da firewall

Se forçar o tráfego de saída da sub-rede de túneis a uma firewall, certifique-se de que todas as dependências FQDN (por exemplo, *.blob.core.windows.net*) são permitidas na configuração da firewall, tal como descrito na [fixação](/azure/data-explorer/vnet-deployment#securing-outbound-traffic-with-firewall)do tráfego de saída com firewall .
