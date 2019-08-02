---
title: Práticas recomendadas-LUIS
titleSuffix: Azure Cognitive Services
description: Aprenda as práticas recomendadas do LUIS para obter os melhores resultados de modelo da sua aplicação LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: 7a2802bd4daa1a009c610688120c9a56583b054f
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639286"
---
# <a name="best-practices-for-building-a-language-understanding-app-with-cognitive-services"></a>Melhores práticas para criar uma aplicação de compreensão de idiomas com os serviços cognitivos
Use o processo de criação de aplicativos para criar seu aplicativo LUIS: 

* Criar modelo de idioma
* Adicionar algumas expressões de exemplo de treinamento (10 a 15 por objetivo)
* Publicar 
* Teste a partir de ponto final 
* Adicionar funcionalidades

Depois que seu aplicativo for [publicado](luis-how-to-publish-app.md), use o ciclo de criação para adicionar recursos, publicar e testar do ponto de extremidade. Não é começa o próximo ciclo de criação ao adicionar mais expressões de exemplo. Que não permitem o LUIS saiba seu modelo com expressões de utilizador do mundo real. 

Por ordem para LUIS ser eficiente em seu trabalho de aprendizagem, não expanda as expressões com até que o conjunto atual de expressões de exemplo e ponto final com retornando pontuações de predição de confiança e de alta. Melhore as pontuações usando [aprendizado ativo](luis-concept-review-endpoint-utterances.md), [padrões](luis-concept-patterns.md)e [listas de frases](luis-concept-feature.md). 

## <a name="do-and-dont"></a>Fazer e não
A lista seguinte inclui as melhores práticas para aplicações de LUIS:

|O que deve fazer|O que não deve fazer|
|--|--|
|[Definir objetivos distintos](#do-define-distinct-intents) |[Adicionar muitas expressões de exemplo para intenções](#dont-add-many-example-utterances-to-intents) |
|[Encontrar um encontramos uma boa entre demasiado genérico e muito específico para cada intenção](#do-find-sweet-spot-for-intents)|[Utilizar o LUIS como uma plataforma de treinamento](#dont-use-luis-as-a-training-platform)|
|[Criar a sua aplicação iterativamente](#do-build-the-app-iteratively)|[Adicionar muitas expressões de exemplo do formato do mesmo, a ignorar a outros formatos](#dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats)|
|[Adicionar listas de frase e padrões em iterações posteriores](#do-add-phrase-lists-and-patterns-in-later-iterations)|[Mistura a definição de objetivos e entidades](#dont-mix-the-definition-of-intents-and-entities)|
|[Equilibre sua declarações em todas as](#balance-your-utterances-across-all-intents) intenções, exceto a intenção de nenhum.<br>[Adicionar expressões de exemplo para nenhum intenção](#do-add-example-utterances-to-none-intent)|[Criar listas de frase com todos os valores possíveis](#dont-create-phrase-lists-with-all-the-possible-values)|
|[Tirar partido da funcionalidade de sugerido para aprendizagem ativa](#do-leverage-the-suggest-feature-for-active-learning)|[Adicionar muitos padrões](#dont-add-many-patterns)|
|[Monitorizar o desempenho da sua aplicação](#do-monitor-the-performance-of-your-app)|[Formar e publicar com cada expressão única de exemplo adicionado](#dont-train-and-publish-with-every-single-example-utterance)|
|[Usar versões para cada iteração de aplicativo](#do-use-versions-for-each-app-iteration)||

## <a name="do-define-distinct-intents"></a>Definir objetivos distintos
Certifique-se do que vocabulário para cada intenção é apenas para esse propósito e não sobrepostos com um objetivo diferente. Por exemplo, se você quiser ter um aplicativo que lide com as organizações de viagem, como vôos de viagens aéreas e Hotéis, você pode optar por ter essas áreas de assunto como intenções separadas ou a mesma intenção com entidades para dados específicos dentro do expressão.

Se o vocabulário entre dois objetivos é o mesmo, combinar a intenção e utilizar entidades. 

Considere as expressões de exemplo seguinte:

|Expressões de exemplo|
|--|
|Programar um vôo|
|Reservar um hotel|

"Programar um vôo" e "Reservar quarto num hotel" utiliza o mesmo vocabulário de "livro um". Esse formato é o mesmo, portanto, deve ser a mesma intenção com as diferentes palavras de voo e Hotel como entidades extraídas. 

Para obter mais informações:
* Piloto [Conceitos sobre tentativas em seu aplicativo LUIS](luis-concept-intent.md)
* Tutorial: [Compilar o aplicativo LUIS para determinar as intenções do usuário](luis-quickstart-intents-only.md)
* Como: [Adicionar tentativas para determinar a intenção do usuário de declarações](luis-how-to-add-intents.md)


## <a name="do-find-sweet-spot-for-intents"></a>Encontrar encontramos uma boa para intenções
Utilize dados de predição do LUIS para determinar se seus objetivos estão sobrepostos. Sobrepondo tentativas de confundir LUIS. O resultado é que a parte superior a intenção de classificação é demasiado fechar para outro objetivo. Como o LUIS não utiliza o mesmo caminho exato através dos dados para cada hora de formação, um objetivo sobreposto tem uma chance de ser o primeiro ou segundo no treinamento. Você quer que a pontuação de expressão para cada intenção fique mais distante, de modo que esse flip/flop não aconteça. Boa distinção para objetivos deve resultar na intenção de principal esperada cada vez. 
 
## <a name="do-build-the-app-iteratively"></a>Criar a aplicação de maneira iterativa
Mantenha um conjunto separado de declarações que não é usado como [exemplo declarações](luis-concept-utterance.md) ou Endpoint declarações. Continuam a melhorar a aplicação para o seu conjunto de teste. Adapte o teste definido para refletir as expressões de utilizador real. Use este conjunto de testes para avaliar cada iteração ou versão do aplicativo. 

Os desenvolvedores devem ter três conjuntos de dados. A primeira é que as expressões de exemplo para criar o modelo. O segundo é para testar o modelo no ponto final. O terceiro é o blind testar dados utilizados no [testes de batch](luis-how-to-batch-test.md). Esse último conjunto não é usado no treinamento do aplicativo nem enviado no ponto de extremidade.  

Para obter mais informações:
* Piloto [Ciclo de criação para seu aplicativo LUIS](luis-concept-app-iteration.md)

## <a name="do-add-phrase-lists-and-patterns-in-later-iterations"></a>Adicionar listas de frase e padrões em iterações posteriores

Uma prática recomendada é não aplicar essas práticas antes que seu aplicativo seja testado. Você deve entender como o aplicativo se comporta antes de adicionar [listas de frases](luis-concept-feature.md) e [padrões](luis-concept-patterns.md) , pois esses recursos são ponderados de forma mais intensa do que o exemplo declarações e distorcerá a confiança. 

Depois de entender como seu aplicativo se comporta sem eles, adicione cada um desses recursos à medida que eles se aplicam ao seu aplicativo. Você não precisa adicionar esses recursos a cada iteração [](luis-concept-app-iteration.md) ou alterar os recursos com cada versão. 

Não há danos para adicioná-los no início do design do modelo, mas é mais fácil ver como cada recurso altera os resultados depois que o modelo é testado com declarações. 

Uma prática recomendada é testar por meio do [ponto de extremidade](luis-get-started-create-app.md#query-the-endpoint-with-a-different-utterance) para que você tenha o benefício adicional do [aprendizado ativo](luis-concept-review-endpoint-utterances.md). O [painel de teste interativo](luis-interactive-test.md) também é uma metodologia de teste válida. 
 

### <a name="phrase-lists"></a>Listas de expressões

[Frase listas](luis-concept-feature.md) permitem-lhe definir os dicionários de palavras relacionadas ao seu domínio de aplicação. Propague sua lista de frases com algumas palavras e, em seguida, use o recurso sugerir para que LUIS saiba mais palavras no vocabulário específico ao seu aplicativo. Uma lista de frases melhora a detecção de intenção e a classificação de entidade, aumentando o sinal associado a palavras ou frases que são significativas para seu aplicativo. 

Não adicione cada palavra ao vocabulário, uma vez que a lista de expressão não é uma correspondência exata. 

Para obter mais informações:
* Piloto [Recursos da lista de frases em seu aplicativo LUIS](luis-concept-feature.md)
* Procedimento: [Usar listas de frases para impulsionar o sinal da lista de palavras](luis-how-to-add-features.md)

### <a name="patterns"></a>Padrões

Expressões de utilizador real do ponto de extremidade, muito semelhante entre si, podem revelar os padrões de escolha do word e o posicionamento. O [padrão](luis-concept-patterns.md) funcionalidade tira esta opção do word e o posicionamento, juntamente com expressões regulares para melhorar a exatidão da previsão. Uma expressão regular no padrão permite palavras e de que pretende ignorar enquanto ainda o padrão de correspondência de pontuação. 

Use a [sintaxe opcional](luis-concept-patterns.md) do padrão para pontuação para que a pontuação possa ser ignorada. Use a [lista explícita](luis-concept-patterns.md#explicit-lists) para compensar o padrão. qualquer problema de sintaxe. 

Para obter mais informações:
* Piloto [Padrões melhoram a precisão da previsão](luis-concept-patterns.md)
* Procedimento: [Como adicionar padrões para melhorar a precisão da previsão](luis-how-to-model-intent-pattern.md)

## <a name="balance-your-utterances-across-all-intents"></a>Equilibre seu declarações em todas as intenções

Para que as previsões de LUIS sejam precisas, a quantidade de declarações de exemplo em cada tentativa (exceto para a intenção de nenhum) deve ser relativamente igual. 

Se você tiver uma intenção com 100 de exemplo declarações e uma intenção com 20 exemplos de declarações, a intenção de 100-expressão terá uma taxa mais alta de previsão.  

## <a name="do-add-example-utterances-to-none-intent"></a>A adicionar expressões de exemplo para nenhum intenção

Essa é a intenção de fallback, indicada tudo fora de seu aplicativo. Adicione uma expressão de exemplo para a intenção de todas as expressões de 10 exemplo no resto da sua aplicação LUIS nenhum.

Para obter mais informações:
* Piloto [Entenda o que são bons declarações para seu aplicativo LUIS](luis-concept-utterance.md)

## <a name="do-leverage-the-suggest-feature-for-active-learning"></a>A tirar partido da funcionalidade de sugerido para aprendizagem ativa

Uso [aprendizagem ativa](luis-how-to-review-endpoint-utterances.md)da **rever expressões de ponto final** regularmente, em vez de adicionar mais expressões de exemplo para objetivos. Uma vez que a aplicação está a receber constantemente expressões de ponto final, esta lista está crescendo e alterar.

Para obter mais informações:
* Piloto [Conceitos para habilitar o aprendizado ativo examinando o ponto de extremidade declarações](luis-concept-review-endpoint-utterances.md)
* Tutorial: [Tutorial: Corrigir previsões inseguras examinando o ponto de extremidade declarações](luis-tutorial-review-endpoint-utterances.md)
* Procedimento: [Como examinar o ponto de extremidade declarações no portal do LUIS](luis-how-to-review-endpoint-utterances.md)

## <a name="do-monitor-the-performance-of-your-app"></a>Monitorizar o desempenho da sua aplicação

Monitore a precisão da previsão usando um conjunto de [teste em lotes](luis-concept-batch-test.md) . 

## <a name="dont-add-many-example-utterances-to-intents"></a>Não adicionar muitas expressões de exemplo para intenções

Depois da aplicação for publicada, adicione apenas expressões com aprendizagem ativa no processo iterativo. Se as expressões são muito semelhantes, adicione um padrão. 

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

Para um chatbot que irá Reservar voos de companhia aérea, crie uma **BookFlight** intenção. Não crie um objetivo para cada companhia aérea ou de cada destino. Utilizar essas partes de dados como [entidades](luis-concept-entity-types.md) e marcá-los nas expressões de exemplo. 

## <a name="dont-create-phrase-lists-with-all-the-possible-values"></a>Não crie listas de frase com todos os valores possíveis

Fornecer exemplos de alguns a [frase listas](luis-concept-feature.md) , mas não todas as palavras. LUIS generaliza e tem o contexto em consideração. 

## <a name="dont-add-many-patterns"></a>Não adicione muitos padrões

Não adicione demasiados [padrões](luis-concept-patterns.md). LUIS destina-se para saber rapidamente com menos de exemplos. Não sobrecarregar o sistema desnecessariamente.

## <a name="dont-train-and-publish-with-every-single-example-utterance"></a>Não formar e publicar com cada expressão única de exemplo

Adicione expressões de 10 ou 15 antes de treinamento e publicação. Que pode ver o impacto na precisão de previsão. Adicionar uma única expressão pode não ter um impacto visível na classificação. 

## <a name="do-use-versions-for-each-app-iteration"></a>Usar versões para cada iteração de aplicativo

Cada ciclo de criação deve estar dentro de uma nova [versão](luis-concept-version.md), clonada de uma versão existente. LUIS não tem limite para versões. Um nome de versão é usado como parte da rota da API para que seja importante selecionar caracteres permitidos em uma URL, bem como manter dentro da contagem de 10 caracteres para uma versão. Desenvolva uma estratégia de nome de versão para manter suas versões organizadas. 

Para obter mais informações:
* Piloto [Entenda como e quando usar uma versão LUIS](luis-concept-version.md)
* Procedimento: [Use versões para editar e testar sem afetar os aplicativos de preparo ou de produção](luis-how-to-manage-versions.md)


## <a name="next-steps"></a>Passos Seguintes

* Saiba como [planear a sua aplicação](luis-how-plan-your-app.md) na sua aplicação LUIS.
