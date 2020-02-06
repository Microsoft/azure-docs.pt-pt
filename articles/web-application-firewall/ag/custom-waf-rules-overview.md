---
title: Regras personalizadas do WAF (firewall do aplicativo Web) V2 no gateway de aplicativo
description: Este artigo fornece uma visão geral das regras personalizadas do WAF (firewall do aplicativo Web) V2 no gateway Aplicativo Azure.
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 01/30/2020
ms.author: victorh
ms.openlocfilehash: 072c7bd5b5b292ca4f0e53c59fcb7e9771331a94
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77031736"
---
# <a name="custom-rules-for-web-application-firewall-v2-on-azure-application-gateway"></a>Regras personalizadas para o Firewall do aplicativo Web V2 no Aplicativo Azure gateway

O Firewall do aplicativo Web do Aplicativo Azure gateway (WAF) V2 vem com um conjunto de regras pré-configurado gerenciado por plataforma que oferece proteção contra vários tipos diferentes de ataques. Esses ataques incluem scripts entre sites, injeção de SQL e outros. Se você é um administrador waf, você pode querer escrever suas próprias regras para aumentar as regras de regra básica (CRS). Suas regras podem bloquear ou permitir o tráfego solicitado com base nos critérios de correspondência.

As regras personalizadas permitem que você crie suas próprias regras que são avaliadas para cada solicitação que passa pelo WAF. Essas regras têm uma prioridade mais alta do que o restante das regras nos conjuntos de regras gerenciadas. As regras personalizadas contêm um nome de regra, prioridade de regra e uma matriz de condições de correspondência. Se essas condições forem atendidas, uma ação será tomada (para permitir ou bloquear).

Por exemplo, você pode bloquear todas as solicitações de um endereço IP no intervalo 192.168.5.4/24. Nesta regra, o operador é *IPMatch*, o matchValues é o intervalo de endereçoIP (192.168.5.4/24), e a ação é bloquear o tráfego. Você também define o nome e a prioridade da regra.

As regras personalizadas dão suporte ao uso da lógica de composição para fazer regras mais avançadas que atendam às suas necessidades de segurança. Por exemplo, (Condição 1 **e** Condição 2) **ou** Condição 3). Isto significa que se a Condição 1 **e** a Condição 2 forem cumpridas, **ou** se a Condição 3 for satisfeita, a WAF deve tomar as medidas especificadas na regra personalizada.

Diferentes condições de correspondência dentro da mesma regra são sempre agravadas utilizando **e**. Por exemplo, bloqueie o tráfego de um endereço IP específico e somente se eles estiverem usando um determinado navegador.

Se quiser **ou** duas condições diferentes, as duas condições devem estar em regras diferentes. Por exemplo, bloqueie o tráfego de um endereço IP específico ou bloqueie o tráfego se eles estiverem usando um navegador específico.

> [!NOTE]
> O número máximo de regras personalizadas de WAF é 100. Para obter mais informações sobre os limites de Gateway de aplicação, consulte [os limites de subscrição e serviço do Azure, quotas e constrangimentos.](../../azure-resource-manager/management/azure-subscription-service-limits.md#application-gateway-limits)

Também há suporte para expressões regulares em regras personalizadas, assim como nos conjuntos de regras do CRS. Por exemplo, consulte os exemplos 3 e 5 em [Create e use regras personalizadas](create-custom-waf-rules.md)de firewall de aplicação web .

## <a name="allowing-vs-blocking"></a>Permitindo vs. bloqueio

Permitir e bloquear o tráfego é simples com regras personalizadas. Por exemplo, você pode bloquear todo o tráfego proveniente de um intervalo de endereços IP. Você pode fazer outra regra para permitir o tráfego se a solicitação vier de um navegador específico.

Para permitir algo, certifique-se de que o parâmetro `-Action` está definido para **permitir**. Para bloquear algo, certifique-se de que o parâmetro `-Action` está definido para **bloquear**.

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

Os mapas `$BlockRule` anteriores para a seguinte regra personalizada no Gestor de Recursos Azure:

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

Essa regra personalizada contém um nome, uma prioridade, uma ação e a matriz de condições de correspondência que devem ser atendidas para que a ação ocorra. Para obter mais explicações sobre esses campos, consulte as descrições de campo a seguir. Por exemplo, regras personalizadas, consulte [Criar e utilizar regras personalizadas](create-custom-waf-rules.md)de firewall de aplicações web .

## <a name="fields-for-custom-rules"></a>Campos para regras personalizadas

### <a name="name-optional"></a>Nome [opcional]

O nome da regra.  Aparece nos registos.

### <a name="priority-required"></a>Prioridade [necessária]

- Determina a ordem de avaliação da regra. Quanto menor o valor, mais cedo a avaliação da regra. O intervalo permitido é de 1-100. 
- Deve ser exclusivo em todas as regras personalizadas. Uma regra com prioridade 40 é avaliada antes de uma regra com prioridade 80.

### <a name="rule-type-required"></a>Tipo de regra [obrigatório]

Atualmente, deve ser **MatchRule**.

### <a name="match-variable-required"></a>Variável de correspondência [necessária]

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

Descreve o campo da coleção matchVariable. Por exemplo, se a correspondência Variável for RequestHeaders, o seletor pode estar no cabeçalho *do Utilizador-Agente.*

### <a name="operator-required"></a>Operador [obrigatório]

Deve ser um dos seguintes operadores:

- IPMatch - usado apenas quando a Variável de Correspondência é *RemoteAddr*
- Equals – a entrada é igual a Matchvalue
- Contains
- LessThan
- GreaterThan
- LessThanOrEqual
- GreaterThanOrEqual
- BeginsWith
- EndsWith
- Regex
- Correspondência geocombinada (versão prévia)

### <a name="negate-condition-optional"></a>Condição negação [opcional]

Nega a condição atual.

### <a name="transform-optional"></a>Transformar [opcional]

Uma lista de cadeias de caracteres com nomes de transformações a serem realizadas antes da tentativa de correspondência. Elas podem ser as seguintes transformações:

- Minúsculas
- Trim
- UrlDecode
- UrlEncode 
- RemoveNulls
- HtmlEntityDecode

### <a name="match-values-required"></a>Valores de correspondência [necessários]

Lista de valores a igualar, que pode ser considerado como *sendo ou*'ed' Por exemplo, pode ser endereços IP ou outras cadeias de caracteres. O formato do valor depende do operador anterior.

### <a name="action-required"></a>Ação [necessária]

- Permitir – Autoriza a transação, ignorando todas as outras regras. O pedido especificado é adicionado à lista de espera e uma vez combinado, o pedido para de ser avaliado e é enviado para o pool backend. As regras que estão na lista de permissões não são avaliadas para outras regras personalizadas ou regras gerenciadas.
- Bloco – Bloqueia a transação com base no *SecDefaultAction* (modo de deteção/prevenção). Assim como a ação permitir, depois que a solicitação é avaliada e adicionada à lista de bloqueios, a avaliação é interrompida e a solicitação é bloqueada. Qualquer pedido depois disso satisfaz as mesmas condições não será avaliado e será apenas bloqueado. 
- Log – permite que a regra grave no log, mas permite que o restante das regras seja executado para avaliação. As outras regras personalizadas são avaliadas por ordem de prioridade, seguidas pelas regras geridas.

## <a name="geomatch-custom-rules-preview"></a>Regras personalizadas de correspondência geocombinadas (versão prévia)

As regras personalizadas permitem-lhe criar regras personalizadas para atender às necessidades exatas das suas aplicações e políticas de segurança. Pode restringir o acesso às suas aplicações web por país/região. Para mais informações, consulte [as regras personalizadas da Geomatch (pré-visualização)](geomatch-custom-rules.md).

## <a name="next-steps"></a>Passos seguintes

Depois de aprender sobre regras personalizadas, [crie as suas próprias regras personalizadas.](create-custom-waf-rules.md)
