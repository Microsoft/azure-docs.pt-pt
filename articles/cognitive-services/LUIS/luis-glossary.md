---
title: Glossário-LUIS
titleSuffix: Azure Cognitive Services
description: O Glossário explica os termos que você pode encontrar enquanto trabalha com o serviço de API do LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 22e8fa8fb6999828076ea5f8f34b1f601b920013
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499557"
---
# <a name="language-understanding-glossary-of-common-vocabulary-and-concepts"></a>Glossário de compreensão da linguagem de vocabulário e conceitos comuns
O Glossário de Reconhecimento vocal (LUIS) explica os termos que você pode encontrar enquanto trabalha com o serviço de API do LUIS.

## <a name="active-version"></a>Versão ativa

A versão do Active LUIS é a versão que recebe todas as alterações no modelo. No portal do [Luis](luis-reference-regions.md) , se você quiser fazer alterações em uma versão que não seja a versão ativa, primeiro você precisará definir essa versão como ativa.

## <a name="authoring"></a>Criação

A criação é a capacidade de criar, gerenciar e implantar um [aplicativo Luis](#luis-app), usando o portal do [Luis](luis-reference-regions.md) ou as [APIs de criação](https://go.microsoft.com/fwlink/?linkid=2092087).

## <a name="authoring-key"></a>Chave de criação

Anteriormente chamada de chave "programática". Usado para criar o aplicativo. Não usado para consultas de ponto de extremidade de nível de produção. Para obter mais informações, consulte [limites de chave](luis-boundaries.md#key-limits).   

## <a name="batch-test-json-file"></a>Arquivo JSON de texto em lotes

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

No contexto LUIS, um **domínio** é uma área de conhecimento. Seu domínio é específico para a área de conhecimento de seu aplicativo. Isso pode ser uma área geral, como o aplicativo do agente de viagem. Um aplicativo de agente de viagem também pode ser específico apenas para as áreas de informações de sua empresa, como locais geográficos, idiomas e serviços específicos.

## <a name="endpoint"></a>Extremidade

A URL do [ponto de extremidade Luis](https://go.microsoft.com/fwlink/?linkid=2092356) é onde você envia consultas Luis depois que o [aplicativo Luis](#luis-app) é criado e publicado. A URL do ponto de extremidade contém a região do aplicativo publicado, bem como a ID do aplicativo. Você pode encontrar o ponto de extremidade na página **[chaves e pontos](luis-how-to-azure-subscription.md)** de extremidade do seu aplicativo, ou pode obter a URL do ponto de extremidade da API [obter informações de aplicativo](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c37) .

## <a name="entity"></a>Entidade

As [entidades](luis-concept-entity-types.md) são palavras importantes no [declarações](luis-concept-utterance.md) que descrevem as informações relevantes para a [intenção](luis-concept-intent.md)e, às vezes, são essenciais para ela. Uma entidade é essencialmente um tipo de dados em LUIS.

## <a name="f-measure"></a>Medida F

No [teste em lotes](luis-interactive-test.md#batch-testing), uma medida da precisão do teste.

## <a name="false-negative"></a>Falso negativo (FN)

No [teste em lotes](luis-interactive-test.md#batch-testing), os pontos de dados representam declarações em que seu aplicativo incorretamente prevê a ausência da intenção/entidade de destino.

## <a name="false-positive"></a>Falso positivo (FP)

No [teste em lotes](luis-interactive-test.md#batch-testing), os pontos de dados representam declarações em que seu aplicativo incorretamente prevê a existência da entidade/intenção de destino.

## <a name="features"></a>Features

No Machine Learning, um [recurso](luis-concept-feature.md) é uma característica ou atributo diferenciado dos dados que seu sistema observa.

## <a name="intent"></a>Tentativa

Uma [intenção](luis-concept-intent.md) representa uma tarefa ou ação que o usuário deseja executar. É uma finalidade ou meta expressa na entrada de um usuário, como a reserva de um vôo, o pagamento de uma conta ou a localização de um artigo de notícias. No LUIS, a previsão de intenção se baseia em toda a expressão. As entidades, por comparação, são partes de um expressão.

## <a name="labeling"></a>Rotulagem

Rotular, ou marcar, é o processo de associar uma palavra ou frase no [expressão](#utterance) de uma intenção a uma [entidade](#entity) (DataType).

## <a name="luis-app"></a>Aplicativo LUIS

Um aplicativo LUIS é uma coleção de modelos de linguagem para processamento de linguagem natural, incluindo [intenções](#intent), [entidades](#entity)e rotulada [declarações](#utterance).

## <a name="owner"></a>Proprietário

Cada aplicativo tem um proprietário que é a pessoa que criou o aplicativo. O proprietário pode adicionar [colaboradores](#collaborator).

## <a name="pattern"></a>Padrões
O recurso de padrão anterior é substituído por [padrões](luis-concept-patterns.md). Use padrões para melhorar a precisão da previsão fornecendo menos exemplos de treinamento.

## <a name="phrase-list"></a>Lista de frases

Uma [lista de frases](luis-concept-feature.md) inclui um grupo de valores (palavras ou frases) que pertencem à mesma classe e deve ser tratada da mesma forma (por exemplo, nomes de cidades ou produtos). Uma lista intercambiável é tratada como sinônimos.

## <a name="prebuilt-domains"></a>Domínio predefinido

Um [domínio predefinido](luis-how-to-use-prebuilt-domains.md) é um aplicativo Luis configurado para um domínio específico, como a automação inicial (HomeAutomation) ou reservas de restaurante (RestaurantReservation). As intenções, declarações e entidades são configuradas para esse domínio.

## <a name="prebuilt-entity"></a>Entidade predefinida

Uma [entidade predefinida](luis-prebuilt-entities.md) é uma entidade que o Luis fornece para tipos comuns de informações, como número, URL e email. Você opta por adicionar uma entidade predefinida ao seu aplicativo.

## <a name="precision"></a>Preciso
No [teste em lotes](luis-interactive-test.md#batch-testing), Precision (também chamado de valor de previsão positivo) é a fração de declarações relevantes entre o declarações recuperado.

## <a name="programmatic-key"></a>Chave programática

Renomeado para a [chave de criação](#authoring-key).

## <a name="publish"></a>Publicou

Publicação significa tornar uma versão LUIS ativa disponível no [ponto de extremidade](#endpoint)de preparo ou de produção.  

## <a name="quota"></a>Cota

A cota de LUIS é a limitação da [camada de assinatura do Azure](https://aka.ms/luis-price-tier). A cota LUIS pode ser limitada por ambas as solicitações por segundo (status HTTP 429) e total de solicitações em um mês (status HTTP 403).

## <a name="recall"></a>Vimos
No [teste em lotes](luis-interactive-test.md#batch-testing), Recall (também conhecido como sensibilidade) é a capacidade de Luis generalizar.

## <a name="semantic-dictionary"></a>Dicionário semântico
Um dicionário semântico é fornecido na página de entidade da lista, bem como na página da lista de frases. O dicionário semântico fornece sugestões de palavras com base no escopo atual.

## <a name="sentiment-analysis"></a>Análise de Sentimento
A análise de sentimentos fornece valores positivos ou negativos do declarações fornecido pelo [análise de texto](https://azure.microsoft.com/services/cognitive-services/text-analytics/).

## <a name="speech-priming"></a>Espriming de fala

A esplicação de fala permite que seu serviço de fala seja orientado com seu modelo LUIS.

## <a name="spelling-correction"></a>Correção ortográfica

Habilite o verificador ortográfico do Bing para corrigir palavras incorretas no declarações antes da previsão.

## <a name="starter-key"></a>Chave de início

Uma chave gratuita a ser usada ao iniciar pela primeira vez usando LUIS.

## <a name="structure"></a>Estruturá

Adicione estrutura a uma entidade aprendida por máquina para fornecer subcomponentes com descritores (recursos) e restrições (entidades regulares ou de lista).

## <a name="subscription-key"></a>Chave de assinatura

A chave de assinatura é a chave de **ponto de extremidade de previsão** associada ao serviço Luis [que você criou no Azure](luis-how-to-azure-subscription.md). Essa chave não é a [chave de criação](#programmatic-key). Se você tiver uma chave de ponto de extremidade, ela deverá ser usada para qualquer solicitação de ponto de extremidade em vez da chave de criação. Você pode ver sua chave de ponto de extremidade atual dentro da URL do ponto de extremidade na parte inferior da página de [ **chaves e pontos de extremidades** ](luis-how-to-azure-subscription.md) no site [Luis](luis-reference-regions.md) . É o valor do par nome/valor da **chave da assinatura** .

## <a name="test"></a>Testar

[Testar](luis-interactive-test.md#test-your-app) um aplicativo Luis significa passar um expressão para Luis e exibir os resultados JSON.

## <a name="timezoneoffset"></a>Deslocamento do fuso horário

O ponto de extremidade inclui timezoneOffset. Esse é o número em minutos que você deseja adicionar ou remover da entidade datetimeV2 predefinida. Por exemplo, se o expressão for "Qual é a hora agora?", o datetimeV2 retornado será a hora atual para a solicitação do cliente. Se a solicitação do cliente for proveniente de um bot ou de outro aplicativo que não seja o mesmo que o usuário do bot, você deverá passar o deslocamento entre o bot e o usuário.

Consulte [alterar o fuso horário da entidade datetimeV2 predefinida](luis-concept-data-alteration.md?#change-time-zone-of-prebuilt-datetimev2-entity).

## <a name="token"></a>Token
Um token é a menor unidade que pode ser rotulada em uma entidade. A geração de tokens é baseada na [cultura](luis-language-support.md#tokenization)do aplicativo.

## <a name="train"></a>Trem

O treinamento é o processo de ensinar LUIS sobre quaisquer alterações na versão ativa desde o último treinamento.

## <a name="true-negative"></a>Verdadeiro negativo (TN)

No [teste em lotes](luis-interactive-test.md#batch-testing), os pontos de dados representam declarações em que seu aplicativo previu corretamente a ausência da intenção/entidade de destino.

## <a name="true-positive"></a>Verdadeiro positivo (TP)

No [teste em lotes](luis-interactive-test.md#batch-testing), os pontos de dados representam declarações em que seu aplicativo prevê corretamente a existência da entidade/intenção de destino.

## <a name="utterance"></a>Expressão

Um expressão é uma frase de idioma natural, como "tíquetes 2 para Seattle na próxima terça-feira". Os declarações de exemplo são adicionados à intenção.

## <a name="version"></a>Versão

Uma [versão](luis-how-to-manage-versions.md) do Luis é um modelo de dados específico associado a uma ID do aplicativo Luis e o ponto de extremidade publicado. Cada aplicativo LUIS tem pelo menos uma versão.
