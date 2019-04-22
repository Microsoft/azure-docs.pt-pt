---
title: Diagnosticar conectividade no local através do gateway VPN com o observador de rede do Azure | Documentos da Microsoft
description: Este artigo descreve como diagnosticar conectividade no local através do gateway VPN com resolução de problemas de recursos do observador de rede do Azure.
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: aeffbf3d-fd19-4d61-831d-a7114f7534f9
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 323e5d63b5f8566d570dfd47323fcf12f7c6b28b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59051585"
---
# <a name="diagnose-on-premises-connectivity-via-vpn-gateways"></a>Diagnosticar a conectividade no local através de gateways de VPN

O Gateway de VPN do Azure permite-lhe criar solução híbrida que atender à necessidade de uma ligação segura entre a sua rede no local e a rede virtual do Azure. Como os seus requisitos são exclusivos, portanto, é a escolha do dispositivo VPN no local. O Azure suporta atualmente [vários dispositivos VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md#devicetable) que constantemente são validados em parceria com fornecedores de dispositivos. Reveja as definições de configuração específicos do dispositivo antes de configurar o dispositivo VPN no local. Da mesma forma, o Gateway de VPN do Azure está configurado com um conjunto de [suportados parâmetros de IPsec](../vpn-gateway/vpn-gateway-about-vpn-devices.md#ipsec) que são utilizadas para estabelecer ligações. Atualmente, não há nenhuma forma para que possa especificar ou selecionar uma combinação de parâmetros de IPsec do Gateway de VPN do Azure. Para estabelecer uma ligação com êxito entre no local e o Azure, as definições do dispositivo VPN no local tem de estar em conformidade com os parâmetros de IPsec prescritos pelo Gateway de VPN do Azure. Se as definições estiverem corretas, aqui é uma perda de conectividade e até agora esses problemas de resolução de problemas não foi trivial e, normalmente, levou horas para identificar e corrigir o problema.

Com o observador de rede do Azure resolver problemas de funcionalidade, pode diagnosticar problemas com o seu Gateway e as ligações e dentro de minutos tiver informações suficientes para tomar uma decisão informada para resolver o problema.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scenario"></a>Cenário

Pretende configurar uma ligação site a site entre o Azure e no local utilizando FortiGate como o Gateway de VPN no local. Para alcançar este cenário, exigiria que a configuração seguinte:

1. Gateway de rede virtual - o Gateway de VPN no Azure
1. Gateway de rede local - a [Gateway de VPN (FortiGate) no local](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md#LocalNetworkGateway) representação na cloud do Azure
1. Ligação de site a site (com base em rota) - [ligação entre o Gateway de VPN e o router no local](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal#createconnection)
1. [Configurar FortiGate](https://github.com/Azure/Azure-vpn-config-samples/blob/master/Fortinet/Current/Site-to-Site_VPN_using_FortiGate.md)

Orientações passo a passo detalhadas para configurar uma configuração Site a Site podem ser encontradas ao visitar: [Criar uma VNet com uma ligação Site a Site com o portal do Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md).

Um dos passos críticos de configuração está a configurar os parâmetros de comunicação de IPsec, qualquer configuração incorreta leva à perda de conectividade entre a rede no local e o Azure. Atualmente, Gateways de VPN do Azure estão configurados para suportar os seguintes parâmetros de IPsec para a fase 1. Tenha em atenção, como mencionado anteriormente, que estas definições não podem ser modificadas.  Como pode ver na tabela abaixo, os algoritmos de encriptação suportados pelo Gateway de VPN do Azure são AES256, AES128 e 3DES.

### <a name="ike-phase-1-setup"></a>O programa de configuração do IKE fase 1

| **Propriedade** | **PolicyBased** | **Gateway de VPN de elevado desempenho e Standard ou RouteBased** |
| --- | --- | --- |
| Versão do IKE |IKEv1 |IKEv2 |
| Grupo Diffie-Hellman |Grupo 2 (1024 bits) |Grupo 2 (1024 bits) |
| Método de Autenticação |Chave Pré-partilhada |Chave Pré-partilhada |
| Algoritmos de Encriptação |AES256 AES128 3DES |AES256 3DES |
| Algoritmo Hash |SHA1(SHA128) |SHA1(SHA128), SHA2(SHA256) |
| Duração (Tempo) da Associação de Segurança (SA) da Fase 1 |28 800 segundos |10 800 segundos |

Como um utilizador, será necessário para configurar seu FortiGate, um exemplo de configuração pode ser encontrado no [GitHub](https://github.com/Azure/Azure-vpn-config-samples/blob/master/Fortinet/Current/fortigate_show%20full-configuration.txt). Inconscientemente configurou seu FortiGate utilizar SHA-512 como algoritmo de hash. Como esse algoritmo não é um algoritmo suportado para ligações baseadas em políticas, a ligação VPN é que funciona.

Estes problemas são difíceis de resolver problemas e as principais causas, muitas vezes, são não intuitivas. Neste caso, é possível abrir um pedido de suporte para obter ajuda sobre como resolver o problema. Mas com o observador de rede de Azure API de resolução de problemas, pode identificar esses problemas por conta própria.

## <a name="troubleshooting-using-azure-network-watcher"></a>Resolução de problemas com o observador de rede do Azure

Para diagnosticar a sua ligação, ligue para o Azure PowerShell e iniciar o `Start-AzNetworkWatcherResourceTroubleshooting` cmdlet. Pode encontrar os detalhes sobre como utilizar este cmdlet em [resolver problemas de Gateway de rede Virtual e ligações - PowerShell](network-watcher-troubleshoot-manage-powershell.md). Este cmdlet pode demorar alguns minutos para concluir.

Assim que o cmdlet for concluído, pode navegar para a localização de armazenamento especificada no cmdlet para obter informações detalhadas sobre sobre o problema e os registos. O observador de rede do Azure cria uma pasta de zip que contém os ficheiros de registo seguinte:

![1][1]

Abra o ficheiro chamado IKEErrors.txt e apresenta o seguinte erro, que indica um problema no local com a configuração incorreta de definição de IKE.

```
Error: On-premises device rejected Quick Mode settings. Check values.
     based on log : Peer sent NO_PROPOSAL_CHOSEN notify
```

Pode obter informações detalhadas de Scrubbed-wfpdiag.txt sobre o erro, como nesse caso ela menciona que havia `ERROR_IPSEC_IKE_POLICY_MATCH` que levam a ligação não está a funcionar corretamente.

Outra configuração incorreta é as especificação chaves partilhadas incorretas. Se no exemplo anterior tivesse especificado diferentes chaves partilhadas, o IKEErrors.txt mostra o seguinte erro: `Error: Authentication failed. Check shared key`.

Resolução de problemas do observador de rede do Azure funcionalidade permite-lhe diagnosticar e resolver problemas do Gateway de VPN e uma ligação com a facilidade de um simple cmdlet do PowerShell. Atualmente estamos suportam a diagnosticar as seguintes condições e estiver a trabalhar para adicionar condição mais.

### <a name="gateway"></a>Gateway

| Tipo de falha | Razão | Registar|
|---|---|---|
| NoFault | Quando não é detetado nenhum erro. |Sim|
| GatewayNotFound | Não é possível encontrar o Gateway ou Gateway não está aprovisionada. |Não|
| PlannedMaintenance |  Instância de gateway está em manutenção.  |Não|
| UserDrivenUpdate | Quando uma atualização de utilizador está em curso. Isto pode ser uma operação de redimensionamento. | Não |
| VipUnResponsive | Não é possível alcançar a instância principal do Gateway. Isto acontece quando a sonda de estado de funcionamento falha. | Não |
| PlatformInActive | Existe um problema com a plataforma. | Não|
| ServiceNotRunning | O serviço subjacente não está em execução. | Não|
| NoConnectionsFoundForGateway | Sem ligações existe no gateway. Este é apenas um aviso.| Não|
| ConnectionsNotConnected | Nenhuma das conexões a estão ligadas. Este é apenas um aviso.| Sim|
| GatewayCPUUsageExceeded | A utilização atual de Gateway a utilização da CPU é > 95%. | Sim |

### <a name="connection"></a>Ligação

| Tipo de falha | Razão | Registar|
|---|---|---|
| NoFault | Quando não é detetado nenhum erro. |Sim|
| GatewayNotFound | Não é possível encontrar o Gateway ou Gateway não está aprovisionada. |Não|
| PlannedMaintenance | Instância de gateway está em manutenção.  |Não|
| UserDrivenUpdate | Quando uma atualização de utilizador está em curso. Isto pode ser uma operação de redimensionamento.  | Não |
| VipUnResponsive | Não é possível alcançar a instância principal do Gateway. Isso acontece quando a sonda de estado de funcionamento falha. | Não |
| ConnectionEntityNotFound | Configuração da ligação está em falta. | Não |
| ConnectionIsMarkedDisconnected | A ligação foi marcada como "desligada". |Não|
| ConnectionNotConfiguredOnGateway | O serviço subjacente não tem uma ligação configurada. | Sim |
| ConnectionMarkedStandby | O serviço subjacente é marcado como modo de espera.| Sim|
| Authentication | Erro de correspondência de chave pré-compartilhada. | Sim|
| PeerReachability | O gateway de ponto a ponto não está acessível. | Sim|
| IkePolicyMismatch | O gateway de mesmo nível tem políticas de IKE que não são suportadas pelo Azure. | Sim|
| WfpParse Error | Ocorreu um erro ao analisar o registo da WFP. |Sim|

## <a name="next-steps"></a>Passos Seguintes

Saiba como verificar a conectividade de Gateway de VPN com o PowerShell e automatização do Azure, visite a página [gateways de VPN de Monitor com resolução de problemas do observador de rede do Azure](network-watcher-monitor-with-azure-automation.md)

[1]: ./media/network-watcher-diagnose-on-premises-connectivity/figure1.png
