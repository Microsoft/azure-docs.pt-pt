---
title: Treine modelos de aprendizagem automática de scikit-learn
titleSuffix: Azure Machine Learning
description: Saiba como o Azure Machine Learning permite-lhe escalar um trabalho de formação scikit-learn usando recursos de computação em nuvem elástica.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: jordane
author: jpe316
ms.date: 09/28/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 807174fdbede2e4631b3ca1df7220904038da4c8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102518301"
---
# <a name="train-scikit-learn-models-at-scale-with-azure-machine-learning"></a>Treine modelos de aprendizagem de scikit em escala com Azure Machine Learning

Neste artigo, aprenda a executar os seus scripts de treino scikit-learn com Azure Machine Learning.

Os scripts de exemplo neste artigo são usados para classificar imagens de flores de íris para construir um modelo de aprendizagem automática baseado no conjunto de dados da [íris](https://archive.ics.uci.edu/ml/datasets/iris)do scikit-learn .

Quer esteja a treinar um modelo de aprendizagem automática de scikit-learn a partir do solo ou se está a trazer um modelo existente para a nuvem, pode usar o Azure Machine Learning para escalar trabalhos de formação de código aberto usando recursos de computação em nuvem elástica. Pode construir, implementar, verrativa e monitorizar modelos de nível de produção com Azure Machine Learning.

## <a name="prerequisites"></a>Pré-requisitos

Executar este código em qualquer um destes ambientes:
 - Exemplo de computação Azure Machine Learning - sem transferências ou instalação necessárias

    - Complete o [Tutorial: Ambiente de configuração e espaço de trabalho](tutorial-1st-experiment-sdk-setup.md)  para criar um servidor de caderno dedicado pré-carregado com o SDK e o repositório de amostras.
    - Na pasta de formação de amostras no servidor do portátil, encontre um caderno completo e expandido navegando para este diretório: **como usar-azureml > estruturas ml > scikit-learn > pasta de ajuste de linha-hiperparametro-implement-com-sklearn.**

 - O seu próprio servidor de cadernos Jupyter

    - [Instale o Azure Machine Learning SDK](/python/api/overview/azure/ml/install) (>= 1.13.0).
    - [Crie um ficheiro de configuração do espaço de trabalho.](how-to-configure-environment.md#workspace)

## <a name="set-up-the-experiment"></a>Configurar a experiência

Esta secção estabelece a experiência de treino carregando os pacotes Python necessários, inicializando um espaço de trabalho, definindo o ambiente de treino e preparando o roteiro de treino.

### <a name="initialize-a-workspace"></a>Inicializar um espaço de trabalho

O espaço de [trabalho Azure Machine Learning](concept-workspace.md) é o recurso de alto nível para o serviço. Proporciona-lhe um lugar centralizado para trabalhar com todos os artefactos que cria. No Python SDK, você pode aceder aos artefactos do espaço de trabalho criando um [`workspace`](/python/api/azureml-core/azureml.core.workspace.workspace) objeto.

Crie um objeto de espaço de trabalho a partir do `config.json` ficheiro criado na secção [pré-requisitos](#prerequisites).

```Python
from azureml.core import Workspace

ws = Workspace.from_config()
```

### <a name="prepare-scripts"></a>Preparar scripts

Neste tutorial, o roteiro de treino **train_iris.py** já [está](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/scikit-learn/train-hyperparameter-tune-deploy-with-sklearn/train_iris.py)aqui fornecido. Na prática, você deve ser capaz de pegar em qualquer script de treino personalizado como está e executá-lo com Azure ML sem ter que modificar o seu código.

Notas:
- O script de treino fornecido mostra como registar algumas métricas na sua execução Azure ML usando o `Run` objeto dentro do script.
- O script de formação fornecido utiliza dados de exemplo da  `iris = datasets.load_iris()` função.  Para utilizar e aceder aos seus próprios dados, consulte [como treinar com conjuntos de dados](how-to-train-with-datasets.md) para disponibilizar dados durante o treino.

### <a name="define-your-environment"></a>Defina o seu ambiente

Para definir o [Ambiente](concept-environments.md) Azure ML que engloba as dependências do seu script de treino, você pode definir um ambiente personalizado ou usar e Azure ML ambiente curado.

#### <a name="use-a-curated-environment"></a>Use um ambiente curado
Opcionalmente, a Azure ML fornece ambientes pré-construídos e curados se não quiser definir o seu próprio ambiente. Para mais informações, consulte [aqui.](resource-curated-environments.md)
Se quiser utilizar um ambiente curado, pode executar o seguinte comando:

```python
from azureml.core import Environment

sklearn_env = Environment.get(workspace=ws, name='AzureML-Tutorial')
```

#### <a name="create-a-custom-environment"></a>Criar um ambiente personalizado

Também pode criar o seu próprio ambiente personalizado. Defina as suas dependências conda num ficheiro YAML; neste exemplo, o ficheiro é nomeado `conda_dependencies.yml` .

```yaml
dependencies:
  - python=3.6.2
  - scikit-learn
  - numpy
  - pip:
    - azureml-defaults
```

Crie um ambiente Azure ML a partir desta especificação ambiental conda. O ambiente será embalado num contentor Docker em tempo de execução.
```python
from azureml.core import Environment

sklearn_env = Environment.from_conda_specification(name='sklearn-env', file_path='conda_dependencies.yml')
```

Para obter mais informações sobre a criação e utilização de ambientes, consulte [Criar e utilizar ambientes de software em Azure Machine Learning](how-to-use-environments.md).

## <a name="configure-and-submit-your-training-run"></a>Configure e submeta a sua formação

### <a name="create-a-scriptrunconfig"></a>Criar um ScriptRunConfig
Crie um objeto ScriptRunConfig para especificar os detalhes de configuração do seu trabalho de treino, incluindo o seu script de treino, ambiente a utilizar e o alvo de computação para executar.
Quaisquer argumentos para o seu script de treino serão passados através da linha de comando, se especificado no `arguments` parâmetro.

O seguinte código configurará um objeto ScriptRunConfig para submeter o seu trabalho para execução na sua máquina local.

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory='.',
                      script='train_iris.py',
                      arguments=['--kernel', 'linear', '--penalty', 1.0],
                      environment=sklearn_env)
```

Se quiser executar o seu trabalho num cluster remoto, pode especificar o alvo de computação pretendido para o `compute_target` parâmetro do ScriptRunConfig.

```python
from azureml.core import ScriptRunConfig

compute_target = ws.compute_targets['<my-cluster-name>']
src = ScriptRunConfig(source_directory='.',
                      script='train_iris.py',
                      arguments=['--kernel', 'linear', '--penalty', 1.0],
                      compute_target=compute_target,
                      environment=sklearn_env)
```

### <a name="submit-your-run"></a>Submeta a sua corrida
```python
from azureml.core import Experiment

run = Experiment(ws,'Tutorial-TrainIRIS').submit(src)
run.wait_for_completion(show_output=True)
```

> [!WARNING]
> A Azure Machine Learning executa scripts de formação copiando todo o diretório de origem. Se tiver dados sensíveis que não pretende fazer o upload, utilize um [ficheiro .ignore](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) ou não o inclua no diretório de origem . Em vez disso, aceda aos seus dados utilizando um [conjunto de dados](how-to-train-with-datasets.md)Azure ML .

### <a name="what-happens-during-run-execution"></a>O que acontece durante a execução da execução
À medida que a corrida é executada, passa pelas seguintes fases:

- **Preparação:** Uma imagem de estivador é criada de acordo com o ambiente definido. A imagem é enviada para o registo de contentores do espaço de trabalho e em cache para posteriores execuções. Os registos também são transmitidos para o histórico de execução e podem ser vistos para monitorizar o progresso. Se um ambiente curado for especificado, em vez disso, a imagem em cache que o ambiente curado será usado.

- **Dimensionamento**: O cluster tenta aumentar se o cluster de AI do lote necessitar de mais nós para executar a execução do que estão atualmente disponíveis.

- **Execução**: Todos os scripts na pasta do script são carregados para o alvo do cálculo, as lojas de dados são montadas ou copiadas, e a `script` é executada. As saídas da sestada e da pasta **./logs** são transmitidas para o histórico de execução e podem ser utilizadas para monitorizar a execução.

- **Pós-Processamento**: A pasta **./outputs** da execução é copiada para o histórico de execução.

## <a name="save-and-register-the-model"></a>Guardar e registar o modelo

Uma vez treinado o modelo, pode economize e registe no seu espaço de trabalho. O registo de modelos permite-lhe armazenar e versar os seus modelos no seu espaço de trabalho para simplificar a [gestão e implementação de modelos.](concept-model-management-and-deployment.md)

Adicione o seguinte código ao seu script de treino, train_iris.py, para salvar o modelo. 

``` Python
import joblib

joblib.dump(svm_model_linear, 'model.joblib')
```

Registe o modelo no seu espaço de trabalho com o seguinte código. Especificando os parâmetros `model_framework` `model_framework_version` , `resource_configuration` e, a implementação de nenhum modelo de código fica disponível. A implementação de um modelo sem código permite-lhe implementar diretamente o seu modelo como um serviço web a partir do modelo registado, e o [`ResourceConfiguration`](/python/api/azureml-core/azureml.core.resource_configuration.resourceconfiguration) objeto define o recurso computacional para o serviço web.

```Python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = run.register_model(model_name='sklearn-iris', 
                           model_path='outputs/model.joblib',
                           model_framework=Model.Framework.SCIKITLEARN,
                           model_framework_version='0.19.1',
                           resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5))
```

## <a name="deployment"></a>Implementação

O modelo que acabou de registar pode ser implantado da mesma forma que qualquer outro modelo registado no Azure ML. A implementação como-fazer contém uma secção sobre os modelos de registo, mas pode saltar diretamente para [criar um alvo de computação](how-to-deploy-and-where.md#choose-a-compute-target) para implementação, uma vez que já tem um modelo registado.

### <a name="preview-no-code-model-deployment"></a>(Pré-visualização) Implementação de modelo sem código

Em vez da rota de implementação tradicional, também pode utilizar a funcionalidade de implementação sem código (pré-visualização) para o scikit-learn. A implementação de modelos sem código é suportada para todos os tipos de modelos de aprendizagem de scikit incorporados. Ao registar o seu modelo como mostrado acima com o `model_framework` `model_framework_version` , e `resource_configuration` parâmetros, pode simplesmente utilizar a [`deploy()`](/python/api/azureml-core/azureml.core.model%28class%29#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) função estática para implantar o seu modelo.

```python
web_service = Model.deploy(ws, "scikit-learn-service", [model])
```

NOTA: Estas dependências estão incluídas no recipiente de inferência scikit-learn pré-construído.

```yaml
    - azureml-defaults
    - inference-schema[numpy-support]
    - scikit-learn
    - numpy
```

A forma completa [de como](how-to-deploy-and-where.md) fazer cobre a implantação em Azure Machine Learning em maior profundidade.


## <a name="next-steps"></a>Passos seguintes

Neste artigo, treinou e registou um modelo de aprendizagem de scikit, e aprendeu sobre opções de implementação. Veja estes outros artigos para saber mais sobre Azure Machine Learning.

* [Métricas de corrida de pista durante o treino](how-to-track-experiments.md)
* [Otimizar hiperparâmetros](how-to-tune-hyperparameters.md)
