---
title: Introdução ao Problema de Ligação de Vigilantes da Rede Azure [ Microsoft Docs
description: Esta página fornece uma visão geral da capacidade de resolução de problemas de ligação do Observador de Rede
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
ms.openlocfilehash: cae3072a3468b232e95d7c1949948b71059695ea
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79283280"
---
# <a name="introduction-to-connection-troubleshoot-in-azure-network-watcher"></a>Introdução à resolução de problemas de ligação no Vigilante da Rede Azure

A funcionalidade de resolução de problemas de ligação do Network Watcher fornece a capacidade de verificar uma ligação TCP direta de uma máquina virtual para uma máquina virtual (VM), nome de domínio totalmente qualificado (FQDN), URI ou endereço IPv4. Os cenários de rede são complexos, são implementados utilizando grupos de segurança de rede, firewalls, rotas definidas pelo utilizador e recursos fornecidos pelo Azure. Configurações complexas tornam problemas de conectividade de resolução de problemas desafiantes. O Network Watcher ajuda a reduzir a quantidade de tempo para encontrar e detetar problemas de conectividade. Os resultados devolvidos podem fornecer informações sobre se um problema de conectividade se deve a um problema de plataforma ou de configuração do utilizador. A conectividade pode ser verificada com [PowerShell,](network-watcher-connectivity-powershell.md) [Azure CLI](network-watcher-connectivity-cli.md)e [REST API](network-watcher-connectivity-rest.md).

> [!IMPORTANT]
> A resolução de problemas de ligação `AzureNetworkWatcherExtension` requer que o VM de que se desloque tem a extensão VM instalada. Para instalar a extensão de um Windows VM visite a extensão virtual do Agente observador de [rede Azure para windows](../virtual-machines/windows/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) e para o Linux VM visite a extensão virtual do Agente observador de rede [Azure para o Linux](../virtual-machines/linux/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json). A extensão não é necessária no ponto final do destino.

## <a name="response"></a>Resposta

A tabela seguinte mostra as propriedades devolvidas quando a sessão de problemas de ligação terminou de funcionar.

|Propriedade  |Descrição  |
|---------|---------|
|Estatuto de Conexão     | O estado da verificação da conectividade. Os resultados possíveis são **alcançáveis** e **inacessíveis.**        |
|AvgLatencyInMs     | Latência média durante o controlo de conectividade em milissegundos. (Apenas demonstrado se o estado da verificação for acessível)        |
|MinLatencyInMs     | Latência mínima durante o controlo de conectividade em milissegundos. (Apenas demonstrado se o estado da verificação for acessível)        |
|MaxLatencyInMs     | Latência máxima durante o controlo de conectividade em milissegundos. (Apenas demonstrado se o estado da verificação for acessível)        |
|SondasSent     | Número de sondas enviadas durante a verificação. O valor máximo é 100.        |
|Sondas Falhadas     | Número de sondas que falharam durante a verificação. O valor máximo é 100.        |
|Saltos     | Pule pelo caminho do salto de fonte para destino.        |
|Lúpulo[]. Tipo     | Tipo de recurso. Os valores possíveis são **Fonte,** **VirtualAppliance,** **VnetLocal**e **Internet**.        |
|Lúpulo[]. Id | Identificador único do lúpulo.|
|Lúpulo[]. Endereço | Endereço IP do lúpulo.|
|Lúpulo[]. Resourceid | ResourceID do lúpulo se o lúpulo for um recurso Azure. Se for um recurso de internet, o ResourceID **é**internet . |
|Lúpulo[]. NextHopIds | O identificador único do próximo salto tomado.|
|Lúpulo[]. Questões | Uma coleção de problemas que foram encontrados durante o cheque naquele salto. Se não houver problemas, o valor está em branco.|
|Lúpulo[]. Questões[]. Origem | No salto atual, onde ocorreu a questão. Os valores possíveis são:<br/> **Entrada** - A questão está no link do salto anterior para o salto atual<br/>**Outbound** - A questão está no link do salto atual para o próximo salto<br/>**Local** - A questão está no salto atual.|
|Lúpulo[]. Questões[]. Gravidade | A gravidade do problema detetado. Os valores possíveis são **Erro** e **Aviso.** |
|Lúpulo[]. Questões[]. Tipo |O tipo de problema encontrado. Os valores possíveis são: <br/>**CPU**<br/>**Memória**<br/>**GuestFirewall**<br/>**Resolução dns**<br/>**Regra de Segurança da Rede**<br/>**Via Definida pelo Utilizador** |
|Lúpulo[]. Questões[]. Contexto |Detalhes sobre o assunto encontrados.|
|Lúpulo[]. Questões[]. Contexto[].chave |Chave do par de valor chave devolvido.|
|Lúpulo[]. Questões[]. Contexto[].valor |Valor do par de valor chave devolvido.|

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
## <a name="fault-types"></a>Tipos de avarias

A resolução de problemas de ligação devolve tipos de falhas sobre a ligação. A tabela seguinte fornece uma lista dos tipos de avarias atuais devolvidos.

|Tipo  |Descrição  |
|---------|---------|
|CPU     | Alta utilização da CPU.       |
|Memória     | Utilização de alta memória.       |
|GuestFirewall     | O tráfego está bloqueado devido a uma configuração de firewall virtual da máquina.        |
|Resolução dNS     | A resolução do DNS falhou no endereço de destino.        |
|Regra de Segurança da Rede    | O tráfego é bloqueado por uma regra nsg (regra é devolvida)        |
|Via Definida pelo Utilizador|O tráfego é diminuído devido a uma rota definida pelo utilizador ou ao sistema. |

### <a name="next-steps"></a>Passos seguintes

Aprenda a resolver ligações utilizando o [portal Azure,](network-watcher-connectivity-portal.md) [PowerShell,](network-watcher-connectivity-powershell.md) [o Azure CLI](network-watcher-connectivity-cli.md)ou [REST API](network-watcher-connectivity-rest.md).