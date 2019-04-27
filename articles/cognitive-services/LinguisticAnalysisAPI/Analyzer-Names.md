---
title: Analisador de nomenclatura estrutura - API de análise linguística
titlesuffix: Azure Cognitive Services
description: Saiba como a estrutura de nomenclatura do analisador de API de análise linguística permite flexibilidade e precisão.
services: cognitive-services
author: RichardSunMS
manager: nitinme
ms.service: cognitive-services
ms.subservice: linguistic-analysis
ms.topic: conceptual
ms.date: 03/23/2016
ms.author: lesun
ROBOTS: NOINDEX
ms.openlocfilehash: b2e956b099022751712f96ab0b15aa57c752b458
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60405261"
---
# <a name="analyzer-names"></a>Nomes de analisador

> [!IMPORTANT]
> A pré-visualização da API de Análise Linguística foi desativada no dia 9 de agosto de 2018. Recomendamos que utilize os [módulos de análise de texto do Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/text-analytics) para o processamento e a análise de texto.

Uma estrutura de nomenclatura um pouco complicada para analisadores são utilizadas para permitir que os dois flexibilidade no analisadores e precisão em compreender o que significa que um nome.
Nomes de analisador consistem em quatro partes: um ID, um tipo, uma especificação e uma implementação.
A função de cada componente definida abaixo.

## <a name="id"></a>ID
Em primeiro lugar, um analisador tem um ID exclusivo; um GUID.
Esses GUIDs devem ser alterado em ocasiões relativamente raras, mas são a única forma de descrever exclusivamente um analisador de particular.

## <a name="kind"></a>Tipo
Em seguida, cada analyzer é uma **tipo**.
Isso define em termos muito amplas devolveu o tipo de análise e exclusivamente deve definir a estrutura de dados usada para representar essa análise.
Atualmente, existem três tipos distintos:
 - [Tokens](Sentences-and-Tokens.md)
 - [Etiquetas de POS](Pos-Tagging.md)
 - [Árvore de constituintes](constituency-parsing.md)

## <a name="specification"></a>Especificação
Dentro de um determinado tipo, no entanto, especialistas em diferentes podem não concordo em como um fenômeno específico deve ser analisado.
Ao contrário de linguagens de programação, não existe nenhuma definição clara e exata de como isso deve ser feito.

Por exemplo, imagine a que estivéssemos tentando encontrar os tokens da sentença em inglês "Ele não está".
Em particular, considere a cadeia de caracteres "não".
Uma interpretação possível é que isso deve ser dividido em dois tokens de: "foi" e "not".
Em seguida, a frase alternativa "ele não foi enviado" teria o mesmo conjunto de tokens.
Outra possibilidade é dizer que devem ser dividido nos tokens "fez" e "n't".
O segundo token não normalmente consideraria uma palavra, mas essa abordagem mantém obter mais informações sobre a cadeia de caracteres superfície, o que pode ser útil.
Ou, talvez essa redução deve ser considerada uma única palavra.

Seja como for que opção feita, que a opção deve ser feita de forma consistente.
Esta é precisamente a função de um **especificação**: para decidir o que deve ser uma representação correta.

Saídas de analisador podem apenas ser bastante em comparação com dados que está em conformidade com a mesma especificação.

## <a name="implementation"></a>Implementação

Muitas vezes, existem vários modelos que tentam atingir os mesmos resultados, mas com características de desempenho diferente.
Um modelo poderá ser mais rápido porém menos precisas; outro poderá fazer uma troca diferente.

O **implementação** parte de um nome de analisador é utilizado para identificar este tipo de informação, para que os utilizadores podem escolher o analisador de mais adequado para as suas necessidades.
