---
title: Diagnosticar conectividade nas instalações via gateway VPN
titleSuffix: Azure Network Watcher
description: Este artigo descreve como diagnosticar a conectividade no local através do gateway VPN com a resolução de problemas de recursos do Azure Network Watcher.
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: aeffbf3d-fd19-4d61-831d-a7114f7534f9
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: 3b8b53446799eec3473d63c89672393a35f9787a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "104670960"
---
# <a name="diagnose-on-premises-connectivity-via-vpn-gateways"></a>Diagnosticar conectividade no local através de gateways VPN

O Azure VPN Gateway permite-lhe criar uma solução híbrida que responda à necessidade de uma ligação segura entre a sua rede no local e a sua rede virtual Azure. Como os seus requisitos são únicos, assim como a escolha do dispositivo VPN no local. A Azure suporta atualmente [vários dispositivos VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md#devicetable) que são constantemente validados em parceria com os fornecedores de dispositivos. Reveja as definições de configuração específicas do dispositivo antes de configurar o dispositivo VPN no local. Da mesma forma, o Gateway Azure VPN é configurado com um conjunto de [parâmetros IPsec suportados](../vpn-gateway/vpn-gateway-about-vpn-devices.md#ipsec) que são usados para estabelecer ligações. Atualmente não existe forma de especificar ou selecionar uma combinação específica de parâmetros IPsec a partir do Gateway Azure VPN. Para estabelecer uma ligação bem sucedida entre as instalações e o Azure, as definições do dispositivo VPN no local devem estar de acordo com os parâmetros IPsec prescritos pela Azure VPN Gateway. Se as definições estiverem incorretas, há uma perda de conectividade e até agora a resolução de problemas não foi trivial e geralmente demorou horas a identificar e corrigir o problema.

Com a funcionalidade de resolução de problemas do Azure Network Watcher, é capaz de diagnosticar quaisquer problemas com o seu Gateway e Conexões e em poucos minutos tem informações suficientes para tomar uma decisão informada para corrigir o problema.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scenario"></a>Scenario

Pretende configurar uma ligação site-to-site entre Azure e no local usando o FortiGate como o VPN Gateway no local. Para alcançar este cenário, seria necessário a seguinte configuração:

1. Gateway de rede virtual - O Gateway VPN em Azure
1. Gateway de rede local - A representação [do Gateway VPN nas instalações (FortiGate)](../vpn-gateway/tutorial-site-to-site-portal.md#LocalNetworkGateway) na nuvem de Azure
1. Conexão local-local (baseada em rotas) - [Ligação entre o Gateway VPN e o router no local](../vpn-gateway/tutorial-site-to-site-portal.md#CreateConnection)
1. [FortiGate configurante](https://github.com/Azure/Azure-vpn-config-samples/blob/master/Fortinet/Current/Site-to-Site_VPN_using_FortiGate.md)

Orientações detalhadas passo a passo para configurar uma configuração site-to-site podem ser encontradas visitando: [Criar um VNet com uma ligação Site-to-Site utilizando o portal Azure](../vpn-gateway/tutorial-site-to-site-portal.md).

Um dos passos críticos de configuração é configurar os parâmetros de comunicação do IPsec, qualquer configuração errada leva à perda de conectividade entre a rede no local e o Azure. Atualmente, os Gateways Azure VPN estão configurados para suportar os seguintes parâmetros IPsec para a Fase 1. Como pode ver na tabela abaixo, os algoritmos de encriptação suportados por Azure VPN Gateway são AES256, AES128 e 3DES.

### <a name="ike-phase-1-setup"></a>Configuração da fase 1 do IKE

| **Propriedade** | **PolicyBased** | **RouteBased e Standard ou High-Performance gateway VPN** |
| --- | --- | --- |
| Versão do IKE |IKEv1 |IKEv2 |
| Grupo Diffie-Hellman |Grupo 2 (1024 bits) |Grupo 2 (1024 bits) |
| Método de autenticação |Chave Pré-partilhada |Chave Pré-partilhada |
| Algoritmos de Encriptação |AES256 AES128 3DES |AES256 3DES |
| Algoritmo Hash |SHA1(SHA128) |SHA1(SHA128), SHA2(SHA256) |
| Duração (Tempo) da Associação de Segurança (SA) da Fase 1 |28 800 segundos |28 800 segundos |

Como utilizador, seria necessário configurar o seu FortiGate, uma configuração de amostra pode ser encontrada no [GitHub](https://github.com/Azure/Azure-vpn-config-samples/blob/master/Fortinet/Current/fortigate_show%20full-configuration.txt). Sem saber, configuraste o teu FortiGate para usar o SHA-512 como algoritmo de hashing. Como este algoritmo não é um algoritmo suportado para ligações baseadas em políticas, a sua ligação VPN funciona.

Estas questões são difíceis de resolver problemas e as causas de raiz são muitas vezes não intuitivas. Neste caso, você pode abrir um bilhete de apoio para obter ajuda na resolução do problema. Mas com a Azure Network Watcher a resolver problemas com a API, pode identificar estes problemas por conta própria.

## <a name="troubleshooting-using-azure-network-watcher"></a>Resolução de problemas usando O Observador de Redes Azure

Para diagnosticar a sua ligação, ligue-se ao Azure PowerShell e inicie o `Start-AzNetworkWatcherResourceTroubleshooting` cmdlet. Pode encontrar os detalhes sobre a utilização deste cmdlet no [Troubleshoot Virtual Network Gateway e ligações - PowerShell](network-watcher-troubleshoot-manage-powershell.md). Este cmdlet pode demorar até alguns minutos para ser concluído.

Uma vez que o cmdlet esteja concluído, você pode navegar para o local de armazenamento especificado no cmdlet para obter informações detalhadas sobre o problema e registos. O Azure Network Watcher cria uma pasta zip que contém os seguintes ficheiros de registo:

![1][1]

Abra o ficheiro chamado IKEErrors.txt e apresenta o seguinte erro, indicando um problema com a configuração errada do IKE no local.

```
Error: On-premises device rejected Quick Mode settings. Check values.
     based on log : Peer sent NO_PROPOSAL_CHOSEN notify
```

Pode obter informações detalhadas da Scrubbed-wfpdiag.txt sobre o erro, uma vez que, neste caso, menciona que houve `ERROR_IPSEC_IKE_POLICY_MATCH` que a ligação não funciona corretamente.

Outra configuração errada comum é a especificação de chaves partilhadas incorretas. Se no exemplo anterior tiver especificado diferentes teclas partilhadas, o IKEErrors.txt mostra o seguinte erro: `Error: Authentication failed. Check shared key` .

A funcionalidade de resolução de problemas do Azure Network Watcher permite-lhe diagnosticar e resolver problemas no gateway e ligação VPN com a facilidade de um simples cmdlet PowerShell. Atualmente apoiamos o diagnóstico das seguintes condições e estamos a trabalhar no sentido de adicionar mais condições.

### <a name="gateway"></a>Gateway

| Tipo de Falha | Razão | Registo|
|---|---|---|
| NoFault | Quando não é detetado nenhum erro. |Sim|
| GatewayNotFound | Não é possível encontrar Gateway ou Gateway não está a ser a provisionado. |Não|
| PlannedMaintenance |  A instância do gateway está em manutenção.  |Não|
| UserDrivenUpdate | Quando uma atualização do utilizador está em andamento. Isto pode ser uma operação de redimensionar. | Não |
| VipUnResponsive | Não consigo chegar ao primeiro caso do Portal. Isto acontece quando a sonda de saúde falha. | Não |
| PlatformInActive | Existe um problema com a plataforma. | No|
| ServiceNotRunning | O serviço subjacente não está a funcionar. | No|
| NoConnectionsFoundForGateway | Não existem ligações no portal. Isto é só um aviso.| No|
| LigaçõesNotConnected | Nenhuma das Ligações está ligada. Isto é só um aviso.| Yes|
| GatewayCPUUsageExceed | O uso atual do CPU de utilização gateway é > 95%. | Yes |

### <a name="connection"></a>Ligação

| Tipo de Falha | Razão | Registo|
|---|---|---|
| NoFault | Quando não é detetado nenhum erro. |Sim|
| GatewayNotFound | Não é possível encontrar Gateway ou Gateway não está a ser a provisionado. |Não|
| PlannedMaintenance | A instância do gateway está em manutenção.  |Não|
| UserDrivenUpdate | Quando uma atualização do utilizador está em andamento. Isto pode ser uma operação de redimensionar.  | Não |
| VipUnResponsive | Não consigo chegar ao primeiro caso do Portal. Acontece quando a sonda de saúde falha. | No |
| Entidade de ConexãoNotFound | Falta a configuração da ligação. | No |
| LigaçãoIsMarkedDis ligados | A Ligação está marcada como "desligada". |No|
| ConexãoNotConfiguredOnGateway | O serviço subjacente não tem a Ligação configurada. | Yes |
| ConnectionMarkedStandby | O serviço subjacente está marcado como standby.| Yes|
| Autenticação | Incompatibilidade de chave pré-partilhada. | Yes|
| PeerReachability | O portal dos pares não é alcançável. | Yes|
| IkePolicyMismatch | O portal de pares tem políticas IKE que não são apoiadas pelo Azure. | Yes|
| Erro de PcPParse | Ocorreu um erro na análise do registo do PAM. |Sim|

## <a name="next-steps"></a>Próximos passos

Aprenda a verificar a conectividade VPN Gateway com a PowerShell e a Azure Automation visitando [gateways VPN monitor com resolução de problemas do Observador de Redes Azure](network-watcher-monitor-with-azure-automation.md)

[1]: ./media/network-watcher-diagnose-on-premises-connectivity/figure1.png