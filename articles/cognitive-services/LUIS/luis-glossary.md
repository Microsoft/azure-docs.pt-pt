---
title: Glossário
titleSuffix: Language Understanding - Azure Cognitive Services
description: O glossário de termos de explica que poderá encontrar ao trabalhar com o serviço de API de LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: ced83b83c2d64ba5c4816f378c66dae9f4210391
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60193633"
---
# <a name="language-understanding-glossary-of-common-vocabulary-and-concepts"></a>Glossário de compreensão de linguagem de vocabulário comum e conceitos
A compreensão de idiomas (LUIS) explica de glossário de termos que poderá encontrar ao trabalhar com o serviço de API de LUIS.

## <a name="active-version"></a>Versão do Active Directory

A versão de LUIS Active Directory é a versão que recebe todas as alterações ao modelo. Na [LUIS](luis-reference-regions.md) Web site, se pretender efetuar alterações a uma versão que não é a versão do Active Directory, precisa primeiro definir essa versão como ativa.

## <a name="authoring"></a>Criação

A criação é a capacidade de criar, gerir e implementar um [aplicação LUIS](#luis-app), usando o [LUIS](luis-reference-regions.md) Web site ou o [APIs de criação](https://aka.ms/luis-authoring-api).

## <a name="authoring-key"></a>Chave de criação

Anteriormente denominado "Programmatic" chave. Usada para criar a aplicação. Não é utilizado para consultas de ponto final de nível de produção. Para obter mais informações, consulte [limites de chaves](luis-boundaries.md#key-limits).   

## <a name="batch-test-json-file"></a>Ficheiro JSON de texto de batch

O ficheiro de batch é uma matriz JSON. Cada elemento na matriz tem três propriedades: `text`, `intent`, e `entities`. O `entities` propriedade é uma matriz. A matriz pode estar vazia. Se o `entities` matriz não está vazia, esta tem de identificar com precisão as entidades.

```JSON
[
    {
        "text": "drive me home",
        "intent": "None",
        "entities": []
    },
    {
        "text": "book a flight to orlando on the 25th",
        "intent": "BookFlight",
        "entities": [
            {
                "entity": "orlando",
                "type": "Location",
                "startIndex": 18,
                "endIndex": 25
            }
        ]
    }
]

```


## <a name="collaborator"></a>Funcionário

Não é um colaborador a [proprietário](#owner) da aplicação, mas tem as mesmas permissões para adicionar, editar e eliminar os objetivos, entidades, expressões com.

## <a name="currently-editing"></a>Atualmente a editar

Mesmo que [versão Active Directory](#active-version)

## <a name="domain"></a>Domínio

No contexto do LUIS, um **domínio** é uma área de dados de conhecimento. O domínio é específico para a sua área de aplicação de dados de conhecimento. Isso pode ser uma área geral como a aplicação de agente de viagens. Uma aplicação de agente de viagens também pode ser específica para apenas as áreas de informações da sua empresa, tais como locais geográficos específicos de linguagens e serviços.

## <a name="endpoint"></a>Ponto final

O [ponto final de LUIS](https://aka.ms/luis-endpoint-apis) URL é onde enviar consultas de LUIS após o [aplicação LUIS](#luis-app) é criou e publicou. O URL de ponto final contém a região da aplicação publicada, bem como o ID da aplicação. Pode encontrar o ponto final sobre o **[chaves e os pontos finais](luis-how-to-azure-subscription.md)** página da sua aplicação, ou pode obter o URL de ponto final da [obter as informações da aplicação](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c37) API.

Um ponto de extremidade de exemplo é semelhante a:

`https://<region>.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>?subscription-key=<subscriptionID>&verbose=true&timezoneOffset=0&q=<utterance>`

|Parâmetro de cadeia de consulta|descrição|
|--|--|
|Região| [região publicado](luis-reference-regions.md#publishing-regions) |
|appID | ID da aplicação LUIS |
|subscriptionID | Chave de ponto final (subscrição) do LUIS criada no portal do Azure |
|p | Expressão |
|timezoneOffset| minutes|

## <a name="entity"></a>Entidade

[Entidades](luis-concept-entity-types.md) são palavras importantes [expressões com](luis-concept-utterance.md) que descrevem informações relevantes para o [intenção](luis-concept-intent.md), e, às vezes, são essenciais para ele. Uma entidade é essencialmente um tipo de dados no LUIS.

## <a name="f-measure"></a>Medida de F

Na [testes de batch](luis-interactive-test.md#batch-testing), uma medida de precisão do teste.

## <a name="false-negative"></a>Falso negativo (TN)

Na [testes de batch](luis-interactive-test.md#batch-testing), os pontos de dados representam expressões em que a aplicação previsto incorretamente a ausência da intenção/entidade de destino.

## <a name="false-positive"></a>Falso positivo (TP)

Na [testes de batch](luis-interactive-test.md#batch-testing), os pontos de dados representam expressões em que a aplicação previsto incorretamente a existência da intenção/entidade de destino.

## <a name="features"></a>Funcionalidades

No machine learning, um [funcionalidade](luis-concept-feature.md) é uma distinção característica ou atributo de dados que observa o seu sistema.

## <a name="intent"></a>Intenção

Uma [intenção](luis-concept-intent.md) representa uma tarefa ou ação que o usuário quer executar. É um objetivo ou objetivo expressado numa entrada de usuário, como reserva um vôo, pagando uma fatura ou localizar um artigo de notícias. No LUIS, a predição de intenção baseia-se a expressão inteira. As entidades, por comparação, são partes de uma expressão.

## <a name="labeling"></a>Etiquetagem

Etiquetagem é o processo de associação de uma palavra ou frase numa intenção [expressão](#utterance) com um [entidade](#entity) (tipo de dados).

## <a name="luis-app"></a>Aplicação LUIS

Uma aplicação do LUIS é um modelo de dados treinados para processamento de linguagem natural, incluindo [intenções](#intent), [entidades](#entity)e etiquetados [expressões com](#utterance).

## <a name="owner"></a>Proprietário

Cada aplicação tem um proprietário que é a pessoa que criou a aplicação. Pode adicionar o proprietário [colaboradores](#collaborator).

## <a name="pattern"></a>Padrões de
A funcionalidade de padrão anterior é substituída pela [padrões](luis-concept-patterns.md). Utilize padrões para melhorar a exatidão da previsão, fornecendo exemplos de treinamento menos.

## <a name="phrase-list"></a>Lista de frase

R [lista de frase](luis-concept-feature.md#what-is-a-phrase-list-feature) inclui um grupo de valores (palavras ou frases) que pertencem à mesma classe e deve ser tratado de maneira semelhante (por exemplo, nomes de cidades ou produtos). Uma lista intercambiável é tratada como sinónimos.

## <a name="prebuilt-domains"></a>Domínio pré-criado

R [domínio pré-criado](luis-how-to-use-prebuilt-domains.md) é uma aplicação do LUIS configurada para um domínio específico, como automação residencial (HomeAutomation) ou reservas de restaurante (RestaurantReservation). Os objetivos, expressões e entidades estão configuradas para este domínio.

## <a name="prebuilt-entity"></a>Entidade pré-criados

R [criados previamente entidade](luis-prebuilt-entities.md) é uma entidade, LUIS, fornece para tipos comuns de informações como número, o URL e o e-mail. Optar por adicionar uma entidade pré-criados à sua aplicação.

## <a name="precision"></a>Precisão
Na [testes de batch](luis-interactive-test.md#batch-testing), precisão (também chamado de valor de previsão positivo) é a fração de expressões com relevantes entre as expressões obtidas.

## <a name="programmatic-key"></a>Chave programática

Nome mudado para [chave de criação](#authoring-key).

## <a name="publish"></a>Publicar

Publicar significa fazer um LUIS [versão active](#active-version) disponível no teste ou produção [endpoint](#endpoint).  

## <a name="quota"></a>Quota

Quota de LUIS é a limitação do [escalão de subscrição do Azure](https://aka.ms/luis-price-tier). A quota de LUIS pode ser limitada por ambos os pedidos por segundo (estado de HTTP 429) e o total de pedidos por mês (estado de HTTP 403).

## <a name="recall"></a>Lembre-se
Na [testes de batch](luis-interactive-test.md#batch-testing), lembre-se (também conhecido como sensibilidade), que é a capacidade de LUIS generalizar.

## <a name="semantic-dictionary"></a>Dicionário semântico
Um dicionário semântico é fornecido a página de lista de entidades, bem como a página de lista de frase. O dicionário semântico fornece sugestões de palavras com base no âmbito atual.

## <a name="sentiment-analysis"></a>Análise de sentimentos
Análise de sentimentos fornece valores positivos ou negativos de expressões fornecidas pela [análise de texto](https://azure.microsoft.com/services/cognitive-services/text-analytics/).

## <a name="speech-priming"></a>Priming de voz

Priming de fala permite que o seu serviço de voz para ser preparados com o seu modelo do LUIS.

## <a name="spelling-correction"></a>Correção ortográfica

Ative o corretor ortográfico Bing corrigir palavras com erros ortográficos nas expressões antes de predição.

## <a name="starter-key"></a>Chave de arranque

Mesmo que [chave programática](#programmatic-key), nome mudado para a chave de criação de conteúdos.

## <a name="subscription-key"></a>Chave de subscrição

A chave de subscrição é o **ponto final** chave associado ao serviço de LUIS [que criou no Azure](luis-how-to-azure-subscription.md). Esta chave não é o [chave de criação](#programmatic-key). Se tiver uma chave de ponto de extremidade, ele deve ser usado para todos os pedidos de ponto final em vez da chave de criação. Pode ver a sua chave de ponto final atual dentro do URL do ponto final na parte inferior [ **as chaves e os pontos finais** página](luis-how-to-azure-subscription.md) na [LUIS](luis-reference-regions.md) Web site. É o valor de **chave de subscrição** par de nome/valor.

## <a name="test"></a>Teste

[Teste](luis-interactive-test.md#test-your-app) aplicação LUIS significa passar uma expressão para LUIS e ver o JSON de resultados.

## <a name="timezoneoffset"></a>Desvio de fuso horário

O ponto final inclui timezoneOffset. Este é o número de minutos que pretende adicionar ou remover o datetimeV2 entidade pré-criados. Por exemplo, se a expressão for "que altura é agora?", datetimeV2 devolvido é a hora atual para o pedido do cliente. Se o seu pedido de cliente é proveniente de um bot ou outro aplicativo que não é o mesmo como usuário de seu bot, deve passar o deslocamento entre o bot e o utilizador.

Ver [altere o fuso horário da entidade de datetimeV2 pré-criados](luis-concept-data-alteration.md?#change-time-zone-of-prebuilt-datetimev2-entity).

## <a name="token"></a>Token
Um token é a menor unidade que pode ser identificada numa entidade. Atomização baseia-se o aplicativo [cultura](luis-language-support.md#tokenization).

## <a name="train"></a>Train

Treinamento é o processo de ensinar o LUIS sobre quaisquer alterações feitas a [Active Directory versão](#active-version) desde o último treinamento.

## <a name="true-negative"></a>Verdadeiro negativo (TN)

Na [testes de batch](luis-interactive-test.md#batch-testing), os pontos de dados representam expressões em que a aplicação previsto corretamente a ausência da intenção/entidade de destino.

## <a name="true-positive"></a>Positivo verdadeiro (TP)

Na [testes de batch](luis-interactive-test.md#batch-testing), os pontos de dados representam expressões em que a aplicação previsto corretamente a existência da intenção/entidade de destino.

## <a name="utterance"></a>Expressão

Uma expressão é uma expressão de linguagem natural, como "permissões de livro 2 para Seattle Terça-feira seguinte". Expressões com de exemplo são adicionados à intenção.

## <a name="version"></a>Versão

Um LUIS [versão](luis-how-to-manage-versions.md) é um modelo de dados específicos associado a um ID de aplicação do LUIS e o ponto de extremidade publicado. Todas as aplicações de LUIS têm, pelo menos, uma versão.
