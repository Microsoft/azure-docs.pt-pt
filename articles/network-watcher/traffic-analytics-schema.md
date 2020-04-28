---
title: Esquema de análise de tráfego azure [ esquema de análise de tráfego azul ] Microsoft Docs
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
ms.openlocfilehash: ccfbb92c27e4508595f19c2ea6900730cde609b9
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "74666380"
---
# <a name="schema-and-data-aggregation-in-traffic-analytics"></a>Schema e agregação de dados em Análise de Tráfego

Traffic Analytics é uma solução baseada na nuvem que proporciona visibilidade na atividade do utilizador e aplicação em redes cloud. Traffic Analytics analisa os registos de fluxo do grupo de segurança da rede Desfluxo (NSG) para fornecer informações sobre o fluxo de tráfego na sua nuvem Azure. Com análise de tráfego, pode:

- Visualize a atividade da rede através das suas subscrições do Azure e identifique pontos quentes.
- Identifique ameaças de segurança e proteja a sua rede, com informações como portas abertas, aplicações que tentem aceder à Internet e máquinas virtuais (VM) conectando-se a redes fraudulentas.
- Compreenda os padrões de fluxo de tráfego em todas as regiões de Azure e na internet para otimizar a implementação da sua rede para desempenho e capacidade.
- Identificar configurações erradas de rede que conduzam a ligações falhadas na sua rede.
- Conheça o uso da rede em bytes, pacotes ou fluxos.

### <a name="data-aggregation"></a>Agregação de dados

1. Todos os registos de fluxo num NSG entre "FlowIntervalStartTime_t" e "FlowIntervalEndTime_t" são capturados em intervalos de um minuto na conta de armazenamento como bolhas antes de serem processados pela Traffic Analytics.
2. O intervalo de processamento padrão do Traffic Analytics é de 60 minutos. Isto significa que a cada 60 minutos o Traffic Analytics apanha bolhas do armazenamento para agregação. Se o intervalo de processamento escolhido for de 10 minutos, o Traffic Analytics colherá bolhas da conta de armazenamento após 10 minutos.
3. Os fluxos que tenham o mesmo IP fonte, destination IP, porta de destino, nome NSG, regra NSG, Direção de Fluxo e Protocolo de Camada de Transporte (TCP ou UDP) (Nota: Porta de origem está excluída para agregação) são colocados num único fluxo pela Traffic Analytics
4. Este único disco é decorado (Detalhes na secção abaixo) e ingerido em Log Analytics by Traffic Analytics.Este processo pode demorar até 1 hora no máximo.
5. FlowStartTime_t campo indica a primeira ocorrência de um fluxo agregado (mesmo quatro tuplés) no intervalo de processamento do registo de fluxo entre "FlowIntervalStartTime_t" e "FlowIntervalEndTime_t".
6. Para qualquer recurso em TA, os fluxos indicados na UI são fluxos totais vistos pelo NSG, mas no Log Analytics o utilizador verá apenas o único e reduzido registo. Para ver todos os fluxos, utilize o campo blob_id, que pode ser referenciado a partir do Armazenamento. A contagem total de fluxopara esse recorde corresponderá aos fluxos individuais observados na bolha.

A consulta abaixo ajuda-o a olhar para todos os registos de fluxo a partir do local nos últimos 30 dias.
```
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FlowStartTime_t >= ago(30d) and FlowType_s == "ExternalPublic"
| project Subnet_s  
```
Para ver o caminho blob para os fluxos na consulta acima mencionada, use a consulta abaixo:

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

A consulta acima constrói um URL para aceder diretamente à bolha. O URL com suportes de local está abaixo:

```
https://{saName}@insights-logs-networksecuritygroupflowevent/resoureId=/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroup}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json

```

### <a name="fields-used-in-traffic-analytics-schema"></a>Campos utilizados em esquema de Análise de Tráfego
  > [!IMPORTANT]
  > O Traffic Analytics Schema foi atualizado no dia 22 de agosto de 2019. O novo esquema fornece iPs de origem e destino removendo separadamente a necessidade de analisar o campo FlowDirection tornando as consultas mais simples. </br>
  > FASchemaVersion_s atualizado de 1 a 2. </br>
  > Campos premeditados: VMIP_s, Subscription_s, Region_s, NSGRules_s, Subnet_s, VM_s, NIC_s, PublicIPs_s, FlowCount_d </br>
  > Novos campos: SrcPublicIPs_s, DestPublicIPs_s, NSGRule_s </br>
  > Os campos degradados estarão disponíveis até 22 de novembro de 2019.

Traffic Analytics é construído em cima do Log Analytics, para que você possa executar consultas personalizadas em dados decorados pela Traffic Analytics e definir alertas no mesmo.

Listados abaixo estão os campos no esquema e o que significam

| Campo | Formato | Comentários |
|:---   |:---    |:---  |
| TableName | AzureNetworkAnalytics_CL | Tabela para dados de Análise de Tráfego
| SubType_s | FlowLog | Subtipo para os troncos de fluxo. Utilize apenas "FlowLog", outros valores de SubType_s são para o funcionamento interno do produto |
| FASchemaVersion_s |   2   | Versão schema. Não reflete a versão NSG Flow Log |
| TimeProcessed_t   | Data e hora em UTC  | Tempo em que o Traffic Analytics processou os registos de fluxo bruto da conta de armazenamento |
| FlowIntervalStartTime_t | Data e hora em UTC |  Tempo de início do intervalo de processamento do registo de fluxo. Este é o tempo a partir do qual o intervalo de fluxo é medido |
| FlowIntervalEndTime_t | Data e hora em UTC | Tempo final do intervalo de processamento de registo de fluxo |
| FlowStartTime_t | Data e hora em UTC |  Primeira ocorrência do fluxo (que será agregado) no intervalo de processamento do registo de fluxo entre "FlowIntervalStartTime_t" e "FlowIntervalEndTime_t". Este fluxo é agregado com base na lógica de agregação |
| FlowEndTime_t | Data e hora em UTC | Última ocorrência do fluxo (que será agregado) no intervalo de processamento do fluxo entre "FlowIntervalStartTime_t" e "FlowIntervalEndTime_t". Em termos de fluxo de registo v2, este campo contém o tempo em que o último fluxo com o mesmo quatro-tuple começou (marcado como "B" no registo de fluxo bruto) |
| FlowType_s |  * Intravnet <br> * InterVNet <br> * S2S <br> * P2S <br> * AzurePublic <br> * Publica externa <br> * Fluxo Malicioso <br> * Privado Desconhecido <br> Desconhecida | Definição em notas abaixo do quadro |
| SrcIP_s | Endereço IP de origem | Ficará em branco no caso dos fluxos Públicos e Externos do AzurePublic |
| DestIP_s | Endereço IP de destino | Ficará em branco no caso dos fluxos Públicos e Externos do AzurePublic |
| VMIP_s | IP do VM | Utilizado para fluxos Públicos e Externos Azure |
| PublicIP_s | Endereços IP públicos | Utilizado para fluxos Públicos e Externos Azure |
| DestPort_d | Porta de destino | Porto onde o tráfego está a chegar |
| L4Protocol_s  | * T <br> * U  | Protocolo de transporte. T = TCP <br> U = UDP |
| L7Protocol_s  | Nome do protocolo | Derivado do porto de destino |
| FlowDirection_s | * I = Entrada<br> * O = Saída | Direção do fluxo dentro/fora do NSG conforme o registo de fluxo |
| FlowStatus_s  | * A = Permitido pela Regra NSG <br> * D = Negado pela Regra nsg  | Estado do fluxo permitido/nbloqueado pelo NSG de acordo com o registo de fluxo |
| NSGList_s | \<>\/<<\/>RESOURCEGROUP_NAME NSG_NAME NSG_NAME> NSG_NAME NSG_NAME | Grupo de Segurança de Rede (NSG) associado ao fluxo |
| NSGRules_s | \<Valor do índice \| \<0)>\| \<NSG_RULENAME>\| \<Direção de fluxo \| \<>Fluxo de fluxo>FluxoCount ProcessedByRule> |  Regra nsg que permitiu ou negou este fluxo |
| NSGRule_s | NSG_RULENAME |  Regra nsg que permitiu ou negou este fluxo |
| NSGRuleType_s | * Definido pelo utilizador * Padrão |   O tipo de Regra NSG utilizada pelo fluxo |
| MACAddress_s | Endereço MAC | Endereço MAC do NIC em que o fluxo foi capturado |
| Subscription_s | A subscrição da rede virtual Do Azure/ interface de rede/ máquina virtual é povoada neste campo | Aplicável apenas para fluxoType = S2S, P2S, AzurePublic, ExternalPublic, MaliciousFlow e UnknownPrivate (tipos de fluxo onde apenas um lado é azul) |
| Subscription1_s | ID da subscrição | Id de assinatura da rede virtual/ interface de rede/ máquina virtual a que pertence a fonte IP no fluxo |
| Subscription2_s | ID da subscrição | Id de assinatura da rede virtual/ interface de rede/ máquina virtual a que o IP de destino no fluxo pertence |
| Region_s | Região azure de rede virtual/ interface de rede/ máquina virtual a que o IP no fluxo pertence | Aplicável apenas para fluxoType = S2S, P2S, AzurePublic, ExternalPublic, MaliciousFlow e UnknownPrivate (tipos de fluxo onde apenas um lado é azul) |
| Region1_s | Região do Azure | Região azure de rede virtual/ interface de rede/ máquina virtual a que pertence a fonte IP no fluxo |
| Region2_s | Região do Azure | Região azure da rede virtual a que pertence o DESTINO IP no caudal |
| NIC_s | \<resourcegroup_Name \/ \<>NetworkInterfaceName> |  NIC associado ao VM envio ou receção do tráfego |
| NIC1_s | <resourcegroup_Name\<>/ NetworkInterfaceName> | NIC associado com a fonte IP no fluxo |
| NIC2_s | <resourcegroup_Name\<>/ NetworkInterfaceName> | NIC associado ao DESTINO IP no fluxo |
| VM_s | <resourcegroup_Name \/ \<>>InterfaceName> | Máquina Virtual associada à interface da rede NIC_s |
| VM1_s | <resourcegroup_Name\<>/> de nome de máquina virtual | Máquina Virtual associada com a fonte IP no fluxo |
| VM2_s | <resourcegroup_Name\<>/> de nome de máquina virtual | Máquina Virtual associada ao IP de destino no fluxo |
| Subnet_s | <ResourceGroup_Name>/<\<VNET_Name>>> | Subnet associada ao NIC_s |
| Subnet1_s | <ResourceGroup_Name>/<\<VNET_Name>>> | Subnet associada ao IP fonte no fluxo |
| Subnet2_s | <ResourceGroup_Name>/<\<VNET_Name>>>    | Subnet associada ao IP de destino no fluxo |
| ApplicationGateway1_s | \<Subscrição ID\<>/ ResourceGroupName>/\<ApplicationGatewayName> | Gateway de aplicação associado ao IP fonte no fluxo |
| ApplicationGateway2_s | \<Subscrição ID\<>/ ResourceGroupName>/\<ApplicationGatewayName> | Gateway de aplicação associado ao IP de destino no fluxo |
| LoadBalancer1_s | \<SubscriçãoID\<>/ ResourceGroupName>/\<LoadBalancerName> | Equilibrador de carga associado ao IP fonte no fluxo |
| LoadBalancer2_s | \<SubscriçãoID\<>/ ResourceGroupName>/\<LoadBalancerName> | Equilibrador de carga associado ao IP de destino no fluxo |
| LocalNetworkGateway1_s | \<Subscrição ID\<>/ ResourceGroupName>/\<LocalNetworkGatewayName> | Gateway de rede local associado com o IP fonte no fluxo |
| LocalNetworkGateway2_s | \<Subscrição ID\<>/ ResourceGroupName>/\<LocalNetworkGatewayName> | Gateway de rede local associado ao IP de destino no fluxo |
| ConnectionType_s | Os valores possíveis são VNetPeering, VpnGateway e ExpressRoute |    Tipo de Ligação |
| ConnectionName_s | \<> de\<Subscrição/>\<de Nome do Grupo de Recursos/> de Nome de Ligação | Nome de ligação. Para o fluxo tipo P2S, <gateway name>este será formatado como _<VPN Client IP> |
| ConnectingVNets_s | Lista separada do espaço de nomes de rede virtual | Em caso de hub e topologia falada, as redes virtuais do hub serão povoadas aqui |
| Country_s | Código de país de duas letras (ISO 3166-1 alfa-2) | Povoado para o tipo de fluxo ExternalPublic. Todos os endereços IP em PublicIPs_s campo partilharão o mesmo código de país |
| AzureRegion_s | Localizações da região de Azure | Povoado para o tipo de fluxo AzurePublic. Todos os endereços IP em campo PublicIPs_s partilharão a região de Azure |
| AllowedInFlows_d | | Contagem de fluxos de entrada que foram permitidos. Isto representa o número de fluxos que partilhavam a mesma entrada de quatro tuple saqueada à interface de rede em que o fluxo foi capturado |
| DeniedInFlows_d |  | Contagem de fluxos de entrada que foram negados. (Aderindo à interface de rede em que o fluxo foi capturado) |
| AllowedOutFlows_d | | Contagem dos fluxos de saída que foram permitidos (saída para a interface de rede em que o fluxo foi capturado) |
| DeniedOutFlows_d  | | Contagem dos fluxos de saída que foram negados (saída para a interface de rede em que o fluxo foi capturado) |
| FlowCount_d | Preterido. Fluxos totais que combinam com os mesmos quatro-tuple. No caso dos tipos de fluxo ExternalPublic e AzurePublic, a contagem incluirá também os fluxos de vários endereços PublicIP.
| InboundPackets_d | Pacotes recebidos como capturados na interface de rede onde a regra NSG foi aplicada | Isto é povoado apenas para a versão 2 do esquema de log de fluxo NSG |
| OutboundPackets_d  | Pacotes enviados como capturados na interface de rede onde a regra NSG foi aplicada | Isto é povoado apenas para a versão 2 do esquema de log de fluxo NSG |
| InboundBytes_d |  Bytes recebidos como capturados na interface de rede onde a regra NSG foi aplicada | Isto é povoado apenas para a versão 2 do esquema de log de fluxo NSG |
| OutboundBytes_d | Bytes enviados como capturados na interface de rede onde a regra NSG foi aplicada | Isto é povoado apenas para a versão 2 do esquema de log de fluxo NSG |
| CompletedFlows_d  |  | Isto é povoado com valor não zero apenas para a versão 2 do esquema de log de fluxo NSG |
| PublicIPs_s | <PUBLIC_IP \| \<>\| \<>\| \<INBOUND_BYTES \| \<>\| \< \| \<OUTBOUND_BYTES INBOUND_PACKETS>>FLOW_ENDED_COUNT>FLOW_STARTED_COUNT>OUTBOUND_PACKETS OUTBOUND_PACKETS>>>>> | Entradas separadas por barras |
| SrcPublicIPs_s | FLOW_STARTED_COUNT>\| \<<\| \<SOURCE_PUBLIC_IP \| \<SOURCE_PUBLIC_IP \| \<SOURCE_PUBLIC_IP \| \<>\| \<>>INBOUND_PACKETS>>FLOW_ENDED_COUNT>OUTBOUND_PACKETS OUTBOUND_PACKETS OUTBOUND_PACKETS OUTBOUND_BYTES OUTBOUND_BYTES OUTBOUND_BYTES> INBOUND_BYTES | Entradas separadas por barras |
| DestPublicIPs_s | <DESTINATION_PUBLIC_IP \| \<>\| \<DESTINATION_PUBLIC_IP \| \<FLOW_STARTED_COUNT \| \<INBOUND_BYTES \| \<>\| \<>INBOUND_PACKETS>OUTBOUND_PACKETS>FLOW_ENDED_COUNT>FLOW_STARTED_COUNT OUTBOUND_BYTES OUTBOUND_BYTES OUTBOUND_BYTES OUTBOUND_BYTES OUTBOUND_BYTES OUTBOUND_BYTES> OUTBOUND_BYTES OUTBOUND_BYTES | Entradas separadas por barras |

### <a name="notes"></a>Notas

1. No caso dos fluxos AzurePublic e ExternalPublic, o cliente proprietário do Azure VM IP é povoado em VMIP_s campo, enquanto os endereços IP públicos estão sendo povoados no campo PublicIPs_s. Para estes dois tipos de fluxo, devemos usar VMIP_s e PublicIPs_s em vez de campos SrcIP_s e DestIP_s. Para endereços AzurePublic e ExternalPublicIP, agregamos ainda mais, de modo que o número de registos ingeridos para o espaço de trabalho de análise de registo de clientes é mínimo. (Este campo será depreciado em breve e devemos utilizar SrcIP_ e DestIP_s dependendo se o VM azul foi a fonte ou o destino no fluxo)
1. Detalhes para os tipos de fluxo: Com base nos endereços IP envolvidos no fluxo, categorizamos os fluxos para os seguintes tipos de fluxo:
1. IntraVNet – Ambos os endereços IP do fluxo residem na mesma Rede Virtual Azure.
1. InterVNet - Endereços IP no fluxo residem nas duas redes virtuais Azure diferentes.
1. S2S – (Site A Site) Um dos endereços IP pertence à Rede Virtual Azure, enquanto o outro endereço IP pertence à rede de clientes (Site) ligado à Rede Virtual Azure através do gateway VPN ou da Rota Expresso.
1. P2S - (Point To Site) Um dos endereços IP pertence à Rede Virtual Azure, enquanto o outro endereço IP pertence à rede de clientes (Site) ligado à Rede Virtual Azure através do gateway VPN.
1. AzurePublic - Um dos endereços IP pertence à Rede Virtual Azure, enquanto o outro endereço IP pertence a endereços IP públicos internos do Azure pertencentes à Microsoft. Os endereços IP públicos do cliente não fazem parte deste tipo de fluxo. Por exemplo, qualquer VM de propriedade de cliente que enviasse tráfego para um Serviço Azure (ponto final de armazenamento) seria categorizado sob este tipo de fluxo.
1. ExternalPublic - Um dos endereços IP pertence à Rede Virtual Azure, enquanto o outro endereço IP é um IP público que não está no Azure, não é reportado como malicioso nos feeds DA ASC que a Traffic Analytics consome para o intervalo de processamento entre "FlowIntervalStartTime_t" e "FlowIntervalEndTime_t".
1. MaliciousFlow - Um dos endereços IP pertence à rede virtual azure, enquanto o outro endereço IP é um IP público que não está no Azure e é relatado como malicioso nos feeds ASC que o Traffic Analytics consome para o intervalo de processamento entre "FlowIntervalStartTime_t" e "FlowIntervalEndTime_t".
1. UnknownPrivate - Um dos endereços IP pertence à Rede Virtual Azure, enquanto o outro endereço IP pertence à gama IP privada, tal como definido no RFC 1918 e não pôde ser mapeado pela Traffic Analytics para um site de propriedade de clientes ou rede virtual Azure.
1. Desconhecido – Incapaz de mapear os endereços IP nos fluxos com a topologia do cliente em Azure, bem como no local (site).
1. Alguns nomes de \_campo \_são anexados com s ou d. Estes NÃO significam origem e destino, mas indicam os tipos de dados string e decimal respectivamente.

### <a name="next-steps"></a>Passos Seguintes
Para obter respostas a perguntas frequentes, consulte [as FAQ](traffic-analytics-faq.md) de análise de tráfego Para ver detalhes sobre a funcionalidade, consulte a [documentação de análise](traffic-analytics.md) de tráfego
