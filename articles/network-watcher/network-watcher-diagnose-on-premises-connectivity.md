---
title: Diagnosticar conectividade no local através do gateway VPN
titleSuffix: Azure Network Watcher
description: Este artigo descreve como diagnosticar a conectividade no local através da gateway VPN com a resolução de problemas de recursos do Azure Network Watcher.
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: aeffbf3d-fd19-4d61-831d-a7114f7534f9
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 835b3a69e779b536961110b674ae67f4e8c13ce0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76845063"
---
# <a name="diagnose-on-premises-connectivity-via-vpn-gateways"></a>Diagnosticar conectividade no local através de gateways VPN

O Azure VPN Gateway permite-lhe criar uma solução híbrida que aborde a necessidade de uma ligação segura entre a sua rede no local e a sua rede virtual Azure. Como os seus requisitos são únicos, também a escolha do dispositivo VPN no local. Atualmente, o Azure suporta [vários dispositivos VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md#devicetable) que são constantemente validados em parceria com os fornecedores de dispositivos. Reveja as definições de configuração específicas do dispositivo antes de configurar o seu dispositivo VPN no local. Da mesma forma, o Azure VPN Gateway é configurado com um conjunto de [parâmetros IPsec suportados](../vpn-gateway/vpn-gateway-about-vpn-devices.md#ipsec) que são usados para estabelecer ligações. Atualmente não existe forma de especificar ou selecionar uma combinação específica de parâmetros IPsec do Gateway VPN Azure. Para estabelecer uma ligação bem sucedida entre as instalações e o Azure, as definições do dispositivo VPN no local devem estar de acordo com os parâmetros IPsec prescritos pelo Azure VPN Gateway. Se as definições estiverem incorretas, há uma perda de conectividade e até agora resolver problemas estes problemas não foi trivial e geralmente demorou horas a identificar e corrigir o problema.

Com a funcionalidade de resolução de problemas do Observador de Rede Azure, é possível diagnosticar quaisquer problemas com o seu Gateway e Connections e em poucos minutos tem informações suficientes para tomar uma decisão informada para corrigir o problema.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scenario"></a>Cenário

Deseja configurar uma ligação site-to-site entre o Azure e as instalações utilizando o FortiGate como o VPN Gateway no local. Para alcançar este cenário, seria necessário a seguinte configuração:

1. Gateway de Rede Virtual - O Gateway VPN em Azure
1. Local Network Gateway - A representação [de VPN Gateway no local (FortiGate)](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md#LocalNetworkGateway) em nuvem Azure
1. Ligação site-a-local (baseada em rota) - [Ligação entre o Gateway VPN e o router no local](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal#CreateConnection)
1. [Configurar o FortiGate](https://github.com/Azure/Azure-vpn-config-samples/blob/master/Fortinet/Current/Site-to-Site_VPN_using_FortiGate.md)

Orientação detalhada passo a passo para configurar uma configuração Site-a-Site pode ser encontrada visitando: [Criar um VNet com uma ligação Site-a-Site utilizando o portal Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md).

Um dos passos críticos de configuração é configurar os parâmetros de comunicação IPsec, qualquer configuração errada leva à perda de conectividade entre a rede no local e o Azure. Atualmente, os Gateways VPN Azure estão configurados para suportar os seguintes parâmetros IPsec para a Fase 1. Como pode ver na tabela abaixo, os algoritmos de encriptação suportados por Azure VPN Gateway são AES256, AES128 e 3DES.

### <a name="ike-phase-1-setup"></a>Configuração da fase 1 do IKE

| **Propriedade** | **PolicyBased** | **Gateway VPN baseado em rota e padrão ou alto desempenho** |
| --- | --- | --- |
| Versão do IKE |IKEv1 |IKEv2 |
| Grupo Diffie-Hellman |Grupo 2 (1024 bits) |Grupo 2 (1024 bits) |
| Método de autenticação |Chave Pré-partilhada |Chave Pré-partilhada |
| Algoritmos de Encriptação |AES256 AES128 3DES |AES256 3DES |
| Algoritmo Hash |SHA1(SHA128) |SHA1(SHA128), SHA2(SHA256) |
| Duração (Tempo) da Associação de Segurança (SA) da Fase 1 |28 800 segundos |28 800 segundos |

Como utilizador, seria necessário configurar o seu FortiGate, podendo ser encontrada uma configuração de amostra no [GitHub](https://github.com/Azure/Azure-vpn-config-samples/blob/master/Fortinet/Current/fortigate_show%20full-configuration.txt). Sem saber configuraste o teu FortiGate para usar o SHA-512 como algoritmo de hashing. Como este algoritmo não é um algoritmo suportado para ligações baseadas em políticas, a sua ligação VPN funciona.

Estas questões são difíceis de resolver e as causas fundamentais são muitas vezes não intuitivas. Neste caso, você pode abrir um bilhete de apoio para obter ajuda na resolução do problema. Mas com o Azure Network Watcher a resolver problemas da API, pode identificar estes problemas por conta própria.

## <a name="troubleshooting-using-azure-network-watcher"></a>Resolução de problemas usando O Observador da Rede Azure

Para diagnosticar a sua ligação, ligue-se ao Azure PowerShell e inicie o `Start-AzNetworkWatcherResourceTroubleshooting` cmdlet. Pode encontrar os detalhes sobre a utilização deste cmdlet no [Troubleshoot Virtual Network Gateway e ligações - PowerShell](network-watcher-troubleshoot-manage-powershell.md). Este cmdlet pode demorar até alguns minutos a ser concluído.

Uma vez concluído o cmdlet, pode navegar até ao local de armazenamento especificado no cmdlet para obter informações detalhadas sobre o problema e os registos. O Azure Network Watcher cria uma pasta zip que contém os seguintes ficheiros de registo:

![1][1]

Abra o ficheiro chamado IKEErrors.txt e apresenta o seguinte erro, indicando um problema com a configuração de configuração IKE no local.

```
Error: On-premises device rejected Quick Mode settings. Check values.
     based on log : Peer sent NO_PROPOSAL_CHOSEN notify
```

Você pode obter informações detalhadas do Scrubbed-wfpdiag.txt sobre o erro, `ERROR_IPSEC_IKE_POLICY_MATCH` como neste caso menciona que houve que levou a que a ligação não funcionasse corretamente.

Outra configuração comum é a especificação incorreta das teclas partilhadas. Se no exemplo anterior tiver especificado diferentes teclas partilhadas, o IKEErrors.txt mostra o seguinte erro: `Error: Authentication failed. Check shared key`.

A funcionalidade de resolução de problemas do Observador de Rede Azure permite diagnosticar e resolver problemas com o seu VPN Gateway e Connection com a facilidade de um simples cmdlet PowerShell. Atualmente apoiamos o diagnóstico das seguintes condições e estamos a trabalhar no sentido de acrescentar mais condições.

### <a name="gateway"></a>Gateway

| Tipo de Falha | Razão | Registar|
|---|---|---|
| NoFault | Quando não é detetado nenhum erro. |Sim|
| GatewayNotFound | Não é possível encontrar gateway ou Gateway não é provisionado. |Não|
| PlannedMaintenance |  A instância do gateway está em manutenção.  |Não|
| UserDrivenUpdate | Quando uma atualização do utilizador está em curso. Isto pode ser uma operação de redimensionar. | Não |
| VipUnResponsive | Não pode chegar à instância primária do Gateway. Isto acontece quando a sonda de saúde falha. | Não |
| PlatformInActive | Existe um problema com a plataforma. | Não|
| ServiceNotRunning | O serviço subjacente não está a funcionar. | Não|
| NoConnectionsFoundForGateway | Não há ligações na porta de entrada. Isto é só um aviso.| Não|
| ConexõesNotConnected | Nenhuma das Ligações está ligada. Isto é só um aviso.| Sim|
| Utilização do GatewayCPUExceeded | O atual uso do CPU de utilização do Gateway é > 95%. | Sim |

### <a name="connection"></a>Ligação

| Tipo de Falha | Razão | Registar|
|---|---|---|
| NoFault | Quando não é detetado nenhum erro. |Sim|
| GatewayNotFound | Não é possível encontrar gateway ou Gateway não é provisionado. |Não|
| PlannedMaintenance | A instância do gateway está em manutenção.  |Não|
| UserDrivenUpdate | Quando uma atualização do utilizador está em curso. Isto pode ser uma operação de redimensionar.  | Não |
| VipUnResponsive | Não pode chegar à instância primária do Gateway. Acontece quando a sonda de saúde falha. | Não |
| Conexão EntidadeNão Encontrada | Falta a configuração da ligação. | Não |
| ConexõesMarcadasDesligadas | A Ligação está marcada como "desligada". |Não|
| ConnectionNotConfiguredOnGateway | O serviço subjacente não tem a Ligação configurada. | Sim |
| ConexãoMarkedStandby | O serviço subjacente está marcado como standby.| Sim|
| Autenticação | Desajuste de chave pré-partilhada. | Sim|
| Alcance da peerreachability | A porta de entrada dos pares não é alcançável. | Sim|
| IkePolicyMismatch | O gateway peer tem políticas IKE que não são apoiadas pelo Azure. | Sim|
| Erro do WfpParse | Ocorreu um erro ao analisar o registo do PAM. |Sim|

## <a name="next-steps"></a>Passos seguintes

Aprenda a verificar a conectividade VPN Gateway com a PowerShell e a Azure Automation visitando [gateways do Monitor VPN com resolução](network-watcher-monitor-with-azure-automation.md) de problemas do Observador de Rede Azure

[1]: ./media/network-watcher-diagnose-on-premises-connectivity/figure1.png
