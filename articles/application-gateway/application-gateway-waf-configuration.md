---
title: Limites de tamanho de pedido de firewall de aplicação Web e listas de exclusão no Gateway de aplicação do Azure - portal do Azure
description: Este artigo fornece informações sobre limites de tamanho de pedido de firewall de aplicação web e configuração no Gateway de aplicação com o portal do Azure apresenta uma lista de exclusão.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 5/15/2019
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 5ddcdeca41e2f21fa27db25f7e0721c7ef87e491
ms.sourcegitcommit: 3675daec6c6efa3f2d2bf65279e36ca06ecefb41
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/14/2019
ms.locfileid: "65620288"
---
# <a name="web-application-firewall-request-size-limits-and-exclusion-lists"></a>Limites de tamanho de pedido de firewall de aplicação Web e listas de exclusão

A firewall de aplicações do Gateway de aplicação Azure web (WAF) fornece proteção para aplicativos web. Este artigo descreve os limites de tamanho do pedido de WAF e configuração de listas de exclusão.

## <a name="waf-request-size-limits"></a>Limites de tamanho do pedido de WAF

![Limites de tamanho do pedido](media/application-gateway-waf-configuration/waf-requestsizelimit.png)

Firewall de aplicações Web permite-lhe configurar limites de tamanho do pedido dentro inferiores e superiores. As seguintes configurações de limites de dois tamanho estão disponíveis:

- O campo de tamanho do corpo de pedido máximo é especificado em quilobytes e controles carrega de limite de tamanho pedido geral, excluindo todos os ficheiros. Este campo pode variar de 1 KB mínimo ao valor máximo de 128 KB. O valor predefinido para o tamanho do corpo de pedido é 128 KB.
- O campo de limite de carregamento do ficheiro é especificado em MB e rege o tamanho de carregamento de ficheiro máximo permitido. Este campo pode ter um valor mínimo de 1 MB e um máximo de 500 MB para instâncias grandes SKU enquanto médio SKU tem um máximo de 100 MB. O valor predefinido para o limite de carregamento de ficheiro é 100 MB.

WAF também oferece um botão configurável para ativar ou desativar à inspeção de corpo de pedido. Por predefinição, a inspeção de corpo de pedido é ativada. Se a inspeção de corpo do pedido estiver desativada, o WAF não avalie o conteúdo do corpo de mensagem HTTP. Nesses casos, o WAF continua para impor as regras de WAF em cabeçalhos, cookies e URI. Se a inspeção de corpo do pedido estiver desativada, o campo de tamanho do corpo de pedido máximo não é aplicável e não é possível definir. Desativar a inspeção de corpo de pedido permite que as mensagens maior do que 128 KB sejam enviados para o WAF, mas o corpo da mensagem não é inspecionado relativamente a vulnerabilidades.

## <a name="waf-exclusion-lists"></a>Listas de exclusão de WAF

![waf-exclusion.png](media/application-gateway-waf-configuration/waf-exclusion.png)

Listas de exclusão de WAF permitem que omitir determinados atributos de pedido de uma avaliação de WAF. Um exemplo comum é o que Active Directory inserido tokens que são utilizados para autenticação ou campos de palavra-passe. Esses atributos são suscetíveis a conter os carateres especiais que podem disparar um falso positivo das regras WAF. Assim que um atributo é adicionado à lista de exclusão de WAF, não é considerada por qualquer regra de WAF configurada e Active Directory. Listas de exclusão são globais em escopo.

Os seguintes atributos podem ser adicionados às listas de exclusão:

* Cabeçalhos de pedido
* Pedido de Cookies
* Nome de atributo de pedido (argumenty)

   * Dados de várias partes do formulário
   * XML
   * JSON
   * Args de consulta de URL

Pode especificar um cabeçalho de pedido exato, o corpo, o cookie ou correspondência de atributo de cadeia de consulta.  Ou, opcionalmente, pode especificar correspondências parciais. A exclusão está sempre um campo de cabeçalho, nunca no seu valor. Regras de exclusão são globais em escopo e aplicam-se a todas as páginas e todas as regras.

Seguem-se os operadores de critérios de correspondência suportados:

- **É igual a**:  Este operador é utilizado para uma correspondência exata. Por exemplo, para a seleção de um cabeçalho denominado **bearerToken**, utilize o operador equals com o Seletor de definido como **bearerToken**.
- **Começa com**: Este operador corresponde a todos os campos que começam com o valor de Seletor especificado.
- **Termina com**:  Este operador corresponde a todos os campos de solicitação que terminam com o valor de Seletor especificado.
- **Contém**: Este operador corresponde a todos os campos de solicitação que contêm o valor de Seletor especificado.
- **Igual a qualquer**: Este operador corresponde a todos os campos de pedido. * será o valor de Seletor.

Em todos os casos, a correspondência é sensível a maiúsculas e expressões regulares não são permitidos como seletores.

### <a name="examples"></a>Exemplos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Os exemplos seguintes demonstram a utilização de exclusões.

### <a name="example-1"></a>Exemplo 1

Neste exemplo, pretende excluir o cabeçalho do agente do usuário. O cabeçalho do pedido de agente do usuário contém uma cadeia de caracteres características que permite que os pontos de protocolo de rede identificar o tipo de aplicação, o sistema operativo, o fabricante de software ou a versão do software do agente do utilizador requerente do software. Para obter mais informações, consulte [agente do usuário](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/User-Agent).

Podem existir diversas razões para desativar a avaliar este cabeçalho. Pode haver uma cadeia de caracteres que o WAF vê e de que ele é malicioso. Por exemplo, o ataque clássico de SQL "x = x" numa cadeia de caracteres. Em alguns casos, isso pode ser tráfego legítimo. Por isso, poderá ter de excluir esse cabeçalho da versão de avaliação de WAF.

O seguinte cmdlet do PowerShell do Azure exclui o cabeçalho do agente do usuário da versão de avaliação:

```azurepowershell
$exclusion1 = New-AzApplicationGatewayFirewallExclusionConfig `
   -MatchVariable "RequestHeaderNames" `
   -SelectorMatchOperator "Equals" `
   -Selector "User-Agent"
```

### <a name="example-2"></a>Exemplo 2

Neste exemplo exclui o valor de *utilizador* parâmetro que é passado no pedido via a URL. Por exemplo, que é comum no seu ambiente para o campo de utilizador conter uma cadeia de caracteres que considera a WAF como conteúdo mal-intencionado, então ela é bloqueada-lo.  Pode excluir o parâmetro de utilizador em vez disso, para que o WAF não avaliar nada no campo.

O seguinte cmdlet do PowerShell do Azure exclui o parâmetro de utilizador da versão de avaliação:

```azurepowershell
$exclusion2 = New-AzApplicationGatewayFirewallExclusionConfig `
   -MatchVariable "RequestArgNames" `
   -SelectorMatchOperator "Equals" `
   -Selector "user"
```
Então, se o URL **http://www.contoso.com/?user=fdafdasfda** é passado para o WAF, ele não avalie a cadeia de caracteres **fdafdasfda**.

## <a name="next-steps"></a>Passos Seguintes

Depois de configurar as definições de WAF, pode aprender a ver os registos da WAF. Para obter mais informações, consulte [diagnóstico do Gateway de aplicação](application-gateway-diagnostics.md#diagnostic-logging).
