---
title: Extrair características N-Gram da referência do módulo de texto
titleSuffix: Azure Machine Learning
description: Aprenda a utilizar o módulo Extract N-Gram no designer de Aprendizagem automática Azure para apresentar dados de texto.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 12/08/2019
ms.openlocfilehash: 37a10d90fa0e277fbe45d9f1377e365cb3d42996
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "96861465"
---
# <a name="extract-n-gram-features-from-text-module-reference"></a>Extrair características N-Gram da referência do módulo de texto

Este artigo descreve um módulo no designer de Aprendizagem automática Azure. Utilize as funcionalidades Extract N-Gram do módulo text para *apresentar dados* de texto não estruturados. 

## <a name="configuration-of-the-extract-n-gram-features-from-text-module"></a>Configuração das funcionalidades de extração N-Gram do módulo de texto

O módulo suporta os seguintes cenários para a utilização de um dicionário n-gram:

* [Crie um novo dicionário n-gram a](#create-a-new-n-gram-dictionary) partir de uma coluna de texto livre.

* [Utilize um conjunto de funcionalidades de texto existente](#use-an-existing-n-gram-dictionary) para apresentar uma coluna de texto gratuita.

* [Marque ou desloque um modelo](#build-inference-pipeline-that-uses-n-grams-to-deploy-a-real-time-endpoint) que utilize n-gramas.

### <a name="create-a-new-n-gram-dictionary"></a>Criar um novo dicionário n-grama

1.  Adicione as funcionalidades Extract N-Gram do módulo de texto ao seu pipeline e conecte o conjunto de dados que tem o texto que pretende processar.

1.  Utilize a **coluna de texto** para escolher uma coluna de tipo de corda que contenha o texto que pretende extrair. Como os resultados são verbosos, só se pode processar uma única coluna de cada vez.

1. Desa ajuste **o modo Vocabulário** para **Criar** para indicar que está a criar uma nova lista de funcionalidades n-gram. 

1. Desempenhe o **tamanho N-Grams** para indicar o tamanho *máximo* dos n-gramas para extrair e armazenar. 

    Por exemplo, se você entrar em 3, unigramas, bigrams e trigramas serão criados.

1. **Função de ponderação** especifica como construir o vetor de recursos documentais e como extrair vocabulário de documentos.

    * **Peso binário**: Atribui um valor de presença binária aos n-gramas extraídos. O valor para cada n-grama é 1 quando existe no documento, e 0 de outra forma.

    * **Peso TF**: Atribui uma pontuação de frequência de termo (TF) às n-gramas extraídas. O valor para cada n-grama é a sua frequência de ocorrência no documento.

    * **Peso IDF**: Atribui uma pontuação inversa de frequência documental (IDF) às n-gramas extraídas. O valor para cada n-grama é o log do tamanho do corpus dividido pela sua frequência de ocorrência em todo o corpus.
    
      `IDF = log of corpus_size / document_frequency`
 
    *  **Peso TF-IDF**: Atribui uma pontuação de frequência/documento inverso (TF/IDF) à nota n-gramas extraída. O valor para cada n-gram é a sua pontuação TF multiplicada pela sua pontuação IDF.

1. Desa esta medida o **comprimento mínimo** da palavra para o número mínimo de letras que podem ser utilizadas numa *única palavra* num n-grama.

1. Utilize **o comprimento máximo** da palavra para definir o número máximo de letras que podem ser utilizadas em qualquer *palavra* num n-grama.

    Por predefinição, são permitidos até 25 caracteres por palavra ou ficha.

1. Utilize **a frequência absoluta do documento n-gram mínimo** para definir as ocorrências mínimas necessárias para que qualquer n-grama seja incluído no dicionário n-grama. 

    Por exemplo, se utilizar o valor predefinido de 5, qualquer n-grama deve aparecer pelo menos cinco vezes no corpus para ser incluído no dicionário n-grama. 

1.  Desa coumar o **rácio máximo de n-grama** para a relação máxima do número de linhas que contêm um determinado n-grama, sobre o número de linhas no corpus geral.

    Por exemplo, uma relação de 1 indicaria que, mesmo que um n-gram específico esteja presente em cada linha, o n-gram pode ser adicionado ao dicionário n-grama. Mais tipicamente, uma palavra que ocorre em cada linha seria considerada uma palavra de ruído e seria removida. Para filtrar as palavras de ruído dependentes do domínio, tente reduzir esta relação.

    > [!IMPORTANT]
    > A taxa de ocorrência de palavras específicas não é uniforme. Varia de documento para documento. Por exemplo, se estiver a analisar comentários do cliente sobre um produto específico, o nome do produto pode ser de alta frequência e perto de uma palavra de ruído, mas será um termo significativo em outros contextos.

1. Selecione a opção **Normalize vetores de recurso n-gram** para normalizar os vetores de recurso. Se esta opção estiver ativada, cada vetor de recurso n-gram é dividido pela sua norma L2.

1. Envie o oleoduto.

### <a name="use-an-existing-n-gram-dictionary"></a>Use um dicionário n-grama existente

1.  Adicione as funcionalidades Extract N-Gram do módulo de texto ao seu pipeline e conecte o conjunto de dados que tem o texto que pretende processar para a porta **Dataset.**

1.  Utilize a **coluna Text** para selecionar a coluna de texto que contém o texto que pretende apresentar. Por predefinição, o módulo seleciona todas as colunas de **cadeia** tipo . Para obter os melhores resultados, processe uma única coluna de cada vez.

1. Adicione o conjunto de dados guardado que contém um dicionário n-grama previamente gerado e conecte-o à porta **do vocabulário de entrada.** Também pode ligar a saída do **vocabulário Resultado** de uma instância a montante das características extrato N-Gram do módulo Text.

1. Para **o modo Vocabulário,** selecione a opção de atualização **ReadOnly** da lista de drop-down.

   A opção **ReadOnly** representa o corpus de entrada para o vocabulário de entrada. Em vez de calcular frequências de termo a partir do novo conjunto de dados de texto (na entrada esquerda), os pesos n-grama do vocabulário de entrada são aplicados como está.

   > [!TIP]
   > Utilize esta opção quando estiver a marcar um classificador de texto.

1.  Para todas as outras opções, consulte as descrições da propriedade na [secção anterior.](#create-a-new-n-gram-dictionary)

1.  Envie o oleoduto.

### <a name="build-inference-pipeline-that-uses-n-grams-to-deploy-a-real-time-endpoint"></a>Construa um gasoduto de inferência que utilize n-grams para implantar um ponto final em tempo real

Um gasoduto de treino que contém **extrato n-gramas recurso do** modelo de texto e **pontuação** para fazer a previsão no conjunto de dados de teste, é construído na seguinte estrutura:

:::image type="content" source="./media/module/extract-n-gram-training-pipeline-score-model.png" alt-text="Extrair exemplo do gasoduto de treinamento N-Grams" border="true":::

**O modo vocabulário** do extrato circulado **N-Grams Recurso do** módulo de texto é **Criar,** e o **modo vocabulário** do módulo que se conecta ao módulo **'Modelo de Pontuação'** é **ReadOnly**.

Depois de submeter o gasoduto de treino acima com sucesso, pode registar a saída do módulo circulado como conjunto de dados.

:::image type="content" source="./media/module/extract-n-gram-output-voc-register-dataset.png" alt-text="conjunto de dados de registo" border="true":::

Então pode criar um pipeline de inferência em tempo real. Depois de criar o gasoduto de inferência, tem de ajustar manualmente o seu gasoduto de inferência como se seguisse:

:::image type="content" source="./media/module/extract-n-gram-inference-pipeline.png" alt-text="gasoduto de inferência" border="true":::

Em seguida, submeta o gasoduto de inferência e implante um ponto final em tempo real.

## <a name="results"></a>Resultados

As funcionalidades extrato N-Gram do módulo de texto criam dois tipos de saída: 

* **Conjunto de dados de resultados**: Esta saída é um resumo do texto analisado combinado com os n-gramas extraídos. As colunas que não selecionou na opção **coluna Texto** são transmitidas para a saída. Para cada coluna de texto que analisa, o módulo gera estas colunas:

  * **Matriz de ocorrências de n-gram**: O módulo gera uma coluna para cada n-grama encontrada no corpus total e adiciona uma pontuação em cada coluna para indicar o peso do n-grama para essa linha. 

* **Vocabulário de resultados**: O vocabulário contém o dicionário de n-grama real, juntamente com as pontuações de frequência do termo que são geradas como parte da análise. Pode guardar o conjunto de dados para reutilização com um conjunto diferente de entradas ou para uma atualização posterior. Também pode reutilizar o vocabulário para modelar e pontuar.

### <a name="result-vocabulary"></a>Vocabulário de resultados

O vocabulário contém o dicionário n-gram com as pontuações de frequência do termo que são geradas como parte da análise. As pontuações df e IDF são geradas independentemente de outras opções.

+ **ID**: Um identificador gerado para cada n-grama único.
+ **NGram**: O n-gram. Espaços ou outros separadores de palavras são substituídos pelo caráter de sublinhado.
+ **DF**: A pontuação de frequência de termo para o n-grama no corpus original.
+ **IDF**: A pontuação inversa da frequência do documento para o n-grama no corpus original.

Pode atualizar manualmente este conjunto de dados, mas pode introduzir erros. Por exemplo:

* Um erro é levantado se o módulo encontrar linhas duplicadas com a mesma chave no vocabulário de entrada. Certifique-se de que não há duas linhas no vocabulário que tenham a mesma palavra.
* O esquema de entrada dos conjuntos de dados do vocabulário deve coincidir exatamente, incluindo nomes de colunas e tipos de colunas. 
* A coluna **ID** e a coluna **DF** devem ser do tipo inteiro. 
* A coluna **IDF** deve ser do tipo flutuante.

> [!Note]
> Não ligue a saída de dados diretamente ao módulo Modelo de Comboio. Deve remover as colunas de texto gratuitas antes de serem introduzidas no Modelo de Comboio. Caso contrário, as colunas de texto gratuitas serão tratadas como características categóricas.

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning.
