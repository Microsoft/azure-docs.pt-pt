---
title: 'Crie modelo de Python: Referência do módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como utilizar o modelo de criar o modelo de Python no serviço Azure Machine Learning para criar o módulo de modelagem ou processamento de dados personalizado.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ROBOTS: NOINDEX
ms.openlocfilehash: ea9b50cede3e2d264ca0476b6a987ca6896c3c79
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029749"
---
# <a name="create-python-model"></a>Criar modelo de Python

Este artigo descreve como utilizar o **criar modelo de Python** módulo para criar um modelo de destreinado a partir de um script de Python. 

Pode basear o modelo de qualquer aprendiz que está incluído num pacote de Python no ambiente do Azure Machine Learning. 

Depois de criar o modelo, pode usar [modelo de formação](train-model.md) para preparar o modelo num conjunto de dados, como qualquer outro aprendiz no Azure Machine Learning. O modelo treinado pode ser transmitido para [modelo de pontuação](score-model.md) para usar o modelo para fazer previsões. O modelo preparado, em seguida, pode ser guardado e o fluxo de trabalho de classificação pode ser publicado como um serviço web.

> [!WARNING]
> Atualmente não é possível passar os resultados com a pontuação de um modelo de Python para [Evaluate Model](evaluate-model.md). Se precisar de avaliar um modelo, pode escrever Python personalizado do script e executá-la com o [executar Script do Python](execute-python-script.md) módulo.  


## <a name="how-to-configure-create-python-model"></a>Como configurar a criar o modelo de Python

Utilize este módulo requer conhecimento intermediário ou especialistas do Python. O módulo suporta a utilização de qualquer aprendiz que está incluído nos pacotes de Python já instalados no Azure Machine Learning. Consulte a lista de pacotes de Python previamente instalada no [executar Script do Python](execute-python-script.md).
  

Este artigo mostra como utilizar o **criar modelo de Python** com uma experiência simples. Segue-se o gráfico da experimentação.

![create-python-model](./media/module/aml-create-python-model.png)

1.  Clique em **criar modelo de Python**, edite o script para implementar a sua modelação ou processar a gestão de dados. Pode basear o modelo de qualquer aprendiz que está incluído num pacote de Python no ambiente do Azure Machine Learning.


    Segue-se um código de exemplo do classificador de classificação bayesiana Ingênua de duas classes ao utilizar o popular *sklearn* pacote.

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


2. Ligar o **criar modelo de Python** módulo que acabou de criar para uma **Train Model** e **Pontuar modelo**

3. Se precisar de avaliar o modelo, adicione uma [executar Script do Python](execute-python-script.md) e edite o script de Python para implementar a avaliação.

Segue-se código de avaliação de exemplo.

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
