---
title: Frases e tokens - API de análise linguística
titlesuffix: Azure Cognitive Services
description: Saiba mais sobre a separação e atomização na API de análise linguística.
services: cognitive-services
author: DavidLiCIG
manager: nitinme
ms.service: cognitive-services
ms.subservice: linguistic-analysis
ms.topic: conceptual
ms.date: 03/21/2016
ms.author: davl
ROBOTS: NOINDEX
ms.openlocfilehash: 435513023cf74bbc259cb922220d5f9940452d79
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60635471"
---
# <a name="sentence-separation-and-tokenization"></a>Separação e atomização

> [!IMPORTANT]
> A pré-visualização da API de Análise Linguística foi desativada no dia 9 de agosto de 2018. Recomendamos que utilize os [módulos de análise de texto do Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/text-analytics) para o processamento e a análise de texto.

## <a name="background-and-motivation"></a>Em segundo plano e motivação

Tendo em conta um corpo de texto, a primeira etapa de análise linguística é quebrá-lo em frases e tokens.

### <a name="sentence-separation"></a>Separação

Na primeira vista, parece que a divisão de texto em sentenças é simples: basta encontrar os marcadores de final da sentença e quebrar as frases lá.
No entanto, essas marcas geralmente são complicadas e é ambígua.

Considere o seguinte texto de exemplo:

> O que falou em favor?!? Eu não conhecer "novo proposta." o diretor É importante para o SR. e Sra Smith.

Este texto contém três frases:

- O que falou em favor?!?
- Eu não conhecer "novo proposta." o diretor
- É importante para o SR. e Sra Smith.

Observe como as extremidades de frases são marcadas como diferentes formas.
A primeira termina dentro de uma combinação de pontos de interrogação e pontos de exclamação (por vezes denominados um interrobang).
O segundo termina com um período ou stop completo, mas as aspas de seguintes deve sejam passado para a frase anterior.
A frase de terceiro, pode ver como esse mesmo caractere de ponto final pode ser usado para marcar abreviaturas também.
Olhando apenas a pontuação fornece um conjunto de bom candidato, mas é necessário qualquer trabalho adicional para identificar os limites de sentença verdadeiro.

### <a name="tokenization"></a>Atomização

A próxima tarefa é dividir essas frases em tokens.
Na maior parte, em inglês tokens são delimitadas por espaço em branco.
(Localizar tokens ou palavras é muito mais fácil em inglês do que em chinês, onde os espaços são usados principalmente não entre as palavras.
A primeira frase pode ser escrita como "Whatdidyousay?")

Existem alguns casos difícil.
Em primeiro lugar, pontuação normalmente (mas não sempre) deve ser dividida afastado disso em torno de contexto.
Em segundo lugar, tem o inglês *contrações*, como "não" ou "é", em que as palavras foram comprimidas e abreviadas em partes mais pequenas.
O objetivo do atomizador é dividir a seqüência de caracteres em palavras.

Vamos voltar para as frases de exemplo acima.
Agora, colocamos um ponto"center" (&middot;) entre cada token distinto.

- O que &middot; fez &middot; &middot; dizer &middot; favor?!?
- Eu &middot; fez &middot; n't &middot; ouvir &middot; sobre &middot; o &middot; diretor &middot; do &middot; " &middot; novo &middot; proposta &middot; . &middot; "
- Ele &middot; da &middot; importante &middot; para &middot; SR.&middot; e &middot; Mrs. &middot; Smith &middot; .

Observe como a maioria dos tokens são palavras que encontraria no dicionário (por exemplo, *importantes*, *diretor*).
Outros são compostos por unicamente de pontuação.
Por fim, existem mais incomuns tokens para representar contrações como *n't* para *não*, e, como o possessives *do*.
Este atomização nos permite lidar com a palavra *não* e a frase *não* de forma mais consistente.

## <a name="specification"></a>Especificação

É importante tomar decisões consistentes sobre o que é composto por uma sentença e um token.
Podemos contar com a especificação do [Penn Treebank](https://catalog.ldc.upenn.edu/LDC99T42) (alguns detalhes adicionais estão disponíveis em ftp://ftp.cis.upenn.edu/pub/treebank/public_html/tokenization.html a maiúsculas e minúsculas).
