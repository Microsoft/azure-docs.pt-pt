---
title: Extrair funcionalidades N-Gram da referência do módulo de texto
titleSuffix: Azure Machine Learning
description: Aprenda a utilizar o módulo Extract n-Gram no Azure Machine Learning para apresentar dados de texto.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/01/2019
ms.openlocfilehash: efe09c1d516b37c23b024e07ae387772fa7e5992
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79477617"
---
# <a name="extract-n-gram-features-from-text-module-reference"></a>Extrair funcionalidades N-Gram da referência do módulo de texto

Este artigo descreve um módulo em Azure Machine Learning designer (pré-visualização). Utilize as funcionalidades N-Gram extratos do módulo texto para *apresentar* dados de texto não estruturados. 

## <a name="configuration-of-the-extract-n-gram-features-from-text-module"></a>Configuração das funcionalidades N-Gram extratos do módulo de texto

O módulo suporta os seguintes cenários para a utilização de um dicionário n-grama:

* [Crie um novo dicionário n-gram](#create-a-new-n-gram-dictionary) a partir de uma coluna de texto gratuito.

* [Utilize um conjunto de funcionalidades de texto existentes](#use-an-existing-n-gram-dictionary) para apresentar uma coluna de texto gratuita.

* [Marque ou publique um modelo](#score-or-publish-a-model-that-uses-n-grams) que use n-grams.

### <a name="create-a-new-n-gram-dictionary"></a>Criar um novo dicionário n-gram

1.  Adicione as funcionalidades N-Gram extratos do módulo de texto ao seu pipeline e ligue o conjunto de dados que tem o texto que pretende processar.

1.  Utilize a **coluna de texto** para escolher uma coluna do tipo de corda que contenha o texto que pretende extrair. Como os resultados são verbosos, só se pode processar uma coluna de cada vez.

1. Defina o **modo Vocabulário** para **Criar** para indicar que está a criar uma nova lista de funcionalidades n-grama. 

1. Detete o **tamanho N-Grams** para indicar o tamanho *máximo* dos n-gramas para extrair e armazenar. 

    Por exemplo, se introduzir 3, unigramas, bigrams e trigramas serão criados.

1. **A função de ponderação** especifica como construir o vetor de características do documento e como extrair vocabulário a partir de documentos.

    * **Peso binário**: Atribui um valor de presença binário aos n-gramas extraídos. O valor para cada n-grama é 1 quando existe no documento, e 0 de outra forma.

    * **Peso TF**: Atribui uma pontuação de frequência de termo (TF) aos n-gramas extraídos. O valor para cada n-grama é a sua frequência de ocorrência no documento.

    * **Peso IDF**: Atribui uma pontuação de frequência de documento inversa (IDF) aos n-gramas extraídos. O valor para cada n-grama é o tronco do tamanho do corpus dividido pela sua frequência de ocorrência em todo o corpus.
    
      `IDF = log of corpus_size / document_frequency`
 
    *  **Peso TF-IDF**: Atribui uma pontuação de frequência/documento inverso (TF/IDF) à pontuação de n-gramas extraído. O valor para cada n-grama é a sua pontuação TF multiplicada pela sua pontuação IDF.

1. Detete o **comprimento mínimo** da palavra para o número mínimo de letras que pode ser usado em qualquer *palavra* num n-grama.

1. Utilize o **comprimento máximo** da palavra para definir o número máximo de letras que podem ser utilizadas em qualquer *palavra* num n-grama.

    Por predefinição, até 25 caracteres por palavra ou token são permitidos.

1. Utilize **a frequência absoluta do documento mínimo n-grama** para definir as ocorrências mínimas necessárias para que qualquer n-grama seja incluído no dicionário n-grama. 

    Por exemplo, se utilizar o valor padrão de 5, qualquer n-grama deve aparecer pelo menos cinco vezes no corpus para ser incluído no dicionário n-grama. 

1.  Definir **rácio máximo de documento n-grama** para o rácio máximo do número de linhas que contêm um n-grama particular, sobre o número de linhas no corpus geral.

    Por exemplo, uma razão de 1 indicaria que, mesmo que um n-grama específico esteja presente em cada linha, o n-grama pode ser adicionado ao dicionário n-grama. Mais tipicamente, uma palavra que ocorre em cada linha seria considerada uma palavra de ruído e seria removida. Para filtrar as palavras de ruído dependentes do domínio, tente reduzir esta relação.

    > [!IMPORTANT]
    > A taxa de ocorrência de palavras específicas não é uniforme. Varia de documento para documento. Por exemplo, se estiver a analisar comentários de clientes sobre um produto específico, o nome do produto pode ser de alta frequência e perto de uma palavra de ruído, mas ser um termo significativo noutros contextos.

1. Selecione a opção **Normalizar os vetores de características n-gramas** para normalizar os vetores de recurso. Se esta opção estiver ativada, cada vetor de características n-grama é dividido pela sua norma L2.

1. Submeta o oleoduto.

### <a name="use-an-existing-n-gram-dictionary"></a>Use um dicionário n-grama existente

1.  Adicione as funcionalidades N-Gram extratos do módulo de texto ao seu pipeline e ligue o conjunto de dados que tem o texto que pretende processar à porta **Dataset.**

1.  Utilize a **coluna de texto** para selecionar a coluna de texto que contém o texto que pretende apresentar. Por defeito, o módulo seleciona todas as colunas da **cadeia**de tipo . Para obter os melhores resultados, processe uma única coluna de cada vez.

1. Adicione o conjunto de dados guardado que contém um dicionário n-grama anteriormente gerado e conecte-o à porta **do vocabulário de entrada.** Também pode ligar a saída do **vocabulário resultado** de uma instância a montante das características N-Gram extratos do módulo Texto.

1. Para **o modo Vocabulário,** selecione a opção de atualização **ReadOnly** da lista de drop-down.

   A opção **ReadOnly** representa o corpus de entrada para o vocabulário de entrada. Em vez de calcular frequências de termo a partir do novo conjunto de dados de texto (na entrada esquerda), os pesos n-grama do vocabulário de entrada são aplicados como está.

   > [!TIP]
   > Use esta opção quando estiver a marcar um classificador de texto.

1.  Para todas as outras opções, consulte as descrições da propriedade na [secção anterior](#create-a-new-n-gram-dictionary).

1.  Submeta o oleoduto.

### <a name="score-or-publish-a-model-that-uses-n-grams"></a>Marque ou publique um modelo que use n-grams

1.  Copie as **funcionalidades n-grama do extrato do** módulo de texto do fluxo de dados de treino para o fluxo de dados de pontuação.

1.  Ligue a saída do Vocabulário de **Resultados** do fluxo de dados de treino ao **Input Vocabulary** no fluxo de dados de pontuação.

1.  No fluxo de trabalho de pontuação, modifique as funcionalidades n-gramas do extrato do módulo text e defina o parâmetro do **modo Vocabulário** para **ReadOnly**. Deixe tudo o resto na mesma.

1.  Para publicar o pipeline, guarde o **Result Vocabulary** como conjunto de dados.

1.  Ligue o conjunto de dados guardado às funcionalidades do Extrato N-Gram do módulo de texto no seu gráfico de pontuação.

## <a name="results"></a>Resultados

As funcionalidades N-Gram extratos do módulo texto criam dois tipos de saída: 

* **Conjunto de dados**de resultados : Esta saída é um resumo do texto analisado combinado com os n-gramas que foram extraídos. As colunas que não selecionou na opção coluna **texto** são transmitidas para a saída. Para cada coluna de texto que analisa, o módulo gera estas colunas:

  * **Matriz de ocorrências n-gram**: O módulo gera uma coluna para cada n-grama encontrada no corpus total e adiciona uma pontuação em cada coluna para indicar o peso do n-grama para essa linha. 

* **Vocabulário de resultados**: O vocabulário contém o dicionário n-grama real, juntamente com as pontuações de frequência de termo que são geradas como parte da análise. Pode guardar o conjunto de dados para reutilização com um conjunto diferente de inputs, ou para uma atualização posterior. Também pode reutilizar o vocabulário para modelação e pontuação.

### <a name="result-vocabulary"></a>Vocabulário de resultados

O vocabulário contém o dicionário n-gram com as pontuações de frequência do termo que são geradas como parte da análise. As pontuações DF e IDF são geradas independentemente de outras opções.

+ **ID**: Um identificador gerado por cada n-grama único.
+ **NGram**: O n-grama. Espaços ou outros separadores de palavras são substituídos pelo caráter de sublinhado.
+ **DF**: A pontuação de frequência de termo para o n-grama no corpus original.
+ **IDF**: A pontuação de frequência de documento inversa para o n-grama no corpus original.

Pode atualizar manualmente este conjunto de dados, mas pode introduzir erros. Por exemplo:

* Um erro é levantado se o módulo encontrar linhas duplicadas com a mesma chave no vocabulário de entrada. Certifique-se de que nenhuma linha no vocabulário tem a mesma palavra.
* O esquema de entrada dos conjuntos de dados do vocabulário deve coincidir exatamente, incluindo nomes de colunas e tipos de colunas. 
* A coluna **DE IDENTIFICAção** e a coluna **DF** devem ser do tipo inteiro. 
* A coluna **IDF** deve ser do tipo flutuador.

> [!Note]
> Não ligue a saída de dados diretamente ao módulo Modelo de Comboio. Deve remover colunas de texto gratuitas antes de serem alimentadas no Modelo de Comboio. Caso contrário, as colunas de texto gratuitas serão tratadas como características categóricas.

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning.
