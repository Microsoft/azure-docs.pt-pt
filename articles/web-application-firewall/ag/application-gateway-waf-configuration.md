---
title: Limites de tamanho e listas de exclusão de aplicativos web na Azure Application Gateway - Portal Azure
description: Este artigo fornece informações sobre limites de tamanho de pedido de aplicação web e configuração de listas de exclusão em Gateway de aplicação com o portal Azure.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 02/20/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 2d34641fdecfe334e84347efe1a2f64482cae74b
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93040247"
---
# <a name="web-application-firewall-request-size-limits-and-exclusion-lists"></a>Limites de tamanho e listas de exclusão de pedidos de Web Application Firewall

O Azure Application Gateway Web Application Firewall (WAF) fornece proteção para aplicações web. Este artigo descreve os limites de tamanho do pedido da WAF e a configuração das listas de exclusão. Estas definições estão localizadas na Política WAF associada ao seu Gateway de Aplicação. Para saber mais sobre as políticas da WAF, consulte [o Firewall de Aplicações Web Azure no Gateway de Aplicações Azure](ag-overview.md) e [Crie políticas de Firewall de Aplicações Web para Gateway de aplicações](create-waf-policy-ag.md)

## <a name="waf-exclusion-lists"></a>Listas de exclusão da WAF

![Limites de tamanho do pedido](../media/application-gateway-waf-configuration/waf-policy.png)

As listas de exclusão da WAF permitem-lhe omitir determinados atributos de pedido de uma avaliação waf. Um exemplo comum são fichas inseridas no Ative Directory que são usadas para campos de autenticação ou palavra-passe. Tais atributos são propensos a conter caracteres especiais que podem desencadear um falso positivo das regras da WAF. Uma vez que um atributo é adicionado à lista de exclusão da WAF, não é considerado por nenhuma regra WAF configurada e ativa. As listas de exclusão são de âmbito global.

Os seguintes atributos podem ser adicionados às listas de exclusão pelo nome. Os valores do campo escolhido não são avaliados contra as regras da WAF, mas os seus nomes ainda estão (ver Exemplo 1 abaixo, o valor do cabeçalho User-Agent está excluído da avaliação da WAF). As listas de exclusão removem a inspeção do valor do campo.

* Pedido cabeçalhos
* Solicitar Cookies
* O nome de atributo de pedido (args) pode ser adicionado como elemento de exclusão, tais como:

   * Nome de campo de formulário
   * Entidade JSON
   * Url consulta string args

Pode especificar um cabeçalho de pedido exato, corpo, cookie ou combinação de atributos de cadeia de consulta.  Ou, opcionalmente, pode especificar correspondências parciais. As regras de exclusão são globais de âmbito, e aplicam-se a todas as páginas e regras.

Seguem-se os operadores de critérios de correspondência suportados:

- **Igual** : Este operador é utilizado para uma correspondência exata. Como exemplo, para selecionar um cabeçalho denominado **BearerToken,** utilize o operador de iguales com o seletor definido como **bearerToken** .
- **Começa por:** Este operador corresponde a todos os campos que começam com o valor do seletor especificado.
- **Termina com:** Este operador corresponde a todos os campos de pedido que terminam com o valor do seletor especificado.
- **Contém** : Este operador corresponde a todos os campos de pedido que contêm o valor do seletor especificado.
- **É igual a qualquer** : Este operador corresponde a todos os campos de pedido. * será o valor do seletor.

Em todos os casos, a correspondência é caso insensível e a expressão regular não é permitida como selecionadores.

> [!NOTE]
> Para obter mais informações e ajuda para resolver problemas, consulte [a resolução de problemas da WAF](web-application-firewall-troubleshoot.md).

### <a name="examples"></a>Exemplos

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Os exemplos que se seguem demonstram a utilização de exclusões.

#### <a name="example-1"></a>Exemplo 1

Neste exemplo, pretende excluir o cabeçalho do agente de utilizador. O cabeçalho de pedido do utilizador-agente contém uma cadeia característica que permite aos pares do protocolo de rede identificar o tipo de aplicação, sistema operativo, fornecedor de software ou versão de software do agente utilizador de software que solicita. Para obter mais informações, consulte [o User-Agent](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/User-Agent).

Pode haver várias razões para desativar a avaliação deste cabeçalho. Pode haver uma corda que a WAF vê e assume que é maliciosa. Por exemplo, o ataque clássico sql "x=x" numa corda. Em alguns casos, este pode ser um tráfego legítimo. Por isso, talvez precises de excluir este cabeçalho da avaliação da WAF.

O cmdlet Azure PowerShell exclui o cabeçalho do agente de utilizador da avaliação:

```azurepowershell
$exclusion1 = New-AzApplicationGatewayFirewallExclusionConfig `
   -MatchVariable "RequestHeaderNames" `
   -SelectorMatchOperator "Equals" `
   -Selector "User-Agent"
```
#### <a name="example-2"></a>Exemplo 2

Este exemplo exclui o valor do parâmetro do *utilizador* que é passado no pedido através do URL. Por exemplo, digamos que é comum no seu ambiente que o campo de utilizador contenha uma cadeia que o WAF vê como conteúdo malicioso, por isso bloqueia-o.  Pode excluir o parâmetro do utilizador neste caso para que o WAF não avalie nada no campo.

O cmdlet Azure PowerShell exclui o parâmetro do utilizador da avaliação:

```azurepowershell
$exclusion2 = New-AzApplicationGatewayFirewallExclusionConfig `
   -MatchVariable "RequestArgNames" `
   -SelectorMatchOperator "StartsWith" `
   -Selector "user"
```
Assim, se o URL `http://www.contoso.com/?user%281%29=fdafdasfda` for passado para a WAF, não avaliará a cadeia **fdafdasfda,** mas ainda avaliará o nome do parâmetro **281%29** . 

## <a name="waf-request-size-limits"></a>Limites de tamanho do pedido waf



O Firewall de Aplicação Web permite-lhe configurar limites de tamanho de pedido dentro dos limites inferior e superior. Estão disponíveis as seguintes configurações de limites de tamanho:

- O campo de tamanho do corpo de pedido máximo é especificado em quilobytes e controla o limite geral do tamanho do pedido, excluindo quaisquer uploads de ficheiros. Este campo tem um valor mínimo de 1 KB e um valor máximo de 128 KB. O valor padrão para o tamanho do corpo de pedido é de 128 KB.
- O campo limite de upload de ficheiros é especificado em MB e rege o tamanho máximo permitido de upload de ficheiros. Este campo pode ter um valor mínimo de 1 MB e os seguintes máximos:

   - 100 MB para gateways v1 Waf Médio
   - 500 MB para v1 Grandes portais WAF
   - 750 MB para gateways v2 WAF 

 O valor predefinido para o limite de upload de ficheiros é de 100 MB.

A WAF também oferece um botão configurável para ligar ou desligar a inspeção do corpo do pedido. Por predefinição, a inspeção do corpo de pedido está ativada. Se a inspeção do corpo do pedido for desligada, a WAF não avalia o conteúdo do corpo de mensagens HTTP. Nestes casos, a WAF continua a impor as regras da WAF em cabeçalhos, cookies e URI. Se a inspeção do corpo do pedido for desligada, então o campo de tamanho do corpo de pedido máximo não é aplicável e não pode ser definido. Desligar a inspeção do corpo de pedido permite que mensagens superiores a 128 KB sejam enviadas para a WAF, mas o corpo da mensagem não é inspecionado para obter vulnerabilidades.

## <a name="next-steps"></a>Passos seguintes

Depois de configurar as definições de WAF, pode aprender a visualizar os seus registos WAF. Para mais informações, consulte os [diagnósticos do Application Gateway](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging).
