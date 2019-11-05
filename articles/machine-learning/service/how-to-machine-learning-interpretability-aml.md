---
title: Interpretação de modelo para execuções locais e remotas
titleSuffix: Azure Machine Learning
description: Saiba como explicar por que seu modelo faz previsões usando o SDK do Azure Machine Learning. Ele pode ser usado durante o treinamento e a inferência para entender como seu modelo determina a importância dos recursos e faz previsões.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: trbye
ms.date: 10/25/2019
ms.openlocfilehash: a2b71a10606b7cd20f06b2497515b758426833a9
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73515307"
---
# <a name="model-interpretability-for-local-and-remote-runs"></a>Interpretação de modelo para execuções locais e remotas

[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, você aprenderá a explicar por que seu modelo fez as previsões feitas com o pacote de interpretação do SDK do Azure Machine Learning Python. Você aprende as seguintes tarefas:

* Interprete modelos de aprendizado de máquina treinados localmente e em recursos de computação remota
* Armazenar explicações locais e globais no histórico de execuções do Azure
* Exibir visualizações de interpretação no [Azure Machine Learning Studio](https://ml.azure.com)
* Implantar um explicador de pontuação com seu modelo

Para saber mais sobre a interpretação de modelo, consulte o [artigo conceito](how-to-machine-learning-interpretability.md).

## <a name="local-interpretability"></a>Interpretabilidade local

O exemplo a seguir mostra como usar o pacote de interpretação localmente sem contatar os serviços do Azure. Execute `pip install azureml-interpret` para obter o pacote de interpretação.

1. Treine um modelo de exemplo em um notebook Jupyter local.

    ```python
    # load breast cancer dataset, a well-known small dataset that comes with scikit-learn
    from sklearn.datasets import load_breast_cancer
    from sklearn import svm
    from sklearn.model_selection import train_test_split
    breast_cancer_data = load_breast_cancer()
    classes = breast_cancer_data.target_names.tolist()
    
    # split data into train and test
    from sklearn.model_selection import train_test_split
    x_train, x_test, y_train, y_test = train_test_split(breast_cancer_data.data,            
                                                        breast_cancer_data.target,  
                                                        test_size=0.2,
                                                        random_state=0)
    clf = svm.SVC(gamma=0.001, C=100., probability=True)
    model = clf.fit(x_train, y_train)
    ```

2. Chamar o explicador localmente: para inicializar um objeto de explicador, você precisa passar seu modelo e alguns dados de treinamento para o construtor do explicador. Você também pode, opcionalmente, passar nomes de recursos e nomes de classe de saída (se estiver fazendo a classificação) que serão usados para tornar as explicações e visualizações mais informativas. Veja como criar uma instância de um objeto de explicador usando `TabularExplainer`, `MimicExplainer`e `PFIExplainer` localmente. `TabularExplainer` está chamando um dos três explicadores SHAP abaixo (`TreeExplainer`, `DeepExplainer`ou `KernelExplainer`) e está selecionando automaticamente o mais apropriado para seu caso de uso. No entanto, você pode chamar cada um dos seus três explicativos subjacentes diretamente.

    ```python
    from interpret.ext.blackbox import TabularExplainer

    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, 
                                 x_train, 
                                 features=breast_cancer_data.feature_names, 
                                 classes=classes)
    ```

    ou

    ```python

    from interpret.ext.blackbox import MimicExplainer
    
    # you can use one of the following four interpretable models as a global surrogate to the black box model
    
    from interpret.ext.glassbox import LGBMExplainableModel
    from interpret.ext.glassbox import LinearExplainableModel
    from interpret.ext.glassbox import SGDExplainableModel
    from interpret.ext.glassbox import DecisionTreeExplainableModel

    # "features" and "classes" fields are optional
    # augment_data is optional and if true, oversamples the initialization examples to improve surrogate model accuracy to fit original model.  Useful for high-dimensional data where the number of rows is less than the number of columns. 
    # max_num_of_augmentations is optional and defines max number of times we can increase the input data size.
    # LGBMExplainableModel can be replaced with LinearExplainableModel, SGDExplainableModel, or DecisionTreeExplainableModel
    explainer = MimicExplainer(model, 
                               x_train, 
                               LGBMExplainableModel, 
                               augment_data=True, 
                               max_num_of_augmentations=10, 
                               features=breast_cancer_data.feature_names, 
                               classes=classes)
    ```
   ou

    ```python
    from interpret.ext.blackbox import PFIExplainer 
    
    # "features" and "classes" fields are optional
    explainer = PFIExplainer(model, 
                             features=breast_cancer_data.feature_names, 
                             classes=classes)
    ```

### <a name="overall-global-feature-importance-values"></a>Valores de importância de recurso geral (global)

Obtenha os valores globais de importância do recurso.
    
```python

# you can use the training data or the test data here
global_explanation = explainer.explain_global(x_train)

# if you used the PFIExplainer in the previous step, use the next line of code instead
# global_explanation = explainer.explain_global(x_train, true_labels=y_test)

# sorted feature importance values and feature names
sorted_global_importance_values = global_explanation.get_ranked_global_values()
sorted_global_importance_names = global_explanation.get_ranked_global_names()
dict(zip(sorted_global_importance_names, sorted_global_importance_values))

# alternatively, you can print out a dictionary that holds the top K feature names and values
global_explanation.get_feature_importance_dict()
```

### <a name="instance-level-local-feature-importance-values"></a>Valores de importância do recurso em nível de instância (local)

Obter os valores de importância do recurso local: Use as seguintes chamadas de função para explicar uma instância individual ou um grupo de instâncias. Observe que o PFIExplainer não oferece suporte a explicações locais.

```python
# explain the first data point in the test set
local_explanation = explainer.explain_local(x_test[0:5])

# sorted feature importance values and feature names
sorted_local_importance_names = local_explanation.get_ranked_local_names()
sorted_local_importance_values = local_explanation.get_ranked_local_values()
```

## <a name="interpretability-for-remote-runs"></a>Interpretabilidade para execuções remotas

Este exemplo mostra como usar a classe `ExplanationClient` para habilitar a interpretação de modelo para execuções remotas. O conceito é semelhante à seção anterior, mas você usa o `ExplanationClient` na execução remota para carregar o contexto de interpretação e, em seguida, você pode baixar o contexto posteriormente em um ambiente local. Use `pip install azureml-contrib-interpret` para obter o pacote necessário.

1. Crie um script de treinamento em um bloco de anotações Jupyter local (por exemplo, train_explain. py).

    ```python
    from azureml.contrib.interpret.explanation.explanation_client import ExplanationClient
    from azureml.core.run import Run
    from interpret.ext.blackbox import TabularExplainer

    run = Run.get_context()
    client = ExplanationClient.from_run(run)

    # write code to get and split your data into train and test sets here
    # write code to train your model here 

    # explain predictions on your local machine
    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, 
                                 x_train, 
                                 features=feature_names, 
                                 classes=classes)

    # explain overall model predictions (global explanation)
    global_explanation = explainer.explain_global(x_test)
    
    # uploading global model explanation data for storage or visualization in webUX
    # the explanation can then be downloaded on any compute
    # multiple explanations can be uploaded
    client.upload_model_explanation(global_explanation, comment='global explanation: all features')
    # or you can only upload the explanation object with the top k feature info
    #client.upload_model_explanation(global_explanation, top_k=2, comment='global explanation: Only top 2 features')
    ```

1. Siga as instruções para configurar os [destinos de computação para treinamento de modelo](how-to-set-up-training-targets.md#amlcompute) para saber como configurar uma computação de Azure Machine Learning como seu destino de computação e enviar sua execução de treinamento. Você também pode ver os [notebooks de exemplo](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model/azure-integration/remote-explanation).

1. Baixe a explicação em seu notebook Jupyter local.

    ```python
    from azureml.contrib.interpret.explanation.explanation_client import ExplanationClient
    
    client = ExplanationClient.from_run(run)
    
    # get model explanation data
    explanation = client.download_model_explanation()
    # or only get the top k (e.g., 4) most important features with their importance values
    explanation = client.download_model_explanation(top_k=4)
    
    global_importance_values = explanation.get_ranked_global_values()
    global_importance_names = explanation.get_ranked_global_names()
    print('global importance values: {}'.format(global_importance_values))
    print('global importance names: {}'.format(global_importance_names))
    ```

## <a name="raw-feature-transformations"></a>Transformações de recursos brutos

Opcionalmente, você pode passar seu pipeline de transformação de recursos para o explicador (em train_explain. py) para receber explicações em termos dos recursos brutos antes da transformação (em vez de recursos de engenharia). Se você ignorar isso, o explicador fornecerá explicações em termos de recursos de engenharia.

O formato das transformações com suporte é o mesmo descrito em [sklearn-pandas](https://github.com/scikit-learn-contrib/sklearn-pandas). Em geral, há suporte para todas as transformações, desde que elas operem em uma única coluna e, portanto, sejam claramente uma para muitas. 

Explique os recursos brutos usando um `sklearn.compose.ColumnTransformer` ou uma lista de tuplas de transformador ajustada. O código a seguir usa `sklearn.compose.ColumnTransformer`. 


```python
from sklearn.compose import ColumnTransformer

numeric_transformer = Pipeline(steps=[
    ('imputer', SimpleImputer(strategy='median')),
    ('scaler', StandardScaler())])

categorical_transformer = Pipeline(steps=[
    ('imputer', SimpleImputer(strategy='constant', fill_value='missing')),
    ('onehot', OneHotEncoder(handle_unknown='ignore'))])

preprocessor = ColumnTransformer(
    transformers=[
        ('num', numeric_transformer, numeric_features),
        ('cat', categorical_transformer, categorical_features)])

# append classifier to preprocessing pipeline.
# now we have a full prediction pipeline.
clf = Pipeline(steps=[('preprocessor', preprocessor),
                      ('classifier', LogisticRegression(solver='lbfgs'))])


# clf.steps[-1][1] returns the trained classification model
# pass transformation as an input to create the explanation object
# "features" and "classes" fields are optional
tabular_explainer = TabularExplainer(clf.steps[-1][1],
                                     initialization_examples=x_train,
                                     features=dataset_feature_names,
                                     classes=dataset_classes,
                                     transformations=preprocessor)
```

Caso você queira executar o exemplo com a lista de tuplas de transformador ajustada, use o código a seguir.

```python
from sklearn.pipeline import Pipeline
from sklearn.impute import SimpleImputer
from sklearn.preprocessing import StandardScaler, OneHotEncoder
from sklearn.linear_model import LogisticRegression
from sklearn_pandas import DataFrameMapper

# assume that we have created two arrays, numerical and categorical, which holds the numerical and categorical feature names

numeric_transformations = [([f], Pipeline(steps=[('imputer', SimpleImputer(
    strategy='median')), ('scaler', StandardScaler())])) for f in numerical]

categorical_transformations = [([f], OneHotEncoder(
    handle_unknown='ignore', sparse=False)) for f in categorical]

transformations = numeric_transformations + categorical_transformations

# append model to preprocessing pipeline.
# now we have a full prediction pipeline.
clf = Pipeline(steps=[('preprocessor', DataFrameMapper(transformations)),
                      ('classifier', LogisticRegression(solver='lbfgs'))])

# clf.steps[-1][1] returns the trained classification model
# pass transformation as an input to create the explanation object
# "features" and "classes" fields are optional
tabular_explainer = TabularExplainer(clf.steps[-1][1],
                                     initialization_examples=x_train,
                                     features=dataset_feature_names,
                                     classes=dataset_classes,
                                     transformations=transformations)
```

## <a name="visualizations"></a>Visualizações

Depois de baixar as explicações no seu notebook Jupyter local, você pode usar o painel de visualização para entender e interpretar seu modelo.

### <a name="global-visualizations"></a>Visualizações globais

As seguintes plotagens fornecem uma exibição global do modelo treinado junto com suas previsões e explicações.

|Plotar|Descrição|
|----|-----------|
|Exploração de dados| Uma visão geral do conjunto de todos os valores de previsão.|
|Importância global|Mostra os principais recursos importantes do K (K configuráveis) globalmente. Esse gráfico é útil para entender o comportamento global do modelo subjacente.|
|Exploração de explicação|Demonstra como um recurso é responsável por fazer uma alteração nos valores de previsão do modelo (ou probabilidade de valores de previsão). Ele também demonstra como dois recursos interagem para afetar as previsões.|
|Importância do Resumo| Usa um recurso local assinado valores de importância em todos os pontos de dados para mostrar a distribuição do impacto que cada recurso tem sobre o valor de previsão.|

[Painel de visualização global do ![](./media/machine-learning-interpretability-explainability/global-charts.png)](./media/machine-learning-interpretability-explainability/global-charts.png#lightbox)

### <a name="local-visualizations"></a>Visualizações locais

Clique em qualquer ponto de dados individual a qualquer momento nos gráficos anteriores para carregar o gráfico de importância do recurso local para o ponto de dados especificado.

|Plotar|Descrição|
|----|-----------|
|Importância local|Mostra os principais recursos importantes do K (K configuráveis) globalmente. Esse gráfico é útil para entender o comportamento local do modelo subjacente em um ponto de dados específico.|
|Exploração de muito|Permite alterar os valores de recursos do ponto de dados selecionado e observar como essas alterações afetarão o valor de previsão.|
|Expectativa condicional individual (ICE)| Permite que você altere um valor de recurso de um valor mínimo para um valor máximo para ver como a previsão do ponto de dados é alterada quando um recurso é alterado.|

[Importância do recurso local do painel de visualização ![](./media/machine-learning-interpretability-explainability/local-charts.png)](./media/machine-learning-interpretability-explainability/local-charts.png#lightbox)


[![o recurso painel de visualização muito](./media/machine-learning-interpretability-explainability/perturbation.gif)](./media/machine-learning-interpretability-explainability/perturbation.gif#lightbox)


[Gráficos de visualização de ![visualização de ICE](./media/machine-learning-interpretability-explainability/ice-plot.png)](./media/machine-learning-interpretability-explainability/ice-plot.png#lightbox)

Observe que você precisará ter extensões de widget do painel de visualização habilitados antes do início do kernel Jupyter.

* Jupyter Notebooks

    ```shell
    jupyter nbextension install --py --sys-prefix azureml.contrib.interpret.visualize
    jupyter nbextension enable --py --sys-prefix azureml.contrib.interpret.visualize
    ```



* Jupyter Labs

    ```shell
    jupyter labextension install @jupyter-widgets/jupyterlab-manager
    jupyter labextension install microsoft-mli-widget
    ```

Para carregar o painel de visualização, use o código a seguir.

```python
from azureml.contrib.interpret.visualize import ExplanationDashboard

ExplanationDashboard(global_explanation, model, x_test)
```

### <a name="visualization-in-azure-machine-learning-studio"></a>Visualização no Azure Machine Learning Studio

Ao concluir as etapas na seção de [interpretação remota](how-to-machine-learning-interpretability-aml.md#interpretability-for-remote-runs) , você pode verificar o painel de visualização no [Azure Machine Learning Studio](https://ml.azure.com). O painel mostrado no Azure Machine Learning Studio é uma versão mais simples do painel de visualização explicada acima e dá suporte apenas às duas guias a seguir.

|Plotar|Descrição|
|----|-----------|
|Importância global|Mostra os principais recursos importantes do K (K configuráveis) globalmente. Esse gráfico é útil para entender o comportamento global do modelo subjacente.|
|Importância do Resumo| Usa um recurso local assinado valores de importância em todos os pontos de dados para mostrar a distribuição do impacto que cada recurso tem sobre o valor de previsão.|

Se as explicações global e local estiverem disponíveis, ambas as guias serão preenchidas com dados. Se apenas a explicação global estiver disponível, a segunda guia será desabilitada.

Para acessar o painel de visualização no Azure Machine Learning Studio, você pode percorrer um dos seguintes caminhos:

1. Guia experimentos (versão prévia): ao clicar na guia "experimentos", você verá uma lista de experimentos que você executou no serviço Azure Machine Learning. Nessa lista, você pode selecionar um experimento específico a ser redirecionado para uma página com todas as execuções sob o nome do experimento selecionado. Ao clicar em cada execução e na guia "explicações", você verá o painel de visualização de explicação.


[Importância do recurso local do painel de visualização ![](./media/machine-learning-interpretability-explainability/amlstudio-experiments.png)](./media/machine-learning-interpretability-explainability/amlstudio-experiments.png#lightbox)


2. Guia modelos: caso você tenha registrado seu modelo original usando as etapas em [implantar modelos com Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where), seu modelo será exibido na lista da guia "modelos". Ao clicar em cada modelo e na guia "explicações", você verá o painel de visualização de explicação.

## <a name="interpretability-at-inference-time"></a>Interpretabilidade no tempo de inferência

O explicador pode ser implantado junto com o modelo original e pode ser usado no momento da inferência para fornecer as informações de explicação local. Também oferecemos explicadores de pontuação mais leves para melhorar o desempenho da interpretação no tempo de inferência. O processo de implantação de um explicador de Pontuação de peso mais leve é semelhante à implantação de um modelo e inclui as seguintes etapas:


1. Crie um objeto de explicação (por exemplo, usando TabularExplainer):

   ```python
    from interpret.ext.blackbox import TabularExplainer


   explainer = TabularExplainer(model, 
                                initialization_examples=x_train, 
                                features=dataset_feature_names, 
                                classes=dataset_classes, 
                                transformations=transformations)
   ```

1. Crie um explicador de Pontuação usando o objeto de explicação:

   ```python
   from azureml.contrib.interpret.scoring.scoring_explainer import KernelScoringExplainer, save

   # create a lightweight explainer at scoring time
   scoring_explainer = KernelScoringExplainer(explainer)

   # pickle scoring explainer
   # pickle scoring explainer locally
   OUTPUT_DIR = 'my_directory'
   save(scoring_explainer, directory=OUTPUT_DIR, exist_ok=True)
   ```

1. Configurar e registrar uma imagem que usa o modelo de explicação de pontuação.

   ```python
   # register explainer model using the path from ScoringExplainer.save - could be done on remote compute
   # scoring_explainer.pkl is the filename on disk, while my_scoring_explainer.pkl will be the filename in cloud storage
   run.upload_file('my_scoring_explainer.pkl', os.path.join(OUTPUT_DIR, 'scoring_explainer.pkl'))
   
   scoring_explainer_model = run.register_model(model_name='my_scoring_explainer', 
                                                model_path='my_scoring_explainer.pkl')
   print(scoring_explainer_model.name, scoring_explainer_model.id, scoring_explainer_model.version, sep = '\t')
   ```

1. Adicional Recuperar o explicador de pontuação da nuvem e testar as explicações

   ```python
   from azureml.contrib.interpret.scoring.scoring_explainer import load

   # retrieve the scoring explainer model from cloud"
   scoring_explainer_model = Model(ws, 'my_scoring_explainer')
   scoring_explainer_model_path = scoring_explainer_model.download(target_dir=os.getcwd(), exist_ok=True)

   # load scoring explainer from disk
   scoring_explainer = load(scoring_explainer_model_path)

   # test scoring explainer locally
   preds = scoring_explainer.explain(x_test)
   print(preds)
   ```

1. Implantar a imagem em um destino de computação:

   1. Criar um arquivo de Pontuação (antes desta etapa, siga as etapas em [implantar modelos com Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where) para registrar seu modelo de previsão original)

        ```python
        %%writefile score.py
        import json
        import numpy as np
        import pandas as pd
        import os
        import pickle
        from sklearn.externals import joblib
        from sklearn.linear_model import LogisticRegression
        from azureml.core.model import Model

        def init():

            global original_model
            global scoring_model

            # retrieve the path to the model file using the model name
            # assume original model is named original_prediction_model
            original_model_path = Model.get_model_path('original_prediction_model')
            scoring_explainer_path = Model.get_model_path('my_scoring_explainer')

            original_model = joblib.load(original_model_path)
            scoring_explainer = joblib.load(scoring_explainer_path)

        def run(raw_data):
            # get predictions and explanations for each data point
            data = pd.read_json(raw_data)
            # make prediction
            predictions = original_model.predict(data)
            # retrieve model explanations
            local_importance_values = scoring_explainer.explain(data)
            # you can return any data type as long as it is JSON-serializable
            return {'predictions': predictions.tolist(), 'local_importance_values': local_importance_values}
        ```

   1. Defina a configuração de implantação (essa configuração depende dos requisitos do seu modelo. O exemplo a seguir define uma configuração que usa um núcleo de CPU e 1 GB de memória)

        ```python
        from azureml.core.webservice import AciWebservice

        aciconfig = AciWebservice.deploy_configuration(cpu_cores=1,
                                                       memory_gb=1,
                                                       tags={"data": "NAME_OF_THE_DATASET",
                                                             "method" : "local_explanation"},
                                                       description='Get local explanations for NAME_OF_THE_PROBLEM')
        ```

   1. Criar um arquivo com dependências de ambiente

        ```python
        from azureml.core.conda_dependencies import CondaDependencies

        # WARNING: to install this, g++ needs to be available on the Docker image and is not by default (look at the next cell)

        azureml_pip_packages = ['azureml-defaults', 'azureml-contrib-interpret', 'azureml-core', 'azureml-telemetry', 'azureml-interpret']
 

        # specify CondaDependencies obj
        myenv = CondaDependencies.create(conda_packages=['scikit-learn', 'pandas'],
                                         pip_packages=['sklearn-pandas'] + azureml_pip_packages,
                                         pin_sdk_version=False)


        with open("myenv.yml","w") as f:
            f.write(myenv.serialize_to_string())

        with open("myenv.yml","r") as f:
            print(f.read())
        ```

   1. Criar um dockerfile personalizado com o g + + instalado

        ```python
        %%writefile dockerfile
        RUN apt-get update && apt-get install -y g++
        ```

   1. Implantar a imagem criada (estimativa de tempo: 5 minutos)

        ```python
        from azureml.core.webservice import Webservice
        from azureml.core.image import ContainerImage

        # use the custom scoring, docker, and conda files we created above
        image_config = ContainerImage.image_configuration(execution_script="score.py",
                                                        docker_file="dockerfile",
                                                        runtime="python",
                                                        conda_file="myenv.yml")

        # use configs and models generated above
        service = Webservice.deploy_from_model(workspace=ws,
                                            name='model-scoring-service',
                                            deployment_config=aciconfig,
                                            models=[scoring_explainer_model, original_model],
                                            image_config=image_config)

        service.wait_for_deployment(show_output=True)
        ```

1. Testar a implementação

    ```python
    import requests

    # create data to test service with
    examples = x_list[:4]
    input_data = examples.to_json()

    headers = {'Content-Type':'application/json'}

    # send request to service
    resp = requests.post(service.scoring_uri, input_data, headers=headers)

    print("POST to url", service.scoring_uri)
    # can covert back to Python objects from json string if desired
    print("prediction:", resp.text)
    ```

1. Limpar: para excluir um serviço Web implantado, use `service.delete()`.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a interpretação de modelo, consulte o [artigo conceitual](how-to-machine-learning-interpretability.md).
