---
title: 'Criar modelo Python: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o modelo de criação de modelo Python no Azure Machine Learning para criar um módulo personalizado de modelagem ou processamento de dados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ms.openlocfilehash: 33e15055958ac99f2aa9eb160f9e5cf3c5b0cd41
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73493794"
---
# <a name="create-python-model"></a>Criar Modelo Python

Este artigo descreve como usar o módulo **criar modelo Python** para criar um modelo não treinado a partir de um script Python. 

Você pode basear o modelo em qualquer aprendiz que esteja incluído em um pacote do Python no ambiente do Azure Machine Learning designer. 

Depois de criar o modelo, você pode usar [treinar modelo](train-model.md) para treinar o modelo em um conjunto de informações, como qualquer outro aprendiz no Azure Machine Learning. O modelo treinado pode ser passado para o [modelo de Pontuação](score-model.md) para usar o modelo para fazer previsões. O modelo treinado pode ser salvo e o fluxo de trabalho de Pontuação pode ser publicado como um serviço Web.

> [!WARNING]
> Atualmente, não é possível passar os resultados pontuados de um modelo Python para [avaliar o modelo](evaluate-model.md). Se você precisar avaliar um modelo, poderá escrever um script Python personalizado e executá-lo usando o módulo [Executar script Python](execute-python-script.md) .  


## <a name="how-to-configure-create-python-model"></a>Como configurar criar modelo Python

O uso deste módulo requer conhecimento intermediário ou especializado do Python. O módulo dá suporte ao uso de qualquer aprendiz que esteja incluído nos pacotes do Python já instalados no Azure Machine Learning. Consulte a lista pré-instalada de pacotes do Python em [Executar script Python](execute-python-script.md).
  

Este artigo mostrará como usar o **modelo de criação de Python** com um pipeline simples. Abaixo está o grafo do pipeline.

![criar-Python-Model](./media/module/aml-create-python-model.png)

1.  Clique em **criar modelo do Python**, edite o script para implementar seu processo de modelagem ou gerenciamento de dados. Você pode basear o modelo em qualquer aprendiz que esteja incluído em um pacote do Python no ambiente de Azure Machine Learning.


    Veja abaixo um código de exemplo de classificador Naive Bayes de duas classes usando o popular pacote *sklearn* .

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


2. Conecte o módulo **criar modelo Python** que você acabou de criar a um **modelo de treinamento** e modelo de **Pontuação**

3. Se você precisar avaliar o modelo, adicione um [script Python de execução](execute-python-script.md) e edite o script Python para implementar a avaliação.

Abaixo está o código de avaliação de exemplo.

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
