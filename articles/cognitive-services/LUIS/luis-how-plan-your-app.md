---
title: Planejar seu aplicativo-LUIS
titleSuffix: Azure Cognitive Services
description: Descrever a aplicação relevante intenções e entidades e, em seguida, criar seus planos de aplicativo no Language Understanding Intelligent Service (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: diberry
ms.openlocfilehash: 6a155f4c43da03ccdc40d289742918973aa6da7b
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326781"
---
# <a name="plan-your-luis-app-schema-with-subject-domain-and-data-extraction"></a>Planejar o esquema do aplicativo LUIS com a extração de dados e domínio do assunto

Um esquema de aplicativo LUIS contém [intenções](luis-glossary.md#intent) e [entidades](luis-glossary.md#entity) relevantes para seu [domínio](luis-glossary.md#domain)de assunto. As intenções classificam [declarações](luis-glossary.md#utterance)do usuário e as entidades extraem dados do declarações do usuário.

## <a name="identify-your-domain"></a>Identificar o seu domínio

Um aplicativo LUIS é centralizado em um domínio de assunto. Por exemplo, você pode ter um aplicativo de viagem que lide com a reserva de ingressos, vôos, hotéis e carros de aluguel. Outra aplicação pode fornecer conteúdo relacionado para exercitar, os esforços de adequação de controlo e definir metas. Identificar o domínio ajuda a localizar palavras ou frases relevantes para seu domínio.

> [!TIP]
> O LUIS oferece [domínios pré-criados](luis-how-to-use-prebuilt-domains.md) para muitos cenários comuns. Verifique se pode utilizar um domínio pré-criado como ponto de partida para a sua aplicação.

## <a name="identify-your-intents"></a>Identificar suas intenções

Pense nas [intenções](luis-concept-intent.md) que são importantes para a tarefa do seu aplicativo.

Vejamos o exemplo de uma aplicação de viagens, com as funções para programar um vôo e verificar o tempo no destino do usuário. Você pode definir as tentativas de `BookFlight` e `GetWeather` para essas ações.

Em um aplicativo mais complexo com mais funções, você tem mais intenções e deve defini-las com cuidado para que as intenções não sejam muito específicas. Por exemplo, `BookFlight` e `BookHotel` talvez precisem de tentativas separadas, mas `BookInternationalFlight` e `BookDomesticFlight` podem ser muito semelhantes.

> [!NOTE]
> É melhor prática para utilizar apenas os objetivos, conforme necessário executar as funções da sua aplicação. Se definir objetivos de demasiados, torna-se mais difícil para o LUIS classificar expressões com corretamente. Se você definir poucos, eles poderão ser tão gerais que se sobrepõem.

Se você não precisar identificar a intenção geral do usuário, adicione todos os declarações de usuário de exemplo à intenção de `None`. Se seu aplicativo aumentar a necessidade de mais intenções, você poderá criá-los mais tarde.

## <a name="create-example-utterances-for-each-intent"></a>Criar expressão de exemplo para cada intenção

Para começar, Evite criar muitas declarações para cada tentativa. Depois de determinar as intenções, crie um exemplo de 15 a 30 declarações por tentativa. Cada expressão deve ser diferente do declarações fornecido anteriormente. Uma boa variedade no declarações inclui contagem geral de palavras, opções de palavras, conjugação de verbo e pontuação.

Para obter mais informações, consulte [Understanding Good declarações for Luis apps](luis-concept-utterance.md).

## <a name="identify-your-entities"></a>Identificar suas entidades

As expressões de exemplo, identificar as entidades que pretende extraídos. Para reservar um vôo, você precisa de informações como o destino, a data, a companhia aérea, a categoria de tíquete e a classe de viagem. Crie entidades para esses tipos de dados e marque as [entidades](luis-concept-entity-types.md) no exemplo declarações. As entidades são importantes para realizar uma intenção.

Ao determinar quais entidades usar em seu aplicativo, tenha em mente que existem diferentes tipos de entidades para capturar relações entre tipos de objeto. As [entidades no Luis](luis-concept-entity-types.md) fornecem mais detalhes sobre os diferentes tipos.

> [!TIP]
> O LUIS oferece [entidades predefinidas](luis-prebuilt-entities.md) para cenários de usuário comuns e de conversação. Considere o uso de entidades predefinidas como um ponto de partida para o desenvolvimento de aplicativos.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Aprendendo o lifecylce de desenvolvimento do LUIS](luis-concept-app-iteration.md)

