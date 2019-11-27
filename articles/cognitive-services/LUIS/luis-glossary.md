---
title: Glossário-LUIS
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
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325951"
---
# <a name="language-understanding-glossary-of-common-vocabulary-and-concepts"></a>Glossário de compreensão da linguagem de vocabulário e conceitos comuns
A compreensão de idiomas (LUIS) explica de glossário de termos que poderá encontrar ao trabalhar com o serviço de API de LUIS.

## <a name="active-version"></a>Versão ativa

A versão de LUIS Active Directory é a versão que recebe todas as alterações ao modelo. No portal do [Luis](luis-reference-regions.md) , se você quiser fazer alterações em uma versão que não seja a versão ativa, primeiro você precisará definir essa versão como ativa.

## <a name="authoring"></a>Criação

A criação é a capacidade de criar, gerenciar e implantar um [aplicativo Luis](#luis-app), usando o portal do [Luis](luis-reference-regions.md) ou as [APIs de criação](https://go.microsoft.com/fwlink/?linkid=2092087).

## <a name="authoring-key"></a>Chave de criação

Anteriormente denominado "Programmatic" chave. Usada para criar a aplicação. Não é utilizado para consultas de ponto final de nível de produção. Para obter mais informações, consulte [limites de chave](luis-boundaries.md#key-limits).

## <a name="batch-test-json-file"></a>Arquivo JSON de teste em lote

O teste em lotes é a capacidade de validar um modelo de aplicativo LUIS atual com um conjunto de teste consistente e conhecido de declarações de usuário. O teste em lotes é definido em um [arquivo formatado em JSON](luis-concept-batch-test.md#batch-file-format).

Veja também:
* [Conceitos](luis-concept-batch-test.md)
* [Instruções](luis-how-to-batch-test.md)
* [Tutorial](luis-tutorial-batch-testing.md)

## <a name="collaborator"></a>Colaborador

Um colaborador/colaborador, não é o [proprietário](#owner) do aplicativo, mas tem as mesmas permissões para adicionar, editar e excluir as intenções, entidades, declarações.

## <a name="contributor"></a>Simplifica

Um colaborador é a mesma coisa que um [colaborador.](#collaborator)

## <a name="descriptor"></a>Descritor

Um descritor é um [recurso](#features) aplicado a um modelo no tempo de treinamento, incluindo [listas de frases](#phrase-list) e [entidades](#entity). 

## <a name="domain"></a>Controlador

No contexto LUIS, um **domínio** é uma área de conhecimento. O domínio é específico para a sua área de aplicação de dados de conhecimento. Isso pode ser uma área geral como a aplicação de agente de viagens. Uma aplicação de agente de viagens também pode ser específica para apenas as áreas de informações da sua empresa, tais como locais geográficos específicos de linguagens e serviços.

## <a name="endpoint"></a>Extremidade

A URL do [ponto de extremidade Luis](https://go.microsoft.com/fwlink/?linkid=2092356) é onde você envia consultas Luis depois que o [aplicativo Luis](#luis-app) é criado e publicado. O URL de ponto final contém a região da aplicação publicada, bem como o ID da aplicação. Você pode encontrar o ponto de extremidade na página **[chaves e pontos](luis-how-to-azure-subscription.md)** de extremidade do seu aplicativo, ou pode obter a URL do ponto de extremidade da API [obter informações de aplicativo](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c37) .

## <a name="entity"></a>Entidade

As [entidades](luis-concept-entity-types.md) são palavras importantes no [declarações](luis-concept-utterance.md) que descrevem as informações relevantes para a [intenção](luis-concept-intent.md)e, às vezes, são essenciais para ela. Uma entidade é essencialmente um tipo de dados no LUIS.

## <a name="f-measure"></a>Medida F

No [teste em lotes](luis-interactive-test.md#batch-testing), uma medida da precisão do teste.

## <a name="false-negative"></a>Falso negativo (FN)

No [teste em lotes](luis-interactive-test.md#batch-testing), os pontos de dados representam declarações em que seu aplicativo incorretamente prevê a ausência da intenção/entidade de destino.

## <a name="false-positive"></a>Falso positivo (FP)

No [teste em lotes](luis-interactive-test.md#batch-testing), os pontos de dados representam declarações em que seu aplicativo incorretamente prevê a existência da entidade/intenção de destino.

## <a name="features"></a>Features

No Machine Learning, um [recurso](luis-concept-feature.md) é uma característica ou atributo diferenciado dos dados que seu sistema observa.

## <a name="intent"></a>Tentativa

Uma [intenção](luis-concept-intent.md) representa uma tarefa ou ação que o usuário deseja executar. É um objetivo ou objetivo expressado numa entrada de usuário, como reserva um vôo, pagando uma fatura ou localizar um artigo de notícias. No LUIS, a predição de intenção baseia-se a expressão inteira. As entidades, por comparação, são partes de uma expressão.

## <a name="labeling"></a>Rotulagem

Rotular, ou marcar, é o processo de associar uma palavra ou frase no [expressão](#utterance) de uma intenção a uma [entidade](#entity) (DataType).

## <a name="luis-app"></a>Aplicativo LUIS

Um aplicativo LUIS é uma coleção de modelos de linguagem para processamento de linguagem natural, incluindo [intenções](#intent), [entidades](#entity)e rotulada [declarações](#utterance).

## <a name="owner"></a>Proprietário

Cada aplicação tem um proprietário que é a pessoa que criou a aplicação. O proprietário pode adicionar [colaboradores](#collaborator).

## <a name="pattern"></a>Padrões
O recurso de padrão anterior é substituído por [padrões](luis-concept-patterns.md). Utilize padrões para melhorar a exatidão da previsão, fornecendo exemplos de treinamento menos.

## <a name="phrase-list"></a>Lista de frases

Uma [lista de frases](luis-concept-feature.md) inclui um grupo de valores (palavras ou frases) que pertencem à mesma classe e deve ser tratada da mesma forma (por exemplo, nomes de cidades ou produtos). Uma lista intercambiável é tratada como sinónimos.

## <a name="prebuilt-domains"></a>Domínio predefinido

Um [domínio predefinido](luis-how-to-use-prebuilt-domains.md) é um aplicativo Luis configurado para um domínio específico, como a automação inicial (HomeAutomation) ou reservas de restaurante (RestaurantReservation). Os objetivos, expressões e entidades estão configuradas para este domínio.

## <a name="prebuilt-entity"></a>Entidade predefinida

Uma [entidade predefinida](luis-prebuilt-entities.md) é uma entidade que o Luis fornece para tipos comuns de informações, como número, URL e email. Optar por adicionar uma entidade pré-criados à sua aplicação.

## <a name="precision"></a>Preciso
No [teste em lotes](luis-interactive-test.md#batch-testing), Precision (também chamado de valor de previsão positivo) é a fração de declarações relevantes entre o declarações recuperado.

## <a name="programmatic-key"></a>Chave programática

Renomeado para a [chave de criação](#authoring-key).

## <a name="publish"></a>Publicou

Publicação significa tornar uma versão LUIS ativa disponível no [ponto de extremidade](#endpoint)de preparo ou de produção.  

## <a name="quota"></a>Cota

A cota de LUIS é a limitação da [camada de assinatura do Azure](https://aka.ms/luis-price-tier). A quota de LUIS pode ser limitada por ambos os pedidos por segundo (estado de HTTP 429) e o total de pedidos por mês (estado de HTTP 403).

## <a name="recall"></a>Vimos
No [teste em lotes](luis-interactive-test.md#batch-testing), Recall (também conhecido como sensibilidade) é a capacidade de Luis generalizar.

## <a name="semantic-dictionary"></a>Dicionário semântico
Um dicionário semântico é fornecido a página de lista de entidades, bem como a página de lista de frase. O dicionário semântico fornece sugestões de palavras com base no âmbito atual.

## <a name="sentiment-analysis"></a>Análise de Sentimento
A análise de sentimentos fornece valores positivos ou negativos do declarações fornecido pelo [análise de texto](https://azure.microsoft.com/services/cognitive-services/text-analytics/).

## <a name="speech-priming"></a>Espriming de fala

Priming de fala permite que o seu serviço de voz para ser preparados com o seu modelo do LUIS.

## <a name="spelling-correction"></a>Correção ortográfica

Ative o corretor ortográfico Bing corrigir palavras com erros ortográficos nas expressões antes de predição.

## <a name="starter-key"></a>Chave de início

Uma chave gratuita a ser usada ao iniciar pela primeira vez usando LUIS.

## <a name="structure"></a>Estruturá

Adicione estrutura a uma entidade aprendida por máquina para fornecer subcomponentes com descritores (recursos) e restrições (entidades regulares ou de lista).

## <a name="subscription-key"></a>Chave de assinatura

A chave de assinatura é a chave de **ponto de extremidade de previsão** associada ao serviço Luis [que você criou no Azure](luis-how-to-azure-subscription.md). Essa chave não é a [chave de criação](#programmatic-key). Se tiver uma chave de ponto de extremidade, ele deve ser usado para todos os pedidos de ponto final em vez da chave de criação. Você pode ver sua chave de ponto de extremidade atual dentro da URL do ponto de extremidade na parte inferior da página de [ **chaves e pontos de extremidades** ](luis-how-to-azure-subscription.md) no site [Luis](luis-reference-regions.md) . É o valor do par nome/valor da **chave da assinatura** .

## <a name="test"></a>Testar

[Testar](luis-interactive-test.md#test-your-app) um aplicativo Luis significa passar um expressão para Luis e exibir os resultados JSON.

## <a name="timezoneoffset"></a>Deslocamento do fuso horário

O ponto final inclui timezoneOffset. Este é o número de minutos que pretende adicionar ou remover o datetimeV2 entidade pré-criados. Por exemplo, se a expressão for "que altura é agora?", datetimeV2 devolvido é a hora atual para o pedido do cliente. Se o seu pedido de cliente é proveniente de um bot ou outro aplicativo que não é o mesmo como usuário de seu bot, deve passar o deslocamento entre o bot e o utilizador.

Consulte [alterar o fuso horário da entidade datetimeV2 predefinida](luis-concept-data-alteration.md?#change-time-zone-of-prebuilt-datetimev2-entity).

## <a name="token"></a>Token
Um token é a menor unidade que pode ser identificada numa entidade. A geração de tokens é baseada na [cultura](luis-language-support.md#tokenization)do aplicativo.

## <a name="train"></a>Trem

O treinamento é o processo de ensinar LUIS sobre quaisquer alterações na versão ativa desde o último treinamento.

## <a name="true-negative"></a>Verdadeiro negativo (TN)

No [teste em lotes](luis-interactive-test.md#batch-testing), os pontos de dados representam declarações em que seu aplicativo previu corretamente a ausência da intenção/entidade de destino.

## <a name="true-positive"></a>Verdadeiro positivo (TP)

No [teste em lotes](luis-interactive-test.md#batch-testing), os pontos de dados representam declarações em que seu aplicativo prevê corretamente a existência da entidade/intenção de destino.

## <a name="utterance"></a>Expressão

Uma expressão é uma expressão de linguagem natural, como "permissões de livro 2 para Seattle Terça-feira seguinte". Expressões com de exemplo são adicionados à intenção.

## <a name="version"></a>Versão

Uma [versão](luis-how-to-manage-versions.md) do Luis é um modelo de dados específico associado a uma ID do aplicativo Luis e o ponto de extremidade publicado. Todas as aplicações de LUIS têm, pelo menos, uma versão.
