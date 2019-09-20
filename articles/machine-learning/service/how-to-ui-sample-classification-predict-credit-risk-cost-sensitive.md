---
title: 'Exemplo de interface visual #4: Classificação para prever o risco de crédito (sensível ao custo)'
titleSuffix: Azure Machine Learning
description: Este artigo mostra como criar um experimento de aprendizado de máquina complexo usando a interface visual. Você aprenderá a implementar scripts Python personalizados e a comparar vários modelos para escolher a melhor opção.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: c06da0fd325f6b79bc0e14c4e6a246497f86a900
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/19/2019
ms.locfileid: "71131912"
---
# <a name="sample-4---classification-predict-credit-risk-cost-sensitive"></a>Amostra 4-classificação: Prever o risco de crédito (sensível ao custo)

Este artigo mostra como criar um experimento de aprendizado de máquina complexo usando a interface visual. Você aprenderá a implementar a lógica personalizada usando scripts Python e a comparar vários modelos para escolher a melhor opção.

Este exemplo treina um classificador para prever o risco de crédito usando informações de aplicativo de crédito, como histórico de crédito, idade e número de cartões de crédito. No entanto, você pode aplicar os conceitos neste artigo para lidar com seus próprios problemas de Machine Learning.

Se você estiver apenas começando a usar o Machine Learning, poderá dar uma olhada no [exemplo de classificador básico](how-to-ui-sample-classification-predict-credit-risk-basic.md) primeiro.

Este é o grafo concluído para este experimento:

[![Grafo do experimento](media/how-to-ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png)](media/how-to-ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Selecione o botão **abrir** para o experimento de exemplo 4:

    ![Abrir o experimento](media/how-to-ui-sample-classification-predict-credit-risk-cost-sensitive/open-sample4.png)

## <a name="data"></a>Data

Este exemplo usa o conjunto de teste de cartão de crédito alemão do repositório Irvine de UC. Esse conjunto de conteúdo contém 1.000 amostras com 20 recursos e 1 rótulo. Cada amostra representa uma pessoa. Os 20 recursos incluem recursos numéricos e categóricos. Para obter mais informações sobre o conjunto de dados, consulte o [site do UCI](https://archive.ics.uci.edu/ml/datasets/Statlog+%28German+Credit+Data%29). A última coluna é o rótulo, que denota o risco de crédito e tem apenas dois valores possíveis: alto risco de crédito = 2 e baixo risco de crédito = 1.

## <a name="experiment-summary"></a>Resumo do experimento

Neste experimento, você compara duas abordagens diferentes para a geração de modelos para resolver esse problema:

- Treinamento com o DataSet original.
- Treinamento com um conjunto de uma réplica.

Com ambas as abordagens, você avalia os modelos usando o conjunto de testes com replicação para garantir que os resultados sejam alinhados com a função de custo. Teste dois classificadores com ambas as abordagens: **Máquina de vetor de suporte de duas classes** e **árvore de decisão aumentada de duas classes**.

O custo de classificar incorretamente um exemplo de baixo risco como alto é 1, e o custo de classificar incorretamente um exemplo de alto risco como baixo é 5. Usamos um módulo **Executar script Python** para considerar esse custo de classificação ineficiente.

Este é o grafo do experimento:

[![Grafo do experimento](media/how-to-ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png)](media/how-to-ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="data-processing"></a>Processamento de dados

Comece usando o módulo **Editor de metadados** para adicionar nomes de coluna para substituir os nomes de coluna padrão por nomes mais significativos, obtidos da descrição do conjunto de informações no site UCI. Forneça os novos nomes de coluna como valores separados por vírgula no campo nome da **nova coluna** do **Editor de metadados**.

Em seguida, gere os conjuntos de treinamento e teste usados para desenvolver o modelo de previsão de risco. Divida o conjunto de dados original em conjuntos de treinamento e teste do mesmo tamanho usando o módulo **dividir data** . Para criar conjuntos de tamanho igual, defina a **fração de linhas na primeira** opção de conjunto de resultados de saída como 0,5.

### <a name="generate-the-new-dataset"></a>Gerar o novo conjunto de um

Como o custo de subestimar o risco é alto, defina o custo da classificação indevido como esta:

- Para casos de alto risco, classificado incorretamente como baixo risco: 5
- Para casos de baixo risco, classificados incorretamente como alto risco: 1

Para refletir essa função de custo, gere um novo conjunto de um. No novo conjunto de um, cada exemplo de alto risco é replicado cinco vezes, mas o número de exemplos de baixo risco não é alterado. Divida os dados em conjuntos de dados de treinamento e teste antes da replicação para impedir que a mesma linha seja em ambos os conjuntos.

Para replicar os dados de alto risco, coloque este código Python em um módulo **Executar script Python** :

```Python
import pandas as pd

def azureml_main(dataframe1 = None, dataframe2 = None):

    df_label_1 = dataframe1[dataframe1.iloc[:, 20] == 1]
    df_label_2 = dataframe1[dataframe1.iloc[:, 20] == 2]

    result = df_label_1.append([df_label_2] * 5, ignore_index=True)
    return result,
```

O módulo **Executar script Python** replica os conjuntos de testes de treinamento e de teste.

### <a name="feature-engineering"></a>Com engenharia

O algoritmo de **máquina de vetor de suporte de duas classes** requer dados normalizados. Portanto, use o módulo **normalizar dados** para normalizar os intervalos de todos os `tanh` recursos numéricos com uma transformação. Uma `tanh` transformação converte todos os recursos numéricos em valores dentro de um intervalo de 0 e 1, preservando a distribuição geral dos valores.

O módulo de **máquina de vetor de suporte de duas classes** manipula recursos de cadeia de caracteres, convertendo-os para recursos categóricos e, em seguida, para recursos binários com um valor de 0 ou 1. Portanto, você não precisa normalizar esses recursos.

## <a name="models"></a>Modelos

Como você aplicou dois classificadores, o SVM ( **computador de vetor de suporte de duas classes** ) e **a árvore de decisão aumentada de duas classes**e dois conjuntos de valores, você gera um total de quatro modelos:

- SVM treinado com dados originais.
- SVM treinado com dados replicados.
- Árvore de decisão aumentada treinada com dados originais.
- Árvore de decisão aumentada treinada com dados replicados.

Este exemplo usa o fluxo de trabalho de ciência de dados padrão para criar, treinar e testar os modelos:

1. Inicialize os algoritmos de aprendizado, usando o **computador de vetor de suporte de duas classes** e **a árvore de decisão aumentada de duas classes**.
1. Use o **modelo de treinamento** para aplicar o algoritmo aos dados e criar o modelo real.
1. Use o **modelo de Pontuação** para produzir pontuações usando os exemplos de teste.

O diagrama a seguir mostra uma parte desse experimento, na qual os conjuntos de treinamento originais e replicados são usados para treinar dois modelos de SVM diferentes. O **modelo Train** está conectado ao conjunto de treinamento e o **modelo de Pontuação** está conectado ao conjunto de teste.

![Grafo de experimento](media/how-to-ui-sample-classification-predict-credit-risk-cost-sensitive/score-part.png)

No estágio de avaliação do experimento, você computa a precisão de cada um dos quatro modelos. Para este experimento, use o **modelo de avaliação** para comparar exemplos com o mesmo custo de classificação inexistente.

O módulo **modelo de avaliação** pode calcular as métricas de desempenho para até dois modelos pontuados. Portanto, você pode usar uma instância do **modelo Evaluate** para avaliar os dois modelos SVM e outra instância do **modelo Evaluate** para avaliar os dois modelos de árvore de decisão impulsionados.

Observe que o conjunto de dados de teste replicado é usado como a entrada para o **modelo de Pontuação**. Em outras palavras, as pontuações de precisão final incluem o custo para obter os rótulos incorretos.

## <a name="combine-multiple-results"></a>Combinar vários resultados

O módulo **modelo de avaliação** produz uma tabela com uma única linha que contém várias métricas. Para criar um único conjunto de resultados de precisão, primeiro usamos **adicionar linhas** para combinar os resultados em uma única tabela. Em seguida, usamos o seguinte script Python no módulo **Executar script Python** para adicionar o nome do modelo e a abordagem de treinamento para cada linha na tabela de resultados:

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

Para exibir os resultados do experimento, você pode clicar com o botão direito do mouse na saída de visualização do último módulo **selecionar colunas no conjunto de DataSet** .

![Visualizar saída](media/how-to-ui-sample-classification-predict-credit-risk-cost-sensitive/result.png)

A primeira coluna lista o algoritmo de aprendizado de máquina usado para gerar o modelo.
A segunda coluna indica o tipo do conjunto de treinamento.
A terceira coluna contém o valor de precisão sensível ao custo.

A partir desses resultados, você pode ver que a melhor precisão é fornecida pelo modelo que foi criado com o **computador de vetor de suporte de duas classes** e treinado no conjunto de resultados de treinamento replicado.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Explore os outros exemplos disponíveis para a interface visual:

- [Amostra 1-regressão: Prever o preço de um automóvel](how-to-ui-sample-regression-predict-automobile-price-basic.md)
- [Exemplo 2-regressão: Comparar algoritmos para previsão de preço de automóvel](how-to-ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [Exemplo 3-classificação: Prever risco de crédito](how-to-ui-sample-classification-predict-credit-risk-basic.md)
- [Exemplo 5-classificação: Previsão de rotatividade](how-to-ui-sample-classification-predict-churn.md)
- [Exemplo 6-classificação: Prever atrasos de voo](how-to-ui-sample-classification-predict-flight-delay.md)
