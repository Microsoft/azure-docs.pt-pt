---
title: Implantar Data Explorer do Azure em sua rede virtual (versão prévia)
description: Saiba como implantar o Azure Data Explorer em sua rede virtual
author: basaba
ms.author: basaba
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/31/2019
ms.openlocfilehash: 28b9c55df8cd7883e05e964b8b67e08c7a3eb8c1
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74812722"
---
# <a name="deploy-azure-data-explorer-into-your-virtual-network-preview"></a>Implantar Data Explorer do Azure em sua rede virtual (versão prévia)

Este artigo explica os recursos que estão presentes quando você implanta um cluster de Data Explorer do Azure em uma rede virtual do Azure personalizada. Essas informações ajudarão você a implantar um cluster em uma sub-rede em sua rede virtual (VNet). Para obter mais informações sobre redes virtuais do Azure, consulte [o que é a rede virtual do Azure?](/azure/virtual-network/virtual-networks-overview)

   ![diagrama de vnet](media/vnet-deployment/vnet-diagram.png)

O Data Explorer do Azure dá suporte à implantação de um cluster em uma sub-rede em sua rede virtual (VNet). Essa funcionalidade permite que você:

* Impor regras de NSG ( [grupo de segurança de rede](/azure/virtual-network/security-overview) ) no seu tráfego de cluster data Explorer do Azure.
* Conecte sua rede local à sub-rede do cluster Data Explorer do Azure.
* Proteja suas fontes de conexão de dados ([Hub de eventos](/azure/event-hubs/event-hubs-about) e a [grade de eventos](/azure/event-grid/overview)) com pontos de [extremidade de serviço](/azure/virtual-network/virtual-network-service-endpoints-overview).

> [!NOTE]
> A integração e a implantação da rede virtual estão no modo de visualização. Para habilitar esse recurso, abra um [tíquete de suporte](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

## <a name="access-your-azure-data-explorer-cluster-in-your-vnet"></a>Acessar o cluster de Data Explorer do Azure em sua VNet

Você pode acessar o cluster de Data Explorer do Azure usando os seguintes endereços IP para cada serviço (serviços de gerenciamento de dados e de mecanismo):

* **IP privado**: usado para acessar o cluster dentro da VNet.
* **IP público**: usado para acessar o cluster de fora da VNet para gerenciamento e monitoramento, e como um endereço de origem para conexões de saída iniciadas a partir do cluster.

Os seguintes registros DNS são criados para acessar o serviço: 

* `[clustername].[geo-region].kusto.windows.net` (mecanismo) `ingest-[clustername].[geo-region].kusto.windows.net` (gerenciamento de dados) são mapeados para o IP público de cada serviço. 

* `private-[clustername].[geo-region].kusto.windows.net` (mecanismo) `private-ingest-[clustername].[geo-region].kusto.windows.net` (gerenciamento de dados) são mapeados para o IP privado para cada serviço.

## <a name="plan-subnet-size-in-your-vnet"></a>Planejar o tamanho da sub-rede em sua VNet

O tamanho da sub-rede usada para hospedar um cluster de Data Explorer do Azure não pode ser alterado depois que a sub-rede é implantada. Em sua VNet, o Azure Data Explorer usa um endereço IP privado para cada VM e dois endereços IP privados para os balanceadores de carga internos (gerenciamento de dados e de mecanismo). A rede do Azure também usa cinco endereços IP para cada sub-rede. O Azure Data Explorer provisiona duas VMs para o serviço de gerenciamento de dados. As VMs do serviço de mecanismo são provisionadas por capacidade de escala de configuração do usuário.

O número total de endereços IP:

| Usar | Número de endereços |
| --- | --- |
| Serviço de mecanismo | 1 por instância |
| Serviço de gerenciamento de dados | 2 |
| Balanceadores de carga internos | 2 |
| Endereços reservados do Azure | 5 |
| **Total** | **#engine_instances + 9** |

> [!IMPORTANT]
> O tamanho da sub-rede deve ser planejado antecipadamente, uma vez que não pode ser alterado após a implantação do Data Explorer do Azure. Portanto, Reserve o tamanho necessário da sub-rede adequadamente.

## <a name="service-endpoints-for-connecting-to-azure-data-explorer"></a>Pontos de extremidade de serviço para conexão com o Azure Data Explorer

Os [pontos de extremidade de serviço do Azure](/azure/virtual-network/virtual-network-service-endpoints-overview) permitem que você proteja seus recursos multilocatários do Azure para sua rede virtual.
A implantação do cluster Data Explorer do Azure em sua sub-rede permite que você configure conexões de dados com o [Hub de eventos](/azure/event-hubs/event-hubs-about) ou a [grade de eventos](/azure/event-grid/overview) ao restringir os recursos subjacentes para a sub-rede do Azure data Explorer.

> [!NOTE]
> Ao usar a instalação do EventGrid com o [armazenamento](/azure/storage/common/storage-introduction) e o [Hub de eventos], a conta de armazenamento usada na assinatura pode ser bloqueada com pontos de extremidade de serviço para a sub-rede do Azure data Explorer, enquanto permite serviços confiáveis da plataforma Azure na [configuração do firewall](/azure/storage/common/storage-network-security), mas o Hub de eventos não pode habilitar o ponto de extremidade de serviço, pois ele não dá suporte a serviços confiáveis da [plataforma Azure](/azure/event-hubs/event-hubs-service-endpoints)

## <a name="dependencies-for-vnet-deployment"></a>Dependências para implantação de VNet

### <a name="network-security-groups-configuration"></a>Configuração de grupos de segurança de rede

Os [NSG (grupos de segurança de rede)](/azure/virtual-network/security-overview) fornecem a capacidade de controlar o acesso à rede em uma VNet. O Azure Data Explorer pode ser acessado usando dois pontos de extremidade: HTTPs (443) e TDS (1433). As regras NSG a seguir devem ser configuradas para permitir o acesso a esses pontos de extremidade para gerenciamento, monitoramento e operação adequada do cluster.

#### <a name="inbound-nsg-configuration"></a>Configuração de NSG de entrada

| **Utilizar**   | **From**   | **Para**   | **Protocolo**   |
| --- | --- | --- | --- |
| Gestão  |[ADX Management Addresses](#azure-data-explorer-management-ip-addresses)/AzureDataExplorerManagement (ServiceTag) | Sub-rede ADX: 443  | TCP  |
| Monitorização do estado de funcionamento  | [Endereços de monitoramento de integridade do ADX](#health-monitoring-addresses)  | Sub-rede ADX: 443  | TCP  |
| ADX comunicação interna  | Sub-rede ADX: todas as portas  | Sub-rede ADX: todas as portas  | Tudo  |
| Permitir entrada do Azure Load Balancer (investigação de integridade)  | AzureLoadBalancer  | Sub-rede ADX: 80443  | TCP  |

#### <a name="outbound-nsg-configuration"></a>Configuração de NSG de saída

| **Utilizar**   | **From**   | **Para**   | **Protocolo**   |
| --- | --- | --- | --- |
| Dependência no armazenamento do Azure  | Sub-rede ADX  | Armazenamento: 443  | TCP  |
| Dependência em Azure Data Lake  | Sub-rede ADX  | AzureDataLake: 443  | TCP  |
| Ingestão de EventHub e monitoramento de serviço  | Sub-rede ADX  | EventHub: 443, 5671  | TCP  |
| Publicar métricas  | Sub-rede ADX  | AzureMonitor: 443 | TCP  |
| Download de configuração do Azure Monitor  | Sub-rede ADX  | [Endereços do ponto de extremidade de configuração do Azure monitor](#azure-monitor-configuration-endpoint-addresses): 443 | TCP  |
| Active Directory (se aplicável) | Sub-rede ADX | AzureActiveDirectory: 443 | TCP |
| Autoridade de certificação | Sub-rede ADX | Internet: 80 | TCP |
| Comunicação interna  | Sub-rede ADX  | Sub-rede ADX: todas as portas  | Tudo  |
| Portas que são usadas para plug-ins `sql\_request` e `http\_request`  | Sub-rede ADX  | Internet: personalizado  | TCP  |

### <a name="relevant-ip-addresses"></a>Endereços IP relevantes

#### <a name="azure-data-explorer-management-ip-addresses"></a>Endereços IP de gerenciamento de Data Explorer do Azure

| Região | Endereços |
| --- | --- |
| Austrália Central | 20.37.26.134 |
| Central2 da Austrália | 20.39.99.177 |
| Leste da Austrália | 40.82.217.84 |
| Sudeste da Austrália | 20.40.161.39 |
| BrazilSouth | 191.233.25.183 |
| Canadá Central | 40.82.188.208 |
| Leste do Canadá | 40.80.255.12 |
| Índia Central | 40.81.249.251 |
| E.U.A. Central | 40.67.188.68 |
| EUA Central EUAP | 40.89.56.69 |
| Ásia Oriental | 20.189.74.103 |
| E.U.A. Leste | 52.224.146.56 |
| E.U.A. Leste 2 | 52.232.230.201 |
| EUAP dos EUA 2 leste | 52.253.226.110 |
| França Central | 40.66.57.91 |
| Sul de França | 40.82.236.24 |
| Leste do Japão | 20.43.89.90 |
| Oeste do Japão | 40.81.184.86 |
| Coreia do Sul Central | 40.82.156.149 |
| Sul da Coreia do Sul | 40.80.234.9 |
| E.U.A. Centro-Norte | 40.81.45.254 |
| Europa do Norte | 52.142.91.221 |
| Norte da África do Sul | 102.133.129.138 |
| Oeste da África do Sul | 102.133.0.97 |
| E.U.A. Centro-Sul | 20.45.3.60 |
| Sudeste Asiático | 40.119.203.252 |
| Sul da Índia | 40.81.72.110 |
| Sul do Reino Unido | 40.81.154.254 |
| Oeste do Reino Unido | 40.81.122.39 |
| E.U.A. Centro-Oeste | 52.159.55.120 |
| Europa Ocidental | 51.145.176.215 |
| Oeste da Índia | 40.81.88.112 |
| E.U.A. Oeste | 13.64.38.225 |
| E.U.A. Oeste 2 | 40.90.219.23 |

#### <a name="health-monitoring-addresses"></a>Endereços de monitoramento de integridade

| Região | Endereços |
| --- | --- |
| Austrália Central | 191.239.64.128 |
| Austrália Central 2 | 191.239.64.128 |
| Leste da Austrália | 191.239.64.128 |
| Sudeste da Austrália | 191.239.160.47 |
| Sul do Brasil | 23.98.145.105 |
| Canadá Central | 168.61.212.201 |
| Leste do Canadá | 168.61.212.201 |
| Índia Central | 23.99.5.162 |
| E.U.A. Central | 168.61.212.201 |
| EUA Central EUAP | 168.61.212.201 |
| Ásia Oriental | 168.63.212.33 |
| E.U.A. Leste | 137.116.81.189 |
| E.U.A. Leste 2 | 137.116.81.189 |
| Leste dos EUA 2 EUAP | 137.116.81.189 |
| França Central | 23.97.212.5 |
| Sul de França | 23.97.212.5 |
| Leste do Japão | 138.91.19.129 |
| Oeste do Japão | 138.91.19.129 |
| Coreia do Sul Central | 138.91.19.129 |
| Sul da Coreia do Sul | 138.91.19.129 |
| E.U.A. Centro-Norte | 23.96.212.108 |
| Europa do Norte | 191.235.212.69 
| Norte da África do Sul | 104.211.224.189 |
| Oeste da África do Sul | 104.211.224.189 |
| E.U.A. Centro-Sul | 23.98.145.105 |
| Sul da Índia | 23.99.5.162 |
| Sudeste Asiático | 168.63.173.234 |
| Sul do Reino Unido | 23.97.212.5 |
| Oeste do Reino Unido | 23.97.212.5 |
| E.U.A. Centro-Oeste | 168.61.212.201 |
| Europa Ocidental | 23.97.212.5 |
| Oeste da Índia | 23.99.5.162 |
| E.U.A. Oeste | 23.99.5.162 |
| E.U.A. Oeste 2 | 23.99.5.162 | 

#### <a name="azure-monitor-configuration-endpoint-addresses"></a>Endereços de ponto de extremidade de configuração Azure Monitor

| Região | Endereços |
| --- | --- |
| Austrália Central | 52.148.86.165 |
| Austrália Central 2 | 52.148.86.165 |
| Leste da Austrália | 52.148.86.165 |
| Sudeste da Austrália | 52.148.86.165 |
| Sul do Brasil | 13.68.89.19 |
| Central do Canadá | 13.90.43.231 |
| Leste do Canadá | 13.90.43.231 |
| Índia central | 13.71.25.187 |
| EUA Central | 52.173.95.68 |
| EUA Central EUAP | 13.90.43.231 |
| Ásia Oriental | 13.75.117.221 |
| Leste dos EUA | 13.90.43.231 |
| Leste dos EUA 2 | 13.68.89.19 | 
| Leste dos EUA 2 EUAP | 13.68.89.19 |
| França central | 52.174.4.112 |
| Sul da França | 52.174.4.112 |
| Leste do Japão | 13.75.117.221 |
| Oeste do Japão | 13.75.117.221 |
| Centro da Coreia | 13.75.117.221 |
| Sul da Coreia | 13.75.117.221 |
| EUA Central norte | 52.162.240.236 |
| Europa Setentrional | 52.169.237.246 |
| Norte da África do Sul | 13.71.25.187 |
| Oeste da África do Sul | 13.71.25.187 |
| EUA Central do Sul | 13.84.173.99 |
| Sul da Índia | 13.71.25.187 |
| Sudeste da Ásia | 52.148.86.165 |
| Sul do Reino Unido | 52.174.4.112 |
| Oeste do Reino Unido | 52.169.237.246 |
| E.U.A. Centro-Oeste | 52.161.31.69 |
| Europa Ocidental | 52.174.4.112 |
| Índia ocidental | 13.71.25.187 |
| Oeste dos EUA | 40.78.70.148 |
| Oeste dos EUA 2 | 52.151.20.103 |

## <a name="expressroute-setup"></a>Configuração do ExpressRoute

Use o ExpressRoute para conectar a rede local à rede virtual do Azure. Uma configuração comum é anunciar a rota padrão (0.0.0.0/0) por meio da sessão de Border Gateway Protocol (BGP). Isso força o tráfego proveniente da rede virtual a ser encaminhada para a rede local do cliente que pode descartar o tráfego, causando a interrupção dos fluxos de saída. Para superar esse padrão, a [UDR (rota definida pelo usuário)](/azure/virtual-network/virtual-networks-udr-overview#user-defined) (0.0.0.0/0) pode ser configurada e o próximo salto será *Internet*. Como o UDR tem precedência sobre o BGP, o tráfego será destinado à Internet.

## <a name="securing-outbound-traffic-with-firewall"></a>Protegendo o tráfego de saída com o firewall

Se você quiser proteger o tráfego de saída usando o [Firewall do Azure](/azure/firewall/overview) ou qualquer dispositivo virtual para limitar os nomes de domínio, os seguintes nomes de domínio totalmente qualificados (FQDN) devem ser permitidos no firewall.

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

Você também precisa definir a [tabela de rotas](/azure/virtual-network/virtual-networks-udr-overview) na sub-rede com os endereços de [Gerenciamento](#azure-data-explorer-management-ip-addresses) e os endereços de [monitoramento de integridade](#health-monitoring-addresses) com a *Internet* do próximo salto para evitar problemas de rotas assimétricas.

Por exemplo, para a região **oeste dos EUA** , o UDRs a seguir deve ser definido:

| Nome | Prefixo do endereço | Próximo salto |
| --- | --- | --- |
| ADX_Management | 13.64.38.225/32 | Internet |
| ADX_Monitoring | 23.99.5.162/32 | Internet |

## <a name="deploy-azure-data-explorer-cluster-into-your-vnet-using-an-azure-resource-manager-template"></a>Implantar o cluster de Data Explorer do Azure em sua VNet usando um modelo de Azure Resource Manager

Para implantar o cluster Data Explorer do Azure em sua rede virtual, use o [cluster implantar data Explorer do Azure em seu modelo de Azure Resource Manager VNet](https://azure.microsoft.com/resources/templates/101-kusto-vnet/) .

Este modelo cria o cluster, a rede virtual, a sub-rede, o grupo de segurança de rede e os endereços IP públicos.
