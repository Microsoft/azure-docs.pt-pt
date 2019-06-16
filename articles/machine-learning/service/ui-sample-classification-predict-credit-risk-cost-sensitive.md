---
title: 'Classificação: Prever o risco de crédito (custo confidencial)'
titleSuffix: Azure Machine Learning service
description: Este artigo mostra-lhe como criar uma complexa experimentação do machine learning utilizando a interface visual. Ficará a saber como implementar scripts de Python personalizados e comparar vários modelos para escolher a melhor opção.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: article
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: d714756c19b94eafc40cc0dbeffbc07704e8f94e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65787822"
---
# <a name="sample-4---classification-predict-credit-risk-cost-sensitive"></a>Exemplo 4 - classificação: Prever o risco de crédito (custo confidencial)

Este artigo mostra-lhe como criar uma complexa experimentação do machine learning utilizando a interface visual. Ficará a saber como implementar a lógica personalizada com scripts do Python e comparar vários modelos para escolher a melhor opção.

Este exemplo prepara um classificador para prever o risco de crédito com informações de aplicação de crédito como o histórico de crédito, a idade e o número de cartões de crédito. No entanto, pode aplicar os conceitos neste artigo para lidar com a sua própria machine learning problemas.

Se estiver apenas a começar com o machine learning, pode dar uma olhada a [exemplo de classificador básica](ui-sample-classification-predict-credit-risk-basic.md) primeiro.

Este é o gráfico concluído para esta fase experimental:

[![Gráfico da experimentação](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png)](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Selecione o **aberto** botão para a experimentação de exemplo 4:

    ![Abra a experimentação](media/ui-sample-classification-predict-credit-risk-cost-sensitive/open-sample4.png)

## <a name="data"></a>Dados

Usamos o conjunto de dados do cartão de crédito alemão do repositório de UC Irvine. Este conjunto de dados contém 1.000 exemplos com 20 recursos e 1 etiqueta. Cada exemplo representa uma pessoa. Os recursos de 20 incluem recursos numéricos e categóricos. Consulte a [Web site UCI](https://archive.ics.uci.edu/ml/datasets/Statlog+%28German+Credit+Data%29) para obter mais informações sobre o conjunto de dados. A última coluna é a etiqueta que indica o risco de crédito e tem apenas dois valores possíveis: risco elevado crédito = 2 e o risco de crédito baixa = 1.

## <a name="experiment-summary"></a>Resumo de experimentação

Nesse experimento, podemos comparar duas abordagens diferentes para gerar modelos para resolver este problema:

- Treinamento com o conjunto de dados original.
- Treinamento com um conjunto de dados replicado.

Com as duas abordagens, iremos avaliar os modelos utilizando o conjunto de dados de teste com a replicação para se certificar de que os resultados estão alinhados com a função de custo. Podemos testar dois classificadores com ambas as abordagens: **Máquina de Vetor com suporte de classe dois** e **árvore de decisões elevada de duas classes**.

O custo de misclassifying um exemplo de baixo risco tão elevado é 1 e o custo de misclassifying um exemplo de alto risco como baixa é 5. Utilizamos uma **executar Script do Python** módulo para levar em conta esta misclassification de custos.

Este é o gráfico da experimentação:

[![Gráfico da experimentação](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png)](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="data-processing"></a>Processamento de dados

Vamos iniciar com o **Editor de metadados** módulo para adicionar nomes de coluna para substituir os nomes de coluna padrão com nomes mais significativos, obtido a partir da descrição do conjunto de dados do site de UCI. Disponibilizamos os novos nomes de coluna como valores separados por vírgulas a **nova coluna** campo de nome da **metadados Editor**.

Em seguida, vamos gerar o treinamento e teste conjuntos usados para desenvolver o modelo de previsão do risco. Vamos dividir o conjunto de dados original em conjuntos de formação e teste do mesmo tamanho ao utilizar o **Split Data** módulo. Para criar conjuntos de tamanho igual, definimos o **fração de linhas no primeiro conjunto de dados de saída** opção de 0,5.

### <a name="generate-the-new-dataset"></a>Gerar novo conjunto de dados

Como o custo de subestimando o risco é alto, definimos o custo de misclassification como este:

- Para casos de alto risco classificadas erroneamente como baixo risco: 5
- Para casos de baixo risco classificadas erroneamente como alto risco: 1

Para refletir esta função de custo, podemos gerar um novo conjunto de dados. O novo conjunto de dados, cada exemplo de alto risco é replicado cinco vezes, mas não altera o número de exemplos de baixo risco. Vamos dividir os dados em conjuntos de dados de treinamento e teste antes da replicação para impedir que a mesma linha estejam em ambos os conjuntos.

Para replicar os dados de alto risco, colocamos esse código de Python numa **executar Script do Python** módulo:

```Python
import pandas as pd

def azureml_main(dataframe1 = None, dataframe2 = None):

    df_label_1 = dataframe1[dataframe1.iloc[:, 20] == 1]
    df_label_2 = dataframe1[dataframe1.iloc[:, 20] == 2]

    result = df_label_1.append([df_label_2] * 5, ignore_index=True)
    return result,
```

O **executar Script do Python** módulo replica os conjuntos de dados de formação e teste.

### <a name="feature-engineering"></a>Com engenharia

O **máquina de Vetor com suporte a duas classes** algoritmo necessita de dados normalizados. Pelo que vamos utilizar o **normalizar dados** módulo normalizar os intervalos de todas as funcionalidades numérico com uma `tanh` transformação. A `tanh` transformação converte todas as funcionalidades numérico para valores dentro de um intervalo de 0 e 1, preservando a distribuição global de valores.

O **máquina de Vetor com suporte a duas classes** módulo lida com recursos de cadeia de caracteres, convertê-los para recursos categóricos e, em seguida, a binários recursos com um valor de 0 ou 1. Portanto, não precisamos normalizar esses recursos.

## <a name="models"></a>Modelos

Uma vez que Aplicamos classificadores de dois, **máquina de Vetor com suporte a duas classes** (SVM) e **árvore de decisões elevada de duas classes**e também utilizar dois conjuntos de dados, podemos gerar um total de quatro modelos:

- SVM treinados com dados originais.
- SVM treinado com os dados replicados.
- Árvore de decisões elevada treinados com dados originais.
- Árvore de decisões elevada treinados com os dados replicados.

Usamos o fluxo de trabalho experimental padrão para criar, dar formação e testar os modelos:

1. Inicializar os algoritmos de aprendizagem, usando **máquina de Vetor com suporte a duas classes** e **árvore de decisões elevada de duas classes**.
1. Uso **Train Model** para aplicar o algoritmo aos dados e criar o modelo real.
1. Uso **modelo de pontuação** para produzir as pontuações ao utilizar os exemplos de teste.

O diagrama seguinte mostra uma parte desta experiência, no qual os conjuntos de formação original e replicadas são utilizados para criar dois modelos SVM diferentes. **Preparar modelo** está ligado ao conjunto de preparação, e **Score Model** está ligado ao conjunto de teste.

![Gráfico de experimentação](media/ui-sample-classification-predict-credit-risk-cost-sensitive/score-part.png)

Na fase de avaliação da experimentação, estamos a precisão de cada um dos quatro modelos de computação. Nesta experimentação, utilizamos **Evaluate Model** comparar os exemplos que têm o mesmo misclassification de custos.

O **Evaluate Model** módulo poderá computar as métricas de desempenho de até dois modelos com a pontuação. Pelo que vamos utilizar uma instância do **Evaluate Model** para avaliar os dois modelos SVM e outra instância do **Evaluate Model** para avaliar os dois modelos de árvore de decisões elevada.

Tenha em atenção que o conjunto de dados de teste replicada é utilizado como entrada para o **modelo de pontuação**. Em outras palavras, as pontuações de precisão final incluem o custo para obter as etiquetas errado.

## <a name="combine-multiple-results"></a>Combinar vários resultados

O **Evaluate Model** módulo produz uma tabela com uma única linha que contém várias métricas. Para criar um único conjunto de resultados de precisão, primeiro, usamos **adicionar linhas** para combinar os resultados numa única tabela. Em seguida, usamos o seguinte script de Python no **executar Script do Python** módulo para adicionar o nome do modelo e a abordagem de treinamento para cada linha na tabela de resultados:

```Python
import pandas as pd

def azureml_main(dataframe1 = None, dataframe2 = None):

    new_cols = pd.DataFrame(
            columns=["Algorithm","Training"],
            data=[
                ["SVM", "weighted"],
                ["SVM", "unweighted"],
                ["Boosted Decision Tree","weighted"],
                ["Boosted Decision Tree","unweighted"]
            ])

    result = pd.concat([new_cols, dataframe1], axis=1)
    return result,
```

## <a name="results"></a>Resultados

Para ver os resultados da experimentação, pode com o botão direito a saída de visualizar da última **Select Columns in Dataset** módulo.

![Visualizar a saída](media/ui-sample-classification-predict-credit-risk-cost-sensitive/result.png)

A primeira coluna indica a algoritmo utilizado para gerar o modelo de aprendizagem automática.
A segunda coluna indica o tipo de conjunto de treinamento.
A terceira coluna contém o valor de precisão de sensível a custos.

Esses resultados, pode ver que a maior precisão é fornecida pelo modelo que foi criado com **máquina de Vetor com suporte a duas classes** e preparado no conjunto de dados replicados de treinamento.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Passos Seguintes

Explore os outros exemplos disponíveis para a interface visual:

- [Exemplo 1 - regressão: Prever o preço de um automóvel](ui-sample-regression-predict-automobile-price-basic.md)
- [Exemplo 2 - regressão: Compare os algoritmos de previsão de preços de automóveis](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [Exemplo 3 - classificação: Prever o risco de crédito](ui-sample-classification-predict-credit-risk-basic.md)
- [Exemplo 5 - classificação: Prever o volume de alterações](ui-sample-classification-predict-churn.md)
