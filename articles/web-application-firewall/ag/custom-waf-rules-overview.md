---
title: Azure Web Application Firewall (WAF) v2 regras personalizadas no Gateway de aplicações
description: Este artigo fornece uma visão geral das regras personalizadas da Web Application Firewall (WAF) v2 no Azure Application Gateway.
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 04/14/2020
ms.author: victorh
ms.openlocfilehash: c0f802f5113e38e811c110ee913099e76fa7be0b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "81383821"
---
# <a name="custom-rules-for-web-application-firewall-v2-on-azure-application-gateway"></a>Regras personalizadas para Firewall de Aplicação Web v2 no Gateway de aplicações Azure

O Azure Application Gateway Web Application Firewall (WAF) v2 vem com um conjunto de regras pré-configurados e geridos pela plataforma que oferece proteção contra vários tipos de ataques. Estes ataques incluem scripts de cross site, injeção de SQL, e outros. Se for um administrador da WAF, pode querer escrever as suas próprias regras para aumentar as regras básicas definidas (CRS). As suas regras podem bloquear ou permitir o tráfego solicitado com base em critérios correspondentes.

As regras personalizadas permitem-lhe criar as suas próprias regras que são avaliadas para cada pedido que passa pela WAF. Estas regras têm uma prioridade maior do que o resto das regras nos conjuntos de regras geridos. As regras personalizadas contêm um nome de regra, prioridade de regra, e uma série de condições de correspondência. Se estas condições forem satisfeitas, serão tomadas medidas (para permitir ou bloquear).

Por exemplo, pode bloquear todos os pedidos a partir de um endereço IP no intervalo 192.168.5.4/24. Nesta regra, o operador é *IPMatch,* o matchValues é a gama de endereços IP (192.168.5.4/24), e a ação consiste em bloquear o tráfego. Também definiu o nome e a prioridade da regra.

As regras personalizadas suportam o uso de lógica de compostagem para fazer regras mais avançadas que respondam às suas necessidades de segurança. Por exemplo, (Condição 1 **e** Condição 2) **ou** Condição 3). Isto significa que se a Condição 1 **e** a Condição 2 forem cumpridas, **ou** se a Condição 3 for cumprida, a WAF deve tomar as medidas especificadas na regra personalizada.

As diferentes condições de correspondência dentro da mesma regra são sempre agravadas usando **e**. Por exemplo, bloqueie o tráfego a partir de um endereço IP específico, e apenas se estiver a usar um determinado navegador.

Se quiser **ou** duas condições diferentes, as duas condições devem estar em regras diferentes. Por exemplo, bloqueie o tráfego a partir de um endereço IP específico ou bloqueie o tráfego se estiver a usar um navegador específico.

> [!NOTE]
> O número máximo de regras personalizadas da WAF é de 100. Para obter mais informações sobre os limites do Gateway de aplicações, consulte [os limites de subscrição e serviços, quotas e restrições de subscrição do Azure.](../../azure-resource-manager/management/azure-subscription-service-limits.md#application-gateway-limits)

Expressões regulares também são suportadas em regras personalizadas, assim como nas regras do CRS. Por exemplo, consulte os Exemplos 3 e 5 em [Criar e utilize regras de firewall de aplicações web personalizadas](create-custom-waf-rules.md).

## <a name="allowing-vs-blocking"></a>Permitindo vs. bloqueio

Permitir e bloquear o tráfego é simples com regras personalizadas. Por exemplo, pode bloquear todo o tráfego proveniente de uma série de endereços IP. Pode fazer outra regra para permitir o tráfego se o pedido vier de um navegador específico.

Para permitir algo, certifique-se de que o `-Action` parâmetro está definido para **Permitir**. Para bloquear algo, certifique-se de que o `-Action` parâmetro está definido para **bloquear**.

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

Os mapas anteriores `$BlockRule` à seguinte regra personalizada no Azure Resource Manager:

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

Esta regra personalizada contém um nome, prioridade, uma ação e o conjunto de condições de correspondência que devem ser cumpridas para que a ação tenha lugar. Para mais informações sobre estes campos, consulte as seguintes descrições de campo. Por exemplo, regras personalizadas, consulte [Criar e utilizar regras de firewall de aplicações web personalizadas](create-custom-waf-rules.md).

## <a name="fields-for-custom-rules"></a>Campos para regras personalizadas

### <a name="name-optional"></a>Nome [opcional]

O nome da regra.  Aparece nos registos.

### <a name="priority-required"></a>Prioridade [necessária]

- Determina a ordem de avaliação da regra. Quanto menor o valor, mais cedo a avaliação da regra. O intervalo permitido é de 1 a 100. 
- Deve ser único em todas as regras personalizadas. Uma regra com prioridade 40 é avaliada antes de uma regra com prioridade 80.

### <a name="rule-type-required"></a>Tipo de regra [obrigatório]

Atualmente, deve ser **MatchRule**.

### <a name="match-variable-required"></a>Variável de correspondência [necessária]

Deve ser uma das variáveis:

- RemoteAddr – Endereço IP/nome de anfitrião da ligação remota do computador
- RequestMethod – MÉTODO DE PEDIDO HTTP (GET, POST, PUT, DELETE, e assim por diante.)
- QueryString – Variável no URI
- PostArgs – Argumentos enviados no corpo DO POST. As regras personalizadas que utilizam esta variável de correspondência só são aplicadas se o cabeçalho 'Tipo de Conteúdo' for definido como 'aplicação/x-www-forma-urlencoded' e 'multiparte/dados de formulário'.
- RequestUri – URI do pedido
- RequestHeaders – Cabeçalhos do pedido
- RequestBody – Isto contém todo o corpo de pedidos como um todo. As regras personalizadas que utilizam esta variável de correspondência só são aplicadas se o cabeçalho 'Tipo de Conteúdo' estiver definido para 'aplicação/x-www-forma-urlencoded'. 
- RequestCookies – Cookies do pedido

### <a name="selector-optional"></a>Selecionador [opcional]

Descreve o campo da coleção matchVariable. Por exemplo, se o matchVariable for RequestHeaders, o seletor pode estar no cabeçalho *do Agente de Utilizador.*

### <a name="operator-required"></a>Operador [obrigatório]

Deve ser um dos seguintes operadores:

- IPMatch - usado apenas quando a variável de correspondência é *RemoteAddr*
- Igual – a entrada é a mesma que o MatchValue
- Contains
- LessThan
- GreaterThan
- LessThanOrEqual
- GreaterThanOrEqual
- BeginsWith
- EndsWith
- Regex
- Geomatch (pré-visualização)

### <a name="negate-condition-optional"></a>Condição de negação [opcional]

Nega a condição atual.

### <a name="transform-optional"></a>Transformar [opcional]

Uma lista de cordas com nomes de transformações a fazer antes da partida ser tentada. Estas podem ser as seguintes transformações:

- Minúsculas
- Trim
- UrlDecode
- UrlEncode 
- RemoveNulls
- HtmlEntityDecode

### <a name="match-values-required"></a>Valores de correspondência [necessários]

Lista de valores a comparar, que podem ser considerados como *sendo OR*'ed. Por exemplo, podem ser endereços IP ou outras cordas. O formato de valor depende do operador anterior.

### <a name="action-required"></a>Ação [necessária]

- Permitir – Autorizar a transação, ignorando todas as outras regras. O pedido especificado é adicionado à lista de admissão e uma vez combinado, o pedido para a avaliação posterior e é enviado para o pool de backend. As regras que constam da lista de autorizações não são avaliadas para quaisquer regras personalizadas ou regras geridas.
- Bloco – Bloqueia a transação com base no *SecDefaultAction* (modo de deteção/prevenção). Tal como a ação Allow, uma vez que o pedido é avaliado e adicionado à lista de blocos, a avaliação é interrompida e o pedido é bloqueado. Qualquer pedido depois que satisfaça as mesmas condições não será avaliado e será apenas bloqueado. 
- Log – Permite que a regra escreva para o registo, mas permite que o resto das regras corra para avaliação. As outras regras aduaneiras são avaliadas por ordem prioritária, seguidas das regras geridas.

## <a name="geomatch-custom-rules-preview"></a>Regras personalizadas geomatch (pré-visualização)

As regras personalizadas permitem criar regras personalizadas para atender às necessidades exatas das suas aplicações e políticas de segurança. Pode restringir o acesso às suas aplicações web por país/região. Para obter mais informações, consulte [as regras personalizadas da Geomatch (pré-visualização)](geomatch-custom-rules.md).

## <a name="next-steps"></a>Passos seguintes

Depois de aprender sobre regras personalizadas, [crie as suas próprias regras personalizadas.](create-custom-waf-rules.md)
