---
title: Intenções e entidades-LUIS
titleSuffix: Azure Cognitive Services
description: Uma única intenção representa uma tarefa ou ação que o usuário deseja executar. É um objetivo ou objetivo expressado na expressão de um utilizador. Defina um conjunto de objetivos que corresponde a ações que os usuários querem tirar em seu aplicativo.
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
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280892"
---
# <a name="intents-in-your-luis-app"></a>Tentativas em seu aplicativo LUIS

Um objetivo representa uma tarefa ou ação o usuário quer executar. Este é um objetivo ou objetivo expressado num usuário [expressão](luis-concept-utterance.md).

Defina um conjunto de objetivos que corresponde a ações que os usuários querem tirar em seu aplicativo. Por exemplo, uma aplicação de viagens define vários objetivos:

Objetivos de aplicações de deslocação   |   Expressões de exemplo   | 
------|------|
 ReservarBilhete     |   "Programar-me um vôo para Rio próxima semana" <br/> "Voar-me para Rio no 24th" <br/> "Preciso de um pedido de suporte do plano seguinte Domingo a Rio de Janeiro"    |
 Saudação     |   "Olá" <br/>"Hello" <br/>"Bom dia"  |
 CheckWeather | "O que é o clima, como em Boston?" <br/> "Mostre-me a previsão para este fim de semana" |
 Nenhum         | "Get-me uma receita de cookie"<br>"Os Lakers ganhar?" |

Todos os aplicativos vêm com a intenção predefinida, "[None](#none-intent)", que é a tentativa de fallback. 

## <a name="prebuilt-domains-provide-intents"></a>Domínios pré-concebidos fornecem objetivos
Além de tentativas que você define, você pode usar tentativas predefinidas de um dos [domínios predefinidos](luis-how-to-use-prebuilt-domains.md). 

## <a name="return-all-intents-scores"></a>Devolver as pontuações de todas as intenções
Atribuição de uma expressão a um objetivo único. Quando LUIS recebe um expressão no ponto de extremidade, por padrão, ele retorna a principal intenção desse expressão. 

Se você quiser as pontuações para todas as intenções para o expressão, poderá fornecer um sinalizador na cadeia de caracteres de consulta da API de previsão. 

|Versão da API de previsão|Sinalizador|
|--|--|
|V2|`verbose=true`|
|V3|`show-all-intents=true`|

## <a name="intent-compared-to-entity"></a>Em comparação comparada a entidade de intenção
A intenção representa a ação que o aplicativo deve executar para o usuário e é baseado em todo o expressão. Uma expressão pode ter apenas uma parte superior de intenção de classificação, mas ele pode ter muitas entidades. 

<a name="how-do-intents-relate-to-entities"></a>

Crie uma intenção quando a _intenção_ do usuário dispararia uma ação em seu aplicativo cliente, como uma chamada para a função checkweather (). Em seguida, crie entidades para representar os parâmetros necessários para executar a ação. 

|Intenção   | Entidade | Expressão de exemplo   | 
|------------------|------------------------------|------------------------------|
| CheckWeather | {"type": "localização", "entity": "porto"}<br>{"type": "builtin.datetimeV2.date","entity": "" amanhã, penso: "Resolução": "2018-05-23"} | E o clima, como na `Seattle` `tomorrow`? |
| CheckWeather | {"type": "date_range", "entity": "este fim de semana"} | Mostre-me a previsão para `this weekend` | 
||||

## <a name="prebuilt-domain-intents"></a>Objetivos de domínio pré-criado

[Domínios pré-criados](luis-how-to-use-prebuilt-domains.md) fornecem intenções com declarações. 

## <a name="none-intent"></a>Intenção None (Nenhuma)

O **None** intenção é criada mas deixada em branco com o objetivo. O **None** intenção é um objetivo necessário e não pode ser eliminada ou renomeada. Preencha-o com expressões que estão fora do seu domínio.

A intenção **None** é a intenção de fallback, importante em todos os aplicativos e deve ter 10% do total de declarações. Ele é usado para ensinar LUIS expressões que não são importantes no domínio de aplicativo (área de assunto). Se não adicionar quaisquer expressões de com para o **None** intenção, LUIS força uma expressão que está fora do domínio em um dos objetivos de domínio. Isto irá distorcer as pontuações de predição ensinando LUIS a intenção errada para a expressão. 

Quando um expressão é previsto como a intenção None, o aplicativo cliente pode fazer mais perguntas ou fornecer um menu para direcionar o usuário a opções válidas. 

## <a name="negative-intentions"></a>Intenções negativas 
Se quiser determinar intenções tanto negativas como positivas, por exemplo, "eu **deseja** um carro" e "eu **não** desejam um carro", pode criar dois objetivos (uma positiva e uma negativa) e adicionar expressões apropriados para cada. Ou pode criar um objetivo único e marcar os dois diferentes positivos e negativos termos como uma entidade.  

## <a name="intents-and-patterns"></a>Tentativas e padrões

Se você tiver um exemplo de declarações, que pode ser definido em parte ou inteiro como uma expressão regular, considere usar a [entidade de expressão regular](luis-concept-entity-types.md#regular-expression-entity) emparelhada com um [padrão](luis-concept-patterns.md). 

O uso de uma entidade de expressão regular garante a extração de dados para que o padrão seja correspondido. O padrão de correspondência garante que uma intenção exata seja retornada. 

## <a name="intent-balance"></a>Saldo de intenção
Os objetivos de domínio de aplicação devem ter um equilíbrio entre expressões com entre cada intenção. Não tiver um objetivo com expressões 10 com e o outro objetivo com expressões 500 com. Isso não fosse equilibrado. Se tiver esta situação, reveja a intenção com expressões 500 com para ver se muitos dos objetivos podem reorganizar num [padrão](luis-concept-patterns.md). 

O **None** intenção não está incluída no saldo. Essa intenção deve conter 10% das expressões total na aplicação.

## <a name="intent-limits"></a>Limites de intenção
Revisão [limites](luis-boundaries.md#model-boundaries) para compreender quantos intenções pode adicionar a um modelo. 

### <a name="if-you-need-more-than-the-maximum-number-of-intents"></a>Se precisar de mais do que o número máximo de intenções 
Em primeiro lugar, considere se o seu sistema está a utilizar demasiados intenções. 

### <a name="can-multiple-intents-be-combined-into-single-intent-with-entities"></a>Podem vários objetivos ser combinados num único objetivo com entidades 
Objetivos que são muito semelhantes podem tornar mais difícil para o LUIS distinguir entre eles. Objetivos devem ser diversificados suficiente para capturar as principais tarefas que o utilizador está pedindo, mas não precisam de capturar cada caminho demora de seu código. Por exemplo, BookFlight e FlightCustomerService podem ser separados objetivos num aplicativo de viagem, mas BookInternationalFlight e BookDomesticFlight são muito semelhantes. Se o sistema precisar para distingui-los, utilize entidades ou outros lógica em vez de objetivos. 

### <a name="dispatcher-model"></a>Modelo de Dispatcher
Saiba mais sobre combinar o LUIS e QnA maker de aplicações com o [modelo de expedição](luis-concept-enterprise.md#when-you-need-to-combine-several-luis-and-qna-maker-apps). 

### <a name="request-help-for-apps-with-significant-number-of-intents"></a>Pedir ajuda para aplicações com o número significativo de intenções
Se reduzir o número de objetivos ou divisão de suas intenções em várias aplicações não estiver a funcionar, contacte o suporte. Se a sua subscrição do Azure inclui os serviços de suporte, contacte [suporte técnico do Azure](https://azure.microsoft.com/support/options/). 

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [entidades](luis-concept-entity-types.md), que são importantes palavras relevantes para as intenções
* Saiba como [adicionar e gerir os objetivos](luis-how-to-add-intents.md) na sua aplicação LUIS.
* Reveja a intenção [melhores práticas](luis-concept-best-practices.md)
