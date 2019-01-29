---
title: Limites
titleSuffix: Language Understanding - Azure Cognitive Services
description: Este artigo contém os limites de conhecidos do Azure Cognitive Services compreensão de idiomas (LUIS). LUIS tem várias áreas de limites. Limite de modelo controla intenções, entidades e recursos do LUIS. Limites de quota com base no tipo de chave. Combinação de teclado controla o Web site do LUIS.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 01/28/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 592b8500f9b032a20bb991326201d45db586980d
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55177530"
---
# <a name="boundaries-for-your-luis-model-and-keys"></a>Limites para o seu modelo do LUIS e chaves
LUIS tem várias áreas de limites. A primeira é o [limites de modelo](#model-boundaries), que controla o intenções, entidades e recursos do LUIS. É a segunda área [limites de quota](#key-limits) com base no tipo de chave. É uma terceira área dos limites da [combinação de teclado](#keyboard-controls) para controlar o Web site do LUIS. É uma área de quarta a [mapeamento de região do mundo](luis-reference-regions.md) entre o LUIS criação de Web site e o LUIS [endpoint](luis-glossary.md#endpoint) APIs. 


## <a name="model-boundaries"></a>Limites do modelo

|Área|Limite|
|--|:--|--|
| [Nome da aplicação][luis-get-started-create-app] | * Máximo de carateres de predefinido |
| [Teste de batch][batch-testing]| conjuntos de dados de 10, expressões de 1000 por conjunto de dados|
| Lista explícita | 50 por aplicação|
| [Objetivos][intents]|500 por aplicação: 499 intenções personalizadas e necessários _None_ intenção.<br>[Com base na expedição](https://aka.ms/dispatch-tool) aplicativo possui origens de expedição 500 correspondente.|
| [Lista de entidades](./luis-concept-entity-types.md) | Principal: 50, filho: 20 000 itens. É o nome canônico * predefinido máx. de caráter. Valores de sinónimos não ter nenhuma restrição de comprimento. |
| [Ficou a saber de máquina entidades](./luis-concept-entity-types.md):<br> Composição,<br>  Hierárquica<br> Simples|Um limite de entidades de 100 principal (não incluindo filhos hierárquicos) ou 330 entidades (incluindo filhos hierárquicos), independentemente da que ocorrer limita as visitas de utilizador pela primeira vez.<br><br>Um exemplo de com hierarquia seria 30 hierarquias com 10 crianças.  Os filhos consumirá 300 total e os elementos da hierarquia irão consumir os restantes 30. |
| [Padrões de](luis-concept-patterns.md)|500 padrões por aplicação.<br>Comprimento máximo do padrão é de 400 caracteres.<br>3 Pattern.any entidades, por padrão<br>Máximo de 2 textos de opcionais aninhados no padrão|
| [Pattern.any](./luis-concept-entity-types.md)|100 por aplicação, 3 pattern.any entidades, por padrão |
| [Lista de frase][phrase-list]|10 de frase de listas, itens de 5000 por lista|
| [Entidades pré-concebidas](./luis-prebuilt-entities.md) | sem limite|
| [Entidades de expressão regular](./luis-concept-entity-types.md)|20 entidades<br>máximo de 500 carateres. Por padrão de entidade de expressão regular|
| [Funções](luis-concept-roles.md)|300 funções por aplicação. 10 funções por entidade|
| [Expressão][utterances] | 500 carateres|
| [Expressões com][utterances] | 15 000 por aplicação|
| [Versões](luis-concept-version.md)| sem limite |
| [Nome da versão][luis-how-to-manage-versions] | 10 caracteres restringidas de alfanuméricos e período (.) |

* Máximo de carateres de predefinido é 50 carateres. 

## <a name="intent-and-entity-naming"></a>Objetivo e a atribuição de nomes de entidade
Não utilize os seguintes carateres em nomes de intenção e entidade:

|Caráter|Nome|
|--|--|
|`{`|Chaveta esquerda|
|`}`|Chaveta direita|
|`[`|Parênteses Reto de abertura|
|`]`|Reto|
|`\`|Barra invertida|

## <a name="key-usage"></a>Utilização de chaves

Compreender de linguagem tem chaves separadas, um tipo para a criação e um tipo para consultar o ponto de extremidade de predição. Para saber mais sobre as diferenças entre tipos de chaves, consulte [criação e a consulta a chaves do ponto final de predição em LUIS](luis-concept-keys.md).

## <a name="key-limits"></a>Limites de chaves

A chave de criação tem limites diferentes para a criação e o ponto final. A chave de ponto final de serviço do LUIS só é válida para consultas de ponto final.


|Chave|Criação de conteúdos|Ponto Final|Objetivo|
|--|--|--|--|
|Compreensão de linguagem de criação/Starter|1 milhão/mês, 5/segundo|1 mil/mês, 5/segundo|Criação da sua aplicação LUIS|
|Compreensão de idiomas [subscrição] [ pricing] escalão gratuito de - F0 - |inválido|10 mil/mês, 5/segundo|Consultar o ponto de final do LUIS|
|Compreensão de idiomas [subscrição] [ pricing] - S0 - de escalão básico|inválido|50/segundo|Consultar o ponto de final do LUIS|
|Serviço cognitivo [subscrição] [ pricing] - S0 - o escalão Standard|inválido|50/segundo|Consultar o ponto de final do LUIS|
|[Integração de análise de sentimentos](luis-how-to-publish-app.md#enable-sentiment-analysis)|inválido|sem custos|Adicionar informações de sentimentos, incluindo a extração de dados de expressões-chave |
|Integração de voz|inválido|Pedidos de ponto final de 5.50 US $/ 1 mil|Converter a expressão falada em expressão de texto e devolvem os resultados de LUIS|

## <a name="keyboard-controls"></a>Controles de teclado

|Entrada de teclado | Descrição | 
|--|--|
|Controle + E|Alterna entre tokens e de entidades na lista de expressões|

## <a name="website-sign-in-time-period"></a>Web site, inicie sessão no período de tempo

O acesso de início de sessão destina-se **60 minutos**. Após este período de tempo, irá obter este erro. Tem de iniciar sessão novamente.

[luis-get-started-create-app]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app
[batch-testing]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-test#batch-testing
[intents]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-intent
[phrase-list]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-feature
[utterances]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-utterance
[luis-how-to-manage-versions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-manage-versions
[pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
<!-- TBD: fix this link -->
[speech-to-intent-pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
