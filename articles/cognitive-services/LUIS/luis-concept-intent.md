---
title: Intenções e entidades-LUIS
titleSuffix: Azure Cognitive Services
description: Uma única intenção representa uma tarefa ou ação que o usuário deseja executar. É uma finalidade ou meta expressa no expressão de um usuário. Defina um conjunto de tentativas que corresponde às ações que os usuários desejam executar em seu aplicativo.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: diberry
ms.openlocfilehash: 3d2895fa8d45ad594963d3f26cbe04fd968f5fcc
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73487539"
---
# <a name="intents-in-your-luis-app"></a>Tentativas em seu aplicativo LUIS

Uma intenção representa uma tarefa ou ação que o usuário deseja executar. É uma finalidade ou meta expressa no [expressão](luis-concept-utterance.md)de um usuário.

Defina um conjunto de tentativas que corresponde às ações que os usuários desejam executar em seu aplicativo. Por exemplo, um aplicativo de viagem define várias tentativas:

Intenções de aplicativo de viagem   |   Expressões de exemplo   | 
------|------|
 ReservarBilhete     |   "Reservar um vôo para o rio da próxima semana" <br/> "Ir para Rio no 24" <br/> "Preciso de um bilhete de plano próximo domingo para Rio de Rio"    |
 Saudação     |   Significativas <br/>Oi <br/>"Bom dia"  |
 CheckWeather | "Qual é o clima como em Boston?" <br/> "Mostre-me a previsão deste fim de semana" |
 Nenhum         | "Obter uma receita de cookie"<br>"O Lakers venceu?" |

Todos os aplicativos vêm com a intenção predefinida, "[None](#none-intent)", que é a tentativa de fallback. 

## <a name="prebuilt-domains-provide-intents"></a>Domínios pré-criados fornecem intenções
Além de tentativas que você define, você pode usar tentativas predefinidas de um dos [domínios predefinidos](luis-how-to-use-prebuilt-domains.md). 

## <a name="return-all-intents-scores"></a>Retornar todas as pontuações de intenções
Você atribui um expressão a uma única intenção. Quando LUIS recebe um expressão no ponto de extremidade, por padrão, ele retorna a principal intenção desse expressão. 

Se você quiser as pontuações para todas as intenções para o expressão, poderá fornecer um sinalizador na cadeia de caracteres de consulta da API de previsão. 

|Versão da API de previsão|identificar|
|--|--|
|V2|`verbose=true`|
|V2|`show-all-intents=true`|

## <a name="intent-compared-to-entity"></a>Intenção comparada à entidade
A intenção representa a ação que o bot deve tomar para o usuário e se baseia em todo o expressão. Um expressão pode ter apenas uma primeira tentativa de pontuação, mas pode ter muitas entidades. 

<a name="how-do-intents-relate-to-entities"></a>

Crie uma intenção quando a _intenção_ do usuário dispararia uma ação em seu aplicativo cliente, como uma chamada para a função checkweather (). Em seguida, crie entidades para representar os parâmetros necessários para executar a ação. 

|Intenção   | Entidade | Expressão de exemplo   | 
|------------------|------------------------------|------------------------------|
| CheckWeather | {"tipo": "local", "entidade": "Seattle"}<br>{"tipo": "BUILTIN. datetimeV2. Date", "entidade": "amanhã", "resolução": "2018-05-23"} | Qual é o clima como no `Seattle` `tomorrow`? |
| CheckWeather | {"tipo": "date_range", "entidade": "este fim de semana"} | Mostre-me a previsão para `this weekend` | 
||||

## <a name="prebuilt-domain-intents"></a>Tentativas de domínio predefinidas

[Domínios pré-criados](luis-how-to-use-prebuilt-domains.md) fornecem intenções com declarações. 

## <a name="none-intent"></a>Intenção None (Nenhuma)

A intenção **None** é criada, mas deixada vazia com a finalidade. A tentativa **None** é uma intenção necessária e não pode ser excluída ou renomeada. Preencha-o com declarações que estão fora do seu domínio.

A intenção **None** é a intenção de fallback, importante em todos os aplicativos e deve ter 10% do total de declarações. Ele é usado para ensinar LUIS declarações que não são importantes no domínio do aplicativo (área de assunto). Se você não adicionar nenhum declarações para a intenção **nenhum** , o Luis forçará um expressão que está fora do domínio em um dos propósitos de domínio. Isso distorcerá as pontuações de previsão, ensinando LUIS a intenção errada para o expressão. 

Quando um expressão é previsto como a intenção None, o aplicativo cliente pode fazer mais perguntas ou fornecer um menu para direcionar o usuário a opções válidas. 

## <a name="negative-intentions"></a>Intenções negativas 
Se você quiser determinar intenções negativas e positivas, como "Eu quero um carro" e " **não** **quero um carro** ", você pode criar duas tentativas (um positivo e um negativo) e adicionar os declarações apropriados para cada. Ou você pode criar uma única intenção e marcar os dois termos positivos e negativos diferentes como uma entidade.  

## <a name="intents-and-patterns"></a>Tentativas e padrões

Se você tiver um exemplo de declarações, que pode ser definido em parte ou inteiro como uma expressão regular, considere usar a [entidade de expressão regular](luis-concept-entity-types.md#regular-expression-entity) emparelhada com um [padrão](luis-concept-patterns.md). 

O uso de uma entidade de expressão regular garante a extração de dados para que o padrão seja correspondido. O padrão de correspondência garante que uma intenção exata seja retornada. 

## <a name="intent-balance"></a>Saldo de intenção
As intenções de domínio de aplicativo devem ter um equilíbrio de declarações em cada tentativa. Não há uma intenção com 10 declarações e outra intenção com 500 declarações. Isso não é balanceado. Se você tiver essa situação, examine a intenção com 500 declarações para ver se muitas das intenções podem ser reorganizadas em um [padrão](luis-concept-patterns.md). 

A intenção **None** não está incluída no saldo. Essa intenção deve conter 10% do total de declarações no aplicativo.

## <a name="intent-limits"></a>Limites de intenção
Examine [os limites](luis-boundaries.md#model-boundaries) para entender quantas tentativas você pode adicionar a um modelo. 

### <a name="if-you-need-more-than-the-maximum-number-of-intents"></a>Se você precisar de mais do que o número máximo de tentativas 
Primeiro, considere se o sistema está usando muitas intenções. 

### <a name="can-multiple-intents-be-combined-into-single-intent-with-entities"></a>Várias intenções podem ser combinadas em uma única intenção com entidades 
As intenções que são muito semelhantes podem dificultar a distinção entre LUIS. As intenções devem ser diferentes o suficiente para capturar as principais tarefas que o usuário está solicitando, mas não precisam capturar todos os caminhos que o seu código usa. Por exemplo, BookFlight e FlightCustomerService podem ser intenções separadas em um aplicativo de viagem, mas BookInternationalFlight e BookDomesticFlight são muito semelhantes. Se o seu sistema precisar distingui-los, use entidades ou outras lógicas em vez de intenções. 

### <a name="dispatcher-model"></a>Modelo de Dispatcher
Saiba mais sobre como combinar aplicativos LUIS e QnA Maker com o [modelo de expedição](luis-concept-enterprise.md#when-you-need-to-combine-several-luis-and-qna-maker-apps). 

### <a name="request-help-for-apps-with-significant-number-of-intents"></a>Solicitar ajuda para aplicativos com um número significativo de tentativas
Se a redução do número de tentativas ou a divisão de suas intenções em vários aplicativos não funcionar para você, entre em contato com o suporte. Se sua assinatura do Azure inclui serviços de suporte, entre em contato com o [suporte técnico do Azure](https://azure.microsoft.com/support/options/). 

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [entidades](luis-concept-entity-types.md), que são palavras importantes relevantes para tentativas
* Saiba como [Adicionar e gerenciar tentativas](luis-how-to-add-intents.md) em seu aplicativo Luis.
* [Práticas recomendadas](luis-concept-best-practices.md) de revisão
