---
title: Práticas recomendadas-LUIS
titleSuffix: Azure Cognitive Services
description: Conheça as práticas recomendadas do LUIS para obter os melhores resultados do modelo do seu aplicativo LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 64d67edaf5affbc908fba7b6c261096589bc84d0
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73487612"
---
# <a name="best-practices-for-building-a-language-understanding-app-with-cognitive-services"></a>Práticas recomendadas para a criação de um aplicativo de compreensão de linguagem com serviços cognitivas
Use o processo de criação de aplicativos para criar seu aplicativo LUIS: 

* Criar modelos de linguagem (intenções e entidades)
* Adicionar alguns exemplos de treinamento declarações (15-30 por tentativa)
* Publicar no ponto de extremidade
* Teste do ponto de extremidade 

Depois que seu aplicativo for [publicado](luis-how-to-publish-app.md), use o ciclo de vida de desenvolvimento para adicionar recursos, publicar e testar do ponto de extremidade. Não comece o próximo ciclo de criação adicionando mais declarações de exemplo porque isso não permite que o LUIS Aprenda seu modelo com declarações de usuário do mundo real. 

Não expanda o declarações até que o conjunto atual de declarações de exemplo e de ponto de extremidade esteja retornando pontuações de previsão alta de confiança. Melhore as pontuações usando o [aprendizado ativo](luis-concept-review-endpoint-utterances.md). 




## <a name="do-and-dont"></a>Fazer e não
A lista a seguir inclui as práticas recomendadas para aplicativos LUIS:

|O que deve fazer|O que não deve fazer|
|--|--|
|[Definir tentativas distintas](#do-define-distinct-intents)<br>[Adicionar descritores a tentativas](#do-add-descriptors-to-intents) |[Adicionar muitos exemplos de declarações a intenções](#dont-add-many-example-utterances-to-intents)<br>[Usar algumas ou entidades simples](#dont-use-few-or-simple-entities) |
|[Encontre um ponto forte entre muito genérico e muito específico para cada tentativa](#do-find-sweet-spot-for-intents)|[Usar o LUIS como uma plataforma de treinamento](#dont-use-luis-as-a-training-platform)|
|[Crie seu aplicativo iterativamente com versões](#do-build-your-app-iteratively-with-versions)<br>[Criar entidades para a decomposição do modelo](#do-build-for-model-decomposition)|[Adicionar vários exemplos de declarações do mesmo formato, ignorando outros formatos](#dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats)|
|[Adicionar padrões em iterações posteriores](#do-add-patterns-in-later-iterations)|[Misturar a definição de intenções e entidades](#dont-mix-the-definition-of-intents-and-entities)|
|[Equilibre sua declarações em todas as](#balance-your-utterances-across-all-intents) intenções, exceto a intenção de nenhum.<br>[Adicionar declarações de exemplo a intenção None](#do-add-example-utterances-to-none-intent)|[Criar descritores com todos os valores possíveis](#dont-create-descriptors-with-all-the-possible-values)|
|[Aproveite o recurso sugerir para o aprendizado ativo](#do-leverage-the-suggest-feature-for-active-learning)|[Adicionar muitos padrões](#dont-add-many-patterns)|
|[Monitorar o desempenho do seu aplicativo com testes em lotes](#do-monitor-the-performance-of-your-app)|[Treine e publique com cada exemplo de expressão adicionado](#dont-train-and-publish-with-every-single-example-utterance)|

## <a name="do-define-distinct-intents"></a>Definir tentativas distintas
Verifique se o vocabulário de cada tentativa é apenas para essa intenção e não se sobrepõe a uma intenção diferente. Por exemplo, se você quiser ter um aplicativo que lide com as organizações de viagem, como vôos de viagens aéreas e Hotéis, você pode optar por ter essas áreas de assunto como intenções separadas ou a mesma intenção com entidades para dados específicos dentro do expressão.

Se o vocabulário entre duas intenções for o mesmo, combine a intenção e use entidades. 

Considere o seguinte exemplo de declarações:

|Expressões de exemplo|
|--|
|Reservar um vôo|
|Reservar um hotel|

`Book a flight` e `Book a hotel` usar o mesmo vocabulário de `book a `. Esse formato é o mesmo, portanto, deve ser a mesma intenção com as diferentes palavras de `flight` e `hotel` como entidades extraídas. 

## <a name="do-add-descriptors-to-intents"></a>Adicionar descritores a tentativas

Os descritores ajudam a descrever os recursos para uma intenção. Um descritor pode ser uma lista de frases de palavras que são significativas para essa intenção ou uma entidade que é significativa para essa intenção. 

## <a name="do-find-sweet-spot-for-intents"></a>Encontre pontos de melhores intenções
Use dados de previsão do LUIS para determinar se suas intenções estão sobrepostas. Sobrepondo tentativas de confundir LUIS. O resultado é que a principal intenção de pontuação está muito próxima de outra intenção. Como o LUIS não usa exatamente o mesmo caminho pelos dados para treinamento a cada vez, uma intenção de sobreposição tem a chance de ser o primeiro ou o segundo no treinamento. Você quer que a pontuação de expressão para cada intenção fique mais distante, de modo que esse flip/flop não aconteça. Boa distinção para tentativas deve resultar na primeira tentativa esperada a cada vez. 
 
<a name="#do-build-the-app-iteratively"></a>

## <a name="do-build-your-app-iteratively-with-versions"></a>Compilar seu aplicativo iterativamente com versões

Cada ciclo de criação deve estar dentro de uma nova [versão](luis-concept-version.md), clonada de uma versão existente. 

## <a name="do-build-for-model-decomposition"></a>Fazer compilação para a decomposição do modelo

A decomposição do modelo tem um processo típico de:

* criar **intenção** com base nas intenções do usuário do aplicativo cliente
* Adicione o exemplo de 15-30 declarações com base na entrada do usuário do mundo real
* rotular o conceito de dados de nível superior no exemplo expressão
* dividir o conceito de dados em subcomponentes
* Adicionar descritores (recursos) a subcomponentes
* Adicionar descritores (recursos) à intenção 

Depois de criar a intenção e adicionar o exemplo declarações, o exemplo a seguir descreve a decomposição da entidade. 

Comece identificando os conceitos de dados completos que você deseja extrair em um expressão. Esta é sua entidade aprendida por computador. Em seguida, decompor a frase em suas partes. Isso inclui a identificação de subcomponentes (como entidades), juntamente com os descritores e restrições. 

Por exemplo, se você quiser extrair um endereço, a entidade aprendida por máquina superior poderá ser chamada `Address`. Ao criar o endereço, identifique alguns de seus subcomponentes, como endereço, cidade, estado e CEP. 

Continue decompondo esses elementos **restringindo** o CEP a uma expressão regular. Decompor o endereço em partes de um número de rua (usando um número predefinido), um nome de rua e um tipo de rua. O tipo de rua pode ser descrito com uma lista de **descritores** como Avenida, círculo, estrada e pista.

A API de criação v3 permite a decomposição do modelo. 

## <a name="do-add-patterns-in-later-iterations"></a>Adicionar padrões em iterações posteriores

Você deve entender como o aplicativo se comporta antes de adicionar [padrões](luis-concept-patterns.md) porque os padrões são ponderados de forma muito maior do que o exemplo declarações e distorcerá a confiança. 

Depois de entender como seu aplicativo se comporta, adicione padrões conforme eles se aplicam ao seu aplicativo. Você não precisa adicioná-los a cada [iteração](luis-concept-app-iteration.md). 

Não há danos para adicioná-los no início do design do modelo, mas é mais fácil ver como cada padrão altera o modelo depois que o modelo é testado com declarações. 
 
<!--

### Phrase lists

[Phrase lists](luis-concept-feature.md) allow you to define dictionaries of words related to your app domain. Seed your phrase list with a few words then use the suggest feature so LUIS knows about more words in the vocabulary specific to your app. A Phrase List improves intent detection and entity classification by boosting the signal associated with words or phrases that are significant to your app. 

Don't add every word to the vocabulary since the phrase list isn't an exact match. 

For more information:
* Concept: [Phrase list features in your LUIS app](luis-concept-feature.md)
* How-to: [Use phrase lists to boost signal of word list](luis-how-to-add-features.md)



### Patterns

Real user utterances from the endpoint, very similar to each other, may reveal patterns of word choice and placement. The [pattern](luis-concept-patterns.md) feature takes this word choice and placement along with regular expressions to improve your prediction accuracy. A regular expression in the pattern allows for words and punctuation you intend to ignore while still matching the pattern. 

Use pattern's [optional syntax](luis-concept-patterns.md) for punctuation so punctuation can be ignored. Use the [explicit list](luis-concept-patterns.md#explicit-lists) to compensate for pattern.any syntax issues. 

For more information:
* Concept: [Patterns improve prediction accuracy](luis-concept-patterns.md)
* How-to: [How to add Patterns to improve prediction accuracy](luis-how-to-model-intent-pattern.md)
-->

<a name="balance-your-utterances-across-all-intents"></a>

## <a name="do-balance-your-utterances-across-all-intents"></a>Equilibre seu declarações em todas as intenções

Para que as previsões de LUIS sejam precisas, a quantidade de declarações de exemplo em cada tentativa (exceto para a intenção de nenhum) deve ser relativamente igual. 

Se você tiver uma intenção com 100 de exemplo declarações e uma intenção com 20 exemplos de declarações, a intenção de 100-expressão terá uma taxa mais alta de previsão.  

## <a name="do-add-example-utterances-to-none-intent"></a>Adicionar exemplo declarações a nenhuma intenção

Essa é a intenção de fallback, indicando tudo fora do seu aplicativo. Adicione um exemplo expressão à intenção None para cada 10 exemplos de declarações no restante do seu aplicativo LUIS.

## <a name="do-leverage-the-suggest-feature-for-active-learning"></a>Aproveite o recurso sugerir para o aprendizado ativo

Use o declarações de **ponto de extremidade** de [aprendizado ativo](luis-how-to-review-endpoint-utterances.md)regularmente, em vez de adicionar mais declarações de exemplo a intenções. Como o aplicativo está constantemente recebendo o ponto de extremidade declarações, essa lista está crescendo e mudando.

## <a name="do-monitor-the-performance-of-your-app"></a>Monitore o desempenho do seu aplicativo

Monitore a precisão da previsão usando um conjunto de [teste em lotes](luis-concept-batch-test.md) . 

Mantenha um conjunto separado de declarações que não são usados como [exemplo declarações](luis-concept-utterance.md) ou Endpoint declarações. Continue melhorando o aplicativo para seu conjunto de teste. Adapte o conjunto de testes para refletir declarações de usuário reais. Use este conjunto de testes para avaliar cada iteração ou versão do aplicativo. 

## <a name="dont-add-many-example-utterances-to-intents"></a>Não adicione muitos exemplos de declarações a intenções

Depois que o aplicativo for publicado, adicione apenas declarações do aprendizado ativo no processo de ciclo de vida de desenvolvimento. Se declarações forem muito semelhantes, adicione um padrão. 

## <a name="dont-use-few-or-simple-entities"></a>Não use algumas ou entidades simples

As entidades são criadas para extração e previsão de dados. É importante que cada tentativa tenha entidades aprendidas por máquina que descrevam os dados na intenção. Isso ajuda a LUIS a prever a intenção, mesmo que o aplicativo cliente não precise usar a entidade extraída. 

## <a name="dont-use-luis-as-a-training-platform"></a>Não use o LUIS como uma plataforma de treinamento

LUIS é específico para o domínio de um modelo de linguagem. Ele não se destina a funcionar como uma plataforma de treinamento em linguagem natural geral. 

## <a name="dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats"></a>Não adicione muitos exemplos de declarações do mesmo formato, ignorando outros formatos

LUIS espera variações no declarações de uma intenção. O declarações pode variar enquanto tem o mesmo significado geral. As variações podem incluir comprimento de expressão, opção de palavra e posicionamento de palavras. 

|Não usar o mesmo formato|Usar formato variável|
|--|--|
|Comprar um tíquete para Seattle<br>Comprar um tíquete para Paris<br>Comprar um tíquete para Orlando|Comprar um tíquete para Seattle<br>Reserve duas estações no olho vermelho para Paris na próxima segunda-feira<br>Eu gostaria de livro 3 tickets para Orlando para Spring Break|

A segunda coluna usa verbos diferentes (comprar, reservar, Book), quantidades diferentes (1, duas, 3) e diferentes disposições de palavras, mas todas têm a mesma intenção de comprar tíquetes de companhia aérea para viagem. 

## <a name="dont-mix-the-definition-of-intents-and-entities"></a>Não misture a definição de intenções e entidades

Crie uma intenção para qualquer ação que seu bot executar. Use entidades como parâmetros que tornam essa ação possível. 

Para um bot que irá reservar vôos de companhia aérea, crie uma intenção **BookFlight** . Não crie uma intenção para cada companhia aérea ou todos os destinos. Use essas partes de dados como [entidades](luis-concept-entity-types.md) e marque-as no exemplo declarações. 

## <a name="dont-create-descriptors-with-all-the-possible-values"></a>Não crie descritores com todos os valores possíveis

Forneça alguns exemplos nas [listas de frases](luis-concept-feature.md) de descritor, mas não todas as palavras. O LUIS generaliza e leva o contexto em conta. 

## <a name="dont-add-many-patterns"></a>Não adicione muitos padrões

Não adicione muitos [padrões](luis-concept-patterns.md). O LUIS destina-se a aprender rapidamente com menos exemplos. Não sobrecarregar o sistema desnecessariamente.

## <a name="dont-train-and-publish-with-every-single-example-utterance"></a>Não treine e publique com cada um dos exemplos expressão

Adicione 10 ou 15 declarações antes de treinar e publicar. Isso permite que você veja o impacto na precisão da previsão. Adicionar um único expressão pode não ter um impacto visível na pontuação. 

## <a name="next-steps"></a>Passos seguintes

* Saiba como [planejar seu aplicativo](luis-how-plan-your-app.md) em seu aplicativo Luis.
