---
title: Recursos-LUIS
titleSuffix: Azure Cognitive Services
description: Adicione recursos para um modelo de idioma para fornecer sugestões sobre como a reconhecer a entrada que deseja identificar ou classificar.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: diberry
ms.openlocfilehash: b151043babd6c67a17f704bb671a415fcc3ba7b2
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280904"
---
# <a name="machine-learned-features"></a>Recursos aprendidos por máquina 

No Machine Learning, um _recurso_ é uma característica ou atributo diferenciado dos dados que seu sistema observa & aprende. No Reconhecimento vocal (LUIS), um recurso descreve e explica o que é significativo sobre suas intenções e entidades.

No [portal do Luis de visualização](https://preview.luis.ai), os recursos são _descritores_ porque são usados para _descrever_ a intenção ou a entidade.  

## <a name="features-_descriptors_-in-language-understanding"></a>Recursos (_descritores_) no reconhecimento vocal

Recursos, também conhecidos como descritores, descrevem pistas para ajudar a Reconhecimento vocal a identificar o exemplo declarações. As funcionalidades incluem: 

* Lista de frases como um recurso para intenções ou entidades
* Entidades como recursos para intenções ou entidades

Os recursos devem ser considerados como uma parte necessária do esquema para a decomposição do modelo. 

## <a name="what-is-a-phrase-list"></a>O que é uma lista de frases

Uma lista de frases é uma lista de palavras, frases, números ou outros caracteres que ajudam a identificar o conceito que você está tentando identificar. A lista não diferencia maiúsculas de minúsculas. 

## <a name="when-to-use-a-phrase-list"></a>Quando usar uma lista de frases

Com uma lista de frases, LUIS considera contexto e generalizações para identificar itens semelhantes a, mas não uma correspondência de texto exata. Se você precisar que seu aplicativo LUIS seja capaz de generalizar e identificar novos itens, use uma lista de frases. 

Quando você quiser ser capaz de reconhecer novas instâncias, como um Agendador de reunião que deve reconhecer os nomes de novos contatos ou um aplicativo de inventário que deve reconhecer novos produtos, comece com uma entidade aprendida por computador. Em seguida, crie uma lista de frases que ajude a LUIS a localizar palavras com significado semelhante. Esta lista de frases orienta o LUIS a reconhecer exemplos adicionando um significado adicional ao valor dessas palavras. 

Listas de frase são como vocabulário específicas de domínio que o ajudam a com a melhorar a qualidade da compreensão dos objetivos e entidades. 

## <a name="considerations-when-using-a-phrase-list"></a>Considerações ao usar uma lista de frases

Uma lista de frases é aplicada, por padrão, a todos os modelos no aplicativo. Isso funcionará para listas de frases que podem cruzar todas as intenções e entidades. Para desdação, você deve aplicar uma lista de frases apenas aos modelos aos quais ela é relevante. 

Se você criar uma lista de frases (criada globalmente por padrão), aplique-a posteriormente como um descritor (recurso) a um modelo específico, ela será removida dos outros modelos. Essa remoção adiciona relevância à lista de frases para o modelo ao qual ela é aplicada, ajudando a melhorar a precisão que ela fornece no modelo. 

O sinalizador `enabledForAllModels` controla esse escopo de modelo na API. 

<a name="how-to-use-phrase-lists"></a>

### <a name="how-to-use-a-phrase-list"></a>Como usar uma lista de frases

[Crie uma lista de frases](luis-how-to-add-features.md) quando sua intenção ou entidade tiver palavras ou frases importantes, como:

* termos do setor
* gírias
* abreviações
* idioma específico da empresa
* linguagem que é de outro idioma, mas frequentemente usada em seu aplicativo
* Palavras-chave e frases em seu exemplo declarações

**Não** adicione todas as palavras ou frases possíveis. Em vez disso, adicione algumas palavras ou frases de cada vez e, em seguida, retreine e publique. À medida que a lista cresce ao longo do tempo, você pode achar que alguns termos têm muitas formas (sinônimos). Divida-os em outra lista. 

<a name="phrase-lists-help-identify-simple-exchangeable-entities"></a>

## <a name="when-to-use-an-entity-as-a-feature"></a>Quando usar uma entidade como um recurso 

Uma entidade pode ser adicionada como um recurso no nível de intenção ou de entidade. 

### <a name="entity-as-a-feature-to-an-intent"></a>Entidade como um recurso para uma intenção

Adicione uma entidade como um descritor (recurso) a uma intenção quando a detecção dessa entidade for significativa para a intenção.

Por exemplo, se a intenção é para reservar um vôo e a entidade são informações de tíquete (como o número de estações, origem e destino), localizar a entidade de informações de tíquete deve adicionar peso à previsão da tentativa de vôo do livro. 

### <a name="entity-as-a-feature-to-another-entity"></a>Entidade como um recurso para outra entidade

Uma entidade (A) deve ser adicionada como um recurso a outra entidade (B) quando a detecção dessa entidade (A) é significativa para a previsão da entidade (B).

Por exemplo, se a entidade endereço (a) for detectada, a localização do endereço (A) adiciona peso à previsão para a entidade endereço de envio (B). 

## <a name="best-practices"></a>Melhores práticas
Saiba mais [melhores práticas](luis-concept-best-practices.md).

## <a name="next-steps"></a>Passos seguintes

Ver [adicionar funcionalidades](luis-how-to-add-features.md) para saber mais sobre como adicionar funcionalidades à sua aplicação LUIS.