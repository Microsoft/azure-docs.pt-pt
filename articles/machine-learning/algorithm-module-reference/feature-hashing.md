---
title: Referência do módulo hashing de recurso
titleSuffix: Azure Machine Learning
description: Saiba como utilizar o módulo De Hashing feature no Azure Machine Learning para apresentar dados de texto.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 7178417a5c20afe5b1ed02bc526ec174704962df
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456255"
---
# <a name="feature-hashing-module-reference"></a>Referência do módulo hashing de recurso

Este artigo descreve um módulo incluído no designer de Machine Learning Azure (pré-visualização).

Utilize o módulo De Hashing feature para transformar um fluxo de texto inglês num conjunto de funcionalidades inteiros. Em seguida, pode passar esta funcionalidade de hashed definida para um algoritmo de aprendizagem automática para treinar um modelo de análise de texto.

A funcionalidade de hashing fornecida neste módulo baseia-se na estrutura nimbusml. Para mais informações, consulte a [aula de NgramHash.](https://docs.microsoft.com/python/api/nimbusml/nimbusml.feature_extraction.text.extractor.ngramhash?view=nimbusml-py-latest)

## <a name="what-is-feature-hashing"></a>O que é hashing de recurso?

O hashing de recurso funciona convertendo fichas únicas em inteiros. Opera nas cordas exatas que fornece como entrada e não realiza qualquer análise linguística ou pré-processamento. 

Por exemplo, tome um conjunto de frases simples como estas, seguidas de uma pontuação de sentimento. Assuma que pretende usar este texto para construir um modelo.

|Texto do utilizador|Sentimento|
|--------------|---------------|
|Adorei este livro.|3|
|Odiei este livro.|1|
|Este livro foi ótimo.|3|
|Adoro livros.|2|

Internamente, o módulo de hashing feature cria um dicionário de n-grams. Por exemplo, a lista de bigrams para este conjunto de dados seria algo assim:

|Termo (bigrams)|Frequência|
|------------|---------------|
|Este livro|3|
|Eu amava|1|
|Odiava.|1|
|Eu amo|1|

Pode controlar o tamanho dos n-gramas utilizando a propriedade **N-grams.** Se escolher bigrams, os unigramas também são calculados. O dicionário também incluiria termos únicos como estes:

|Termo (unigramas)|Frequência|
|------------|---------------|
|livro|3|
|I|3|
|livros|1|
|foi|1|

Após a construção do dicionário, o módulo De Hashing de Recurso converte os termos do dicionário em valores de hash. Em seguida, calcula se uma característica foi usada em cada caso. Para cada linha de dados de texto, o módulo produz um conjunto de colunas, uma coluna para cada característica hashed.

Por exemplo, depois do hashing, as colunas de recurso podem parecer algo assim:

|Classificação|Característica de hashing 1|Hashing recurso 2|Característica de hashing 3|
|-----|-----|-----|-----|
|4|1|1|0|
|5|0|0|0|

* Se o valor na coluna for 0, a linha não continha a característica hashed.
* Se o valor for 1, a linha continha a característica.

O hashing de recurso permite-lhe representar documentos de texto de comprimento variável como vetores de característica numérica de igual comprimento para reduzir a dimensionalidade. Se tentasse utilizar a coluna de texto para treinar como está, seria tratada como uma coluna de características categóricas com muitos valores distintos.

As saídas numéricas também tornam possível a utilização de métodos comuns de aprendizagem automática, incluindo classificação, agrupamento e recuperação de informação. Como as operações de procura podem usar hashes inteiros em vez de comparações de cordas, obter os pesos da funcionalidade também é muito mais rápido.

## <a name="configure-the-feature-hashing-module"></a>Configure o módulo hashing de funcionalidade

1.  Adicione o módulo De Hashing feature ao seu pipeline no designer.

1. Ligue o conjunto de dados que contém o texto que pretende analisar.

    > [!TIP]
    > Uma vez que o hashing da funcionalidade não realiza operações lexicais, tais como a desposição ou truncação, pode por vezes obter melhores resultados através do texto de pré-processamento antes de aplicar o hashing da funcionalidade. 

1. Defina **as colunas Target** para as colunas de texto que pretende converter para funcionalidades de hashed. Tenha em mente que:

    * As colunas devem ser do tipo de dados de cordas.
    
    * Escolher várias colunas de texto pode ter um impacto significativo na dimensionalidade da funcionalidade. Por exemplo, o número de colunas para um hash de 10 bits vai de 1.024 para uma única coluna para 2.048 para duas colunas.

1. Use **o bitsize hashing** para especificar o número de bits a utilizar quando estiver a criar a tabela de haxixe.
    
    O tamanho da broca padrão é 10. Para muitos problemas, este valor é adequado. Pode precisar de mais espaço para evitar colisões, dependendo do tamanho do vocabulário n-grams no texto de treino.
    
1. Para **N-grams**, introduza um número que defina o comprimento máximo dos n-gramas para adicionar ao dicionário de formação. Um n-grama é uma sequência *de* n palavras, tratada como uma unidade única.

    Por exemplo, se introduzir 3, unigramas, bigrams e trigramas serão criados.

1. Submeta o oleoduto.

## <a name="results"></a>Resultados

Após o processamento estar concluído, o módulo produz um conjunto de dados transformado no qual a coluna de texto original foi convertida em várias colunas. Cada coluna representa uma característica no texto. Dependendo da importação do dicionário, o conjunto de dados resultante pode ser grande:

|Nome da coluna 1|Coluna tipo 2|
|-------------------|-------------------|
|TEXTO DE UTILIZADOR|Coluna de dados original|
|SENTIMENTO|Coluna de dados original|
|USERTEXT - Característica de hashing 1|Coluna de recurso hashed|
|USERTEXT - Característica de hashing 2|Coluna de recurso hashed|
|USERTEXT - Característica de hashing n|Coluna de recurso hashed|
|USERTEXT - Característica de hashing 1024|Coluna de recurso hashed|

Depois de criar o conjunto de dados transformado, pode usá-lo como entrada para o módulo Modelo de Comboio.
 
## <a name="best-practices"></a>Melhores práticas

As seguintes boas práticas podem ajudá-lo a tirar o máximo partido do módulo De Hashing feature:

* Adicione um módulo de texto pré-processado antes de utilizar o Hashing de Recurso para pré-processar o texto de entrada. 

* Adicione um módulo De Colunas Select depois do módulo de hashing de funcionalidade seletiva para remover as colunas de texto do conjunto de dados de saída. Não precisa das colunas de texto depois de geradas as características de hashing.
    
* Considere utilizar estas opções de pré-processamento de texto, para simplificar resultados e melhorar a precisão:

    * Quebra de palavras
    * Parar a remoção de palavras
    * Normalização de casos
    * Remoção da pontuação e caracteres especiais
    * Consoante  

O conjunto ideal de métodos de pré-processamento para aplicar em qualquer solução depende do domínio, vocabulário e necessidade empresarial. oleoduto com os seus dados para ver quais os métodos de processamento de texto mais eficazes.

## <a name="next-steps"></a>Passos seguintes
            
Veja o [conjunto de módulos disponíveis](module-reference.md) para O Machine Learning Azure 
