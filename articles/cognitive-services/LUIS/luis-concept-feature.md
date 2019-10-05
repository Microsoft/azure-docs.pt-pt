---
title: Recursos-LUIS
titleSuffix: Azure Cognitive Services
description: Adicione recursos para um modelo de idioma para fornecer sugestões sobre como a reconhecer a entrada que deseja identificar ou classificar.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: dab4b4c6f41a95623a40e5d3fd859f9613afac27
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/04/2019
ms.locfileid: "71949603"
---
# <a name="phrase-list-features-in-your-luis-app"></a>Recursos da lista de frases em seu aplicativo LUIS

No machine learning, um *funcionalidade* é uma distinção característica ou atributo de dados que observa o seu sistema. 

Adicione recursos para um modelo de idioma para fornecer sugestões sobre como a reconhecer a entrada que deseja identificar ou classificar. Recursos ajudam o LUIS reconhecer intenções e entidades, mas as funcionalidades não são objetivos ou as entidades em si. Em vez disso, recursos podem fornecer exemplos de termos relacionados.  

## <a name="what-is-a-phrase-list-feature"></a>O que é uma funcionalidade de lista de frase?
Uma lista de frases é uma lista de palavras ou frases que são significativas para seu aplicativo, mais do que outras palavras no declarações. Uma lista de frases adiciona ao vocabulário do domínio do aplicativo como um sinal adicional para LUIS sobre essas palavras. O que LUIS aprende sobre uma delas é aplicada automaticamente para os outros também. Esta lista não é uma [entidade de lista](luis-concept-entity-types.md#types-of-entities) fechada de correspondências exatas de texto.

As listas de frases não ajudam na lematização, portanto, você precisa adicionar exemplos de expressão que usam uma variedade de lematização para quaisquer palavras e frases de vocabulário significativas.

## <a name="phrase-lists-help-all-models"></a>A frase lista ajuda todos os modelos

As listas de frases não são vinculadas a uma entidade ou intenção específica, mas são adicionadas como um aumento significativo para todas as intenções e entidades. Seu objetivo é melhorar a detecção de intenção e a classificação de entidade.

## <a name="how-to-use-phrase-lists"></a>Como utilizar listas de frase

[Crie uma lista de frases](luis-how-to-add-features.md) quando seu aplicativo tiver palavras ou frases que são importantes para o aplicativo, como:

* termos do setor
* gírias
* abreviações
* idioma específico da empresa
* linguagem que é de outro idioma, mas frequentemente usada em seu aplicativo
* Palavras-chave e frases em seu exemplo declarações

Depois de inserir algumas palavras ou frases, use o recurso **recomendado** para localizar valores relacionados. Revise os valores relacionados antes de adicioná-los aos valores da lista de frases.

Uma lista de frases é para valores que são sinônimos. Por exemplo, se você quiser todos os corpos de água encontrados e tiver exemplo declarações como: 

* Quais cidades estão próximas aos grandes lagos? 
* Qual estrada é executada junto com o Lake Havasu?
* Onde o Nilo começa e termina? 

Cada expressão deve ser determinado para a intenção e as entidades, independentemente do corpo da água: 

* Quais cidades estão perto de [bodyOfWater]?
* Qual estrada é executada junto com [bodyOfWater]?
* Onde o [bodyOfWater] inicia e termina? 

Porque as palavras ou frases do corpo da água são sinônimos e podem ser usadas de forma intercambiável no declarações. 

<a name="phrase-lists-help-identify-simple-exchangeable-entities"></a>

## <a name="phrase-lists-help-identify-simple-interchangeable-entities"></a>Listas de frases ajudam a identificar entidades intercambiáveis simples
Frase intercambiáveis listas são uma boa maneira de otimizar o desempenho da sua aplicação LUIS. Se a sua aplicação tiver problemas ao prever expressões com a intenção correta, ou no reconhecimento de entidades, pense se as expressões contenham palavras invulgares ou palavras que podem ser ambíguas em significado. Essas palavras são bons candidatos para incluir numa lista frase.

## <a name="phrase-lists-help-identify-intents-by-better-understanding-context"></a>Apresenta uma lista de frase ajuda identificar intenções ao contexto de compreensão melhor
Uma lista de expressão não é uma instrução para o LUIS para efetuar a correspondência strict ou etiqueta sempre o todos os termos da lista de frase exatamente da mesma. É simplesmente uma dica. Por exemplo, poderia ter uma lista de expressão que indica "Patti" e "Selma" são nomes, mas LUIS ainda pode usar informações contextuais para reconhecer o que significa algo diferente de "Tornar uma reserva para 2 no cliente de Patti para o jantar" e "encontrar me orientar instruções para Selma, Geórgia". 

Adicionar uma lista de frase é uma alternativa à adição de mais expressões de exemplo para um objetivo. 

## <a name="when-to-use-phrase-lists-versus-list-entities"></a>Quando utilizar listas de frase em relação a entidades de lista
Embora uma lista de frases e as [entidades de lista](reference-entity-list.md) possam afetar o declarações em todas as intenções, cada uma faz isso de uma maneira diferente. Utilize uma lista de frase para afetar a pontuação de predição de intenção. Utilize uma entidade de lista para afetar a extração de entidades para obter uma correspondência exata de texto. 

### <a name="use-a-phrase-list"></a>Utilize uma lista de frase
Com uma lista de frase, LUIS ainda pode levar o contexto em conta e generalizar a identificar itens que são semelhantes, mas não é uma correspondência exata, como itens numa lista. Se precisar da aplicação do LUIS para poder generalizar e identificar novos itens numa categoria, utilize uma lista de frase. 

Quando você quiser ser capaz de reconhecer novas instâncias de uma entidade, como um Agendador de reunião que deve reconhecer os nomes de novos contatos ou um aplicativo de inventário que deve reconhecer novos produtos, use outro tipo de entidade aprendida por máquina, como uma entidade simples. Em seguida, crie uma lista de frase de palavras e expressões que o ajuda a encontrar outras palavras semelhante para a entidade de LUIS. Esta lista orienta o LUIS para reconhecer exemplos da entidade através da adição de significância adicional para o valor dessas palavras. 

Listas de frase são como vocabulário específicas de domínio que o ajudam a com a melhorar a qualidade da compreensão dos objetivos e entidades. Um uso comum de uma lista de frase é nomes próprios, como os nomes das cidades. Um nome de cidade pode ter várias palavras, incluindo hífenes ou apóstrofos.
 
### <a name="dont-use-a-phrase-list"></a>Não utilize uma lista de frase 
Uma entidade de lista define explicitamente a cada valor de uma entidade pode demorar e apenas identifica os valores que correspondem exatamente. Uma entidade de lista pode ser adequada para uma aplicação em que todas as instâncias de uma entidade são conhecidas e não são alterados frequentemente. Os exemplos são itens de comida num menu de restaurante alterado com pouca frequência. Se precisar de uma correspondência exata do texto de uma entidade, não utilize uma lista de frase. 

## <a name="best-practices"></a>Melhores práticas
Saiba mais [melhores práticas](luis-concept-best-practices.md).

## <a name="next-steps"></a>Passos Seguintes

Ver [adicionar funcionalidades](luis-how-to-add-features.md) para saber mais sobre como adicionar funcionalidades à sua aplicação LUIS.
