---
title: Limites de tamanho de pedido de firewall de aplicação Web e listas de exclusão no Gateway de aplicação do Azure - portal do Azure
description: Este artigo fornece informações sobre limites de tamanho de pedido de firewall de aplicação web e configuração no Gateway de aplicação com o portal do Azure apresenta uma lista de exclusão.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.workload: infrastructure-services
ms.date: 1/29/2019
ms.author: victorh
ms.openlocfilehash: 3e8014a9cb2353ef65482aa35a1e686567ca6e35
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57315049"
---
# <a name="web-application-firewall-request-size-limits-and-exclusion-lists"></a>Limites de tamanho de pedido de firewall de aplicação Web e listas de exclusão

A firewall de aplicações do Gateway de aplicação Azure web (WAF) fornece proteção para aplicativos web. Este artigo descreve os limites de tamanho do pedido de WAF e configuração de listas de exclusão.

## <a name="waf-request-size-limits"></a>Limites de tamanho do pedido de WAF

![Limites de tamanho do pedido](media/application-gateway-waf-configuration/waf-requestsizelimit.png)

Firewall de aplicações Web permite-lhe configurar limites de tamanho do pedido dentro inferiores e superiores. As seguintes configurações de limites de dois tamanho estão disponíveis:

- O campo de tamanho do corpo de pedido máximo é especificado em KBs e controles carrega de limite de tamanho pedido geral, excluindo todos os ficheiros. Este campo pode variar de 1 KB mínimo ao valor máximo de 128 KB. O valor predefinido para o tamanho do corpo de pedido é 128 KB.
- O campo de limite de carregamento do ficheiro é especificado em MB e rege o tamanho de carregamento de ficheiro máximo permitido. Este campo pode ter um valor mínimo de 1 MB e um máximo de 500 MB para instâncias grandes SKU enquanto médio SKU tem um máximo de 100 MB. O valor predefinido para o limite de carregamento de ficheiro é 100 MB.

WAF também oferece um botão configurável para ativar ou desativar à inspeção de corpo de pedido. Por predefinição, a inspeção de corpo de pedido é ativada. Se a inspeção de corpo do pedido estiver desativada, o WAF não avalia o conteúdo do corpo da mensagem HTTP. Nesses casos, o WAF continua para impor as regras de WAF em cabeçalhos, cookies e URI. Se a inspeção de corpo do pedido estiver desativada, o campo de tamanho do corpo de pedido máximo não é aplicável e não é possível definir. Desativar a inspeção de corpo de pedido permite que as mensagens maior do que 128 KB sejam enviados para o WAF, mas o corpo da mensagem não é inspecionado relativamente a vulnerabilidades.

## <a name="waf-exclusion-lists"></a>Listas de exclusão de WAF

![waf-exclusion.png](media/application-gateway-waf-configuration/waf-exclusion.png)

Listas de exclusão de WAF permitem que omitir determinados atributos de pedido de uma avaliação de WAF. Um exemplo comum é o que Active Directory inserido tokens que são utilizados para autenticação ou campos de palavra-passe. Esses atributos são suscetíveis a conter os carateres especiais que podem disparar um falso positivo das regras WAF. Assim que um atributo é adicionado à lista de exclusão de WAF, não é considerada por qualquer regra de WAF configurada e Active Directory. Listas de exclusão são globais em escopo.

Os seguintes atributos podem ser adicionados às listas de exclusão:

* Cabeçalhos de pedido
* Pedido de Cookies
* Corpo do Pedido

   * Dados de várias partes do formulário
   * XML
   * JSON

Pode especificar um cabeçalho de pedido exato, o corpo, o cookie ou correspondência de atributo de cadeia de consulta.  Ou, opcionalmente, pode especificar correspondências parciais. A exclusão está sempre um campo de cabeçalho, nunca no seu valor. Regras de exclusão são globais em escopo e aplicam-se a todas as páginas e todas as regras.

Seguem-se os operadores de critérios de correspondência suportados:

- **É igual a**:  Este operador é utilizado para uma correspondência exata. Por exemplo, para a seleção de um cabeçalho denominado **bearerToken**, utilize o operador equals com o Seletor de definido como **bearerToken**.
- **Começa com**: Este operador corresponde a todos os campos que começam com o valor de Seletor especificado.
- **Termina com**:  Este operador corresponde a todos os campos de solicitação que terminam com o valor de Seletor especificado.
- **Contém**: Este operador corresponde a todos os campos de solicitação que contêm o valor de Seletor especificado.

Em todos os casos, a correspondência é sensível a maiúsculas e expressões regulares não são permitidos como seletores.

### <a name="examples"></a>Exemplos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

O Azure PowerShell trecho a seguir demonstra o uso de exclusões:

```azurepowershell
// exclusion 1: exclude request head start with xyz
// exclusion 2: exclude request args equals a

$exclusion1 = New-AzApplicationGatewayFirewallExclusionConfig -MatchVariable "RequestHeaderNames" -SelectorMatchOperator "StartsWith" -Selector "xyz"

$exclusion2 = New-AzApplicationGatewayFirewallExclusionConfig -MatchVariable "RequestArgNames" -SelectorMatchOperator "Equals" -Selector "a"

// add exclusion lists to the firewall config

$firewallConfig = New-AzApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode Prevention -RuleSetType "OWASP" -RuleSetVersion "2.2.9" -DisabledRuleGroups $disabledRuleGroup1,$disabledRuleGroup2 -RequestBodyCheck $true -MaxRequestBodySizeInKb 80 -FileUploadLimitInMb 70 -Exclusions $exclusion1,$exclusion2
```

O fragmento json seguinte demonstra o uso de exclusões:

```json
"webApplicationFirewallConfiguration": {
          "enabled": "[parameters('wafEnabled')]",
          "firewallMode": "[parameters('wafMode')]",
          "ruleSetType": "[parameters('wafRuleSetType')]",
          "ruleSetVersion": "[parameters('wafRuleSetVersion')]",
          "disabledRuleGroups": [],
          "exclusions": [
            {
                "matchVariable": "RequestArgNames",
                "selectorMatchOperator": "StartsWith",
                "selector": "a^bc"
            }
```

## <a name="next-steps"></a>Passos Seguintes

Depois de configurar as definições de WAF, pode aprender a ver os registos da WAF. Para obter mais informações, consulte [diagnóstico do Gateway de aplicação](application-gateway-diagnostics.md#diagnostic-logging).
