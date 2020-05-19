---
title: Limites - LUIS
description: Este artigo contém os limites conhecidos da compreensão da linguagem dos serviços cognitivos azure (LUIS). O LUIS tem várias áreas de limites. O limite de modelo controla as intenções, entidades e funcionalidades no LUIS. Limites de quota com base no tipo-chave. A combinação de teclado controla o website da LUIS.
ms.topic: reference
ms.date: 05/06/2020
ms.openlocfilehash: 71f6126cbf9615d7f808f098202f29094a913982
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83593244"
---
# <a name="limits-for-your-luis-model-and-keys"></a>Limites para o seu modelo LUIS e chaves
O LUIS tem várias áreas limite. O primeiro é o limite de [modelo,](#model-limits)que controla as intenções, entidades e características no LUIS. A segunda área é [os limites](#key-limits) de quota com base no tipo-chave. Uma terceira área de limites é a [combinação](#keyboard-controls) de teclado para controlar o site da LUIS. Uma quarta área é a [região mundial mapeando](luis-reference-regions.md) entre o site de autor luis e as APIs [endpoint](luis-glossary.md#endpoint) LUIS.

<a name="model-boundaries"></a>

## <a name="model-limits"></a>Limites de modelo

Se a sua aplicação exceder os limites do modelo LUIS, considere utilizar uma aplicação de [despacho LUIS](luis-concept-enterprise.md#dispatch-tool-and-model) ou utilizar um [recipiente LUIS](luis-container-howto.md).

|Área|Limite|
|--|:--|
| [Nome da aplicação][luis-get-started-create-app] | *Padrão de caracteres max |
| Aplicações| 500 candidaturas por recurso de autoria azure |
| [Testes em lote][batch-testing]| 10 conjuntos de dados, 1000 expressões por conjunto de dados|
| Lista explícita | 50 por aplicação|
| Entidades externas | sem limites |
| [Intenções][intents]|500 por aplicação: 499 intenções personalizadas, e a intenção de _nenhuma_ intencional.<br>A aplicação [baseada em despachos](https://aka.ms/dispatch-tool) tem 500 fontes de expedição correspondentes.|
| [Listar entidades](./luis-concept-entity-types.md) | Pai: 50 anos, criança: 20.000 itens. O nome canónico é *padrão de caracteres max. Os valores do sinónimo não têm restrição de comprimento. |
| [Entidades aprendidas por máquinas + funções:](./luis-concept-entity-types.md)<br> composto,<br>simples,<br>papel da entidade|Um limite de 100 entidades-mãe ou 330 entidades, o que limita o utilizador primeiro. Um papel conta como entidade para efeitos deste limite. Um exemplo é um compósito com uma entidade simples, que tem 2 funções é: 1 composto + 1 simples + 2 funções = 4 das 330 entidades.<br>As subentidades podem ser aninhadas até 5 níveis.|
|Modelo como recurso| Número máximo de modelos que podem ser usados como uma característica para um modelo específico para ser 10 modelos. O número máximo de listas de frases utilizadas como recurso para um modelo específico ser de 10 listas de frases.|
| [Pré-visualização - Entidades de lista dinâmica](https://aka.ms/luis-api-v3-doc#dynamic-lists-passed-in-at-prediction-time)|2 listas de ~1k por pedido de ponto final de previsão de consulta|
| [Padrões](luis-concept-patterns.md)|500 padrões por aplicação.<br>O comprimento máximo do padrão é de 400 caracteres.<br>3 Padrão.quaisquer entidades por padrão<br>Máximo de 2 textos opcionais aninhados em padrão|
| [Padrão.qualquer](./luis-concept-entity-types.md)|100 por aplicação, 3 padrão.quaisquer entidades por padrão |
| [Lista de frases][phrase-list]|500 listas de frases. 10 listas de frases globais devido ao modelo como um limite de características. A lista de frases não permutáveis tem um máximo de 5.000 frases. A lista de frases intercambiáveis tem um máximo de 50.000 frases. Número máximo de frases totais por aplicação de 500.000 frases.|
| [Entidades pré-criadas](./luis-prebuilt-entities.md) | sem limite|
| [Entidades de expressão regular](./luis-concept-entity-types.md)|20 entidades<br>500 caracteres no máximo. por padrão de entidade de expressão regular|
| [Funções](luis-concept-roles.md)|300 funções por aplicação. 10 funções por entidade|
| [Expressão][utterances] | 500 caracteres|
| [Expressões][utterances] | 15.000 por aplicação - não há limite para o número de expressões por intenção|
| [Versões](luis-concept-version.md)| 100 versões por aplicação |
| [Nome da versão][luis-how-to-manage-versions] | 128 caracteres |

*O maxilar de caracteres padrão é de 50 caracteres.

<a name="intent-and-entity-naming"></a>

## <a name="name-uniqueness"></a>Nome singularidade

Os nomes dos objetos devem ser únicos quando comparados com outros objetos do mesmo nível.

|Objetos|Restrições|
|--|--|
|Intenção, entidade|Todos os nomes de intenção e entidadedevem ser únicos numa versão de uma aplicação.|
|Componentes da entidade ML|Todos os componentes de entidades aprendidas por máquinas (entidades infantis) devem ser únicos, dentro dessa entidade para componentes ao mesmo nível.|
|Funcionalidades | Todas as funcionalidades nomeadas, como listas de frases, devem ser únicas dentro de uma versão de uma aplicação.|
|Funções de entidade|Todas as funções numa entidade ou componente de entidade devem ser únicas quando estiverem ao mesmo nível de entidade (pai, filho, neto, etc.).|

## <a name="object-naming"></a>Nomeação de objeto

Não utilize os seguintes caracteres nos seguintes nomes.

|Objeto|Excluir caracteres|
|--|--|
|Nomes de intenção, entidade e papéis|`:`<br>`$` <br> `&`|
|Nome da versão|`\`<br> `/`<br> `:`<br> `?`<br> `&`<br> `=`<br> `*`<br> `+`<br> `(`<br> `)`<br> `%`<br> `@`<br> `$`<br> `~`<br> `!`<br> `#`|

## <a name="resource-usage-and-limits"></a>Utilização e limites de recursos

Language Understand tem recursos separados, um tipo para autoria, e um tipo para consulta do ponto final da previsão. Para saber mais sobre as diferenças entre os tipos-chave, consulte [as chaves finais de autor e](luis-concept-keys.md)previsão de consulta em LUIS .

<a name="key-limits"></a>

### <a name="authoring-resource-limits"></a>Limites de recursos de autoria

Use _kind_o `LUIS.Authoring` tipo, ao filtrar recursos no portal Azure. LUIS limita 500 aplicações por recurso de autoria Azure.

|Recurso de autoria|TPS de autoria|
|--|--|
|Inicial|1 milhão/mês, 5/segundo|
|F0 - Nível livre |1 milhão/mês, 5/segundo|

* TPS = Transações por segundo

[Saiba mais sobre preços.][pricing]

### <a name="query-prediction-resource-limits"></a>Limites de recursos de previsão de consulta

Use _kind_o `LUIS` tipo, ao filtrar recursos no portal Azure. O recurso final de previsão de consulta LUIS, utilizado no tempo de execução, só é válido para consultas de ponto final.

|Recurso de previsão de consulta|TPS de consulta|
|--|--|
|F0 - Nível livre |10 mil/mês, 5/segundo|
|S0 - Nível standard|50/segundo|

### <a name="sentiment-analysis"></a>Análise de sentimentos

[A integração](luis-how-to-publish-app.md#enable-sentiment-analysis)da análise de sentimentos, que fornece informações sobre sentimentos, é fornecida sem precisar de outro recurso Azure.

### <a name="speech-integration"></a>Integração da fala

[A integração da fala](../speech-service/how-to-recognize-intents-from-speech-csharp.md) fornece 1.000 pedidos de ponto final por custo unitário.

[Saiba mais sobre preços.][pricing]

## <a name="keyboard-controls"></a>Controlos de teclado

|Entrada do teclado | Descrição |
|--|--|
|Controlo+E|alterna entre fichas e entidades na lista de expressões|

## <a name="website-sign-in-time-period"></a>Período de hora de inscrição no site

O seu acesso ao registo é de **60 minutos.** Após este período de tempo, terá este erro. Precisa voltar a assinar.

[luis-get-started-create-app]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app
[batch-testing]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-test#batch-testing
[intents]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-intent
[phrase-list]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-feature
[utterances]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-utterance
[luis-how-to-manage-versions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-manage-versions
[pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
<!-- TBD: fix this link -->
[speech-to-intent-pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
