---
title: Glossário - LUIS
titleSuffix: Azure Cognitive Services
description: O glossário de termos de explica que poderá encontrar ao trabalhar com o serviço de API de LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 4f78b4c50f4cd65f3dc32c48cea81b705dc44de1
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79220954"
---
# <a name="language-understanding-glossary-of-common-vocabulary-and-concepts"></a>Glossário de compreensão linguística do vocabulário e conceitos comuns
A compreensão de idiomas (LUIS) explica de glossário de termos que poderá encontrar ao trabalhar com o serviço de API de LUIS.

## <a name="active-version"></a>Versão ativa

A versão de LUIS Active Directory é a versão que recebe todas as alterações ao modelo. No portal [LUIS,](luis-reference-regions.md) se quiser fazer alterações a uma versão que não seja a versão ativa, precisa de definir essa versão como ativa.

## <a name="authoring"></a>Autoria

A autoria é a capacidade de criar, gerir e implementar uma [aplicação LUIS,](#luis-app)quer utilizando o portal [LUIS,](luis-reference-regions.md) quer a autoria de [APIs.](https://go.microsoft.com/fwlink/?linkid=2092087)

## <a name="authoring-key"></a>Chave de autoria

Anteriormente denominado "Programmatic" chave. Usada para criar a aplicação. Não é utilizado para consultas de ponto final de nível de produção. Para mais informações, consulte [os limites-chave](luis-boundaries.md#key-limits).

## <a name="batch-test-json-file"></a>Ficheiro JSON de teste de lote

O teste do lote é a capacidade de validar o modelo de uma aplicação LUIS atual com um conjunto de comentários consistente e conhecido de expressões de utilizadores. O teste do lote é definido num [ficheiro formatado JSON](luis-concept-batch-test.md#batch-file-format).

Veja também:
* [Conceitos](luis-concept-batch-test.md)
* [Como](luis-how-to-batch-test.md)
* [Tutorial](luis-tutorial-batch-testing.md)

## <a name="collaborator"></a>Colaborador

Colaborador/colaborador, não é o [proprietário](#owner) da app, mas tem as mesmas permissões para adicionar, editar e apagar as intenções, entidades, expressões.

## <a name="contributor"></a>Contribuinte

Um colaborador é a mesma coisa que um [colaborador.](#collaborator)

## <a name="descriptor"></a>Descritor

Um descritor é uma [característica](#features) aplicada a um modelo em tempo de formação, incluindo [listas](#phrase-list) de frases e [entidades.](#entity) 

## <a name="domain"></a>Domínio

No contexto LUIS, um **domínio** é uma área de conhecimento. O domínio é específico para a sua área de aplicação de dados de conhecimento. Isso pode ser uma área geral como a aplicação de agente de viagens. Uma aplicação de agente de viagens também pode ser específica para apenas as áreas de informações da sua empresa, tais como locais geográficos específicos de linguagens e serviços.

## <a name="endpoint"></a>Ponto final

O URL [de ponto final](https://go.microsoft.com/fwlink/?linkid=2092356) luis é onde você submete consultas LUIS após a [aplicação LUIS](#luis-app) ser autora e publicada. O URL de ponto final contém a região da aplicação publicada, bem como o ID da aplicação. Pode encontrar o ponto final na página **[de Chaves e pontos finais](luis-how-to-azure-subscription.md)** da sua aplicação, ou pode obter o URL final da API get [App Info.](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c37)

## <a name="entity"></a>Entidade

[As entidades](luis-concept-entity-types.md) são palavras importantes em [expressões](luis-concept-utterance.md) que descrevem informações relevantes para a [intenção](luis-concept-intent.md), e por vezes são essenciais para ela. Uma entidade é essencialmente um tipo de dados no LUIS.

## <a name="f-measure"></a>Medida F

Nos ensaios de [lote,](luis-interactive-test.md#batch-testing)uma medida da exatidão do ensaio.

## <a name="false-negative"></a>Falso negativo (FN)

Nos testes de [lote,](luis-interactive-test.md#batch-testing)os pontos de dados representam declarações nas quais a sua aplicação previu incorretamente a ausência da intenção/entidade alvo.

## <a name="false-positive"></a>Falso positivo (FP)

Nos testes de [lote,](luis-interactive-test.md#batch-testing)os pontos de dados representam declarações nas quais a sua aplicação previu incorretamente a existência da intenção/entidade alvo.

## <a name="features"></a>Características

No machine learning, uma [característica](luis-concept-feature.md) é um traço distintivo ou atributo de dados que o seu sistema observa.

## <a name="intent"></a>Intenção

Uma [intenção](luis-concept-intent.md) representa uma tarefa ou ação que o utilizador quer realizar. É um objetivo ou objetivo expressado numa entrada de usuário, como reserva um vôo, pagando uma fatura ou localizar um artigo de notícias. No LUIS, a predição de intenção baseia-se a expressão inteira. As entidades, por comparação, são partes de uma expressão.

## <a name="labeling"></a>Rotulagem

Rotulagem, ou marcação, é o processo de associar uma palavra ou frase na [expressão](#utterance) de uma intenção com uma [entidade](#entity) (datatype).

## <a name="luis-app"></a>App LUIS

Uma aplicação LUIS é uma coleção de modelos linguísticos para processamento de linguagem natural, incluindo [intenções,](#intent) [entidades,](#entity)e [expressões](#utterance)rotuladas.

## <a name="owner"></a>Proprietário

Cada aplicação tem um proprietário que é a pessoa que criou a aplicação. O proprietário pode adicionar [colaboradores.](#collaborator)

## <a name="pattern"></a>Padrões
A função Padrão anterior é substituída por [Padrões](luis-concept-patterns.md). Utilize padrões para melhorar a exatidão da previsão, fornecendo exemplos de treinamento menos.

## <a name="phrase-list"></a>Lista de frases

Uma lista de [frases](luis-concept-feature.md) inclui um grupo de valores (palavras ou frases) que pertencem à mesma classe e devem ser tratados da mesma forma (por exemplo, nomes de cidades ou produtos). Uma lista intercambiável é tratada como sinónimos.

## <a name="prebuilt-domains"></a>Domínio pré-construído

Um [domínio pré-construído](luis-how-to-use-prebuilt-domains.md) é uma aplicação LUIS configurada para um domínio específico, como domótica (HomeAutomation) ou reservas de restaurantes (RestaurantReservation). Os objetivos, expressões e entidades estão configuradas para este domínio.

## <a name="prebuilt-entity"></a>Entidade pré-construída

Uma [entidade pré-construída](luis-prebuilt-entities.md) é uma entidade que a LUIS fornece para tipos comuns de informação, como número, URL e e-mail. Optar por adicionar uma entidade pré-criados à sua aplicação.

## <a name="precision"></a>Precisão
Nos testes de [lote,](luis-interactive-test.md#batch-testing)a precisão (também chamada de valor preditivo positivo) é a fração de expressões relevantes entre as expressões recuperadas.

## <a name="programmatic-key"></a>Chave programática

Renomeado para [chave de autoria](#authoring-key).

## <a name="publish"></a>Publicar

Publicação significa disponibilizar uma versão ativa luis na encenação ou [no ponto final](#endpoint)da produção.  

## <a name="quota"></a>Quota

A quota LUIS é a limitação do nível de [subscrição do Azure.](https://aka.ms/luis-price-tier) A quota de LUIS pode ser limitada por ambos os pedidos por segundo (estado de HTTP 429) e o total de pedidos por mês (estado de HTTP 403).

## <a name="recall"></a>Recordar
Nos testes de [lote,](luis-interactive-test.md#batch-testing)lembre-se (também conhecido como sensibilidade), é a capacidade de LUIS generalizar.

## <a name="semantic-dictionary"></a>Dicionário semântico
Um dicionário semântico é fornecido a página de lista de entidades, bem como a página de lista de frase. O dicionário semântico fornece sugestões de palavras com base no âmbito atual.

## <a name="sentiment-analysis"></a>Análise de Sentimentos
A análise do sentimento fornece valores positivos ou negativos das expressões fornecidas pelo [Text Analytics](https://azure.microsoft.com/services/cognitive-services/text-analytics/).

## <a name="speech-priming"></a>Preparação do discurso

Priming de fala permite que o seu serviço de voz para ser preparados com o seu modelo do LUIS.

## <a name="spelling-correction"></a>Correção ortográfica

Ative o corretor ortográfico Bing corrigir palavras com erros ortográficos nas expressões antes de predição.

## <a name="starter-key"></a>Chave de arranque

Uma chave gratuita para usar quando começar a usar o LUIS.

## <a name="structure"></a>Estrutura

Adicione estrutura a uma entidade aprendida por máquinas para fornecer subcomponentes com descritores (características) e constrangimentos (expressão regular ou entidades de lista).

## <a name="subscription-key"></a>Chave de subscrição

A chave de subscrição é a chave final de **previsão** associada ao serviço LUIS [que criou no Azure.](luis-how-to-azure-subscription.md) Esta chave não é a chave de [autor.](#programmatic-key) Se tiver uma chave de ponto de extremidade, ele deve ser usado para todos os pedidos de ponto final em vez da chave de criação. Pode ver a sua chave de ponto final atual dentro do URL de ponto final na parte inferior de Keys e página [ **de pontos finais** ](luis-how-to-azure-subscription.md) no site [da LUIS.](luis-reference-regions.md) É o valor do **nome/par** de valor da chave de assinatura.

## <a name="test"></a>Teste

[Testar](luis-interactive-test.md#test-your-app) uma aplicação LUIS significa passar uma expressão ao LUIS e ver os resultados da JSON.

## <a name="timezoneoffset"></a>Compensação do fuso horário

O ponto final inclui timezoneOffset. Este é o número de minutos que pretende adicionar ou remover o datetimeV2 entidade pré-criados. Por exemplo, se a expressão for "que altura é agora?", datetimeV2 devolvido é a hora atual para o pedido do cliente. Se o seu pedido de cliente é proveniente de um bot ou outro aplicativo que não é o mesmo como usuário de seu bot, deve passar o deslocamento entre o bot e o utilizador.

Consulte [alterar o fuso horário da entidade datav2 pré-construída](luis-concept-data-alteration.md?#change-time-zone-of-prebuilt-datetimev2-entity).

## <a name="token"></a>Token
Um token é a menor unidade que pode ser identificada numa entidade. A tokenização baseia-se na [cultura](luis-language-support.md#tokenization)da aplicação.

## <a name="train"></a>Comboio

A formação é o processo de ensino da LUIS sobre quaisquer alterações à versão ativa desde a última formação.

## <a name="true-negative"></a>Verdadeiro negativo (TN)

Nos testes de [lote,](luis-interactive-test.md#batch-testing)os pontos de dados representam declarações nas quais a sua aplicação previu corretamente a ausência da intenção/entidade alvo.

## <a name="true-positive"></a>Verdadeiro positivo (TP)

Nos testes de [lote,](luis-interactive-test.md#batch-testing)os pontos de dados representam declarações nas quais a sua aplicação previu corretamente a existência da intenção/entidade alvo.

## <a name="utterance"></a>Proclamação

Uma expressão é uma expressão de linguagem natural, como "permissões de livro 2 para Seattle Terça-feira seguinte". Expressões com de exemplo são adicionados à intenção.

## <a name="version"></a>Versão

Uma [versão](luis-how-to-manage-versions.md) LUIS é um modelo de dados específico associado a um ID de aplicação LUIS e ao ponto final publicado. Todas as aplicações de LUIS têm, pelo menos, uma versão.
