---
title: Referência do módulo hashing de recurso
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo de Hashing de Recurso no designer de Aprendizagem automática Azure para apresentar dados de texto.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: cd48b32afee320aa3d252540d566317c374c73a8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "93420789"
---
# <a name="feature-hashing-module-reference"></a>Referência do módulo hashing de recurso

Este artigo descreve um módulo incluído no designer de Aprendizagem automática Azure.

Utilize o módulo hashing de recurso para transformar um fluxo de texto inglês num conjunto de funcionalidades de inteiro. Em seguida, pode passar este conjunto de funcionalidades de haxixe para um algoritmo de aprendizagem automática para treinar um modelo de análise de texto.

A funcionalidade de hashing de funcionalidade fornecida neste módulo baseia-se na estrutura nimbusml. Para mais informações, consulte [a classe NgramHash.](/python/api/nimbusml/nimbusml.feature_extraction.text.extractor.ngramhash?view=nimbusml-py-latest&preserve-view=true)

## <a name="what-is-feature-hashing"></a>O que é hashing de recurso?

O hashing de recurso funciona convertendo fichas únicas em inteiros. Funciona com as cordas exatas que fornece como entrada e não realiza qualquer análise linguística ou pré-processamento. 

Por exemplo, tome um conjunto de frases simples como estas, seguidas de uma pontuação de sentimento. Assuma que quer usar este texto para construir um modelo.

|Texto do utilizador|Sentimento|
|--------------|---------------|
|Adorei este livro.|3|
|Odiava este livro.|1|
|Este livro foi ótimo.|3|
|Adoro livros.|2|

Internamente, o módulo hashing de recurso cria um dicionário de n-gramas. Por exemplo, a lista de bigrams para este conjunto de dados seria algo assim:

|Termo (bigrams)|Frequência|
|------------|---------------|
|Este livro|3|
|Adorava.|1|
|Odiava|1|
|Adoro|1|

Pode controlar o tamanho dos n-gramas utilizando a propriedade **N-grams.** Se escolher bigrams, os unigramas também são calculados. O dicionário também incluiria termos únicos como estes:

|Termo (unigramas)|Frequência|
|------------|---------------|
|livro|3|
|I|3|
|livros|1|
|foi|1|

Após a construção do dicionário, o módulo hashing de recurso converte os termos do dicionário em valores de haxixe. Em seguida, calcula se uma funcionalidade foi usada em cada caso. Para cada linha de dados de texto, o módulo produz um conjunto de colunas, uma coluna para cada função hashed.

Por exemplo, após o haxixe, as colunas de recurso podem parecer algo assim:

|Classificação|Característica de hashing 1|Recurso de hashing 2|Característica de hashing 3|
|-----|-----|-----|-----|
|4|1|1|0|
|5|0|0|0|

* Se o valor na coluna for 0, a linha não continha a função hashed.
* Se o valor for 1, a linha continha a função.

O hashing de recurso permite-lhe representar documentos de texto de comprimento variável como vetores de característica numérica de igual comprimento para reduzir a dimensionalidade. Se tentasse utilizar a coluna de texto para treinar como está, seria tratada como uma coluna de recurso categórica com muitos valores distintos.

As saídas numéricas também possibilitam a utilização de métodos comuns de aprendizagem automática, incluindo classificação, agrupamento e recuperação de informação. Como as operações de procura podem usar hashes inteiros em vez de comparações de cordas, obter os pesos de recurso também é muito mais rápido.

## <a name="configure-the-feature-hashing-module"></a>Configure o módulo de hashing de recurso

1.  Adicione o módulo de hashing de recurso ao seu oleoduto no designer.

1. Ligue o conjunto de dados que contém o texto que pretende analisar.

    > [!TIP]
    > Como o hashing de recurso não realiza operações lexicais como o stemming ou a truncação, às vezes pode obter melhores resultados através do texto pré-processamento antes de aplicar o hashing de recurso. 

1. Desloque **as colunas-alvo** para as colunas de texto que pretende converter para funcionalidades hashed. Tenha em mente que:

    * As colunas devem ser do tipo de dados de cadeia.
    
    * Escolher várias colunas de texto pode ter um impacto significativo na dimensionalidade do recurso. Por exemplo, o número de colunas para um haxixe de 10 bits vai de 1.024 para uma única coluna para 2.048 para duas colunas.

1. Use **o bits de hashing** para especificar o número de bits a utilizar quando estiver a criar a tabela de haxixe.
    
    O tamanho da broca padrão é 10. Para muitos problemas, este valor é adequado. Você pode precisar de mais espaço para evitar colisões, dependendo do tamanho do vocabulário n-gramas no texto de treino.
    
1. Para **N-gramas,** introduza um número que defina o comprimento máximo dos n-gramas para adicionar ao dicionário de treino. Um n-gram é uma sequência de *n* palavras, tratada como uma unidade única.

    Por exemplo, se você entrar em 3, unigramas, bigrams e trigramas serão criados.

1. Envie o oleoduto.

## <a name="results"></a>Resultados

Após o processamento estar concluído, o módulo produz um conjunto de dados transformado no qual a coluna de texto original foi convertida em várias colunas. Cada coluna representa uma característica no texto. Dependendo da importência do dicionário, o conjunto de dados resultante pode ser grande:

|Nome da coluna 1|Coluna tipo 2|
|-------------------|-------------------|
|USERTEXT|Coluna de dados original|
|SENTIMENTO|Coluna de dados original|
|USERTEXT - Hashing feature 1|Coluna de recurso hashed|
|USERTEXT - Hashing feature 2|Coluna de recurso hashed|
|USERTEXT - Recurso de hashing n|Coluna de recurso hashed|
|USERTEXT - Hashing feature 1024|Coluna de recurso hashed|

Depois de criar o conjunto de dados transformado, pode usá-lo como entrada para o módulo Modelo de Comboio.
 
## <a name="best-practices"></a>Melhores práticas

As seguintes boas práticas podem ajudá-lo a tirar o máximo partido do módulo de Hashing de Recurso:

* Adicione um módulo de texto pré-processamento antes de utilizar o Hashing de Recurso para pré-processar o texto de entrada. 

* Adicione um módulo Select Columns após o módulo hashing de recurso para remover as colunas de texto do conjunto de dados de saída. Não precisa das colunas de texto depois de terem sido geradas as características do hashing.
    
* Considere usar estas opções de pré-processamento de texto, para simplificar os resultados e melhorar a precisão:

    * Quebra de palavras
    * Parar a remoção de palavras
    * Normalização de casos
    * Remoção de pontuação e caracteres especiais
    * Estação Stemming  

O conjunto ideal de métodos de pré-processamento para aplicar em qualquer solução depende do domínio, vocabulário e necessidade de negócio. pipeline com os seus dados para ver quais os métodos de processamento de texto mais eficazes.

## <a name="next-steps"></a>Passos seguintes
            
Veja o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning