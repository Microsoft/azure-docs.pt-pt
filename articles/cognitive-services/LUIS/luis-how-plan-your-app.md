---
title: Planeie a sua app - LUIS
description: Delineie as intenções e entidades relevantes da aplicação e, em seguida, crie os seus planos de aplicação em Serviços Inteligentes de Compreensão de Línguas (LUIS).
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: dfed27a05973a2ea2e9a97eaa1c233b847b33d87
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382297"
---
# <a name="plan-your-luis-app-schema-with-subject-domain-and-data-extraction"></a>Planeje o seu esquema de aplicação LUIS com domínio sujeito e extração de dados

Um esquema de aplicação LUIS contém [intenções](luis-glossary.md#intent) e [entidades relevantes](luis-glossary.md#entity) para o seu [domínio](luis-glossary.md#domain)de assunto. As intenções classificam as [declarações](luis-glossary.md#utterance)dos utilizadores, e as entidades extraem dados das declarações do utilizador.

## <a name="identify-your-domain"></a>Identifique o seu domínio

Uma aplicação LUIS está centrada em torno de um domínio de assunto. Por exemplo, você pode ter uma app de viagens que lida com a reserva de bilhetes, voos, hotéis e carros de aluguel. Outra aplicação pode fornecer conteúdo relacionado com o exercício, acompanhamento dos esforços de fitness e definição de objetivos. Identificar o domínio ajuda-o a encontrar palavras ou frases relevantes para o seu domínio.

> [!TIP]
> O LUIS oferece [domínios pré-construídos](luis-how-to-use-prebuilt-domains.md) para muitos cenários comuns. Verifique se pode usar um domínio pré-construído como ponto de partida para a sua aplicação.

## <a name="identify-your-intents"></a>Identifique as suas intenções

Pense nas intenções que são [importantes](luis-concept-intent.md) para a tarefa da sua aplicação.

Vamos dar o exemplo de uma aplicação de viagem, com funções para reservar um voo e verificar o tempo no destino do utilizador. Pode definir `BookFlight` as `GetWeather` intenções e intenções para estas ações.

Numa aplicação mais complexa, com mais funções, tem mais intenções, e deve defini-las cuidadosamente para que as intenções não sejam muito específicas. Por `BookFlight` exemplo, `BookHotel` e pode ter de `BookInternationalFlight` ser `BookDomesticFlight` intenções separadas, mas pode ser muito semelhante.

> [!NOTE]
> É uma boa prática usar apenas as intenções necessárias para desempenhar as funções da sua aplicação. Se definir demasiadas intenções, torna-se mais difícil para luis classificar corretamente as expressões. Se definirmos poucos, podem ser tão gerais que se sobrepõem.

Se não precisar identificar a intenção geral do utilizador, adicione `None` todas as declarações de exemplo do utilizador à intenção. Se a sua aplicação crescer a precisar de mais intenções, pode criá-las mais tarde.

## <a name="create-example-utterances-for-each-intent"></a>Criar pronunciações exemplo para cada intenção

Para começar, evite criar demasiadas expressões para cada intenção. Uma vez determinados as intenções, crie 15 a 30 exemplos de pronunciações por intenção. Cada expressão deve ser diferente das declarações anteriormente fornecidas. Uma boa variedade em expressões incluem contagem geral de palavras, escolha de palavras, verbo tenso e [pontuação.](luis-reference-application-settings.md#punctuation-normalization)

Para mais informações, consulte a compreensão de [boas expressões para aplicações LUIS.](luis-concept-utterance.md)

## <a name="identify-your-entities"></a>Identifique as suas entidades

Nas declarações exemplo, identifique as entidades que pretende extrair. Para reservar um voo, você precisa de informações como o destino, data, companhia aérea, categoria de bilhetes e classe de viagem. Crie entidades para estes tipos de dados e, em seguida, marque as [entidades](luis-concept-entity-types.md) nas declarações de exemplo. As entidades são importantes para realizar uma intenção.

Ao determinar quais as entidades a utilizar na sua app, lembre-se que existem diferentes tipos de entidades para capturar relações entre tipos de objetos. [As entidades do LUIS](luis-concept-entity-types.md) fornecem mais detalhes sobre os diferentes tipos.

> [!TIP]
> A LUIS oferece [entidades pré-construídas](luis-prebuilt-entities.md) para cenários comuns e de utilizadores de conversação. Considere usar entidades pré-construídas como ponto de partida para o desenvolvimento da sua aplicação.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Aprender o desenvolvimento do LUIS lifecylce](luis-concept-app-iteration.md)

