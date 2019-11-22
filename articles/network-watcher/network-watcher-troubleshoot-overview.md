---
title: Introdução à solução de problemas de recursos
titleSuffix: Azure Network Watcher
description: Esta página fornece uma visão geral dos recursos de solução de problemas de recursos do observador de rede
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: c1145cd6-d1cf-4770-b1cc-eaf0464cc315
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: kumud
ms.openlocfilehash: 736bbd16456dd0abda3292b9b9e73ea5b941e7ed
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277780"
---
# <a name="introduction-to-resource-troubleshooting-in-azure-network-watcher"></a>Introdução à solução de problemas de recursos no observador de rede do Azure

Os gateways de rede virtual fornecem conectividade entre os recursos locais e outras redes virtuais no Azure. Monitorizar os gateways e respetivas ligações é fundamental para garantir que a comunicação não é interrompida. O observador de rede fornece a capacidade de solucionar problemas de gateways e conexões. O recurso pode ser chamado por meio do portal, do PowerShell, do CLI do Azure ou da API REST. Quando chamado, o observador de rede diagnostica a integridade do gateway, ou a conexão, e retorna os resultados apropriados. A solicitação é uma transação de longa execução. Os resultados são retornados quando o diagnóstico é concluído.

![portal][2]

## <a name="results"></a>Resultados

Os resultados preliminares retornados fornecem uma visão geral da integridade do recurso. Informações mais profundas podem ser fornecidas para os recursos, conforme mostrado na seção a seguir:

A lista a seguir é os valores retornados com a API de solução de problemas:

* **StartTime** -esse valor é a hora em que a chamada à API de solução de problemas foi iniciada.
* **EndTime** -esse valor é a hora em que a solução de problemas terminou.
* **código** -esse valor não está íntegro, se houver uma única falha de diagnóstico.
* **resultados** -resultados é uma coleção de resultados retornados na conexão ou no gateway de rede virtual.
    * **ID** -esse valor é o tipo de falha.
    * **Resumo** -esse valor é um resumo da falha.
    * **detalhado** – esse valor fornece uma descrição detalhada da falha.
    * **recommendedActions** -essa propriedade é uma coleção de ações recomendadas a serem executadas.
      * **actionText** -esse valor contém o texto que descreve a ação a ser tomada.
      * **actionUri** -esse valor fornece o URI para a documentação sobre como agir.
      * **actionUriText** -esse valor é uma breve descrição do texto da ação.

As tabelas a seguir mostram os diferentes tipos de falha (ID em resultados da lista anterior) que estão disponíveis e se a falha cria logs.

### <a name="gateway"></a>Gateway

| Tipo de falha | Razão | Registar|
|---|---|---|
| Nofault | Quando nenhum erro é detectado |Sim|
| GatewayNotFound | Não é possível localizar o gateway ou o gateway não está provisionado |Não|
| PlannedMaintenance |  A instância do gateway está em manutenção  |Não|
| UserDrivenUpdate | Essa falha ocorre quando uma atualização do usuário está em andamento. A atualização pode ser uma operação de redimensionamento. | Não |
| VipUnResponsive | Essa falha ocorre quando a instância primária do gateway não pode ser acessada devido a uma falha de investigação de integridade. | Não |
| PlatformInActive | Há um problema com a plataforma. | Não|
| ServiceNotRunning | O serviço subjacente não está em execução. | Não|
| NoConnectionsFoundForGateway | Não existem conexões no gateway. Essa falha é apenas um aviso.| Não|
| ConnectionsNotConnected | As conexões não estão conectadas. Essa falha é apenas um aviso.| Sim|
| GatewayCPUUsageExceeded | O uso da CPU do gateway atual é > 95%. | Sim |

### <a name="connection"></a>Ligação

| Tipo de falha | Razão | Registar|
|---|---|---|
| Nofault | Quando nenhum erro é detectado |Sim|
| GatewayNotFound | Não é possível localizar o gateway ou o gateway não está provisionado |Não|
| PlannedMaintenance | A instância do gateway está em manutenção  |Não|
| UserDrivenUpdate | Essa falha ocorre quando uma atualização do usuário está em andamento. A atualização pode ser uma operação de redimensionamento.  | Não |
| VipUnResponsive | Essa falha ocorre quando a instância primária do gateway não pode ser acessada devido a uma falha de investigação de integridade. | Não |
| ConnectionEntityNotFound | A configuração da conexão está ausente | Não |
| ConnectionIsMarkedDisconnected | A conexão está marcada como "desconectada" |Não|
| ConnectionNotConfiguredOnGateway | O serviço subjacente não tem a conexão configurada. | Sim |
| ConnectionMarkedStandby | O serviço subjacente está marcado como em espera.| Sim|
| Autenticação | Incompatibilidade de chave pré-compartilhada | Sim|
| PeerReachability | O gateway de mesmo nível não está acessível. | Sim|
| IkePolicyMismatch | O gateway par tem políticas IKE que não são suportadas pelo Azure. | Sim|
| WfpParse Error | Ocorreu um erro ao analisar o log WFP. |Sim|

## <a name="supported-gateway-types"></a>Tipos de gateway com suporte

A tabela a seguir lista quais gateways e conexões têm suporte com a solução de problemas do observador de rede:

|  |  |
|---------|---------|
|**Tipos de gateway**   |         |
|VPN      | Suportado        |
|ExpressRoute | Não Suportado |
|**Tipos de VPN** | |
|Baseado em rota | Suportado|
|Baseado em política | Não Suportado|
|**Tipos de conexão**||
|IPSec| Suportado|
|VNet2Vnet| Suportado|
|ExpressRoute| Não Suportado|
|VPNClient| Não Suportado|

## <a name="log-files"></a>Ficheiros de registo

Os arquivos de log de solução de problemas de recursos são armazenados em uma conta de armazenamento após a conclusão da solução de problemas de recursos. A imagem a seguir mostra o conteúdo de exemplo de uma chamada que resultou em um erro.

![arquivo zip][1]

> [!NOTE]
> Em alguns casos, apenas um subconjunto dos arquivos de log é gravado no armazenamento.

Para obter instruções sobre como baixar arquivos de contas de armazenamento do Azure, consulte Introdução ao [armazenamento de BLOBs do Azure usando o .net](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Outra ferramenta que pode ser usada é Gerenciador de Armazenamento. Mais informações sobre Gerenciador de Armazenamento podem ser encontradas aqui no seguinte link: [Gerenciador de armazenamento](https://storageexplorer.com/)

### <a name="connectionstatstxt"></a>ConnectionStats.txt

O arquivo **ConnectionStats. txt** contém estatísticas gerais da conexão, incluindo bytes de entrada e saída, status da conexão e a hora em que a conexão foi estabelecida.

> [!NOTE]
> Se a chamada para a API de solução de problemas retornar íntegro, a única coisa retornada no arquivo zip será um arquivo **ConnectionStats. txt** .

O conteúdo desse arquivo é semelhante ao exemplo a seguir:

```
Connectivity State : Connected
Remote Tunnel Endpoint :
Ingress Bytes (since last connected) : 288 B
Egress Bytes (Since last connected) : 288 B
Connected Since : 2/1/2017 8:22:06 PM
```

### <a name="cpustatstxt"></a>CPUStats.txt

O arquivo **CPUStats. txt** contém uso de CPU e memória disponível no momento do teste.  O conteúdo desse arquivo é semelhante ao exemplo a seguir:

```
Current CPU Usage : 0 % Current Memory Available : 641 MBs
```

### <a name="ikeerrorstxt"></a>IKEErrors. txt

O arquivo **IKEErrors. txt** contém erros de Ike que foram encontrados durante o monitoramento.

O exemplo a seguir mostra o conteúdo de um arquivo IKEErrors. txt. Seus erros podem ser diferentes dependendo do problema.

```
Error: Authentication failed. Check shared key. Check crypto. Check lifetimes. 
     based on log : Peer failed with Windows error 13801(ERROR_IPSEC_IKE_AUTH_FAIL)
Error: On-prem device sent invalid payload. 
     based on log : IkeFindPayloadInPacket failed with Windows error 13843(ERROR_IPSEC_IKE_INVALID_PAYLOAD)
```

### <a name="scrubbed-wfpdiagtxt"></a>Scrubbed-wfpdiag.txt

O arquivo de log **scrubbed-Wfpdiag. txt** contém o log WFP. Esse log contém o registro em log do descarte de pacotes e falhas de IKE/AuthIP.

O exemplo a seguir mostra o conteúdo do arquivo scrubbed-Wfpdiag. txt. Neste exemplo, a chave compartilhada de uma conexão não estava correta, pois pode ser vista na terceira linha da parte inferior. O exemplo a seguir é apenas um trecho de todo o log, pois o log pode ser demorado dependendo do problema.

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

O arquivo **arquivo Wfpdiag. txt. Sum** é um log que mostra os buffers e os eventos processados.

O exemplo a seguir é o conteúdo de um arquivo arquivo Wfpdiag. txt. Sum.
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

Para saber como diagnosticar um problema com uma conexão de gateway ou gateway, consulte [diagnosticar problemas de comunicação entre redes](diagnose-communication-problem-between-networks.md).
<!--Image references-->

[1]: ./media/network-watcher-troubleshoot-overview/GatewayTenantWorkerLogs.png
[2]: ./media/network-watcher-troubleshoot-overview/portal.png
