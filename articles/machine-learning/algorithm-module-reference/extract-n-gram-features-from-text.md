---
title: Extrair recursos de N-Gram de referência de módulo de texto
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo extrair N-Gram no Azure Machine Learning para personalizarr dados de texto.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/01/2019
ms.openlocfilehash: cae128505c61e7c640819041c6ffdae10a4947e7
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152283"
---
# <a name="extract-n-gram-features-from-text-module-reference"></a>Extrair recursos de N-Gram de referência de módulo de texto

Este artigo descreve um módulo em Azure Machine Learning designer (pré-visualização). Utilize as funcionalidades N-Gram extratos do módulo texto para *apresentar* dados de texto não estruturados. 

## <a name="configuration-of-the-extract-n-gram-features-from-text-module"></a>Configuração dos recursos de extrair N-Gram do módulo de texto

O módulo dá suporte aos seguintes cenários para usar um dicionário de n-Gram:

* [Crie um novo dicionário n-gram](#create-a-new-n-gram-dictionary) a partir de uma coluna de texto gratuito.

* [Utilize um conjunto de funcionalidades de texto existentes](#use-an-existing-n-gram-dictionary) para apresentar uma coluna de texto gratuita.

* [Marque ou publique um modelo](#score-or-publish-a-model-that-uses-n-grams) que use n-grams.

### <a name="create-a-new-n-gram-dictionary"></a>Criar um novo dicionário de n-Gram

1.  Adicione os recursos extrair N-Gram do módulo de texto ao seu pipeline e conecte o conjunto de módulos que contém o texto que você deseja processar.

1.  Utilize a **coluna de texto** para escolher uma coluna do tipo de corda que contenha o texto que pretende extrair. Como os resultados são detalhados, você pode processar apenas uma única coluna de cada vez.

1. Defina o **modo Vocabulário** para **Criar** para indicar que está a criar uma nova lista de funcionalidades n-grama. 

1. Detete o **tamanho N-Grams** para indicar o tamanho *máximo* dos n-gramas para extrair e armazenar. 

    Por exemplo, se você inserir 3, unigrams, bigrams e trigrams serão criados.

1. **A função de ponderação** especifica como construir o vetor de características do documento e como extrair vocabulário a partir de documentos.

    * **Peso binário**: Atribui um valor de presença binário aos n-gramas extraídos. O valor de cada n-Gram é 1 quando ele existe no documento; caso contrário, 0.

    * **Peso TF**: Atribui uma pontuação de frequência de termo (TF) aos n-gramas extraídos. O valor de cada n-Gram é sua frequência de ocorrência no documento.

    * **Peso IDF**: Atribui uma pontuação de frequência de documento inversa (IDF) aos n-gramas extraídos. O valor de cada n-Gram é o log do tamanho de Corpus dividido por sua frequência de ocorrência no Corpus inteiro.
    
      `IDF = log of corpus_size / document_frequency`
 
    *  **Peso TF-IDF**: Atribui uma pontuação de frequência/documento inverso (TF/IDF) à pontuação de n-gramas extraído. O valor de cada n-Gram é sua pontuação de TF multiplicada por sua pontuação de IDF.

1. Detete o **comprimento mínimo** da palavra para o número mínimo de letras que pode ser usado em qualquer *palavra* num n-grama.

1. Utilize o **comprimento máximo** da palavra para definir o número máximo de letras que podem ser utilizadas em qualquer *palavra* num n-grama.

    Por padrão, são permitidos até 25 caracteres por palavra ou token.

1. Utilize **a frequência absoluta do documento mínimo n-grama** para definir as ocorrências mínimas necessárias para que qualquer n-grama seja incluído no dicionário n-grama. 

    Por exemplo, se você usar o valor padrão de 5, qualquer n-Gram deverá aparecer pelo menos cinco vezes no corpus a ser incluído no dicionário de n-Gram. 

1.  Definir **rácio máximo de documento n-grama** para o rácio máximo do número de linhas que contêm um n-grama particular, sobre o número de linhas no corpus geral.

    Por exemplo, uma taxa de 1 indicaria que, mesmo se um n-Gram específico estiver presente em cada linha, o n-Gram poderá ser adicionado ao dicionário de n-Gram. Normalmente, uma palavra que ocorre em cada linha seria considerada uma palavra de ruído e seria removida. Para filtrar as palavras de ruído dependentes do domínio, tente reduzir essa taxa.

    > [!IMPORTANT]
    > A taxa de ocorrência de palavras específicas não é uniforme. Varia de documento para documento. Por exemplo, se você estiver analisando comentários de clientes sobre um produto específico, o nome do produto poderá ser muito alta, e perto de uma palavra de ruído, mas ser um termo significativo em outros contextos.

1. Selecione a opção **Normalizar os vetores de características n-gramas** para normalizar os vetores de recurso. Se essa opção estiver habilitada, cada vetor de recurso de n-Gram será dividido por sua norma de L2.

1. Executar o pipeline.

### <a name="use-an-existing-n-gram-dictionary"></a>Usar um dicionário de n-Gram existente

1.  Adicione as funcionalidades N-Gram extratos do módulo de texto ao seu pipeline e ligue o conjunto de dados que tem o texto que pretende processar à porta **Dataset.**

1.  Utilize a **coluna de texto** para selecionar a coluna de texto que contém o texto que pretende apresentar. Por defeito, o módulo seleciona todas as colunas da **cadeia**de tipo . Para obter melhores resultados, processe uma única coluna de cada vez.

1. Adicione o conjunto de dados guardado que contém um dicionário n-grama anteriormente gerado e conecte-o à porta **do vocabulário de entrada.** Também pode ligar a saída do **vocabulário resultado** de uma instância a montante das características N-Gram extratos do módulo Texto.

1. Para **o modo Vocabulário,** selecione a opção de atualização **ReadOnly** da lista de drop-down.

   A opção **ReadOnly** representa o corpus de entrada para o vocabulário de entrada. Em vez de calcular frequências de termo do novo conjunto de dados de texto (na entrada à esquerda), os pesos de n-Gram do vocabulário de entrada são aplicados como estão.

   > [!TIP]
   > Use esta opção quando estiver pontuando um classificador de texto.

1.  Para todas as outras opções, consulte as descrições da propriedade na [secção anterior](#create-a-new-n-gram-dictionary).

1.  Executar o pipeline.

### <a name="score-or-publish-a-model-that-uses-n-grams"></a>Pontuar ou publicar um modelo que usa n-grams

1.  Copie as **funcionalidades n-grama do extrato do** módulo de texto do fluxo de dados de treino para o fluxo de dados de pontuação.

1.  Ligue a saída do Vocabulário de **Resultados** do fluxo de dados de treino ao **Input Vocabulary** no fluxo de dados de pontuação.

1.  No fluxo de trabalho de pontuação, modifique as funcionalidades n-gramas do extrato do módulo text e defina o parâmetro do **modo Vocabulário** para **ReadOnly**. Deixe todas as outras as mesmas.

1.  Para publicar o pipeline, guarde o **Result Vocabulary** como conjunto de dados.

1.  Conecte o conjunto de texto salvo ao módulo extrair os recursos do N-Gram do Text no grafo de pontuação.

## <a name="results"></a>Resultados

O módulo extrair recursos N-Gram do texto cria dois tipos de saída: 

* **Conjunto de dados**de resultados : Esta saída é um resumo do texto analisado combinado com os n-gramas que foram extraídos. As colunas que não selecionou na opção coluna **texto** são transmitidas para a saída. Para cada coluna de texto que você analisa, o módulo gera estas colunas:

  * **Matriz de ocorrências n-gram**: O módulo gera uma coluna para cada n-grama encontrada no corpus total e adiciona uma pontuação em cada coluna para indicar o peso do n-grama para essa linha. 

* **Vocabulário de resultados**: O vocabulário contém o dicionário n-grama real, juntamente com as pontuações de frequência de termo que são geradas como parte da análise. Você pode salvar o conjunto de informações para reutilização com um conjunto diferente de entradas ou para uma atualização posterior. Você também pode reutilizar o vocabulário para modelagem e pontuação.

### <a name="result-vocabulary"></a>Vocabulário de resultado

O vocabulário contém o dicionário de n-Gram com as pontuações de frequência de termos que são geradas como parte da análise. As pontuações de DF e IDF são geradas independentemente de outras opções.

+ **ID**: Um identificador gerado por cada n-grama único.
+ **NGram**: O n-grama. Espaços ou outros separadores de palavras são substituídos pelo caractere de sublinhado.
+ **DF**: A pontuação de frequência de termo para o n-grama no corpus original.
+ **IDF**: A pontuação de frequência de documento inversa para o n-grama no corpus original.

Você pode atualizar manualmente esse conjunto de um, mas pode introduzir erros. Por exemplo:

* Um erro será gerado se o módulo encontrar linhas duplicadas com a mesma chave no vocabulário de entrada. Certifique-se de que duas linhas no vocabulário tenham a mesma palavra.
* O esquema de entrada dos conjuntos de dados de vocabulário deve corresponder exatamente, incluindo nomes de coluna e tipos de coluna. 
* A coluna **DE IDENTIFICAção** e a coluna **DF** devem ser do tipo inteiro. 
* A coluna **IDF** deve ser do tipo flutuador.

> [!Note]
> Não conecte a saída de dados ao módulo treinar modelo diretamente. Você deve remover as colunas de texto livre antes que elas sejam inseridas no modelo de treinamento. Caso contrário, as colunas de texto livre serão tratadas como recursos categóricos.

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning.
