---
title: Implemente o Azure Data Explorer na sua Rede Virtual
description: Saiba como implantar o Azure Data Explorer na sua Rede Virtual
author: basaba
ms.author: basaba
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/31/2019
ms.openlocfilehash: c1ad8390bc5db72636c637c2ffb817e34674d0fa
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548853"
---
# <a name="deploy-azure-data-explorer-cluster-into-your-virtual-network"></a>Implemente o cluster Azure Data Explorer na sua Rede Virtual

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

* `[clustername].[geo-region].kusto.windows.net`(motor) `ingest-[clustername].[geo-region].kusto.windows.net` (gestão de dados) são mapeados para o IP público para cada serviço. 

* `private-[clustername].[geo-region].kusto.windows.net`(motor) `private-ingest-[clustername].[geo-region].kusto.windows.net` (gestão de dados) são mapeados para o IP privado para cada serviço.

## <a name="plan-subnet-size-in-your-vnet"></a>Planeie o tamanho da subnet no seu VNet

O tamanho da sub-rede utilizada para alojar um cluster do Azure Data Explorer não pode ser alterado após a implantação da subneta. No seu VNet, o Azure Data Explorer utiliza um endereço IP privado para cada VM e dois endereços IP privados para os equilibradores internos de carga (gestão de motores e dados). A rede Azure também utiliza cinco endereços IP para cada sub-rede. O Azure Data Explorer disponibiliza dois VMs para o serviço de gestão de dados. Os VMs do serviço de motor são provisionados por capacidade de escala de configuração do utilizador.

O número total de endereços IP:

| Utilizar | Número de endereços |
| --- | --- |
| Serviço de motor | 1 por instância |
| Serviço de gestão de dados | 2 |
| Balanceadores de carga internos | 2 |
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

| **Usar**   | **From**   | **Para**   | **Protocolo**   |
| --- | --- | --- | --- |
| Gestão  |[Endereços de gestão ADX](#azure-data-explorer-management-ip-addresses)/AzureDataExplorerManagement(ServiceTag) | Sub-rede ADX:443  | TCP  |
| Monitorização do estado de funcionamento  | [Endereços de monitorização da saúde ADX](#health-monitoring-addresses)  | Sub-rede ADX:443  | TCP  |
| Comunicação interna ADX  | Subnet ADX: Todas as portas  | Sub-rede ADX:Todas as portas  | Todos  |
| Permitir a entrada do equilibrador de carga Azure (sonda de saúde)  | AzureLoadBalancer  | Subnet ADX:80.443  | TCP  |

#### <a name="outbound-nsg-configuration"></a>Configuração de NSG de saída

| **Usar**   | **From**   | **Para**   | **Protocolo**   |
| --- | --- | --- | --- |
| Dependência do armazenamento azure  | Subnet ADX  | Armazenamento:443  | TCP  |
| Dependência do Lago de Dados Azure  | Subnet ADX  | AzureDataLake:443  | TCP  |
| Ingestão e monitorização de serviços eventHub  | Subnet ADX  | EventHub:443.5671  | TCP  |
| Publicar Métricas  | Subnet ADX  | AzureMonitor:443 | TCP  |
| Download de configuração do Monitor Azure  | Subnet ADX  | [Endereços finais de configuração do Monitor Azure](#azure-monitor-configuration-endpoint-addresses):443 | TCP  |
| Diretório Ativo (se aplicável) | Subnet ADX | AzureActiveDirectory:443 | TCP |
| Autoridade de certificação | Subnet ADX | Internet:80 | TCP |
| Comunicação interna  | Subnet ADX  | Subnet ADX:Todas as portas  | Todos  |
| Portas que são `sql\_request` usadas `http\_request` e plugins  | Subnet ADX  | Internet:Personalizado  | TCP  |

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
| Europa ocidental | 51.145.176.215 |
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
| Europa ocidental | 23.97.212.5 |
| Oeste da Índia | 23.99.5.162 |
| E.U.A. Oeste | 23.99.5.162 |
| E.U.A.Oeste 2 | 23.99.5.162, 104.210.32.14 |    

#### <a name="azure-monitor-configuration-endpoint-addresses"></a>Endereços finais de configuração do Monitor Azure

| Região | Endereços |
| --- | --- |
| Austrália Central | 52.148.86.165 |
| Austrália Central 2 | 52.148.86.165 |
| Leste da Austrália | 52.148.86.165 |
| Austrália Sudeste | 52.148.86.165 |
| Sul do Brasil | 13.68.89.19 |
| Canadá Central | 13.90.43.231 |
| Leste do Canadá | 13.90.43.231 |
| Índia Central | 13.71.25.187 |
| E.U.A. Central | 52.173.95.68 |
| EUA Centrais EUA | 13.90.43.231 |
| Ásia Oriental | 13.75.117.221 |
| E.U.A. Leste | 13.90.43.231 |
| E.U.A. Leste 2 | 13.68.89.19 |    
| LESTE DOS EUA 2 EUAP | 13.68.89.19 |
| França Central | 52.174.4.112 |
| Sul de França | 52.174.4.112 |
| Leste do Japão | 13.75.117.221 |
| Oeste do Japão | 13.75.117.221 |
| Coreia do Sul Central | 13.75.117.221 |
| Sul da Coreia do Sul | 13.75.117.221 |
| E.U.A. Centro-Norte | 52.162.240.236 |
| Europa do Norte | 52.169.237.246 |
| África do Sul Norte | 13.71.25.187 |
| África do Sul Ocidental | 13.71.25.187 |
| Centro-Sul dos EUA | 13.84.173.99 |
| Sul da Índia | 13.71.25.187 |
| Ásia Sudeste | 52.148.86.165 |
| Sul do Reino Unido | 52.174.4.112 |
| Oeste do Reino Unido | 52.169.237.246 |
| E.U.A. Centro-Oeste | 52.161.31.69 |
| Europa ocidental | 52.174.4.112 |
| Oeste da Índia | 13.71.25.187 |
| E.U.A. Oeste | 40.78.70.148 |
| E.U.A.Oeste 2 | 52.151.20.103 |

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
