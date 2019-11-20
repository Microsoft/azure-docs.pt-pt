---
title: 'Designer: exemplo de classificação de análises de livros'
titleSuffix: Azure Machine Learning
description: Crie um classificador de regressão logística de multiclasse para prever a categoria da empresa com o Azure Machine Learning conjunto 500 de
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: peterlu
ms.date: 11/04/2019
ms.openlocfilehash: 43545c2d3bb3afe4e1c458f14c1ba30e41eea721
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196012"
---
# <a name="build-a-classifier-to-predict-company-category-using-azure-machine-learning-designer"></a>Crie um classificador para prever a categoria da empresa usando o designer de Azure Machine Learning.

**Designer (visualização) exemplo 7**

[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku.md)]

Este exemplo demonstra como usar módulos de análise de texto para criar um pipeline de classificação de texto no designer de Azure Machine Learning (versão prévia).

A meta da classificação de texto é atribuir uma parte do texto a uma ou mais classes ou categorias predefinidas. A parte do texto pode ser um documento, artigo de notícias, consulta de pesquisa, email, tweet, tíquetes de suporte, comentários do cliente, revisão do produto do usuário, etc. Aplicativos de classificação de texto incluem a categorização de artigos de jornal e conteúdo de emails de notícias em tópicos, organização de páginas da Web em categorias hierárquicas, filtragem de e-mail de spam, análise de sentimentos, previsão de intenção de usuário de consultas de pesquisa, roteamento suporte a tíquetes e análise de comentários do cliente. 

Esse pipeline treina um **classificador de regressão logística de multiclasse** para prever a categoria da empresa com o **conjunto de conjuntos da Wikipédia SP 500 derivado da Wikipédia**.  

As etapas fundamentais de um modelo de aprendizado de máquina de treinamento com dados de texto são:

1. Obter os dados

1. Pré-processar os dados de texto

1. Engenharia de recursos

   Converta o recurso de texto no recurso numérico com o módulo de extração de recursos, como o hash de recurso, extraia o recurso n-Gram dos dados de texto.

1. Dar formação sobre o modelo

1. Conjunto de pontos de Pontuação

1. Avaliar o modelo

Aqui está o grafo final concluído do pipeline no qual vamos trabalhar. Forneceremos a lógica para todos os módulos para que você possa tomar decisões semelhantes por conta própria.

[![grafo do pipeline](./media/how-to-ui-sample-text-classification/nlp-modules-overall.png)](./media/how-to-ui-sample-text-classification/nlp-modules-overall.png#lightbox)

## <a name="data"></a>Dados

Nesse pipeline, usamos o conjunto de entrada da **Wikipédia SP 500** . O conjunto de notícias é derivado da Wikipédia (https://www.wikipedia.org/) com base em artigos de cada S & a empresa P 500. Antes de carregar no Azure Machine Learning designer, o conjunto de um foi processado da seguinte maneira:

- Extraia o conteúdo de texto para cada empresa específica
- Remover formatação wiki
- Remover caracteres não alfanuméricos
- Converter todo o texto em minúsculas
- Foram adicionadas categorias de empresa conhecidas

Não foi possível encontrar artigos para algumas empresas, portanto, o número de registros é menor que 500.

## <a name="pre-process-the-text-data"></a>Pré-processar os dados de texto

Usamos o módulo de **texto pré-processar** para pré-processar os dados de texto, incluindo detectar as sentenças, Tokenize sentenças e assim por diante. Você encontraria todas as opções com suporte no artigo de [**texto de pré-processamento**](../algorithm-module-reference/preprocess-text.md) . Após o pré-processamento dos dados de Tex, usamos o módulo **dividir dados** para dividir aleatoriamente os dados de entrada, de modo que o conjunto de dados de treinamento contenha 50% dos dados originais e o conjunto de dado de teste contenha 50% dos originais.

## <a name="feature-engineering"></a>Engenharia de recursos
Neste exemplo, usaremos dois métodos executando a engenharia de recursos.

### <a name="feature-hashing"></a>Hashing de Funcionalidade
Usamos o módulo [**hash de recurso**](../algorithm-module-reference/feature-hashing.md) para converter o texto sem formatação dos artigos em inteiros e usei os valores inteiros como recursos de entrada para o modelo. 

O módulo **hash de recurso** pode ser usado para converter documentos de texto de comprimento variável em vetores de recursos numéricos de comprimento igual, usando o método de hash de 32 bits murmurhash v3 fornecido pela biblioteca Vowpal Wabbit. O objetivo de usar o hash de recurso é a redução da dimensionalidade; o hash de recurso também torna a pesquisa de pesos de recursos mais rápida no tempo de classificação porque usa comparação de valor de hash em vez de comparação de cadeia de caracteres.

No pipeline de exemplo, definimos o número de bits de hash como 14 e definimos o número de n-grams como 2. Com essas configurações, a tabela de hash pode conter 2 ^ 14 entradas, nas quais cada recurso de hash representa um ou mais recursos de n-Gram e seu valor representa a frequência de ocorrência desse n-Gram na instância de texto. Para muitos problemas, uma tabela de hash desse tamanho é mais do que a adequada, mas em alguns casos, mais espaço pode ser necessário para evitar colisões. Avalie o desempenho da sua solução de aprendizado de máquina usando um número diferente de bits. 

### <a name="extract-n-gram-feature-from-text"></a>Extrair recurso de N-Gram de texto

Um n-Gram é uma sequência contígua de n termos de uma determinada sequência de texto. Um n-grama de tamanho 1 é chamado de unigram; um n-grama de tamanho 2 é um bigrama; um n-grama de tamanho 3 é um diagrama. N-gramas de tamanhos maiores às vezes são referenciados pelo valor de n, por exemplo, "quatro-Gram", "cinco-grama" e assim por diante.

Usamos a [**extração do recurso N-Gram do módulo de texto**](../algorithm-module-reference/extract-n-gram-features-from-text.md)como outra solução para a engenharia de recursos. Esse módulo primeiro extrai o conjunto de n-grams, além de n-grams, o número de documentos em que cada n-Gram aparece no texto é contado (DF). Neste exemplo, a métrica TF-IDF é usada para calcular valores de recursos. Em seguida, ele converte dados de texto não estruturados em vetores de recursos numéricos de comprimento igual, em que cada recurso representa o TF-IDF de um n-Gram em uma instância de texto.

Depois de converter os dados de texto em vetores de recursos numéricos, um módulo **Selecionar coluna** é usado para remover os dados de texto do DataSet. 

## <a name="train-the-model"></a>Dar formação sobre o modelo

A escolha do algoritmo geralmente depende dos requisitos do caso de uso. Como o objetivo desse pipeline é prever a categoria da empresa, um modelo classificador multiclasse é uma boa opção. Considerando que o número de recursos é grande e que esses recursos são esparsos, usamos o modelo de **regressão logística multiclasse** para esse pipeline.

## <a name="test-evaluate-and-compare"></a>Testar, avaliar e comparar

 Dividimos o conjunto de informações e usamos conjuntos de valores diferentes para treinar e testar o modelo para tornar a avaliação do modelo mais objetiva.

Depois que o modelo é treinado, usaremos o **modelo de Pontuação** e **avaliamos** os módulos de modelo para gerar resultados previstos e avaliar os modelos. No entanto, antes de usar o módulo **modelo de Pontuação** , é necessário realizar a engenharia de recursos como o que fizemos durante o treinamento. 

Para o módulo **hash de recurso** , é fácil executar engenheiro de recursos no fluxo de Pontuação como fluxo de treinamento. Use o módulo **hash de recurso** diretamente para processar os dados de texto de entrada.

Para **extrair o recurso N-Gram do módulo de texto** , conectamos a **saída do vocabulário de resultado** do fluxo de **dados** de treinamento ao vocabulário de entrada no fluxo de informações de Pontuação e definimos o parâmetro **modo de vocabulário** como **ReadOnly** .
[Grafo de ![de Pontuação de n-Gram](./media/how-to-ui-sample-text-classification/n-gram.png)](./media/how-to-ui-sample-text-classification/n-gram.png)

Depois de concluir a etapa de engenharia, o **modelo de Pontuação** poderia ser usado para gerar previsões para o conjunto de teste usando o modelo treinado. Para verificar o resultado, selecione a porta de saída do **modelo de Pontuação** e, em seguida, selecione **Visualizar**.

Em seguida, passamos as pontuações para o módulo **modelo** de avaliação para gerar métricas de avaliação. O **modelo de avaliação** tem duas portas de entrada, para que possamos avaliar e comparar conjuntos de dados pontuados que são gerados com métodos diferentes. Neste exemplo, comparamos o desempenho do resultado gerado com o método de hash de recurso e o método n-Gram.
Para verificar o resultado, selecione a porta de saída do **modelo de avaliação** e, em seguida, selecione **Visualizar**.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Explore os outros exemplos disponíveis para o designer:
- [Exemplo 1-regressão: prever o preço de um automóvel](how-to-designer-sample-regression-automobile-price-basic.md)
- [Exemplo 2-regressão: comparar algoritmos para previsão de preço de automóvel](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [Exemplo 3-classificação com seleção de recursos: Previsão de renda](how-to-designer-sample-classification-predict-income.md)
- [Exemplo 4-classificação: prever o risco de crédito (sensível ao custo)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [Exemplo 5-classificação: Previsão de rotatividade](how-to-designer-sample-classification-churn.md)
- [Exemplo 6-classificação: prever atrasos de voo](how-to-designer-sample-classification-flight-delay.md)
