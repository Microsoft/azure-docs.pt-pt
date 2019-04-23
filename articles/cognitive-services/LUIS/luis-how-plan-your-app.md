---
title: Planear a sua aplicação
titleSuffix: Language Understanding - Azure Cognitive Services
description: Descrever a aplicação relevante intenções e entidades e, em seguida, criar seus planos de aplicativo no Language Understanding Intelligent Service (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: diberry
ms.openlocfilehash: 9d54cff81f39f41b60800e9b33f3b4da1a735d85
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59795000"
---
# <a name="plan-your-luis-app-with-subject-domain-intents-and-entities"></a>Planear a sua aplicação LUIS com o domínio de assunto, objetivos e entidades

É importante planear a sua aplicação. Identifica o seu domínio, incluindo possíveis intenções e entidades que são relevantes para a sua aplicação.  

## <a name="identify-your-domain"></a>Identificar o seu domínio

Uma aplicação do LUIS centra-se um tópico específica do domínio.  Por exemplo, pode ter uma aplicação de viagens que executa a reserva de pedidos de suporte, voos, hotéis e carros de aluguel. Outra aplicação pode fornecer conteúdo relacionado para exercitar, os esforços de adequação de controlo e definir metas. Identificar o domínio de ajuda para encontrar palavras ou frases que são importantes para seu domínio.

> [!TIP]
> LUIS oferecem [domínios pré-concebidos](luis-how-to-use-prebuilt-domains.md) para inúmeros cenários comuns.
> Verifique se pode utilizar um domínio pré-criado como ponto de partida para a sua aplicação.

## <a name="identify-your-intents"></a>Identificar suas intenções

Pense sobre o [intenções](luis-concept-intent.md) que são importantes para tarefas de seu aplicativo. Vejamos o exemplo de uma aplicação de viagens, com as funções para programar um vôo e verificar o tempo no destino do usuário. Pode definir os objetivos de "BookFlight" e "GetWeather" para estas ações. Aplicações mais complexas com mais funções, tem mais objetivos e deve defini-los cuidadosamente modo a não ser muito específica. Por exemplo, "BookFlight" e "BookHotel" talvez precise ser intenções separadas, mas "BookInternationalFlight" e "BookDomesticFlight" podem ser muito semelhantes.

> [!NOTE]
> É melhor prática para utilizar apenas os objetivos, conforme necessário executar as funções da sua aplicação. Se definir objetivos de demasiados, torna-se mais difícil para o LUIS classificar expressões com corretamente. Se definir muito poucos ou pode ser tão gerais como para uma sobreposição.

## <a name="create-example-utterances-for-each-intent"></a>Criar expressão de exemplo para cada intenção

Depois de determinar os objetivos, crie 10 ou 15 expressões de exemplo para cada intenção. Para começar, não existem menos do que este número ou criar muitas expressões para cada intenção. Cada ocorrência de pronunciação deve ser diferente do que a expressão anterior. Uma variedade de bom nas expressões inclui contagem de palavras geral, escolha do word, tempo verbal de verbo e pontuação. 

## <a name="identify-your-entities"></a>Identificar suas entidades

As expressões de exemplo, identificar as entidades que pretende extraídos. Para programar um vôo, precisa de algumas informações como o destino, a data, a companhia aérea, a categoria de pedido de suporte e classe de viagens. Criar entidades para estes tipos de dados e, em seguida, marcar a [entidades](luis-concept-entity-types.md) nas expressões de exemplo porque eles são importantes para a realização de um objetivo. 

Quando determinar quais entidades para utilizar na sua aplicação, tenha em atenção que existem diferentes tipos de entidades para capturar as relações entre tipos de objetos. [Entidades no LUIS](luis-concept-entity-types.md) fornece mais detalhes sobre os diferentes tipos.

## <a name="next-steps"></a>Passos Seguintes

Depois da aplicação é preparada, publicado e obtém as expressões de ponto de extremidade, pretendem implementar melhorias de previsão com [aprendizagem ativa](luis-how-to-review-endpoint-utterances.md), [frase listas](luis-concept-feature.md), e [padrões](luis-concept-patterns.md). 


* Ver [crie seu primeiro aplicativo do Language Understanding Intelligent Service (LUIS)](luis-get-started-create-app.md) para uma passo a passo rápido de como criar uma aplicação do LUIS.
