---
title: Formato de dados - API de serviço de exploração de conhecimento
titlesuffix: Azure Cognitive Services
description: Saiba mais sobre o formato de dados no conhecimento exploração de serviço (KES) API.
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: bba257c61509d862bb3161625750f05a86af8770
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57532172"
---
# <a name="data-format"></a>Formato de dados

O ficheiro de dados descreve a lista de objetos para indexar.
Cada linha do arquivo Especifica os valores de atributo de um objeto no [formato JSON](https://json.org/) com codificação UTF-8.
Além dos atributos definidos na [esquema](SchemaFormat.md), cada objeto tem um atributo de "logprob" opcional que especifica a probabilidade do registo relativa entre os objetos.
Quando o serviço retorna objetos por ordem de diminuir a probabilidade, podemos usar "logprob" para indicar a ordem de retorna dos objetos correspondentes.
Dada uma probabilidade *p* entre 0 e 1, a probabilidade de registo correspondente pode ser computada como log (*p*), em que log() é a função de registo natural.
Quando é especificado nenhum valor para logprob, é utilizado o valor predefinido 0.

```json
{"logprob":-5.3, "Title":"latent dirichlet allocation", "Year":2003, "Author":{"Name":"david m blei", "Affiliation":"uc berkeley"}, "Author":{"Name":"andrew y ng", "Affiliation":"stanford"}, "Author":{"Name":"michael i jordan", "Affiliation":"uc berkeley"}}
{"logprob":-6.1, "Title":"probabilistic latent semantic indexing", "Year":1999, "Author":{"Name":"thomas hofmann", "Affiliation":"uc berkeley"}}
...
```

Para atributos de cadeia de caracteres, GUID e BLOBs, o valor é representado como uma cadeia de caracteres do JSON com aspas simples.  Para atributos numéricos (Int32, Int64, Double), o valor é representado como um número JSON.  Para atributos compostos, o valor é um objeto JSON que especifica os valores de atributo secundárias.  Para compilações de índice mais rápidas, presort os objetos, diminuindo a probabilidade de registo.

Em geral, um atributo pode ter 0 ou mais valores.  Se um atributo não tem nenhum valor, podemos simplesmente removê-la do JSON.  Se um atributo tem 2 ou mais valores, podemos pode repetir o par de valor de atributo no objeto JSON.  Em alternativa, podemos atribuir uma matriz JSON que contém os vários valores para o atributo.

```json
{"logprob":0, "Title":"0 keyword"}
{"logprob":0, "Title":"1 keyword", "Keyword":"foo"}
{"logprob":0, "Title":"2 keywords", "Keyword":"foo", "Keyword":"bar"}
{"logprob":0, "Title":"2 keywords (alt)", "Keyword":["foo", "bar"]}
```
