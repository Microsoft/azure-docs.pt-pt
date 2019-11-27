---
title: Criar com modelos-LUIS
titleSuffix: Azure Cognitive Services
description: A compreensão da linguagem fornece vários tipos de modelos. Alguns modelos podem ser usados de mais de uma maneira.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: d721ceb25b3ce2408563a0bed16457d05affe7b4
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280872"
---
# <a name="design-with-intent-and-entity-models"></a>Design com modelos de intenção e entidade 

A compreensão da linguagem fornece vários tipos de modelos. Alguns modelos podem ser usados de mais de uma maneira. 

## <a name="v3-authoring-uses-machine-teaching"></a>A criação v3 usa o ensino de máquina

O LUIS permite que as pessoas ensinem conceitos a um computador com facilidade. Em seguida, o computador pode criar modelos (aproximações funcionais de conceitos, como classificadores e extratores) que podem ser usados para alimentar aplicativos inteligentes. Embora o LUIS seja alimentado pelo Machine Learning, a compreensão do aprendizado de máquina não é necessária para usá-lo. Em vez disso, os professores de máquina comunicam conceitos ao LUIS mostrando exemplos positivos e negativos do conceito e explicando como um conceito deve ser modelado usando outros conceitos relacionados. Os professores também podem melhorar o modelo do LUIS interativamente identificando e corrigindo os erros de previsão. 

## <a name="v3-authoring-model-decomposition"></a>Decomposição de modelo de criação v3

O LUIS dá suporte à _decomposição de modelo_ com as APIs de criação v3, dividindo o modelo em partes menores. Isso permite que você crie seus modelos com confiança em como as várias partes são construídas e previstas.

A decomposição do modelo tem as seguintes partes:

* [tentativas](#intents-classify-utterances)
    * [descritores](#descriptors-are-features) fornecidos pelos recursos
* [entidades aprendidas por computador](#machine-learned-entities)
    * [Subcomponentes](#entity-subcomponents-help-extract-data) (também entidades aprendidas por máquina)
        * [descritores](#descriptors-are-features) fornecidos pelos recursos 
        * [restrições](#constraints-are-text-rules) fornecidas por entidades não aprendidas por computador, como expressões regulares e listas

## <a name="v2-authoring-models"></a>Modelos de criação v2

O LUIS dá suporte a entidades compostas com as APIs de criação v2. Isso fornece a decomposição de modelo semelhante, mas não é o mesmo que a decomposição de modelo v3. A arquitetura de modelo recomendada é mudar para a decomposição de modelo nas APIs de criação v3. 

## <a name="intents-classify-utterances"></a>Tentativas de classificação declarações

Uma intenção classifica o exemplo declarações para ensinar LUIS sobre a intenção. Os declarações de exemplo em uma intenção são usados como exemplos positivos de expressão. Esses mesmos declarações são usados como exemplos negativos em todas as outras intenções.

Considere um aplicativo que precisa determinar a intenção de um usuário para solicitar um livro e um aplicativo que precise do endereço de envio para o cliente. Este aplicativo tem duas intenções: `OrderBook` e `ShippingLocation`.

O expressão a seguir é um **exemplo positivo** para a tentativa de `OrderBook` e um **exemplo negativo** para o `ShippingLocation` e `None` tentativas: 

`Buy the top-rated book on bot architecture.`

O resultado de tentativas bem projetadas, com seus declarações de exemplo, é uma previsão de alta intenção. 

## <a name="entities-extract-data"></a>Dados de extração de entidades

Uma entidade representa uma unidade de dados que você deseja extrair do expressão. 

### <a name="machine-learned-entities"></a>Entidades aprendidas por computador

Uma entidade aprendida por máquina é uma entidade de nível superior que contém subcomponentes, que também são entidades aprendidas por computador. 

**Use uma entidade aprendida por máquina**:

* Quando os subcomponentes são necessários para o aplicativo cliente
* para ajudar o algoritmo de aprendizado de máquina decompor entidades

Cada Subcomponente pode ter:

* subcomponentes
* restrições (entidade de expressão regular ou entidade de lista)
* Descritores (recursos como uma lista de frases) 

Um exemplo de uma entidade aprendida por máquina é uma ordem para um tíquete de plano. Conceitualmente, essa é uma única transação com muitas unidades menores de dados, como data, hora, quantidade de estações, tipo de assento, como primeira classe, local de origem, local de destino e escolha de refeição.


### <a name="entity-subcomponents-help-extract-data"></a>Os subcomponentes de entidade ajudam a extrair dados

Um subcomponente é uma entidade filho aprendida por computador em uma entidade pai aprendida por computador. 

**Use o subcomponente para**:

* decompor as partes da entidade aprendida pela máquina (entidade pai).

O seguinte representa uma entidade aprendida por computador com todas essas partes de dados separadas:

* TravelOrder (entidade aprendida por computador)
    * DateTime (datetimeV2 predefinido)
    * Local (entidade aprendida por computador)
        * Origem (função encontrada por meio de contexto, como `from`)
        * Destino (função encontrada por meio de contexto, como `to`)
    * Assentos (entidade aprendida por computador)
        * Quantidade (número predefinido)
        * Qualidade (entidade aprendida por computador com descritor de lista de frases)
    * Refeições (entidade aprendida por máquina com restrição de entidade de lista como opções de alimentos)

Alguns desses dados, como o local de origem e o local de destino, devem ser aprendidos no contexto do expressão, talvez com essas palavras como `from` e `to`. Outras partes de dados podem ser extraídas com correspondências exatas de cadeias de caracteres (`Vegan`) ou entidades predefinidas (geographyV2 de `Seattle` e `Cairo`). 

Você cria como os dados são correspondidos e extraídos por quais modelos você escolhe e como você os configura.

### <a name="constraints-are-text-rules"></a>As restrições são regras de texto

Uma restrição é uma regra de correspondência de texto, fornecida por uma entidade não aprendida por computador, como a entidade de expressão regular ou uma entidade de lista. A restrição é aplicada no momento da previsão para limitar a previsão e fornecer a resolução de entidade necessária para o aplicativo cliente. Você define essas regras ao criar o subcomponente. 

**Usar uma restrição**:
* Quando você souber o texto exato a ser extraído.

As restrições incluem:

* entidades de [expressão regular](reference-entity-regular-expression.md)
* [listar](reference-entity-list.md) entidades 
* entidades [predefinidas](luis-reference-prebuilt-entities.md)

Continuando com o exemplo do tíquete de plano, os códigos de aeroporto podem estar em uma entidade de lista para correspondências exatas de texto. 

Para uma lista de aeroportos, a entrada de lista para Seattle é o nome da cidade, `Seattle` e os sinônimos de Seattle incluem o código do aeroporto para Seattle junto com cidades e cidades ao redor:

|sinônimos de entidade de lista de `Seattle`|
|--|
|`Sea`|
|`seatac`|
|`Bellevue`|

Se você quiser reconhecer apenas 3 códigos de letra para códigos de aeroportos, use uma expressão regular como restrição. 

`/^[A-Z]{3}$/`

## <a name="intents-versus-entities"></a>Tentativas versus entidades

Uma intenção é o resultado desejado de _todo_ o expressão, enquanto as entidades são partes dos dados extraídos do expressão. Geralmente, as intenções são vinculadas a ações que o aplicativo cliente deve tomar e as entidades são informações necessárias para executar essa ação. De uma perspectiva de programação, uma intenção dispararia uma chamada de método e as entidades seriam usadas como parâmetros para essa chamada de método.

Este expressão _deve_ ter uma intenção e _pode_ ter entidades:

`Buy an airline ticket from Seattle to Cairo`

Este expressão tem uma única intenção:

* Comprando um tíquete de plano

Este expressão _pode_ ter várias entidades:

* Locais de Seattle (origem) e Cairo (destino)
* A quantidade de um único tíquete

## <a name="descriptors-are-features"></a>Os descritores são recursos

Um descritor é um recurso aplicado a um modelo no tempo de treinamento, incluindo listas de frases e entidades. 

**Use um descritor quando desejar**:

* aumentar a significância de palavras e frases identificadas pelo descritor
* fazer com que o LUIS recomende novo texto ou frases para recomendar para o descritor
* corrigir um erro nos dados de treinamento

## <a name="next-steps"></a>Passos Seguintes

* Entenda as [intenções](luis-concept-intent.md) e as [entidades](luis-concept-entity-types.md). 