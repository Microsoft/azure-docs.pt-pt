---
title: Regras personalizadas de Firewall de aplicações Web (WAF) do Azure
description: Este artigo fornece uma descrição geral da firewall de aplicações web regras personalizadas de (WAF) no Gateway de aplicação do Azure.
services: application-gateway
ms.topic: article
author: vhorne
ms.service: application-gateway
ms.date: 6/5/2019
ms.author: victorh
ms.openlocfilehash: d35e1dc96f65e51ab14c4962d1824334cbdb1616
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/07/2019
ms.locfileid: "66752023"
---
# <a name="custom-rules-for-web-application-firewall"></a>Regras personalizadas para o Firewall de aplicação Web

A firewall de aplicações do Gateway de aplicação Azure web (WAF) é fornecido com um ruleset pré-configuração de plataforma gerido que oferece proteção contra muitos tipos diferentes de ataques. Esses ataques incluem cruzada scripts sites, injeção de SQL e outros. Se for um administrador do WAF, pode querer escrever próprias regras para aumentar a regra de núcleo definidas regras (CRS). As regras podem bloquear ou permitir tráfego pedido com base nos critérios de correspondência.

Regras personalizadas permitem-lhe criar suas próprias regras que são avaliadas para cada pedido que passa o WAF. Estas regras manterá uma prioridade mais alta do que o resto das regras os conjuntos de regras gerido. As regras personalizadas contêm um nome de regra, prioridade da regra e uma matriz de condições de correspondência. Se estas condições são cumpridas, é efetuada uma ação (para permitir ou bloquear).

Por exemplo, pode bloquear todos os pedidos de um endereço IP no 192.168.5.4/24 intervalo. Esta regra, é o operador *IPMatch*, o matchValues é o intervalo de endereços IP (192.168.5.4/24) e a ação é bloquear o tráfego. Também definir o nome e a prioridade da regra.

Regras personalizadas de suporte com a lógica compondo para tornar mais avançadas de regras que abordam que precisa de sua segurança. Por exemplo, (1 de condição **e** condição 2) **ou** condição 3).  Neste exemplo significa que, se a 1 de condição **e** condição 2 são cumpridos, **ou** se 3 de condição for cumprida, o WAF deve levar a ação especificada na regra personalizada.

Condições de correspondência diferentes dentro da mesma regra são sempre compostas usando **e**. Por exemplo, bloquear o tráfego a partir de um endereço IP específico, e apenas se estiver a utilizar um determinado navegador.

Se quiser **ou** duas condições diferentes, as duas condições tem de estar no regras diferentes. Por exemplo, bloquear o tráfego a partir de um tráfego de endereço ou de bloqueios IP específico se estiver a utilizar um determinado navegador.

> [!NOTE]
> O número máximo de regras personalizadas do WAF é 100. Para obter mais informações sobre os limites do Gateway de aplicação, consulte [subscrição do Azure e limites do serviço, quotas e restrições](../azure-subscription-service-limits.md#application-gateway-limits).

Expressões regulares são também suportadas em regras personalizadas, assim como os conjuntos de regras CRS. Para obter exemplos deles, consulte exemplos 3 e 5 na [criar e utilizar regras de firewall de aplicações web personalizado](create-custom-waf-rules.md).

## <a name="allowing-vs-blocking"></a>Permitindo versus a bloquear

Permitir e bloquear o tráfego são simples com regras personalizadas. Por exemplo, pode bloquear todo o tráfego proveniente de um intervalo de endereços IP. Pode fazer outra regra para permitir o tráfego se a solicitação é proveniente de um determinado navegador.

Para permitir que algo, certifique-se de que o `-Action` parâmetro estiver definido como **permitir**. Para bloquear algo, certifique-se de que o `-Action` parâmetro estiver definido como **bloco**.

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

Anterior `$BlockRule` mapeia para a seguinte regra personalizada no Azure Resource Manager:

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

Esta regra personalizada contém um nome, prioridade, uma ação e a matriz de correspondência de condições que devem ser cumpridas para a ação a tomar o lugar. Para obter mais explicações desses campos, consulte as seguintes descrições de campo. Por exemplo regras personalizadas, consulte [criar e utilizar regras de firewall de aplicações web personalizado](create-custom-waf-rules.md).

## <a name="fields-for-custom-rules"></a>Campos de regras personalizadas

### <a name="name-optional"></a>[Opcional] nome de

Este é o nome da regra. Este nome é apresentado nos registos.

### <a name="priority-required"></a>Prioridade [necessária]

- Determina a ordem em que as regras são avaliadas no. Menor o valor, a anterior avaliação da regra.
-Tem de ser exclusivo entre todas as regras personalizadas. Uma regra com prioridade 100 será avaliada antes de uma regra com prioridade de 200.

### <a name="rule-type-required"></a>Tipo de regra [necessário]

Atualmente, tem de ser **MatchRule**.

### <a name="match-variable-required"></a>Variável de correspondência [necessário]

Tem de ser um das variáveis:

- RemoteAddr – IP endereço/nome de anfitrião da ligação do computador remoto
- RequestMethod – método de pedido de HTTP (GET, POST, PUT, DELETE e assim por diante.)
- QueryString – variável no URI de
- PostArgs – argumentos enviados no corpo da mensagem
- RequestUri – o URI do pedido
- RequestHeaders – cabeçalhos do pedido
- RequestBody – o corpo do pedido
- RequestCookies – Cookies do pedido

### <a name="selector-optional"></a>Seletor de [opcional]

Descreve o campo da coleção matchVariable. Por exemplo, se o matchVariable RequestHeaders, o seletor pode ser sobre o *agente do usuário* cabeçalho.

### <a name="operator-required"></a>Operador [necessário]

Tem de ser um dos seguintes operadores:

- IPMatch - usado apenas quando a variável de correspondência é *RemoteAddr*
- É igual a – de entrada é o mesmo que o MatchValue
- Contém
- LessThan
- GreaterThan
- LessThanOrEqual
- GreaterThanOrEqual
- BeginsWith
- EndsWith
- RegEx

### <a name="negate-condition-optional"></a>Negar a condição [opcional]

Elimina a condição atual.

### <a name="transform-optional"></a>Transformação [opcional]

Uma lista de cadeias de caracteres com nomes de transformações antes da correspondência é tentada. Estes podem ser as seguintes transformações:

- Minúsculas
- Cortar
- UrlDecode
- UrlEncode 
- RemoveNulls
- HtmlEntityDecode

### <a name="match-values-required"></a>Correspondem aos valores [necessários]

Lista de valores para correspondência, o que pode ser encarada como sendo *ou*"ed. Por exemplo, pode ser endereços IP ou outras cadeias de caracteres. O formato do valor depende o operador anterior.

### <a name="action-required"></a>Ação [necessária]

- Permitir – autoriza a transação, a ignorar regras de todos os subsequentes. Isso significa que o pedido especificado é adicionado à lista de permissões e depois correspondentes, a avaliação de mais de paragens de pedido e é enviado para o conjunto de back-end. As regras que estejam na lista de permissões não são avaliadas para qualquer ainda mais as regras personalizadas ou regras geridas.
- Bloco – bloqueia a transação com base na *SecDefaultAction* (modo de detecção/prevenção). Tal como a ação permitir, depois do pedido é avaliado e adicionado à lista de bloqueios, avaliação está parada e o pedido está bloqueado. Qualquer pedido depois de que cumpre as condições de mesmo não será avaliado e apenas será bloqueado. 
- Registo – permite que a regra de gravar no log, mas permite que o resto das regras de executar para avaliação. Regras personalizadas subsequentes são avaliadas por ordem de prioridade, seguida as regras geridas.

## <a name="next-steps"></a>Passos Seguintes

Depois de aprender sobre regras personalizadas, [criar suas próprias regras personalizadas](create-custom-waf-rules.md).
