---
title: Intenções e entidades - LUIS
titleSuffix: Azure Cognitive Services
description: Uma única intenção representa uma tarefa ou ação que o utilizador quer realizar. É a finalidade ou objetivo expresso na intenção do utilizador. Defina um conjunto de intenções que correspondam às ações que os utilizadores pretendem tomar na sua aplicação.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: diberry
ms.openlocfilehash: f2e4f91dbc03853d6f1a5240f693ea8ff510e8c4
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82101081"
---
# <a name="intents-in-your-luis-app"></a>Intenções na sua app LUIS

Uma intenção representa uma tarefa ou ação que o utilizador quer realizar. É um propósito ou objetivo expresso na [expressão](luis-concept-utterance.md)de um utilizador.

Defina um conjunto de intenções que correspondam às ações que os utilizadores pretendem tomar na sua aplicação. Por exemplo, uma aplicação de viagem define várias intenções:

Intenção sinuosa de aplicativos de viagem   |   Expressões de exemplo   |
------|------|
 ReservarBilhete     |   "Reserve-me um voo para o Rio na próxima semana" <br/> "Voa-me até ao Rio no dia 24" <br/> "Preciso de um bilhete de avião no próximo domingo para o Rio de janeiro"    |
 Saudação     |   "Olá" <br/>"Olá" <br/>"Bom dia"  |
 CheckWeather | "Como está o tempo em Boston?" <br/> "Mostre-me a previsão para este fim de semana" |
 Nenhuma         | "Arranja-me uma receita de biscoitos"<br>"Os Lakers ganharam?" |

Todas as aplicações vêm com a intenção predefinida, "[Nenhuma](#none-intent)", que é a intenção de recuo.

## <a name="prebuilt-domains-provide-intents"></a>Domínios pré-construídos fornecem intenções
Além das intenções que define, pode utilizar intenções pré-construídas a partir de um dos [domínios pré-construídos.](luis-how-to-use-prebuilt-domains.md)

## <a name="return-all-intents-scores"></a>Devolver todas as pontuações de todas as intenções
Atribui uma expressão a uma única intenção. Quando luis recebe uma expressão sobre o ponto final, por padrão, devolve a intenção superior para essa expressão.

Se quiser as pontuações para todas as intenções para a expressão, pode fornecer uma bandeira na cadeia de consulta da Previsão API.

|Versão API de previsão|Sinalizador|
|--|--|
|V2|`verbose=true`|
|V3|`show-all-intents=true`|

## <a name="intent-compared-to-entity"></a>Intenção comparada com entidade
A intenção representa uma ação que a aplicação deve tomar para o utilizador e baseia-se em toda a expressão. Uma expressão pode ter apenas uma intenção de pontuação de topo, mas pode ter muitas entidades.

<a name="how-do-intents-relate-to-entities"></a>

Crie uma intenção quando a _intenção_ do utilizador desencadearia uma ação na sua aplicação de cliente, como uma chamada para a função checkweather() Em seguida, crie entidades para representar parâmetros necessários para executar a ação.

|Intenção   | Entidade | Expressão de exemplo   |
|------------------|------------------------------|------------------------------|
| CheckWeather | { "tipo": "localização", "entidade": "seattle" }<br>{ "type": "builtin.datetimeV2.date","entidade": "amanhã", resolução":"2018-05-23" } | Como está o tempo? `Seattle` `tomorrow` |
| CheckWeather | { "tipo": "date_range", "entidade": "this weekend" } | Mostre-me a previsão para`this weekend` |
||||

## <a name="prebuilt-domain-intents"></a>Intenções de domínio pré-construídos

[Os domínios pré-construídos](luis-how-to-use-prebuilt-domains.md) fornecem intenções com expressões.

## <a name="none-intent"></a>Intenção None (Nenhuma)

A intenção **"Nenhum"** é criada, mas deixada vazia de propósito. A intenção **de "Nenhuma** intenção" é uma intenção necessária e não pode ser apagada ou renomeada. Encha-o com expressões que estão fora do seu domínio.

A intenção **de "None"** é a intenção de recuo, importante em todas as aplicações, e deve ter 10% do total de expressões. É usado para ensinar expressões LUIS que não são importantes no domínio da app (área do assunto). Se não adicionar nenhuma expressão para a intenção **de Ninguém,** LUIS força uma expressão que está fora do domínio em uma das intenções de domínio. Isto irá distorcer as pontuações de previsão ensinando luis a intenção errada para a expressão.

Quando uma expressão é prevista como a intenção None, a aplicação do cliente pode fazer mais perguntas ou fornecer um menu para direcionar o utilizador para escolhas válidas.

## <a name="negative-intentions"></a>Intenções negativas
Se quiser determinar intenções negativas e positivas, como **"quero** um carro" e **"não** quero um carro", pode criar duas intenções (uma positiva e uma negativa) e acrescentar declarações apropriadas para cada um. Ou pode criar uma única intenção e marcar os dois termos positivos e negativos diferentes como entidade.

## <a name="intents-and-patterns"></a>Intençãos e padrões

Se tiver declarações de exemplo, que podem ser definidas em parte ou no todo como uma expressão regular, considere usar a entidade de [expressão regular](luis-concept-entity-types.md#regular-expression-entity) emparelhada com um [padrão](luis-concept-patterns.md).

A utilização de uma entidade de expressão regular garante a extração de dados para que o padrão seja igualado. A correspondência de padrões garante que uma intenção exata é devolvida.

## <a name="intent-balance"></a>Equilíbrio de intenções
As intenções de domínio da aplicação devem ter um equilíbrio de expressões em cada intenção. Não tenha uma intenção com 10 proclamações e outra intenção com 500 expressões. Isto não é equilibrado. Se tiver esta situação, reveja a intenção com 500 expressões para ver se muitas das intenções podem ser reorganizadas num [padrão](luis-concept-patterns.md).

A intenção **de "None"** não está incluída na balança. Essa intenção deve conter 10% do total de declarações na aplicação.

## <a name="intent-limits"></a>Limites de intenção
[Limites](luis-limits.md#model-boundaries) de revisão para entender quantas intenções pode adicionar a um modelo.

### <a name="if-you-need-more-than-the-maximum-number-of-intents"></a>Se precisar de mais do que o número máximo de intenções
Primeiro, considere se o seu sistema está a usar demasiadas intenções.

### <a name="can-multiple-intents-be-combined-into-single-intent-with-entities"></a>As múltiplas intenções podem ser combinadas em intenções únicas com entidades
Intenções demasiado semelhantes podem dificultar a distinção entre luis. As intenções devem ser variadas o suficiente para capturar as principais tarefas que o utilizador está a pedir, mas não precisam de capturar todos os caminhos que o seu código toma. Por exemplo, bookFlight e FlightCustomerService podem ser intenções separadas numa aplicação de viagem, mas bookInternationalFlight e BookDomesticFlight são muito similares. Se o seu sistema precisa distingui-los, use entidades ou outralógica em vez de intenções.

### <a name="dispatcher-model"></a>Modelo de expedidor
Saiba mais sobre a combinação de apps de fabricante luis e QnA com o [modelo de despacho.](luis-concept-enterprise.md#when-you-need-to-combine-several-luis-and-qna-maker-apps)

### <a name="request-help-for-apps-with-significant-number-of-intents"></a>Solicitar ajuda para apps com um número significativo de intenções
Se reduzir o número de intenções ou dividir as suas intenções em várias aplicações não funcionar para si, o suporte de contato. Se a sua subscrição Azure incluir serviços de suporte, contacte o [suporte técnico do Azure.](https://azure.microsoft.com/support/options/)

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [entidades](luis-concept-entity-types.md), que são palavras importantes relevantes para as intenções
* Saiba como [adicionar e gerir as intenções](luis-how-to-add-intents.md) na sua aplicação LUIS.
* Rever [as boas práticas](luis-concept-best-practices.md) de intenção
