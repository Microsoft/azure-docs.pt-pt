---
title: Introdução à resolução de problemas de recursos
titleSuffix: Azure Network Watcher
description: Esta página fornece uma visão geral das capacidades de resolução de problemas de recursos do Observador de Rede
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: damendo
ms.openlocfilehash: 199b4fc762919c2e3988f477c14d09fc23b0136b
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76840694"
---
# <a name="introduction-to-resource-troubleshooting-in-azure-network-watcher"></a>Introdução à resolução de problemas de recursos no Vigilante da Rede Azure

Os Gateways de Rede Virtual proporcionam conectividade entre os recursos no local e outras redes virtuais dentro do Azure. Monitorizar os gateways e respetivas ligações é fundamental para garantir que a comunicação não é interrompida. O Network Watcher fornece a capacidade de resolver os gateways e as ligações. A capacidade pode ser chamada através do portal PowerShell, Azure CLI ou REST API. Quando chamado, o Observador de Rede diagnostica a saúde do portal, ou ligação, e devolve os resultados apropriados. O pedido é uma transação de longa duração. Os resultados são devolvidos assim que o diagnóstico estiver concluído.

![portal][2]

## <a name="results"></a>Resultados

Os resultados preliminares devolvidos dão uma imagem geral da saúde do recurso. Informações mais profundas podem ser fornecidas para os recursos, tal como demonstrado na seguinte secção:

A seguinte lista são os valores devolvidos com a API problemática:

* **inícioTempo** - Este valor é o momento em que a chamada da API começou.
* **tempo final** - Este valor é o momento em que a resolução de problemas terminou.
* **código** - Este valor é Insalubre, se houver uma única falha de diagnóstico.
* **resultados** - Resultados é uma recolha de resultados devolvidos na Ligação ou no portal da rede virtual.
    * **id** - Este valor é o tipo de avaria.
    * **resumo** - Este valor é um resumo da falha.
    * **detalhado** - Este valor fornece uma descrição detalhada da falha.
    * **ações recomendadas** - Esta propriedade é uma coleção de ações recomendadas a tomar.
      * **actionText** - Este valor contém o texto que descreve que medidas tomar.
      * **actionUri** - Este valor fornece o URI à documentação sobre como agir.
      * **actionUriText** - Este valor é uma breve descrição do texto de ação.

As tabelas a seguir mostram os diferentes tipos de avarias (id em resultados da lista anterior) que estão disponíveis e se a falha criar registos.

### <a name="gateway"></a>Gateway

| Tipo de avaria | Razão | Registo|
|---|---|---|
| NoFault | Quando não é detetado nenhum erro |Sim|
| GatewayNotFound | Não é possível encontrar gateway ou gateway não é provisionado |Não|
| Manutenção Planeada |  A instância gateway está sob manutenção  |Não|
| UserDrivenUpdate | Esta falha ocorre quando uma atualização do utilizador está em andamento. A atualização pode ser uma operação de redimensionação. | Não |
| VipUnResponsive | Esta falha ocorre quando a instância primária do portal não pode ser alcançada devido a uma falha na sonda de saúde. | Não |
| PlataformaInActive | Há um problema com a plataforma. | Não|
| ServiceNotRunning | O serviço subjacente não está em execução. | Não|
| NoConnectionsFoundForGateway | Não existem ligações na porta de entrada. Esta falha é apenas um aviso.| Não|
| ConnectionsNotConnected | As ligações não estão ligadas. Esta falha é apenas um aviso.| Sim|
| GatewayCPUUsageExceeded | O atual uso do CPU de gateway é > 95%. | Sim |

### <a name="connection"></a>Ligação

| Tipo de avaria | Razão | Registo|
|---|---|---|
| NoFault | Quando não é detetado nenhum erro |Sim|
| GatewayNotFound | Não é possível encontrar gateway ou gateway não é provisionado |Não|
| Manutenção Planeada | A instância gateway está sob manutenção  |Não|
| UserDrivenUpdate | Esta falha ocorre quando uma atualização do utilizador está em andamento. A atualização pode ser uma operação de redimensionação.  | Não |
| VipUnResponsive | Esta falha ocorre quando a instância primária do portal não pode ser alcançada devido a uma falha na sonda de saúde. | Não |
| ConnectionEntityNotFound | Falta a configuração da ligação | Não |
| ConnectionIsMarkedDisconnected | A ligação está marcada como "desligada" |Não|
| ConnectionNotConfiguredOnGateway | O serviço subjacente não tem a ligação configurada. | Sim |
| ConnectionMarkedStandby | O serviço subjacente está marcado como em espera.| Sim|
| Autenticação | Desfasamento da chave pré-partilhada | Sim|
| PeerReachability | O gateway de mesmo nível não está acessível. | Sim|
| IkePolicyMismatch | O gateway par tem políticas IKE que não são suportadas pelo Azure. | Sim|
| WfpParse Error | Ocorreu um erro ao analisar o log WFP. |Sim|

## <a name="supported-gateway-types"></a>Tipos de Gateway suportados

As seguintes listas de tabelas que gateways e ligações são suportadas com resolução de problemas do Observador de Rede:

|  |  |
|---------|---------|
|**Tipos de gateway**   |         |
|VPN      | Suportadas        |
|ExpressRoute | Não suportado |
|**Tipos VPN** | |
|Baseado em Rota | Suportadas|
|Baseado em Políticas | Não suportado|
|**Tipos de ligação**||
|IPSec| Suportadas|
|VNet2Vnet| Suportadas|
|ExpressRoute| Não suportado|
|VPNClient| Não suportado|

## <a name="log-files"></a>Ficheiros de registo

Os ficheiros de registo de resolução de problemas de recursos são armazenados numa conta de armazenamento após a resolução de problemas de recursos. A imagem que se segue mostra o conteúdo de uma chamada que resultou num erro.

![arquivo zip][1]

> [!NOTE]
> Em alguns casos, apenas um subconjunto dos ficheiros de registos é escrito para armazenamento.

Para obter instruções sobre o download de ficheiros de contas de armazenamento azure, consulte o Get started com o [armazenamento Azure Blob utilizando .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Outra ferramenta que pode ser usada é o Storage Explorer. Mais informações sobre o Storage Explorer podem ser encontradas aqui no seguinte link: [Storage Explorer](https://storageexplorer.com/)

### <a name="connectionstatstxt"></a>ConnectionStats.txt

O ficheiro **ConnectionStats.txt** contém estatísticas globais da Ligação, incluindo bytes de entrada e saída, estado de ligação e o tempo em que a Ligação foi estabelecida.

> [!NOTE]
> Se a chamada para a Resolução de Problemas a API for saudável, a única coisa devolvida no ficheiro zip é um ficheiro **ConnectionStats.txt.**

O conteúdo deste ficheiro é semelhante ao seguinte exemplo:

```
Connectivity State : Connected
Remote Tunnel Endpoint :
Ingress Bytes (since last connected) : 288 B
Egress Bytes (Since last connected) : 288 B
Connected Since : 2/1/2017 8:22:06 PM
```

### <a name="cpustatstxt"></a>CPUStats.txt

O ficheiro **CPUStats.txt** contém a utilização e a memória do CPU disponíveis no momento do teste.  O conteúdo deste ficheiro é semelhante ao seguinte exemplo:

```
Current CPU Usage : 0 % Current Memory Available : 641 MBs
```

### <a name="ikeerrorstxt"></a>IKEErrors.txt

O ficheiro **IKEErrors.txt** contém quaisquer erros IKE que tenham sido encontrados durante a monitorização.

O exemplo que se segue mostra o conteúdo de um ficheiro IKEErrors.txt. Os seus erros podem ser diferentes dependendo do problema.

```
Error: Authentication failed. Check shared key. Check crypto. Check lifetimes. 
     based on log : Peer failed with Windows error 13801(ERROR_IPSEC_IKE_AUTH_FAIL)
Error: On-prem device sent invalid payload. 
     based on log : IkeFindPayloadInPacket failed with Windows error 13843(ERROR_IPSEC_IKE_INVALID_PAYLOAD)
```

### <a name="scrubbed-wfpdiagtxt"></a>Scrubbed-wfpdiag.txt

O ficheiro de registo **Scrubbed-wfpdiag.txt** contém o registo do wfp. Este registo contém registo de gotas de pacote e falhas IKE/AuthIP.

O exemplo seguinte mostra o conteúdo do ficheiro Scrubbed-wfpdiag.txt. Neste exemplo, a chave partilhada de uma Ligação não estava correta como se pode ver a partir da terceira linha a partir da parte inferior. O exemplo seguinte é apenas um corte de todo o registo, uma vez que o registo pode ser longo dependendo da questão.

```
...
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|Deleted ICookie from the high priority thread pool list
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|IKE diagnostic event:
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|Event Header:
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Timestamp: 1601-01-01T00:00:00.000Z
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Flags: 0x00000106
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|    Local address field set
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|    Remote address field set
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|    IP version field set
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  IP version: IPv4
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  IP protocol: 0
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Local address: 13.78.238.92
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Remote address: 52.161.24.36
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Local Port: 0
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Remote Port: 0
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Application ID:
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  User SID: <invalid>
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|Failure type: IKE/Authip Main Mode Failure
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|Type specific info:
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Failure error code:0x000035e9
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|    IKE authentication credentials are unacceptable
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Failure point: Remote
...
```

### <a name="wfpdiagtxtsum"></a>wfpdiag.txt.sum

O ficheiro **wfpdiag.txt.sum** é um registo que mostra os amortecedores e eventos processados.

O exemplo seguinte é o conteúdo de um ficheiro wfpdiag.txt.sum.
```
Files Processed:
    C:\Resources\directory\924336c47dd045d5a246c349b8ae57f2.GatewayTenantWorker.DiagnosticsStorage\2017-02-02T17-34-23\wfpdiag.etl
Total Buffers Processed 8
Total Events  Processed 2169
Total Events  Lost      0
Total Format  Errors    0
Total Formats Unknown   486
Elapsed Time            330 sec
+-----------------------------------------------------------------------------------+
|EventCount    EventName            EventType   TMF                                 |
+-----------------------------------------------------------------------------------+
|        36    ikeext               ike_addr_utils_c844  a0c064ca-d954-350a-8b2f-1a7464eef8b6|
|        12    ikeext               ike_addr_utils_c857  a0c064ca-d954-350a-8b2f-1a7464eef8b6|
|        96    ikeext               ike_addr_utils_c832  a0c064ca-d954-350a-8b2f-1a7464eef8b6|
|         6    ikeext               ike_bfe_callbacks_c133  1dc2d67f-8381-6303-e314-6c1452eeb529|
|         6    ikeext               ike_bfe_callbacks_c61  1dc2d67f-8381-6303-e314-6c1452eeb529|
|        12    ikeext               ike_sa_management_c5698  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|         6    ikeext               ike_sa_management_c8447  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|        12    ikeext               ike_sa_management_c494  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|        12    ikeext               ike_sa_management_c642  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|         6    ikeext               ike_sa_management_c3162  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|        12    ikeext               ike_sa_management_c3307  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
```

## <a name="next-steps"></a>Passos seguintes

Para aprender a diagnosticar um problema com uma ligação de gateway ou gateway, consulte diagnosticar problemas de [comunicação entre redes](diagnose-communication-problem-between-networks.md).
<!--Image references-->

[1]: ./media/network-watcher-troubleshoot-overview/GatewayTenantWorkerLogs.png
[2]: ./media/network-watcher-troubleshoot-overview/portal.png
