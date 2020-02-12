---
title: Referência de módulo de hash de recurso
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo hash de recurso no Azure Machine Learning para personalizarr dados de texto.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/01/2019
ms.openlocfilehash: 4340ee4ed1edda8590726151e07eec45c0751ed6
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152266"
---
# <a name="feature-hashing-module-reference"></a>Referência de módulo de hash de recurso

Este artigo descreve um módulo incluído no designer de Machine Learning Azure (pré-visualização).

Use o módulo hash de recurso para transformar um fluxo de texto em inglês em um conjunto de recursos inteiros. Em seguida, você pode passar esse conjunto de recursos com hash para um algoritmo de aprendizado de máquina para treinar um modelo de análise de texto.

A funcionalidade de hash de recurso fornecida neste módulo é baseada na estrutura nimbusml. Para mais informações, consulte a [aula de NgramHash.](https://docs.microsoft.com/python/api/nimbusml/nimbusml.feature_extraction.text.extractor.ngramhash?view=nimbusml-py-latest)

## <a name="what-is-feature-hashing"></a>O que é o hash de recurso?

O hash de recurso funciona convertendo tokens exclusivos em inteiros. Ele opera nas cadeias de caracteres exatas que você fornece como entrada e não executa nenhuma análise linguística ou pré-processamento. 

Por exemplo, faça um conjunto de frases simples como essas, seguido por uma pontuação de sentimentos. Suponha que você deseja usar esse texto para criar um modelo.

|Texto do usuário|Sentimento|
|--------------|---------------|
|Adorei este livro|3|
|Eu odiei este livro|1|
|Este livro foi ótimo|3|
|Eu adoro livros|2|

Internamente, o módulo hash de recurso cria um dicionário de n-grams. Por exemplo, a lista de bigrams para esse conjunto de tais seria algo assim:

|Termo (bigrams)|Frequência|
|------------|---------------|
|Este livro|3|
|Eu adore|1|
|Eu odiei|1|
|Eu adoro|1|

Pode controlar o tamanho dos n-gramas utilizando a propriedade **N-grams.** Se você escolher bigrams, unigrams também serão computados. O dicionário também incluiria termos únicos como estes:

|Termo (unigrams)|Frequência|
|------------|---------------|
|livro|3|
|I|3|
|livros|1|
|deveria|1|

Depois que o dicionário é criado, o módulo hash de recurso converte os termos do dicionário em valores de hash. Em seguida, ele computa se um recurso foi usado em cada caso. Para cada linha de dados de texto, o módulo gera um conjunto de colunas, uma coluna para cada recurso com hash.

Por exemplo, após o hash, as colunas de recurso podem ter uma aparência semelhante a esta:

|Classificação|Recurso de hash 1|Recurso de hash 2|Recurso de hash 3|
|-----|-----|-----|-----|
|4|1|1|0|
|5|0|0|0|

* Se o valor na coluna for 0, a linha não conterá o recurso com hash.
* Se o valor for 1, a linha conterá o recurso.

O hash de recurso permite que você represente documentos de texto de comprimento variável como vetores de recursos numéricos de comprimento igual para reduzir a dimensionalidade. Se você tentou usar a coluna de texto para treinamento como está, ela seria tratada como uma coluna de recursos categóricos com muitos valores distintos.

As saídas numéricas também possibilitam o uso de métodos comuns de aprendizado de máquina, incluindo classificação, clustering e recuperação de informações. Como as operações de pesquisa podem usar hashes inteiros em vez de comparações de cadeias de caracteres, obter os pesos dos recursos também é muito mais rápido.

## <a name="configure-the-feature-hashing-module"></a>Configurar o módulo hash de recurso

1.  Adicione o módulo hash de recurso ao seu pipeline no designer.

1. Conecte o conjunto de um que contém o texto que você deseja analisar.

    > [!TIP]
    > Como o hash de recurso não executa operações léxicas como lematização ou truncamento, às vezes você pode obter resultados melhores por meio de pré-processamento de texto antes de aplicar o hash de recurso. 

1. Defina **as colunas Target** para as colunas de texto que pretende converter para funcionalidades de hashed. Tenha em mente que:

    * As colunas devem ser do tipo de dados de cadeia de caracteres.
    
    * Escolher várias colunas de texto pode ter um impacto significativo na dimensionalidade do recurso. Por exemplo, o número de colunas para um hash de 10 bits vai de 1.024 para uma única coluna a 2.048 para duas colunas.

1. Use **o bitsize hashing** para especificar o número de bits a utilizar quando estiver a criar a tabela de haxixe.
    
    O tamanho de bit padrão é 10. Para muitos problemas, esse valor é adequado. Talvez seja necessário mais espaço para evitar colisões, dependendo do tamanho do vocabulário de n-grams no texto de treinamento.
    
1. Para **N-grams**, introduza um número que defina o comprimento máximo dos n-gramas para adicionar ao dicionário de formação. Um n-grama é uma sequência *de* n palavras, tratada como uma unidade única.

    Por exemplo, se você inserir 3, unigrams, bigrams e trigrams serão criados.

1. Executar o pipeline.

## <a name="results"></a>Resultados

Depois que o processamento é concluído, o módulo gera um conjunto de resultados transformado no qual a coluna de texto original foi convertida em várias colunas. Cada coluna representa um recurso no texto. Dependendo do quão significativo for o dicionário, o conjunto de resultados resultante pode ser grande:

|Nome da coluna 1|Tipo de coluna 2|
|-------------------|-------------------|
|UserText|Coluna de dados original|
|SENTIMENTO|Coluna de dados original|
|UserText-recurso de hash 1|Coluna de recurso com hash|
|UserText-recurso de hash 2|Coluna de recurso com hash|
|UserText-recurso de hash n|Coluna de recurso com hash|
|UserText-recurso de hashing 1024|Coluna de recurso com hash|

Depois de criar o conjunto de dados transformado, você pode usá-lo como a entrada para o módulo modelo de treinamento.
 
### <a name="best-practices"></a>Melhores práticas

As práticas recomendadas a seguir podem ajudá-lo a obter o máximo do módulo de hash de recurso:

* Adicione um módulo de texto de pré-processamento antes de usar o hash de recurso para pré-processar o texto de entrada. 

* Adicione um módulo selecionar colunas após o módulo hash de recurso para remover as colunas de texto do conjunto de resultados de saída. Você não precisa das colunas de texto depois que os recursos de hash tiverem sido gerados.
    
* Considere usar essas opções de pré-processamento de texto para simplificar os resultados e melhorar a precisão:

    * Quebra de palavras
    * Parando a remoção do Word
    * Normalização de caso
    * Remoção de Pontuação e caracteres especiais
    * Lematização  

O conjunto ideal de métodos de pré-processamento a serem aplicados em qualquer solução depende do domínio, do vocabulário e da necessidade de negócios. Pipeline com seus dados para ver quais métodos de processamento de texto são mais eficazes.

## <a name="next-steps"></a>Passos seguintes
            
Veja o [conjunto de módulos disponíveis](module-reference.md) para O Machine Learning Azure 
