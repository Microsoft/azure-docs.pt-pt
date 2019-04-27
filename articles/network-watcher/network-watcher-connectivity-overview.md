---
title: Resolver problemas de introdução à ligação do observador de rede do Azure | Documentos da Microsoft
description: Esta página fornece uma visão geral da capacidade de resolução de problemas de ligação de observador de rede
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/11/2017
ms.author: kumud
ms.openlocfilehash: 4b1164c3dedc5d8a2fa02d70f66ff00afe604836
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60532460"
---
# <a name="introduction-to-connection-troubleshoot-in-azure-network-watcher"></a>Introdução à ligação resolução de problemas no observador de rede do Azure

Resolver problemas relacionados com a ligação de recurso do observador de rede fornece a capacidade para verificar uma ligação TCP direta de uma máquina virtual a uma máquina virtual (VM), o nome de domínio completamente qualificado (FQDN), URI, ou o endereço IPv4. Cenários de rede são complexos, eles são implementados usando grupos de segurança de rede, firewalls, rotas definidas pelo usuário e recursos fornecidos pelo Azure. Configurações complexas tornam a resolução de problemas de conectividade um desafio. Observador de rede ajuda a reduzir a quantidade de tempo para localizar e detetar problemas de conectividade. Os resultados devolvidos podem fornecer informações sobre se um problema de conectividade é devido a uma plataforma ou um problema de configuração do utilizador. Conectividade pode ser verificada com [PowerShell](network-watcher-connectivity-powershell.md), [CLI do Azure](network-watcher-connectivity-cli.md), e [REST API](network-watcher-connectivity-rest.md).

> [!IMPORTANT]
> Resolver problemas de ligação requer que a solucionar problemas a partir de VM tem o `AzureNetworkWatcherExtension` extensão da VM instalado. Para instalar a extensão numa VM do Windows, visite [extensão de máquina virtual de agente do observador de rede do Azure para Windows](../virtual-machines/windows/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) e para visite de VM do Linux [extensão da máquina virtual de agente do observador de rede do Azure para Linux](../virtual-machines/linux/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json). A extensão não é necessário no ponto de extremidade de destino.

## <a name="response"></a>Resposta

A tabela seguinte mostra as propriedades devolvidas quando resolver problemas de ligação foi concluída em execução.

|Propriedade  |Descrição  |
|---------|---------|
|ConnectionStatus     | O estado da verificação de conectividade. Possíveis resultados são **Reachable** e **inacessível**.        |
|AvgLatencyInMs     | Latência média durante a verificação de conectividade em milissegundos. (Mostrado apenas se o estado de verificação está acessível)        |
|MinLatencyInMs     | Latência mínima durante a verificação de conectividade em milissegundos. (Mostrado apenas se o estado de verificação está acessível)        |
|MaxLatencyInMs     | Latência máxima durante a verificação de conectividade em milissegundos. (Mostrado apenas se o estado de verificação está acessível)        |
|ProbesSent     | Número de sondas enviadas durante a verificação. Valor máximo é 100.        |
|ProbesFailed     | Número de sondas que falharam durante a verificação. Valor máximo é 100.        |
|Saltos     | Salto a caminho de salto de origem para destino.        |
|Hops[].Type     | Tipo de recurso. Os valores possíveis são **origem**, **VirtualAppliance**, **VnetLocal**, e **Internet**.        |
|Hops[].Id | Identificador exclusivo do salto.|
|Hops[].Address | Endereço IP do salto.|
|Hops[].ResourceId | ResourceID de salto se o salto é um recurso do Azure. Se for um recurso de internet, é ResourceID **Internet**. |
|Hops[].NextHopIds | O identificador exclusivo do próximo salto tomado.|
|Hops[].Issues | Uma coleção dos problemas encontrados durante a verificação desse salto. Se não houvesse nenhum problema, o valor está em branco.|
|Hops[].Issues[].Origin | O salto atual, em que ocorreu o problema. Os valores possíveis são:<br/> **Entrada** -problema é sobre a ligação do salto anterior para o salto atual<br/>**Saída** -problema é sobre a ligação do salto atual para o próximo salto<br/>**Local** -trate de mensagens em fila o salto atual.|
|Hops[].Issues[].Severity | A gravidade do problema detetado. Os valores possíveis são **erro** e **aviso**. |
|Hops[].Issues[].Type |O tipo de problema encontrado. Os valores possíveis são: <br/>**CPU**<br/>**Memória**<br/>**GuestFirewall**<br/>**DnsResolution**<br/>**NetworkSecurityRule**<br/>**UserDefinedRoute** |
|Hops[].Issues[].Context |Detalhes sobre o problema encontrado.|
|Hops[].Issues[].Context[].key |Chave do par chave-valor retornado.|
|Hops[].Issues[].Context[].value |Valor do par chave-valor retornado.|

Segue-se um exemplo de um problema encontrado num salto.

```json
"Issues": [
    {
        "Origin": "Outbound",
        "Severity": "Error",
        "Type": "NetworkSecurityRule",
        "Context": [
            {
                "key": "RuleName",
                "value": "UserRule_Port80"
            }
        ]
    }
]
```
## <a name="fault-types"></a>Tipos de falha

Devolve a tipos de falha sobre a ligação resolver problemas de ligação. A tabela seguinte fornece uma lista de tipos de falha atual devolvido.

|Type  |Descrição  |
|---------|---------|
|CPU     | Alta utilização da CPU.       |
|Memória     | Utilização de memória elevada.       |
|GuestFirewall     | O tráfego é bloqueado devido a uma configuração de firewall de máquina virtual.        |
|DNSResolution     | Falha na resolução DNS para o endereço de destino.        |
|NetworkSecurityRule    | O tráfego está bloqueado por uma regra de NSG (regra será devolvida)        |
|UserDefinedRoute|Tráfego é interrompido devido a uma definidas pelo utilizador ou a rota de sistema. |

### <a name="next-steps"></a>Passos Seguintes

Saiba como resolver problemas de ligações com o [portal do Azure](network-watcher-connectivity-portal.md), [PowerShell](network-watcher-connectivity-powershell.md), o [da CLI do Azure](network-watcher-connectivity-cli.md), ou [REST API](network-watcher-connectivity-rest.md).