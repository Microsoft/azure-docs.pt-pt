---
title: Avaliar a equidade dos modelos ML em Python (pré-visualização)
titleSuffix: Azure Machine Learning
description: Aprenda a avaliar e a mitigar a equidade dos seus modelos de aprendizagem automática usando Fairlearn e o Azure Machine Learning Python SDK.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: mesameki
author: mesameki
ms.reviewer: luquinta
ms.date: 11/16/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, responsible-ml
ms.openlocfilehash: 70ad5d6d88dabb51e022a1fc5c011341b06f02fd
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2021
ms.locfileid: "105640690"
---
# <a name="use-azure-machine-learning-with-the-fairlearn-open-source-package-to-assess-the-fairness-of-ml-models-preview"></a>Utilize a Azure Machine Learning com o pacote fairlearn open-source para avaliar a equidade dos modelos ML (pré-visualização)

Neste guia de como fazer, você aprenderá a usar o pacote De Pitão de código aberto [fairlearn](https://fairlearn.github.io/) com Azure Machine Learning para executar as seguintes tarefas:

* Avalie a equidade das previsões do seu modelo. Para saber mais sobre justiça na aprendizagem automática, veja a [equidade no artigo de aprendizagem automática.](concept-fairness-ml.md)
* Faça upload, liste e baixe insights de avaliação de justiça de/para o estúdio Azure Machine Learning.
* Consulte um dashboard de avaliação de equidade no estúdio Azure Machine Learning para interagir com os seus conhecimentos de equidade do seu modelo.

>[!NOTE]
> A avaliação da equidade não é um exercício puramente técnico. **Este pacote pode ajudá-lo a avaliar a equidade de um modelo de machine learning, mas só você pode configurar e tomar decisões sobre o desempenho do modelo.**  Embora este pacote ajude a identificar métricas quantitativas para avaliar a equidade, os desenvolvedores de modelos de machine learning também devem realizar uma análise qualitativa para avaliar a equidade dos seus próprios modelos.

## <a name="azure-machine-learning-fairness-sdk"></a>Azure Machine Learning Fairness SDK 

O Azure Machine Learning Fairness SDK, `azureml-contrib-fairness` integra o pacote Python de código aberto, [Fairlearn,](http://fairlearn.github.io)no Azure Machine Learning. Para saber mais sobre a integração de Fairlearn no Azure Machine Learning, confira estes [cadernos de amostras.](https://github.com/Azure/MachineLearningNotebooks/tree/master/contrib/fairness) Para obter mais informações sobre Fairlearn, consulte o guia de [exemplo](https://fairlearn.org/v0.6.0/auto_examples/) e [os cadernos de amostras.](https://github.com/fairlearn/fairlearn/tree/master/notebooks) 

Utilize os seguintes comandos para instalar as `azureml-contrib-fairness` `fairlearn` embalagens e as embalagens:
```bash
pip install azureml-contrib-fairness
pip install fairlearn==0.4.6
```
Versões posteriores de Fairlearn também devem funcionar no seguinte código de exemplo.



## <a name="upload-fairness-insights-for-a-single-model"></a>Carregar insights de equidade para um único modelo

O exemplo a seguir mostra como utilizar o pacote de equidade. Vamos enviar insights de justiça de modelos para a Azure Machine Learning e ver o painel de avaliação de justiça no estúdio Azure Machine Learning.

1. Treine um modelo de amostra no Caderno Jupyter. 

    Para o conjunto de dados, usamos o conhecido conjunto de dados de recenseamento para adultos, que recolhemos do OpenML. Fingimos ter um problema de decisão de empréstimo com o rótulo indicando se um indivíduo pagou um empréstimo anterior. Vamos formar um modelo para prever se indivíduos nunca antes invisíveis vão pagar um empréstimo. Tal modelo pode ser usado na tomada de decisões de empréstimos.

    ```python
    import copy
    import numpy as np
    import pandas as pd

    from sklearn.compose import ColumnTransformer
    from sklearn.datasets import fetch_openml
    from sklearn.impute import SimpleImputer
    from sklearn.linear_model import LogisticRegression
    from sklearn.model_selection import train_test_split
    from sklearn.preprocessing import StandardScaler, OneHotEncoder
    from sklearn.compose import make_column_selector as selector
    from sklearn.pipeline import Pipeline
    
    from fairlearn.widget import FairlearnDashboard

    # Load the census dataset
    data = fetch_openml(data_id=1590, as_frame=True)
    X_raw = data.data
    y = (data.target == ">50K") * 1
    
    # (Optional) Separate the "sex" and "race" sensitive features out and drop them from the main data prior to training your model
    X_raw = data.data
    y = (data.target == ">50K") * 1
    A = X_raw[["race", "sex"]]
    X = X_raw.drop(labels=['sex', 'race'],axis = 1)
    
    # Split the data in "train" and "test" sets
    (X_train, X_test, y_train, y_test, A_train, A_test) = train_test_split(
        X_raw, y, A, test_size=0.3, random_state=12345, stratify=y
    )

    # Ensure indices are aligned between X, y and A,
    # after all the slicing and splitting of DataFrames
    # and Series
    X_train = X_train.reset_index(drop=True)
    X_test = X_test.reset_index(drop=True)
    y_train = y_train.reset_index(drop=True)
    y_test = y_test.reset_index(drop=True)
    A_train = A_train.reset_index(drop=True)
    A_test = A_test.reset_index(drop=True)

    # Define a processing pipeline. This happens after the split to avoid data leakage
    numeric_transformer = Pipeline(
        steps=[
            ("impute", SimpleImputer()),
            ("scaler", StandardScaler()),
        ]
    )
    categorical_transformer = Pipeline(
        [
            ("impute", SimpleImputer(strategy="most_frequent")),
            ("ohe", OneHotEncoder(handle_unknown="ignore")),
        ]
    )
    preprocessor = ColumnTransformer(
        transformers=[
            ("num", numeric_transformer, selector(dtype_exclude="category")),
            ("cat", categorical_transformer, selector(dtype_include="category")),
        ]
    )

    # Put an estimator onto the end of the pipeline
    lr_predictor = Pipeline(
        steps=[
            ("preprocessor", copy.deepcopy(preprocessor)),
            (
                "classifier",
                LogisticRegression(solver="liblinear", fit_intercept=True),
            ),
        ]
    )

    # Train the model on the test data
    lr_predictor.fit(X_train, y_train)

    # (Optional) View this model in Fairlearn's fairness dashboard, and see the disparities which appear:
    from fairlearn.widget import FairlearnDashboard
    FairlearnDashboard(sensitive_features=A_test, 
                       sensitive_feature_names=['Race', 'Sex'],
                       y_true=y_test,
                       y_pred={"lr_model": lr_predictor.predict(X_test)})
    ```

2. Inicie sessão no Azure Machine Learning e registe o seu modelo.
   
    O painel de justiça pode integrar-se com modelos registados ou não registados. Registe o seu modelo em Azure Machine Learning com os seguintes passos:
    ```python
    from azureml.core import Workspace, Experiment, Model
    import joblib
    import os
    
    ws = Workspace.from_config()
    ws.get_details()

    os.makedirs('models', exist_ok=True)
    
    # Function to register models into Azure Machine Learning
    def register_model(name, model):
        print("Registering ", name)
        model_path = "models/{0}.pkl".format(name)
        joblib.dump(value=model, filename=model_path)
        registered_model = Model.register(model_path=model_path,
                                        model_name=name,
                                        workspace=ws)
        print("Registered ", registered_model.id)
        return registered_model.id

    # Call the register_model function 
    lr_reg_id = register_model("fairness_logistic_regression", lr_predictor)
    ```

3. Métricas de justiça pré-computação.

    Crie um dicionário de tablier usando o pacote de `metrics` Fairlearn. O `_create_group_metric_set` método tem argumentos semelhantes ao construtor do Dashboard, exceto que as características sensíveis são passadas como um dicionário (para garantir que os nomes estão disponíveis). Devemos também especificar o tipo de previsão (classificação binária neste caso) ao chamar este método.

    ```python
    #  Create a dictionary of model(s) you want to assess for fairness 
    sf = { 'Race': A_test.race, 'Sex': A_test.sex}
    ys_pred = { lr_reg_id:lr_predictor.predict(X_test) }
    from fairlearn.metrics._group_metric_set import _create_group_metric_set

    dash_dict = _create_group_metric_set(y_true=y_test,
                                        predictions=ys_pred,
                                        sensitive_features=sf,
                                        prediction_type='binary_classification')
    ```
4. Carrequique as métricas de justiça pré-computação.
    
    Agora, pacote de importação `azureml.contrib.fairness` para realizar o upload:

    ```python
    from azureml.contrib.fairness import upload_dashboard_dictionary, download_dashboard_by_upload_id
    ```
    Crie uma Experiência, em seguida, uma Corrida, e carre fique com o painel de instrumentos:
    ```python
    exp = Experiment(ws, "Test_Fairness_Census_Demo")
    print(exp)

    run = exp.start_logging()

    # Upload the dashboard to Azure Machine Learning
    try:
        dashboard_title = "Fairness insights of Logistic Regression Classifier"
        # Set validate_model_ids parameter of upload_dashboard_dictionary to False if you have not registered your model(s)
        upload_id = upload_dashboard_dictionary(run,
                                                dash_dict,
                                                dashboard_name=dashboard_title)
        print("\nUploaded to id: {0}\n".format(upload_id))
        
        # To test the dashboard, you can download it back and ensure it contains the right information
        downloaded_dict = download_dashboard_by_upload_id(run, upload_id)
    finally:
        run.complete()
    ```
5. Verifique o painel de justiça do estúdio Azure Machine Learning

    Se completar os passos anteriores (carregar insights de justiça gerados para a Azure Machine Learning), pode ver o painel de justiça no [estúdio Azure Machine Learning](https://ml.azure.com). Este painel de instrumentos é o mesmo painel de visualização fornecido em Fairlearn, permitindo-lhe analisar as disparidades entre os subgrupos da sua característica sensível (por exemplo, masculino vs. feminino).
    Siga um destes caminhos para aceder ao painel de visualização no estúdio Azure Machine Learning:

    * **Painel de experiências (Pré-visualização)**
    1. Selecione **Experiências** no painel esquerdo para ver uma lista de experiências que executou no Azure Machine Learning.
    1. Selecione uma experiência particular para ver todas as corridas nessa experiência.
    1. Selecione uma execução e, em seguida, o **separador Fairness** para o painel de visualização de explicação.
    1. Uma vez que aterre no **separador Fairness,** clique num **id** de equidade do menu à direita.
    1. Configure o seu painel selecionando o seu atributo sensível, métrica de desempenho e métrica de interesse de equidade para aterrar na página de avaliação da equidade.
    1. Tipo de gráfico de um para outro para observar tanto os danos de **atribuição** como a qualidade dos danos **de serviço.**



    [![Repartição do Painel de Justiça](./media/how-to-machine-learning-fairness-aml/dashboard-1.png)](./media/how-to-machine-learning-fairness-aml/dashboard-1.png#lightbox)
    
    [![Justo Dashboard Qualidade de Serviço](./media/how-to-machine-learning-fairness-aml/dashboard-2.png)](./media/how-to-machine-learning-fairness-aml/dashboard-2.png#lightbox)
    * **Painel de modelos**
    1. Se registou o seu modelo original seguindo os passos anteriores, pode selecionar **Modelos** no painel esquerdo para o visualizar.
    1. Selecione um modelo e, em seguida, o **separador Fairness** para visualizar o painel de visualização de explicação.

    Para saber mais sobre o painel de visualização e o que contém, consulte o guia de [utilizador](https://fairlearn.org/v0.6.0/user_guide/assessment.html#fairlearn-dashboard)da Fairlearn.

## <a name="upload-fairness-insights-for-multiple-models"></a>Carregar insights de equidade para vários modelos

Para comparar vários modelos e ver como as suas avaliações de equidade diferem, pode passar mais do que um modelo ao painel de visualização e comparar as suas compensações de desempenho e equidade.

1. Treine os seus modelos:
    
    Agora criamos um segundo classificador, baseado num estimador de máquinas de vetores de suporte, e carregamos um dicionário de painel de justiça usando o pacote de `metrics` Fairlearn. Assumimos que o modelo previamente treinado ainda está disponível.


    ```python
    # Put an SVM predictor onto the preprocessing pipeline
    from sklearn import svm
    svm_predictor = Pipeline(
        steps=[
            ("preprocessor", copy.deepcopy(preprocessor)),
            (
                "classifier",
                svm.SVC(),
            ),
        ]
    )

    # Train your second classification model
    svm_predictor.fit(X_train, y_train)
    ```

2. Registe os seus modelos

    Em seguida, registe ambos os modelos dentro da Azure Machine Learning. Por conveniência, guarde os resultados num dicionário, que mapeia `id` o modelo registado (uma corda em `name:version` formato) ao próprio preditor:

    ```python
    model_dict = {}

    lr_reg_id = register_model("fairness_logistic_regression", lr_predictor)
    model_dict[lr_reg_id] = lr_predictor

    svm_reg_id = register_model("fairness_svm", svm_predictor)
    model_dict[svm_reg_id] = svm_predictor
    ```

3. Carregue o painel fairlearn localmente

    Antes de enviar as ideias de equidade para a Azure Machine Learning, você pode examinar estas previsões em um dashboard de Fairlearn invocado localmente. 



    ```python
    #  Generate models' predictions and load the fairness dashboard locally 
    ys_pred = {}
    for n, p in model_dict.items():
        ys_pred[n] = p.predict(X_test)

    from fairlearn.widget import FairlearnDashboard

    FairlearnDashboard(sensitive_features=A_test, 
                    sensitive_feature_names=['Race', 'Sex'],
                    y_true=y_test.tolist(),
                    y_pred=ys_pred)
    ```

3. Métricas de justiça pré-computação.

    Crie um dicionário de tablier usando o pacote de `metrics` Fairlearn.

    ```python
    sf = { 'Race': A_test.race, 'Sex': A_test.sex }

    from fairlearn.metrics._group_metric_set import _create_group_metric_set

    dash_dict = _create_group_metric_set(y_true=Y_test,
                                        predictions=ys_pred,
                                        sensitive_features=sf,
                                        prediction_type='binary_classification')
    ```
4. Carrequique as métricas de justiça pré-computação.
    
    Agora, pacote de importação `azureml.contrib.fairness` para realizar o upload:

    ```python
    from azureml.contrib.fairness import upload_dashboard_dictionary, download_dashboard_by_upload_id
    ```
    Crie uma Experiência, em seguida, uma Corrida, e carre fique com o painel de instrumentos:
    ```python
    exp = Experiment(ws, "Compare_Two_Models_Fairness_Census_Demo")
    print(exp)

    run = exp.start_logging()

    # Upload the dashboard to Azure Machine Learning
    try:
        dashboard_title = "Fairness Assessment of Logistic Regression and SVM Classifiers"
        # Set validate_model_ids parameter of upload_dashboard_dictionary to False if you have not registered your model(s)
        upload_id = upload_dashboard_dictionary(run,
                                                dash_dict,
                                                dashboard_name=dashboard_title)
        print("\nUploaded to id: {0}\n".format(upload_id))
        
        # To test the dashboard, you can download it back and ensure it contains the right information
        downloaded_dict = download_dashboard_by_upload_id(run, upload_id)
    finally:
        run.complete()
    ```


    À semelhança da secção anterior, pode seguir um dos caminhos acima descritos (via Experiments ou **Modelos)** no estúdio Azure Machine Learning para aceder ao painel de visualização e comparar os dois modelos em termos de equidade e desempenho. 


## <a name="upload-unmitigated-and-mitigated-fairness-insights"></a>Upload insípido e atenuado insights de justiça

Você pode usar os [algoritmos](https://fairlearn.org/v0.6.0/user_guide/mitigation.html)de mitigação de Fairlearn , comparar os seus modelos(s) gerados mitigados com o modelo original não atenuado, e navegar nas trocas de desempenho/justiça entre modelos comparados.

Para ver um exemplo que demonstra a utilização do algoritmo de mitigação [grid Search](https://fairlearn.org/v0.6.0/user_guide/mitigation.html#grid-search) (que cria uma coleção de modelos mitigados com diferentes compensações e trocas de desempenho) confira este caderno [de amostras.](https://github.com/Azure/MachineLearningNotebooks/blob/master/contrib/fairness/fairlearn-azureml-mitigation.ipynb) 

Carregar os conhecimentos de equidade de vários modelos num único Run permite comparar modelos no que diz respeito à equidade e desempenho. Pode clicar em qualquer um dos modelos apresentados no gráfico de comparação do modelo para ver as ideias detalhadas de equidade do modelo em particular.


[![Painel de comparação de modelo Fairlearn](./media/how-to-machine-learning-fairness-aml/multi-model-dashboard.png)](./media/how-to-machine-learning-fairness-aml/multi-model-dashboard.png#lightbox)
    

## <a name="next-steps"></a>Passos seguintes

[Saiba mais sobre a equidade do modelo](concept-fairness-ml.md)

[Confira os cadernos de amostras da Azure Machine Learning Fairness](https://github.com/Azure/MachineLearningNotebooks/tree/master/contrib/fairness)
