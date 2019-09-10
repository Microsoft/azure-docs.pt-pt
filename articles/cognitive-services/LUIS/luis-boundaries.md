---
title: Limites-LUIS
titleSuffix: Azure Cognitive Services
description: Este artigo contém os limites de conhecidos do Azure Cognitive Services compreensão de idiomas (LUIS). LUIS tem várias áreas de limites. Limite de modelo controla intenções, entidades e recursos do LUIS. Limites de quota com base no tipo de chave. Combinação de teclado controla o Web site do LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: afa6c5e40918906eb9fe0e40ed633715e3f2741d
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70844793"
---
# <a name="boundaries-for-your-luis-model-and-keys"></a>Limites para o modelo e as chaves do LUIS
LUIS tem várias áreas de limites. A primeira é o [limites de modelo](#model-boundaries), que controla o intenções, entidades e recursos do LUIS. É a segunda área [limites de quota](#key-limits) com base no tipo de chave. É uma terceira área dos limites da [combinação de teclado](#keyboard-controls) para controlar o Web site do LUIS. É uma área de quarta a [mapeamento de região do mundo](luis-reference-regions.md) entre o LUIS criação de Web site e o LUIS [endpoint](luis-glossary.md#endpoint) APIs. 


## <a name="model-boundaries"></a>Limites do modelo

Se o seu aplicativo exceder os limites e limites do modelo LUIS, considere o uso de um aplicativo de [expedição Luis](luis-concept-enterprise.md#dispatch-tool-and-model) ou o uso de um [contêiner Luis](luis-container-howto.md). 

|Área|Limite|
|--|:--|
| [Nome da aplicação ][luis-get-started-create-app] | \* Máximo de carateres de predefinido |
| Aplicações| 500 aplicativos por recurso de criação do Azure |
| [Teste em lote][batch-testing]| conjuntos de dados de 10, expressões de 1000 por conjunto de dados|
| Lista explícita | 50 por aplicação|
| Entidades externas | sem limites |
| [Tentativas][intents]|500 por aplicativo: 499 tentativas personalizadas e a intenção _nenhuma_ necessária.<br>O aplicativo [baseado em expedição](https://aka.ms/dispatch-tool) tem fontes de expedição 500 correspondentes.|
| [Lista de entidades](./luis-concept-entity-types.md) | Primária 50, filho: 20.000 itens. É o nome canônico * predefinido máx. de caráter. Valores de sinónimos não ter nenhuma restrição de comprimento. |
| [Entidades e funções aprendidas por computador](./luis-concept-entity-types.md):<br> Spot<br>único<br>função de entidade|Um limite de entidades pai 100 ou de 330 entidades, o que limitará o usuário primeiro. Uma função conta como uma entidade para a finalidade desse limite. Um exemplo é uma composição com uma entidade simples que tem duas funções é: 1 composição + 1 simples + 2 funções = 4 das entidades 330.|
| [Visualização-entidades de lista dinâmica](https://aka.ms/luis-api-v3-doc#dynamic-lists-passed-in-at-prediction-time)|2 listas de ~ 1K por solicitação de ponto de extremidade de previsão de consulta|
| [Padrões de](luis-concept-patterns.md)|500 padrões por aplicação.<br>Comprimento máximo do padrão é de 400 caracteres.<br>3 Pattern.any entidades, por padrão<br>Máximo de 2 textos de opcionais aninhados no padrão|
| [Pattern.any](./luis-concept-entity-types.md)|100 por aplicação, 3 pattern.any entidades, por padrão |
| [Lista de frases][phrase-list]|10 de frase de listas, itens de 5000 por lista|
| [Entidades pré-concebidas](./luis-prebuilt-entities.md) | sem limite|
| [Entidades de expressão regular](./luis-concept-entity-types.md)|20 entidades<br>máximo de 500 carateres. Por padrão de entidade de expressão regular|
| [Funções](luis-concept-roles.md)|300 funções por aplicação. 10 funções por entidade|
| [Expressão][utterances] | 500 carateres|
| [Declarações][utterances] | 15.000 por aplicativo – não há limite para o número de declarações por tentativa|
| [Versões](luis-concept-version.md)| 100 versões por aplicativo |
| [Nome da versão][luis-how-to-manage-versions] | 10 caracteres restringidas de alfanuméricos e período (.) |

\* Máximo de carateres de predefinido é 50 carateres. 

<a name="intent-and-entity-naming"></a>

## <a name="object-naming"></a>Nomenclatura de objeto

Não use os seguintes caracteres nos nomes a seguir.

|Objeto|Excluir caracteres|
|--|--|
|Nomes de intenção, entidade e função|`:`<br>`$`|
|Nome da versão|`\`<br> `/`<br> `:`<br> `?`<br> `&`<br> `=`<br> `*`<br> `+`<br> `(`<br> `)`<br> `%`<br> `@`<br> `$`<br> `~`<br> `!`<br> `#`|

## <a name="key-usage"></a>Utilização de chaves

O entendimento da linguagem tem chaves separadas, um tipo para a criação e um tipo para consultar o ponto de extremidade de previsão. Para saber mais sobre as diferenças entre os tipos de chave, veja [criação e consulta de chaves de ponto de extremidade de previsão em Luis](luis-concept-keys.md).

<a name="key-limits"></a>

## <a name="resource-key-limits"></a>Limites de chave de recurso

As chaves de recurso têm limites diferentes para criação e ponto de extremidade. A chave do ponto de extremidade de consulta de previsão LUIS só é válida para consultas de ponto de extremidade. 

* 500 aplicativos por recurso de criação do Azure 

|Chave|Criação de conteúdos|Ponto Final|Objetivo|
|--|--|--|--|
|Inicial|1 milhão/mês, 5/segundo|1 mil/mês, 5/segundo|Criação da sua aplicação LUIS|
|F0-camada gratuita |1 milhão/mês, 5/segundo|10 mil/mês, 5/segundo|Consultar o ponto de final do LUIS|
|S0 – camada básica|-|50/segundo|Consultar o ponto de final do LUIS|
|S0 – camada padrão|-|50/segundo|Consultar o ponto de final do LUIS|
|[Integração de análise de sentimentos](luis-how-to-publish-app.md#enable-sentiment-analysis)|-|-|Adicionar informações de sentimentos, incluindo extração de dados de frase-chave, é fornecido sem a necessidade de outro recurso do Azure. |
|[Integração de fala](../speech-service/how-to-recognize-intents-from-speech-csharp.md)|-|1000 solicitações de ponto de extremidade por custo unitário|Converter a expressão falada em expressão de texto e devolvem os resultados de LUIS|

[Saiba mais sobre preços.][pricing]

## <a name="keyboard-controls"></a>Controles de teclado

|Entrada de teclado | Descrição | 
|--|--|
|Controle + E|Alterna entre tokens e de entidades na lista de expressões|

## <a name="website-sign-in-time-period"></a>Período de tempo de entrada do site

O acesso de início de sessão destina-se **60 minutos**. Após este período de tempo, irá obter este erro. Você precisa entrar novamente.

[luis-get-started-create-app]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app
[batch-testing]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-test#batch-testing
[intents]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-intent
[phrase-list]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-feature
[utterances]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-utterance
[luis-how-to-manage-versions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-manage-versions
[pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
<!-- TBD: fix this link -->
[speech-to-intent-pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
