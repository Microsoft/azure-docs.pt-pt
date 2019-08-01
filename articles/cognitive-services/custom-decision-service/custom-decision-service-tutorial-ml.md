---
title: 'Tutorial: Personalização e especificação de recursos-Serviço de Decisão Personalizada'
titlesuffix: Azure Cognitive Services
description: Um tutorial relativo à caracterização e à especificação de características de machine learning no Serviço de Decisão Personalizada.
services: cognitive-services
author: slivkins
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: tutorial
ms.date: 05/08/2018
ms.author: slivkins
ROBOTS: NOINDEX
ms.openlocfilehash: 409dcf7dc224eaf2a3f51325010507b9182fadf2
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707048"
---
# <a name="tutorial-featurization-and-feature-specification"></a>Tutorial: Personalização e especificação de recursos

Este tutorial aborda a funcionalidade avançada de machine learning no Serviço de Decisão Personalizada. Consiste em duas partes: [caracterização](#featurization-concepts-and-implementation) e [especificação de características](#feature-specification-format-and-apis). A caracterização significa representar os seus dados como “características” para machine learning. A especificação de características abrange o formato JSON e as APIs de apoio para especificar essas características.

Por predefinição, o machine learning no Serviço de Decisão Personalizada é transparente do ponto de vista do cliente. As características são extraídas automaticamente dos seus conteúdos e é utilizado um algoritmo de aprendizagem de reforço padrão. A extração de recursos aproveita vários outros serviços cognitivas do Azure: [Vinculação de entidades](../entitylinking/home.md), [análise de texto](../text-analytics/overview.md), [emoções](../emotion/home.md)e [Pesquisa Visual computacional](../computer-vision/home.md). Se só for utilizada a funcionalidade predefinida, este tutorial pode ser ignorado.

## <a name="featurization-concepts-and-implementation"></a>Caracterização: conceitos e implementação

O Serviço de Decisão Personalizada toma as decisões uma a uma. Cada decisão envolve escolher de entre várias alternativas, o que também é denominado ações. Dependendo da aplicação, a decisão pode escolher uma ação individual ou uma lista ordenada (pequena) de ações.

Por exemplo, personalizar a seleção de artigos na página inicial de um site. Neste caso, as ações correspondem aos artigos e cada decisão é que artigos mostrar a um determinado utilizador.

Cada ação é representada por um vetor de propriedades, doravante denominado *características*. Para além das características extraídas automaticamente, pode especificar outras novas. Também pode instruir o Serviço de Decisão Personalizada a registar algumas características, mas a ignorá-las no machine learning.

### <a name="native-vs-internal-features"></a>Características nativas vs. internas

Pode especificar características no formato que seja mais natural para a sua aplicação, seja um número, uma cadeia ou uma matriz. Essas características são chamadas “características nativas”. O Serviço de Decisão Personalizada traduz cada característica nativa em uma ou mais características numéricas, denominadas “características internas”.

A tradução em características internas é feita da seguinte forma:

- as características numéricas permanecem iguais.
- uma matriz numérica traduz em várias características numéricas, uma para cada elemento da matriz.
- por predefinição, uma característica `"Name":"Value"` com valor de cadeia é traduzida numa característica com o nome `"NameValue"` e o valor 1.
- Opcionalmente, as cadeias podem ser representadas como um “[saco de palavras](https://en.wikipedia.org/wiki/Bag-of-words_model)”. Em seguida, é criada uma característica interna para cada palavra na cadeia, cujo valor é o número de ocorrências dessas palavras.
- As características internas com valor de zero são omitidas.

### <a name="shared-vs-action-dependent-features"></a>Características partilhadas vs. características dependentes da ação

Algumas características referem-se a toda a decisão e são as mesmas para todas as ações. São chamadas *características partilhadas*. Outras características são específicas de uma determinada ação, a que se dão o nome de *características dependentes da ação* (ADFs).

No exemplo utilizado, as características partilhadas podem descrever o utilizador e/ou o estado da palavra. Podem ser características como geolocalização, idade e género do utilizador e os principais eventos que estão a acontecer neste momento. As características dependentes da ação podem descrever as propriedades de um determinado artigo, como os temas abordados no mesmo.

### <a name="interacting-features"></a>Interação entre características

Muitas vezes, as características “interagem”, ou seja, o efeito de uma depende de outras. Por exemplo, a característica X define se o utilizador tem interesse em desporto. A Y define se um determinado artigo é sobre desporto. Assim, o efeito da característica Y está muito dependente da X.

Para identificar a interação entre ambas as características, crie uma característica *quadrática*, cujo valor seja X\*Y (também dizemos “cruzar” X e Y). Pode escolher que pares de características vão ser cruzados.

> [!TIP]
> As características partilhadas devem ser cruzadas com características dependentes da ação para influenciar a classificação destas. As características dependentes da ação devem ser cruzadas com características partilhadas para contribuir para a personalização.

Por outras palavras, uma característica partilhada não cruzada com nenhuma ADF influencia cada ação da mesma forma. Também uma ADF não cruzada com nenhuma característica partilhada influencia cada decisão. Estes tipos de características podem reduzir a variação das estimativas de recompensa.

### <a name="implementation-via-namespaces"></a>Implementação através de espaços de nomes

Pode implementar características cruzadas (bem como outros conceitos da caracterização) através da “linha de comandos VW” no Portal. A sintaxe tem por base a linha de comandos [Vowpal Wabbit](http://hunch.net/~vw/).

Fundamental para a implementação é o conceito de *espaço de nomes*, um subconjunto nomeado de características. Cada característica pertence a exatamente um espaço de nomes. O espaço de nomes pode ser especificado explicitamente quando o valor da característica é fornecido ao Serviço de Decisão Personalizada. Esta é a única forma de fazer referência a uma característica na linha de comandos VW.

Os espaços de nomes são “partilhados” ou “dependentes da ação” - ou consistem apenas em características partilhadas ou em características dependentes da ação da mesma ação.

> [!TIP]
> É boa prática encapsular as características em espaços de nomes especificados explicitamente. Agrupe as características relacionadas no mesmo espaço de nomes.

Se o espaço de nomes não for indicado, a característica é atribuída automaticamente ao espaço de nomes predefinido.

> [!IMPORTANT]
> As características e os espaços de nomes não têm de ser consistentes entre as ações. Em particular, os espaços de nomes podem ter características diferentes para diferentes ações. Além disso, um determinado espaço de nomes pode ser definido para algumas ações e não para outras.

As múltiplas características internas provenientes da mesma característica nativa com valor de cadeia são agrupadas no mesmo espaço de nomes. Duas características nativas que estejam em espaços de nomes diferentes são tratadas como sendo diferentes, mesmo que tenham o mesmo nome.

> [!IMPORTANT]
> Embora os IDs de espaços de nomes compridos e descritivos sejam comuns, a linha de comandos VW não distingue os espaços cujos IDs comecem pela mesma letra. No exemplo abaixo, os IDs dos espaços de nomes são letras individuais, como `x` e `y`.

Os detalhes da implementação são os seguintes:

- Para cruzar os espaços de nomes `x` e `y`, escreva `-q xy` ou `--quadratic xy`. Depois, cada característica em `x` é cruzada com cada característica em `y`. Utilize `-q x:` para cruzar `x` com todos os espaços de nomes e `-q ::` para cruzar todos os pares de espaços de nomes.

- Para ignorar todas as características no espaço de nomes `x`, escreva `--ignore x`.

Estes comandos são aplicados a cada ação separadamente, sempre que os espaços forem definidos.

### <a name="estimated-average-as-a-feature"></a>Média estimada como característica

Como experiência mental, qual seria a recompensa média de uma determinada ação se esta fosse escolhida para todas as decisões? Essa recompensa média poderia ser utilizada como medida da “qualidade geral” dessa ação. Não se sabe ao certo quando é que foram escolhidas outras ações em algumas decisões. No entanto, é possível estimar através de técnicas de aprendizagem de reforço. Geralmente, a qualidade dessa estimativa melhora ao longo do tempo.

Pode optar por incluir esta “recompensa média estimada” como característica de uma determinada ação. Em seguida, o Serviço de Decisão Personalizada atualiza essa estimativa automaticamente quando forem recebidos dados novos. Esta característica é denominada *característica marginal* desta ação. As características marginais podem ser utilizadas para machine learning e para auditoria.

Para adicionar características marginais, escreva `--marginal <namespace>` na linha de comandos VW. Defina `<namespace>` no JSON da seguinte forma:

```json
{<namespace>: {"mf_name":1 "action_id":1}
```

Insira este espaço de nomes, juntamente com outras características dependentes da ação de uma determinada ação. Forneça esta definição para cada decisão, utilizando os mesmos `mf_name` e `action_id` para todas as decisões.

A característica marginal é adicionada a cada ação com `<namespace>`. `action_id` pode ser qualquer nome de característica que identifique exclusivamente a ação. O nome da característica é definido como `mf_name`. Em particular, as características marginais com `mf_name` diferente são tratadas como características distintas — é aprendida uma ponderação diferente para cada `mf_name`.

A utilização predefinida pressupõe que `mf_name` é igual para todas as ações. Em seguida, é aprendida uma ponderação para todas as características marginais.

Também pode especificar várias características marginais para a mesma ação, com valores iguais, mas nomes de características diferentes.

```json
{<namespace>: {"mf_name1":1 "action_id":1 "mf_name2":1 "action_id":1}}
```

### <a name="1-hot-encoding"></a>Codificação 1-hot

Pode optar por representar algumas características como vetores de bits, em que cada bit corresponde a um intervalo de valores possíveis. Esse bit é definido como 1 se e apenas se a característica estiver nesse intervalo. Assim, um bit “hot” é definido como 1 e os outros como 0. Esta representação é, geralmente, denominada *codificação 1-hot*.

A codificação 1-hot é comum em características de categorias, como, por exemplo, “região geográfica”, que não têm uma representação numérica inerentemente significativa. Também é aconselhável para características numéricas cuja influência sobre a recompensa seja, provavelmente, não linear. Por exemplo, um determinado artigo pode ser relevante para uma faixa etária específica e irrelevante para as outras pessoas mais velhas ou mais novas.

Todas as características com valor de cadeia são codificadas com 1-hot — é criada uma característica interna distinta para cada valor possível. Atualmente, a codificação 1-hot não é disponibilizada para características numéricas e/ou características com intervalos personalizados.

> [!TIP]
> Os algoritmos de machine learning tratam todos os valores possíveis de uma determinada característica interna de forma uniforme, através de uma “ponderação” comum. A codificação 1-hot permite uma “ponderação” separada para cada intervalo de valores. Diminuir o tamanho dos intervalos origina recompensas melhores assim que forem recolhidos dados suficientes, mas pode aumentar a quantidade de dados necessários para convergir em recompensas melhores.

## <a name="feature-specification-format-and-apis"></a>Especificação de características: formato e APIs

Pode utilizar várias APIs de apoio para especificar as características. Todas as APIs utilizam um formato JSON comum. Pode ver as APIs e o formato a um nível conceptual. A especificação é complementada com um esquema do Swagger.

O modelo JSON básico para a especificação de características é o seguinte:

```json
{
"<name>":<value>, "<name>":<value>, ... ,
"namespace1": {"<name>":<value>, ... },
"namespace2": {"<name>":<value>, ... },
...
}
```

Aqui, `<name>` e `<value>` dizem respeito ao nome da característica e ao valor da característica, respetivamente. `<value>` pode ser uma cadeia, um número inteiro, um número fracionário, um booleano ou uma matriz. As características que não forem encapsuladas num espaço de nomes são atribuídas automaticamente ao espaço de nomes predefinido.

Para representar uma cadeia como um “saco de palavras”, utilize a sintaxe especial `"_text":"string"`, em vez de `"<name>":<value>`. Na realidade, é criada uma característica interna separada para cada palavra na cadeia. O respetivo valor é o número de ocorrências dessas palavras.

Se `<name>` começar por "_" (e não for `"_text"`), a característica é ignorada.

> [!TIP]
> Por vezes, são unidas características de várias origens JSON. Para conveniência, pode representá-las da seguinte forma:
>
> ```json
> {
> "source1":<features>,
> "source2":<features>,
> ...
> }
> ```

Aqui, `<features>` refere-se à especificação da característica básica que foi definida anteriormente. Também são permitidos níveis mais profundos de “aninhamento”. O Serviço de Decisão Personalizada localiza automaticamente os objetos JSON mais profundos que podem ser interpretados como `<features>`.

#### <a name="feature-set-api"></a>API Conjunto de Características

A API Conjunto de Características devolve uma lista de características no formato JSON descrito anteriormente. Pode utilizar vários pontos finais da API Conjunto de Características. Cada ponto final é identificado por conjunto de características e por um URL. O mapeamento entre os IDs e os URLs dos conjuntos de características é definido no portal.

Para chamar a API Conjunto de Características, insira o ID do conjunto de características correspondente no sítio adequado no JSON. Relativamente às características dependentes da ação, a chamada é parametrizada automaticamente pelo ID da ação. Pode especificar vários IDs de conjunto de características para a mesma ação.

#### <a name="action-set-api-json-version"></a>API Conjunto de Ações (versão JSON)

A API Conjunto de Ações tem uma versão na qual as ações e as características são especificadas em JSON. As características podem ser especificadas explicitamente e/ou através das APIs Conjunto de Características. As características partilhadas podem ser especificadas uma vez para todas as ações.

#### <a name="ranking-api-http-post-call"></a>API Classificação (chamada HTTP POST)

A API Classificação tem uma versão que utiliza a chamada HTTP POST. O corpo dessa chamada especifica as ações e as características através de uma sintaxe JSON flexível.

As ações podem ser especificadas explicitamente e/ou através dos IDs de conjuntos de ações. Sempre que for encontrado um ID de conjunto de ações, é executada uma chamada ao ponto final da API Conjunto de Ações correspondente.

Relativamente à API Conjunto de Ações, as características podem ser especificadas explicitamente e/ou através das APIs Conjunto de Características. As características partilhadas podem ser especificadas uma vez para todas as ações.
