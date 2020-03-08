---
title: Design com modelos - LUIS
titleSuffix: Azure Cognitive Services
description: A compreensão da linguagem fornece vários tipos de modelos. Alguns modelos podem ser usados de uma forma.
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
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78393872"
---
# <a name="design-with-intent-and-entity-models"></a>Design com modelos de intenção e entidade 

A compreensão da linguagem fornece vários tipos de modelos. Alguns modelos podem ser usados de uma forma. 

## <a name="v3-authoring-uses-machine-teaching"></a>V3 Autoria usa ensino de máquinas

O LUIS permite que as pessoas ensinem facilmente conceitos a uma máquina. A máquina pode então construir modelos (aproximações funcionais de conceitos como classificadores e extratores) que podem ser usados para alimentar aplicações inteligentes. Enquanto o LUIS é alimentado por machine learning, a compreensão da aprendizagem automática não é necessária para usá-lo. Em vez disso, os professores de máquinas comunicam conceitos ao LUIS mostrando exemplos positivos e negativos do conceito e explicando como um conceito deve ser modelado usando outros conceitos relacionados. Os professores também podem melhorar o modelo do LUIS interativamente identificando e corrigindo os erros de previsão. 

## <a name="v3-authoring-model-decomposition"></a>V3 Decomposição do modelo de autoria

A LUIS suporta a _decomposição_ do modelo com as APIs de autor a v3, decompondo o modelo em partes mais pequenas. Isto permite-lhe construir os seus modelos com confiança na forma como as várias peças são construídas e previstas.

A decomposição do modelo tem as seguintes peças:

* [intençãos](#intents-classify-utterances)
    * [descritores](#descriptors-are-features) fornecidos por características
* [entidades aprendidas por máquinas](#machine-learned-entities)
    * [subcomponentes](#entity-subcomponents-help-extract-data) (também entidades aprendidas por máquinas)
        * [descritores](#descriptors-are-features) fornecidos por características 
        * [constrangimentos](#constraints-are-text-rules) fornecidos por entidades não-aprendidas por máquinas, tais como expressões regulares e listas

## <a name="v2-authoring-models"></a>Modelos de autoria V2

A LUIS apoia entidades compostas com as APIs de autoria v2. Isto proporciona uma decomposição de modelo semelhante, mas não é o mesmo que a decomposição do modelo V3. A arquitetura modelo recomendada é passar para a decomposição do modelo nas APIs de autor v3. 

## <a name="intents-classify-utterances"></a>Intenções classificam as palavras

Uma intenção classifica as declarações de exemplo para ensinar LUIS sobre a intenção. As declarações de exemplo dentro de uma intenção são usadas como exemplos positivos da expressão. Estas mesmas expressões são usadas como exemplos negativos em todas as outras intenções.

Considere uma aplicação que precisa determinar a intenção de um utilizador de encomendar um livro e uma app que precisa do endereço de envio para o cliente. Esta aplicação tem duas intenções: `OrderBook` e `ShippingLocation`.

A seguinte expressão é um **exemplo positivo** para a intenção `OrderBook` e um **exemplo negativo** para as intenções `ShippingLocation` e `None`: 

`Buy the top-rated book on bot architecture.`

O resultado de intenções bem desenhadas, com as suas declarações exemplo, é uma previsão de alta intenção. 

## <a name="entities-extract-data"></a>Entidades extraem dados

Uma entidade representa uma unidade de dados que pretende extrair da expressão. 

### <a name="machine-learned-entities"></a>Entidades aprendidas por máquinas

Uma entidade aprendida por máquinas é uma entidade de alto nível que contém subcomponentes, que também são entidades aprendidas por máquinas. 

**Utilize uma entidade aprendida por máquinas:**

* quando os subcomponentes são necessários pela aplicação do cliente
* para ajudar o algoritmo de aprendizagem automática a decompor entidades

Cada subcomponente pode ter:

* subcomponentes
* restrições (entidade de expressão regular ou entidade de lista)
* descritores (características como uma lista de frases) 

Um exemplo de uma entidade aprendida por máquinas é uma encomenda de um bilhete de avião. Conceptualmente esta é uma única transação com muitas unidades menores de dados, tais como data, hora, quantidade de assentos, tipo de assento, como primeira classe ou autocarro, localização de origem, localização de destino e escolha de refeições.


### <a name="entity-subcomponents-help-extract-data"></a>Subcomponentes da entidade ajudam a extrair dados

Um subcomponente é uma entidade infantil aprendida por máquinas dentro de uma entidade-mãe aprendida por máquinas. 

**Utilize o subcomponente para:**

* decompor as partes da entidade aprendida pela máquina (entidade-mãe).

O seguinte representa uma entidade aprendida por máquinas com todos estes dados separados:

* TravelOrder (entidade aprendida por máquinas)
    * DataTime (data pré-construídaV2)
    * Localização (entidade aprendida por máquinas)
        * Origem (função encontrada através de contexto como `from`)
        * Destino (função encontrada através de contexto sinuoso `to`)
    * Assentos (entidade aprendida por máquinas)
        * Quantidade (número pré-construído)
        * Qualidade (entidade aprendida por máquinas com descritor da lista de frases)
    * Refeições (entidade aprendida por máquinas com restrição da entidade da lista como escolhas alimentares)

Alguns destes dados, como a localização da origem e a localização do destino, devem ser aprendidos a partir do contexto da expressão, talvez com uma redação como `from` e `to`. Outras partes dos dados podem ser extraídas com correspondências exatas de cordas (`Vegan`) ou entidades pré-construídas (geografiaV2 de `Seattle` e `Cairo`). 

Você desenha como os dados são combinados e extraídos por quais os modelos que você escolhe e como os configura.

### <a name="constraints-are-text-rules"></a>Constrangimentos são regras de texto

Um constrangimento é uma regra de correspondência de texto, fornecida por uma entidade não-aprendizagem de máquinas, como a entidade de expressão regular ou uma entidade de lista. O constrangimento é aplicado no momento da previsão para limitar a previsão e fornecer a resolução da entidade necessária pela aplicação do cliente. Define estas regras enquanto é autora do subcomponente. 

**Utilize uma restrição:**
* quando souber o texto exato para extrair.

Os constrangimentos incluem:

* entidades de [expressão regular](reference-entity-regular-expression.md)
* [lista](reference-entity-list.md) de entidades 
* entidades [pré-construídas](luis-reference-prebuilt-entities.md)

Continuando com o exemplo do bilhete de avião, os códigos do aeroporto podem estar numa entidade da Lista para correspondências de texto exatas. 

Para uma lista de aeroportos, a lista de inscrições para Seattle é o nome da cidade, `Seattle` e os sinónimos de Seattle incluem o código do aeroporto para Seattle, juntamente com cidades e cidades circundantes:

|`Seattle` Lista de sinónimos de entidade|
|--|
|`Sea`|
|`seatac`|
|`Bellevue`|

Se quiser reconhecer apenas códigos de 3 letras para códigos de aeroporto, use uma expressão regular como restrição. 

`/^[A-Z]{3}$/`

## <a name="intents-versus-entities"></a>Intenções versus entidades

Uma intenção é o resultado desejado de _toda_ a expressão, enquanto as entidades são pedaços de dados extraídos da expressão. Normalmente, as intenções estão ligadas a ações que a aplicação do cliente deve tomar e as entidades são informações necessárias para realizar esta ação. Do ponto de vista da programação, uma intenção desencadearia uma chamada de método e as entidades seriam usadas como parâmetros para essa chamada de método.

Esta expressão _deve_ ter uma intenção e _pode_ ter entidades:

`Buy an airline ticket from Seattle to Cairo`

Esta expressão tem uma única intenção:

* Comprar um bilhete de avião

Esta expressão _pode_ ter várias entidades:

* Localizações de Seattle (origem) e Cairo (destino)
* A quantidade de um único bilhete

## <a name="descriptors-are-features"></a>Descritores são características

Um descritor é uma característica aplicada a um modelo no tempo de treino, incluindo listas de frases e entidades. 

**Utilize um descritor quando quiser:**

* aumentar o significado de palavras e frases identificadas pelo descritor
* ter LUIS recomendar novo texto ou frases para recomendar para o descritor
* corrigir um erro nos dados de treino

## <a name="next-steps"></a>Passos seguintes

* Compreender [as intenções](luis-concept-intent.md) e [as entidades.](luis-concept-entity-types.md) 