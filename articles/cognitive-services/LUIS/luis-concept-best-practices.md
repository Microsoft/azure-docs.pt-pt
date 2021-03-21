---
title: Melhores práticas para construir a sua app LUIS
description: Aprenda as melhores práticas para obter os melhores resultados do modelo da sua app LUIS.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/17/2020
ms.openlocfilehash: 2f6ed85416cc5d7c3c2baba2b2cfe489e301d7e5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98788490"
---
# <a name="best-practices-for-building-a-language-understanding-luis-app"></a>Melhores práticas para a construção de uma app de compreensão linguística (LUIS)
Utilize o processo de autoria da aplicação para construir a sua aplicação LUIS:

* Construir modelos linguísticos (intenções e entidades)
* Adicione algumas declarações de exemplo de treino (15-30 por intenção)
* Publicar para ponto final
* Teste a partir do ponto final

Assim que a sua aplicação for [publicada,](luis-how-to-publish-app.md)utilize o ciclo de vida do desenvolvimento para adicionar funcionalidades, publicar e testar a partir do ponto final. Não comece o próximo ciclo de autoria adicionando mais palavras de exemplo porque isso não permite que a LUIS aprenda o seu modelo com expressões de utilizadores no mundo real.

Não expanda as expressões até que o conjunto atual de palavras de exemplo e ponto final esteja a voltar confiante, com notas de previsão elevadas. Melhorar as pontuações utilizando [a aprendizagem ativa.](luis-concept-review-endpoint-utterances.md)




## <a name="do-and-dont"></a>Fazer e Não Fazer
A lista a seguir inclui as melhores práticas para aplicações LUIS:

|O que deve fazer|O que não deve fazer|
|--|--|
|[Planeie o seu esquema](#do-plan-your-schema)|[Construir e publicar sem um plano](#dont-publish-too-quickly)|
|[Definir intenções distintas](#do-define-distinct-intents)<br>[Adicionar funcionalidades a intenções](#do-add-features-to-intents)<br>
[Use entidades aprendidas com máquinas](#do-use-machine-learned-entities) |[Adicione muitos exemplos de declarações às intenções](#dont-add-many-example-utterances-to-intents)<br>[Use poucas ou simples entidades](#dont-use-few-or-simple-entities) |
|[Encontre um ponto doce entre muito genérico e muito específico para cada intenção](#do-find-sweet-spot-for-intents)|[Use o LUIS como plataforma de formação](#dont-use-luis-as-a-training-platform)|
|[Construa a sua aplicação iterativamente com versões](#do-build-your-app-iteratively-with-versions)<br>[Construir entidades para decomposição de modelos](#do-build-for-model-decomposition)|[Adicione muitos exemplos de palavras do mesmo formato, ignorando outros formatos](#dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats)|
|[Adicione padrões em iterações posteriores](#do-add-patterns-in-later-iterations)|[Misture a definição de intenções e entidades](#dont-mix-the-definition-of-intents-and-entities)|
|[Equilibre as suas declarações em todas as intenções,](#balance-your-utterances-across-all-intents) exceto a intenção de Nenhum.<br>[Adicione palavras de exemplo a nenhuma intenção](#do-add-example-utterances-to-none-intent)|[Criar listas de frases com todos os valores possíveis](#dont-create-phrase-lists-with-all-the-possible-values)|
|[Aproveite a característica de sugestão para a aprendizagem ativa](#do-leverage-the-suggest-feature-for-active-learning)|[Adicione muitos padrões](#dont-add-many-patterns)|
|[Monitorize o desempenho da sua app com testes de lote](#do-monitor-the-performance-of-your-app)|[Treine e publique com cada exemplo de expressão adicionada](#dont-train-and-publish-with-every-single-example-utterance)|

## <a name="do-plan-your-schema"></a>Planeie o seu esquema

Antes de começar a construir o esquema da sua aplicação, deve identificar o que e onde planeia usar esta aplicação. Quanto mais minucioso e específico for o seu planeamento, melhor será a sua aplicação.

* Utilizadores direcionados para a investigação
* Definindo personas de ponta a ponta para representar a sua app - voz, avatar, tratamento de problemas (proativo, reativo)
* Identifique as interações dos utilizadores (texto, fala) através dos canais, entregando-se às soluções existentes ou criando uma nova solução para esta app
* Viagem de utilizador de ponta a ponta
    * O que deve esperar que esta aplicação faça e não faça? Quais são as prioridades do que deve fazer?
    * Quais são os principais casos de utilização?
* Recolha de dados - [saiba](data-collection.md) a recolha e preparação de dados

## <a name="do-define-distinct-intents"></a>Defina intenções distintas
Certifique-se de que o vocabulário para cada intenção é apenas para essa intenção e não sobrepondo-se a uma intenção diferente. Por exemplo, se quiser ter uma app que trate de acordos de viagem como voos de companhias aéreas e hotéis, pode optar por ter estas áreas temáticas como intenções separadas ou a mesma intenção com entidades para dados específicos dentro da expressão.

Se o vocabulário entre duas intenções for o mesmo, combine a intenção e use entidades.

Considere as seguintes palavras de exemplo:

|Expressões de exemplo|
|--|
|Reserve um voo|
|Reserve um hotel|

`Book a flight` e `Book a hotel` usar o mesmo vocabulário `book a ` de. Este formato é o mesmo, pelo que deve ser a mesma intenção com as diferentes palavras de `flight` e `hotel` como entidades extraídas.

## <a name="do-add-features-to-intents"></a>Adicionar funcionalidades a intenções

As características descrevem conceitos para uma intenção. Uma característica pode ser uma lista de frases de palavras que são significativas para essa intenção ou uma entidade que é significativa para essa intenção.

## <a name="do-find-sweet-spot-for-intents"></a>Encontre um ponto doce para intenções
Utilize dados de previsão da LUIS para determinar se as suas intenções estão sobrepostas. As intenções sobrepostas confundem LUIS. O resultado é que a intenção de pontuação superior está muito perto de outra intenção. Como o LUIS não usa exatamente o mesmo caminho através dos dados para treinar cada vez, uma intenção sobreposta tem a chance de ser o primeiro ou o segundo no treino. Queres que a pontuação da expressão para cada intenção esteja mais afastada para que este flip/flop não aconteça. Uma boa distinção para as intenções deve resultar na intenção esperada de todas as vezes.

## <a name="do-use-machine-learned-entities"></a>Use entidades aprendidas com máquinas

As entidades aprendidas com máquinas são adaptadas à sua app e exigem que a rotulagem seja bem sucedida. Se não estiver a utilizar entidades aprendidas com máquinas, poderá estar a utilizar a ferramenta errada.

As entidades aprendidas com máquinas podem usar outras entidades como funcionalidades. Estas outras entidades podem ser entidades personalizadas, como entidades de expressão regular ou entidades de lista, ou pode utilizar as entidades pré-construídas como funcionalidades.

Conheça as [entidades aprendidas com máquinas eficazes.](luis-concept-entity-types.md#effective-machine-learned-entities)

<a name="#do-build-the-app-iteratively"></a>

## <a name="do-build-your-app-iteratively-with-versions"></a>Construa a sua aplicação iterativamente com versões

Cada ciclo de autoria deve estar dentro de uma nova [versão,](./luis-concept-app-iteration.md)clonada a partir de uma versão existente.

## <a name="do-build-for-model-decomposition"></a>Construa para decomposição de modelos

A decomposição do modelo tem um processo típico de:

* criar **intenção** com base nas intenções de utilizador da app cliente
* adicionar 15-30 palavras de exemplo com base na entrada de utilizadores no mundo real
* rótulo conceito de dados de nível superior em palavra de exemplo
* quebrar o conceito de dados em subentidades
* adicionar funcionalidades a subentidades
* adicionar funcionalidades a intenções

Depois de ter criado as intenções e acrescentado palavras de exemplo, o exemplo a seguir descreve a decomposição da entidade.

Comece por identificar conceitos completos de dados que pretende extrair numa expressão. Esta é a sua entidade de aprendizagem automática. Em seguida, decomponha a frase nas suas partes. Isto inclui a identificação de subentamentos e funcionalidades.

Por exemplo, se pretender extrair um endereço, a entidade superior de aprendizagem automática pode ser chamada `Address` . Ao criar o endereço, identifique algumas das suas subentidades, tais como endereço de rua, cidade, estado e código postal.

Continue a decompor estes elementos por:
* Adicionar uma característica necessária do código postal como entidade de expressão regular.
* Decompondo o endereço da rua em partes:
    * Um **número de rua** com uma característica necessária de uma entidade de número pré-construído.
    * Um **nome de rua.**
    * Um **tipo de rua** com uma característica necessária de uma entidade de lista, incluindo palavras como avenida, círculo, estrada e pista.

A V3 autoria da API permite a decomposição do modelo.

## <a name="do-add-patterns-in-later-iterations"></a>Adicione padrões em iterações posteriores

Você deve entender como a aplicação se comporta antes de adicionar [padrões](luis-concept-patterns.md) porque os padrões são ponderados mais fortemente do que palavras de exemplo e vai distorcer a confiança.

Assim que perceberes como a tua aplicação se comporta, adiciona padrões à medida que se aplicam à tua aplicação. Não é necessário adicioná-los a cada [iteração](luis-concept-app-iteration.md).

Não há nenhum mal a adicioná-los no início do seu design de modelo, mas é mais fácil ver como cada padrão muda o modelo depois de o modelo ser testado com expressões.

<a name="balance-your-utterances-across-all-intents"></a>

## <a name="do-balance-your-utterances-across-all-intents"></a>Equilibre as suas declarações em todas as intenções

Para que as previsões do LUIS sejam precisas, a quantidade de palavras de exemplo em cada intenção (com exceção da intenção de Ninguém), deve ser relativamente igual.

Se tiver uma intenção com 100 palavras de exemplo e uma intenção com 20 palavras de exemplo, a intenção de 100 palavras terá uma taxa de previsão mais elevada.

## <a name="do-add-example-utterances-to-none-intent"></a>Adicione palavras de exemplo a nenhuma intenção

Esta intenção é a intenção de recuo, indicando tudo fora da sua aplicação. Adicione um exemplo de intenção ao None intenção por cada 10 palavras de exemplo no resto da sua app LUIS.

## <a name="do-leverage-the-suggest-feature-for-active-learning"></a>Aproveite a característica de sugestão para a aprendizagem ativa

Use **as expressões** de ponto final de revisão da [aprendizagem ativa](luis-how-to-review-endpoint-utterances.md)regularmente, em vez de adicionar mais palavras de exemplo às intenções. Como a aplicação está constantemente a receber declarações de ponto final, esta lista está a crescer e a mudar.

## <a name="do-monitor-the-performance-of-your-app"></a>Monitorize o desempenho da sua aplicação

Monitorize a precisão da previsão utilizando um conjunto [de testes de lote.](./luis-how-to-batch-test.md)

Mantenha um conjunto separado de expressões que não são usadas como [palavras de exemplo](luis-concept-utterance.md) ou proclamações de ponto final. Continue a melhorar a aplicação para o seu conjunto de testes. Adaptar o conjunto de teste para refletir as declarações reais do utilizador. Utilize este conjunto de testes para avaliar cada iteração ou versão da aplicação.

## <a name="dont-publish-too-quickly"></a>Não publique muito rápido.

Publicar a sua aplicação muito rapidamente, sem [um planeamento adequado,](#do-plan-your-schema)pode levar a várias questões como:

* A sua aplicação não funcionará no seu cenário real a um nível aceitável de desempenho.
* O esquema (intenções e entidades) não seria apropriado, e se desenvolveu a lógica da aplicação do cliente seguindo o esquema, poderá ter de reescrevê-lo do zero. Isto causaria atrasos inesperados e um custo extra para o projeto em que está a trabalhar.
* As declarações que adiciona ao modelo podem causar distorção para o conjunto de exemplos que é difícil de depurar e identificar. Também dificultará a eliminação da ambiguidade depois de se ter comprometido com um certo esquema.

## <a name="dont-add-many-example-utterances-to-intents"></a>Não adicione muitos exemplos de declarações às intenções

Após a publicação da app, apenas adicione expressões de aprendizagem ativa no processo de ciclo de vida de desenvolvimento. Se as expressões forem muito semelhantes, adicione um padrão.

## <a name="dont-use-few-or-simple-entities"></a>Não use poucas ou simples entidades

As entidades são construídas para a extração e previsão de dados. É importante que cada intenção tenha entidades de aprendizagem automática que descrevam os dados na intenção. Isto ajuda a LUIS a prever a intenção, mesmo que a sua aplicação de cliente não precise de usar a entidade extraída.

## <a name="dont-use-luis-as-a-training-platform"></a>Não use o LUIS como plataforma de treino

LUIS é específico do domínio de um modelo de linguagem. Não é para funcionar como uma plataforma geral de formação em linguagem natural.

## <a name="dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats"></a>Não adicione muitos exemplos de palavras do mesmo formato, ignorando outros formatos

LUIS espera variações nas declarações de uma intenção. As expressões podem variar enquanto têm o mesmo significado geral. Variações podem incluir comprimento de expressão, escolha de palavras e colocação de palavras.

|Não use o mesmo formato|Use formato variado|
|--|--|
|Compre um bilhete para Seattle<br>Compre um bilhete para Paris<br>Compre um bilhete para Orlando|Compre 1 bilhete para Seattle<br>Reserve dois lugares no olho vermelho para Paris na próxima segunda-feira<br>Gostaria de reservar 3 bilhetes para Orlando para as férias de primavera.|

A segunda coluna utiliza verbos diferentes (comprar, reservar, livro), quantidades diferentes (1, dois, 3) e diferentes arranjos de palavras, mas todos têm a mesma intenção de comprar bilhetes de avião para viagens.

## <a name="dont-mix-the-definition-of-intents-and-entities"></a>Não misture a definição de intenções e entidades

Crie uma intenção para qualquer ação que o seu bot irá tomar. Use as entidades como parâmetros que tornem essa ação possível.

Para um bot que irá reservar voos de companhias aéreas, crie uma intenção **bookFlight.** Não crie uma intenção para todas as companhias aéreas ou destinos. Use esses dados como [entidades](luis-concept-entity-types.md) e marque-os nas declarações de exemplo.

## <a name="dont-create-phrase-lists-with-all-the-possible-values"></a>Não crie listas de frases com todos os valores possíveis

Forneça alguns exemplos nas listas de [frases,](luis-concept-feature.md) mas não em cada palavra ou frase. LUIS generaliza e leva em conta o contexto.

## <a name="dont-add-many-patterns"></a>Não adicione muitos padrões

Não adicione muitos [padrões.](luis-concept-patterns.md) Luis é para aprender rapidamente com menos exemplos. Não sobrecarregue o sistema desnecessariamente.

## <a name="dont-train-and-publish-with-every-single-example-utterance"></a>Não treine e publique com cada exemplo de expressão

Adicione 10 ou 15 expressões antes do treino e publicação. Isso permite-lhe ver o impacto na precisão da previsão. Adicionar uma única expressão pode não ter um impacto visível na pontuação.

## <a name="next-steps"></a>Passos seguintes

* Saiba como planear a [sua aplicação](luis-how-plan-your-app.md) na sua aplicação LUIS.