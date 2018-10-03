---
title: Análise de constituintes - API de análise linguística
titlesuffix: Azure Cognitive Services
description: Saiba mais sobre a forma como a análise, também conhecido como "frase analisar a estrutura," de grupos identifica frases no texto.
services: cognitive-services
author: RichardSunMS
manager: cgronlun
ms.service: cognitive-services
ms.component: linguistic-analysis
ms.topic: conceptual
ms.date: 03/21/2016
ms.author: lesun
ROBOTS: NOINDEX
ms.openlocfilehash: 89832f2d936a08df8b6f9e846c3dd4a5665c06a4
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/03/2018
ms.locfileid: "48238629"
---
# <a name="constituency-parsing"></a>Análise de grupos

> [!IMPORTANT]
> A pré-visualização de análise linguística foi desativada no dia 9 de Agosto de 2018. Recomendamos que utilize [módulos de análise de texto do Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/text-analytics) para processamento de texto e análise.

O objetivo da análise (também conhecido como "frase estrutura análise") de grupos é identificar as frases no texto.
Isso pode ser útil quando a extração de informações de texto.
Clientes talvez queiram para localizar os nomes das funcionalidades ou expressões-chave a partir de um grande corpo de texto e para ver os modificadores e as ações que envolvem cada frase desse tipo.

## <a name="phrases"></a>Frases

Para uma linguísticas, um *frase* é mais do que apenas uma sequência de palavras.
Para ser uma expressão, um grupo de palavras tem de se reúnem para reproduzir uma função específica da sentença.
Esse grupo de palavras pode ser movido em conjunto ou substituído como um todo, e a frase deve permanecer fluente e gramatical.

Considere a frase

> Quero encontrar um automóvel híbrida novo com Bluetooth.

Esta frase contiver a expressão de nome: "um novo automobile de híbrida com Bluetooth".
Como sabemos que se trata de uma frase?
Podemos reescrever a sentença (um pouco poetically) ao mover essa frase inteira para a frente:

> Um novo automóvel híbrida com Bluetooth quero encontrar.

Em alternativa, pode substituir essa frase com uma expressão de função e significado, como "um carro novo especial" semelhantes:

> Quero encontrar um carro novo especial.

Se em vez disso, escolhemos um subconjunto de diferente de palavras, estas tarefas de substituição poderia levar a frases estranhas ou ilegíveis.
Considere o que acontece quando movemos "encontrar um novo" para a frente:

> Encontre um novo que quero automóvel híbrida com Bluetooth.

Os resultados é muito difícil de ler e compreender.

O objetivo de um analisador é encontrar todas essas expressões.
Curiosamente, em linguagem natural, as frases tendem a ser aninhado dentro das outras.
Uma representação natural destas expressões é uma árvore, como o seguinte:

![Árvore](./Images/tree.png)

Essa árvore, os ramos marcados como "NP" são frases substantivo.
Existem vários desses frases: *eu*, *um automóvel híbrida novo*, *Bluetooth*, e *um automóvel híbrida novo com Bluetooth*.

## <a name="phrase-types"></a>Tipos de expressão

| Etiqueta | Descrição | Exemplo |
|-------|-------------|---------|
|ADJP   | Frase adjective | "então, rude" |
|ADVP   | Frase de adverb | "claro por meio de" |
|CONJP  | Expressão de conjunto | ", bem como" |
|FRAG   | Fragmento, utilizado para entradas incompletas ou fragmentary | "Altamente recomendado..." |
|INTJ   | Interjection | "Felicidade" |
|LST    | Marcador de lista, incluindo pontuação | "#4)" |
|NAC    | Não A constituintes, usado para indicar o controlo de âmbito de uma frase de acesso não-constituinte |  "e para uma boa dose" em "fazer as coisas e para uma boa lidar" |
|NP | Expressão de nome próprio | "um pancake potato pedaços" |
|NX | Usada dentro de determinados NPs complexos para marcar o cabeçalho| |
|PP | Frase de Prepositional| "no conjunto" |
|PRN    | Aninhamentos| "(então, chamada)" |
|PRT    | Nuvem de partículas| "de saída" em "rasguei out" |
|QP | Frase de quantidade (ou seja, complexa medida/quantidade) dentro de uma expressão de nome próprio| "em torno de US $75" |
|RRC    | Cláusula relativo reduzida.| "ainda não resolvidos" em "muitos problemas ainda não resolvidos" |
|S  | Cláusula ou frase. | "Esta é uma frase."
|SBAR   | Cláusula de subordinado, muitas vezes, introduzida por um conjunto subordinating | "como eu esquerda" em "Olhei como deixei."|
|SBARQ  | Pergunta direta introduzida por uma palavra-q ou - expressão | "O que foi o ponto de?" |
|SINV   | Frase declarativa invertida | "Nenhum momento tinha eles conhecimento." (Observe como o assunto normal "dizem" foi movidas para depois do verbo "foi") |
|SQ | Invertido Sim/não pergunta ou cláusula de principal de uma pergunta-q | "Eles obteve o carro?" |
|UCP    | Ao contrário da frase coordenada| "pequeno e com bugs" (tenha em atenção a como um adjetivo e uma frase preposition são conjoined com "e")|
|VICE-PRESIDENTE | Frase de verbo | "foi executada na floresta" |
|WHADJP | Q-adjetivo frase | "como desconfortável" |
|WHADVP | Q-adverb frase| "quando" |
|WHNP   | Frase de q-nome| "qual potato", "quantidade letrinhas"|
|WHPP   | Q prepositional frase| "em país no qual"|
|X  | Desconhecido, incerteza ou unbracketable.| primeiro "a" em "a... a letrinhas" |


## <a name="specification"></a>Especificação

Árvores aqui usam as expressões de S partir do [Penn Treebank](https://catalog.ldc.upenn.edu/ldc99t42).
