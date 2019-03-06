---
title: Esquema de análise de tráfego do Azure | Documentos da Microsoft
description: Compreenda o esquema de análise de tráfego para analisar registos de fluxo de grupo de segurança de rede do Azure.
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
ms.openlocfilehash: b46a89e331c63aadc76b856b56b7b40bbc626193
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57453070"
---
# <a name="schema-and-data-aggregation-in-traffic-analytics"></a>Agregação de esquema e os dados na análise de tráfego

Análise de tráfego é uma solução baseada na cloud que fornece visibilidade em atividades de utilizador e da aplicação em redes na cloud. Análise de tráfego analisa registos fluxo (NSG) do grupo de segurança de rede de observador de rede para fornecer informações sobre o fluxo de tráfego na cloud do Azure. Com a análise de tráfego, pode:

- Visualize a atividade de rede nas suas subscrições do Azure e identifique hotspots.
- Identificar ameaças de segurança para e proteger a sua rede, com informações como portas abertas, aplicações de acesso à internet e máquinas virtuais (VM) de ligação não autorizados redes a tentar.
- Compreenda os padrões de fluxo de tráfego entre regiões do Azure e a internet para otimizar a sua implementação de rede para o desempenho e a capacidade.
- Identifique os erros de configuração de rede que leva a ligações falhadas na sua rede.
- Sabe a utilização de rede em bytes, pacotes ou fluxos.

### <a name="data-aggregation"></a>Agregação de dados

1. Todos os registos de fluxo num NSG entre "FlowIntervalStartTime_t" e "FlowIntervalEndTime_t" são capturados em intervalos de um minuto na conta de armazenamento, como blobs, antes de ser processado pela análise de tráfego. 
2. Intervalo de processamento de padrão de análise de tráfego é de 60 minutos. Isso significa que cada 60 minutos, a análise de tráfego escolhe blobs de armazenamento para agregação.
3. Protocolo (TCP ou UDP) de camada de fluxos que tenham o mesmo IP de origem, IP de destino, porta de destino, nome do NSG, regra NSG, direção de fluxo e transporte (observação: Porta de origem está excluída para agregação) clubbed num único fluxo pela análise de tráfego
4. Este registo único está decorada (detalhes na secção abaixo) e ingeridos no Log Analytics, análise de tráfego.
5. Campo de FlowStartTime_t indica a primeira ocorrência de tal um fluxo agregado (mesmo quatro cadeias de identificação) no registo de fluxo do processamento de intervalo entre "FlowIntervalStartTime_t" e "FlowIntervalEndTime_t". 
6. Para qualquer recurso no TA, os fluxos do indicado na interface do Usuário são vistos pelo NSG de total de fluxos, mas no Anlaytics de registo de utilizador irá ver apenas o registo único, reduzido. Para ver todos os fluxos, utilize o campo de blob_id, que pode ser referenciado a partir do armazenamento. O fluxo total contar para que o registo irá corresponder os fluxos individuais vistos no blob.


### <a name="fields-used-in-traffic-analytics-schema"></a>Campos utilizados no esquema de análise de tráfego

Análise de tráfego baseia-se se no Log Analytics, para que possa executar consultas personalizadas nos dados decorada por análise de tráfego e definir alertas no mesmo.

Abaixo encontram-se os campos no esquema e o que eles significar

| Campo | Formato | Comentários | 
|:---   |:---    |:---  |
| TableName | AzureNetworkAnalytics_CL | Tabela de dados de tráfego Anlaytics
| SubType_s | FlowLog | Subtipo para os registos de fluxo |
| FASchemaVersion_s |   1   | Versão de Scehma. Não refletem a versão do registo de fluxo de NSG |
| TimeProcessed_t   | Data e hora em UTC  | Tempo em que a análise de tráfego processados os registos de fluxo não processados da conta de armazenamento |
| FlowIntervalStartTime_t | Data e hora em UTC |  Hora de início do intervalo de processamento do registo de fluxo. Esta é a partir do qual será medido o intervalo de fluxo de tempo |
| FlowIntervalEndTime_t | Data e hora em UTC | Terminar em vez do intervalo de processamento do registo de fluxo |
| FlowStartTime_t | Data e hora em UTC |  Primeira ocorrência do fluxo (o que irá obter agregado) no intervalo de processamento de registo de fluxo entre "FlowIntervalStartTime_t" e "FlowIntervalEndTime_t". Este fluxo é agregado com base na lógica de agregação |
| FlowEndTime_t | Data e hora em UTC | Última ocorrência do fluxo (o que irá obter agregado) no intervalo de processamento de registo de fluxo entre "FlowIntervalStartTime_t" e "FlowIntervalEndTime_t". Em termos de fluxo registo v2, este campo contém a hora de início (marcado como "B" no registo de fluxo não processados) o último fluxo com o mesmo quatro cadeias de identificação |
| FlowType_s |  * IntraVNet <br> * Inter-Vnet <br> * S2S <br> * P2S <br> * AzurePublic <br> * ExternalPublic <br> * MaliciousFlow <br> * Privada desconhecido <br> * Desconhecido | Definição nas notas abaixo da tabela |
| SrcIP_s | Endereço IP de origem | Estará em branco em caso de AzurePublic e ExternalPublic fluxos |
| DestIP_s | Endereço IP de destino | Estará em branco em caso de AzurePublic e ExternalPublic fluxos |
| VMIP_s | IP da VM | Utilizado para os fluxos AzurePublic e ExternalPublic |
| PublicIP_S | Endereços IP públicos | Utilizado para os fluxos AzurePublic e ExternalPublic |
| DestPort_d | |Porta de destino| Porta em que o tráfego é recebido | 
| L4Protocol_s  | * T <br> * U  | Protocolo de transporte. T = TCP <br> U = UDP | 
| L7Protocol_s  | Nome de protocolo | Derivado de porta de destino |
| FlowDirection_s | * Posso = de entrada<br> * S = saída | Direção do fluxo de entrada/fora do NSG de acordo com o registo de fluxo | 
| FlowStatus_s  | * A = permitido por regra NSG <br> * 1!d = negado por regra NSG  | Estado do fluxo permitido/nblocked por NSG de acordo com o registo de fluxo |
| NSGList_s | \<SUBSCRIPTIONID>\/<RESOURCEGROUP_NAME>\/<NSG_NAME> | Grupo de segurança de rede (NSG) associados com o fluxo |
| NSGRules_s | \<O valor 0 de índice) >< NSG_RULENAME >\<direção do fluxo >\<estado do fluxo >\<FlowCount ProcessedByRule > |  Regra do NSG que permitido ou negado este fluxo |
| NSGRuleType_s | * Definidas pelo utilizador * predefinido |   O tipo de regra de NSG utilizado pelo fluxo |
| MACAddress_s | Endereço MAC | Endereço MAC da NIC em que o fluxo foi capturado |
| Subscription_s | Subscrição da rede virtual do Azure / interface de rede / máquina virtual é preenchida neste campo | Aplicável apenas para FlowType = tipos de fluxo de S2S, P2S, AzurePublic, ExternalPublic, MaliciousFlow e UnknownPrivate (tipos de fluxo em que apenas um lado é o azure) |
| Subscription1_s | ID da subscrição | ID de subscrição da rede virtual / interface de rede / máquina virtual para que o IP de origem no fluxo pertence a |
| Subscription2_s | ID da subscrição | ID de subscrição da rede virtual / interface de rede / máquina virtual para que o IP de destino no fluxo pertence a |
| Region_s | Região do Azure da rede virtual / interface de rede / máquina virtual para que o IP no fluxo pertence a | Aplicável apenas para FlowType = tipos de fluxo de S2S, P2S, AzurePublic, ExternalPublic, MaliciousFlow e UnknownPrivate (tipos de fluxo em que apenas um lado é o azure) |
| Region1_s | Região do Azure | Região do Azure da rede virtual / interface de rede / máquina virtual para que o IP de origem no fluxo pertence a |
| Region2_s | Região do Azure | Região do Azure da rede virtual à qual o IP de destino no fluxo pertence a |
| NIC_s | \<resourcegroup_Name>\/\<NetworkInterfaceName> |  NIC associado à VM enviar ou receber o tráfego |
| NIC1_s | <resourcegroup_Name>/\<NetworkInterfaceName> | NIC associada com o IP de origem no fluxo |
| NIC2_s | <resourcegroup_Name>/\<NetworkInterfaceName> | NIC associada com o IP de destino no fluxo |
| VM_s | <resourcegroup_Name>\/\<NetworkInterfaceName> | Máquina virtual associada com a interface de rede NIC_s |
| VM1_s | <resourcegroup_Name>/\<VirtualMachineName> | Máquina virtual associada com o IP de origem no fluxo |
| VM2_s | <resourcegroup_Name>/\<VirtualMachineName> | Máquina virtual associada com o IP de destino no fluxo |
| Subnet_s | <ResourceGroup_Name>/<VNET_Name>/\<SubnetName> | Sub-rede associado a NIC_s |
| Subnet1_s | <ResourceGroup_Name>/<VNET_Name>/\<SubnetName> | Sub-rede associada com o IP de origem no fluxo |
| Subnet2_s | <ResourceGroup_Name>/<VNET_Name>/\<SubnetName>    | Sub-rede associada com o IP de destino no fluxo |
| ApplicationGateway1_s | \<SubscriptionID>/\<ResourceGroupName>/\<ApplicationGatewayName> | Gateway de aplicação associado com o IP de origem no fluxo | 
| ApplicationGateway2_s | \<SubscriptionID>/\<ResourceGroupName>/\<ApplicationGatewayName> | Gateway de aplicação associado com o IP de destino no fluxo |
| LoadBalancer1_s | \<SubscriptionID>/\<ResourceGroupName>/\<LoadBalancerName> | Balanceador de carga associado com o IP de origem no fluxo |
| LoadBalancer2_s | \<SubscriptionID>/\<ResourceGroupName>/\<LoadBalancerName> | Balanceador de carga associado com o IP de destino no fluxo |
| LocalNetworkGateway1_s | \<SubscriptionID>/\<ResourceGroupName>/\<LocalNetworkGatewayName> | Gateway de rede local associado com o IP de origem no fluxo |
| LocalNetworkGateway2_s | \<SubscriptionID>/\<ResourceGroupName>/\<LocalNetworkGatewayName> | Gateway de rede local associado com o IP de destino no fluxo |
| ConnectionType_s | Os valores possíveis são VNetPeering VpnGateway e ExpressRoute |    Tipo de Ligação |
| ConnectionName_s | \<SubscriptionID>/\<ResourceGroupName>/\<ConnectionName> | Nome da Ligação |
| ConnectingVNets_s | Lista de separados de espaço de nomes de rede virtual | Em caso de topologia hub- and -spoke, redes virtuais de hub serão preenchidas aqui |
| Country_s | Duas letras indicativo de país (ISO 3166-1 alpha-2) | Preenchido para o tipo de fluxo ExternalPublic. Todos os endereços IP no campo de PublicIPs_s irão partilhar o mesmo código de país |
| AzureRegion_s | Localizações de região do Azure | Preenchido para o tipo de fluxo AzurePublic. Todos os endereços IP no campo de PublicIPs_s irão partilhar a região do Azure |
| AllowedInFlows_d | | Contagem de fluxos de entrada que foram permissão. Isso representa o número de fluxos que partilhada a mesma quatro cadeias de identificação de entrada para a interface de netweork em que o fluxo foi capturado | 
| DeniedInFlows_d |  | Contagem de fluxos de entrada que foram negados. (De entrada para a interface de rede em que o fluxo foi capturado) |
| AllowedOutFlows_d | | Contagem de fluxos de saída que foram permitidas (para a interface de rede em que foi capturado o fluxo de saída) |
| DeniedOutFlows_d  | | Contagem de fluxos de saída que foram negados (para a interface de rede em que foi capturado o fluxo de saída) |
| FlowCount_d | Preterido. Total de fluxos que correspondem a mesma quatro cadeias de identificação. Em caso de tipos de fluxo ExternalPublic e AzurePublic, contagem incluirá os fluxos a partir de vários endereços de PublicIP também.
| InboundPackets_d | Pacotes recebidos como capturados a interface de rede em que foi aplicada a regra NSG | Ela é preenchida, apenas para o esquema de registo do fluxo de versão 2 do NSG |
| OutboundPackets_d  | Pacotes enviados como capturados a interface de rede em que foi aplicada a regra NSG | Ela é preenchida, apenas para o esquema de registo do fluxo de versão 2 do NSG |
| InboundBytes_d |  Bytes recebidos como capturados a interface de rede em que foi aplicada a regra NSG | Ela é preenchida, apenas para o esquema de registo do fluxo de versão 2 do NSG |
| OutboundBytes_d | Bytes enviados como capturados a interface de rede em que foi aplicada a regra NSG | Ela é preenchida, apenas para o esquema de registo do fluxo de versão 2 do NSG |
| CompletedFlows_d  |  | Ele é populado com valor diferente de zero apenas para o esquema de registo do fluxo de versão 2 do NSG |
| PublicIPs_s | <PUBLIC_IP>\|\<FLOW_STARTED_COUNT>\|\<FLOW_ENDED_COUNT>\|\<OUTBOUND_PACKETS>\|\<INBOUND_PACKETS>\|\<OUTBOUND_BYTES>\|\<INBOUND_BYTES> | Entradas separada por barras |
    
### <a name="notes"></a>Notas
    
1. Em caso de fluxos AzurePublic e ExternalPublic, o cliente propriedade que IP da VM do Azure é preenchida no campo de VMIP_s, enquanto os endereços IP públicos estão a ser preenchidos no campo PublicIPs_s. Para estes tipos de fluxo de dois, devemos usar VMIP_s e PublicIPs_s em vez de campos SrcIP_s e DestIP_s. Para endereços AzurePublic e ExternalPublicIP, podemos agregar ainda mais, para que o número de registos ingeridos a área de trabalho de análise de registo de cliente é mínimo. (Este campo vai ser preterido em breve e deve estar a utilizar SrcIP_ e DestIP_s dependendo se a VM do azure foi a origem ou o destino no fluxo) 
2. Detalhes para tipos de fluxo: Com base nos endereços IP envolvidos no fluxo, vamos categorizar os fluxos para os seguintes tipos de fluxo: 
* IntraVNet – os endereços IP no fluxo residirem na mesma rede Virtual do Azure. 
* Inter-Vnet - endereços IP no fluxo de residir na duas diferentes redes virtuais do Azure. 
* S2S – (Site a Site) de um dos endereços IP pertence a rede Virtual do Azure enquanto o outro endereço IP pertence a rede de cliente (Site), ligado à rede Virtual do Azure através do gateway VPN ou Expressroute. 
* P2S - (ponto a Site) um dos endereços IP pertence a rede Virtual do Azure enquanto o outro endereço IP pertence a rede de cliente (Site) ligado à rede Virtual do Azure através do gateway VPN.
* AzurePublic - um dos endereços IP pertence a rede Virtual do Azure enquanto o outro endereço IP pertence a endereços IP públicos interno do Azure pertencentes à Microsoft. Cliente pertence a endereços IP públicos não serão parte deste tipo de fluxo. Por exemplo, todos os clientes de dispositivos VM enviar o tráfego para um serviço do Azure (ponto final de armazenamento) poderia ser categorizada sob este tipo de fluxo. 
* ExternalPublic - um dos endereços IP pertence a rede Virtual do Azure enquanto o outro endereço IP é um IP público que não se encontra no Azure, não é reportada como malicioso em feeds o ASC que consome de análise de tráfego para o intervalo de processamento entre " FlowIntervalStartTime_t"e"FlowIntervalEndTime_t". 
* MaliciousFlow - um dos endereços IP pertencem à rede virtual do azure enquanto o outro endereço IP é um IP público que não se encontra no Azure e é comunicado como malicioso em feeds o ASC que consome de análise de tráfego para o intervalo de processamento entre" FlowIntervalStartTime_t"e"FlowIntervalEndTime_t". 
* UnknownPrivate - um dos endereços IP pertence à rede Virtual do Azure enquanto o outro endereço IP pertence ao intervalo de IP privados, conforme definido na RFC 1918 e não foi possível mapear pela análise de tráfego para um cliente propriedade site ou a rede Virtual do Azure.
* Desconhecido – não é possível mapear a qualquer um dos endereços IP nos fluxos com a topologia de cliente no Azure, bem como no local (site).

### <a name="next-steps"></a>Próximos Passos
Para obter respostas às perguntas mais frequentes, consulte [FAQ da análise de tráfego](traffic-analytics-faq.md) para ver detalhes sobre a funcionalidade, consulte [documentação da análise de tráfego](traffic-analytics.md)
    


    


