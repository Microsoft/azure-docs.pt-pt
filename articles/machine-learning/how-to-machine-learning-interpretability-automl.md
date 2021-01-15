---
title: Explicabilidade em ML automatizado (pré-visualização)
titleSuffix: Azure Machine Learning
description: Saiba como obter explicações sobre como o seu modelo automatizado de ML determina a importância do recurso e faz previsões ao utilizar o Azure Machine Learning SDK.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, automl, responsible-ml
ms.author: mithigpe
author: minthigpen
ms.date: 07/09/2020
ms.openlocfilehash: 19cebefd64f5b6dce9c265a591c8d5072fcd83db
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222739"
---
# <a name="interpretability-model-explanations-in-automated-machine-learning-preview"></a>Capacidade de interpretação: explicações de modelos no machine learning automatizado (pré-visualização)



Neste artigo, aprende-se a obter explicações para o machine learning automatizado (AutoML) em Azure Machine Learning. O AutoML ajuda-o a compreender a importância da funcionalidade dos modelos gerados. 

Todas as versões SDK depois de 1.0.85 `model_explainability=True` definidas por padrão. Na versão SDK 1.0.85 e nas versões anteriores os utilizadores precisam de definir `model_explainability=True` o objeto para utilizar a interpretação do `AutoMLConfig` modelo. 

Neste artigo, vai aprender a:

- Execute a interpretação durante o treino para melhor modelo ou qualquer modelo.
- Ativar visualizações para ajudá-lo a ver padrões em dados e explicações.
- Implementar a interpretação durante a inferência ou pontuação.

## <a name="prerequisites"></a>Pré-requisitos

- Características de interpretação. Corra `pip install azureml-interpret` para obter o pacote necessário.
- Conhecimento de construir experiências autoML. Para obter mais informações sobre como utilizar o Azure Machine Learning SDK, complete este [tutorial de modelo de regressão](tutorial-auto-train-models.md) ou veja como [configurar experiências AutoML](how-to-configure-auto-train.md).

## <a name="interpretability-during-training-for-the-best-model"></a>Interpretação durante o treino para o melhor modelo

Recupere a explicação do `best_run` , que inclui explicações para características cruas e projetadas.

> [!Warning]
> A interpretação, melhor explicação para o modelo, não está disponível para experiências de previsão de ML auto que recomendem os seguintes algoritmos como o melhor modelo: 
> * TCNForecaster
> * AutoArima
> * ExponencialSmoothing
> * Profeta
> * Média 
> * Ingénuo
> * Média sazonal 
> * Ingénua Sazonal

### <a name="download-engineered-feature-importance-from-artifact-store"></a>Descarregue a importância do recurso projetado da loja de artefactos

Pode utilizar `ExplanationClient` para descarregar as explicações de recursos projetados da loja de artefactos do `best_run` . 

```python
from azureml.interpret import ExplanationClient

client = ExplanationClient.from_run(best_run)
engineered_explanations = client.download_model_explanation(raw=False)
print(engineered_explanations.get_feature_importance_dict())
```

## <a name="interpretability-during-training-for-any-model"></a>Interpretação durante o treino para qualquer modelo 

Quando calculas explicações de modelos e as visualizas, não te limitas a uma explicação de modelo existente para um modelo AutoML. Também pode obter uma explicação para o seu modelo com diferentes dados de teste. Os passos desta secção mostram-lhe como calcular e visualizar a importância da funcionalidade projetada com base nos seus dados de teste.

### <a name="retrieve-any-other-automl-model-from-training"></a>Recupere qualquer outro modelo AutoML do treino

```python
automl_run, fitted_model = local_run.get_output(metric='accuracy')
```

### <a name="set-up-the-model-explanations"></a>Configurar as explicações do modelo

Use `automl_setup_model_explanations` para obter as explicações projetadas. Pode `fitted_model` gerar os seguintes itens:

- Dados em destaque a partir de amostras treinadas ou de teste
- Listas de nomes de recursos projetados
- Classes encontráveis na sua coluna rotulada em cenários de classificação

`automl_explainer_setup_obj`Contém todas as estruturas da lista acima.

```python
from azureml.train.automl.runtime.automl_explain_utilities import automl_setup_model_explanations

automl_explainer_setup_obj = automl_setup_model_explanations(fitted_model, X=X_train, 
                                                             X_test=X_test, y=y_train, 
                                                             task='classification')
```

### <a name="initialize-the-mimic-explainer-for-feature-importance"></a>Inicialize o Explicador De Mimic para a importância do recurso

Para gerar uma explicação para os modelos AutoML, utilize a `MimicWrapper` classe. Pode rubricar o MimicWrapper com estes parâmetros:

- O objeto de configuração do explicador
- O seu espaço de trabalho
- Um modelo de substituição para explicar o `fitted_model` modelo AutoML

O MimicWrapper também leva o `automl_run` objeto onde as explicações projetadas serão carregadas.

```python
from azureml.interpret import MimicWrapper

# Initialize the Mimic Explainer
explainer = MimicWrapper(ws, automl_explainer_setup_obj.automl_estimator,
                         explainable_model=automl_explainer_setup_obj.surrogate_model, 
                         init_dataset=automl_explainer_setup_obj.X_transform, run=automl_run,
                         features=automl_explainer_setup_obj.engineered_feature_names, 
                         feature_maps=[automl_explainer_setup_obj.feature_map],
                         classes=automl_explainer_setup_obj.classes,
                         explainer_kwargs=automl_explainer_setup_obj.surrogate_model_params)
```

### <a name="use-mimicexplainer-for-computing-and-visualizing-engineered-feature-importance"></a>Use MimicExplainer para computação e visualização da importância do recurso projetado

Pode ligar para o `explain()` método em MimicWrapper com as amostras de teste transformadas para obter a importância da funcionalidade para as características geradas. Também pode utilizar `ExplanationDashboard` para visualizar a visualização do painel de instrumentos dos valores de importância da característica das funcionalidades geradas pelos admis de marcação AutoML.

```python
engineered_explanations = explainer.explain(['local', 'global'], eval_dataset=automl_explainer_setup_obj.X_test_transform)
print(engineered_explanations.get_feature_importance_dict())
```

## <a name="interpretability-during-inference"></a>Interpretação durante a inferência

Nesta secção, aprende-se a operacionalizar um modelo AutoML com o explicador que foi utilizado para calcular as explicações na secção anterior.

### <a name="register-the-model-and-the-scoring-explainer"></a>Registe o modelo e o explicador de pontuação

Utilize `TreeScoringExplainer` o para criar o explicador de pontuação que irá calcular os valores de importância da característica projetada no tempo de inferência. Inicializa-se o explicador de pontuação com o `feature_map` que foi calculado anteriormente. 

Guarde o explicador de pontuação e, em seguida, registe o modelo e o explicador de pontuação com o Serviço de Gestão de Modelos. Execute o seguinte código:

```python
from azureml.interpret.scoring.scoring_explainer import TreeScoringExplainer, save

# Initialize the ScoringExplainer
scoring_explainer = TreeScoringExplainer(explainer.explainer, feature_maps=[automl_explainer_setup_obj.feature_map])

# Pickle scoring explainer locally
save(scoring_explainer, exist_ok=True)

# Register trained automl model present in the 'outputs' folder in the artifacts
original_model = automl_run.register_model(model_name='automl_model', 
                                           model_path='outputs/model.pkl')

# Register scoring explainer
automl_run.upload_file('scoring_explainer.pkl', 'scoring_explainer.pkl')
scoring_explainer_model = automl_run.register_model(model_name='scoring_explainer', model_path='scoring_explainer.pkl')
```

### <a name="create-the-conda-dependencies-for-setting-up-the-service"></a>Criar as dependências conda para a criação do serviço

Em seguida, crie as dependências ambientais necessárias no recipiente para o modelo implantado. Por favor, note que os incumprimentos azureml com a versão >= 1.0.45 devem ser listados como uma dependência de pip, porque contém a funcionalidade necessária para hospedar o modelo como um serviço web.

```python
from azureml.core.conda_dependencies import CondaDependencies

azureml_pip_packages = [
    'azureml-interpret', 'azureml-train-automl', 'azureml-defaults'
]

myenv = CondaDependencies.create(conda_packages=['scikit-learn', 'pandas', 'numpy', 'py-xgboost<=0.80'],
                                 pip_packages=azureml_pip_packages,
                                 pin_sdk_version=True)

with open("myenv.yml","w") as f:
    f.write(myenv.serialize_to_string())

with open("myenv.yml","r") as f:
    print(f.read())

```

### <a name="deploy-the-service"></a>Implementar o serviço

Desloque o serviço utilizando o ficheiro conda e o ficheiro de pontuação dos passos anteriores.

```python
from azureml.core.webservice import Webservice
from azureml.core.webservice import AciWebservice
from azureml.core.model import Model, InferenceConfig
from azureml.core.environment import Environment

aciconfig = AciWebservice.deploy_configuration(cpu_cores=1,
                                               memory_gb=1,
                                               tags={"data": "Bank Marketing",  
                                                     "method" : "local_explanation"},
                                               description='Get local explanations for Bank marketing test data')
myenv = Environment.from_conda_specification(name="myenv", file_path="myenv.yml")
inference_config = InferenceConfig(entry_script="score_local_explain.py", environment=myenv)

# Use configs and models generated above
service = Model.deploy(ws,
                       'model-scoring',
                       [scoring_explainer_model, original_model],
                       inference_config,
                       aciconfig)
service.wait_for_deployment(show_output=True)
```

### <a name="inference-with-test-data"></a>Inferência com dados de teste

Inferência com alguns dados de teste para ver o valor previsto do modelo AutoML, atualmente suportado apenas em Azure Machine Learning SDK. Veja as importâncias do recurso contribuindo para um valor previsto. 

```python
if service.state == 'Healthy':
    # Serialize the first row of the test data into json
    X_test_json = X_test[:1].to_json(orient='records')
    print(X_test_json)
    # Call the service to get the predictions and the engineered explanations
    output = service.run(X_test_json)
    # Print the predicted value
    print(output['predictions'])
    # Print the engineered feature importances for the predicted value
    print(output['engineered_local_importance_values'])
```

### <a name="visualize-to-discover-patterns-in-data-and-explanations-at-training-time"></a>Visualizar para descobrir padrões em dados e explicações no momento do treino

Pode visualizar o gráfico de importância de recurso no seu espaço de trabalho no [estúdio Azure Machine Learning.](https://ml.azure.com) Depois de concluída a sua execução AutoML, **selecione Ver detalhes do modelo** para visualizar uma execução específica. Selecione o **separador Explicações** para ver o painel de visualização da explicação.

[![Arquitetura de Interpretação de Aprendizagem Automática](./media/how-to-machine-learning-interpretability-automl/automl-explanation.png)](./media/how-to-machine-learning-interpretability-automl/automl-explanation.png#lightbox)

Para obter mais informações sobre as visualizações do painel de explicações e parcelas específicas, consulte o [documento de como fazer o doc sobre a interpretação](how-to-machine-learning-interpretability-aml.md).

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre como pode permitir explicações de modelos e destacar importância em áreas do Azure Machine Learning SDK que não seja o machine learning automatizado, consulte o [artigo de conceito sobre interpretação.](how-to-machine-learning-interpretability.md)
