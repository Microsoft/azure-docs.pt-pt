---
title: Regras personalizadas do firewall do aplicativo Web do Azure (WAF) V2
description: Este artigo fornece uma visão geral das regras personalizadas do WAF (firewall do aplicativo Web) V2 no gateway Aplicativo Azure.
services: application-gateway
ms.topic: article
author: vhorne
ms.service: application-gateway
ms.date: 6/18/2019
ms.author: victorh
ms.openlocfilehash: 2e96a2a2dd5504c906b5fb84b643467a83518f21
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027570"
---
# <a name="overview-custom-rules-for-web-application-firewall-v2"></a>Visão geral: regras personalizadas para o Firewall do aplicativo Web v2

O WAF (firewall do aplicativo Web) de gateway Aplicativo Azure v2 vem com um conjunto de regras pré-configurado e gerenciado por plataforma que oferece proteção contra vários tipos diferentes de ataques. Esses ataques incluem scripts entre sites, injeção de SQL e outros. Se você for um administrador de WAF, talvez queira escrever suas próprias regras para ampliar as regras de conjunto de regras principais. Suas regras podem bloquear ou permitir o tráfego solicitado com base nos critérios de correspondência.

Com as regras personalizadas, você pode criar suas próprias regras, que são avaliadas para cada solicitação que passa pelo WAF. Essas regras têm uma prioridade mais alta do que o restante das regras nos conjuntos de regras gerenciadas. As regras personalizadas contêm um nome de regra, uma prioridade de regra e uma matriz de condições de correspondência. Se essas condições forem atendidas, uma ação será tomada para permitir ou bloquear.

Por exemplo, você pode criar uma regra para bloquear todas as solicitações de um endereço IP no intervalo 192.168.5.4/24. Nessa regra, o operador é *IPMatch*, *matchValues* é o intervalo de endereços IP (192.168.5.4/24) e a *ação* é bloquear o tráfego. Você também define o nome e a prioridade da regra.

As regras personalizadas dão suporte ao uso da lógica de composição para fazer regras mais avançadas que atendam às suas necessidades de segurança. Por exemplo, "(condição 1 *e* condição 2) *ou* condição 3)" significa que, se a condição 1 *e* a condição 2 forem atendidas *ou* se a condição 3 for atendida, WAF deverá executar a ação especificada na regra personalizada.

Diferentes condições de correspondência dentro da mesma regra são sempre compostas usando *e*. Por exemplo, uma regra que usa *e* pode especificar para bloquear o tráfego de um determinado endereço IP e somente se um determinado navegador estiver sendo usado.

Se você quiser usar o *ou* o para duas condições diferentes, as duas condições deverão estar em regras diferentes. Por exemplo, a regra que usa *ou* pode especificar para bloquear o tráfego de um determinado endereço IP ou bloquear o tráfego se um determinado navegador estiver sendo usado.

> [!NOTE]
> O número máximo de regras personalizadas de WAF é 100. Para obter mais informações sobre os limites do gateway de aplicativo, consulte [assinatura e limites de serviço, cotas e restrições do Azure](../azure-subscription-service-limits.md#application-gateway-limits).

Também há suporte para expressões regulares em regras personalizadas, assim como nos conjuntos de regras principais. Para obter exemplos dessas regras, consulte "exemplo 3" e "exemplo 5" em [criar e usar regras personalizadas de firewall do aplicativo Web](create-custom-waf-rules.md).

> [!NOTE]
> As regras personalizadas não estão disponíveis no WAF de SKU v1.

## <a name="allowing-or-blocking-traffic"></a>Permitindo ou bloqueando o tráfego

Permitir ou bloquear o tráfego é simples com regras personalizadas. Por exemplo, você pode bloquear todo o tráfego proveniente de um intervalo de endereços IP. Você pode fazer outra regra para permitir o tráfego se a solicitação vier de um determinado navegador.

Para permitir algo, verifique se o parâmetro `-Action` está definido como **permitir**. Para bloquear algo, verifique se o parâmetro `-Action` está definido como **Bloquear**, conforme mostrado no código a seguir:

```azurepowershell
$AllowRule = New-AzApplicationGatewayFirewallCustomRule `
   -Name example1 `
   -Priority 2 `
   -RuleType MatchRule `
   -MatchCondition $condition `
   -Action Allow

$BlockRule = New-AzApplicationGatewayFirewallCustomRule `
   -Name example2 `
   -Priority 2 `
   -RuleType MatchRule `
   -MatchCondition $condition `
   -Action Block
```

O `$BlockRule` anterior é mapeado para a seguinte regra personalizada no Azure Resource Manager:

```json
"customRules": [
      {
        "name": "blockEvilBot",
        "priority": 2,
        "ruleType": "MatchRule",
        "action": "Block",
        "matchConditions": [
          {
            "matchVariables": [
              {
                "variableName": "RequestHeaders",
                "selector": "User-Agent"
              }
            ],
            "operator": "Contains",
            "negationConditon": false,
            "matchValues": [
              "evilbot"
            ],
            "transforms": [
              "Lowercase"
            ]
          }
        ]
      }
    ], 
```

Essa regra personalizada contém um nome, uma prioridade, uma ação e uma matriz de condições de correspondência que devem ser atendidas para que a ação ocorra. Para obter descrições dos campos de regra personalizada, consulte as seções a seguir. Para obter exemplos de regras personalizadas, consulte [criar e usar regras personalizadas de firewall do aplicativo Web](create-custom-waf-rules.md).

## <a name="custom-rule-fields"></a>Campos de regra personalizada

### <a name="name-optional"></a>Nome (opcional)

Este é o nome da regra. O nome aparece nos logs.

### <a name="priority-required"></a>Prioridade (obrigatória)

- A prioridade determina a ordem na qual as regras são avaliadas. Quanto menor o valor, mais cedo a avaliação da regra. O intervalo permitido é de 1 a 100.
- A prioridade deve ser exclusiva entre todas as regras personalizadas. Uma regra com uma prioridade de 40 é avaliada antes de uma regra com prioridade de 80.

### <a name="rule-type-required"></a>Tipo de regra (obrigatório)

Atualmente, o tipo de regra deve ser **MatchRule**.

### <a name="match-variable-required"></a>Variável de correspondência (obrigatório)

A variável de correspondência deve ser uma das seguintes:

- RemoteAddr: o endereço IP ou o nome do host da conexão do computador remoto
- RequestMethod: o método de solicitação HTTP (GET, POST, PUT, DELETE e assim por diante).
- QueryString: a variável no URI.
- Deargs: os argumentos que são enviados no corpo da POSTAgem. As regras personalizadas que usam essa variável de correspondência serão aplicadas somente se o cabeçalho Content-Type estiver definido como "application/x-www-form-urlencoded" e "multipart/form-data".
- RequestUri: o URI da solicitação.
- RequestHeaders: os cabeçalhos da solicitação.
- RequestBody: a variável que contém todo o corpo da solicitação como um todo. As regras personalizadas que usam essa variável de correspondência serão aplicadas somente se o cabeçalho Content-Type estiver definido como "application/x-www-form-urlencoded". 
- RequestCookies: os cookies da solicitação.

### <a name="selector-optional"></a>Seletor (opcional)

O seletor descreve o campo da coleção matchVariable. Por exemplo, se matchVariable for "RequestHeaders", o seletor poderá estar no cabeçalho User-Agent.

### <a name="operator-required"></a>Operador (obrigatório)

O operador deve ser um dos seguintes:

- IPMatch: esse operador é usado somente quando a variável de correspondência é *RemoteAddr*.
- Equals: a entrada é igual a Matchvalue.
- Contains
- LessThan
- GreaterThan
- LessThanOrEqual
- GreaterThanOrEqual
- BeginsWith
- EndsWith
- Regex

### <a name="negate-condition-optional"></a>Negar condição (opcional)

Nega a condição atual.

### <a name="transform-optional"></a>Transformação (opcional)

Uma lista de cadeias de caracteres com os nomes de transformações a serem concluídas antes da tentativa de correspondência. As transformações incluem:

- Minúsculas
- Trim
- UrlDecode
- UrlEncode 
- RemoveNulls
- HtmlEntityDecode

### <a name="match-values-required"></a>Valores de correspondência (obrigatório)

O campo matchValues é uma lista de valores a serem correspondidos, que pode ser considerado como sendo *ou*' Ed '. Por exemplo, os valores podem ser endereços IP ou outras cadeias de caracteres. O formato do valor depende do operador anterior.

### <a name="action-required"></a>Ação (obrigatório)

O campo ação oferece as seguintes opções: 

- Permitir: autoriza a transação e ignora todas as regras subsequentes. Isso significa que a solicitação especificada é adicionada à lista de permissões e, depois que ela é correspondida, a solicitação para uma avaliação adicional e é enviada para o pool de back-end. As regras que estão na lista de permissões não são avaliadas para regras personalizadas adicionais ou regras gerenciadas.

- Bloquear: bloqueia a transação com base em *SecDefaultAction* (modo de detecção/prevenção). Como a ação permitir, depois que a solicitação é avaliada e adicionada à lista de bloqueios, a avaliação é interrompida e a solicitação é bloqueada. As solicitações subsequentes que atendem às mesmas condições não são avaliadas. Eles são bloqueados somente. 

- Log: permite que a regra grave no log e permite que o restante das regras seja executado para avaliação. As regras personalizadas subsequentes são avaliadas em ordem de prioridade, seguidas pelas regras gerenciadas.

## <a name="next-steps"></a>Passos seguintes

Agora que você aprendeu sobre regras personalizadas, você pode [criar suas próprias regras personalizadas](create-custom-waf-rules.md).
