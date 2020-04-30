---
title: Glossário - LUIS
titleSuffix: Azure Cognitive Services
description: O glossário explica termos que pode encontrar enquanto trabalha com o Serviço LUIS API.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: f764b0d42e08e68b45d49a5aae8542f05707ccde
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82099364"
---
# <a name="language-understanding-glossary-of-common-vocabulary-and-concepts"></a>Glossário de compreensão linguística do vocabulário e conceitos comuns
O glossário da Compreensão linguística (LUIS) explica termos que pode encontrar enquanto trabalha com o Serviço LUIS API.

## <a name="active-version"></a><a name="active-version"></a>Versão ativa

A versão ATIVA DO LUIS é a versão que recebe quaisquer alterações ao modelo. No portal [LUIS,](luis-reference-regions.md) se quiser fazer alterações a uma versão que não seja a versão ativa, precisa de definir essa versão como ativa.

## <a name="authoring"></a><a name="authoring"></a>Criação de conteúdos

A autoria é a capacidade de criar, gerir e implementar uma [aplicação LUIS,](#luis-app)quer utilizando o portal [LUIS,](luis-reference-regions.md) quer a autoria de [APIs.](https://go.microsoft.com/fwlink/?linkid=2092087)

## <a name="authoring-key"></a><a name="authoring-key"></a>Chave de autoria

Anteriormente chamada "Programática". Usado para ser o autor da app. Não utilizado para consultas de ponto final de nível de produção. Para mais informações, consulte [os limites-chave](luis-limits.md#key-limits).

## <a name="batch-test-json-file"></a><a name="batch-test-json-file"></a>Ficheiro JSON de teste de lote

O teste do lote é a capacidade de validar o modelo de uma aplicação LUIS atual com um conjunto de comentários consistente e conhecido de expressões de utilizadores. O teste do lote é definido num [ficheiro formatado JSON](luis-concept-batch-test.md#batch-file-format).

Veja também:
* [Conceitos](luis-concept-batch-test.md)
* [Como](luis-how-to-batch-test.md)
* [Tutorial](luis-tutorial-batch-testing.md)

## <a name="collaborator"></a><a name="collaborator"></a>Colaborador

Colaborador/colaborador, não é o [proprietário](#owner) da app, mas tem as mesmas permissões para adicionar, editar e apagar as intenções, entidades, expressões.

## <a name="contributor"></a><a name="contributor"></a>Contribuinte

Um colaborador é a mesma coisa que um [colaborador.](#collaborator)

## <a name="descriptor"></a><a name="descriptor"></a>Descritor

Um descritor é uma [característica](#features) aplicada a um modelo em tempo de formação, incluindo [listas](#phrase-list) de frases e [entidades.](#entity)

## <a name="domain"></a><a name="domain"></a>Domínio

No contexto LUIS, um **domínio** é uma área de conhecimento. O seu domínio é específico da sua área de conhecimento da sua aplicação. Esta pode ser uma área geral, como a app de agente de viagens. Uma aplicação de agente de viagens também pode ser específica apenas para as áreas de informação para a sua empresa, tais como localizações geográficas específicas, idiomas e serviços.

## <a name="endpoint"></a><a name="endpoint"></a>Ponto Final

O URL [de ponto final](https://go.microsoft.com/fwlink/?linkid=2092356) luis é onde você submete consultas LUIS após a [aplicação LUIS](#luis-app) ser autora e publicada. O URL final contém a região da aplicação publicada, bem como o ID da aplicação. Pode encontrar o ponto final na página **[de Chaves e pontos finais](luis-how-to-azure-subscription.md)** da sua aplicação, ou pode obter o URL final da API get [App Info.](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c37)

## <a name="entity"></a><a name="entity"></a>Entidade

[As entidades](luis-concept-entity-types.md) são palavras importantes em [expressões](luis-concept-utterance.md) que descrevem informações relevantes para a [intenção](luis-concept-intent.md), e por vezes são essenciais para ela. Uma entidade é essencialmente um tipo de dados no LUIS.

## <a name="f-measure"></a><a name="f-measure"></a>Medida F

Nos ensaios de [lote,](luis-interactive-test.md#batch-testing)uma medida da exatidão do ensaio.

## <a name="false-negative-fn"></a><a name="false-negative"></a>Falso negativo (FN)

Nos testes de [lote,](luis-interactive-test.md#batch-testing)os pontos de dados representam declarações nas quais a sua aplicação previu incorretamente a ausência da intenção/entidade alvo.

## <a name="false-positive-fp"></a><a name="false-positive"></a>Falso positivo (FP)

Nos testes de [lote,](luis-interactive-test.md#batch-testing)os pontos de dados representam declarações nas quais a sua aplicação previu incorretamente a existência da intenção/entidade alvo.

## <a name="features"></a><a name="features"></a>Funcionalidades

No machine learning, uma [característica](luis-concept-feature.md) é um traço distintivo ou atributo de dados que o seu sistema observa.

## <a name="intent"></a><a name="intent"></a>Intenção

Uma [intenção](luis-concept-intent.md) representa uma tarefa ou ação que o utilizador quer realizar. É um propósito ou objetivo expresso na entrada de um utilizador, como reservar um voo, pagar uma conta ou encontrar um artigo de notícias. No LUIS, a previsão das intenções baseia-se em toda a expressão. As entidades, por comparação, são peças de uma expressão.

## <a name="labeling"></a><a name="labeling"></a>Rotulagem

Rotulagem, ou marcação, é o processo de associar uma palavra ou frase na [expressão](#utterance) de uma intenção com uma [entidade](#entity) (datatype).

## <a name="luis-app"></a><a name="luis-app"></a>App LUIS

Uma aplicação LUIS é uma coleção de modelos linguísticos para processamento de linguagem natural, incluindo [intenções,](#intent) [entidades,](#entity)e [expressões](#utterance)rotuladas.

## <a name="owner"></a><a name="owner"></a>Proprietário

Cada aplicação tem um proprietário que é a pessoa que criou a app. O proprietário pode adicionar [colaboradores.](#collaborator)

## <a name="patterns"></a><a name="pattern"></a>Padrões
A função Padrão anterior é substituída por [Padrões](luis-concept-patterns.md). Utilize padrões para melhorar a precisão da previsão, fornecendo menos exemplos de treino.

## <a name="phrase-list"></a><a name="phrase-list"></a>Lista de frases

Uma lista de [frases](luis-concept-feature.md) inclui um grupo de valores (palavras ou frases) que pertencem à mesma classe e devem ser tratados da mesma forma (por exemplo, nomes de cidades ou produtos). Uma lista intercambiável é tratada como sinónimos.

## <a name="prebuilt-domain"></a><a name="prebuilt-domains"></a>Domínio pré-construído

Um [domínio pré-construído](luis-how-to-use-prebuilt-domains.md) é uma aplicação LUIS configurada para um domínio específico, como domótica (HomeAutomation) ou reservas de restaurantes (RestaurantReservation). As intenções, as declarações e as entidades estão configuradas para este domínio.

## <a name="prebuilt-entity"></a><a name="prebuilt-entity"></a>Entidade pré-construída

Uma [entidade pré-construída](luis-prebuilt-entities.md) é uma entidade que a LUIS fornece para tipos comuns de informação, como número, URL e e-mail. Opta por adicionar uma entidade pré-construída à sua aplicação.

## <a name="precision"></a><a name="precision"></a>Precisão
Nos testes de [lote,](luis-interactive-test.md#batch-testing)a precisão (também chamada de valor preditivo positivo) é a fração de expressões relevantes entre as expressões recuperadas.

## <a name="programmatic-key"></a><a name="programmatic-key"></a>Chave programática

Renomeado para [chave de autoria](#authoring-key).

## <a name="publish"></a><a name="publish"></a>Publicar

Publicação significa disponibilizar uma versão ativa luis na encenação ou [no ponto final](#endpoint)da produção.

## <a name="quota"></a><a name="quota"></a>Quota

A quota LUIS é a limitação do nível de [subscrição do Azure.](https://aka.ms/luis-price-tier) A quota LUIS pode ser limitada por ambos os pedidos por segundo (Http Status 429) e pelo total de pedidos num mês (Http Status 403).

## <a name="recall"></a><a name="recall"></a>Recordar
Nos testes de [lote,](luis-interactive-test.md#batch-testing)lembre-se (também conhecido como sensibilidade), é a capacidade de LUIS generalizar.

## <a name="semantic-dictionary"></a><a name="semantic-dictionary"></a>Dicionário semântico
Um dicionário semântico é fornecido na página da entidade lista, bem como na página da lista de frases. O dicionário semântico fornece sugestões de palavras com base no âmbito atual.

## <a name="sentiment-analysis"></a><a name="sentiment-analysis"></a>Análise de Sentimentos
A análise do sentimento fornece valores positivos ou negativos das expressões fornecidas pelo [Text Analytics](https://azure.microsoft.com/services/cognitive-services/text-analytics/).

## <a name="speech-priming"></a><a name="speech-priming"></a>Preparação do discurso

A preparação da fala permite que o seu serviço de fala seja preparado com o seu modelo LUIS.

## <a name="spelling-correction"></a><a name="spelling-correction"></a>Correção ortográfica

Ative o verificador de feitiços bing para corrigir palavras mal escritas nas palavras antes da previsão.

## <a name="starter-key"></a><a name="starter-key"></a>Chave de arranque

Uma chave gratuita para usar quando começar a usar o LUIS.

## <a name="structure"></a><a name="structure"></a>Estrutura

Adicione estrutura a uma entidade aprendida por máquinas para fornecer subcomponentes com descritores (características) e constrangimentos (expressão regular ou entidades de lista).

## <a name="subscription-key"></a><a name="subscription-key"></a>Chave de subscrição

A chave de subscrição é a chave final de **previsão** associada ao serviço LUIS [que criou no Azure.](luis-how-to-azure-subscription.md) Esta chave não é a chave de [autor.](#programmatic-key) Se tiver uma chave de ponto final, deve ser utilizada para quaisquer pedidos de ponto final em vez da chave de autor. Pode ver a sua chave de ponto final atual dentro do URL de ponto final na parte inferior de Keys e página [ **de pontos finais** ](luis-how-to-azure-subscription.md) no site [da LUIS.](luis-reference-regions.md) É o valor do **nome/par** de valor da chave de assinatura.

## <a name="test"></a><a name="test"></a>Teste

[Testar](luis-interactive-test.md#test-your-app) uma aplicação LUIS significa passar uma expressão ao LUIS e ver os resultados da JSON.

## <a name="timezone-offset"></a><a name="timezoneoffset"></a>Compensação do fuso horário

O ponto final inclui timezoneOffset. Este é o número em minutos que pretende adicionar ou remover da entidade pré-construída datetimeV2. Por exemplo, se a expressão for "a que horas é agora?", a data de dmissão do V2 é a hora atual para o pedido do cliente. Se o pedido do seu cliente vier de um bot ou de outra aplicação que não seja o mesmo que o utilizador do seu bot, deverá passar no offset entre o bot e o utilizador.

Consulte [alterar o fuso horário da entidade datav2 pré-construída](luis-concept-data-alteration.md?#change-time-zone-of-prebuilt-datetimev2-entity).

## <a name="token"></a><a name="token"></a>Certificado de
Um símbolo é a unidade mais pequena que pode ser rotulado numa entidade. A tokenização baseia-se na [cultura](luis-language-support.md#tokenization)da aplicação.

## <a name="train"></a><a name="train"></a>Preparar

A formação é o processo de ensino da LUIS sobre quaisquer alterações à versão ativa desde a última formação.

## <a name="true-negative-tn"></a><a name="true-negative"></a>Verdadeiro negativo (TN)

Nos testes de [lote,](luis-interactive-test.md#batch-testing)os pontos de dados representam declarações nas quais a sua aplicação previu corretamente a ausência da intenção/entidade alvo.

## <a name="true-positive-tp"></a><a name="true-positive"></a>Verdadeiro positivo (TP)

Nos testes de [lote,](luis-interactive-test.md#batch-testing)os pontos de dados representam declarações nas quais a sua aplicação previu corretamente a existência da intenção/entidade alvo.

## <a name="utterance"></a><a name="utterance"></a>Expressão

Uma expressão é uma frase de linguagem natural como "reservar 2 bilhetes para Seattle na próxima terça-feira". As declarações exemplosão adicionadas à intenção.

## <a name="version"></a><a name="version"></a>Versão

Uma [versão](luis-how-to-manage-versions.md) LUIS é um modelo de dados específico associado a um ID de aplicação LUIS e ao ponto final publicado. Cada aplicação LUIS tem pelo menos uma versão.
