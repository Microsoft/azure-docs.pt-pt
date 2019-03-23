---
title: Intenções
titleSuffix: Language Understanding - Azure Cognitive Services
description: Um objetivo único representa uma tarefa ou ação o usuário quer executar. É um objetivo ou objetivo expressado na expressão de um utilizador. Defina um conjunto de objetivos que corresponde a ações que os usuários querem tirar em seu aplicativo.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: diberry
ms.openlocfilehash: e635a11cb99d11befc40703d9f5d2abec8559632
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58371116"
---
# <a name="concepts-about-intents-in-your-luis-app"></a>Conceitos sobre intenções na sua aplicação LUIS

Um objetivo representa uma tarefa ou ação o usuário quer executar. Este é um objetivo ou objetivo expressado num usuário [expressão](luis-concept-utterance.md).

Defina um conjunto de objetivos que corresponde a ações que os usuários querem tirar em seu aplicativo. Por exemplo, uma aplicação de viagens define vários objetivos:

Objetivos de aplicações de deslocação   |   Expressões de exemplo   | 
------|------|
 ReservarBilhete     |   "Programar-me um vôo para Rio próxima semana" <br/> "Voar-me para Rio no 24th" <br/> "Preciso de um pedido de suporte do plano seguinte Domingo a Rio de Janeiro"    |
 Saudação     |   "Olá" <br/>"Hello" <br/>"Bom dia"  |
 CheckWeather | "O que é o clima, como em Boston?" <br/> "Mostre-me a previsão para este fim de semana" |
 Nenhuma         | "Get-me uma receita de cookie"<br>"Os Lakers ganhar?" |

Todas as aplicações vêm com a intenção predefinida, "[None](#none-intent-is-fallback-for-app)", que é a intenção de contingência. 

## <a name="prebuilt-domains-provide-intents"></a>Domínios pré-concebidos fornecem objetivos
Além de objetivos que definir, pode utilizar os objetivos criados previamente a partir de um dos domínios criados previamente. Para obter mais informações, consulte [utilizar domínios pré-concebidos nas aplicações de LUIS](luis-how-to-use-prebuilt-domains.md) para saber mais sobre como personalizar os objetivos de um domínio pré-criado para utilização na sua aplicação.

## <a name="return-all-intents-scores"></a>Devolver as pontuações de todas as intenções
Atribuição de uma expressão a um objetivo único. Quando o LUIS recebe uma expressão no ponto de extremidade, ele retorna a intenção de principal de um para essa expressão. Se pretender que as pontuações para todos os objetivos para a expressão, pode fornecer `verbose=true` sinalizador na seqüência de consulta da API [chamada de ponto final](https://aka.ms/v1-endpoint-api-docs). 

## <a name="intent-compared-to-entity"></a>Em comparação comparada a entidade de intenção
A intenção representa a ação a chatbot deve levar para o utilizador e baseia-se a expressão inteira. A entidade representa palavras ou frases contidos dentro da expressão. Uma expressão pode ter apenas uma parte superior de intenção de classificação, mas ele pode ter muitas entidades. 

<a name="how-do-intents-relate-to-entities"></a> Criar uma intenção de quando o usuário _intenção_ dispararia uma ação em seu aplicativo de cliente, como uma chamada para a função de checkweather(). Em seguida, crie uma entidade para representar os parâmetros necessários para executar a ação. 

|Intenção de exemplo   | Entidade | Entidade em expressões de exemplo   | 
|------------------|------------------------------|------------------------------|
| CheckWeather | {"type": "localização", "entity": "porto"}<br>{"type": "builtin.datetimeV2.date","entity": "" amanhã, penso: "Resolução": "2018-05-23"} | E o clima, como na `Seattle` `tomorrow`? |
| CheckWeather | {"type": "date_range", "entity": "este fim de semana"} | Mostre-me a previsão para `this weekend` | 

## <a name="custom-intents"></a>Objetivos personalizados

Da mesma forma bem-intencionado [expressões com](luis-concept-utterance.md) correspondem a um objetivo único. Expressões com em sua intenção, podem utilizar qualquer [entidade](luis-concept-entity-types.md) na aplicação, uma vez que as entidades não são específicos de intenção. 

## <a name="prebuilt-domain-intents"></a>Objetivos de domínio pré-criado

[Domínios pré-concebidos](luis-how-to-use-prebuilt-domains.md) ter intenções com expressões com.  

## <a name="none-intent"></a>Intenção None (Nenhuma)

O **None** intenção é importante para todos os aplicativos e não deve ter zero expressões de com.

### <a name="none-intent-is-fallback-for-app"></a>Nenhuma a intenção é contingência para a aplicação
O **None** intenção é uma intenção catch-all ou de contingência. Ele é usado para ensinar LUIS expressões que não são importantes no domínio de aplicativo (área de assunto). O **None** intenção deve ter entre 10 e 20% das expressões total no aplicativo. Não deixe o None vazio. 

### <a name="none-intent-helps-conversation-direction"></a>Nenhum intenção ajuda a direção de conversação
Quando uma expressão é prevista como None intenção e retornado para o chatbot com essa previsão, o bot pode fazer mais perguntas ou fornecer um menu para direcionar o usuário escolhas válidas no chatbot. 

### <a name="no-utterances-in-none-intent-skews-predictions"></a>Sem expressões de com em nenhum intenção inclina previsões
Se não adicionar quaisquer expressões de com para o **None** intenção, LUIS força uma expressão que está fora do domínio em um dos objetivos de domínio. Isto irá distorcer as pontuações de predição ensinando LUIS a intenção errada para a expressão. 

### <a name="add-utterances-to-the-none-intent"></a>Adicionar expressões com a intenção None
O **None** intenção é criada mas deixada em branco com o objetivo. Preencha-o com expressões que estão fora do seu domínio. Uma expressão boa para **None** é algo totalmente fora da aplicação, bem como o setor a aplicação serve. Por exemplo, uma aplicação de viagens não deve utilizar qualquer expressão para **None** pode relacionadas com a viajar, como as reservas, faturação, comida, hospitalidade, carga, entretenimento em utilização. 

Que tipo de expressões com deixarei para None intenção? Começar com algo específico que o seu bot não deve responder a essas "que tipo de dinossauro tem dentes azuis?" Essa é uma pergunta muito específica até agora fora de uma aplicação de viagens. 

### <a name="none-is-a-required-intent"></a>Nenhuma é um objetivo necessário
O **None** intenção é um objetivo necessário e não pode ser eliminada ou renomeada.

## <a name="negative-intentions"></a>Intenções negativas 
Se quiser determinar intenções tanto negativas como positivas, por exemplo, "eu **deseja** um carro" e "eu **não** desejam um carro", pode criar dois objetivos (uma positiva e uma negativa) e adicionar expressões apropriados para cada. Ou pode criar um objetivo único e marcar os dois diferentes positivos e negativos termos como uma entidade.  

## <a name="intents-and-patterns"></a>Objetivos e padrões

Se tiver de expressões de exemplo, o que podem ser definidas em parte ou totalmente como uma expressão regular, considere utilizar o [entidade de expressão regular](luis-concept-entity-types.md#regular-expression-entity) emparelhado com um [padrão](luis-concept-patterns.md). 

Utilizar uma entidade de expressão regular garante a extração de dados para que o padrão é correspondido. Correspondência de padrões garante que uma intenção exata é devolvida. 

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



## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre [entidades](luis-concept-entity-types.md), que são importantes palavras relevantes para as intenções
* Saiba como [adicionar e gerir os objetivos](luis-how-to-add-intents.md) na sua aplicação LUIS.
* Reveja a intenção [melhores práticas](luis-concept-best-practices.md)
