---
title: Regras personalizadas do firewall do aplicativo Web do Azure (WAF) V2
description: Este artigo fornece uma visão geral das regras personalizadas do WAF (firewall do aplicativo Web) V2 no gateway Aplicativo Azure.
services: application-gateway
ms.topic: article
author: vhorne
ms.service: application-gateway
ms.date: 6/18/2019
ms.author: victorh
ms.openlocfilehash: 154317e558c2c9a22f569f569684cced467900d5
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/03/2019
ms.locfileid: "71937471"
---
# <a name="custom-rules-for-web-application-firewall-v2"></a>Regras personalizadas para o Firewall do aplicativo Web v2

O Firewall do aplicativo Web do Aplicativo Azure gateway (WAF) V2 vem com um conjunto de regras pré-configurado gerenciado por plataforma que oferece proteção contra vários tipos diferentes de ataques. Esses ataques incluem scripts entre sites, injeção de SQL e outros. Se você for um administrador de WAF, talvez queira escrever suas próprias regras para aumentar as regras do CRS (conjunto de regras principais). Suas regras podem bloquear ou permitir o tráfego solicitado com base nos critérios de correspondência.

As regras personalizadas permitem que você crie suas próprias regras que são avaliadas para cada solicitação que passa pelo WAF. Essas regras têm uma prioridade mais alta do que o restante das regras nos conjuntos de regras gerenciadas. As regras personalizadas contêm um nome de regra, prioridade de regra e uma matriz de condições de correspondência. Se essas condições forem atendidas, uma ação será tomada (para permitir ou bloquear).

Por exemplo, você pode bloquear todas as solicitações de um endereço IP no intervalo 192.168.5.4/24. Nessa regra, o operador é *IPMatch*, o matchValues é o intervalo de endereços IP (192.168.5.4/24) e a ação é bloquear o tráfego. Você também define o nome e a prioridade da regra.

As regras personalizadas dão suporte ao uso da lógica de composição para fazer regras mais avançadas que atendam às suas necessidades de segurança. Por exemplo, (condição 1 **e** condição 2) **ou** condição 3).  Este exemplo significa que, se a condição 1 **e** a condição 2 forem atendidas **ou** se a condição 3 for atendida, o WAF deverá executar a ação especificada na regra personalizada.

Diferentes condições de correspondência dentro da mesma regra sempre são compostas usando **e**. Por exemplo, bloqueie o tráfego de um endereço IP específico e somente se eles estiverem usando um determinado navegador.

Se você quiser **ou** duas condições diferentes, as duas condições deverão estar em regras diferentes. Por exemplo, bloqueie o tráfego de um endereço IP específico ou bloqueie o tráfego se eles estiverem usando um navegador específico.

> [!NOTE]
> O número máximo de regras personalizadas de WAF é 100. Para obter mais informações sobre os limites do gateway de aplicativo, consulte [assinatura e limites de serviço, cotas e restrições do Azure](../azure-subscription-service-limits.md#application-gateway-limits).

Também há suporte para expressões regulares em regras personalizadas, assim como nos conjuntos de regras do CRS. Para obter exemplos desses, consulte os exemplos 3 e 5 em [criar e usar regras personalizadas de firewall do aplicativo Web](create-custom-waf-rules.md).

## <a name="allowing-vs-blocking"></a>Permitindo vs. bloqueio

Permitir e bloquear o tráfego é simples com regras personalizadas. Por exemplo, você pode bloquear todo o tráfego proveniente de um intervalo de endereços IP. Você pode fazer outra regra para permitir o tráfego se a solicitação vier de um navegador específico.

Para permitir algo, verifique se o parâmetro `-Action` está definido como **permitir**. Para bloquear algo, verifique se o parâmetro `-Action` está definido como **Bloquear**.

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

Os `$BlockRule` anteriores mapeiam para a seguinte regra personalizada no Azure Resource Manager:

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

Essa regra personalizada contém um nome, uma prioridade, uma ação e a matriz de condições de correspondência que devem ser atendidas para que a ação ocorra. Para obter mais explicações sobre esses campos, consulte as descrições de campo a seguir. Por exemplo, regras personalizadas, consulte [criar e usar regras personalizadas de firewall do aplicativo Web](create-custom-waf-rules.md).

## <a name="fields-for-custom-rules"></a>Campos para regras personalizadas

### <a name="name-optional"></a>Nome [opcional]

Este é o nome da regra. Esse nome aparece nos logs.

### <a name="priority-required"></a>Prioridade [obrigatório]

- Determina a ordem em que as regras são avaliadas. Quanto menor o valor, mais cedo a avaliação da regra. O intervalo permitido é de 1-100. 
- Deve ser exclusivo entre todas as regras personalizadas. Uma regra com prioridade 40 será avaliada antes de uma regra com prioridade 80.

### <a name="rule-type-required"></a>Tipo de regra [obrigatório]

No momento, deve ser **MatchRule**.

### <a name="match-variable-required"></a>Variável de correspondência [obrigatório]

Deve ser uma das variáveis:

- RemoteAddr – endereço IP/nome do host da conexão do computador remoto
- RequestMethod – método de solicitação HTTP (GET, POST, PUT, DELETE e assim por diante).
- QueryString – variável no URI
- POST args – argumentos enviados no corpo da postagem. As regras personalizadas que usam essa variável de correspondência serão aplicadas somente se o cabeçalho ' Content-Type ' estiver definido como ' application/x-www-form-urlencoded ' e ' multipart/form-data '.
- RequestUri – URI da solicitação
- RequestHeaders – cabeçalhos da solicitação
- RequestBody – contém todo o corpo da solicitação como um todo. As regras personalizadas que usam essa variável de correspondência serão aplicadas somente se o cabeçalho ' Content-Type ' estiver definido como ' application/x-www-form-urlencoded '. 
- RequestCookies – cookies da solicitação

### <a name="selector-optional"></a>Seletor [opcional]

Descreve o campo da coleção matchVariable. Por exemplo, se matchVariable for RequestHeaders, o seletor poderá estar no cabeçalho *User-Agent* .

### <a name="operator-required"></a>Operador [obrigatório]

Deve ser um dos seguintes operadores:

- IPMatch-usado somente quando a variável de correspondência é *RemoteAddr*
- Equals – a entrada é igual a Matchvalue
- Contém
- LessThan
- GreaterThan
- LessThanOrEqual
- GreaterThanOrEqual
- BeginsWith
- EndsWith
- Regex

### <a name="negate-condition-optional"></a>Negar condição [opcional]

Nega a condição atual.

### <a name="transform-optional"></a>Transformar [opcional]

Uma lista de cadeias de caracteres com nomes de transformações a serem realizadas antes da tentativa de correspondência. Elas podem ser as seguintes transformações:

- Minúsculas
- Cortar
- UrlDecode
- UrlEncode 
- RemoveNulls
- HtmlEntityDecode

### <a name="match-values-required"></a>Valores de correspondência [obrigatório]

Lista de valores a serem correspondidos, que pode ser considerada como sendo *ou*' Ed '. Por exemplo, pode ser endereços IP ou outras cadeias de caracteres. O formato do valor depende do operador anterior.

### <a name="action-required"></a>Ação [obrigatório]

- Permitir – autoriza a transação, ignorando todas as regras subsequentes. Isso significa que a solicitação especificada é adicionada à lista de permissões e, uma vez correspondente, a solicitação para uma avaliação adicional e é enviada para o pool de back-end. As regras que estão na lista de permissões não são avaliadas para outras regras personalizadas ou regras gerenciadas.
- Block – bloqueia a transação com base em *SecDefaultAction* (modo de detecção/prevenção). Assim como a ação permitir, depois que a solicitação é avaliada e adicionada à lista de bloqueios, a avaliação é interrompida e a solicitação é bloqueada. Qualquer solicitação após isso atenderá às mesmas condições não será avaliada e será bloqueada apenas. 
- Log – permite que a regra grave no log, mas permite que o restante das regras seja executado para avaliação. As regras personalizadas subsequentes são avaliadas em ordem de prioridade, seguidas pelas regras gerenciadas.

## <a name="next-steps"></a>Passos seguintes

Depois de aprender sobre regras personalizadas, [crie suas próprias regras personalizadas](create-custom-waf-rules.md).
