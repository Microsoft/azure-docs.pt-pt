---
title: Introdução à Azure Network Watcher Connection Troubleshoot Microsoft Docs
description: Esta página fornece uma visão geral da capacidade de resolução de problemas de conexão do Observador de Rede
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/11/2017
ms.author: damendo
ms.openlocfilehash: 6411e019d77b219e40bd91da973e00afda0ff18b
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94965490"
---
# <a name="introduction-to-connection-troubleshoot-in-azure-network-watcher"></a>Introdução à resolução de problemas de conexão no Observador da Rede Azure

A funcionalidade de resolução de problemas de ligação do Network Watcher fornece a capacidade de verificar uma ligação TCP direta de uma máquina virtual a uma máquina virtual (VM), nome de domínio totalmente qualificado (FQDN), URI ou endereço IPv4. Os cenários de rede são complexos, são implementados usando grupos de segurança de rede, firewalls, rotas definidas pelo utilizador e recursos fornecidos pela Azure. Configurações complexas tornam os problemas de conectividade de resolução de problemas desafiantes. O Network Watcher ajuda a reduzir a quantidade de tempo para encontrar e detetar problemas de conectividade. Os resultados devolvidos podem fornecer informações sobre se um problema de conectividade se deve a uma plataforma ou a um problema de configuração do utilizador. A conectividade pode ser verificada com [PowerShell,](network-watcher-connectivity-powershell.md) [Azure CLI](network-watcher-connectivity-cli.md)e [REST API](network-watcher-connectivity-rest.md).

> [!IMPORTANT]
> A resolução de problemas de ligação requer que a resolução de problemas de VM de onde provém a `AzureNetworkWatcherExtension` extensão VM tenha a extensão VM instalada. Para instalar a extensão num Windows VM visite [a extensão da máquina virtual do Observador de Redes Azure para windows](../virtual-machines/extensions/network-watcher-windows.md?toc=%252fazure%252fnetwork-watcher%252ftoc.json) e para Linux VM visite a [extensão virtual do Agente de Observadores de Rede Azure para o Linux](../virtual-machines/extensions/network-watcher-linux.md?toc=%252fazure%252fnetwork-watcher%252ftoc.json). A extensão não é necessária no ponto final de destino.

## <a name="response"></a>Resposta

A tabela seguinte mostra as propriedades devolvidas quando a resolução de problemas de ligação terminou de funcionar.

|Propriedade  |Descrição  |
|---------|---------|
|ConexãoStatus     | O estado da verificação de conectividade. Os resultados **possíveis** são alcançáveis e **inacessíveis.**        |
|AvgLatencyInMs     | Latência média durante o controlo de conectividade em milissegundos. (Só indicado se o estado de verificação for alcançável)        |
|MinLatencyInMs     | Latência mínima durante a verificação da conectividade em milissegundos. (Só indicado se o estado de verificação for alcançável)        |
|MaxLatencyInMs     | Latência máxima durante a verificação da conectividade em milissegundos. (Só indicado se o estado de verificação for alcançável)        |
|SondaSent     | Número de sondas enviadas durante a verificação. O valor máximo é de 100.        |
|SondasFailadas     | Número de sondas que falharam durante a verificação. O valor máximo é de 100.        |
|Saltos     | Hop by hop caminho de origem para destino.        |
|Hops[]. Tipo     | Tipo de recurso. Os valores possíveis são **Fonte,** **VirtualAppliance,** **VnetLocal** e **Internet.**        |
|Hops[]. ID | Identificador único do lúpulo.|
|Hops[]. Endereço | Endereço IP do lúpulo.|
|Hops[]. ResourceId | RecursoID do lúpulo se o lúpulo for um recurso Azure. Se for um recurso de internet, o ResourceID é **internet.** |
|Hops[]. NextHopIds | O identificador único do próximo salto tomado.|
|Hops[]. Questões | Uma coleção de problemas que foram encontrados durante o cheque naquele salto. Se não houvesse problemas, o valor está em branco.|
|Hops[]. Questões[]. Origem | No salto atual, onde ocorreu a questão. Os valores possíveis são:<br/> **Entrada** - A emissão está no link do salto anterior para o salto atual<br/>**Outbound** - A emissão está na ligação do salto atual para o próximo salto<br/>**Local** - A edição está no salto atual.|
|Hops[]. Questões[]. Severidade | A gravidade do problema detetado. Os valores possíveis são **Erro** e **Aviso**. |
|Hops[]. Questões[]. Tipo |O tipo de problema encontrado. Os valores possíveis são: <br/>**CPU**<br/>**Memória**<br/>**GuestFirewall**<br/>**DnsResolution**<br/>**Regra de Segurança de Rede**<br/>**UserDefinedRoute** |
|Hops[]. Questões[]. Contexto |Detalhes sobre o assunto encontrados.|
|Hops[]. Questões[]. Contexto[].key |A chave do par de valores chave voltou.|
|Hops[]. Questões[]. Contexto[].valor |Valor do par de valores chave devolvido.|

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
## <a name="fault-types"></a>Tipos de falhas

A resolução de problemas de ligação devolve tipos de falhas sobre a ligação. A tabela a seguir fornece uma lista dos tipos de avarias atuais devolvidos.

|Tipo  |Descrição  |
|---------|---------|
|CPU     | Alta utilização do CPU.       |
|Memória     | Alta utilização de memória.       |
|GuestFirewall     | O tráfego está bloqueado devido a uma configuração de firewall de máquina virtual.        |
|DNSResolution     | A resolução do DNS falhou no endereço de destino.        |
|Regra de Segurança de Rede    | O tráfego é bloqueado por uma regra do NSG (Regra é devolvida)        |
|UserDefinedRoute|O tráfego é desviado devido a uma rota definida pelo utilizador ou pelo sistema. |

### <a name="next-steps"></a>Passos seguintes

Saiba como resolver os problemas através do [portal Azure](network-watcher-connectivity-portal.md), [PowerShell,](network-watcher-connectivity-powershell.md) [Azure CLI](network-watcher-connectivity-cli.md)ou [REST API](network-watcher-connectivity-rest.md).