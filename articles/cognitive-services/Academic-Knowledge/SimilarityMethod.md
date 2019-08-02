---
title: Método de similaridade – API de Conhecimento Acadêmico
titlesuffix: Azure Cognitive Services
description: Use o método de similaridade para calcular a similaridade acadêmica de duas cadeias de caracteres.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 01/18/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: 855d29d2c55b841bbbe4e9eadce8c29ad85fad90
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704858"
---
# <a name="similarity-method"></a>Método de similaridade

A API REST de **similaridade** é usada para calcular a similaridade acadêmica entre duas cadeias de caracteres. 
<br>

**Ponto de extremidade REST:**
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/similarity?
```

## <a name="request-parameters"></a>Parâmetros do Pedido

Parâmetro        |Tipo de Dados      |Requerido | Descrição
----------|----------|----------|------------
**s1**        |Cadeia   |Sim  |Cadeia de caracteres * a ser comparada
**s2**        |Cadeia   |Sim  |Cadeia de caracteres * a ser comparada

<sub>* As cadeias de caracteres para comparar têm um comprimento máximo de 1 MB.</sub>
<br>

## <a name="response"></a>Resposta

Nome | Descrição
--------|---------
**SimilarityScore**        |Um valor de ponto flutuante que representa a similaridade do cosseno de S1 e S2, com valores próximos de 1,0, o que significa mais semelhante e valores mais próximos de-1,0, o que significa menos

<br>

## <a name="successerror-conditions"></a>Condições de êxito/erro

Status do HTTP | Reason | Resposta
-----------|----------|--------
**200**         |Êxito | Número de ponto flutuante
**400**         | Solicitação ou solicitação inválida | Mensagem de erro      
**500**         |Erro de servidor interno | Mensagem de erro
**Tempo limite atingido**     | O pedido excedeu o limite de tempo.  | Mensagem de erro

<br>

## <a name="example-calculate-similarity-of-two-partial-abstracts"></a>Exemplo: Calcular a similaridade de dois resumos parciais
#### <a name="request"></a>Pedido:
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/similarity?s1=Using complementary priors, we derive a fast greedy algorithm that can learn deep directed belief networks one layer at a time, provided the top two layers form an undirected associative memory
&s2=Deepneural nets with a large number of parameters are very powerful machine learning systems. However, overfitting is a serious problem in such networks
```
Neste exemplo, geramos a pontuação de similaridade entre dois resumos parciais usando a API de **similaridade** .
#### <a name="response"></a>Resposta:
```
0.520
```
#### <a name="remarks"></a>Comentários
A pontuação de similaridade é determinada pela avaliação dos conceitos acadêmicos por meio da incorporação de palavras. Neste exemplo, 0,52 significa que os dois resumos parciais são um pouco semelhantes.
<br>
