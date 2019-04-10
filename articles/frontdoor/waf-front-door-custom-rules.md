---
title: Regra personalizada do Web application firewall para a porta da frente do Azure
description: Saiba como utilizar o web application firewall (WAF) regras personalizadas Protegendo seus aplicativos web de ataques maliciosos.
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/07/2019
ms.author: kumud;tyao
ms.openlocfilehash: 744c6fb9235c9daa2d5239ef9fd13679db943650
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2019
ms.locfileid: "59363086"
---
#  <a name="custom-rules-for-web-application-firewall-with-azure-front-door"></a>Regras personalizadas para o firewall de aplicação web com porta da frente do Azure
Firewall de aplicações web do Azure (WAF) com o serviço de porta de entrada permite-lhe controlar o acesso às suas aplicações web com base nas condições que definir. Uma regra personalizada de WAF é composta por um número de prioridade, um tipo de regra, condições de correspondência e uma ação. Existem dois tipos de regras personalizadas: corresponderem às regras e regras de limite de velocidade. Uma regra de correspondência controla o acesso com base em condições de correspondência, enquanto uma regra de limite de taxa controla o acesso com base na correspondência de condições e as taxas de pedidos recebidos. Pode desativar uma regra personalizada para impedi-lo de que está a ser avaliada, mas continuar a manter a configuração. Este artigo discute as regras de correspondência que se baseiam em parâmetros de http.

## <a name="priority-match-conditions-and-action-types"></a>Prioridade, condições de correspondência e tipos de ação
Pode controlar o acesso com uma regra personalizada da WAf que define um número de prioridade, um tipo de regra, condições de correspondência e uma ação. 

- **Prioridade:** é um número inteiro exclusivo que descreve a ordem de avaliação de regras WAF. As regras com valores mais baixos são avaliadas antes das regras com valores superiores

- **Ação:** define como encaminhar um pedido se uma regra de WAF é correspondida. Pode escolher uma do abaixo das ações a aplicar quando um pedido de corresponde a uma regra personalizada.

    - *Permitir* -WAF reencaminha a busca para o back-end, registos de uma entrada no registos WAF e saídas.
    - *Bloco* -pedido está bloqueado, WAF envia a resposta ao cliente sem reencaminhar o pedido para o back-end. WAF registra uma entrada nos registos WAF.
    - *Registo* -registos WAF uma entrada no WAF regista e continua a avaliar a regra seguinte.
    - *Redirecionar* -WAF redireciona o pedido para um URI especificado, registos de uma entrada nos registos WAF e é encerrado.

- **Corresponde à condição:** define uma variável de correspondência, um operador e corresponde ao valor. Cada regra pode conter várias condições de correspondência. Pode basear-se numa condição de correspondência do abaixo *corresponder variáveis*:
    - RemoteAddr (IP de cliente)
    - RequestMethod
    - Cadeia de consulta
    - PostArgs
    - RequestUri
    - RequestHeader
    - RequestBody

- **Operador:** lista inclui o seguinte:
    - Qualquer: é frequentemente utilizada para definir a ação predefinida se não há regras são correspondentes. Qualquer é uma correspondência operador all.
    - IPMatch: definir a restrição de IP para a variável de RemoteAddr
    - GeoMatch: Definir geo de filtragem para RemoteAddr variável
    - Equal
    - Contains
    - LessThan: restrição de tamanho
    - GreaterThan: restrição de tamanho
    - LessThanOrEqual: size constraint
    - GreaterThanOrEqual: size constraint
    - BeginsWith
     - EndsWith

Pode definir *negar* condição ser VERDADEIRO se o resultado de uma condição deve ser negado.

*Corresponde ao valor* define a lista de valores de correspondência possível.
Suporta o método de pedido HTTP valores incluem:
- GET
- POST
- PUT
- CABEÇALHO
- DELETE
- BLOQUEIO
- DESBLOQUEAR
- PERFIL
- OPÇÕES
- PROPFIND
- PROPPATCH
- MKCOL
- CÓPIA
- MOVER

## <a name="examples"></a>Exemplos

### <a name="waf-custom-rules-example-based-on-http-parameters"></a>Exemplo de regras personalizadas de WAF com base nos parâmetros de http

Eis um exemplo que mostra a configuração de uma regra personalizada com duas condições de correspondência. Pedidos são de um site especificado, conforme definido pela Referenciador e cadeia de consulta não contém "palavra-passe".

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
Um exemplo de configuração de bloqueio de "Colocar" método é mostrado abaixo:

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

Pode criar uma regra personalizada que especifica a restrição de tamanho na parte de uma solicitação de entrada. Por exemplo, abaixo regra bloqueia um Url que é mais de 100 carateres.

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

## <a name="next-steps"></a>Passos Seguintes
- Saiba mais sobre [firewall de aplicações web](waf-overview.md)
- Saiba como [criar um Front Door](quickstart-create-front-door.md).

