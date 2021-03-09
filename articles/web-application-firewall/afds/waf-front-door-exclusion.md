---
title: Listas de exclusão de firewall de aplicações web em Azure Front Door - Portal Azure
description: Este artigo fornece informações sobre a configuração das listas de exclusão na Frente Azure com o portal Azure.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/10/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: a92679bb3114c4a60870424f3ec68a8de7b303da
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102499922"
---
# <a name="web-application-firewall-waf-with-front-door-service-exclusion-lists"></a>Firewall de aplicação web (WAF) com listas de exclusão do Serviço de Porta Frontal 

Por vezes, a Firewall de Aplicações Web (WAF) pode bloquear um pedido que pretende permitir para a sua aplicação. Por exemplo, o Ative Directory insere fichas que são utilizadas para autenticação. Estes tokens podem conter caracteres especiais que podem desencadear um falso positivo das regras da WAF. As listas de exclusão da WAF permitem-lhe omitir determinados atributos de pedido de uma avaliação waf.  Uma lista de exclusão pode ser configurada usando  [PowerShell,](/powershell/module/az.frontdoor/New-AzFrontDoorWafManagedRuleExclusionObject) [Azure CLI,](/cli/azure/ext/front-door/network/front-door/waf-policy/managed-rules/exclusion#ext-front-door-az-network-front-door-waf-policy-managed-rules-exclusion-add) [Rest API](/rest/api/frontdoorservice/webapplicationfirewall/policies/createorupdate)ou o portal Azure. O exemplo a seguir mostra a configuração do portal Azure. 
## <a name="configure-exclusion-lists-using-the-azure-portal"></a>Configurar listas de exclusão utilizando o portal Azure
**Gerir exclusões** é acessível a partir do portal WAF de acordo com **as regras geridas**

![Gerir exclusão ](../media/waf-front-door-exclusion/exclusion1.png)
 ![ Gerir exclusion_add](../media/waf-front-door-exclusion/exclusion2.png)

 Uma lista de exclusão de exemplo: ![ Gerir exclusion_define](../media/waf-front-door-exclusion/exclusion3.png)

Este exemplo exclui o valor no campo do cabeçalho do *utilizador.* Um pedido válido pode incluir o campo de *utilizador* que contém uma cadeia que desencadeia uma regra de injeção SQL. Pode excluir o parâmetro do *utilizador* neste caso para que a regra WAF não avalie nada no campo.

Os seguintes atributos podem ser adicionados às listas de exclusão pelo nome. Os valores dos campos que utiliza não são avaliados contra as regras da WAF, mas os seus nomes são avaliados. As listas de exclusão removem a inspeção do valor do campo.

* Pedido nome do cabeçalho
* Solicitar nome de cookies
* Nome de args de cadeia de consulta
* Solicitar nome de args post do corpo

Pode especificar um cabeçalho de pedido exato, corpo, cookie ou combinação de atributos de cadeia de consulta.  Ou, opcionalmente, pode especificar correspondências parciais. Os seguintes operadores são os critérios de correspondência suportados:

- **Igual**: Este operador é utilizado para uma correspondência exata. Por exemplo, para selecionar um cabeçalho denominado **BearerToken**, utilize o operador de iguales com o seletor definido como **bearerToken**.
- **Começa por:** Este operador corresponde a todos os campos que começam com o valor do seletor especificado.
- **Termina com:** Este operador corresponde a todos os campos de pedido que terminam com o valor do seletor especificado.
- **Contém**: Este operador corresponde a todos os campos de pedido que contêm o valor do seletor especificado.
- **É igual a qualquer**: Este operador corresponde a todos os campos de pedido. * é o valor do seletor.

Nomes de cabeçalho e biscoitos são insensíveis a caso.

Se um valor de cabeçalho, valor de cookies, valor de argumento pós-argumentação ou valor de argumento de consulta produzir falsos positivos para algumas regras, pode excluir essa parte do pedido da consideração pela regra:


|matchVariableName de registos WAF  |Exclusão de regras no Portal  |
|---------|---------|
|CookieValue:SOME_NAME        |Solicitar nome de cookies Igual a SOME_NAME|
|HeaderValue:SOME_NAME        |Pedido nome de cabeçalho Igual a SOME_NAME|
|PostParamValue:SOME_NAME     |Solicitar nome de args post do corpo Igual SOME_NAME|
|ConsultaParamValue:SOME_NAME    |Nome de args de cadeia de consulta é igual a SOME_NAME|


Atualmente, apenas apoiamos exclusões de regras para os números de matchVariableNames acima nos seus registos WAF. Para qualquer outro matchVariableNames, você deve desativar regras que dão falsos positivos, ou criar uma regra personalizada que permita explicitamente esses pedidos. Em particular, quando o nome de MatchVariable é CookieName, HeaderName, PostParamName ou QueryParamName, significa que o nome em si está desencadeando a regra. A exclusão de regras não tem suporte para estes matchVariableNames neste momento.


Se excluir um post de corpo request chamado *FOO,* nenhuma regra deve mostrar PostParamValue:FOO como o nome matchVariableName nos seus registos WAF. No entanto, ainda pode ver uma regra com matchVariableName InitialBodyContents que corresponde ao valor do post param FOO uma vez que os valores post param fazem parte dos InitialBodyContents.

Pode aplicar listas de exclusão a todas as regras dentro da regra gerida definida, às regras de um grupo de regras específicos, ou a uma única regra, como mostrado no exemplo anterior.

## <a name="define-exclusion-based-on-web-application-firewall-logs"></a>Defina exclusão com base em registos de firewall de aplicação web
 A monitorização e registo de registos da [Azure Web Application Firewall](waf-front-door-monitor.md) mostra detalhes compatíveis com um pedido bloqueado. Se um valor de cabeçalho, valor de cookies, valor de argumento pós-post ou valor de argumento de consulta produzir falsos positivos para algumas regras, pode excluir que parte do pedido seja considerado pela regra. O quadro a seguir mostra valores de exemplo a partir de registos WAF e as condições de exclusão correspondentes.

|matchVariableName de registos WAF    |Exclusão de regras no Portal|
|--------|------|
|CookieValue:SOME_NAME  |Solicitar nome de cookies Igual a SOME_NAME|
|HeaderValue:SOME_NAME  |Pedido nome de cabeçalho Igual a SOME_NAME|
|PostParamValue:SOME_NAME|  Solicitar nome de args post do corpo Igual SOME_NAME|
|ConsultaParamValue:SOME_NAME| Nome de args de cadeia de consulta é igual a SOME_NAME|


## <a name="next-steps"></a>Passos seguintes

Depois de configurar as definições de WAF, aprenda a visualizar os seus registos WAF. Para mais informações, consulte [os diagnósticos da Porta Frontal.](../afds/waf-front-door-monitor.md)