---
title: 'Criar modelo Python: referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a utilizar o módulo Create Python Model em Azure Machine Learning para criar um módulo de modelação ou processamento de dados personalizado.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 06/18/2020
ms.openlocfilehash: 03f80ebeb7b30d8c0fe14060335541d77b7523b0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90898631"
---
# <a name="create-python-model-module"></a>Criar módulo modelo Python

Este artigo descreve um módulo no designer de Aprendizagem automática Azure.

Aprenda a usar o módulo Create Python Model para criar um modelo destreinado a partir de um script Python. Você pode basear o modelo em qualquer aprendiz que esteja incluído em um pacote Python no ambiente de designer de machine learning Azure. 

Depois de criar o modelo, pode utilizar o [Train Model](train-model.md) para treinar o modelo num conjunto de dados, como qualquer outro aprendiz em Azure Machine Learning. O modelo treinado pode ser passado para [o Score Model](score-model.md) para fazer previsões. Em seguida, pode guardar o modelo treinado e publicar o fluxo de trabalho de pontuação como um serviço web.

> [!WARNING]
> Atualmente, não é possível ligar este módulo ao módulo **Tune Model Hyperparameters** ou passar os resultados pontuados de um modelo Python para [avaliar o modelo.](evaluate-model.md) Se precisar de sintonizar os hiperparímetros ou avaliar um modelo, pode escrever um script Python personalizado utilizando o módulo [Execut Python Script.](execute-python-script.md)


## <a name="configure-the-module"></a>Configure o módulo

A utilização deste módulo requer conhecimento intermédio ou especializado da Python. O módulo suporta o uso de qualquer aluno incluído nos pacotes Python já instalados no Azure Machine Learning. Consulte a lista de pacotes python pré-instalados no [Executo Python Script](execute-python-script.md).

> [!NOTE]
> Tenha muito cuidado ao escrever o seu script e certifique-se de que não existe erro de sintaxe, como a utilização de um objeto não declarado ou de um módulo não importado.

> [!NOTE]
> Também prestem mais atenção à lista de módulos pré-instalados no [Executo Python Script](execute-python-script.md). Apenas importar módulos pré-instalados. Por favor, não instale pacotes extras como "pip install xgboost" neste script, caso contrário serão levantados erros ao ler modelos em módulos de corrente.
  
Este artigo mostra como usar **o Modelo Create Python** com um simples oleoduto. Aqui está um diagrama do oleoduto:

![Diagrama de Criar Modelo Python](./media/module/create-python-model.png)

1. Selecione **Create Python Model**e edite o script para implementar o seu processo de modelação ou gestão de dados. Você pode basear o modelo em qualquer aprendiz que esteja incluído em um pacote Python no ambiente Azure Machine Learning.

> [!NOTE]
> Por favor, preste mais atenção aos comentários no código de amostra do script e certifique-se de que o seu script segue rigorosamente o requisito, incluindo o nome de classe, métodos e assinatura de método. A violação levará a exceções. 

   O seguinte código de amostra do classificador Naive Bayes de duas classes utiliza o popular pacote *de sklearn:*

   ```Python

   # The script MUST define a class named AzureMLModel.
   # This class MUST at least define the following three methods:
       # __init__: in which self.model must be assigned,
       # train: which trains self.model, the two input arguments must be pandas DataFrame,
       # predict: which generates prediction result, the input argument and the prediction result MUST be pandas DataFrame.
   # The signatures (method names and argument names) of all these methods MUST be exactly the same as the following example.

   # Please do not install extra packages such as "pip install xgboost" in this script,
   # otherwise errors will be raised when reading models in down-stream modules.
   
   import pandas as pd
   from sklearn.naive_bayes import GaussianNB


   class AzureMLModel:
       def __init__(self):
           self.model = GaussianNB()
           self.feature_column_names = list()

       def train(self, df_train, df_label):
           # self.feature_column_names records the column names used for training.
           # It is recommended to set this attribute before training so that the
           # feature columns used in predict and train methods have the same names.
           self.feature_column_names = df_train.columns.tolist()
           self.model.fit(df_train, df_label)

       def predict(self, df):
           # The feature columns used for prediction MUST have the same names as the ones for training.
           # The name of score column ("Scored Labels" in this case) MUST be different from any other columns in input data.
           return pd.DataFrame(
               {'Scored Labels': self.model.predict(df[self.feature_column_names]), 
                'probabilities': self.model.predict_proba(df[self.feature_column_names])[:, 1]}
           )


   ```

2. Ligue o módulo **Modelo Create Python** que acaba de criar para Model **e** Modelo **de Pontuação.**

3. Se precisar de avaliar o modelo, adicione um módulo [de Script Execute Python](execute-python-script.md) e edite o script Python.

   O seguinte guião é o código de avaliação da amostra:

   ```Python


   # The script MUST contain a function named azureml_main
   # which is the entry point for this module.

   # imports up here can be used to 
   import pandas as pd

   # The entry point function MUST have two input arguments:
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

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 