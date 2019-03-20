---
title: Introdução aos recursos de resolução de problemas no observador de rede do Azure | Documentos da Microsoft
description: Esta página fornece uma descrição geral das capacidades de resolução de problemas de recurso de observador de rede
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.assetid: c1145cd6-d1cf-4770-b1cc-eaf0464cc315
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: jdial
ms.openlocfilehash: aa7fce21228d4413dc4964d6e828bf60478aee27
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57901836"
---
# <a name="introduction-to-resource-troubleshooting-in-azure-network-watcher"></a>Introdução aos recursos de resolução de problemas no observador de rede do Azure

Gateways de rede virtual fornecer conectividade entre recursos no local e a outras redes virtuais no Azure. Monitorizar os gateways e respetivas ligações é fundamental para garantir que a comunicação não é interrompida. Observador de rede fornece a capacidade de resolver problemas de ligações e gateways. O recurso pode ser chamado através do portal, o PowerShell, a CLI do Azure ou a REST API. Quando chamado, o observador de rede diagnostica o estado de funcionamento do gateway ou ligação e devolve os resultados apropriados. O pedido é uma transação de longa execução. Os resultados devolvidos assim que o diagnóstico esteja concluído.

![portal][2]

## <a name="results"></a>Resultados

Os resultados preliminares devolvidos dar uma visão geral do Estado de funcionamento do recurso. Informações mais aprofundadas podem ser fornecidas para recursos, conforme mostrado na seção a seguir:

A lista seguinte é os valores devolvidos com a API de resolução de problemas:

* **startTime** -este valor é a hora de início da chamada de API de resolução de problemas.
* **endTime** -este valor é a hora quando a solução de problemas terminou.
* **código** -este valor é mau estado de funcionamento, se ocorrer uma falha de diagnóstico único.
* **resultados** -resultados é um conjunto de resultados devolvidos na ligação ou o gateway de rede virtual.
    * **ID** -este valor é o tipo de falha.
    * **resumo** -este valor é um resumo sobre a falha.
    * **detalhadas** -este valor fornece uma descrição detalhada sobre a falha.
    * **recommendedActions** -esta propriedade é uma coleção de ações recomendadas.
      * **actionText** -este valor contém o texto que descreve a ação a tomar.
      * **actionUri** -este valor fornece o URI para a documentação sobre como agir.
      * **actionUriText** -este valor é uma breve descrição do texto de ação.

As tabelas seguintes mostram os tipos de falhas diferentes (id em resultados na lista anterior) que estão disponíveis e, se a falha cria registos.

### <a name="gateway"></a>Gateway

| Tipo de falha | Razão | Registar|
|---|---|---|
| NoFault | Quando não é detetado nenhum erro |Sim|
| GatewayNotFound | Não é possível encontrar o gateway ou gateway não está aprovisionada |Não|
| PlannedMaintenance |  Instância de gateway está em manutenção  |Não|
| UserDrivenUpdate | Esta falha ocorre quando uma atualização de utilizador está em curso. A atualização pode ser uma operação de redimensionamento. | Não |
| VipUnResponsive | Esta falha ocorre quando a instância principal do gateway não pode ser alcançada devido a uma falha de sonda de estado de funcionamento. | Não |
| PlatformInActive | Existe um problema com a plataforma. | Não|
| ServiceNotRunning | O serviço subjacente não está em execução. | Não|
| NoConnectionsFoundForGateway | Sem ligações existem no gateway. Esta falha é apenas um aviso.| Não|
| ConnectionsNotConnected | Ligações não estão ligadas. Esta falha é apenas um aviso.| Sim|
| GatewayCPUUsageExceeded | O gateway atual, a utilização da CPU é > 95%. | Sim |

### <a name="connection"></a>Ligação

| Tipo de falha | Razão | Registar|
|---|---|---|
| NoFault | Quando não é detetado nenhum erro |Sim|
| GatewayNotFound | Não é possível encontrar o gateway ou gateway não está aprovisionada |Não|
| PlannedMaintenance | Instância de gateway está em manutenção  |Não|
| UserDrivenUpdate | Esta falha ocorre quando uma atualização de utilizador está em curso. A atualização pode ser uma operação de redimensionamento.  | Não |
| VipUnResponsive | Esta falha ocorre quando a instância principal do gateway não pode ser alcançada devido a uma falha de sonda de estado de funcionamento. | Não |
| ConnectionEntityNotFound | Configuração de ligação está em falta | Não |
| ConnectionIsMarkedDisconnected | A ligação foi marcada como "desligada" |Não|
| ConnectionNotConfiguredOnGateway | O serviço subjacente não tem uma ligação configurada. | Sim |
| ConnectionMarkedStandby | O serviço subjacente é marcado como modo de espera.| Sim|
| Authentication | Erro de correspondência de chave pré-partilhada | Sim|
| PeerReachability | O gateway de ponto a ponto não está acessível. | Sim|
| IkePolicyMismatch | O gateway de mesmo nível tem políticas de IKE que não são suportadas pelo Azure. | Sim|
| WfpParse Error | Ocorreu um erro ao analisar o registo da WFP. |Sim|

## <a name="supported-gateway-types"></a>Tipos de Gateway suportados

A tabela seguinte lista os gateways e ligações são suportadas na resolução de problemas do observador de rede:

|  |  |
|---------|---------|
|**Tipos de gateway**   |         |
|VPN      | Suportadas        |
|ExpressRoute | Não suportado |
|**Tipos de VPN** | |
|Rota com base | Suportadas|
|Baseado na política | Não suportado|
|**Tipos de ligação**||
|IPSec| Suportadas|
|VNet2Vnet| Suportadas|
|ExpressRoute| Não suportado|
|VPNClient| Não suportado|

## <a name="log-files"></a>Ficheiros de registo

Os ficheiros de registo de resolução de problemas de recursos são armazenados numa conta de armazenamento após a conclusão da resolução de problemas de recursos. A imagem seguinte mostra os conteúdos de exemplo de uma chamada que resultou num erro.

![ficheiro zip][1]

> [!NOTE]
> Em alguns casos, apenas um subconjunto dos ficheiros de registo é escrito para o armazenamento.

Para obter instruções sobre o download de arquivos de contas de armazenamento do azure, consulte [introdução ao armazenamento de Blobs do Azure com o .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Outra ferramenta que pode ser utilizada é o Explorador de armazenamento. Obter mais informações sobre o Explorador de armazenamento podem ser encontradas aqui na seguinte hiperligação: [Explorador de armazenamento](https://storageexplorer.com/)

### <a name="connectionstatstxt"></a>ConnectionStats.txt

O **ConnectionStats.txt** arquivo contém estatísticas geral da ligação, incluindo bytes de entrada e saída, o estado da ligação e a hora a ligação foi estabelecida.

> [!NOTE]
> Se a chamada para a resolução de problemas API devolve em bom estado, a única coisa que devolvido no ficheiro zip é um **ConnectionStats.txt** ficheiro.

O conteúdo deste ficheiro é semelhante ao seguinte exemplo:

```
Connectivity State : Connected
Remote Tunnel Endpoint :
Ingress Bytes (since last connected) : 288 B
Egress Bytes (Since last connected) : 288 B
Connected Since : 2/1/2017 8:22:06 PM
```

### <a name="cpustatstxt"></a>CPUStats.txt

O **CPUStats.txt** ficheiro contém a utilização da CPU e memória disponível no momento de teste.  O conteúdo deste ficheiro é semelhante ao seguinte exemplo:

```
Current CPU Usage : 0 % Current Memory Available : 641 MBs
```

### <a name="ikeerrorstxt"></a>IKEErrors.txt

O **IKEErrors.txt** arquivo contém quaisquer erros de IKE que foram encontrados durante a monitorização.

O exemplo seguinte mostra o conteúdo de um arquivo de IKEErrors.txt. Os erros podem ser diferentes dependendo do problema.

```
Error: Authentication failed. Check shared key. Check crypto. Check lifetimes. 
     based on log : Peer failed with Windows error 13801(ERROR_IPSEC_IKE_AUTH_FAIL)
Error: On-prem device sent invalid payload. 
     based on log : IkeFindPayloadInPacket failed with Windows error 13843(ERROR_IPSEC_IKE_INVALID_PAYLOAD)
```

### <a name="scrubbed-wfpdiagtxt"></a>Scrubbed-wfpdiag.txt

O **Scrubbed wfpdiag.txt** ficheiro de registo contém o registo da wfp. Este registo contém o registo de largada de pacotes e falhas de IKE/AuthIP.

O exemplo seguinte mostra o conteúdo do arquivo Scrubbed wfpdiag.txt. Neste exemplo, a chave partilhada de uma conexão não estava correta como pode ser visto na terceira linha na parte inferior de. O exemplo seguinte é apenas um fragmento de todo o registo, como o registo pode ser demorado, dependendo do problema.

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

O **wfpdiag.txt.sum** ficheiro é um registo que mostra os eventos processados e buffers.

O exemplo seguinte é o conteúdo de um ficheiro de wfpdiag.txt.sum.
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

## <a name="next-steps"></a>Passos Seguintes

Para saber como diagnosticar um problema com um gateway ou a ligação de gateway, veja [diagnosticar problemas de comunicação entre redes](diagnose-communication-problem-between-networks.md).
<!--Image references-->

[1]: ./media/network-watcher-troubleshoot-overview/GatewayTenantWorkerLogs.png
[2]: ./media/network-watcher-troubleshoot-overview/portal.png
