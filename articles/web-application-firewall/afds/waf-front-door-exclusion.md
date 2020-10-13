---
title: Listas de exclusão de firewall de aplicações web em Azure Front Door - Portal Azure
description: Este artigo fornece informações sobre a configuração das listas de exclusão na Frente Azure com o portal Azure.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 02/25/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 6ed382e88700e4ecd7f8de20a2c8da7ed3c13566
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "77925933"
---
# <a name="web-application-firewall-waf-with-front-door-service-exclusion-lists"></a>Firewall de aplicação web (WAF) com listas de exclusão do Serviço de Porta Frontal 

Por vezes, a Firewall de Aplicações Web (WAF) pode bloquear um pedido que pretende permitir para a sua aplicação. Por exemplo, o Ative Directory insere fichas que são utilizadas para autenticação. Estes tokens podem conter caracteres especiais que podem desencadear um falso positivo das regras da WAF. As listas de exclusão da WAF permitem-lhe omitir determinados atributos de pedido de uma avaliação waf.  Uma lista de exclusão pode ser configurada usando  [PowserShell,](https://docs.microsoft.com/powershell/module/az.frontdoor/New-AzFrontDoorWafManagedRuleExclusionObject?view=azps-3.5.0) [Azure CLI,](https://docs.microsoft.com/cli/azure/ext/front-door/network/front-door/waf-policy/managed-rules/exclusion?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-managed-rules-exclusion-add) [Rest API](https://docs.microsoft.com/rest/api/frontdoorservice/webapplicationfirewall/policies/createorupdate)ou o portal Azure. O exemplo a seguir mostra a configuração do portal Azure. 
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

Pode aplicar a lista de exclusão a todas as regras dentro da regra gerida definida, às regras de um grupo de regras específicos, ou a uma única regra, como mostrado no exemplo anterior. 

## <a name="next-steps"></a>Passos seguintes

Depois de configurar as definições de WAF, aprenda a visualizar os seus registos WAF. Para mais informações, consulte [os diagnósticos da Porta Frontal.](../afds/waf-front-door-monitor.md)
