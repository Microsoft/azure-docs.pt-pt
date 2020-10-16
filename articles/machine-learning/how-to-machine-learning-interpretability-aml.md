---
title: Interprete & explicar os modelos ML em Python (pré-visualização)
titleSuffix: Azure Machine Learning
description: Aprenda a obter explicações sobre como o seu modelo de aprendizagem automática determina a importância da funcionalidade e faz previsões ao utilizar o Azure Machine Learning SDK.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: mithigpe
author: minthigpen
ms.reviewer: Luis.Quintanilla
ms.date: 07/09/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 08981ad21c15b6fc375e2e0733564c40d54932ba
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91708259"
---
# <a name="use-the-interpretability-package-to-explain-ml-models--predictions-in-python-preview"></a>Utilize o pacote de interpretação para explicar os modelos ML & previsões em Python (pré-visualização)



Neste guia de como orientar, aprende-se a utilizar o pacote de interpretação do Azure Machine Learning Python SDK para executar as seguintes tarefas:


* Explique todo o comportamento do modelo ou previsões individuais na sua máquina pessoal localmente.

* Ativar técnicas de interpretação para funcionalidades de engenharia.

* Explique o comportamento de todo o modelo e previsões individuais em Azure.

* Utilize um painel de visualização para interagir com as explicações do seu modelo.

* Coloque um explicador de pontuação ao lado do seu modelo para observar explicações durante a inferencção.



Para obter mais informações sobre as técnicas de interpretação suportadas e modelos de machine learning, consulte [a interpretação do Modelo em Azure Machine Learning](how-to-machine-learning-interpretability.md) e amostra de [cadernos.](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model)

## <a name="generate-feature-importance-value-on-your-personal-machine"></a>Gere um valor de importância de recurso na sua máquina pessoal 
O exemplo a seguir mostra como utilizar o pacote de interpretação na sua máquina pessoal sem contactar os serviços da Azure.

1. Instale o pacote `azureml-interpret`.
    ```bash
    pip install azureml-interpret
    ```

2. Treine um modelo de amostra num caderno jupyter local.

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

3. Ligue para o explicador localmente.
   * Para inicializar um objeto explicador, passe o seu modelo e alguns dados de treino ao construtor do explicador.
   * Para tornar as suas explicações e visualizações mais informativas, pode optar por passar em nomes de funcionalidades e nomes de classe de saída se fizer a classificação.

   Os blocos de código que se seguem mostram como instantaneamente um objeto explicador com `TabularExplainer` `MimicExplainer` , e `PFIExplainer` localmente.
   * `TabularExplainer` chama um dos três explicadores SHAP por baixo ( `TreeExplainer` `DeepExplainer` , , ou `KernelExplainer` .
   * `TabularExplainer` seleciona automaticamente o mais adequado para o seu caso de utilização, mas pode ligar diretamente para cada um dos seus três explicadores subjacentes.

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

### <a name="explain-the-entire-model-behavior-global-explanation"></a>Explique todo o comportamento do modelo (explicação global) 

Consulte o exemplo seguinte para ajudá-lo a obter os valores de importância de característica agregada (global).

```python

# you can use the training data or the test data here, but test data would allow you to use Explanation Exploration
global_explanation = explainer.explain_global(x_test)

# if you used the PFIExplainer in the previous step, use the next line of code instead
# global_explanation = explainer.explain_global(x_train, true_labels=y_train)

# sorted feature importance values and feature names
sorted_global_importance_values = global_explanation.get_ranked_global_values()
sorted_global_importance_names = global_explanation.get_ranked_global_names()
dict(zip(sorted_global_importance_names, sorted_global_importance_values))

# alternatively, you can print out a dictionary that holds the top K feature names and values
global_explanation.get_feature_importance_dict()
```

### <a name="explain-an-individual-prediction-local-explanation"></a>Explicar uma previsão individual (explicação local)
Obtenha os valores de importância de característica individual de diferentes pontos de dados, chamando explicações para uma instância individual ou um grupo de instâncias.
> [!NOTE]
> `PFIExplainer` não apoia explicações locais.

```python
# get explanation for the first data point in the test set
local_explanation = explainer.explain_local(x_test[0:5])

# sorted feature importance values and feature names
sorted_local_importance_names = local_explanation.get_ranked_local_names()
sorted_local_importance_values = local_explanation.get_ranked_local_values()
```

### <a name="raw-feature-transformations"></a>Transformações de recursos brutos

Pode optar por obter explicações em termos de funcionalidades cruas e não não transformadas em vez de funcionalidades concebidas. Para esta opção, passe o pipeline de transformação de recurso para o explicador em `train_explain.py` . Caso contrário, o explicador fornece explicações em termos de funcionalidades de engenharia.

O formato das transformações apoiadas é o mesmo descrito em [sklearn-pandas.](https://github.com/scikit-learn-contrib/sklearn-pandas) Em geral, quaisquer transformações são apoiadas desde que operem numa única coluna, de modo que fique claro que são de um para muitos.

Obtenha uma explicação para as características cruas utilizando uma `sklearn.compose.ColumnTransformer` ou com uma lista de tuples de transformador equipados. O exemplo a seguir `sklearn.compose.ColumnTransformer` utiliza.

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

Caso pretenda executar o exemplo com a lista de tuples de transformador equipados, utilize o seguinte código:

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

## <a name="generate-feature-importance-values-via-remote-runs"></a>Gere valores de importância de recurso através de execuções remotas

O exemplo a seguir mostra como pode utilizar a `ExplanationClient` classe para permitir a interpretação do modelo para execuções remotas. É conceptualmente semelhante ao processo local, exceto você:

* Utilize o `ExplanationClient` na execução remota para carregar o contexto de interpretação.
* Descarregue o contexto mais tarde num ambiente local.

1. Instale o pacote `azureml-interpret`.
    ```bash
    pip install azureml-interpret
    ```
1. Crie um roteiro de treino num caderno jupyter local. Por exemplo, `train_explain.py`.

    ```python
    from azureml.interpret import ExplanationClient
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

1. Crie um Azure Machine Learning Compute como seu alvo de computação e submeta o seu treino. Consulte [Criar e gerir os clusters de cálculo Azure Machine Learning](how-to-create-attach-compute-cluster.md) para obter instruções. Também pode achar útil o [exemplo de cadernos.](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model/azure-integration/remote-explanation)

1. Baixe a explicação no seu caderno Jupyter local.

    ```python
    from azureml.interpret import ExplanationClient
    
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


## <a name="visualizations"></a>Visualizações

Depois de descarregar as explicações no seu caderno Jupyter local, pode utilizar o painel de visualização para compreender e interpretar o seu modelo.

### <a name="understand-entire-model-behavior-global-explanation"></a>Compreender todo o comportamento do modelo (explicação global) 

Os seguintes enredos proporcionam uma visão geral do modelo treinado juntamente com as suas previsões e explicações.

|Lote|Descrição|
|----|-----------|
|Exploração de Dados| Apresenta uma visão geral do conjunto de dados juntamente com os valores de previsão.|
|Importância Global|Os agregados apresentam valores importantes de pontos de dados individuais para mostrar as características importantes do modelo no topo do K (configurável K). Ajuda a compreender o comportamento geral do modelo subjacente.|
|Exploração de Explicações|Demonstra como uma característica afeta uma mudança nos valores de previsão do modelo, ou a probabilidade de valores de previsão. Mostra o impacto da interação da funcionalidade.|
|Importância sumária|Utiliza valores de importância de característica individual em todos os pontos de dados para mostrar a distribuição do impacto de cada recurso no valor de previsão. Utilizando este diagrama, investiga-se em que direção os valores da característica afetam os valores de previsão.
|

[![Painel de Visualização Global](./media/how-to-machine-learning-interpretability-aml/global-charts.png)](./media/how-to-machine-learning-interpretability-aml/global-charts.png#lightbox)

### <a name="understand-individual-predictions-local-explanation"></a>Compreender previsões individuais (explicação local) 

Pode carregar o enredo de importância de recurso individual para qualquer ponto de dados clicando em qualquer um dos pontos de dados individuais em qualquer um dos enredos globais.

|Lote|Descrição|
|----|-----------|
|Importância Local|Mostra as características importantes k (configurável K) para uma previsão individual. Ajuda a ilustrar o comportamento local do modelo subjacente num ponto de dados específico.|
|Exploração de Perturbação (e se análise)|Permite alterações aos valores de característica do ponto de dados selecionados e observa alterações resultantes ao valor de previsão.|
|Expectativa Condicional Individual (ICE)| Permite alterações de valor de recurso de um valor mínimo para um valor máximo. Ajuda a ilustrar como a previsão do ponto de dados muda quando uma funcionalidade muda.|

[![Importância da característica local do painel de visualização](./media/how-to-machine-learning-interpretability-aml/local-charts.png)](./media/how-to-machine-learning-interpretability-aml/local-charts.png#lightbox)


[![Perturbação do painel de visualização](./media/how-to-machine-learning-interpretability-aml/perturbation.gif)](./media/how-to-machine-learning-interpretability-aml/perturbation.gif#lightbox)


[![Parcelas de GELO do Painel de Visualização](./media/how-to-machine-learning-interpretability-aml/ice-plot.png)](./media/how-to-machine-learning-interpretability-aml/ice-plot.png#lightbox)

Para carregar o painel de visualização, utilize o seguinte código.

```python
from interpret_community.widget import ExplanationDashboard

ExplanationDashboard(global_explanation, model, datasetX=x_test)
```

### <a name="visualization-in-azure-machine-learning-studio"></a>Visualização no estúdio Azure Machine Learning

Se completar os passos [de interpretação remota](how-to-machine-learning-interpretability-aml.md#generate-feature-importance-values-via-remote-runs) (carregar a explicação gerada para a Azure Machine Learning Run History), pode ver o painel de visualização no estúdio [Azure Machine Learning](https://ml.azure.com). Este dashboard é uma versão mais simples do painel de visualização explicado acima (a exploração de explicações e os enredos ICE são desativados, uma vez que não existe um computação ativo em estúdio que possa executar os seus cálculos em tempo real).

Se o conjunto de dados, as explicações globais e locais estiverem disponíveis, os dados povoam todos os separadores (exceto Exploração de Perturbação e ICE). Se apenas houver uma explicação global disponível, o separador De Importância Sumária e todos os separadores de explicação locais estão desativados.

Siga um destes caminhos para aceder ao painel de visualização no estúdio Azure Machine Learning:

* **Painel de experiências** (Pré-visualização)
  1. Selecione **Experiências** no painel esquerdo para ver uma lista de experiências que executou no Azure Machine Learning.
  1. Selecione uma experiência particular para ver todas as corridas nessa experiência.
  1. Selecione uma execução e, em seguida, o **separador Explicações** para o painel de visualização de explicações.

   [![Visualização Dashboard Característica Local Importância no estúdio AzureML em experiências](./media/how-to-machine-learning-interpretability-aml/amlstudio-experiments.png)](./media/how-to-machine-learning-interpretability-aml/amlstudio-experiments.png#lightbox)

* **Painel de modelos**
  1. Se registou o seu modelo original seguindo os passos nos [modelos Implementar com Azure Machine Learning,](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where)pode selecionar **Modelos** no painel esquerdo para o visualizar.
  1. Selecione um modelo e, em seguida, o **separador Explicações** para visualizar o painel de visualização de explicação.

## <a name="interpretability-at-inference-time"></a>Interpretação no tempo da inferência

Pode implantar o explicador juntamente com o modelo original e usá-lo no momento da inferência para fornecer os valores de importância de característica individual (explicação local) para qualquer novo ponto de dados. Também oferecemos explicadores de pontuação mais leves para melhorar o desempenho da interpretação no tempo de inferência. O processo de implantação de um explicador de pontuação mais leve é semelhante à implantação de um modelo e inclui os seguintes passos:

1. Criar um objeto de explicação. Por exemplo, pode `TabularExplainer` utilizar:

   ```python
    from interpret.ext.blackbox import TabularExplainer


   explainer = TabularExplainer(model, 
                                initialization_examples=x_train, 
                                features=dataset_feature_names, 
                                classes=dataset_classes, 
                                transformations=transformations)
   ```

1. Crie um explicador de pontuação com o objeto de explicação.

   ```python
   from azureml.interpret.scoring.scoring_explainer import KernelScoringExplainer, save

   # create a lightweight explainer at scoring time
   scoring_explainer = KernelScoringExplainer(explainer)

   # pickle scoring explainer
   # pickle scoring explainer locally
   OUTPUT_DIR = 'my_directory'
   save(scoring_explainer, directory=OUTPUT_DIR, exist_ok=True)
   ```

1. Configure e registe uma imagem que utilize o modelo de explicação de pontuação.

   ```python
   # register explainer model using the path from ScoringExplainer.save - could be done on remote compute
   # scoring_explainer.pkl is the filename on disk, while my_scoring_explainer.pkl will be the filename in cloud storage
   run.upload_file('my_scoring_explainer.pkl', os.path.join(OUTPUT_DIR, 'scoring_explainer.pkl'))
   
   scoring_explainer_model = run.register_model(model_name='my_scoring_explainer', 
                                                model_path='my_scoring_explainer.pkl')
   print(scoring_explainer_model.name, scoring_explainer_model.id, scoring_explainer_model.version, sep = '\t')
   ```

1. Como um passo opcional, pode recuperar o explicador de pontuação da nuvem e testar as explicações.

   ```python
   from azureml.interpret.scoring.scoring_explainer import load

   # retrieve the scoring explainer model from cloud"
   scoring_explainer_model = Model(ws, 'my_scoring_explainer')
   scoring_explainer_model_path = scoring_explainer_model.download(target_dir=os.getcwd(), exist_ok=True)

   # load scoring explainer from disk
   scoring_explainer = load(scoring_explainer_model_path)

   # test scoring explainer locally
   preds = scoring_explainer.explain(x_test)
   print(preds)
   ```

1. Implementar a imagem num alvo de computação, seguindo estes passos:

   1. Se necessário, registe o seu modelo de previsão original seguindo os passos nos [modelos Deploy com Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where).

   1. Crie um ficheiro de pontuação.

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
   1. Defina a configuração da implementação.

         Esta configuração depende dos requisitos do seu modelo. O exemplo a seguir define uma configuração que utiliza um núcleo de CPU e um GB de memória.

         ```python
         from azureml.core.webservice import AciWebservice

          aciconfig = AciWebservice.deploy_configuration(cpu_cores=1,
                                                    memory_gb=1,
                                                    tags={"data": "NAME_OF_THE_DATASET",
                                                          "method" : "local_explanation"},
                                                    description='Get local explanations for NAME_OF_THE_PROBLEM')
         ```

   1. Crie um ficheiro com dependências ambientais.

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

   1. Crie um estivador personalizado com g++ instalado.

         ```python
         %%writefile dockerfile
         RUN apt-get update && apt-get install -y g++
         ```

   1. Implemente a imagem criada.
   
         Este processo leva aproximadamente cinco minutos.

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

1. Teste a colocação.

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

1. Limpe-se.

   Para eliminar um serviço web implantado, utilize `service.delete()` .

## <a name="next-steps"></a>Passos seguintes

[Saiba mais sobre a interpretação do modelo](how-to-machine-learning-interpretability.md)

[Confira os cadernos de amostra de interpretação de aprendizagem de máquinas Azure](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model)

