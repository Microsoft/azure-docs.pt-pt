---
title: Diagnosticar a conectividade local por meio do gateway de VPN
titleSuffix: Azure Network Watcher
description: Este artigo descreve como diagnosticar a conectividade local por meio do gateway de VPN com a solução de problemas de recursos do observador de rede do Azure.
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
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845063"
---
# <a name="diagnose-on-premises-connectivity-via-vpn-gateways"></a>Diagnosticar a conectividade local por meio de gateways de VPN

O gateway de VPN do Azure permite que você crie uma solução híbrida que atenda à necessidade de uma conexão segura entre sua rede local e sua rede virtual do Azure. À medida que seus requisitos são exclusivos, é a opção de dispositivo VPN local. Atualmente, o Azure dá suporte a [vários dispositivos VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md#devicetable) que são constantemente validados em parceria com os fornecedores de dispositivos. Examine as definições de configuração específicas do dispositivo antes de configurar seu dispositivo VPN local. Da mesma forma, o gateway de VPN do Azure é configurado com um conjunto de [parâmetros de IPsec com suporte](../vpn-gateway/vpn-gateway-about-vpn-devices.md#ipsec) que são usados para estabelecer conexões. Atualmente, não há como especificar ou selecionar uma combinação específica de parâmetros IPsec do gateway de VPN do Azure. Para estabelecer uma conexão bem-sucedida entre o local e o Azure, as configurações do dispositivo VPN local devem estar de acordo com os parâmetros IPsec prescritos pelo gateway de VPN do Azure. Se as configurações estiverem incorretas, haverá perda de conectividade e, até agora, a solução desses problemas não era trivial e geralmente levava horas para identificar e corrigir o problema.

Com o recurso de solução de problemas do observador de rede do Azure, você pode diagnosticar problemas com seu gateway e conexões e, em minutos, ter informações suficientes para tomar uma decisão informada de retificar o problema.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scenario"></a>Cenário

Você deseja configurar uma conexão site a site entre o Azure e o local usando o FortiGate como o gateway de VPN local. Para obter esse cenário, você precisaria da seguinte configuração:

1. Gateway de rede virtual-o gateway de VPN no Azure
1. Gateway de rede local-a representação do [Gateway de VPN (FortiGate) no local](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md#LocalNetworkGateway) na nuvem do Azure
1. Conexão site a site (baseada em rota)- [conexão entre o gateway de VPN e o roteador local](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal#CreateConnection)
1. [Configurando o FortiGate](https://github.com/Azure/Azure-vpn-config-samples/blob/master/Fortinet/Current/Site-to-Site_VPN_using_FortiGate.md)

As diretrizes detalhadas passo a passo para configurar uma configuração site a site podem ser encontradas visitando: [criar uma VNet com uma conexão site a site usando o portal do Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md).

Uma das etapas de configuração críticas é configurar os parâmetros de comunicação IPsec, qualquer configuração incorreta leva à perda de conectividade entre a rede local e o Azure. Atualmente, os gateways de VPN do Azure são configurados para dar suporte aos seguintes parâmetros IPsec para a fase 1. Como você pode ver na tabela abaixo, os algoritmos de criptografia com suporte do gateway de VPN do Azure são AES256, AES128 e 3DES.

### <a name="ike-phase-1-setup"></a>Fase 1 da instalação do IKE

| **Propriedade** | **PolicyBased** | **Gateway de VPN RouteBased e Standard ou de alto desempenho** |
| --- | --- | --- |
| Versão do IKE |IKEv1 |IKEv2 |
| Grupo Diffie-Hellman |Grupo 2 (1024 bits) |Grupo 2 (1024 bits) |
| Método de autenticação |Chave Pré-partilhada |Chave Pré-partilhada |
| Algoritmos de Encriptação |AES256 AES128 3DES |AES256 3DES |
| Algoritmo Hash |SHA1(SHA128) |SHA1(SHA128), SHA2(SHA256) |
| Duração (Tempo) da Associação de Segurança (SA) da Fase 1 |28 800 segundos |28 800 segundos |

Como usuário, seria necessário configurar o FortiGate, uma configuração de exemplo pode ser encontrada no [GitHub](https://github.com/Azure/Azure-vpn-config-samples/blob/master/Fortinet/Current/fortigate_show%20full-configuration.txt). Sem saber, você configurou seu FortiGate para usar o SHA-512 como o algoritmo de hash. Como esse algoritmo não é um algoritmo com suporte para conexões baseadas em políticas, sua conexão VPN funciona.

Esses problemas são difíceis de solucionar e as causas raiz geralmente não são intuitivas. Nesse caso, você pode abrir um tíquete de suporte para obter ajuda sobre como resolver o problema. Mas com a API de solução de problemas do observador de rede do Azure, você pode identificar esses problemas por conta própria.

## <a name="troubleshooting-using-azure-network-watcher"></a>Solucionando problemas usando o observador de rede do Azure

Para diagnosticar sua conexão, conecte-se a Azure PowerShell e inicie o cmdlet `Start-AzNetworkWatcherResourceTroubleshooting`. Você pode encontrar os detalhes sobre como usar esse cmdlet em [solucionar problemas de gateway de rede virtual e conexões-PowerShell](network-watcher-troubleshoot-manage-powershell.md). Esse cmdlet pode levar alguns minutos para ser concluído.

Depois que o cmdlet for concluído, você poderá navegar até o local de armazenamento especificado no cmdlet para obter informações detalhadas sobre o problema e os logs. O observador de rede do Azure cria uma pasta zip que contém os seguintes arquivos de log:

![1][1]

Abra o arquivo chamado IKEErrors. txt e ele exibirá o erro a seguir, indicando um problema com a configuração de IKE local incorreta.

```
Error: On-premises device rejected Quick Mode settings. Check values.
     based on log : Peer sent NO_PROPOSAL_CHOSEN notify
```

Você pode obter informações detalhadas do Scrubbed-wfpdiag.txt sobre o erro, uma vez que neste caso menciona que houve `ERROR_IPSEC_IKE_POLICY_MATCH` que levam a que a ligação não funcione corretamente.

Outra configuração errada comum é a especificação de chaves compartilhadas incorretas. Se, no exemplo anterior, você tiver especificado chaves compartilhadas diferentes, o IKEErrors. txt mostrará o seguinte erro: `Error: Authentication failed. Check shared key`.

O recurso de solução de problemas do observador de rede do Azure permite diagnosticar e solucionar problemas de seu gateway de VPN e conexão com a facilidade de um cmdlet simples do PowerShell. Atualmente, damos suporte ao diagnóstico das seguintes condições e estamos trabalhando para adicionar mais condições.

### <a name="gateway"></a>Gateway

| Tipo de avaria | Razão | Registo|
|---|---|---|
| NoFault | Quando não for detetado nenhum erro. |Sim|
| GatewayNotFound | Não é possível localizar o gateway ou o gateway não está provisionado. |Não|
| Manutenção Planeada |  A instância gateway está em manutenção.  |Não|
| UserDrivenUpdate | Quando uma atualização do usuário está em andamento. Isso pode ser uma operação de redimensionamento. | Não |
| VipUnResponsive | Não é possível acessar a instância primária do gateway. Isso acontece quando a investigação de integridade falha. | Não |
| PlataformaInActive | Há um problema com a plataforma. | Não|
| ServiceNotRunning | O serviço subjacente não está em execução. | Não|
| NoConnectionsFoundForGateway | Não existe nenhuma conexão no gateway. Isso é apenas um aviso.| Não|
| ConnectionsNotConnected | Nenhuma das conexões está conectada. Isso é apenas um aviso.| Sim|
| GatewayCPUUsageExceeded | O atual uso do CPU de utilização gateway é > 95%. | Sim |

### <a name="connection"></a>Ligação

| Tipo de avaria | Razão | Registo|
|---|---|---|
| NoFault | Quando não for detetado nenhum erro. |Sim|
| GatewayNotFound | Não é possível localizar o gateway ou o gateway não está provisionado. |Não|
| Manutenção Planeada | A instância gateway está em manutenção.  |Não|
| UserDrivenUpdate | Quando uma atualização do usuário está em andamento. Isso pode ser uma operação de redimensionamento.  | Não |
| VipUnResponsive | Não é possível acessar a instância primária do gateway. Ocorre quando a investigação de integridade falha. | Não |
| ConnectionEntityNotFound | A configuração da conexão está ausente. | Não |
| ConnectionIsMarkedDisconnected | A conexão está marcada como "desconectada". |Não|
| ConnectionNotConfiguredOnGateway | O serviço subjacente não tem a conexão configurada. | Sim |
| ConnectionMarkedStandby | O serviço subjacente está marcado como em espera.| Sim|
| Autenticação | Incompatibilidade de chave pré-compartilhada. | Sim|
| PeerReachability | O gateway de mesmo nível não está acessível. | Sim|
| IkePolicyMismatch | O gateway par tem políticas IKE que não são suportadas pelo Azure. | Sim|
| WfpParse Error | Ocorreu um erro ao analisar o log WFP. |Sim|

## <a name="next-steps"></a>Passos seguintes

Saiba como verificar a conectividade do gateway de VPN com o PowerShell e a automação do Azure visitando [monitorar gateways de VPN com a solução de problemas do observador de rede do Azure](network-watcher-monitor-with-azure-automation.md)

[1]: ./media/network-watcher-diagnose-on-premises-connectivity/figure1.png
