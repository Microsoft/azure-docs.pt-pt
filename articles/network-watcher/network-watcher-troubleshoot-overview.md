---
title: Introdução à resolução de problemas de recursos
titleSuffix: Azure Network Watcher
description: Esta página fornece uma visão geral das capacidades de resolução de problemas de recursos do Observador de Rede
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: damendo
ms.openlocfilehash: 0d0597c2df8731171505a090de6959d8a112c004
ms.sourcegitcommit: 9d9221ba4bfdf8d8294cf56e12344ed05be82843
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2021
ms.locfileid: "98569985"
---
# <a name="introduction-to-resource-troubleshooting-in-azure-network-watcher"></a>Introdução à resolução de problemas de recursos no Observador da Rede Azure

Os Gateways de Rede Virtual fornecem conectividade entre recursos no local e outras redes virtuais dentro do Azure. Monitorizar os gateways e respetivas ligações é fundamental para garantir que a comunicação não é interrompida. O Network Watcher fornece a capacidade de resolver os gateways e ligações. A capacidade pode ser chamada através do portal PowerShell, Azure CLI ou REST API. Quando chamado, o Network Watcher diagnostica a saúde do gateway, ou ligação, e devolve os resultados apropriados. O pedido é uma transação de longa duração. Os resultados são devolvidos assim que o diagnóstico estiver completo.

![A screenshot mostra o Network Watcher V P N Diagnostics.][2]

## <a name="results"></a>Resultados

Os resultados preliminares devolvidos dão uma imagem geral da saúde do recurso. Podem ser fornecidas informações mais profundas sobre os recursos, tal como indicado na secção seguinte:

Segue-se a seguinte lista os valores devolvidos com a API de resolução de problemas:

* **startTime** - Este valor é o momento em que a chamada de API de resolução de problemas começou.
* **endTime** - Este valor é o momento em que a resolução de problemas terminou.
* **código** - Este valor é UnHealthy, se houver uma falha de diagnóstico única.
* **resultados** - Resultados é uma recolha de resultados devolvidos na Ligação ou no gateway de rede virtual.
    * **id** - Este valor é o tipo de falha.
    * **resumo** - Este valor é um resumo da falha.
    * **detalhado** - Este valor fornece uma descrição detalhada da falha.
    * **recomendações -** Esta propriedade é uma coleção de ações recomendadas a tomar.
      * **actionText** - Este valor contém o texto que descreve que medidas tomar.
      * **actionUri** - Este valor fornece o URI à documentação sobre como agir.
      * **actionUriText** - Este valor é uma breve descrição do texto de ação.

As tabelas a seguir mostram os diferentes tipos de avaria (id em resultados da lista anterior) que estão disponíveis e se a falha criar registos.

### <a name="gateway"></a>Gateway

| Tipo de Falha | Razão | Registo|
|---|---|---|
| NoFault | Quando nenhum erro é detetado |Sim|
| GatewayNotFound | Não é possível encontrar porta de entrada ou gateway não é a provisionado |Não|
| PlannedMaintenance |  A instância gateway está sob manutenção  |Não|
| UserDrivenUpdate | Esta falha ocorre quando uma atualização de utilizador está em curso. A atualização pode ser uma operação de redimensionamento. | Não |
| VipUnResponsive | Esta falha ocorre quando a instância principal do gateway não pode ser acedida devido a uma falha da pesquisa de estado de funcionamento. | Não |
| PlatformInActive | Existe um problema com a plataforma. | Não|
| ServiceNotRunning | O serviço subjacente não está a funcionar. | Não|
| NoConnectionsFoundForGateway | Não existem ligações no portal. Esta falha é apenas um aviso.| Não|
| LigaçõesNotConnected | As ligações não estão ligadas. Esta falha é apenas um aviso.| Sim|
| GatewayCPUUsageExceed | O uso atual do CPU de gateway é > 95%. | Sim |

### <a name="connection"></a>Ligação

| Tipo de Falha | Razão | Registo|
|---|---|---|
| NoFault | Quando nenhum erro é detetado |Sim|
| GatewayNotFound | Não é possível encontrar porta de entrada ou gateway não é a provisionado |Não|
| PlannedMaintenance | A instância gateway está sob manutenção  |Não|
| UserDrivenUpdate | Esta falha ocorre quando uma atualização de utilizador está em curso. A atualização pode ser uma operação de redimensionamento.  | Não |
| VipUnResponsive | Esta falha ocorre quando a instância principal do gateway não pode ser acedida devido a uma falha da pesquisa de estado de funcionamento. | Não |
| Entidade de ConexãoNotFound | Falta a configuração de ligação | Não |
| LigaçãoIsMarkedDis ligados | A ligação está marcada como "desligada" |Não|
| ConexãoNotConfiguredOnGateway | O serviço subjacente não tem a ligação configurada. | Sim |
| ConnectionMarkedStandby | O serviço subjacente está marcado como standby.| Sim|
| Autenticação | Desajuste de chave pré-partilhada | Sim|
| PeerReachability | O portal dos pares não é alcançável. | Sim|
| IkePolicyMismatch | O portal de pares tem políticas IKE que não são apoiadas pelo Azure. | Sim|
| Erro de PcPParse | Ocorreu um erro na análise do registo do PAM. |Sim|

## <a name="supported-gateway-types"></a>Tipos de Gateway suportados

As seguintes listas de tabelas que gateways e conexões são suportadas com a resolução de problemas do Observador de Rede:

| Gateway ou conexão | Suportado  |
|---------|---------|
|**Tipos de gateway**   |         |
|VPN      | Suportado        |
|ExpressRoute | Não suportado |
|**Tipos de VPN** | |
|Baseado em Rotas | Suportado|
|Baseado em Políticas | Não suportado|
|**Tipos de ligação**||
|IPsec| Suportado|
|VNet2Vnet| Suportado|
|ExpressRoute| Não suportado|
|VPNClient| Não suportado|

## <a name="log-files"></a>Ficheiros de registo

Os ficheiros de registo de resolução de problemas de recursos são armazenados numa conta de armazenamento após a resolução de problemas de recursos. A imagem a seguir mostra o conteúdo exemplo de uma chamada que resultou num erro.

![arquivo zip][1]

> [!NOTE]
> Em alguns casos, apenas um subconjunto dos ficheiros de registos é escrito para armazenamento.

Para obter instruções sobre o descarregamento de ficheiros a partir de contas de armazenamento Azure, consulte para [começar com o armazenamento Azure Blob usando .NET](../storage/blobs/storage-quickstart-blobs-dotnet.md). Outra ferramenta que pode ser usada é o Storage Explorer. Mais informações sobre o Storage Explorer podem ser encontradas aqui no seguinte link: [Storage Explorer](https://storageexplorer.com/)

### <a name="connectionstatstxt"></a>ConnectionStats.txt

O ficheiro **ConnectionStats.txt** contém estatísticas globais da Conexão, incluindo bytes de entrada e saída, estado de conexão e a hora em que a Ligação foi estabelecida.

> [!NOTE]
> Se a chamada para a API de resolução de problemas voltar saudável, a única coisa devolvida no ficheiro zip é um ficheiro **ConnectionStats.txt.**

O conteúdo deste ficheiro é semelhante ao seguinte exemplo:

```
Connectivity State : Connected
Remote Tunnel Endpoint :
Ingress Bytes (since last connected) : 288 B
Egress Bytes (Since last connected) : 288 B
Connected Since : 2/1/2017 8:22:06 PM
```

### <a name="cpustatstxt"></a>CPUStats.txt

O **ficheiroCPUStats.txt** contém utilização e memória de CPU disponíveis no momento do teste.  O conteúdo deste ficheiro é semelhante ao seguinte exemplo:

```
Current CPU Usage : 0 % Current Memory Available : 641 MBs
```

### <a name="ikeerrorstxt"></a>IKEErrors.txt

O **ficheiroIKEErrors.txt** contém quaisquer erros do IKE que foram encontrados durante a monitorização.

O exemplo a seguir mostra o conteúdo de um ficheiro IKEErrors.txt. Os seus erros podem ser diferentes dependendo do problema.

```
Error: Authentication failed. Check shared key. Check crypto. Check lifetimes. 
     based on log : Peer failed with Windows error 13801(ERROR_IPSEC_IKE_AUTH_FAIL)
Error: On-prem device sent invalid payload. 
     based on log : IkeFindPayloadInPacket failed with Windows error 13843(ERROR_IPSEC_IKE_INVALID_PAYLOAD)
```

### <a name="scrubbed-wfpdiagtxt"></a>Scrubbed-wfpdiag.txt

O **ficheiro de** registoScrubbed-wfpdiag.txtcontém o registo do PAM. Este registo contém registo de gotas de pacote e falhas do IKE/AuthIP.

O exemplo que se segue mostra o conteúdo do ficheiro Scrubbed-wfpdiag.txt. Neste exemplo, a chave partilhada de uma Ligação não estava correta, como se pode ver a partir da terceira linha a partir de baixo. O exemplo a seguir é apenas um corte de todo o log, uma vez que o log pode ser longo dependendo do problema.

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

### <a name="wfpdiagtxtsum"></a>wfpdiag.txt.soma

O **ficheirowfpdiag.txt.sum** é um registo que mostra os amortecedores e eventos processados.

Segue-se o conteúdo de um ficheiro wfpdiag.txt.sum.
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

## <a name="considerations"></a>Considerações 
* Apenas uma operação de resolução de problemas pode ser executada de cada vez por subscrição. Para executar outra operação de resolução de problemas, aguarde que a anterior esteja concluída. Desencadear mais operações enquanto uma anterior ainda não ter terminado fará com que as operações subsequentes falhem. 
* CLI Bug: Se estiver a utilizar o Azure CLI para executar o comando, o Gateway VPN e a conta de Armazenamento têm de estar no mesmo grupo de recursos. Os clientes com recursos em diferentes grupos de recursos podem usar o PowerShell ou o portal Azure.  


## <a name="next-steps"></a>Passos seguintes

Para aprender a diagnosticar um problema com uma ligação de gateway ou gateway, consulte [os problemas de comunicação do Diagnóstico entre redes](diagnose-communication-problem-between-networks.md).
<!--Image references-->

[1]: ./media/network-watcher-troubleshoot-overview/GatewayTenantWorkerLogs.png
[2]: ./media/network-watcher-troubleshoot-overview/portal.png
