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
ms.date: 04/01/2019
ms.author: diberry
ms.openlocfilehash: 1aed6f9a0ceec18ca800e5030ec09bbb8d98cb76
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68560731"
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

Crie uma lista de frases quando seu aplicativo tiver palavras ou frases que são importantes para o aplicativo, como:

* termos do setor
* gírias
* abreviações
* idioma específico da empresa
* linguagem que é de outro idioma, mas frequentemente usada em seu aplicativo
* Palavras-chave e frases em seu exemplo declarações

Depois de inserir algumas palavras ou frases, use o recurso **recomendado** para localizar valores relacionados. Revise os valores relacionados antes de adicioná-los aos valores da lista de frases.

|Tipo de lista|Objetivo|
|--|--|
|Intercambiáveis|Sinônimos ou palavras que, quando alterados para outra palavra na lista, têm a mesma intenção e extração de entidade.|
|Não intercambiável|Vocabulário do aplicativo, específico ao seu aplicativo, mais do que geralmente outras palavras nesse idioma.|

### <a name="interchangeable-lists"></a>Listas intercambiáveis

Uma  lista de frases intercambiáveis é para valores que são sinônimos. Por exemplo, se você quiser todos os corpos de água encontrados e tiver exemplo declarações como: 

* Quais cidades estão próximas aos grandes lagos? 
* Qual estrada é executada junto com o Lake Havasu?
* Onde o Nilo começa e termina? 

Cada expressão deve ser determinado para a intenção e as entidades, independentemente do corpo da água: 

* Quais cidades estão perto de [bodyOfWater]?
* Qual estrada é executada junto com [bodyOfWater]?
* Onde o [bodyOfWater] inicia e termina? 

Como as palavras ou frases do corpo de água são sinônimos e podem ser usadas de forma intercambiável no declarações, use a configuração **intercambiável** na lista de frases. 

### <a name="non-interchangeable-lists"></a>Listas não intercambiáveis

Uma lista de frases não intercambiáveis é um sinal que aumenta a detecção para LUIS. A lista de frases indica palavras ou frases que são mais significativas que outras palavras. Isso ajuda a determinar a detecção de intenção e entidade. Por exemplo, digamos que você tenha um domínio de assunto como viagem que seja global (o que significa entre culturas, mas ainda em uma única linguagem). Há palavras e frases que são importantes para o aplicativo, mas não são sinônimos. 

Para outro exemplo, use uma lista de frases não intercambiáveis para palavras raras, proprietárias e estrangeiras. LUIS pode não ser possível reconhecer palavras raras e proprietárias, como palavras estrangeiras (fora da cultura da aplicação). A definição não intercambiáveis indica que o conjunto de palavras raro constitui uma classe que deve aprender a reconhecer o LUIS, mas não são sinónimos ou intercambiáveis entre si.

Não adicione cada palavra ou frase possível a uma lista de frases, adicione algumas palavras ou frases de cada vez e, em seguida, retreine e publique. 

À medida que a lista de frases cresce ao longo do tempo, você pode achar que alguns termos têm muitas formas (sinônimos). Divida-os em outra lista de frases que seja intercambiável. 

<a name="phrase-lists-help-identify-simple-exchangeable-entities"></a>

## <a name="phrase-lists-help-identify-simple-interchangeable-entities"></a>Apresenta uma lista de frase ajuda a identificar as entidades intercambiáveis simples
Frase intercambiáveis listas são uma boa maneira de otimizar o desempenho da sua aplicação LUIS. Se a sua aplicação tiver problemas ao prever expressões com a intenção correta, ou no reconhecimento de entidades, pense se as expressões contenham palavras invulgares ou palavras que podem ser ambíguas em significado. Essas palavras são bons candidatos para incluir numa lista frase.

## <a name="phrase-lists-help-identify-intents-by-better-understanding-context"></a>Apresenta uma lista de frase ajuda identificar intenções ao contexto de compreensão melhor
Uma lista de expressão não é uma instrução para o LUIS para efetuar a correspondência strict ou etiqueta sempre o todos os termos da lista de frase exatamente da mesma. É simplesmente uma dica. Por exemplo, poderia ter uma lista de expressão que indica "Patti" e "Selma" são nomes, mas LUIS ainda pode usar informações contextuais para reconhecer o que significa algo diferente de "Tornar uma reserva para 2 no cliente de Patti para o jantar" e "encontrar me orientar instruções para Selma, Geórgia". 

Adicionar uma lista de frase é uma alternativa à adição de mais expressões de exemplo para um objetivo. 

## <a name="when-to-use-phrase-lists-versus-list-entities"></a>Quando utilizar listas de frase em relação a entidades de lista
Embora uma lista de frase e entidades de lista podem afetar os discursos em todas as intenções, cada um faz isso de forma diferente. Utilize uma lista de frase para afetar a pontuação de predição de intenção. Utilize uma entidade de lista para afetar a extração de entidades para obter uma correspondência exata de texto. 

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
