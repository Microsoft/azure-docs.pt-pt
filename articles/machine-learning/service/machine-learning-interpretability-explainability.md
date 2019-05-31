---
title: Capacidade de interpretação do modelo
titleSuffix: Azure Machine Learning service
description: Aprenda a explicar por que o seu modelo faz predições com o SDK do Azure Machine Learning. Ele pode ser usado durante a formação e inferência para compreender como o seu modelo faz predições.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: larryfr
ms.date: 05/30/2019
ms.openlocfilehash: 94309a019800b560cf6731d84cea324932e3f357
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66398534"
---
# <a name="model-interpretability-with-azure-machine-learning-service"></a>Modelo interpretability com o serviço Azure Machine Learning

Neste artigo, irá aprender a explicar por que o seu modelo de feitas as previsões com vários pacotes de interpretability do SDK de Python do Azure Machine Learning.

Usando as classes e métodos no SDK, pode obter:
+ Valores de importância para funcionalidades não processadas e engenharia de funcionalidades
+ Interpretability em conjuntos de dados do mundo real em escala, durante a formação e inferência.
+ Visualizações interativas para ajudá-lo na deteção de padrões nos dados e explicações em tempo de treinamento

Durante a fase de treinamento do ciclo de desenvolvimento, designers de modelo e avaliadores podem utilizar resultado interpretability de um modelo para verificar hipóteses e criar a confiança com os participantes.  Também utilizam as informações sobre o modelo para depuração, a validar o comportamento de modelo corresponde aos seus objetivos e verificar a existência de tendência.

No machine learning, **funcionalidades** são os campos de dados utilizados para prever um ponto de dados de destino. Por exemplo, para prever o risco de crédito, campos de dados para idade, tamanho de conta e a idade de conta podem ser usados. Neste caso, idade, tamanho de conta e a idade de conta são **funcionalidades**. Importância da funcionalidade indica como cada campo de dados afetados previsões do modelo. Por exemplo, idade pode ser muito utilizada na predição enquanto o tamanho de conta e a idade o não afetam a precisão de predição significativamente. Este processo permite que os cientistas de dados explicar previsões resultantes, para que as partes interessadas têm visibilidade sobre quais pontos de dados são mais importantes no modelo.

Com estas ferramentas, pode explicar modelos de machine learning **globalmente em todos os dados**, ou **localmente nos pontos de dados específicos** usando as tecnologias de topo de gama de forma fácil de usar e dimensionável.

As classes de interpretability são disponibilizadas por meio de vários pacotes do SDK. Saiba como [instalar pacotes do SDK do Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

* [`azureml.explain.model`](https://docs.microsoft.com/python/api/azureml-explain-model/?view=azure-ml-py), o pacote principal, que contém funcionalidades suportadas pela Microsoft.

* `azureml.contrib.explain.model`, pré-visualização, funções e funcionalidades experimentais, que pode experimentar.

* `azureml.train.automl.automlexplainer` pacote para interpretar automatizado modelos de machine learning.

> [!IMPORTANT]
> Conteúdo no `contrib` espaço de nomes não é totalmente suportado. Como as funcionalidades experimentais maduras, eles gradualmente serão movidos para o espaço de nomes principal.

## <a name="how-to-interpret-your-model"></a>Como interpretar o seu modelo

É possível aplicar as classes de interpretability e métodos para compreender o comportamento de globais do modelo ou predições específicas. O primeiro é chamado a explicação global e a última opção é chamado de explicação local.

Os métodos podem ser categorizados de também com base em se o método é independente do modelo ou modelo específico. Alguns métodos de um determinado tipo de modelos de destinam. Por exemplo, explicador de árvore do SHAP só se aplica a modelos baseados em árvore. Alguns métodos tratam o modelo como uma caixa preta, como explicador mimic ou explicador de kernel do SHAP. O `explain` pacote tira partido destas abordagens diferentes com base em conjuntos de dados, tipos de modelo e casos de utilização.

O resultado é um conjunto de informações sobre como um determinado modelo torna sua predição, tais como:
* Importância da funcionalidade de relativo global/local

* Relação de funcionalidade e previsão global/local

### <a name="explainers"></a>Explainers

Existem dois conjuntos de explainers: Explainers diretos e Explainers de Meta no SDK.

__Direcionar explainers__ provenientes de bibliotecas integradas. O SDK encapsula todas as explainers para que eles expor uma API comum e o formato de saída. Se for mais confortável diretamente com estes explainers, pode invocá-los diretamente em vez de utilizar a API comum e o formato de saída. Seguem-se uma lista dos explainers diretos disponíveis no SDK:

* **Explicador da árvore**: Explicador árvore do SHAP, que se concentra nas polynomial rápido SHAP valor estimativa algoritmo de tempo específico para árvores e conjuntos de árvores.
* **Explicador profunda**: Com base na explicação de SHAP, Explicador profunda "é um algoritmo de aproximação de alta velocidade para valores SHAP nos modelos de aprendizagem profunda que se baseia numa conexão com DeepLIFT descrito no documento SHAP NIPS. Modelos de TensorFlow e modelos de Keras utilizando o back-end do TensorFlow são suportados (há também um suporte preliminar para PyTorch) ".
* **Explicador kernel**: Explicador de Kernel do SHAP utiliza uma regressão linear de local especialmente ponderado para estimar os valores SHAP para qualquer modelo.
* **Imitar Explicador**: Explicador mimic baseia-se com o conceito de modelos de substituição global. Um modelo de substituição global é um modelo de intrinsecamente interpretable que está preparado para aproximar as previsões de um modelo de caixa preta de forma mais precisa possível. Cientista de dados pode interpretar o modelo de substitutos para tirar conclusões sobre o modelo de caixa preta.
* **Verde-LIMÃO Explicador** (`contrib`): Com base em verde-LIMÃO, verde-LIMÃO Explicador usa o algoritmo de explicações de modelo agnóstico topo de gama Local interpretable (verde-LIMÃO) para criar modelos de local de substituição. Ao contrário dos modelos de substituição global, verde-LIMÃO concentra-se na formação de modelos de local de substituição para explicar previsões individuais.
* **Explicador de texto HAN** (`contrib`): Explicador de texto HAN utiliza uma rede de atenção hierárquica para obter explicações de modelo de dados de texto para um modelo de texto determinado caixa-preta. Para preparar o modelo de substitutos HAN em saídas previstos de um modelo de professor determinado. Depois de treinamento globalmente entre o corpo de texto, adicionámos um passo de fine-tune para um documento específico para melhorar a exatidão das explicações. HAN utiliza um bidirecional RNN com duas camadas de atenção, pela atenção frase e word. Depois do DNN é preparado o modelo de professor e otimizar num documento específico, vamos pode extrair importances o word das camadas de atenção. Descobrimos HAN para ser mais precisos do que verde-LIMÃO ou SHAP para dados de texto, mas mais dispendiosos em termos de também o tempo de treinamento. No entanto, fizemos melhorias para o tempo de treinamento por dar ao usuário a opção para inicializar a rede com GloVe incorporações, embora seja lento ainda. O tempo de treinamento pode ser melhorado significativamente executando HAN numa VM remota GPU do Azure. A implementação de HAN é descrita em "Hierárquica redes de atenção para a classificação de documento (Yang et al., 2016)" ([https://www.cs.cmu.edu/~diyiy/docs/naacl16.pdf](https://www.cs.cmu.edu/~diyiy/docs/naacl16.pdf)).

__Meta explainers__ automaticamente selecione um explicador direto adequado e gerar as informações de explicação melhor, com base no modelo em questão e conjuntos de dados. Os explainers meta tirar partido de todas as bibliotecas (SHAP, verde-LIMÃO, imitam, etc.) que foi integrado ou desenvolvidos. Seguem-se a explainers meta disponíveis no SDK:

* **Explicador tabular**: Utilizado com conjuntos de dados em tabela.
* **Explicador texto**: Utilizado com conjuntos de dados de texto.

Além de meta-selecionar dos explainers diretos, meta explainers desenvolver funcionalidades adicionais sobre as bibliotecas subjacentes e melhorar a velocidade e escalabilidade com as explainers diretos.

Atualmente `TabularExplainer` emprega a seguinte lógica para invocar o Explainers direto:

1. Se for um modelo baseado em árvore, aplicar `TreeExplainer`, outra
2. Se é um modelo DNN, aplicam-se `DeepExplainer`, outra
3. Trate-o como um modelo de caixa preta e aplicar `KernelExplainer`

A inteligência incorporada ao `TabularExplainer` irá tornar-se mais sofisticados como bibliotecas adicionais estão integradas no SDK e que conhecemos prós e contras de cada explicador.

`TabularExplainer` também fez aprimoramentos significativos de funcionalidade e o desempenho ao longo do Explainers direto:

* **Resumo do conjunto de dados de inicialização**. Em casos em que a velocidade de explicação é mais importante, vamos resumir o conjunto de dados de inicialização e gerar um pequeno conjunto de exemplos representativos, que acelera a explicação global e local.
* **O conjunto de dados de avaliação de amostragem**. Se o utilizador passa um grande conjunto de exemplos de avaliação, mas, na verdade, não tem todos eles a ser avaliada, o parâmetro de amostragem pode ser definido como verdadeiro para acelerar a explicação global.

O diagrama seguinte mostra a relação entre os dois conjuntos de direct e meta explainers.

[![Arquitetura de Interpretability do Machine Learning](./media/machine-learning-interpretability-explainability/interpretability-architecture.png)](./media/machine-learning-interpretability-explainability/interpretability-architecture.png#lightbox)

### <a name="models-supported"></a>Modelos suportados

Quaisquer modelos que estão treinados em conjuntos de dados em Python `numpy.array`, `pandas.DataFrame`, `iml.datatypes.DenseData`, ou `scipy.sparse.csr_matrix` formato são suportadas pelo interpretability `explain` pacote do SDK.

As funções de explicação modelos e pipelines de aceitar como entrada. Se for fornecido um modelo, o modelo tem de implementar a função de predição `predict` ou `predict_proba` que está em conformidade com a Convenção de Scikit. Se não for fornecido um pipeline (nome do script de pipeline), a função de explicação parte do princípio de que o script em execução do pipeline retorna uma predição.

### <a name="local-and-remote-compute-target"></a>Destino de computação de locais e remotos

O `explain` pacote foi concebido para funcionar com ambos os destinos de computação de locais e remotos. Se executar localmente, funções do SDK não irão contactar todos os serviços do Azure. Pode executar a explicação remotamente na computação do Azure Machine Learning e registar as informações de explicação em serviços de histórico de execução do Azure Machine Learning. Depois desta informação seja registrada, relatórios e visualizações da explicação estão prontamente disponíveis no portal de área de trabalho do Azure Machine Learning para análise de utilizador.

## <a name="interpretability-in-training"></a>Interpretability no treinamento

### <a name="train-and-explain-locally"></a>Dar formação e explicar localmente

1. Prepare o seu modelo num bloco de notas Jupyter local.

    ```python
    # load breast cancer dataset, a well-known small dataset that comes with scikit-learn
    from sklearn.datasets import load_breast_cancer
    from sklearn import svm
    from sklearn.model_selection import train_test_split
    breast_cancer_data = load_breast_cancer()
    classes = breast_cancer_data.target_names.tolist()
    # Split data into train and test
    from sklearn.model_selection import train_test_split
    x_train, x_test, y_train, y_test = train_test_split(breast_cancer_data.data, breast_cancer_data.target, test_size=0.2, random_state=0)
    clf = svm.SVC(gamma=0.001, C=100., probability=True)
    model = clf.fit(x_train, y_train)
    ```

2. Chame o explicador: Para inicializar um objeto de explicação, precisa transmitir o seu modelo e alguns dados de treinamento ao construtor a explicação. Opcionalmente, também pode passar os nomes das funcionalidades e de saída nomes de classe (se a fazer a classificação) que serão utilizados para tornar sua explicações e visualizações mais informativo. Eis como criar uma instância de um objeto de explicador utilizando [TabularExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.tabularexplainer?view=azure-ml-py) e [MimicExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.mimic.mimicexplainer?view=azure-ml-py) localmente. `TabularExplainer` está chamando um as três explainers por baixo (`TreeExplainer`, `DeepExplainer`, ou `KernelExplainer`) e é automaticamente selecionar o mais adequado para seu caso de utilização. No entanto, pode chamar cada um dos seus três explainers subjacentes diretamente.

    ```python
    from azureml.explain.model.tabular_explainer import TabularExplainer
    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, x_train, features=breast_cancer_data.feature_names, classes=classes)
    ```

    ou

    ```python
    from azureml.explain.model.mimic.mimic_explainer import MimicExplainer
    from azureml.explain.model.mimic.models.lightgbm_model import LGBMExplainableModel

    # "features" and "classes" fields are optional
    explainer = MimicExplainer(model, x_train, LGBMExplainableModel, features=breast_cancer_data.feature_names, classes=classes)
    ```

3. Obter o recurso global valores de importância.

    ```python
    # You can use the training data or the test data here
    global_explanation = explainer.explain_global(x_train)
    # Sorted feature importance values and feature names
    sorted_global_importance_values = global_explanation.get_ranked_global_values()
    sorted_global_importance_names = global_explanation.get_ranked_global_names()
    dict(zip(sorted_global_importance_names, sorted_global_importance_values))
    ```

4. Valores de importância de funcionalidade local: Utilize as seguintes chamadas de função para explicar uma instância individual ou um grupo de instâncias.

    ```python
    # explain the first data point in the test set
    local_explanation = explainer.explain_local(x_test[0])

    # sorted feature importance values and feature names
    sorted_local_importance_names = local_explanation.get_ranked_local_names()
    sorted_local_importance_values = local_explanation.get_ranked_local_values()
    ```

    ou

    ```python
    # explain the first five data points in the test set
    local_explanation = explainer.explain_local(x_test[0:4])

    # sorted feature importance values and feature names
    sorted_local_importance_names = local_explanation.get_ranked_local_names()
    sorted_local_importance_values = local_explanation.get_ranked_local_values()
    ```

### <a name="train-and-explain-remotely"></a>Dar formação e explicar remotamente

Enquanto pode treinar a vários destinos de computação suportados pelo serviço Azure Machine Learning, o exemplo nesta secção mostra como fazê-lo com um destino de computação do Azure Machine Learning.

1. Crie um script de treinamento num bloco de notas do Jupyter local (por exemplo, run_explainer.py).

    ```python
    run = Run.get_context()
    client = ExplanationClient.from_run(run)

    # Train your model here

    # explain predictions on your local machine
    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, x_train, features=breast_cancer_data.feature_names, classes=classes)
    # explain overall model predictions (global explanation)
    global_explanation = explainer.explain_global(x_test)
    # explain local data points (individual instances)
    local_explanation = explainer.explain_local(x_test[0])
    # upload global and local explanation objects to Run History
    client.upload_model_explanation(run, local_explanation, top_k=2, comment='local explanation: top 2 features')
    # Uploading global model explanation data for storage or visualization in webUX
    # The explanation can then be downloaded on any compute
    # Multiple explanations can be uploaded
    client.upload_model_explanation(global_explanation, comment='global explanation: all features')
    # Or you can only upload the explanation object with the top k feature info
    #client.upload_model_explanation(global_explanation, top_k=2, comment='global explanation: Only top 2 features')
    ```

2. Siga as instruções [configurar destinos de computação de preparação de modelos](how-to-set-up-training-targets.md#amlcompute) para saber mais sobre como configurar uma computação do Azure Machine Learning como o destino de computação e submeter a execução de treinamento.

3. Baixe a explicação em seu bloco de notas Jupyter local.

    ```python
    from azureml.contrib.explain.model.explanation.explanation_client import ExplanationClient
    # Get model explanation data
    client = ExplanationClient.from_run(run)
    explanation = client.download_model_explanation()
    local_importance_values = explanation.local_importance_values
    expected_values = explanation.expected_values
    # Or you can use the saved run.id to retrive the feature importance values
    client = ExplanationClient.from_run_id(ws, experiment_name, run.id)
    explanation = client.download_model_explanation()
    local_importance_values = explanation.local_importance_values
    expected_values = explanation.expected_values
    # Get the top k (e.g., 4) most important features with their importance values
    explanation = client.download_model_explanation(top_k=4)
    global_importance_values = explanation.get_ranked_global_values()
    global_importance_names = explanation.get_ranked_global_names()
    print('global importance values: {}'.format(global_importance_values))
    print('global importance names: {}'.format(global_importance_names))
    ```

## <a name="visualizations"></a>Visualizações

Utilize o dashboard de visualização para compreender e interpretar o seu modelo:

### <a name="global-visualizations"></a>Visualizações global

Os gráficos seguintes fornecem uma vista global do modelo preparado, juntamente com suas previsões e explicações.

|Diagram|Descrição|
|----|-----------|
|Exploração de dados| Uma visão geral do conjunto de dados juntamente com os valores de previsão.|
|Importância global|Mostra os principais recursos importantes de K (configurável K) globalmente. Este gráfico é útil para entender o comportamento global do modelo subjacente.|
|Exploração de explicação|Demonstra como um recurso é responsável por fazer uma alteração nos valores de previsão do modelo (ou a probabilidade de valores de previsão). |
|Resumo| Utiliza um valores de importância assinado funcionalidade local em todos os pontos de dados para ver a distribuição do impacto de que cada recurso tem o valor de previsão.|

[![Dashboard de visualização Global](./media/machine-learning-interpretability-explainability/global-charts.png)](./media/machine-learning-interpretability-explainability/global-charts.png#lightbox)

### <a name="local-visualizations"></a>Visualizações locais

Pode clicar em qualquer ponto de dados individuais em qualquer altura dos gráficos anteriores para carregar o desenho de importância de funcionalidade local para o ponto de dados fornecido.

|Diagram|Descrição|
|----|-----------|
|Importância local|Mostra os principais recursos importantes de K (configurável K) globalmente. Este gráfico é útil para entender o comportamento de local do modelo subjacente num ponto de dados específico.|

[![Local de Dashboard de visualização](./media/machine-learning-interpretability-explainability/local-charts.png)](./media/machine-learning-interpretability-explainability/local-charts.png#lightbox)

Para carregar o dashboard de visualização, utilize o seguinte código:

```python
from azureml.contrib.explain.model.visualize import ExplanationDashboard

ExplanationDashboard(global_explanation, model, x_test)
```

## <a name="raw-feature-transformations"></a>Transformações de funcionalidade não processados

Opcionalmente, pode transmitir o seu pipeline de transformação de funcionalidade para explicador para receber explicações em termos de recursos não processados antes da transformação (em vez de engenharia de funcionalidades). Se ignorar isso, a explicação fornece explicações em termos de recursos de engenharia.

O formato de transformações suportadas é mesmo, tal como aquele descrito em [sklearn pandas](https://github.com/scikit-learn-contrib/sklearn-pandas). Em geral, quaisquer transformações são suportadas desde que eles operam numa única coluna e são, portanto, claramente um para muitos.

```python
from sklearn.pipeline import Pipeline
from sklearn.impute import SimpleImputer
from sklearn.preprocessing import StandardScaler, OneHotEncoder
from sklearn.linear_model import LogisticRegression
from sklearn_pandas import DataFrameMapper

# Assume that we have created two arrays, numerical and categorical, which holds the numerical and categorical feature names

numeric_transformations = [([f], Pipeline(steps=[('imputer', SimpleImputer(strategy='median')), ('scaler', StandardScaler())])) for f in numerical]

categorical_transformations = [([f], OneHotEncoder(handle_unknown='ignore', sparse=False)) for f in categorical]

transformations = numeric_transformations + categorical_transformations

# Append model to preprocessing pipeline.
# Now we have a full prediction pipeline.
clf = Pipeline(steps=[('preprocessor', DataFrameMapper(transformations)),
                    ('classifier', LogisticRegression(solver='lbfgs'))])

# clf.steps[-1][1] returns the trained classification model
# Pass transformation as an input to create the explanation object
# "features" and "classes" fields are optional
tabular_explainer = TabularExplainer(clf.steps[-1][1], initialization_examples=x_train, features=dataset_feature_names, classes=dataset_classes, transformations=transformations)
```

## <a name="interpretability-in-inference"></a>Interpretability em inferência de tipos

A explicação pode ser implementada juntamente com o modelo original e pode ser utilizada em vez de classificação para fornecer as informações do local de explicação. O processo de implantação de uma explicação da classificação é semelhante ao implementar um modelo e inclui os seguintes passos:

1. Crie um objeto de explicação:

   ```python
   from azureml.contrib.explain.model.tabular_explainer import TabularExplainer

   explainer = TabularExplainer(model, x_test)
   ```

1. Crie uma classificação explicador usando o objeto de explicação:

   ```python
   scoring_explainer = explainer.create_scoring_explainer(x_test)

   # Pickle scoring explainer
   scoring_explainer_path = scoring_explainer.save('scoring_explainer_deploy')
   ```

1. Configurar e registar uma imagem que utiliza o modelo de classificação de explicação.

   ```python
   # Register explainer model using the path from ScoringExplainer.save - could be done on remote compute
   run.upload_file('breast_cancer_scoring_explainer.pkl', scoring_explainer_path)
   model = run.register_model(model_name='breast_cancer_scoring_explainer', model_path='breast_cancer_scoring_explainer.pkl')
   print(model.name, model.id, model.version, sep = '\t')
   ```

1. [Opcional] Obter a classificação explicador da cloud e testar as explicações

   ```python
   from azureml.contrib.explain.model.scoring.scoring_explainer import ScoringExplainer

   # Retrieve the scoring explainer model from cloud"
   scoring_explainer_model = Model(ws, 'breast_cancer_scoring_explainer')
   scoring_explainer_model_path = scoring_explainer_model.download(target_dir=os.getcwd(), exist_ok=True)

   # Load scoring explainer from disk
   scoring_explainer = ScoringExplainer.load(scoring_explainer_model_path)

   # Test scoring explainer locally
   preds = scoring_explainer.explain(x_test)
   print(preds)
   ```

1. Implantar a imagem num destino de computação:

   1. Criar um ficheiro de classificação (antes deste passo, siga os passos em [implementar modelos com o serviço Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where) para registar o seu modelo de predição original)

        ```python
        %%writefile score.py
        import json
        import numpy as np
        import os
        import pickle
        from sklearn.externals import joblib
        from sklearn.linear_model import LogisticRegression
        from azureml.core.model import Model

        def init():

            global original_model
            global scoring_model

            # Retrieve the path to the model file using the model name
            # Assume original model is named original_prediction_model
            original_model_path = Model.get_model_path('original_prediction_model')
            scoring_explainer_path = Model.get_model_path('breast_cancer_scoring_explainer')

            original_model = joblib.load(original_model_path)
            scoring_explainer = joblib.load(scoring_explainer_path)

        def run(raw_data):
            # Get predictions and explanations for each data point
            data = np.array(json.loads(raw_data)['data'])
            # Make prediction
            predictions = original_model.predict(data)
            # Retrieve model explanations
            local_importance_values = scoring_explainer.explain(data)
            # You can return any data type as long as it is JSON-serializable
            return {'predictions': predictions.tolist(), 'local_importance_values': local_importance_values}
        ```

   1. Definir a configuração de implementação (esta configuração, depende dos requisitos do seu modelo. O exemplo seguinte define uma configuração que utiliza um núcleo de CPU e 1 GB de memória)

        ```python
        from azureml.core.webservice import AciWebservice

        aciconfig = AciWebservice.deploy_configuration(cpu_cores=1,
                                                       memory_gb=1,
                                                       tags={"data": "breastcancer",
                                                             "method" : "local_explanation"},
                                                       description='Get local explanations for breast cancer data')
        ```

   1. Crie um ficheiro com dependências de ambiente

        ```python
        from azureml.core.conda_dependencies import CondaDependencies

        # WARNING: to install this, g++ needs to be available on the Docker image and is not by default (look at the next cell)


        myenv = CondaDependencies.create(pip_packages=["azureml-defaults", "azureml-explain-model", "azureml-contrib-explain-model"],
                                        conda_packages=["scikit-learn"])

        with open("myenv.yml","w") as f:
            f.write(myenv.serialize_to_string())

        with open("myenv.yml","r") as f:
            print(f.read())
        ```

   1. Criar um dockerfile personalizado com g + + instalado

        ```python
        %%writefile dockerfile
        RUN apt-get update && apt-get install -y g++
        ```

   1. Implementar a imagem criada (estimativa de tempo: 5 minutos)

        ```python
        from azureml.core.webservice import Webservice
        from azureml.core.image import ContainerImage

        # Use the custom scoring, docker, and conda files we created above
        image_config = ContainerImage.image_configuration(execution_script="score.py",
                                                        docker_file="dockerfile",
                                                        runtime="python",
                                                        conda_file="myenv.yml")

        # Use configs and models generated above
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

    # Create data to test service with
    x_list = x_test.tolist()
    examples = x_list[:4]
    input_data = "{\"data\": " + str(examples) + "}"

    headers = {'Content-Type':'application/json'}

    # send request to service
    resp = requests.post(service.scoring_uri, input_data, headers=headers)

    print("POST to url", service.scoring_uri)
    # can covert back to Python objects from json string if desired
    print("prediction:", resp.text)
    ```

1. Limpe: Para eliminar um serviço web implementado, utilize `service.delete()`.

## <a name="interpretability-in-automated-ml"></a>Interpretability no ML automatizada

Aprendizagem automática contém pacotes para interpretar a importância de funcionalidade em modelos treinados para automática. Além disso, os cenários de classificação permitem-lhe obter a importância de recurso de nível de classe. Existem dois métodos para habilitar esse comportamento na aprendizagem automática:

* Para ativar a importância de funcionalidade para um modelo treinado ensemble, utilize o [ `explain_model()` ](https://docs.microsoft.com/en-us/python/api/azureml-train-automl/azureml.train.automl.automlexplainer?view=azure-ml-py) função.

    ```python
    from azureml.train.automl.automlexplainer import explain_model

    shap_values, expected_values, overall_summary, overall_imp, \
        per_class_summary, per_class_imp = explain_model(fitted_model, X_train, X_test)
    ```

* Para ativar a importância de funcionalidade para cada execução individual antes da formação, defina o `model_explainability` parâmetro `True` no `AutoMLConfig` objeto, juntamente com a fornecer os dados de validação. Em seguida, utilize o [ `retrieve_model_explanation()` ](https://docs.microsoft.com/en-us/python/api/azureml-train-automl/azureml.train.automl.automlexplainer?view=azure-ml-py) função.

    ```python
    from azureml.train.automl.automlexplainer import retrieve_model_explanation

    shap_values, expected_values, overall_summary, overall_imp, per_class_summary, \
        per_class_imp = retrieve_model_explanation(best_run)
    ```

Para obter mais informações, consulte a [procedimentos](how-to-configure-auto-train.md#explain-the-model-interpretability) sobre como ativar funcionalidades de interpretability na aprendizagem automática.

## <a name="next-steps"></a>Passos Seguintes

Para ver uma coleção de blocos de notas do Jupyter que demonstram as instruções acima, consulte a [blocos de notas do Azure Machine Learning Interpretability exemplo](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model).
