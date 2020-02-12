---
title: 'Designer: classificar exemplo de críticas de livros'
titleSuffix: Azure Machine Learning
description: Construa um classificador de regressão logística multiclasse para prever a categoria da empresa com o conjunto de dados Wikipedia SP 500 usando o designer de Machine Learning Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.date: 02/11/2020
ms.openlocfilehash: f15f50e372d0bfe58018b16ebfa5d5d85644ae1a
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77137792"
---
# <a name="build-a-classifier-to-predict-company-category-using-azure-machine-learning-designer"></a>Construa um classificador para prever a categoria da empresa usando o designer de Machine Learning Azure.

**Amostra de designer (pré-visualização) 7**

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Esta amostra demonstra como usar módulos de análise de texto para construir um pipeline de classificação de texto em Azure Machine Learning designer (pré-visualização).

O objetivo da classificação de texto é atribuir um pedaço de texto a uma ou mais classes ou categorias predefinidas. O texto pode ser um documento, artigo de notícias, consulta de pesquisa, e-mail, tweet, bilhetes de apoio, feedback do cliente, revisão do produto do utilizador, etc. As aplicações de classificação de texto incluem categorizar artigos de jornais e conteúdos de fios de notícias em tópicos, organizar páginas web em categorias hierárquicas, filtrar e-mails de spam, análise de sentimentos, prever a intenção do utilizador de consultas de pesquisa, encaminhamento bilhetes de apoio e análise do feedback do cliente. 

Este gasoduto treina um **classificador de regressão logística multiclasse** para prever a categoria da empresa com o conjunto de **dados Wikipedia SP 500 derivado da Wikipédia.**  

Os passos fundamentais de um modelo de aprendizagem automática de formação com dados de texto são:

1. Obter os dados

1. Pré-processar os dados de texto

1. Engenharia de Recursos

   Converta a função de texto na funcionalidade numérica com módulo de extração de funcionalidades, como o hashing de características, extrair a função n-grama dos dados de texto.

1. Dar formação sobre o modelo

1. Marcar conjunto de dados

1. Avaliar o modelo

Aqui está o gráfico final e completo do oleoduto em que vamos trabalhar. Vamos fornecer a razão para todos os módulos para que possa tomar decisões semelhantes por conta própria.

[gráfico ![do oleoduto](./media/how-to-designer-sample-text-classification/nlp-modules-overall.png)](./media/how-to-designer-sample-text-classification/nlp-modules-overall.png#lightbox)

## <a name="data"></a>Dados

Neste pipeline, utilizamos o conjunto de dados **Wikipedia SP 500.** O conjunto de dados é derivado da Wikipédia (https://www.wikipedia.org/) com base em artigos de cada empresa S&P 500. Antes de enviar para o designer de Machine Learning Azure, o conjunto de dados foi processado da seguinte forma:

- Extrair conteúdo de texto para cada empresa específica
- Remover a formatação wiki
- Remover caracteres não alfanuméricos
- Converter todo o texto em minúscula
- Foram adicionadas categorias de empresas conhecidas

Os artigos não puderam ser encontrados para algumas empresas, pelo que o número de registos é inferior a 500.

## <a name="pre-process-the-text-data"></a>Pré-processar os dados de texto

Utilizamos o módulo **de texto pré-processado** para pré-processar os dados de texto, incluindo detetar as frases, tokenizar frases e assim por diante. Você encontraria todas as opções suportadas no artigo [**texto pré-processo.** ](algorithm-module-reference/preprocess-text.md) Após o pré-processamento dos dados de texto, utilizamos o módulo **Split Data** para dividir aleatoriamente os dados de entrada de modo a que o conjunto de dados de formação contenha 50% dos dados originais e o conjunto de dados de teste contém 50% dos dados originais.

## <a name="feature-engineering"></a>Engenharia de Recursos
Nesta amostra, utilizaremos dois métodos de realização de engenharia de recursos.

### <a name="feature-hashing"></a>Hashing de Funcionalidade
Usamos o módulo [**Feature Hashing**](algorithm-module-reference/feature-hashing.md) para converter o texto simples dos artigos em inteiros e usamos os valores inteiros como características de entrada para o modelo. 

O módulo **de hashing feature** pode ser usado para converter documentos de texto de comprimento variável em vetores de características numéricas de comprimento igual, utilizando o método de hashing murmurhash v3 de 32 bits fornecido pela biblioteca Vowpal Wabbit. O objetivo da utilização do hashing de recurso é a redução da dimensionalidade; também o hashing de características torna a procura de pesos de recurso mais rápido no tempo de classificação porque usa a comparação de valor hash em vez de comparação de cordas.

No gasoduto da amostra, definimos o número de pedaços de hashing para 14 e definimos o número de n-gramas para 2. Com estas definições, a tabela de hash pode conter 2^14 entradas, nas quais cada característica de hashing representa uma ou mais funcionalidades n-grama e o seu valor representa a frequência de ocorrência desse n-grama na instância de texto. Para muitos problemas, uma mesa de haxixe deste tamanho é mais do que adequada, mas em alguns casos, mais espaço pode ser necessário para evitar colisões. Avalie o desempenho da sua solução de aprendizagem automática utilizando um número diferente de bits. 

### <a name="extract-n-gram-feature-from-text"></a>Extrair recurso N-Gram do Texto

Um n-grama é uma sequência contígua de n termos a partir de uma determinada sequência de texto. Um n-grama de tamanho 1 é referido como um unigrama; um n-grama de tamanho 2 é um bigram; um n-grama de tamanho 3 é um trigrama. Os n-gramas de tamanhos maiores são por vezes referidos pelo valor de n, por exemplo, "quatro gramas", "cinco gramas", e assim por diante.

Usamos a [**função Extract N-Gram do**](algorithm-module-reference/extract-n-gram-features-from-text.md) módulo Text como outra solução para a engenharia de recursos. Este módulo extrai primeiro o conjunto de n-gramas, para além dos n-gramas, o número de documentos onde cada n-grama aparece no texto é contado (DF). Nesta amostra, a métrica TF-IDF é utilizada para calcular os valores da característica. Em seguida, converte dados de texto não estruturados em vetores de características numéricas de comprimento igual onde cada característica representa o TF-IDF de um n-grama em uma instância de texto.

Depois de converter dados de texto em vetores de características numéricas, um módulo De **Coluna Select** é usado para remover os dados de texto do conjunto de dados. 

## <a name="train-the-model"></a>Dar formação sobre o modelo

A sua escolha de algoritmo depende frequentemente dos requisitos do caso de utilização. Como o objetivo deste oleoduto é prever a categoria de empresa, um modelo de classificação multi-classe é uma boa escolha. Tendo em conta que o número de funcionalidades é grande e estas características são escassas, utilizamos o modelo **multiclasse de regressão logística** para este pipeline.

## <a name="test-evaluate-and-compare"></a>Testar, avaliar e comparar

 Dividimos o conjunto de dados e usamos diferentes conjuntos de dados para treinar e testar o modelo para tornar a avaliação do modelo mais objetiva.

Depois de o modelo ser treinado, usaríamos os módulos **'Modelo de Pontuação'** e Avaliamos os módulos de Modelo de **Pontuação** para gerar resultados previstos e avaliar os modelos. No entanto, antes de utilizar o módulo **Score Model,** é necessário executar a engenharia de funcionalidades como o que fizemos durante o treino. 

Para o módulo **De Hashing feature,** é fácil executar o engenheiro de recursos no fluxo de pontuação como fluxo de treino. Utilize o módulo **de hashing de funcionalidade** diretamente para processar os dados de texto de entrada.

Para **extrair funcionalidade N-Gram do** módulo texto, ligaríamos a saída do Vocabulário de **Resultado** saca do fluxo de dados de treino ao Vocabulário de **Entrada** no fluxo de dados de pontuação, e definiria o parâmetro do **modo Vocabulário** para **ReadOnly**.
[Gráfico ![da pontuação n-grama](./media/how-to-designer-sample-text-classification/n-gram.png)](./media/how-to-designer-sample-text-classification/n-gram.png)

Depois de terminar o passo de engenharia, o **Score Model** poderia ser usado para gerar previsões para o conjunto de dados de teste utilizando o modelo treinado. Para verificar o resultado, selecione a porta de saída do **Modelo de Pontuação** e, em seguida, selecione **Visualize**.

Em seguida, passamos as pontuações para o módulo **'Avaliar Modelo'** para gerar métricas de avaliação. **Avaliar o Model** tem duas portas de entrada, para que possamos avaliar e comparar conjuntos de dados pontuados que são gerados com diferentes métodos. Nesta amostra, comparamos o desempenho do resultado gerado com o método de hashing de características e o método n-gram.
Para verificar o resultado, selecione a porta de saída do **Modelo de Avaliação** e, em seguida, selecione **Visualize**.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Explore as outras amostras disponíveis para o designer:
- [Amostra 1 - Regressão: Prever o preço de um automóvel](how-to-designer-sample-regression-automobile-price-basic.md)
- [Amostra 2 - Regressão: Comparar algoritmos para previsão de preços do automóvel](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [Amostra 3 - Classificação com seleção de características: Previsão do Rendimento](how-to-designer-sample-classification-predict-income.md)
- [Amostra 4 - Classificação: Prever o risco de crédito (sensível ao custo)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [Amostra 5 - Classificação: Prever churn](how-to-designer-sample-classification-churn.md)
- [Amostra 6 - Classificação: Prever atrasos nos voos](how-to-designer-sample-classification-flight-delay.md)
