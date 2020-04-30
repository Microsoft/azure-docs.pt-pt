---
title: Características - LUIS
titleSuffix: Azure Cognitive Services
description: Adicione funcionalidades a um modelo de idioma para fornecer dicas sobre como reconhecer a entrada que pretende rotular ou classificar.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: diberry
ms.openlocfilehash: 5b8257e24cf52d01be8065d97db17fd685aa316d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81531903"
---
# <a name="machine-learned-features"></a>Características aprendidas por máquinas

No machine learning, uma _característica_ é um traço distintivo ou atributo de dados que o seu sistema observa & aprende através. Na Compreensão da Linguagem (LUIS), uma funcionalidade descreve e explica o que é significativo sobre as suas intenções e entidades.

No [portal DE pré-visualização LUIS,](https://preview.luis.ai)as funcionalidades são _descritores_ porque são usadas para _descrever_ a intenção ou entidade.

## <a name="features-_descriptors_-in-language-understanding"></a>Características _(descritores)_ na compreensão da linguagem

As funcionalidades, também conhecidas como descritores, descrevem pistas para ajudar a Compreensão da Linguagem a identificar as expressões do exemplo. As funcionalidades incluem:

* Lista de frases como recurso a intenções ou entidades
* Entidades como características de intenções ou entidades

As funcionalidades devem ser consideradas como uma parte necessária do seu esquema para a decomposição do modelo.

## <a name="what-is-a-phrase-list"></a>O que é uma lista de frases

Uma lista de frases é uma lista de palavras, frases, números ou outros caracteres que ajudam a identificar o conceito que está a tentar identificar. A lista é insensível a casos.

## <a name="when-to-use-a-phrase-list"></a>Quando usar uma lista de frases

Com uma lista de frases, a LUIS considera o contexto e generaliza para identificar itens semelhantes, mas não uma correspondência exata de texto. Se precisar da sua aplicação LUIS para poder generalizar e identificar novos itens, utilize uma lista de frases.

Quando pretende reconhecer novos casos, como um programador de reuniões que deve reconhecer os nomes de novos contactos, ou uma app de inventário que deve reconhecer novos produtos, comece por uma entidade aprendida por máquinas. Em seguida, crie uma lista de frases que ajude LUIS a encontrar palavras com significado semelhante. Esta lista de frases guia luis a reconhecer exemplos adicionando significado adicional ao valor dessas palavras.

As listas de frases são como vocabulário específico do domínio que ajudam a melhorar a qualidade de compreensão tanto das intenções como das entidades.

## <a name="considerations-when-using-a-phrase-list"></a>Considerações ao usar uma lista de frases

Uma lista de frases é aplicada, por padrão, a todos os modelos da aplicação. Isto funcionará para listas de frases que possam cruzar todas as intenções e entidades. Para a descomposição, deve aplicar uma lista de frases apenas aos modelos a que é relevante.

Se criar uma lista de frases (criada globalmente por padrão), depois aplice-a como descritor (recurso) a um modelo específico, é removida dos outros modelos. Esta remoção acrescenta relevância à lista de frases para o modelo a que é aplicada, ajudando a melhorar a precisão que proporciona no modelo.

A `enabledForAllModels` bandeira controla este âmbito de modelo na API.

<a name="how-to-use-phrase-lists"></a>

### <a name="how-to-use-a-phrase-list"></a>Como usar uma lista de frases

[Crie uma lista de frases](luis-how-to-add-features.md) quando a sua intenção ou entidade tem palavras ou frases importantes tais como:

* termos da indústria
* gíria
* abreviaturas
* linguagem específica da empresa
* linguagem que é de outra língua, mas frequentemente usado na sua app
* palavras-chave e frases nas suas declarações exemplo

**Não** adicione todas as palavras ou frases possíveis. Em vez disso, adicione algumas palavras ou frases de cada vez, em seguida, retree e publique. À medida que a lista cresce ao longo do tempo, poderá descobrir que alguns termos têm muitas formas (sinónimos). Quebre isto para outra lista.

<a name="phrase-lists-help-identify-simple-exchangeable-entities"></a>

## <a name="when-to-use-an-entity-as-a-feature"></a>Quando usar uma entidade como recurso

Uma entidade pode ser adicionada como uma funcionalidade a nível de intenção ou entidade.

### <a name="entity-as-a-feature-to-an-intent"></a>Entidade como característica de uma intenção

Adicione uma entidade como descritor (recurso) a uma intenção quando a deteção dessa entidade é significativa para a intenção.

Por exemplo, se a intenção é reservar um voo e a entidade for informação de bilhetes (como o número de lugares, origem e destino), então encontrar a entidade de informação do bilhete deve adicionar peso à previsão da intenção do voo do livro.

### <a name="entity-as-a-feature-to-another-entity"></a>Entidade como recurso a outra entidade

Uma entidade (A) deve ser adicionada como uma característica a outra entidade (B) quando a deteção dessa entidade (A) for significativa para a previsão da entidade (B).

Por exemplo, se a entidade de endereço de rua (A) for detetada, então encontrar o endereço de rua (A) adiciona peso à previsão para a entidade de endereço de envio (B).

## <a name="best-practices"></a>Melhores práticas
Aprenda [as melhores práticas.](luis-concept-best-practices.md)

## <a name="next-steps"></a>Passos seguintes

* [Estenda](schema-change-prediction-runtime.md) os seus modelos de aplicações no tempo de execução da previsão
* Consulte [funcionalidades adicionais](luis-how-to-add-features.md) para saber mais sobre como adicionar funcionalidades à sua aplicação LUIS.
