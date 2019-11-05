---
title: Planejar seu aplicativo-LUIS
titleSuffix: Azure Cognitive Services
description: Contorne as entidades e as intenções de aplicativos relevantes e, em seguida, crie seus planos de aplicativo no Reconhecimento vocal Intelligent Services (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: b5e5df111b81cb60b6d194be190421bdb5ce2683
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73467708"
---
# <a name="plan-your-luis-app-schema-with-subject-domain-and-data-extraction"></a>Planejar o esquema do aplicativo LUIS com a extração de dados e domínio do assunto

Um esquema de aplicativo LUIS contém intenções e entidades relevantes para seu domínio de assunto. As intenções classificam declarações do usuário e as entidades extraem dados do declarações do usuário. 

## <a name="identify-your-domain"></a>Identificar seu domínio

Um aplicativo LUIS é centralizado em um tópico específico de domínio.  Por exemplo, você pode ter um aplicativo de viagem que executa a reserva de ingressos, vôos, hotéis e carros de aluguel. Outro aplicativo pode fornecer conteúdo relacionado a exercer, controlar esforços de ADEQÜAÇÃO e definir metas. Identificar o domínio ajuda a localizar palavras ou frases que são importantes para seu domínio.

> [!TIP]
> O LUIS oferece [domínios pré-criados](luis-how-to-use-prebuilt-domains.md) para muitos cenários comuns.
> Verifique se você pode usar um domínio predefinido como um ponto de partida para seu aplicativo.

## <a name="identify-your-intents"></a>Identificar suas intenções

Pense nas [intenções](luis-concept-intent.md) que são importantes para a tarefa do seu aplicativo. 

Vamos pegar o exemplo de um aplicativo de viagem, com funções para reservar um vôo e verificar o clima no destino do usuário. Você pode definir as tentativas de `BookFlight` e `GetWeather` para essas ações. 

Em um aplicativo mais complexo com mais funções, você tem mais intenções e deve defini-las com cuidado para que as intenções não sejam muito específicas. Por exemplo, `BookFlight` e `BookHotel` talvez precisem de tentativas separadas, mas `BookInternationalFlight` e `BookDomesticFlight` podem ser muito semelhantes.

> [!NOTE]
> É uma prática recomendada usar apenas o número de tentativas que você precisa para executar as funções do seu aplicativo. Se você definir tentativas demais, será mais difícil para LUIS classificar declarações corretamente. Se você definir poucos, eles poderão ser tão gerais que se sobrepõem.

Se você não precisar identificar a intenção geral do usuário, adicione todos os declarações de usuário de exemplo à intenção nenhum. Se seu aplicativo aumentar a necessidade de mais intenções, você poderá criá-los mais tarde. 

## <a name="create-example-utterances-for-each-intent"></a>Criar exemplo declarações para cada tentativa

Depois de determinar as tentativas, crie um exemplo de 15 a 30 declarações para cada tentativa. Para começar, não tem menos que esse número ou crie muitas declarações para cada tentativa. Cada expressão deve ser diferente do expressão anterior. Uma boa variedade no declarações inclui contagem geral de palavras, opções de palavras, conjugação de verbo e pontuação. 

Examine [declarações](luis-concept-utterance.md) para obter mais informações.

## <a name="identify-your-entities"></a>Identificar suas entidades

No exemplo declarações, identifique as entidades que você deseja extrair. Para reservar um vôo, você precisa de informações como o destino, a data, a companhia aérea, a categoria de tíquete e a classe de viagem. Crie entidades para esses tipos de dados e marque as [entidades](luis-concept-entity-types.md) no exemplo declarações porque elas são importantes para realizar uma intenção. 

Quando você determina quais entidades usar em seu aplicativo, tenha em mente que existem diferentes tipos de entidades para capturar relações entre tipos de objetos. As [entidades no Luis](luis-concept-entity-types.md) fornecem mais detalhes sobre os diferentes tipos.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o [ciclo de desenvolvimento](luis-concept-app-iteration.md)típico.  