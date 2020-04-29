---
title: Listas de exclusão de firewall de aplicações web em Azure Front Door - portal Azure
description: Este artigo fornece informações sobre a configuração das listas de exclusão na Frente Azure com o portal Azure.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 02/25/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 6ed382e88700e4ecd7f8de20a2c8da7ed3c13566
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77925933"
---
# <a name="web-application-firewall-waf-with-front-door-service-exclusion-lists"></a>Firewall de aplicação web (WAF) com listas de exclusão do Serviço da Porta Frontal 

Por vezes, a Firewall de Aplicações Web (WAF) pode bloquear um pedido que pretende permitir para a sua aplicação. Por exemplo, o Diretório Ativo insere fichas que são usadas para autenticação. Estas fichas podem conter caracteres especiais que podem desencadear um falso positivo das regras da WAF. As listas de exclusão waf permitem-lhe omitir certos atributos de pedido de uma avaliação WAF.  Uma lista de exclusão pode ser configurada usando [PowserShell,](https://docs.microsoft.com/powershell/module/az.frontdoor/New-AzFrontDoorWafManagedRuleExclusionObject?view=azps-3.5.0) [Azure CLI,](https://docs.microsoft.com/cli/azure/ext/front-door/network/front-door/waf-policy/managed-rules/exclusion?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-managed-rules-exclusion-add) [Rest API](https://docs.microsoft.com/rest/api/frontdoorservice/webapplicationfirewall/policies/createorupdate)ou o portal Azure. O exemplo que se segue mostra a configuração do portal Azure. 
## <a name="configure-exclusion-lists-using-the-azure-portal"></a>Configure listas de exclusão utilizando o portal Azure
**Gerir exclusões** é acessível a partir do portal WAF de acordo com **as regras geridas**

![Gerir](../media/waf-front-door-exclusion/exclusion1.png)
![a exclusão Gerir exclusion_add](../media/waf-front-door-exclusion/exclusion2.png)

 Uma lista de ![exclusão de exemplo: Gerir exclusion_define](../media/waf-front-door-exclusion/exclusion3.png)

Este exemplo exclui o valor no campo de cabeçalho do *utilizador.* Um pedido válido pode incluir o campo de *utilizador* que contém uma cadeia que desencadeia uma regra de injeção SQL. Neste caso, pode excluir o parâmetro do *utilizador* para que a regra WAF não avalie nada no campo.

Os seguintes atributos podem ser adicionados às listas de exclusão pelo nome. Os valores dos campos que usa não são avaliados contra as regras da WAF, mas os seus nomes são avaliados. As listas de exclusão removem a inspeção do valor do campo.

* Pedir nome cabeçalho
* Solicitar nome de cookie
* Nome de args de corda de consulta
* Solicitar o nome do post do corpo args

Pode especificar um cabeçalho de pedido exato, corpo, cookie ou correspondência de atributo de corda de consulta.  Ou, pode especificar opcionalmente fósforos parciais. Os seguintes operadores são os critérios de correspondência suportados:

- **Igual:** Este operador é utilizado para uma correspondência exata. Por exemplo, para selecionar um cabeçalho chamado **bearerToken,** utilize o operador igual com o seletor definido como **bearerToken**.
- **Começa com:** Este operador corresponde a todos os campos que começam com o valor do seletor especificado.
- **Termina com**: Este operador corresponde a todos os campos de pedidos que terminam com o valor do seletor especificado.
- **Contém:** Este operador corresponde a todos os campos de pedidos que contenham o valor do seletor especificado.
- **Igual a qualquer**um: Este operador corresponde a todos os campos de pedidos. * é o valor do seletor.

Nomes de cabeçalho e biscoitos são insensíveis.

Pode aplicar a lista de exclusão a todas as regras dentro da regra gerida definida, às regras de um determinado grupo de regras, ou a uma regra única, como mostrado no exemplo anterior. 

## <a name="next-steps"></a>Passos seguintes

Depois de configurar as definições de WAF, aprenda a ver os seus registos WAF. Para mais informações, consulte os [diagnósticos da Porta da Frente](../afds/waf-front-door-monitor.md).
