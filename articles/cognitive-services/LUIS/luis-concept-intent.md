---
title: Intenções e entidades - LUIS
titleSuffix: Azure Cognitive Services
description: Uma única intenção representa uma tarefa ou ação que o utilizador quer realizar. É um objetivo ou objetivo expressado na expressão de um utilizador. Defina um conjunto de objetivos que corresponde a ações que os usuários querem tirar em seu aplicativo.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: diberry
ms.openlocfilehash: 309a2592dbac2918aeb532fbe91e33d296f4e5a5
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79220976"
---
# <a name="intents-in-your-luis-app"></a>Intenções na sua app LUIS

Um objetivo representa uma tarefa ou ação o usuário quer executar. É um propósito ou objetivo expresso na [expressão](luis-concept-utterance.md)de um utilizador.

Defina um conjunto de objetivos que corresponde a ações que os usuários querem tirar em seu aplicativo. Por exemplo, uma aplicação de viagens define vários objetivos:

Objetivos de aplicações de deslocação   |   Expressões de exemplo   | 
------|------|
 ReservarBilhete     |   "Programar-me um vôo para Rio próxima semana" <br/> "Voar-me para Rio no 24th" <br/> "Preciso de um pedido de suporte do plano seguinte Domingo a Rio de Janeiro"    |
 Saudação     |   "Olá" <br/>"Hello" <br/>"Bom dia"  |
 CheckWeather | "O que é o clima, como em Boston?" <br/> "Mostre-me a previsão para este fim de semana" |
 Nenhum         | "Get-me uma receita de cookie"<br>"Os Lakers ganhar?" |

Todas as aplicações vêm com a intenção predefinida, "[Nenhuma](#none-intent)", que é a intenção de recuo. 

## <a name="prebuilt-domains-provide-intents"></a>Domínios pré-concebidos fornecem objetivos
Além das intenções que define, pode utilizar intenções pré-construídas a partir de um dos [domínios pré-construídos.](luis-how-to-use-prebuilt-domains.md) 

## <a name="return-all-intents-scores"></a>Devolver as pontuações de todas as intenções
Atribuição de uma expressão a um objetivo único. Quando luis recebe uma expressão sobre o ponto final, por padrão, devolve a intenção superior para essa expressão. 

Se quiser as pontuações para todas as intenções para a expressão, pode fornecer uma bandeira na cadeia de consulta da Previsão API. 

|Versão API de previsão|Sinalizador|
|--|--|
|V2|`verbose=true`|
|V3|`show-all-intents=true`|

## <a name="intent-compared-to-entity"></a>Em comparação comparada a entidade de intenção
A intenção representa uma ação que a aplicação deve tomar para o utilizador e baseia-se em toda a expressão. Uma expressão pode ter apenas uma parte superior de intenção de classificação, mas ele pode ter muitas entidades. 

<a name="how-do-intents-relate-to-entities"></a>

Crie uma intenção quando a _intenção_ do utilizador desencadearia uma ação na sua aplicação de cliente, como uma chamada para a função checkweather() Em seguida, crie entidades para representar parâmetros necessários para executar a ação. 

|Intenção   | Entidade | Expressão de exemplo   | 
|------------------|------------------------------|------------------------------|
| CheckWeather | {"type": "localização", "entity": "porto"}<br>{"type": "builtin.datetimeV2.date","entity": "" amanhã, penso: "Resolução": "2018-05-23"} | Como está o tempo em `Seattle` `tomorrow`? |
| CheckWeather | {"type": "date_range", "entity": "este fim de semana"} | Mostre-me a previsão para `this weekend` | 
||||

## <a name="prebuilt-domain-intents"></a>Objetivos de domínio pré-criado

[Os domínios pré-construídos](luis-how-to-use-prebuilt-domains.md) fornecem intenções com expressões. 

## <a name="none-intent"></a>Intenção None (Nenhuma)

A intenção **"Nenhum"** é criada, mas deixada vazia de propósito. A intenção **de "Nenhuma** intenção" é uma intenção necessária e não pode ser apagada ou renomeada. Preencha-o com expressões que estão fora do seu domínio.

A intenção **de "None"** é a intenção de recuo, importante em todas as aplicações, e deve ter 10% do total de expressões. Ele é usado para ensinar LUIS expressões que não são importantes no domínio de aplicativo (área de assunto). Se não adicionar nenhuma expressão para a intenção **de Ninguém,** LUIS força uma expressão que está fora do domínio em uma das intenções de domínio. Isto irá distorcer as pontuações de predição ensinando LUIS a intenção errada para a expressão. 

Quando uma expressão é prevista como a intenção None, a aplicação do cliente pode fazer mais perguntas ou fornecer um menu para direcionar o utilizador para escolhas válidas. 

## <a name="negative-intentions"></a>Intenções negativas 
Se quiser determinar intenções negativas e positivas, como **"quero** um carro" e **"não** quero um carro", pode criar duas intenções (uma positiva e uma negativa) e acrescentar declarações apropriadas para cada um. Ou pode criar um objetivo único e marcar os dois diferentes positivos e negativos termos como uma entidade.  

## <a name="intents-and-patterns"></a>Intençãos e padrões

Se tiver declarações de exemplo, que podem ser definidas em parte ou no todo como uma expressão regular, considere usar a entidade de [expressão regular](luis-concept-entity-types.md#regular-expression-entity) emparelhada com um [padrão](luis-concept-patterns.md). 

A utilização de uma entidade de expressão regular garante a extração de dados para que o padrão seja igualado. A correspondência de padrões garante que uma intenção exata é devolvida. 

## <a name="intent-balance"></a>Saldo de intenção
Os objetivos de domínio de aplicação devem ter um equilíbrio entre expressões com entre cada intenção. Não tiver um objetivo com expressões 10 com e o outro objetivo com expressões 500 com. Isso não fosse equilibrado. Se tiver esta situação, reveja a intenção com 500 expressões para ver se muitas das intenções podem ser reorganizadas num [padrão](luis-concept-patterns.md). 

A intenção **de "None"** não está incluída na balança. Essa intenção deve conter 10% das expressões total na aplicação.

## <a name="intent-limits"></a>Limites de intenção
[Limites](luis-boundaries.md#model-boundaries) de revisão para entender quantas intenções pode adicionar a um modelo. 

### <a name="if-you-need-more-than-the-maximum-number-of-intents"></a>Se precisar de mais do que o número máximo de intenções 
Em primeiro lugar, considere se o seu sistema está a utilizar demasiados intenções. 

### <a name="can-multiple-intents-be-combined-into-single-intent-with-entities"></a>Podem vários objetivos ser combinados num único objetivo com entidades 
Objetivos que são muito semelhantes podem tornar mais difícil para o LUIS distinguir entre eles. Objetivos devem ser diversificados suficiente para capturar as principais tarefas que o utilizador está pedindo, mas não precisam de capturar cada caminho demora de seu código. Por exemplo, BookFlight e FlightCustomerService podem ser separados objetivos num aplicativo de viagem, mas BookInternationalFlight e BookDomesticFlight são muito semelhantes. Se o sistema precisar para distingui-los, utilize entidades ou outros lógica em vez de objetivos. 

### <a name="dispatcher-model"></a>Modelo de Dispatcher
Saiba mais sobre a combinação de apps de fabricante luis e QnA com o [modelo de despacho.](luis-concept-enterprise.md#when-you-need-to-combine-several-luis-and-qna-maker-apps) 

### <a name="request-help-for-apps-with-significant-number-of-intents"></a>Pedir ajuda para aplicações com o número significativo de intenções
Se reduzir o número de objetivos ou divisão de suas intenções em várias aplicações não estiver a funcionar, contacte o suporte. Se a sua subscrição Azure incluir serviços de suporte, contacte o [suporte técnico do Azure.](https://azure.microsoft.com/support/options/) 

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre [entidades](luis-concept-entity-types.md), que são palavras importantes relevantes para as intenções
* Saiba como [adicionar e gerir as intenções](luis-how-to-add-intents.md) na sua aplicação LUIS.
* Rever [as boas práticas](luis-concept-best-practices.md) de intenção
