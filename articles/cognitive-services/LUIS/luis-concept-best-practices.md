---
title: Boas práticas para construir a sua app LUIS
titleSuffix: Azure Cognitive Services
description: Aprenda as melhores práticas para obter os melhores resultados do modelo da sua app LUIS.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "74280916"
---
# <a name="best-practices-for-building-a-language-understanding-luis-app"></a>Boas práticas para a construção de uma app de compreensão linguística (LUIS)
Utilize o processo de autoria da aplicação para construir a sua app LUIS: 

* Construir modelos linguísticos (intenções e entidades)
* Adicione algumas declarações de exemplo de treino (15-30 por intenção)
* Publicar para endpoint
* Teste a partir do ponto final 

Assim que a sua aplicação for [publicada,](luis-how-to-publish-app.md)utilize o ciclo de vida de desenvolvimento para adicionar funcionalidades, publicar e testar a partir do ponto final. Não inicie o próximo ciclo de autoria adicionando mais declarações de exemplo porque isso não permite que luis aprenda o seu modelo com declarações de utilizadores do mundo real. 

Não expanda as expressões até que o conjunto atual de ambos os exemplos e as expressões de pontofinal estejam a voltar confiantes e altas pontuações de previsão. Melhorar as pontuações utilizando [a aprendizagem ativa.](luis-concept-review-endpoint-utterances.md) 




## <a name="do-and-dont"></a>Fazer e Não
A lista seguinte inclui as melhores práticas para apps LUIS:

|O que deve fazer|O que não deve fazer|
|--|--|
|[Definir intenções distintas](#do-define-distinct-intents)<br>[Adicione descritores às intenções](#do-add-descriptors-to-intents) |[Adicione muitas declarações de exemplo às intenções](#dont-add-many-example-utterances-to-intents)<br>[Utilizar poucas ou entidades simples](#dont-use-few-or-simple-entities) |
|[Encontre um ponto doce entre genérico sinuoso e muito específico para cada intenção](#do-find-sweet-spot-for-intents)|[Use o LUIS como plataforma de formação](#dont-use-luis-as-a-training-platform)|
|[Construa a sua aplicação iterativamente com versões](#do-build-your-app-iteratively-with-versions)<br>[Construir entidades para decomposição de modelos](#do-build-for-model-decomposition)|[Adicione muitos exemplos do mesmo formato, ignorando outros formatos](#dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats)|
|[Adicione padrões em iterações posteriores](#do-add-patterns-in-later-iterations)|[Misture a definição de intenções e entidades](#dont-mix-the-definition-of-intents-and-entities)|
|[Equilibre as suas declarações em todas as intenções,](#balance-your-utterances-across-all-intents) exceto a intenção de "Nenhum".<br>[Adicione declarações exemplo sem intenção](#do-add-example-utterances-to-none-intent)|[Criar descritores com todos os valores possíveis](#dont-create-descriptors-with-all-the-possible-values)|
|[Aproveite a funcionalidade de sugestão para a aprendizagem ativa](#do-leverage-the-suggest-feature-for-active-learning)|[Adicione muitos padrões](#dont-add-many-patterns)|
|[Monitorize o desempenho da sua aplicação com testes de lote](#do-monitor-the-performance-of-your-app)|[Treinar e publicar com cada exemplo de expressão adicionado](#dont-train-and-publish-with-every-single-example-utterance)|

## <a name="do-define-distinct-intents"></a>Definir intenções distintas
Certifique-se de que o vocabulário para cada intenção é apenas para essa intenção e não se sobrepõe a uma intenção diferente. Por exemplo, se quiser ter uma app que trate de arranjos de viagem, como voos de companhias aéreas e hotéis, pode optar por ter estas áreas temáticas como intenções separadas ou a mesma intenção com as entidades para dados específicos dentro da expressão.

Se o vocabulário entre duas intenções for o mesmo, combine a intenção e use as entidades. 

Considere as seguintes declarações exemplo:

|Expressões de exemplo|
|--|
|Reserve um voo|
|Reserve um hotel|

`Book a flight`e `Book a hotel` usar o mesmo `book a `vocabulário de . Este formato é o mesmo, pelo que deve `flight` ser `hotel` a mesma intenção com as diferentes palavras de e como entidades extraídas. 

## <a name="do-add-descriptors-to-intents"></a>Adicione descritores às intenções

Os descritores ajudam a descrever as funcionalidades para uma intenção. Um descritor pode ser uma lista de frases que são significativas para essa intenção ou uma entidade que é significativa a essa intenção. 

## <a name="do-find-sweet-spot-for-intents"></a>Encontre um ponto doce para as intenções
Utilize os dados de previsão da LUIS para determinar se as suas intenções estão sobrepostas. Intenções sobrepostas confundem LUIS. O resultado é que a principal intenção de pontuação está muito perto de outra intenção. Como o LUIS não usa exatamente o mesmo caminho através dos dados para treinar cada vez, uma intenção sobreposta tem a possibilidade de ser o primeiro ou o segundo no treino. Queres a pontuação da expressão para cada intenção estar mais distante para que este flip/flop não aconteça. Uma boa distinção para as intenções deve resultar na esperada intenção máxima de cada vez. 
 
<a name="#do-build-the-app-iteratively"></a>

## <a name="do-build-your-app-iteratively-with-versions"></a>Construa a sua app iterativamente com versões

Cada ciclo de autoria deve estar dentro de uma nova [versão,](luis-concept-version.md)clonada a partir de uma versão existente. 

## <a name="do-build-for-model-decomposition"></a>Construir para decomposição de modelos

A decomposição do modelo tem um processo típico de:

* criar **Intenção** com base nas intenções de utilizador da aplicação de clientes
* adicionar 15-30 expressões de exemplo com base na entrada de utilizadores do mundo real
* rotular conceito de dados de alto nível em exemplo expressão
* quebrar o conceito de dados em subcomponentes
* adicionar descritores (características) a subcomponentes
* adicionar descritores (características) à intenção 

Uma vez criado a intenção e acrescentando pronunciações exemplo, o exemplo seguinte descreve a decomposição da entidade. 

Comece por identificar conceitos completos de dados que pretende extrair numa expressão. Esta é a sua entidade aprendida por máquinas. Em seguida, decomponha a frase nas suas partes. Isto inclui identificar subcomponentes (como entidades), juntamente com descritores e constrangimentos. 

Por exemplo, se quiser extrair um endereço, a entidade `Address`mais aprendida com máquinas pode ser chamada . Ao criar o endereço, identifique alguns dos seus subcomponentes, tais como endereço de rua, cidade, estado e código postal. 

Continue a decompor esses elementos **limitando** o código postal a uma expressão regular. Decomponha o endereço da rua em partes de um número de rua (usando um número pré-construído), um nome de rua e um tipo de rua. O tipo de rua pode ser descrito com uma lista **de descritores** como avenida, círculo, estrada e pista.

A API de autoria V3 permite a decomposição do modelo. 

## <a name="do-add-patterns-in-later-iterations"></a>Adicione padrões em iterações posteriores

Deve entender como a aplicação se comporta antes de adicionar [padrões](luis-concept-patterns.md) porque os padrões são ponderados mais fortemente do que as expressões de exemplo e irá distorcer a confiança. 

Assim que entender como a sua aplicação se comporta, adicione padrões à medida que se aplicam à sua aplicação. Não precisa adicioná-las a cada [iteração.](luis-concept-app-iteration.md) 

Não há mal nenhum adicioná-los no início do design do seu modelo, mas é mais fácil ver como cada padrão muda o modelo após o modelo ser testado com expressões. 
 
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

## <a name="do-balance-your-utterances-across-all-intents"></a>Equilibre as suas declarações em todas as intenções

Para que as previsões luis sejam precisas, a quantidade de pronunciamentos de exemplo em cada intenção (exceto a intenção de Nenhum), deve ser relativamente igual. 

Se tiver uma intenção com 100 declarações de exemplo e uma intenção com 20 pronunciações de exemplo, a intenção de 100 expressões terá uma taxa de previsão mais elevada.  

## <a name="do-add-example-utterances-to-none-intent"></a>Adicione declarações de exemplo a Nenhuma intenção

Esta intenção é a intenção de recuo, indicando tudo fora da sua aplicação. Adicione um exemplo de expressão à intenção De Sem por cada 10 exemplos de pronunciamentos no resto da sua aplicação LUIS.

## <a name="do-leverage-the-suggest-feature-for-active-learning"></a>Aproveite a funcionalidade de sugestão para a aprendizagem ativa

Utilize as **declarações finais** de revisão de [aprendizagem ativa](luis-how-to-review-endpoint-utterances.md)regularmente, em vez de adicionar mais declarações de exemplo às intenções. Como a aplicação está constantemente a receber declarações de ponto final, esta lista está a crescer e a mudar.

## <a name="do-monitor-the-performance-of-your-app"></a>Monitorize o desempenho da sua app

Monitorize a precisão da previsão utilizando um conjunto de ensaios de [lote.](luis-concept-batch-test.md) 

Mantenha um conjunto separado de expressões que não são usadas como [declarações](luis-concept-utterance.md) de exemplo ou expressões de ponto final. Continue a melhorar a aplicação para o seu conjunto de testes. Adapte o conjunto de teste para refletir as declarações reais dos utilizadores. Utilize este conjunto de teste para avaliar cada iteração ou versão da aplicação. 

## <a name="dont-add-many-example-utterances-to-intents"></a>Não adicione muitas declarações de exemplo às intenções

Após a publicação da aplicação, adicione apenas expressões de aprendizagem ativa no processo de ciclo de vida de desenvolvimento. Se as palavras forem muito semelhantes, adicione um padrão. 

## <a name="dont-use-few-or-simple-entities"></a>Não utilize poucas ou entidades simples

As entidades são construídas para extração e previsão de dados. É importante que cada intenção tenha entidades aprendidas com máquinas que descrevem os dados na intenção. Isto ajuda a LUIS a prever a intenção, mesmo que a sua aplicação de cliente não precise de usar a entidade extraída. 

## <a name="dont-use-luis-as-a-training-platform"></a>Não use o LUIS como plataforma de treino

Luis é específico do domínio de um modelo de linguagem. Não é para funcionar como uma plataforma geral de formação de linguagem natural. 

## <a name="dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats"></a>Não adicione muitos exemplos do mesmo formato, ignorando outros formatos

Luis espera variações nas declarações de uma intenção. As expressões podem variar enquanto têm o mesmo significado geral. Variações podem incluir comprimento de expressão, escolha de palavras e colocação de palavras. 

|Não use o mesmo formato|Utilizar formato variado|
|--|--|
|Compre um bilhete para Seattle<br>Compre um bilhete para Paris<br>Compre um bilhete para Orlando|Comprar 1 bilhete para Seattle<br>Reserve dois lugares no olho vermelho para Paris na próxima segunda-feira<br>Eu gostaria de reservar 3 bilhetes para Orlando para as férias de primavera|

A segunda coluna utiliza diferentes verbos (comprar, reservar, reservar), diferentes quantidades (1, 2, 3) e diferentes arranjos de palavras, mas todos têm a mesma intenção de comprar bilhetes de avião para viagens. 

## <a name="dont-mix-the-definition-of-intents-and-entities"></a>Não misture a definição de intenções e entidades

Crie uma intenção para qualquer ação que o seu bot tome. Utilize as entidades como parâmetros que tornem essa ação possível. 

Para um bot que reserva voos de companhiaaérea, crie uma intenção **bookFlight.** Não crie uma intenção para todas as companhias aéreas ou destinos. Use esses pedaços de dados como [entidades](luis-concept-entity-types.md) e marque-os nas declarações de exemplo. 

## <a name="dont-create-descriptors-with-all-the-possible-values"></a>Não crie descritores com todos os valores possíveis

Forneça alguns exemplos nas [listas](luis-concept-feature.md) de frases do descritor, mas não todas as palavras. Luis generaliza e tem em conta o contexto. 

## <a name="dont-add-many-patterns"></a>Não adicione muitos padrões

Não adicione muitos [padrões.](luis-concept-patterns.md) Luis destina-se a aprender rapidamente com menos exemplos. Não sobrecarregue o sistema desnecessariamente.

## <a name="dont-train-and-publish-with-every-single-example-utterance"></a>Não treine e publique com cada exemplo pronunciamento

Adicione 10 ou 15 expressões antes do treino e da publicação. Isso permite-lhe ver o impacto na precisão da previsão. Adicionar uma única expressão pode não ter um impacto visível na pontuação. 

## <a name="next-steps"></a>Passos seguintes

* Saiba como planear a [sua aplicação](luis-how-plan-your-app.md) na sua aplicação LUIS.
