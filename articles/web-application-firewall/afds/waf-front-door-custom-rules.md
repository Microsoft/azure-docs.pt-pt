---
title: Regra personalizada de firewall de aplicação web para porta frontal Azure
description: Saiba como utilizar regras personalizadas de Firewall de Aplicação Web (WAF) que protegem as suas aplicações web de ataques maliciosos.
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 09/05/2019
ms.author: victorh
ms.openlocfilehash: 158bfe30bf48ee420be8efb9ff32fff0e555d9e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79475829"
---
#  <a name="custom-rules-for-web-application-firewall-with-azure-front-door"></a>Regras personalizadas para firewall de aplicação web com porta frontal Azure

O Firewall de aplicação web Azure (WAF) com porta frontal permite controlar o acesso às suas aplicações web com base nas condições que define. Uma regra waf personalizada consiste num número prioritário, tipo de regra, condições de jogo e uma ação. Existem dois tipos de regras personalizadas: regras de correspondência e regras de limite de tarifas. Uma regra de correspondência controla o acesso com base num conjunto de condições correspondentes, enquanto uma regra de limite de taxa controla o acesso com base nas condições correspondentes e nas taxas dos pedidos de entrada. Pode desativar uma regra personalizada para evitar que seja avaliada, mas ainda assim manter a configuração. 

## <a name="priority-match-conditions-and-action-types"></a>Prioridade, condições de jogo e tipos de ação

Você pode controlar o acesso com uma regra wAf personalizada que define um número prioritário, um tipo de regra, um conjunto de condições de jogo, e uma ação. 

- **Prioridade:** é um inteiro único que descreve a ordem de avaliação das regras da WAF. As regras com valores prioritários mais baixos são avaliadas antes de regras com valores mais elevados. Os números prioritários devem ser únicos entre todas as regras personalizadas.

- **Ação:** define como encaminhar um pedido se uma regra WAF for correspondida. Pode escolher uma das ações abaixo a aplicar quando um pedido corresponde a uma regra personalizada.

    - *Permitir* - WAF encaminha a busca para a parte de trás, regista uma entrada em registos e saídas WAF.
    - *Bloco* - O pedido está bloqueado, a WAF envia resposta ao cliente sem reencaminhar o pedido para o back-end. WAF regista uma entrada nos registos WAF.
    - *Log* - WAF regista uma entrada nos registos waf e continua a avaliar a regra seguinte.
    - *Redirecionamento* - WAF redireciona pedido para um URI especificado, regista uma entrada em registos WAF e saídas.

- **Condição de jogo:** define uma variável de correspondência, um operador e o valor da correspondência. Cada regra pode conter múltiplas condições de correspondência. Uma condição de correspondência pode basear-se na localização geo, endereços IP do cliente (CIDR), tamanho ou correspondência de cordas. A correspondência de cordas pode ser contra uma lista de variáveis de correspondência.
  - **Variável de correspondência:**
    - Método de Pedido
    - QueryString
    - Pós-Args
    - Requesturi
    - Cabeçalho de Pedido
    - RequestBody
    - Cookies
  - **Operador:**
    - Qualquer: é frequentemente usado para definir a ação padrão se nenhuma regra for correspondida. Qualquer um é compatível com todos os operadores.
    - Igual
    - Contains
    - LessThan: restrição de tamanho
    - GreaterThan: restrição de tamanho
    - LessThanOrEqual: restrição de tamanho
    - GreaterThanOrEqual: restrição de tamanho
    - BeginsWith
    - EndsWith
    - Rio Regex
  
  - A **Regex** não suporta as seguintes operações: 
    - Referências e captação de subexpressões
    - Afirmações arbitrárias de largura zero
    - Referências de subrotina e padrões recursivos
    - Padrões condicionais
    - Verbos de controlo de rastreio de retrocesso
    - A diretiva \C single-byte
    - A diretiva \R newline match
    - A diretiva \K início da reset da partida
    - Chamadas e código incorporado
    - Agrupamentos atómicos e quantificantes possessivos

  - **Negação [opcional]:** Pode estabelecer a condição *de negação* verdadeiramente se o resultado de uma condição for negado.
      
  - **Transformar [opcional]:** Uma lista de cordas com nomes de transformações a fazer antes da partida ser tentada. Estas podem ser as seguintes transformações:
     - Maiúscula 
     - Minúsculas
     - Trim
     - Remover Nulls
     - UrlDecode
     - UrlEncode
     
   - **Valor da correspondência:** Os valores do método de pedido de pedido http suportado incluem:
     - GET
     - POST
     - PUT
     - HEAD
     - DELETE
     - BLOQUEIO
     - DESBLOQUEAR
     - PERFIL
     - Opções
     - PROPFIND
     - PROPPATCH
     - MKCOL
     - CÓPIA
     - MOVIMENTO

## <a name="examples"></a>Exemplos

### <a name="waf-custom-rules-example-based-on-http-parameters"></a>WaF regras personalizadas exemplo baseado em parâmetros http

Aqui está um exemplo que mostra a configuração de uma regra personalizada com duas condições de jogo. Os pedidos são de um site especificado, tal como definido pelo remetente, e a cadeia de consulta não contém "password".

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

Pode construir uma regra personalizada que especifica a restrição de tamanho em parte de um pedido de entrada. Por exemplo, abaixo da regra bloqueia um Url que é superior a 100 caracteres.

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
- [Configure uma política de firewall de aplicação web usando o Azure PowerShell](waf-front-door-custom-rules-powershell.md) 
- Saiba mais sobre firewall de [aplicação web com porta da frente](afds-overview.md)
- Saiba como [criar um Front Door](../../frontdoor/quickstart-create-front-door.md).

