---
title: Planeie a sua app - LUIS
description: Delineie as intenções e entidades de aplicações relevantes e, em seguida, crie os seus planos de aplicação em Serviços Inteligentes de Compreensão de Linguagem (LUIS).
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/14/2020
ms.openlocfilehash: 66df23466694fe8b9caea4e56565cde6d8fd7416
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95018961"
---
# <a name="plan-your-luis-app-schema-with-subject-domain-and-data-extraction"></a>Planeie o seu esquema de aplicações LUIS com domínio sujeito e extração de dados

Um esquema de aplicações LUIS contém [intenções](luis-glossary.md#intent) e [entidades relevantes](luis-glossary.md#entity) para o seu [domínio](luis-glossary.md#domain)sujeito. As intenções classificam as [declarações dos utilizadores,](luis-glossary.md#utterance)e as entidades extraem dados das declarações dos utilizadores.

## <a name="identify-your-domain"></a>Identifique o seu domínio

Uma aplicação LUIS está centrada em torno de um domínio de assunto. Por exemplo, você pode ter uma app de viagem que trata da reserva de bilhetes, voos, hotéis e carros de aluguel. Outra aplicação pode fornecer conteúdo relacionado com exercício, rastreio de esforços de fitness e definição de objetivos. Identificar o domínio ajuda-o a encontrar palavras ou frases relevantes para o seu domínio.

> [!TIP]
> LUIS oferece [domínios pré-construídos](./howto-add-prebuilt-models.md) para muitos cenários comuns. Verifique se pode utilizar um domínio pré-construído como ponto de partida para a sua aplicação.

## <a name="identify-your-intents"></a>Identifique as suas intenções

Pense nas intenções que são [importantes](luis-concept-intent.md) para a tarefa da sua aplicação.

Vejamos o exemplo de uma aplicação de viagem, com funções para reservar um voo e verificar o tempo no destino do utilizador. Pode definir as `BookFlight` intenções e `GetWeather` as intenções destas ações.

Numa aplicação mais complexa, com mais funções, tem mais intenções, e deve defini-las cuidadosamente para que as intenções não sejam muito específicas. Por exemplo, `BookFlight` e pode ter de ser `BookHotel` intenções separadas, mas `BookInternationalFlight` e pode ser muito `BookDomesticFlight` semelhante.

> [!NOTE]
> É uma boa prática utilizar apenas as intenções necessárias para desempenhar as funções da sua app. Se definir demasiadas intenções, torna-se mais difícil para o LUIS classificar corretamente as expressões. Se definires muito poucos, podem ser tão gerais que se sobrepõem.

Se não precisar de identificar a intenção geral do utilizador, adicione todas as declarações de exemplo do utilizador à `None` intenção. Se a sua aplicação crescer para precisar de mais intenções, pode criá-las mais tarde.

## <a name="create-example-utterances-for-each-intent"></a>Criar palavras de exemplo para cada intenção

Para começar, evite criar demasiadas declarações para cada intenção. Uma vez determinadas as intenções, crie 15 a 30 palavras por intenção. Cada expressão deve ser diferente das expressões anteriormente fornecidas. Uma boa variedade em expressões inclui contagem geral de palavras, escolha de palavras, verbo tenso e [pontuação.](luis-reference-application-settings.md#punctuation-normalization)

Para obter mais informações, consulte [a compreensão de boas expressões para aplicações LUIS.](luis-concept-utterance.md)

## <a name="identify-your-entities"></a>Identifique as suas entidades

No exemplo, identifique as entidades que pretende extrair. Para reservar um voo, você precisa de informações como o destino, data, companhia aérea, categoria de bilhete e classe de viagem. Criar entidades para estes tipos de dados e, em seguida, marcar as [entidades](luis-concept-entity-types.md) nas declarações de exemplo. As entidades são importantes para realizar uma intenção.

Ao determinar quais as entidades a utilizar na sua app, tenha em mente que existem diferentes tipos de entidades para capturar relações entre tipos de objetos. [As entidades da LUIS](luis-concept-entity-types.md) fornecem mais detalhes sobre os diferentes tipos.

> [!TIP]
> A LUIS oferece [entidades pré-construídas](./howto-add-prebuilt-models.md) para cenários comuns e conversadores de utilizadores. Considere usar as entidades pré-construídas como ponto de partida para o desenvolvimento da sua aplicação.

## <a name="resolution-with-intent-or-entity"></a>Resolução com intenção ou entidade?

Em muitos casos, especialmente quando trabalham com conversas naturais, os utilizadores fornecem uma expressão que pode conter mais do que uma função ou intenção. Para resolver este problema, uma regra geral do polegar é entender que a representação da saída pode ser feita tanto em intenções como em entidades. Esta representação deve ser mapeável às ações de candidatura do seu cliente, e não precisa de se limitar às intenções.

**Int-ent-ties** é o conceito de que as ações (geralmente entendidas como intenções) também podem ser capturadas como entidades e confiadas nesta forma na saída JSON onde você pode mapeá-lo para uma ação específica. _A negação_ é um uso comum para alavancar esta dependência tanto da intenção como da entidade para a extração completa.

Considere as seguintes duas expressões que são muito próximas considerando a escolha da palavra, mas têm resultados diferentes:

|Expressão|
|--|
|`Please schedule my flight from Cairo to Seattle`|
|`Cancel my flight from Cairo to Seattle`|

Em vez de ter duas intenções separadas, crie uma única intenção com uma `FlightAction` entidade de aprendizagem automática. A entidade de machine learning deve extrair os detalhes da ação tanto para um agendamento como para um pedido de cancelamento, bem como para uma origem ou localização de destino.

A `FlightAction` entidade seria estruturada no pseudo-esquema da entidade e subentências de machine learning:

* Reação de Voo
    * Ação
    * Origem
    * Destino

Para ajudar a extração adicione características às subentidades. Escolherá as suas funcionalidades com base no vocabulário que espera ver nas declarações dos utilizadores e nos valores que pretende devolvidos na resposta de previsão.

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Aprender o ciclo de desenvolvimento LUIS](luis-concept-app-iteration.md)