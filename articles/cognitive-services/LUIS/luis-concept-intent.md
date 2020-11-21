---
title: Intenções e entidades - LUIS
titleSuffix: Azure Cognitive Services
description: Uma única intenção representa uma tarefa ou ação que o utilizador quer realizar. É a finalidade ou objetivo expresso na intenção do utilizador. Defina um conjunto de intenções que corresponda às ações que os utilizadores querem ter na sua aplicação.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/10/2019
ms.openlocfilehash: bf5ab7361c84fb787366c7c361829e52362fe427
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95025913"
---
# <a name="intents-in-your-luis-app"></a>Intenções na sua app LUIS

Uma intenção representa uma tarefa ou ação que o utilizador quer realizar. É um propósito ou objetivo expresso na [expressão](luis-concept-utterance.md)de um utilizador .

Defina um conjunto de intenções que corresponda às ações que os utilizadores querem ter na sua aplicação. Por exemplo, uma aplicação de viagem define várias intenções:

Intenções de aplicativo de viagem   |   Expressões de exemplo   |
------|------|
 ReservarBilhete     |   "Reserve-me um voo para o Rio na próxima semana" <br/> "Voe-me para o Rio no dia 24" <br/> "Preciso de um bilhete de avião no próximo domingo para o Rio de janeiro"    |
 Saudação     |   "Hi" (Viva) <br/>"Hello" (Olá) <br/>"Good morning" (Bom dia)  |
 CheckWeather | "Como está o tempo em Boston?" <br/> "Mostre-me a previsão para este fim de semana" |
 Nenhum         | "Arranja-me uma receita de biscoitos"<br>"Os Lakers ganharam?" |

Todas as aplicações vêm com a intenção predefinida, "[Nenhuma](#none-intent)", que é a intenção de recuo.

## <a name="prebuilt-domains-provide-intents"></a>Domínios pré-construídos fornecem intenções
Além das intenções que define, pode utilizar intenções pré-construídas a partir de um dos [domínios pré-construídos.](./howto-add-prebuilt-models.md)

## <a name="return-all-intents-scores"></a>Devolva todas as pontuações de intenções
Atribua uma expressão a uma única intenção. Quando LUIS recebe uma expressão no ponto final, por defeito, devolve a intenção superior para essa expressão.

Se quiser as pontuações para todas as intenções para a expressão, pode fornecer uma bandeira na cadeia de consulta da API de previsão.

|Versão API de previsão|Sinalizador|
|--|--|
|V2|`verbose=true`|
|V3|`show-all-intents=true`|

## <a name="intent-compared-to-entity"></a>Intenção comparada com entidade
A intenção representa uma ação que a aplicação deve tomar para o utilizador e baseia-se em toda a expressão. Uma expressão pode ter apenas uma intenção de pontuação superior, mas pode ter muitas entidades.

<a name="how-do-intents-relate-to-entities"></a>

Crie uma intenção quando a _intenção_ do utilizador desencadearia uma ação na aplicação do seu cliente, como uma chamada para a função checkweather(). Em seguida, crie entidades para representar os parâmetros necessários para executar a ação.

|Intenção   | Entidade | Expressão de exemplo   |
|------------------|------------------------------|------------------------------|
| CheckWeather | { "tipo": "localização", "entidade": "seattle" }<br>{ "tipo": "builtin.datetimeV2.date","entidade": "amanhã","resolução":"2018-05-23" } | Como está o `Seattle` `tomorrow` tempo? |
| CheckWeather | { "tipo": "date_range", "entidade": "este fim de semana" } | Mostre-me a previsão para `this weekend` |
||||

## <a name="prebuilt-domain-intents"></a>Intenções de domínio pré-construídas

[Domínios pré-construídos](./howto-add-prebuilt-models.md) fornecem intenções com expressões.

## <a name="none-intent"></a>Intenção None (Nenhuma)

A intenção **de Ninguém** é criada, mas deixada vazia de propósito. A intenção **de Nenhum** é uma intenção necessária e não pode ser apagada ou renomeada. Preencha-a com expressões que estejam fora do seu domínio.

A intenção **de None** é a intenção de recuo, importante em cada app, e deve ter 10% do total de declarações. É usado para ensinar expressões LUIS que não são importantes no domínio da aplicação (área de assunto). Se não adicionar nenhuma expressão para a intenção **de Ninguém,** LUIS força uma expressão que está fora do domínio em uma das intenções de domínio. Isto irá distorcer as notas de previsão ensinando a LUIS a intenção errada para a expressão.

Quando uma expressão é prevista como a intenção de Zero, a aplicação do cliente pode fazer mais perguntas ou fornecer um menu para direcionar o utilizador para escolhas válidas.

## <a name="negative-intentions"></a>Intenções negativas
Se quiser determinar intenções negativas e positivas, como **"Quero** um carro" e **"não** quero um carro", pode criar duas intenções (uma positiva e uma negativa) e acrescentar declarações apropriadas para cada um. Ou pode criar uma única intenção e marcar os dois termos positivos e negativos diferentes como entidade.

## <a name="intents-and-patterns"></a>Intenções e padrões

Se tiver palavras de exemplo, que podem ser definidas em parte ou no seu conjunto como uma expressão regular, considere usar a [entidade de expressão regular](luis-concept-entity-types.md#regular-expression-entity) emparelhada com um [padrão](luis-concept-patterns.md).

A utilização de uma entidade de expressão regular garante a extração de dados de modo a que o padrão seja igualado. A correspondência de padrões garante que uma intenção exata é devolvida.

## <a name="intent-balance"></a>Equilíbrio de intenção
As intenções de domínio da aplicação devem ter um equilíbrio de expressões em todas as intenções. Não tenha uma intenção com 10 proclamações e outra intenção com 500 proclamações. Isto não é equilibrado. Se tiver esta situação, reveja a intenção com 500 expressões para ver se muitas das intenções podem ser reorganizadas num [padrão.](luis-concept-patterns.md)

A intenção **de Nenhum** não está incluída na balança. Essa intenção deve conter 10% do total de declarações na app.

## <a name="intent-limits"></a>Limites de intenção
Reveja [os limites](luis-limits.md#model-boundaries) para entender quantas intenções pode adicionar a um modelo.

### <a name="if-you-need-more-than-the-maximum-number-of-intents"></a>Se precisar de mais do que o número máximo de intenções
Primeiro, considere se o seu sistema está a usar demasiadas intenções.

### <a name="can-multiple-intents-be-combined-into-single-intent-with-entities"></a>Podem várias intenções ser combinadas em uma única intenção com entidades
As intenções que são demasiado semelhantes podem dificultar a distinção entre eles. As intenções devem ser variadas o suficiente para capturar as principais tarefas que o utilizador está a pedir, mas não precisam de capturar todos os caminhos que o seu código toma. Por exemplo, o BookFlight e o FlightCustomerService podem ser intenções separadas numa aplicação de viagem, mas o BookInternationalFlight e o BookDomesticFlight são demasiado semelhantes. Se o seu sistema precisar de os distinguir, utilize entidades ou outra lógica em vez de intenções.

### <a name="dispatcher-model"></a>Modelo de despachante
Saiba mais sobre a combinação de apps de fabricantes LUIS e QnA com o [modelo de despacho.](luis-concept-enterprise.md#when-you-need-to-combine-several-luis-and-qna-maker-apps)

### <a name="request-help-for-apps-with-significant-number-of-intents"></a>Pedir ajuda para apps com número significativo de intenções
Se reduzir o número de intenções ou dividir as suas intenções em várias aplicações não funcionar para si, contacte o suporte. Se a sua subscrição Azure incluir serviços de suporte, contacte [o suporte técnico da Azure.](https://azure.microsoft.com/support/options/)

## <a name="next-steps"></a>Próximos passos

* Saiba mais sobre [as entidades,](luis-concept-entity-types.md)que são palavras importantes relevantes para as intenções
* Saiba como [adicionar e gerir intenções](luis-how-to-add-intents.md) na sua app LUIS.
* Rever as [melhores práticas de intenção](luis-concept-best-practices.md)