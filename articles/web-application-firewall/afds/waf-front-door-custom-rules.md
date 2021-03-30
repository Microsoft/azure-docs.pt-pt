---
title: Regra personalizada de firewall de aplicação web para Azure Front Door
description: Saiba como utilizar regras personalizadas do Web Application Firewall (WAF) que protegem as suas aplicações web de ataques maliciosos.
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 09/05/2019
ms.author: victorh
ms.openlocfilehash: 158bfe30bf48ee420be8efb9ff32fff0e555d9e7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "79475829"
---
#  <a name="custom-rules-for-web-application-firewall-with-azure-front-door"></a>Regras personalizadas para firewall de aplicação web com porta frontal Azure

O Azure Web Application Firewall (WAF) com porta frontal permite-lhe controlar o acesso às suas aplicações web com base nas condições que define. Uma regra WAF personalizada consiste num número prioritário, tipo de regra, condições de correspondência e uma ação. Existem dois tipos de regras personalizadas: regras de correspondência e regras limite de taxa. Uma regra de correspondência controla o acesso com base num conjunto de condições de correspondência, enquanto uma regra limite de taxa controla o acesso com base nas condições de correspondência e nas taxas de pedidos de entrada. Pode desativar uma regra personalizada para evitar que seja avaliada, mas ainda assim manter a configuração. 

## <a name="priority-match-conditions-and-action-types"></a>Prioridade, condições de correspondência e tipos de ação

Você pode controlar o acesso com uma regra WAf personalizada que define um número prioritário, um tipo de regra, uma variedade de condições de jogo, e uma ação. 

- **Prioridade:** é um número inteiro único que descreve a ordem de avaliação das regras da WAF. As regras com valores de prioridade mais baixos são avaliadas antes de regras com valores mais elevados. Os números prioritários devem ser únicos entre todas as regras personalizadas.

- **Ação:** define como encaminhar um pedido se uma regra da WAF for correspondida. Pode escolher uma das ações abaixo a aplicar quando um pedido corresponde a uma regra personalizada.

    - *Permitir* - WAF encaminha a missão para o back-end, regista uma entrada em registos e saídas WAF.
    - *Bloco* - O pedido está bloqueado, a WAF envia resposta ao cliente sem encaminhar o pedido para o back-end. WAF regista uma entrada em registos WAF.
    - *Log* - WAF regista uma entrada em registos WAF e continua a avaliar a regra seguinte.
    - *Redirecionamento* - WAF redireciona o pedido para um URI especificado, regista uma entrada em registos WAF e saídas.

- **Condição de jogo:** define uma variável de correspondência, um operador e valor de correspondência. Cada regra pode conter várias condições de correspondência. Uma condição de correspondência pode basear-se na localização geoparal, endereços IP do cliente (CIDR), tamanho ou combinação de cordas. A correspondência de cordas pode ser contra uma lista de variáveis de correspondência.
  - **Variável de correspondência:**
    - RequestMethod
    - QueryString
    - PostArgs
    - RequestUri
    - RequestHeader
    - PedidoCorpo
    - Cookies
  - **Operador:**
    - Any: é frequentemente usado para definir a ação padrão se nenhuma regra for correspondida. Qualquer um é compatível com todos os operadores.
    - Igual
    - Contains
    - LessThan: restrição de tamanho
    - GreaterTh: restrição de tamanho
    - LessThanOrEqual: restrição de tamanho
    - GreaterThanOrEqual: restrição de tamanho
    - BeginsWith
    - EndsWith
    - Regex
  
  - **A Regex** não suporta as seguintes operações: 
    - Retroreferências e captura de subexpressões
    - Afirmações arbitrárias de zero largura
    - Referências subroutinas e padrões recursivos
    - Padrões condicionais
    - Verbos de controlo de retrocesso
    - A diretiva \C single byte
    - A diretiva \R newline match
    - O início da diretiva de reset de correspondência \K
    - Callouts e código incorporado
    - Agrupamentos atómicos e quantificadores possessivos

  - **Negação [opcional]:** Pode definir a condição *de negação* para ser verdadeira se o resultado de uma condição for anulado.
      
  - **Transformar [opcional]:** Uma lista de cordas com nomes de transformações a fazer antes da partida ser tentada. Estas podem ser as seguintes transformações:
     - Maiúscula 
     - Minúsculas
     - Trim
     - RemoveNulls
     - UrlDecode
     - UrlEncode
     
   - **Valor do jogo:** Os valores do método do pedido HTTP suportado incluem:
     - GET
     - POST
     - PUT
     - HEAD
     - DELETE
     - FECHADURA
     - DESBLOQUEAR
     - PERFIL
     - Opções
     - PROPFIND
     - PROPPATCH
     - MKCOL
     - CÓPIA
     - MOVER-SE

## <a name="examples"></a>Exemplos

### <a name="waf-custom-rules-example-based-on-http-parameters"></a>Exemplo de regras personalizadas waf com base em parâmetros http

Aqui está um exemplo que mostra a configuração de uma regra personalizada com duas condições de jogo. Os pedidos são de um site especificado, conforme definido pelo remetente, e o string de consulta não contém "palavra-passe".

```
# http rules example
{
  "name": "AllowFromTrustedSites",
  "priority": 1,
  "ruleType": "MatchRule",
  "matchConditions": [
    {
      "matchVariable": "RequestHeader",
      "selector": "Referer",
      "operator": "Equal",
      "negateCondition": false,
      "matchValue": [
        "www.mytrustedsites.com/referpage.html"
      ]
    },
    {
      "matchVariable": "QueryString",
      "operator": "Contains",
      "matchValue": [
        "password"
      ],
      "negateCondition": true
    }
  ],
  "action": "Allow",
  "transforms": []
}

```
Uma configuração de exemplo para bloquear o método "PUT" é mostrada como abaixo:

``` 
# http Request Method custom rules
{
  "name": "BlockPUT",
  "priority": 2,
  "ruleType": "MatchRule",
  "matchConditions": [
    {
      "matchVariable": "RequestMethod",
      "selector": null,
      "operator": "Equal",
      "negateCondition": false,
      "matchValue": [
        "PUT"
      ]
    }
  ],
  "action": "Block",
  "transforms": []
}
```

### <a name="size-constraint"></a>Restrição de tamanho

Pode construir uma regra personalizada que especifica restrições de tamanho em parte de um pedido de entrada. Por exemplo, a regra abaixo bloqueia um Url com mais de 100 caracteres.

```
# http parameters size constraint
{
  "name": "URLOver100",
  "priority": 5,
  "ruleType": "MatchRule",
  "matchConditions": [
    {
      "matchVariable": "RequestUri",
      "selector": null,
      "operator": "GreaterThanOrEqual",
      "negateCondition": false,
      "matchValue": [
        "100"
      ]
    }
  ],
  "action": "Block",
  "transforms": []
}
```

## <a name="next-steps"></a>Passos seguintes
- [Configure uma política de Firewall de aplicação web usando a Azure PowerShell](waf-front-door-custom-rules-powershell.md) 
- Saiba mais sobre [firewall de aplicação web com porta frontal](afds-overview.md)
- Saiba como [criar um Front Door](../../frontdoor/quickstart-create-front-door.md).

