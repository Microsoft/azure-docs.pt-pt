---
title: Funcionalidades
titleSuffix: Language Understanding - Azure Cognitive Services
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
ms.openlocfilehash: 7889f223b607912fd88c798b31ec028f97dfbbd6
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/12/2019
ms.locfileid: "59522858"
---
# <a name="phrase-list-features-in-your-luis-app"></a>Funcionalidades de lista de frase na sua aplicação LUIS

No machine learning, um *funcionalidade* é uma distinção característica ou atributo de dados que observa o seu sistema. 

Adicione recursos para um modelo de idioma para fornecer sugestões sobre como a reconhecer a entrada que deseja identificar ou classificar. Recursos ajudam o LUIS reconhecer intenções e entidades, mas as funcionalidades não são objetivos ou as entidades em si. Em vez disso, recursos podem fornecer exemplos de termos relacionados.  

## <a name="what-is-a-phrase-list-feature"></a>O que é uma funcionalidade de lista de frase?
Uma lista de frase é uma lista de palavras ou frases que são relevantes para a sua aplicação, assim que mais do que outros palavras em expressões. Uma lista de frase adiciona ao vocabulário do domínio de aplicativo como um sinal adicional para LUIS sobre essas palavras. O que LUIS aprende sobre uma delas é aplicada automaticamente para os outros também. Esta lista não é um fechada [listar entidade](luis-concept-entity-types.md#types-of-entities) de texto exato corresponde.

Listas de expressão não ajudar na Lematização por isso terá de adicionar exemplos de expressão que utilizam uma variedade de Lematização para quaisquer palavras de vocabulário significativas e expressões.

## <a name="phrase-lists-help-all-models"></a>Todos os modelos de ajudar a frase listas

Listas de expressão não estão ligadas a um objetivo específico ou uma entidade, mas são adicionadas como um aumento significativo para todas as intenções e entidades. Seu objetivo é melhorar a classificação de deteção e de entidade intencional.

## <a name="how-to-use-phrase-lists"></a>Como utilizar listas de frase

Crie uma lista de frase quando a aplicação tem palavras ou frases que são importantes para a aplicação, tais como:

* termos da indústria
* gíria
* abreviações
* linguagem específica da empresa
* idioma que é a partir de outro idioma, mas frequentemente utilizados na sua aplicação
* palavras-chave e expressões no seu expressões de exemplo

Depois de introduzir algumas palavras ou frases, utilize o **Recomendamos** funcionalidade para encontrar valores relacionados. Reveja os valores relacionados antes de adicionar a seus valores de lista de frase.

|Tipo de lista|Objetivo|
|--|--|
|Intercambiáveis|Sinónimos ou palavras que, quando alterado para outra palavra na lista, têm o mesmo objetivo e extração de entidades.|
|Não intercambiáveis|Vocabulário de aplicação, específico para a sua aplicação, mais, para que, geralmente, outro palavras nesse idioma.|

### <a name="interchangeable-lists"></a>Listas intercambiáveis

Uma *intercambiáveis* lista frase é para os valores que são sinónimos. Por exemplo, se pretender que todos os corpos de água encontrado e tem tal como expressões de exemplo: 

* O que cidades estiverem prestes as Lakes ótimo? 
* Que viagem é executada na parte Lake Havasu?
* Onde o Nilo iniciar e terminar? 

Cada ocorrência de pronunciação deve ser determinada para intenções e entidades, independentemente do corpo de água: 

* O que cidades estão próximos [bodyOfWater]?
* Que viagem é executada na parte [bodyOfWater]?
* Em que [bodyOfWater] iniciar e terminar? 

Uma vez que as palavras ou frases para o corpo de água são sinônimos e podem ser utilizados alternadamente as expressões, utilize o **Interchangeable** definição na lista de frase. 

### <a name="non-interchangeable-lists"></a>Listas não intercambiáveis

Uma lista de expressão não intercambiáveis é um sinal de que aumenta a detecção de LUIS. A lista de frase indica palavras ou frases que são mais significativas que outros palavras. Isto ajuda a com ambos os detecção de intenção e entidade determinante. Por exemplo, digamos que tenha um domínio de assunto como viagens é global (isto é, entre culturas, mas ainda num único idioma). Existem palavras e expressões que são importantes para a aplicação, mas não são sinônimos. 

Por outro exemplo, utilize uma lista de expressão não intercambiáveis palavras raras, proprietárias e externa. LUIS pode não ser possível reconhecer palavras raras e proprietárias, como palavras estrangeiras (fora da cultura da aplicação). A definição não intercambiáveis indica que o conjunto de palavras raro constitui uma classe que deve aprender a reconhecer o LUIS, mas não são sinónimos ou intercambiáveis entre si.

Não adicione todos os possíveis palavra ou frase a uma lista de frase, adicionar algumas palavras ou frases de cada vez, em seguida, voltar a preparar e publicar. 

À medida que aumenta a lista de frase ao longo do tempo, pode encontrar alguns termos têm muitas formas (sinónimos). Divida estas-se em outra lista de frase é intercambiável. 

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

Quando quer ser capaz de reconhecer novas instâncias de uma entidade, como um agendador de reunião deve reconhecer os nomes dos novos contactos ou uma aplicação de inventário que deve reconhecer a novos produtos, utilize outro tipo de entidade aprendidas por máquina, como uma entidade. Em seguida, crie uma lista de frase de palavras e expressões que o ajuda a encontrar outras palavras semelhante para a entidade de LUIS. Esta lista orienta o LUIS para reconhecer exemplos da entidade através da adição de significância adicional para o valor dessas palavras. 

Listas de frase são como vocabulário específicas de domínio que o ajudam a com a melhorar a qualidade da compreensão dos objetivos e entidades. Um uso comum de uma lista de frase é nomes próprios, como os nomes das cidades. Um nome de cidade pode ter várias palavras, incluindo hífenes ou apóstrofos.
 
### <a name="dont-use-a-phrase-list"></a>Não utilize uma lista de frase 
Uma entidade de lista define explicitamente a cada valor de uma entidade pode demorar e apenas identifica os valores que correspondem exatamente. Uma entidade de lista pode ser adequada para uma aplicação em que todas as instâncias de uma entidade são conhecidas e não são alterados frequentemente. Os exemplos são itens de comida num menu de restaurante alterado com pouca frequência. Se precisar de uma correspondência exata do texto de uma entidade, não utilize uma lista de frase. 

## <a name="best-practices"></a>Melhores práticas
Saiba mais [melhores práticas](luis-concept-best-practices.md).

## <a name="next-steps"></a>Passos Seguintes

Ver [adicionar funcionalidades](luis-how-to-add-features.md) para saber mais sobre como adicionar funcionalidades à sua aplicação LUIS.
