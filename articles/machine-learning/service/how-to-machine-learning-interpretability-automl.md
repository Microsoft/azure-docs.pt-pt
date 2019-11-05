---
title: Interpretação de modelo no ML automatizado
titleSuffix: Azure Machine Learning
description: Saiba como explicar por que o modelo de ML automatizado faz previsões usando o SDK do Azure Machine Learning. Ele pode ser usado durante o treinamento e a inferência para entender como seu modelo determina a importância dos recursos e faz previsões.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: trbye
ms.date: 10/25/2019
ms.openlocfilehash: 2c9df55eb319dd45281eca4684c79d83dc6ef933
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73515333"
---
# <a name="model-interpretability-for-automated-ml-models"></a>Interpretação de modelo para modelos de ML automatizados

[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste "como", você aprenderá a habilitar a funcionalidade de interpretação no Machine Learning automatizado usando o serviço Azure Machine Learning. O ML automatizado permite que você entenda a importância bruta e em engenharia dos recursos. Para usar a interpretação de modelo, defina `model_explainability=True` no objeto `AutoMLConfig`.  

Neste artigo, você aprende as seguintes tarefas:

* Interpretabilidade durante o treinamento para o melhor modelo ou qualquer modelo
* Habilitando visualizações para ajudá-lo na descoberta de padrões em dados e explicações
* Interpretabilidade durante a inferência ou Pontuação

## <a name="prerequisites"></a>Pré-requisitos

* Execute `pip install azureml-interpret azureml-contrib-interpret` para obter os pacotes necessários para os recursos de interpretação.
* Este artigo pressupõe o conhecimento da criação de experimentos de ML automatizados. Consulte o [tutorial](tutorial-auto-train-models.md) ou [instruções](how-to-configure-auto-train.md) para aprender a usar o ml automatizado no SDK.
 
## <a name="interpretability-during-training-for-the-best-model"></a>Interpretabilidade durante o treinamento para o melhor modelo 

Recupere a explicação da `best_run`, que inclui explicações para recursos de engenharia e recursos brutos. 

### <a name="download-engineered-feature-importance-from-artifact-store"></a>Baixar a importância de recursos com engenharia da loja de artefatos

Você pode usar `ExplanationClient` para baixar as explicações de recursos de engenharia do repositório de artefatos do best_run. Para obter a explicação do conjunto de recursos brutos `raw=True`. 

```python
from azureml.contrib.interpret.explanation.explanation_client import ExplanationClient

client = ExplanationClient.from_run(best_run)
engineered_explanations = client.download_model_explanation(raw=False)
print(engineered_explanations.get_feature_importance_dict())
```

## <a name="interpretability-during-training-for-any-model"></a>Interpretabilidade durante o treinamento de qualquer modelo 

Nesta seção, você aprenderá a computar explicações de modelo e visualizar as explicações. Além de recuperar uma explicação de modelo existente para um modelo de ML automatizado, você também pode explicar seu modelo com dados de teste diferentes. As etapas a seguir permitirão que você calcule e visualize a importância de recursos com engenharia e a importância bruta do recurso com base em seus dados de teste.

### <a name="retrieve-any-other-automl-model-from-training"></a>Recuperar qualquer outro modelo de AutoML do treinamento

```python
automl_run, fitted_model = local_run.get_output(metric='r2_score')
```

### <a name="setup-the-model-explanations"></a>Configurar as explicações do modelo

O fitted_model pode gerar os seguintes itens, que serão usados para obter as explicações de recurso com engenharia e bruta usando o automl_setup_model_explanations:

* Dados de destacados de exemplos de treinamento/exemplos de teste
* Coletar listas de nomes de recursos com engenharia e bruto
* Localize as classes na coluna rotulada em cenários de classificação

O automl_explainer_setup_obj contém todas as estruturas da lista acima.

```python
from azureml.train.automl.automl_explain_utilities import AutoMLExplainerSetupClass, automl_setup_model_explanations

automl_explainer_setup_obj = automl_setup_model_explanations(fitted_model, X=X_train, 
                                                             X_test=X_test, y=y_train, 
                                                             task='classification')
```
### <a name="initialize-the-mimic-explainer-for-feature-importance"></a>Inicializar o explicador de imitação para a importância do recurso

Para explicar os modelos de AutoML, use a classe `MimicWrapper`. O MimicWrapper pode ser inicializado com parâmetros para o objeto de instalação do explicador, seu espaço de trabalho e um modelo de LightGBM que atua como um modelo substituto para explicar o modelo de ML automatizado (fitted_model aqui). O MimicWrapper também usa o objeto automl_run onde as explicações brutas e com engenharia serão carregadas.

```python
from azureml.interpret.mimic.models.lightgbm_model import LGBMExplainableModel
from azureml.interpret.mimic_wrapper import MimicWrapper

explainer = MimicWrapper(ws, automl_explainer_setup_obj.automl_estimator, LGBMExplainableModel, 
                         init_dataset=automl_explainer_setup_obj.X_transform, run=automl_run,
                         features=automl_explainer_setup_obj.engineered_feature_names, 
                         feature_maps=[automl_explainer_setup_obj.feature_map],
                         classes=automl_explainer_setup_obj.classes)
```

### <a name="use-mimicexplainer-for-computing-and-visualizing-engineered-feature-importance"></a>Use MimicExplainer para computação e visualização da importância do recurso com engenharia

O método explique () em MimicWrapper pode ser chamado com os exemplos de teste transformados para obter a importância do recurso para os recursos de engenharia gerados. Você também pode usar o ExplanationDashboard para exibir a visualização da placa tracejada dos valores de importância do recurso dos recursos de engenharia gerados por featurizers do ML automatizado.

```python
from azureml.contrib.interpret.visualize import ExplanationDashboard
engineered_explanations = explainer.explain(['local', 'global'],              
                                            eval_dataset=automl_explainer_setup_obj.X_test_transform)

print(engineered_explanations.get_feature_importance_dict())
ExplanationDashboard(engineered_explanations, automl_explainer_setup_obj.automl_estimator, automl_explainer_setup_obj.X_test_transform)
```
### <a name="use-mimic-explainer-for-computing-and-visualizing-raw-feature-importance"></a>Usar o explicador de imitação para computação e visualização da importância bruta do recurso

O método explique () em MimicWrapper pode ser chamado novamente com os exemplos de teste transformados e a configuração `get_raw` como true para obter a importância do recurso para os recursos brutos. Você também pode usar o ExplanationDashboard para exibir a visualização do painel dos valores de importância do recurso dos recursos brutos.

```python
from azureml.contrib.interpret.visualize import ExplanationDashboard

raw_explanations = explainer.explain(['local', 'global'], get_raw=True, 
                                     raw_feature_names=automl_explainer_setup_obj.raw_feature_names,
                                     eval_dataset=automl_explainer_setup_obj.X_test_transform)

print(raw_explanations.get_feature_importance_dict())
ExplanationDashboard(raw_explanations, automl_explainer_setup_obj.automl_pipeline, automl_explainer_setup_obj.X_test_raw)
```

### <a name="interpretability-during-inference"></a>Interpretabilidade durante a inferência

Nesta seção, você aprenderá a colocar em operação um modelo de ML automatizado com o explicador, que foi usado para calcular as explicações na seção anterior.

### <a name="register-the-model-and-the-scoring-explainer"></a>Registrar o modelo e o explicador de Pontuação

Use o `TreeScoringExplainer` para criar o explicador de pontuação, que será usado para calcular os valores brutos e de importância de recursos de engenharia no tempo de inferência. Observe que você inicializa o explicador de pontuação com o feature_map que foi calculado anteriormente. O feature_map será usado pelo explicador de Pontuação para retornar a importância bruta do recurso.

No código a seguir, você salva o explicador de Pontuação e registra o modelo e o explicador de pontuação com o Serviço Gerenciamento de Modelos.

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

### <a name="create-the-conda-dependencies-for-setting-up-the-service"></a>Criar as dependências de Conda para configurar o serviço

Em seguida, você cria as dependências de ambiente necessárias no contêiner para o modelo implantado.

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

Implante o serviço usando o arquivo Conda e o arquivo de Pontuação das etapas anteriores.

```python
from azureml.core.webservice import Webservice
from azureml.core.model import InferenceConfig
from azureml.core.webservice import AciWebservice
from azureml.core.model import Model

aciconfig = AciWebservice.deploy_configuration(cpu_cores=1, 
                                               memory_gb=1, 
                                               tags={"data": "Bank Marketing",  
                                                     "method" : "local_explanation"}, 
                                               description='Get local explanations for Bank marketing test data')

inference_config = InferenceConfig(runtime= "python", 
                                   entry_script="score_local_explain.py",
                                   conda_file="myenv.yml")

# Use configs and models generated above
service = Model.deploy(ws, 'model-scoring', [scoring_explainer_model, original_model], inference_config, aciconfig)
service.wait_for_deployment(show_output=True)
```

### <a name="inference-using-test-data"></a>Inferência usando dados de teste

Inferência usando alguns dados de teste para ver o valor previsto do modelo de ML automatizado e exibir a importância do recurso projetado para o valor previsto e a importância bruta do recurso para o valor previsto.

```python
if service.state == 'Healthy':
    # Serialize the first row of the test data into json
    X_test_json = X_test[:1].to_json(orient='records')
    print(X_test_json)
    # Call the service to get the predictions and the engineered and raw explanations
    output = service.run(X_test_json)
    # Print the predicted value
    print(output['predictions'])
    # Print the engineered feature importances for the predicted value
    print(output['engineered_local_importance_values'])
    # Print the raw feature importances for the predicted value
    print(output['raw_local_importance_values'])
```

### <a name="visualizations-to-aid-you-in-the-discovery-of-patterns-in-data-and-explanations-at-training-time"></a>Visualizações para ajudá-lo na descoberta de padrões em dados e explicações no tempo de treinamento

Você também pode visualizar o gráfico de importância do recurso em seu espaço de trabalho no [Azure Machine Learning Studio](https://ml.azure.com). Quando a execução do ML automatizado for concluída, você precisará clicar em "Exibir detalhes do modelo", que levará você para uma execução específica. Aqui, você clica na guia "explicações" para ver o painel de visualização da explicação. 

[Arquitetura de interpretação de Machine Learning ![](./media/machine-learning-interpretability-explainability/automl-explainability.png)](./media/machine-learning-interpretability-explainability/automl-explainability.png#lightbox)

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre como as explicações de modelo e a importância do recurso podem ser habilitadas em outras áreas do SDK fora do aprendizado de máquina automatizado, consulte o artigo [conceito](how-to-machine-learning-interpretability.md) sobre interpretação.