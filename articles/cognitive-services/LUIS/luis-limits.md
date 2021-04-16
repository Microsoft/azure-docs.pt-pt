---
title: Limites - LUIS
description: Este artigo contém os limites conhecidos da Azure Cognitive Services Language Understanding (LUIS). A LUIS tem várias áreas de limites. O limite de modelo controla as intenções, entidades e características no LUIS. Limites de quota baseados no tipo-chave. A combinação de teclado controla o website da LUIS.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 06/04/2020
ms.openlocfilehash: 1f917087eb15d8c77356995299e27dfc1657cb5d
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107497205"
---
# <a name="limits-for-your-luis-model-and-keys"></a>Limites para o seu modelo e chaves LUIS
O LUIS tem várias áreas limite. O primeiro é o limite do [modelo](#model-limits), que controla intenções, entidades e características no LUIS. A segunda área são [os limites de quota baseados](#key-limits) no tipo-chave. Uma terceira área de limites é a [combinação](#keyboard-controls) de teclado para controlar o website da LUIS. Uma quarta área é a [região mundial de mapeamento](luis-reference-regions.md) entre o site de autoria LUIS e as APIs [do ponto final](luis-glossary.md#endpoint) luis.

<a name="model-boundaries"></a>

## <a name="model-limits"></a>Limites de modelo

Se a sua aplicação exceder os limites do modelo LUIS, considere utilizar uma aplicação [de despacho LUIS](luis-concept-enterprise.md#dispatch-tool-and-model) ou utilizar um recipiente [LUIS](luis-container-howto.md).

|Área|Limite|
|--|:--|
| [Nome da aplicação][luis-get-started-create-app] | *Padrão de caracteres max |
| Aplicações| 500 aplicações por recurso de autoria da Azure |
| [Testes em lote][batch-testing]| 10 conjuntos de dados, 1000 expressões por conjunto de dados|
| Lista explícita | 50 por pedido|
| Entidades externas | sem limites |
| [Intenções][intents]|500 por aplicação: 499 intenções personalizadas e a intenção necessária _de Nenhuma._<br>A aplicação [baseada em despachos](https://aka.ms/dispatch-tool) tem 500 fontes de expedição correspondentes.|
| [Listar entidades](./luis-concept-entity-types.md) | Pai: 50, criança: 20.000 itens. O nome canónico é *padrão de caracteres max. Os valores do synonym não têm restrição de comprimento. |
| [entidades de aprendizagem automática + funções](./luis-concept-entity-types.md):<br> composto,<br>simples,<br>papel de entidade|Um limite de 100 entidades-mãe ou 330 entidades, qualquer que seja o limite que o utilizador atinge primeiro. Um papel conta como entidade para efeitos deste limite. Um exemplo é um composto com uma entidade simples, que tem 2 funções: 1 composto + 1 simples + 2 funções = 4 das 330 entidades.<br>As subentidades podem ser aninhadas até 5 níveis, com um máximo de 20 crianças por nível.|
|Modelo como recurso| Número máximo de modelos que podem ser usados como uma característica para um modelo específico para ser de 10 modelos. O número máximo de listas de frases utilizadas como recurso para um modelo específico ser de 10 listas de frases.|
| [Pré-visualização - Entidades de lista dinâmica](./luis-migration-api-v3.md)|2 listas de ~1k por pedido de previsão de consulta|
| [Padrões](luis-concept-patterns.md)|500 padrões por aplicação.<br>O comprimento máximo do padrão é de 400 caracteres.<br>3 Padrão.quaisquer entidades por padrão<br>Máximo de 2 textos opcionais aninhados em padrão|
| [Padrão.qualquer](./luis-concept-entity-types.md)|100 por aplicação, 3 padrão.qualquer entidade por padrão |
| [Lista de frases][phrase-list]|500 listas de frases. 10 listas de frases globais devido ao modelo como limite de recurso. A lista de frases não permutáveis tem o máximo de 5.000 frases. A lista de frases intercambiáveis tem o máximo de 50.000 frases. Número máximo de frases totais por aplicação de 500.000 frases.|
| [Entidades pré-criadas](./howto-add-prebuilt-models.md) | sem limite|
| [Entidades de expressão regular](./luis-concept-entity-types.md)|20 entidades<br>500 caracteres no máximo. por padrão de entidade de expressão regular|
| [Funções](./luis-concept-entity-types.md)|300 papéis por aplicação. 10 funções por entidade|
| [Expressão][utterances] | 500 caracteres<br><br>Se tiver texto mais longo do que este limite de caracteres, precisa segmentar a expressão antes da entrada para o LUIS e receberá respostas individuais de intenção por segmento. Há pausas óbvias com as qual se pode trabalhar, como marcas de pontuação e longas pausas no discurso.|
| [Exemplos de expressão][utterances] | 15.000 por pedido - não há limite para o número de declarações por intenção<br><br>Se precisar de treinar a aplicação com mais exemplos, utilize uma abordagem de modelo [de despacho.](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch) Treina aplicações INDIVIDUAIS LUIS (conhecidas como aplicações infantis para a aplicação de despacho dos pais) com uma ou mais intenções e, em seguida, treina uma aplicação de despacho que amostra as expressões de cada app LUIS para direcionar o pedido de previsão para a aplicação correta para crianças. |
| [Versions](./luis-concept-app-iteration.md) (Versões)| 100 versões por aplicação |
| [Nome da versão][luis-how-to-manage-versions] | 128 caracteres |

*O caracteres padrão max é de 50 caracteres.

<a name="intent-and-entity-naming"></a>

## <a name="name-uniqueness"></a>Singularidade do nome

Os nomes dos objetos devem ser únicos quando comparados com outros objetos do mesmo nível.

|Objetos|Restrições|
|--|--|
|Intenção, entidade|Todos os nomes de intenção e entidade devem ser únicos numa versão de uma aplicação.|
|Componentes de entidades ML|Todos os componentes da entidade de aprendizagem automática (entidades infantis) devem ser únicos, dentro dessa entidade para componentes ao mesmo nível.|
|Funcionalidades | Todas as funcionalidades nomeadas, como listas de frases, devem ser únicas numa versão de uma aplicação.|
|Funções de entidade|Todas as funções numa entidade ou componente de entidade devem ser únicas quando estão ao mesmo nível de entidade (pai, filho, neto, etc.).|

## <a name="object-naming"></a>Nomeação de objetos

Não utilize os seguintes caracteres nos seguintes nomes.

|Objeto|Excluir caracteres|
|--|--|
|Intenção, entidade e nomes de papéis|`:`<br>`$` <br> `&`|
|Nome da versão|`\`<br> `/`<br> `:`<br> `?`<br> `&`<br> `=`<br> `*`<br> `+`<br> `(`<br> `)`<br> `%`<br> `@`<br> `$`<br> `~`<br> `!`<br> `#`|

## <a name="resource-usage-and-limits"></a>Utilização de recursos e limites

A Linguagem Compreender tem recursos separados, um tipo para a autoria, e um tipo para consulta do ponto final de previsão. Para saber mais sobre as diferenças entre tipos-chave, consulte [as teclas de ponto final](luis-how-to-azure-subscription.md)de autoria e previsão de consulta em LUIS .

<a name="key-limits"></a>

### <a name="authoring-resource-limits"></a>Limites de recursos de autoria

Utilize o _tipo_, `LUIS.Authoring` quando filtrar recursos no portal Azure. A LUIS limita 500 aplicações por recurso de autoria Azure.

|Recurso de autoria|Autoria TPS|
|--|--|
|Inicial|1 milhão/mês, 5/segundo|
|F0 - Nível livre |1 milhão/mês, 5/segundo|

* TPS = Transações por segundo

[Saiba mais sobre preços.][pricing]

### <a name="query-prediction-resource-limits"></a>Limites de recursos de previsão de consulta

Utilize o _tipo_, `LUIS` quando filtrar recursos no portal Azure. O recurso de previsão de previsão LUIS, utilizado no tempo de execução, só é válido para consultas de ponto final.

|Recurso de previsão de consulta|Consulta TPS|
|--|--|
|F0 - Nível livre |10 mil por mês, 5/segundo|
|S0 - Nível padrão|50/segundo|

### <a name="sentiment-analysis"></a>Análise de sentimentos

[A integração da análise](luis-how-to-publish-app.md#enable-sentiment-analysis)de sentimentos , que fornece informações sobre sentimentos, é fornecida sem exigir outro recurso Azure.

### <a name="speech-integration"></a>Integração da fala

[A integração da fala](../speech-service/how-to-recognize-intents-from-speech-csharp.md) fornece 1 mil pedidos de ponto final por custo unitário.

[Saiba mais sobre preços.][pricing]

## <a name="keyboard-controls"></a>Controlos de teclado

|Entrada de teclado | Description |
|--|--|
|Controlo+E|muda entre tokens e entidades na lista de expressões|

## <a name="website-sign-in-time-period"></a>Período de tempo de inscrição no site

O seu acesso ao sôm-in é de **60 minutos**. Após este período de tempo, terá este erro. Precisa assinar de novo.

[luis-get-started-create-app]: ./luis-get-started-create-app.md
[batch-testing]: ./luis-concept-test.md#batch-testing
[intents]: ./luis-concept-intent.md
[phrase-list]: ./luis-concept-feature.md
[utterances]: ./luis-concept-utterance.md
[luis-how-to-manage-versions]: ./luis-how-to-manage-versions.md
[pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
<!-- TBD: fix this link -->
[speech-to-intent-pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
