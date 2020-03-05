---
title: 'Criar modelo Python: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a utilizar o modelo Create Python em Azure Machine Learning para criar modelação personalizada ou módulo de processamento de dados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/19/2019
ms.openlocfilehash: 26bcd855dbe2ea85df9fb6aca74f8b52e8ce8e46
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2020
ms.locfileid: "78268900"
---
# <a name="create-python-model"></a>Criar Modelo Python

Este artigo descreve um módulo em Azure Machine Learning designer (pré-visualização).

Aprenda a usar o módulo **Create Python Model** para criar um modelo destreinado a partir de um script Python. Você pode basear o modelo em qualquer aprendiz que esteja incluído num pacote Python no ambiente de designer de machine learning Azure. 

Depois de criar o modelo, pode utilizar o [Modelo de Comboio](train-model.md) para treinar o modelo num conjunto de dados, como qualquer outro aprendiz em Azure Machine Learning. O modelo treinado pode ser passado para [o Score Model](score-model.md) para usar o modelo para fazer previsões. O modelo treinado pode então ser salvo, e o fluxo de trabalho de pontuação pode ser publicado como um serviço web.

> [!WARNING]
> Atualmente não é possível passar os resultados pontuados de um modelo Python para [avaliar o modelo](evaluate-model.md). Se precisar de avaliar um modelo, pode escrever script Python personalizado e executá-lo utilizando o módulo [Execute Python Script.](execute-python-script.md)  


## <a name="how-to-configure-create-python-model"></a>Como configurar o Modelo De Criação python

A utilização deste módulo requer conhecimentos intermédios ou especializados da Python. O módulo suporta a utilização de qualquer aluno que esteja incluído nos pacotes Python já instalados no Azure Machine Learning. Consulte a lista de pacotes Python pré-instalado no [Script Execute Python](execute-python-script.md).
  

Este artigo mostrará como usar o **Modelo Create Python** com um simples oleoduto. Abaixo está o gráfico do oleoduto.

![criar-python-modelo](./media/module/create-python-model.png)

1.  Clique em **Criar Modelo Python,** editar o script para implementar o seu processo de modelação ou gestão de dados. Pode basear o modelo em qualquer aluno que esteja incluído num pacote Python no ambiente Azure Machine Learning.


    Abaixo está um código de amostra de duas classes Naive Bayes classificando usando o popular pacote *sklearn.*

```Python

# The script MUST define a class named AzureMLModel.
# This class MUST at least define the following three methods:
    # __init__: in which self.model must be assigned,
    # train: which trains self.model, the two input arguments must be pandas DataFrame,
    # predict: which generates prediction result, the input argument and the prediction result MUST be pandas DataFrame.
# The signatures (method names and argument names) of all these methods MUST be exactly the same as the following example.


import pandas as pd
from sklearn.naive_bayes import GaussianNB


class AzureMLModel:
    def __init__(self):
        self.model = GaussianNB()
        self.feature_column_names = list()

    def train(self, df_train, df_label):
        self.feature_column_names = df_train.columns.tolist()
        self.model.fit(df_train, df_label)

    def predict(self, df):
        return pd.DataFrame(
            {'Scored Labels': self.model.predict(df[self.feature_column_names]), 
             'probabilities': self.model.predict_proba(df[self.feature_column_names])[:, 1]}
        )


```


2. Ligue o módulo **Create Python Model** que acabou de criar a um Modelo de **Comboio** e Modelo de **Pontuação**

3. Se precisar de avaliar o modelo, adicione um [Script De Python execute](execute-python-script.md) e edite o script Python para implementar a avaliação.

Abaixo está o código de avaliação da amostra.

```Python


# The script MUST contain a function named azureml_main
# which is the entry point for this module.

# imports up here can be used to 
import pandas as pd

# The entry point function can contain up to two input arguments:
#   Param<dataframe1>: a pandas.DataFrame
#   Param<dataframe2>: a pandas.DataFrame
def azureml_main(dataframe1 = None, dataframe2 = None):
    
    from sklearn.metrics import accuracy_score, precision_score, recall_score, roc_auc_score, roc_curve
    import pandas as pd
    import numpy as np
    
    scores = dataframe1.ix[:, ("income", "Scored Labels", "probabilities")]
    ytrue = np.array([0 if val == '<=50K' else 1 for val in scores["income"]])
    ypred = np.array([0 if val == '<=50K' else 1 for val in scores["Scored Labels"]])    
    probabilities = scores["probabilities"]
    
    accuracy, precision, recall, auc = \
    accuracy_score(ytrue, ypred),\
    precision_score(ytrue, ypred),\
    recall_score(ytrue, ypred),\
    roc_auc_score(ytrue, probabilities)
    
    metrics = pd.DataFrame();
    metrics["Metric"] = ["Accuracy", "Precision", "Recall", "AUC"];
    metrics["Value"] = [accuracy, precision, recall, auc]
    
    return metrics,

```
