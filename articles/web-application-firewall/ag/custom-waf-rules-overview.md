---
title: Firewall de aplicação web Azure (WAF) v2 regras personalizadas no Gateway de Aplicações
description: Este artigo fornece uma visão geral das regras personalizadas do Web Application Firewall (WAF) v2 no Gateway de Aplicações Azure.
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 01/30/2020
ms.author: victorh
ms.openlocfilehash: 072c7bd5b5b292ca4f0e53c59fcb7e9771331a94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77031736"
---
# <a name="custom-rules-for-web-application-firewall-v2-on-azure-application-gateway"></a>Regras personalizadas para Firewall v2 de aplicação web no Gateway de aplicações do Azure

O Firewall web application (WAF) da Aplicação Web da Aplicação Azure Application Gateway (WAF) vem com um conjunto de regras pré-configurado e gerido pela plataforma que oferece proteção contra vários tipos de ataques. Estes ataques incluem roteiros de cross site, injeção SQL, e outros. Se você é um administrador waf, você pode querer escrever suas próprias regras para aumentar as regras de regra básica (CRS). As suas regras podem bloquear ou permitir o tráfego solicitado com base em critérios correspondentes.

As regras personalizadas permitem-lhe criar as suas próprias regras que são avaliadas para cada pedido que passa pelo WAF. Estas regras têm uma prioridade maior do que as restantes regras dos conjuntos de regras geridos. As regras personalizadas contêm um nome de regra, prioridade de regra, e uma série de condições correspondentes. Se estas condições forem satisfeitas, é tomada uma ação (para permitir ou bloquear).

Por exemplo, pode bloquear todos os pedidos de um endereço IP no intervalo 192.168.5.4/24. Nesta regra, o operador é *IPMatch*, o matchValues é o intervalo de endereçoIP (192.168.5.4/24), e a ação é bloquear o tráfego. Também definiu o nome e a prioridade da regra.

As regras personalizadas suportam o uso da lógica de compostagem para fazer regras mais avançadas que respondam às suas necessidades de segurança. Por exemplo, (Condição 1 **e** Condição 2) **ou** Condição 3). Isto significa que se a Condição 1 **e** a Condição 2 forem cumpridas, **ou** se a Condição 3 for satisfeita, a WAF deve tomar as medidas especificadas na regra personalizada.

Diferentes condições de correspondência dentro da mesma regra são sempre agravadas utilizando **e**. Por exemplo, bloqueie o tráfego a partir de um endereço IP específico, e apenas se estiver usando um determinado navegador.

Se quiser **ou** duas condições diferentes, as duas condições devem estar em regras diferentes. Por exemplo, bloqueie o tráfego a partir de um endereço IP específico ou bloqueie o tráfego se estiver usando um navegador específico.

> [!NOTE]
> O número máximo de regras personalizadas da WAF é de 100. Para obter mais informações sobre os limites de Gateway de aplicação, consulte [os limites de subscrição e serviço do Azure, quotas e constrangimentos.](../../azure-resource-manager/management/azure-subscription-service-limits.md#application-gateway-limits)

Expressões regulares também são suportadas em regras personalizadas, assim como nas regras do CRS. Por exemplo, consulte os exemplos 3 e 5 em [Create e use regras personalizadas](create-custom-waf-rules.md)de firewall de aplicação web .

## <a name="allowing-vs-blocking"></a>Permitir vs. bloqueio

Permitir e bloquear o tráfego é simples com regras personalizadas. Por exemplo, pode bloquear todo o tráfego proveniente de uma série de endereços IP. Pode fazer outra regra para permitir o tráfego se o pedido vier de um navegador específico.

Para permitir algo, `-Action` certifique-se de que o parâmetro está definido para **permitir**. Para bloquear algo, `-Action` certifique-se de que o parâmetro está definido para **bloquear**.

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

Os `$BlockRule` mapas anteriores para a seguinte regra personalizada no Gestor de Recursos Azure:

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

Esta regra personalizada contém um nome, prioridade, uma ação e o conjunto de condições correspondentes que devem ser cumpridas para que a ação ocorra. Para obter mais explicações sobre estes campos, consulte as seguintes descrições de campo. Por exemplo, regras personalizadas, consulte [Criar e utilizar regras personalizadas](create-custom-waf-rules.md)de firewall de aplicações web .

## <a name="fields-for-custom-rules"></a>Campos para regras personalizadas

### <a name="name-optional"></a>Nome [opcional]

O nome da regra.  Aparece nos registos.

### <a name="priority-required"></a>Prioridade [necessária]

- Determina a ordem de avaliação da regra. Quanto menor o valor, mais cedo a avaliação da regra. O intervalo permitido é de 1-100. 
- Deve ser único em todas as regras personalizadas. Uma regra com prioridade 40 é avaliada antes de uma regra com prioridade 80.

### <a name="rule-type-required"></a>Tipo de regra [obrigatório]

Atualmente, deve ser **MatchRule**.

### <a name="match-variable-required"></a>Variável de correspondência [necessária]

Deve ser uma das variáveis:

- RemoteAddr – Endereço IP/nome de anfitrião da ligação remota do computador
- Método de Pedido – MÉTODO DE Pedido HTTP (GET, POST, PUT, DELETE, e assim por diante.)
- ConsultaString – Variável no URI
- PostArgs – Argumentos enviados no corpo do POST. As regras personalizadas que utilizam esta variável de correspondência só são aplicadas se o cabeçalho 'Content-Type' for definido como "aplicação/x-www-for-urlencoded" e "dados multipart/formulário".
- RequestUri – URI do pedido
- RequestHeaders – Cabeçalhos do pedido
- RequestBody – Isto contém todo o corpo de pedidos como um todo. As regras personalizadas que utilizam esta variável de correspondência só são aplicadas se o cabeçalho 'Content-Type' for definido para 'application/x-www-form-urlencoded'. 
- RequestCookies – Cookies do pedido

### <a name="selector-optional"></a>Seletor [opcional]

Descreve o campo da coleção matchVariable. Por exemplo, se a correspondência Variável for RequestHeaders, o seletor pode estar no cabeçalho *do Utilizador-Agente.*

### <a name="operator-required"></a>Operador [obrigatório]

Deve ser um dos seguintes operadores:

- IPMatch - usado apenas quando a Variável de Correspondência é *RemoteAddr*
- Igual - entrada é a mesma que o MatchValue
- Contains
- LessThan
- GreaterThan
- LessThanOrEqual
- GreaterThanOrEqual
- BeginsWith
- EndsWith
- Rio Regex
- Geomatch (pré-visualização)

### <a name="negate-condition-optional"></a>Condição negação [opcional]

Nega a condição atual.

### <a name="transform-optional"></a>Transformar [opcional]

Uma lista de cordas com nomes de transformações a fazer antes da partida ser tentada. Estas podem ser as seguintes transformações:

- Minúsculas
- Trim
- UrlDecode
- UrlEncode 
- Remover Nulls
- HtmlEntityDecode

### <a name="match-values-required"></a>Valores de correspondência [necessários]

Lista de valores a igualar, que pode ser considerado como *sendo ou*'ed' Por exemplo, podem ser endereços IP ou outras cordas. O formato de valor depende do operador anterior.

### <a name="action-required"></a>Ação [necessária]

- Permitir – Autoriza a transação, ignorando todas as outras regras. O pedido especificado é adicionado à lista de espera e uma vez combinado, o pedido para de ser avaliado e é enviado para o pool backend. As regras que estão na lista de admissões não são avaliadas para outras regras personalizadas ou regras geridas.
- Bloco – Bloqueia a transação com base no *SecDefaultAction* (modo de deteção/prevenção). Tal como a ação Permitir, uma vez que o pedido é avaliado e adicionado à lista de blocos, a avaliação é interrompida e o pedido é bloqueado. Qualquer pedido depois disso satisfaz as mesmas condições não será avaliado e será apenas bloqueado. 
- Log – Permite que a regra escreva para o registo, mas permite que o resto das regras seja executada para avaliação. As outras regras personalizadas são avaliadas por ordem de prioridade, seguidas pelas regras geridas.

## <a name="geomatch-custom-rules-preview"></a>Regras personalizadas de Geomatch (pré-visualização)

As regras personalizadas permitem-lhe criar regras personalizadas para atender às necessidades exatas das suas aplicações e políticas de segurança. Pode restringir o acesso às suas aplicações web por país/região. Para mais informações, consulte [as regras personalizadas da Geomatch (pré-visualização)](geomatch-custom-rules.md).

## <a name="next-steps"></a>Passos seguintes

Depois de aprender sobre regras personalizadas, [crie as suas próprias regras personalizadas.](create-custom-waf-rules.md)
