---
title: Práticas recomendadas para criar seu aplicativo LUIS
titleSuffix: Azure Cognitive Services
description: Conheça as práticas recomendadas para obter os melhores resultados do modelo do aplicativo LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: b4be79338db71ad83204fae971da0b77885a8070
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280916"
---
# <a name="best-practices-for-building-a-language-understanding-luis-app"></a>Práticas recomendadas para a criação de um aplicativo de reconhecimento de linguagem (LUIS)
Use o processo de criação de aplicativos para criar seu aplicativo LUIS: 

* Criar modelos de linguagem (intenções e entidades)
* Adicionar alguns exemplos de treinamento declarações (15-30 por tentativa)
* Publicar no ponto de extremidade
* Teste a partir de ponto final 

Depois que seu aplicativo for [publicado](luis-how-to-publish-app.md), use o ciclo de vida de desenvolvimento para adicionar recursos, publicar e testar do ponto de extremidade. Não comece o próximo ciclo de criação adicionando mais declarações de exemplo porque isso não permite que o LUIS Aprenda seu modelo com declarações de usuário do mundo real. 

Não expanda o declarações até que o conjunto atual de declarações de exemplo e de ponto de extremidade esteja retornando pontuações de previsão alta de confiança. Melhore as pontuações usando o [aprendizado ativo](luis-concept-review-endpoint-utterances.md). 




## <a name="do-and-dont"></a>Fazer e não
A lista seguinte inclui as melhores práticas para aplicações de LUIS:

|O que deve fazer|O que não deve fazer|
|--|--|
|[Definir objetivos distintos](#do-define-distinct-intents)<br>[Adicionar descritores a tentativas](#do-add-descriptors-to-intents) |[Adicionar muitas expressões de exemplo para intenções](#dont-add-many-example-utterances-to-intents)<br>[Usar algumas ou entidades simples](#dont-use-few-or-simple-entities) |
|[Encontrar um encontramos uma boa entre demasiado genérico e muito específico para cada intenção](#do-find-sweet-spot-for-intents)|[Utilizar o LUIS como uma plataforma de treinamento](#dont-use-luis-as-a-training-platform)|
|[Crie seu aplicativo iterativamente com versões](#do-build-your-app-iteratively-with-versions)<br>[Criar entidades para a decomposição do modelo](#do-build-for-model-decomposition)|[Adicionar muitas expressões de exemplo do formato do mesmo, a ignorar a outros formatos](#dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats)|
|[Adicionar padrões em iterações posteriores](#do-add-patterns-in-later-iterations)|[Mistura a definição de objetivos e entidades](#dont-mix-the-definition-of-intents-and-entities)|
|[Equilibre sua declarações em todas as](#balance-your-utterances-across-all-intents) intenções, exceto a intenção de nenhum.<br>[Adicionar expressões de exemplo para nenhum intenção](#do-add-example-utterances-to-none-intent)|[Criar descritores com todos os valores possíveis](#dont-create-descriptors-with-all-the-possible-values)|
|[Tirar partido da funcionalidade de sugerido para aprendizagem ativa](#do-leverage-the-suggest-feature-for-active-learning)|[Adicionar muitos padrões](#dont-add-many-patterns)|
|[Monitorar o desempenho do seu aplicativo com testes em lotes](#do-monitor-the-performance-of-your-app)|[Formar e publicar com cada expressão única de exemplo adicionado](#dont-train-and-publish-with-every-single-example-utterance)|

## <a name="do-define-distinct-intents"></a>Definir objetivos distintos
Certifique-se do que vocabulário para cada intenção é apenas para esse propósito e não sobrepostos com um objetivo diferente. Por exemplo, se você quiser ter um aplicativo que lide com as organizações de viagem, como vôos de viagens aéreas e Hotéis, você pode optar por ter essas áreas de assunto como intenções separadas ou a mesma intenção com entidades para dados específicos dentro do expressão.

Se o vocabulário entre dois objetivos é o mesmo, combinar a intenção e utilizar entidades. 

Considere as expressões de exemplo seguinte:

|Expressões de exemplo|
|--|
|Programar um vôo|
|Reservar um hotel|

`Book a flight` e `Book a hotel` usar o mesmo vocabulário de `book a `. Esse formato é o mesmo, portanto, deve ser a mesma intenção com as diferentes palavras de `flight` e `hotel` como entidades extraídas. 

## <a name="do-add-descriptors-to-intents"></a>Adicionar descritores a tentativas

Os descritores ajudam a descrever os recursos para uma intenção. Um descritor pode ser uma lista de frases de palavras que são significativas para essa intenção ou uma entidade que é significativa para essa intenção. 

## <a name="do-find-sweet-spot-for-intents"></a>Encontrar encontramos uma boa para intenções
Utilize dados de predição do LUIS para determinar se seus objetivos estão sobrepostos. Sobrepondo tentativas de confundir LUIS. O resultado é que a parte superior a intenção de classificação é demasiado fechar para outro objetivo. Como o LUIS não utiliza o mesmo caminho exato através dos dados para cada hora de formação, um objetivo sobreposto tem uma chance de ser o primeiro ou segundo no treinamento. Você quer que a pontuação de expressão para cada intenção fique mais distante, de modo que esse flip/flop não aconteça. Boa distinção para objetivos deve resultar na intenção de principal esperada cada vez. 
 
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

## <a name="do-add-example-utterances-to-none-intent"></a>A adicionar expressões de exemplo para nenhum intenção

Essa é a intenção de fallback, indicando tudo fora do seu aplicativo. Adicione uma expressão de exemplo para a intenção de todas as expressões de 10 exemplo no resto da sua aplicação LUIS nenhum.

## <a name="do-leverage-the-suggest-feature-for-active-learning"></a>A tirar partido da funcionalidade de sugerido para aprendizagem ativa

Uso [aprendizagem ativa](luis-how-to-review-endpoint-utterances.md)da **rever expressões de ponto final** regularmente, em vez de adicionar mais expressões de exemplo para objetivos. Uma vez que a aplicação está a receber constantemente expressões de ponto final, esta lista está crescendo e alterar.

## <a name="do-monitor-the-performance-of-your-app"></a>Monitorizar o desempenho da sua aplicação

Monitore a precisão da previsão usando um conjunto de [teste em lotes](luis-concept-batch-test.md) . 

Mantenha um conjunto separado de declarações que não são usados como [exemplo declarações](luis-concept-utterance.md) ou Endpoint declarações. Continuam a melhorar a aplicação para o seu conjunto de teste. Adapte o teste definido para refletir as expressões de utilizador real. Use este conjunto de testes para avaliar cada iteração ou versão do aplicativo. 

## <a name="dont-add-many-example-utterances-to-intents"></a>Não adicionar muitas expressões de exemplo para intenções

Depois que o aplicativo for publicado, adicione apenas declarações do aprendizado ativo no processo de ciclo de vida de desenvolvimento. Se as expressões são muito semelhantes, adicione um padrão. 

## <a name="dont-use-few-or-simple-entities"></a>Não use algumas ou entidades simples

As entidades são criadas para extração e previsão de dados. É importante que cada tentativa tenha entidades aprendidas por máquina que descrevam os dados na intenção. Isso ajuda a LUIS a prever a intenção, mesmo que o aplicativo cliente não precise usar a entidade extraída. 

## <a name="dont-use-luis-as-a-training-platform"></a>Não utilizar o LUIS como uma plataforma de treinamento

LUIS é específico do domínio de um modelo de idioma. Ele não se destina a funcionar como uma plataforma de treinamento em linguagem natural geral. 

## <a name="dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats"></a>Não adicionar muitas expressões de exemplo do formato do mesmo, a ignorar a outros formatos

LUIS espera variações em expressões de com um objetivo. As expressões podem variar enquanto tem o mesmo significado geral. Variações podem incluir o comprimento da expressão, escolha do word e colocação do word. 

|Não utilize o mesmo formato|Utilize o formato variado|
|--|--|
|Comprar um pedido de suporte a Seattle<br>Comprar um pedido para Paris<br>Comprar um pedido para Orlando|Comprar 1 pedido de suporte a Seattle<br>Reservar dois lugares nos olhos vermelho para Paris seguinte segunda-feira<br>Eu gostaria de reservar 3 pedidos de suporte para Orlando de quebra de spring|

A segunda coluna utiliza diferentes verbos (comprar, reserva, livro), quantidades diferentes (1, dois, 3), e disposições diferentes palavras, mas todos têm a mesma intenção de compra de bilhetes de companhia aérea para viagem. 

## <a name="dont-mix-the-definition-of-intents-and-entities"></a>Não misture a definição de objetivos e entidades

Crie um objetivo para qualquer ação do bot irá demorar. Utilize entidades como parâmetros que tornam essa ação possível. 

Para um bot que irá reservar vôos de companhia aérea, crie uma intenção **BookFlight** . Não crie um objetivo para cada companhia aérea ou de cada destino. Utilizar essas partes de dados como [entidades](luis-concept-entity-types.md) e marcá-los nas expressões de exemplo. 

## <a name="dont-create-descriptors-with-all-the-possible-values"></a>Não crie descritores com todos os valores possíveis

Forneça alguns exemplos nas [listas de frases](luis-concept-feature.md) de descritor, mas não todas as palavras. LUIS generaliza e tem o contexto em consideração. 

## <a name="dont-add-many-patterns"></a>Não adicione muitos padrões

Não adicione demasiados [padrões](luis-concept-patterns.md). LUIS destina-se para saber rapidamente com menos de exemplos. Não sobrecarregar o sistema desnecessariamente.

## <a name="dont-train-and-publish-with-every-single-example-utterance"></a>Não formar e publicar com cada expressão única de exemplo

Adicione expressões de 10 ou 15 antes de treinamento e publicação. Que pode ver o impacto na precisão de previsão. Adicionar uma única expressão pode não ter um impacto visível na classificação. 

## <a name="next-steps"></a>Passos seguintes

* Saiba como [planear a sua aplicação](luis-how-plan-your-app.md) na sua aplicação LUIS.
