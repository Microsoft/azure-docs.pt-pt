---
title: Aplicação web firewall aplicação limites de tamanho e listas de exclusão em Portal de Aplicações Azure - Portal Azure
description: Este artigo fornece informações sobre os limites de tamanho do pedido de firewall de aplicação web e listas de exclusão configuração no Gateway de Aplicação com o portal Azure.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 02/20/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 7244788bbc7431c7f26363b2852babb72d5697e9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77526795"
---
# <a name="web-application-firewall-request-size-limits-and-exclusion-lists"></a>Web Application Firewall solicitam limites de tamanho e listas de exclusão

O Firewall de aplicação web da Aplicação Gateway (WAF) da Aplicação Azure Application (WAF) fornece proteção para aplicações web. Este artigo descreve os limites de tamanho do pedido waf e a configuração das listas de exclusão. Estas definições estão localizadas na Política WAF associada ao seu Gateway de Aplicação. Para saber mais sobre as políticas da WAF, consulte o Firewall de [aplicação web do Azure no Portal de Aplicações Azure](ag-overview.md) e crie políticas de firewall de [aplicação web para gateway](create-waf-policy-ag.md) de aplicações

## <a name="waf-exclusion-lists"></a>Listas de exclusão da WAF

![Limites de tamanho de pedido](../media/application-gateway-waf-configuration/waf-policy.png)

As listas de exclusão waf permitem-lhe omitir certos atributos de pedido de uma avaliação WAF. Um exemplo comum é o Ative Directory inserida em fichas que são usadas para campos de autenticação ou palavra-passe. Tais atributos são propensos a conter caracteres especiais que podem desencadear um falso positivo das regras waf. Uma vez que um atributo é adicionado à lista de exclusão waf, não é considerado por qualquer regra WAF configurada e ativa. As listas de exclusão são globais.

Os seguintes atributos podem ser adicionados às listas de exclusão pelo nome. Os valores do campo escolhido não são avaliados em relação às regras da WAF, mas os seus nomes ainda são (ver Exemplo 1 abaixo, o valor do cabeçalho user-agent é excluído da avaliação waf). As listas de exclusão removem a inspeção do valor do campo.

* Cabeçalhos dos Pedidos
* Solicitar Cookies
* O nome do atributo de pedido (args) pode ser adicionado como elemento de exclusão, tais como:

   * Nome de campo de formulário
   * Entidade XML
   * Entidade JSON
   * URL consulta string args

Pode especificar um cabeçalho de pedido exato, corpo, cookie ou correspondência de atributo de corda de consulta.  Ou, pode especificar opcionalmente fósforos parciais. As regras de exclusão são globais e aplicam-se a todas as páginas e todas as regras.

Seguem-se os critérios de correspondência suportados:

- **Igual:** Este operador é utilizado para uma correspondência exata. Como exemplo, para selecionar um cabeçalho chamado **bearerToken,** utilize o operador igual com o seletor definido como **bearerToken**.
- **Começa com:** Este operador corresponde a todos os campos que começam com o valor do seletor especificado.
- **Termina com**: Este operador corresponde a todos os campos de pedidos que terminam com o valor do seletor especificado.
- **Contém:** Este operador corresponde a todos os campos de pedidos que contenham o valor do seletor especificado.
- **Igual a qualquer**um: Este operador corresponde a todos os campos de pedidos. * será o valor do seletor.

Em todos os casos, a correspondência é caso insensível e a expressão regular não é permitida como selecionadores.

> [!NOTE]
> Para obter mais informações e ajuda para resolver problemas, consulte a resolução de [problemas da WAF](web-application-firewall-troubleshoot.md).

### <a name="examples"></a>Exemplos

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Os exemplos que se seguem demonstram a utilização de exclusões.

#### <a name="example-1"></a>Exemplo 1

Neste exemplo, pretende excluir o cabeçalho do utilizador-agente. O cabeçalho de pedido do utilizador-agente contém uma cadeia característica que permite aos pares do protocolo de rede identificar o tipo de aplicação, sistema operativo, fornecedor de software ou versão de software do agente utilizador do software solicitado. Para mais informações, consulte [o Utilizador-Agente](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/User-Agent).

Pode haver várias razões para desativar a avaliação deste cabeçalho. Pode haver uma corda que a WAF vê e assume que é maliciosa. Por exemplo, o clássico ataque SQL "x=x" numa corda. Em alguns casos, este pode ser um tráfego legítimo. Por isso, talvez precises de excluir este cabeçalho da avaliação da WAF.

O seguinte cmdlet Azure PowerShell exclui o cabeçalho do utilizador-agente da avaliação:

```azurepowershell
$exclusion1 = New-AzApplicationGatewayFirewallExclusionConfig `
   -MatchVariable "RequestHeaderNames" `
   -SelectorMatchOperator "Equals" `
   -Selector "User-Agent"
```
#### <a name="example-2"></a>Exemplo 2

Este exemplo exclui o valor no parâmetro do *utilizador* que é passado no pedido através do URL. Por exemplo, diga que é comum no seu ambiente que o campo de utilizador contenha uma cadeia que a WAF vê como conteúdo malicioso, por isso bloqueia-a.  Neste caso, pode excluir o parâmetro do utilizador para que o WAF não avalie nada no campo.

O seguinte cmdlet Azure PowerShell exclui o parâmetro do utilizador da avaliação:

```azurepowershell
$exclusion2 = New-AzApplicationGatewayFirewallExclusionConfig `
   -MatchVariable "RequestArgNames" `
   -SelectorMatchOperator "StartsWith" `
   -Selector "user"
```
Portanto, se `http://www.contoso.com/?user%281%29=fdafdasfda` o URL for passado para o WAF, não avaliará a **cadeia fdafdasfda,** mas ainda avaliará o nome do parâmetro **user%281%29**. 

## <a name="waf-request-size-limits"></a>LIMITES de tamanho de pedido waf



A Firewall da Aplicação Web permite-lhe configurar os limites de tamanho do pedido dentro dos limites inferiores e superiores. Estão disponíveis as seguintes configurações de dois limites de tamanho:

- O campo de tamanho do corpo de pedido máximo é especificado em quilobytes e controla o limite de tamanho total do pedido, excluindo quaisquer uploads de ficheiros. Este campo pode variar de 1-KB mínimo a 128-KB valor máximo. O valor padrão para o tamanho do corpo de pedido é de 128 KB.
- O campo limite de carregamento de ficheiros é especificado em MB e rege o tamanho máximo permitido de upload de ficheiros. Este campo pode ter um valor mínimo de 1 MB e os seguintes máximos:

   - 100 MB para gateways v1 Medium WAF
   - 500 MB para portais v1 grandes WAF
   - 750 MB para gateways v2 WAF 

 O valor padrão para o limite de upload de ficheiros é de 100 MB.

A WAF também oferece um botão configurável para ativar a inspeção corporal de pedido dentro ou fora. Por predefinição, a inspeção do corpo de pedidos está ativada. Se a inspeção do corpo de pedidos for desligada, a WAF não avalia o conteúdo do corpo de mensagens HTTP. Nesses casos, a WAF continua a impor as regras da WAF em cabeçalhos, cookies e URI. Se a inspeção do corpo de pedido for desligada, então o campo de tamanho do corpo de pedido máximo não é aplicável e não pode ser definido. Desligar a inspeção do corpo de pedidos permite que mensagens superiores a 128 KB sejam enviadas para a WAF, mas o corpo da mensagem não é inspecionado para obter vulnerabilidades.

## <a name="next-steps"></a>Passos seguintes

Depois de configurar as definições de WAF, pode aprender a visualizar os seus registos WAF. Para mais informações, consulte [diagnósticos de Gateway](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging)de aplicação .
