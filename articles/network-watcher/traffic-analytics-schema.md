---
title: Esquema de análise de tráfego Azure Microsoft Docs
description: Compreenda o esquema da Traffic Analytics para analisar os registos de fluxo do grupo de segurança da rede Azure.
services: network-watcher
documentationcenter: na
author: vinynigam
manager: agummadi
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/26/2019
ms.author: vinigam
ms.openlocfilehash: 015b8e400e9d386fff8f35756a77139e61bbaff1
ms.sourcegitcommit: 31d242b611a2887e0af1fc501a7d808c933a6bf6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/29/2020
ms.locfileid: "97809297"
---
# <a name="schema-and-data-aggregation-in-traffic-analytics"></a>Schema e agregação de dados em Análise de Tráfego

Traffic Analytics é uma solução baseada na nuvem que proporciona visibilidade à atividade do utilizador e da aplicação em redes em nuvem. Traffic Analytics analisa os registos de fluxo do Grupo de Segurança da Rede Observador (NSG) para fornecer informações sobre o fluxo de tráfego na sua nuvem Azure. Com análises de tráfego, pode:

- Visualize a atividade da rede através das suas subscrições Azure e identifique pontos quentes.
- Identifique ameaças de segurança para, e proteja a sua rede, com informações como portas abertas, aplicações que tentam aceder à Internet e máquinas virtuais (VM) conectando-se a redes fraudulentas.
- Compreenda os padrões de fluxo de tráfego em todas as regiões de Azure e na internet para otimizar a implementação da sua rede para desempenho e capacidade.
- Identifique as configurações erradas da rede que conduzam a ligações falhadas na sua rede.
- Conheça o uso da rede em bytes, pacotes ou fluxos.

### <a name="data-aggregation"></a>Agregação de dados

1. Todos os registos de fluxo de um NSG entre "FlowIntervalStartTime_t" e "FlowIntervalEndTime_t" são capturados em intervalos de um minuto na conta de armazenamento como bolhas antes de serem processados pela Traffic Analytics.
2. O intervalo de processamento predefinido da Análise de Tráfego é de 60 minutos. Isto significa que a cada 60 minutos a Traffic Analytics apanha bolhas do armazenamento para agregação. Se o intervalo de processamento escolhido for de 10 minutos, a Traffic Analytics escolherá as bolhas da conta de armazenamento após cada 10 minutos.
3. Os fluxos que têm o mesmo IP de Origem, Destination IP, porto de destino, nome NSG, regra NSG, Direção de Fluxo e Protocolo de Camada de Transporte (TCP ou UDP) (Nota: A porta de origem está excluída para agregação) são colididos com um único fluxo pela Traffic Analytics
4. Este registo único está decorado (Detalhes na secção abaixo) e ingerido em Log Analytics pela Traffic Analytics.Este processo pode demorar até 1 hora no máximo.
5. FlowStartTime_t campo indica a primeira ocorrência de tal fluxo agregado (os mesmos quatro tuple) no intervalo de processamento do registo de fluxo entre "FlowIntervalStartTime_t" e "FlowIntervalEndTime_t".
6. Para qualquer recurso em AT, os fluxos indicados na UI são fluxos totais vistos pelo NSG, mas no Log Analytics o utilizador verá apenas o registo único e reduzido. Para ver todos os fluxos, utilize o campo blob_id, que pode ser referenciado a partir de Armazenamento. A contagem total de fluxo para esse registo corresponderá aos fluxos individuais observados na bolha.

A consulta abaixo ajuda-o a olhar para todas as sub-redes que interagem com iPs públicos não-Azure nos últimos 30 dias.
```
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FlowStartTime_t >= ago(30d) and FlowType_s == "ExternalPublic"
| project Subnet1_s, Subnet2_s  
```
Para ver o caminho do blob para os fluxos na consulta acima mencionada, utilize a consulta abaixo:

```
let TableWithBlobId =
(AzureNetworkAnalytics_CL
   | where SubType_s == "Topology" and ResourceType == "NetworkSecurityGroup" and DiscoveryRegion_s == Region_s and IsFlowEnabled_b
   | extend binTime = bin(TimeProcessed_t, 6h),
            nsgId = strcat(Subscription_g, "/", Name_s),
            saNameSplit = split(FlowLogStorageAccount_s, "/")
   | extend saName = iif(arraylength(saNameSplit) == 3, saNameSplit[2], '')
   | distinct nsgId, saName, binTime)
| join kind = rightouter (
   AzureNetworkAnalytics_CL
   | where SubType_s == "FlowLog"  
   | extend binTime = bin(FlowEndTime_t, 6h)
) on binTime, $left.nsgId == $right.NSGList_s  
| extend blobTime = format_datetime(todatetime(FlowIntervalStartTime_t), "yyyy MM dd hh")
| extend nsgComponents = split(toupper(NSGList_s), "/"), dateTimeComponents = split(blobTime, " ")
| extend BlobPath = strcat("https://", saName,
                        "@insights-logs-networksecuritygroupflowevent/resoureId=/SUBSCRIPTIONS/", nsgComponents[0],
                        "/RESOURCEGROUPS/", nsgComponents[1],
                        "/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/", nsgComponents[2],
                        "/y=", dateTimeComponents[0], "/m=", dateTimeComponents[1], "/d=", dateTimeComponents[2], "/h=", dateTimeComponents[3],
                        "/m=00/macAddress=", replace(@"-", "", MACAddress_s),
                        "/PT1H.json")
| project-away nsgId, saName, binTime, blobTime, nsgComponents, dateTimeComponents;

TableWithBlobId
| where SubType_s == "FlowLog" and FlowStartTime_t >= ago(30d) and FlowType_s == "ExternalPublic"
| project Subnet_s , BlobPath
```

A consulta acima constrói um URL para aceder diretamente à bolha. O URL com os detentores de lugares é abaixo:

```
https://{saName}@insights-logs-networksecuritygroupflowevent/resoureId=/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroup}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json

```

### <a name="fields-used-in-traffic-analytics-schema"></a>Campos utilizados no esquema de Análise de Tráfego
  > [!IMPORTANT]
  > O Traffic Analytics Schema foi atualizado no dia 22 de agosto de 2019. O novo esquema fornece iPs de origem e destino removendo separadamente a necessidade de analisar o campo FlowDirection tornando as consultas mais simples. </br>
  > FASchemaVersion_s atualizado de 1 a 2. </br>
  > Campos preprecados: VMIP_s, Subscription_s, Region_s, NSGRules_s, Subnet_s, VM_s, NIC_s, PublicIPs_s, FlowCount_d </br>
  > Novos campos: SrcPublicIPs_s, DestPublicIPs_s, NSGRule_s </br>
  > Os campos precatados estarão disponíveis até 22 de novembro de 2019.

Traffic Analytics é construído em cima do Log Analytics, para que você possa executar consultas personalizadas sobre dados decorados pela Traffic Analytics e definir alertas sobre o mesmo.

Listados abaixo estão os campos no esquema e o que eles significam

| Campo | Formato | Comentários |
|:---   |:---    |:---  |
| TableName | AzureNetworkAnalytics_CL | Tabela para dados de Análise de Tráfego
| SubType_s | Fluxolog | Subtipo para os registos de fluxo. Utilize apenas "FlowLog", outros valores de SubType_s são para o funcionamento interno do produto |
| FASchemaVersion_s |   2   | Versão schema. Não reflete a versão NSG Flow Log |
| TimeProcessed_t   | Data e Hora na UTC  | Momento em que o Traffic Analytics processou os registos de fluxo bruto da conta de armazenamento |
| FlowIntervalStartTime_t | Data e Hora na UTC |  Tempo de início do intervalo de processamento do registo de fluxo. Este é o tempo a partir do qual o intervalo de fluxo é medido |
| FlowIntervalEndTime_t | Data e Hora na UTC | Tempo final do intervalo de processamento do registo de fluxo |
| FlowStartTime_t | Data e Hora na UTC |  Primeira ocorrência do caudal (que será agregado) no intervalo de processamento de registo de fluxo entre "FlowIntervalStartTime_t" e "FlowIntervalEndTime_t". Este fluxo é agregado com base na lógica de agregação |
| FlowEndTime_t | Data e Hora na UTC | Última ocorrência do caudal (que será agregado) no intervalo de processamento de registo de fluxo entre "FlowIntervalStartTime_t" e "FlowIntervalEndTime_t". Em termos de log de fluxo v2, este campo contém o tempo em que o último fluxo com o mesmo quatro tuple começou (marcado como "B" no registo de fluxo bruto) |
| FlowType_s |  * IntraVNet <br> * InterVNet <br> * S2S <br> * P2S <br> * AzurePublic <br> * Publiclic Externo <br> * Fluxo malicioso <br> * Privado Desconhecido <br> Desconhecido | Definição em notas abaixo da tabela |
| SrcIP_s | Endereço IP de origem | Ficarão em branco no caso dos fluxos públicos azurePúblicos e Externos |
| DestIP_s | Endereço IP de destino | Ficarão em branco no caso dos fluxos públicos azurePúblicos e Externos |
| VMIP_s | IP do VM | Utilizado para fluxos públicos azurePúblicos e Externos |
| PublicIP_s | Endereços IP públicos | Utilizado para fluxos públicos azurePúblicos e Externos |
| DestPort_d | Porta de destino | Porto onde o tráfego está a chegar |
| L4Protocol_s  | * T <br> U  | Protocolo de Transporte. T = TCP <br> U = UDP |
| L7Protocol_s  | Nome do protocolo | Derivado do porto de destino |
| FlowDirection_s | * I = Entrada<br> * O = Saída | Direção do fluxo dentro/fora do NSG de acordo com o registo de fluxo |
| FlowStatus_s  | * A = Permitido pela Regra NSG <br> * D = Negado pela Regra NSG  | Estado do fluxo permitido/nblocked por NSG como registo de fluxo |
| NSGList_s | \<SUBSCRIPTIONID>\/<RESOURCEGROUP_NAME>\/<NSG_NAME> | Grupo de Segurança de Rede (NSG) associado ao fluxo |
| NSGRules_s | \<Index value 0)>\|\<NSG_RULENAME>\|\<Flow Direction>\|\<Flow Status>\|\<FlowCount ProcessedByRule> |  Regra NSG que permitiu ou negou este fluxo |
| NSGRule_s | NSG_RULENAME |  Regra NSG que permitiu ou negou este fluxo |
| NSGRuleType_s | * Utilizador definido * Predefinido |   O tipo de Regra NSG usada pelo fluxo |
| MACAddress_s | Endereço MAC | Endereço MAC do NIC no qual o fluxo foi capturado |
| Subscription_s | A subscrição da rede virtual Azure/ interface de rede/ máquina virtual é povoada neste campo | Aplicável apenas para os tipos de fluxo FlowType = S2S, P2S, AzurePublic, ExternalPublic, MaliciousFlow e UnknownPrivate flow (tipos de fluxo onde apenas um lado é azul) |
| Subscription1_s | ID da subscrição | ID de subscrição de rede virtual/interface de rede/ máquina virtual à qual pertence o IP de origem no fluxo |
| Subscription2_s | ID da subscrição | ID de subscrição de rede virtual/ interface de rede/ máquina virtual à qual o IP de destino no fluxo pertence |
| Region_s | Região azul da rede virtual/ interface de rede/ máquina virtual à qual o IP no fluxo pertence | Aplicável apenas para os tipos de fluxo FlowType = S2S, P2S, AzurePublic, ExternalPublic, MaliciousFlow e UnknownPrivate flow (tipos de fluxo onde apenas um lado é azul) |
| Region1_s | Região do Azure | Região azul da rede virtual/ interface de rede/ máquina virtual à qual pertence o IP de origem no fluxo |
| Region2_s | Região do Azure | Região azul da rede virtual a que o IP de destino no fluxo pertence |
| NIC_s | \<resourcegroup_Name>\/\<NetworkInterfaceName> |  NIC associado ao envio ou receção do tráfego de VM |
| NIC1_s | <resourcegroup_Name>/\<NetworkInterfaceName> | NIC associado com o IP de origem no fluxo |
| NIC2_s | <resourcegroup_Name>/\<NetworkInterfaceName> | NIC associado ao IP de destino no fluxo |
| VM_s | <resourcegroup_Name>\/\<NetworkInterfaceName> | Máquina Virtual associada à interface da Rede NIC_s |
| VM1_s | <resourcegroup_Name>/\<VirtualMachineName> | Máquina Virtual associada ao IP de origem no fluxo |
| VM2_s | <resourcegroup_Name>/\<VirtualMachineName> | Máquina Virtual associada ao IP de destino no fluxo |
| Subnet_s | <ResourceGroup_Name>/<VNET_Name>/\<SubnetName> | Sub-rede associada à NIC_s |
| Subnet1_s | <ResourceGroup_Name>/<VNET_Name>/\<SubnetName> | Sub-rede associada ao IP de origem no fluxo |
| Subnet2_s | <ResourceGroup_Name>/<VNET_Name>/\<SubnetName>    | Sub-rede associada ao Destination IP no fluxo |
| ApplicationGateway1_s | \<SubscriptionID>/\<ResourceGroupName>/\<ApplicationGatewayName> | Gateway de aplicação associado ao IP de origem no fluxo |
| ApplicationGateway2_s | \<SubscriptionID>/\<ResourceGroupName>/\<ApplicationGatewayName> | Gateway de aplicação associado ao Destination IP no fluxo |
| LoadBalancer1_s | \<SubscriptionID>/\<ResourceGroupName>/\<LoadBalancerName> | Balançador de carga associado ao IP de origem no fluxo |
| LoadBalancer2_s | \<SubscriptionID>/\<ResourceGroupName>/\<LoadBalancerName> | Balançador de carga associado ao Destination IP no fluxo |
| LocalNetworkGateway1_s | \<SubscriptionID>/\<ResourceGroupName>/\<LocalNetworkGatewayName> | Gateway de rede local associado ao IP fonte no fluxo |
| LocalNetworkGateway2_s | \<SubscriptionID>/\<ResourceGroupName>/\<LocalNetworkGatewayName> | Gateway de rede local associado ao Destination IP no fluxo |
| ConnectionType_s | Os valores possíveis são VNetPeering, VpnGateway e ExpressRoute |    Tipo de Ligação |
| ConnectionName_s | \<SubscriptionID>/\<ResourceGroupName>/\<ConnectionName> | Nome de ligação. Para o tipo de fluxo P2S, este será formatado como <gateway name> _<VPN Client IP> |
| ConnectingVNets_s | Lista de nomes de rede virtual separada do espaço | No caso do hub e da topologia falada, as redes virtuais do hub serão povoadas aqui |
| Country_s | Código de país de duas letras (ISO 3166-1 alfa-2) | Povoado para o tipo de fluxo ExternalPublic. Todos os endereços IP no campo PublicIPs_s partilharão o mesmo código de país |
| AzureRegion_s | Localizações da região de Azure | Povoado para o tipo de fluxo AzurePublic. Todos os endereços IP no campo PublicIPs_s partilharão a região de Azure |
| AllowedInFlows_d | | Contagem de fluxos de entrada que eram permitidos. Isto representa o número de fluxos que partilharam a mesma entrada de quatro tuple para a interface de rede em que o fluxo foi capturado |
| DeniedInFlows_d |  | Contagem de fluxos de entrada que foram negados. (Entrada na interface de rede em que o fluxo foi capturado) |
| AllowedOutFlows_d | | Contagem de fluxos de saída que foram permitidos (Saída para a interface de rede em que o fluxo foi capturado) |
| DeniedOutFlows_d  | | Contagem de fluxos de saída que foram negados (Saída para a interface de rede em que o fluxo foi capturado) |
| FlowCount_d | Preterido. Fluxos totais que combinam com os mesmos quatro tuples. No caso dos tipos de fluxo ExternalPublic e AzurePublic, a contagem incluirá os fluxos de vários endereços PublicIP também.
| InboundPackets_d | Pacotes recebidos como capturados na interface de rede onde a regra NSG foi aplicada | Isto é povoado apenas para a versão 2 do esquema de registo de fluxo NSG |
| OutboundPackets_d  | Pacotes enviados como capturados na interface de rede onde a regra NSG foi aplicada | Isto é povoado apenas para a versão 2 do esquema de registo de fluxo NSG |
| InboundBytes_d |  Bytes recebidos como capturados na interface de rede onde a regra NSG foi aplicada | Isto é povoado apenas para a versão 2 do esquema de registo de fluxo NSG |
| OutboundBytes_d | Bytes enviados como capturados na interface de rede onde a regra NSG foi aplicada | Isto é povoado apenas para a versão 2 do esquema de registo de fluxo NSG |
| CompletedFlows_d  |  | Isto é povoado com valor não zero apenas para a versão 2 do esquema de registo de fluxo NSG |
| PublicIPs_s | <PUBLIC_IP>\|\<FLOW_STARTED_COUNT>\|\<FLOW_ENDED_COUNT>\|\<OUTBOUND_PACKETS>\|\<INBOUND_PACKETS>\|\<OUTBOUND_BYTES>\|\<INBOUND_BYTES> | Entradas separadas por bares |
| SrcPublicIPs_s | <SOURCE_PUBLIC_IP>\|\<FLOW_STARTED_COUNT>\|\<FLOW_ENDED_COUNT>\|\<OUTBOUND_PACKETS>\|\<INBOUND_PACKETS>\|\<OUTBOUND_BYTES>\|\<INBOUND_BYTES> | Entradas separadas por bares |
| DestPublicIPs_s | <DESTINATION_PUBLIC_IP>\|\<FLOW_STARTED_COUNT>\|\<FLOW_ENDED_COUNT>\|\<OUTBOUND_PACKETS>\|\<INBOUND_PACKETS>\|\<OUTBOUND_BYTES>\|\<INBOUND_BYTES> | Entradas separadas por bares |

### <a name="notes"></a>Notas

1. No caso de fluxos públicos azurePúblico e Externo, o cliente detido Azure VM IP é povoado em VMIP_s campo, enquanto os endereços IP públicos estão sendo povoados no campo PublicIPs_s. Para estes dois tipos de fluxo, devemos utilizar VMIP_s e PublicIPs_s em vez de campos de SrcIP_s e DestIP_s. Para os endereços AzurePublic e ExternalPublicIP, agregamos ainda mais, de modo a que o número de registos ingeridos no espaço de trabalho de análise de registo de clientes seja mínimo. (Este campo será depreciado em breve e devemos usar SrcIP_ e DestIP_s dependendo se a Azure VM foi a fonte ou o destino no fluxo)
1. Detalhes para os tipos de fluxo: Com base nos endereços IP envolvidos no fluxo, categorizamos os fluxos para os seguintes tipos de fluxo:
1. IntraVNet – Ambos os endereços IP no fluxo residem na mesma Rede Virtual Azure.
1. Os endereços INTERVNet - IP no fluxo residem nas duas redes virtuais Azure diferentes.
1. S2S – (Site Ao Site) Um dos endereços IP pertence à Rede Virtual Azure, enquanto o outro endereço IP pertence à rede de clientes (Site) ligada à Rede Virtual Azure através do gateway VPN ou da Rota Expressa.
1. P2S - (Ponto Para o Local) Um dos endereços IP pertence à Rede Virtual Azure, enquanto o outro endereço IP pertence à rede de clientes (Site) ligada à Rede Virtual Azure através do gateway VPN.
1. AzurePublic - Um dos endereços IP pertence à Rede Virtual Azure, enquanto o outro endereço IP pertence aos endereços IP internos do Azure, propriedade da Microsoft. Os endereços IP públicos de propriedade do cliente não serão parte deste tipo de fluxo. Por exemplo, qualquer cliente pertencente à VM que envie tráfego para um Serviço Azure (ponto final de armazenamento) seria categorizado sob este tipo de fluxo.
1. ExternalPublic - Um dos endereços IP pertence à Rede Virtual Azure enquanto o outro endereço IP é um IP público que não está no Azure, não é reportado como malicioso nos feeds asc que o Traffic Analytics consome para o intervalo de processamento entre "FlowIntervalStartTime_t" e "FlowIntervalEndTime_t".
1. MaliciousFlow - Um dos endereços IP pertence à rede virtual azul enquanto o outro endereço IP é um IP público que não está no Azure e é reportado como malicioso nos feeds da ASC que o Traffic Analytics consome para o intervalo de processamento entre "FlowIntervalStartTime_t" e "FlowIntervalEndTime_t".
1. UnknownPrivate - Um dos endereços IP pertence à Rede Virtual Azure, enquanto o outro endereço IP pertence à gama IP privada, tal como definida no RFC 1918 e não pôde ser mapeada pela Traffic Analytics para um site de propriedade do cliente ou rede virtual Azure.
1. Desconhecido – Não é possível mapear qualquer um dos endereços IP nos fluxos com a topologia do cliente em Azure, bem como no local (site).
1. Alguns nomes de campo são anexados com \_ s ou \_ d. Estes NÃO significam origem e destino, mas indicam os tipos de dados de cadeia e decimal respectivamente.

### <a name="next-steps"></a>Passos Seguintes
Para obter respostas a perguntas frequentes, consulte [o Traffic analytics FAQ](traffic-analytics-faq.md) Para ver detalhes sobre a funcionalidade, consulte [documentação de análise de tráfego](traffic-analytics.md)
