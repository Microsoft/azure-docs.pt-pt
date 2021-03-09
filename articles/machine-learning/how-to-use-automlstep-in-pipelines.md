---
title: Utilizar ML automatizado em gasodutos ML
titleSuffix: Azure Machine Learning
description: O AutoMLStep permite-lhe utilizar aprendizagem automática de máquinas nos seus oleodutos.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
manager: cgronlun
ms.date: 02/28/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, automl
ms.openlocfilehash: 0de3c9a7cf464f38a1a12d8bc19451fb1158a5ad
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102520511"
---
# <a name="use-automated-ml-in-an-azure-machine-learning-pipeline-in-python"></a>Utilize ML automatizado num oleoduto de aprendizagem de máquinas Azure em Python


A capacidade automatizada de ML da Azure Machine Learning ajuda-o a descobrir modelos de alto desempenho sem que reimpliure todas as abordagens possíveis. Combinado com os oleodutos Azure Machine Learning, pode criar fluxos de trabalho implantáveis que podem rapidamente descobrir o algoritmo que funciona melhor para os seus dados. Este artigo irá mostrar-lhe como juntar eficientemente um passo de preparação de dados para um passo ML automatizado. ML automatizado pode rapidamente descobrir o algoritmo que funciona melhor para os seus dados, ao mesmo tempo que o coloca na estrada para MLOps e operacionalização de ciclo de vida modelo com oleodutos.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma conta gratuita antes de começar. Experimente hoje a [versão gratuita ou paga do Azure Machine Learning.](https://aka.ms/AMLFree)

* Uma área de trabalho do Azure Machine Learning. Ver [Criar um espaço de trabalho de aprendizagem de máquinas Azure](how-to-manage-workspace.md).  

* Familiaridade com as instalações automatizadas de [aprendizagem automática](concept-automated-ml.md) de máquinas e [máquinas de aprendizagem](concept-ml-pipelines.md) da Azure e SDK.

## <a name="review-automated-mls-central-classes"></a>Rever as classes centrais automatizadas da ML

ML automatizado num oleoduto é representado por um `AutoMLStep` objeto. A `AutoMLStep` classe é uma subclasse `PipelineStep` de. Um gráfico de `PipelineStep` objetos define um `Pipeline` .

Existem várias subclasses `PipelineStep` de. Além do `AutoMLStep` , este artigo mostrará um para a preparação de `PythonScriptStep` dados e outro para o registo do modelo.

A forma preferida de mover inicialmente os dados _para_ um oleoduto ML é com `Dataset` objetos. Para mover dados _entre_ etapas e possível poupança de saída de dados de execuções, a forma preferida é com [`OutputFileDatasetConfig`](/python/api/azureml-core/azureml.data.outputfiledatasetconfig) e [`OutputTabularDatasetConfig`](/python/api/azureml-core/azureml.data.output_dataset_config.outputtabulardatasetconfig) objetos. Para ser `AutoMLStep` utilizado, o `PipelineData` objeto deve ser transformado num `PipelineOutputTabularDataset` objeto. Para obter mais informações, consulte [os dados de entrada e saída dos oleodutos ML](how-to-move-data-in-out-of-pipelines.md).

O `AutoMLStep` é configurado através de um `AutoMLConfig` objeto. `AutoMLConfig` é uma classe flexível, como discutido em [Configure experiências automatizadas de ML em Python](./how-to-configure-auto-train.md#configure-your-experiment-settings). 

Um `Pipeline` corre `Experiment` numa. O gasoduto `Run` tem, para cada passo, uma `StepRun` criança. As saídas do ML automatizado `StepRun` são as métricas de formação e o modelo de maior desempenho.

Para tornar as coisas concretas, este artigo cria um simples oleoduto para uma tarefa de classificação. A tarefa é prever a sobrevivência do Titanic, mas não discutiremos os dados ou tarefas a não ser de passagem.

## <a name="get-started"></a>Introdução

### <a name="retrieve-initial-dataset"></a>Recuperar o conjunto de dados inicial

Frequentemente, um fluxo de trabalho ML começa com dados de base pré-existentes. Este é um bom cenário para um conjunto de dados registado. Os conjuntos de dados são visíveis em todo o espaço de trabalho, na versão de suporte, e podem ser explorados interativamente. Existem muitas formas de criar e povoar um conjunto de dados, como discutido no [Create Azure Machine Learning datasets](how-to-create-register-datasets.md). Uma vez que vamos usar o Python SDK para criar o nosso oleoduto, use o SDK para descarregar dados de base e registá-lo com o nome 'titanic_ds'.

```python
from azureml.core import Workspace, Dataset

ws = Workspace.from_config()
if not 'titanic_ds' in ws.datasets.keys() :
    # create a TabularDataset from Titanic training data
    web_paths = ['https://dprepdata.blob.core.windows.net/demo/Titanic.csv',
                 'https://dprepdata.blob.core.windows.net/demo/Titanic2.csv']
    titanic_ds = Dataset.Tabular.from_delimited_files(path=web_paths)

    titanic_ds.register(workspace = ws,
                                     name = 'titanic_ds',
                                     description = 'Titanic baseline data',
                                     create_new_version = True)

titanic_ds = Dataset.get_by_name(ws, 'titanic_ds')
```

O código inicia sessão no espaço de trabalho Azure Machine Learning definido em **config.js** (para uma explicação, consulte [Criar um ficheiro de configuração do espaço de trabalho](how-to-configure-environment.md#workspace). Se já não existe um conjunto de dados `'titanic_ds'` nomeado registado, então cria um. O código descarrega dados de CSV a partir da Web, utiliza-os para instantaneamente a `TabularDataset` e, em seguida, regista o conjunto de dados com o espaço de trabalho. Finalmente, a função `Dataset.get_by_name()` atribui `Dataset` o a `titanic_ds` . 

### <a name="configure-your-storage-and-compute-target"></a>Configure o seu alvo de armazenamento e cálculo

Os recursos adicionais que o oleoduto necessitará são de armazenamento e, em geral, recursos de cálculo Azure Machine Learning. 

```python
from azureml.core import Datastore
from azureml.core.compute import AmlCompute, ComputeTarget

datastore = ws.get_default_datastore()

compute_name = 'cpu-cluster'
if not compute_name in ws.compute_targets :
    print('creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                                min_nodes=0,
                                                                max_nodes=1)
    compute_target = ComputeTarget.create(ws, compute_name, provisioning_config)

    compute_target.wait_for_completion(
        show_output=True, min_node_count=None, timeout_in_minutes=20)

    # Show the result
    print(compute_target.get_status().serialize())

compute_target = ws.compute_targets[compute_name]
```

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

Os dados intermédios entre a preparação de dados e o passo ML automatizado podem ser armazenados na loja de dados padrão do espaço de trabalho, pelo que não precisamos de fazer mais do que chamar `get_default_datastore()` o `Workspace` objeto. 

Depois disso, o código verifica se o alvo de computação AML `'cpu-cluster'` já existe. Caso contrário, especificamos que queremos um pequeno alvo de computação baseado em CPU. Se planeia utilizar as funcionalidades automatizadas de aprendizagem profunda da ML (por exemplo, caracterização de texto com suporte DNN) deve escolher um cálculo com forte suporte gpu, como descrito nos [tamanhos de máquinas virtuais otimizados da GPU.](../virtual-machines/sizes-gpu.md) 

O código bloqueia até que o alvo seja a provisionado e, em seguida, imprime alguns detalhes do alvo de computação criado. Finalmente, o alvo de computação nomeado é recuperado do espaço de trabalho e atribuído a `compute_target` . 

### <a name="configure-the-training-run"></a>Configure a corrida de treino

O AutoMLStep configura automaticamente as suas dependências durante a submissão do trabalho. O contexto de tempo de execução é definido através da criação e configuração de um `RunConfiguration` objeto. Aqui definimos o alvo do cálculo.

```python
from azureml.core.runconfig import RunConfiguration

aml_run_config = RunConfiguration()
# Use just-specified compute target ("cpu-cluster")
aml_run_config.target = compute_target
```

## <a name="prepare-data-for-automated-machine-learning"></a>Preparar dados para aprendizagem automática de máquinas

### <a name="write-the-data-preparation-code"></a>Escreva o código de preparação de dados

O conjunto de dados titânico de base consiste em dados numéricos e de texto mistos, faltando alguns valores. Para prepará-lo para a aprendizagem automática de máquinas, o passo do pipeline de preparação de dados:

- Preencha os dados em falta com dados aleatórios ou uma categoria correspondente a "Desconhecido"
- Transformar dados categóricos em inteiros
- Derrube colunas que não pretendemos usar
- Divida os dados em conjuntos de treino e testes
- Escreva os dados transformados para os caminhos de `OutputFileDatasetConfig` saída

```python
%%writefile dataprep.py
from azureml.core import Run

import pandas as pd 
import numpy as np 
import argparse

RANDOM_SEED=42

def prepare_age(df):
    # Fill in missing Age values from distribution of present Age values 
    mean = df["Age"].mean()
    std = df["Age"].std()
    is_null = df["Age"].isnull().sum()
    # compute enough (== is_null().sum()) random numbers between the mean, std
    rand_age = np.random.randint(mean - std, mean + std, size = is_null)
    # fill NaN values in Age column with random values generated
    age_slice = df["Age"].copy()
    age_slice[np.isnan(age_slice)] = rand_age
    df["Age"] = age_slice
    df["Age"] = df["Age"].astype(int)
    
    # Quantize age into 5 classes
    df['Age_Group'] = pd.qcut(df['Age'],5, labels=False)
    df.drop(['Age'], axis=1, inplace=True)
    return df

def prepare_fare(df):
    df['Fare'].fillna(0, inplace=True)
    df['Fare_Group'] = pd.qcut(df['Fare'],5,labels=False)
    df.drop(['Fare'], axis=1, inplace=True)
    return df 

def prepare_genders(df):
    genders = {"male": 0, "female": 1, "unknown": 2}
    df['Sex'] = df['Sex'].map(genders)
    df['Sex'].fillna(2, inplace=True)
    df['Sex'] = df['Sex'].astype(int)
    return df

def prepare_embarked(df):
    df['Embarked'].replace('', 'U', inplace=True)
    df['Embarked'].fillna('U', inplace=True)
    ports = {"S": 0, "C": 1, "Q": 2, "U": 3}
    df['Embarked'] = df['Embarked'].map(ports)
    return df
    
parser = argparse.ArgumentParser()
parser.add_argument('--output_path', dest='output_path', required=True)
args = parser.parse_args()
    
titanic_ds = Run.get_context().input_datasets['titanic_ds']
df = titanic_ds.to_pandas_dataframe().drop(['PassengerId', 'Name', 'Ticket', 'Cabin'], axis=1)
df = prepare_embarked(prepare_genders(prepare_fare(prepare_age(df))))

df.to_csv(os.path.join(args.output_path,"prepped_data.csv"))

print(f"Wrote prepped data to {args.output_path}/prepped_data.csv")
```

O corte de código acima é um exemplo completo, mas mínimo, de preparação de dados para os dados do Titanic. O snippet começa com um "comando mágico" do Jupyter para entregar o código a um ficheiro. Se não estiver a utilizar um caderno Jupyter, retire essa linha e crie o ficheiro manualmente.

As `prepare_` várias funções no snippet acima modificam a coluna relevante no conjunto de dados de entrada. Estas funções funcionam nos dados uma vez que foram transformados num `DataFrame` objeto pandas. Em cada caso, os dados em falta são preenchidos com dados aleatórios representativos ou dados categóricos que indicam "Desconhecido". Os dados categóricos baseados em texto são mapeados para inteiros. As colunas já não necessárias são substituídas ou abandonadas. 

Depois de o código definir as funções de preparação de dados, o código analisa o argumento de entrada, que é o caminho para o qual queremos escrever os nossos dados.  (Estes valores serão determinados por `OutputFileDatasetConfig` objetos que serão discutidos no próximo passo.) O código recupera o `'titanic_cs'` `Dataset` registrado, converte-o em pandas `DataFrame` , e chama as várias funções de preparação de dados. 

Uma vez que `output_path` é um diretório, a chamada para `to_csv()` especificar o nome de arquivo `prepped_data.csv` .

### <a name="write-the-data-preparation-pipeline-step-pythonscriptstep"></a>Escreva o passo do gasoduto de preparação de dados `PythonScriptStep` ()

O código de preparação de dados acima descrito deve ser associado a um `PythonScripStep` objeto a utilizar com um gasoduto. O caminho para o qual a saída CSV é escrita é gerado por um `OutputFileDatasetConfig` objeto. Os recursos preparados anteriormente, como `ComputeTarget` o , o , e os são `RunConfig` `'titanic_ds' Dataset` usados para completar a especificação.

```python
from azureml.data import OutputFileDatasetConfig
from azureml.pipeline.steps import PythonScriptStep

prepped_data_path = OutputFileDatasetConfig(name="output_path")

dataprep_step = PythonScriptStep(
    name="dataprep", 
    script_name="dataprep.py", 
    compute_target=compute_target, 
    runconfig=aml_run_config,
    arguments=["--output_path", prepped_data_path],
    inputs=[titanic_ds.as_named_input('titanic_ds')],
    allow_reuse=True
)
```

O `prepped_data_path` objeto é do tipo que aponta para um `OutputFileDatasetConfig` diretório.  Note que está especificado no `arguments` parâmetro. Se rever o passo anterior, verá que dentro do código de preparação de dados, o valor do argumento é o caminho do `'--output_path'` diretório em que o ficheiro CSV foi escrito. 

## <a name="train-with-automlstep"></a>Comboio com AutoMLStep

Configurar um passo de gasoduto ML automatizado é feito com a `AutoMLConfig` classe. Esta classe flexível é descrita em [experiências de ML automatizadas Configure em Python](./how-to-configure-auto-train.md). A entrada e saída de dados são os únicos aspetos da configuração que requerem uma atenção especial num pipeline ML. A entrada e a saída para `AutoMLConfig` os oleodutos são discutidas em pormenor abaixo. Além dos dados, uma vantagem dos oleodutos ML é a capacidade de usar diferentes alvos de computação para diferentes etapas. Pode optar por utilizar um processo mais poderoso `ComputeTarget` apenas para o processo ML automatizado. Fazê-lo é tão simples como atribuir um mais poderoso `RunConfiguration` ao `AutoMLConfig` parâmetro do `run_configuration` objeto.

### <a name="send-data-to-automlstep"></a>Enviar dados para `AutoMLStep`

Num gasoduto ML, os dados de entrada devem ser um `Dataset` objeto. A forma de maior desempenho é fornecer os dados de entrada sob a forma de `OutputTabularDatasetConfig` objetos. Cria-se um objeto desse tipo com o  `read_delimited_files()` num , como o , como o `OutputFileDatasetConfig` `prepped_data_path` `prepped_data_path` objeto.

```python
# type(prepped_data) == OutputTabularDatasetConfig
prepped_data = prepped_data_path.read_delimited_files()
```

Outra opção é utilizar `Dataset` objetos registados no espaço de trabalho:

```python
prepped_data = Dataset.get_by_name(ws, 'Data_prepared')
```

Comparando as duas técnicas:

| Técnica | Benefícios e inconvenientes | 
|-|-|
|`OutputTabularDatasetConfig`| Maior desempenho | 
|| Rota natural de `OutputFileDatasetConfig` | 
|| Os dados não persistem após a execução do gasoduto |
|| [Técnica de exibição de caderno `OutputTabularDatasetConfig`](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/nyc-taxi-data-regression-model-building/nyc-taxi-data-regression-model-building.ipynb) |
| Registado `Dataset` | Menor desempenho |
| | Pode ser gerado de muitas maneiras | 
| | Os dados persistem e são visíveis em todo o espaço de trabalho |
| | [Caderno mostrando técnica registada `Dataset`](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/continuous-retraining/auto-ml-continuous-retraining.ipynb)


### <a name="specify-automated-ml-outputs"></a>Especificar saídas de ML automatizadas

As saídas do `AutoMLStep` são as pontuações métricas finais do modelo de maior desempenho e do próprio modelo. Para utilizar estas saídas em etapas adicionais de gasoduto, prepare `OutputFileDatasetConfig` os objetos para recebê-los.

```python
from azureml.pipeline.core import TrainingOutput

metrics_data = PipelineData(name='metrics_data',
                            datastore=datastore,
                            pipeline_output_name='metrics_output',
                            training_output=TrainingOutput(type='Metrics'))

model_data = PipelineData(name='best_model_data',
                          datastore=datastore,
                          pipeline_output_name='model_output',
                          training_output=TrainingOutput(type='Model'))
```

O corte acima cria os dois `PipelineData` objetos para as métricas e saída do modelo. Cada um é nomeado, atribuído à loja de dados predefinida recuperada anteriormente, e associada com a `type` particularidade `TrainingOutput` do `AutoMLStep` . Como atribuímos `pipeline_output_name` a estes `PipelineData` objetos, os seus valores estarão disponíveis não só a partir do passo do gasoduto individual, mas do gasoduto como um todo, como será discutido abaixo na secção "Examinar os resultados do gasoduto". 

### <a name="configure-and-create-the-automated-ml-pipeline-step"></a>Configure e crie o passo automatizado do gasoduto ML

Uma vez definidas as entradas e saídas, é hora de criar o `AutoMLConfig` e `AutoMLStep` . Os detalhes da configuração dependerão da sua tarefa, conforme descrito em [experiências ML automatizadas de Configure em Python](./how-to-configure-auto-train.md). Para a tarefa de classificação de sobrevivência titânica, o seguinte corte demonstra uma configuração simples.

```python
from azureml.train.automl import AutoMLConfig
from azureml.pipeline.steps import AutoMLStep

# Change iterations to a reasonable number (50) to get better accuracy
automl_settings = {
    "iteration_timeout_minutes" : 10,
    "iterations" : 2,
    "experiment_timeout_hours" : 0.25,
    "primary_metric" : 'AUC_weighted'
}

automl_config = AutoMLConfig(task = 'classification',
                             path = '.',
                             debug_log = 'automated_ml_errors.log',
                             compute_target = compute_target,
                             run_configuration = aml_run_config,
                             featurization = 'auto',
                             training_data = prepped_data,
                             label_column_name = 'Survived',
                             **automl_settings)

train_step = AutoMLStep(name='AutoML_Classification',
    automl_config=automl_config,
    passthru_automl_config=False,
    outputs=[metrics_data,model_data],
    enable_default_model_output=False,
    enable_default_metrics_output=False,
    allow_reuse=True)
```
O snippet mostra uma idioma comumente usada com `AutoMLConfig` . Os argumentos mais fluidos (hiperparameter-ish) são especificados num dicionário separado, enquanto os valores menos propensos a alterar são especificados diretamente no `AutoMLConfig` construtor. Neste caso, `automl_settings` a especificação de uma breve execução: a corrida para após apenas 2 iterações ou 15 minutos, o que vier primeiro.

O `automl_settings` dicionário é passado para o construtor como `AutoMLConfig` kwargs. Os outros parâmetros não são complexos:

- `task` é definido `classification` para este exemplo. Outros valores válidos são `regression` e `forecasting`
- `path` e `debug_log` descrever o caminho para o projeto e um arquivo local para o qual a informação de depurar será escrita 
- `compute_target` é o previamente definido `compute_target` que, neste exemplo, é uma máquina barata baseada em CPU. Se estiver a utilizar as instalações de Deep Learning da AutoML, gostaria de alterar o objetivo de computação para ser baseado em GPU
- `featurization` está definido para `auto` . Mais detalhes podem ser encontrados na secção de Exibição de [Dados](./how-to-configure-auto-train.md#data-featurization) do documento de configuração automatizado de ML 
- `label_column_name` indica que coluna estamos interessados em prever 
- `training_data` é definido para os `OutputTabularDatasetConfig` objetos feitos a partir das saídas do passo de preparação de dados 

O `AutoMLStep` próprio toma e `AutoMLConfig` tem, como saídas, os `PipelineData` objetos criados para segurar as métricas e os dados do modelo. 

>[!Important]
> Tem de definir `enable_default_model_output` e só se estiver a utilizar `enable_default_metrics_output` `True`  `AutoMLStepRun` .

Neste exemplo, o processo automatizado de ML realizará validações cruzadas no `training_data` . Pode controlar o número de validações cruzadas com o `n_cross_validations` argumento. Se já dividiu os seus dados de treino como parte das suas etapas de preparação de dados, pode definir `validation_data` para os seus próprios `Dataset` .

Pode ocasionalmente ver a utilização `X` para funcionalidades de dados e `y` para etiquetas de dados. Esta técnica é depreciada e deve ser utilizada `training_data` para a entrada. 

## <a name="register-the-model-generated-by-automated-ml"></a>Registar o modelo gerado por ML automatizado 

O último passo num simples gasoduto ML é registar o modelo criado. Ao adicionar o modelo ao registo de modelos do espaço de trabalho, estará disponível no portal e poderá ser lançado. Para registar o modelo, escreva outro `PythonScriptStep` que tire a saída do `model_data` `AutoMLStep` .

### <a name="write-the-code-to-register-the-model"></a>Escreva o código para registar o modelo

Um modelo está registado num `Workspace` . Provavelmente está familiarizado com o uso `Workspace.from_config()` para iniciar sessão no seu espaço de trabalho na sua máquina local, mas há outra maneira de obter o espaço de trabalho dentro de um oleoduto ML em funcionamento. O `Run.get_context()` recupera o `Run` ativo. Este `run` objeto fornece acesso a muitos objetos importantes, incluindo o usado `Workspace` aqui.

```python
%%writefile register_model.py
from azureml.core.model import Model, Dataset
from azureml.core.run import Run, _OfflineRun
from azureml.core import Workspace
import argparse

parser = argparse.ArgumentParser()
parser.add_argument("--model_name", required=True)
parser.add_argument("--model_path", required=True)
args = parser.parse_args()

print(f"model_name : {args.model_name}")
print(f"model_path: {args.model_path}")

run = Run.get_context()
ws = Workspace.from_config() if type(run) == _OfflineRun else run.experiment.workspace

model = Model.register(workspace=ws,
                       model_path=args.model_path,
                       model_name=args.model_name)

print("Registered version {0} of model {1}".format(model.version, model.name))
```

### <a name="write-the-pythonscriptstep-code"></a>Escreva o código PythonScriptStep

O modelo-registramento `PythonScriptStep` usa um para um dos seus `PipelineParameter` argumentos. Os parâmetros do gasoduto são argumentos para os gasodutos que podem ser facilmente definidos no tempo de execução. Uma vez declarados, são aprovados como argumentos normais. 

```python

from azureml.pipeline.core.graph import PipelineParameter

# The model name with which to register the trained model in the workspace.
model_name = PipelineParameter("model_name", default_value="TitanicSurvivalInitial")

register_step = PythonScriptStep(script_name="register_model.py",
                                       name="register_model",
                                       allow_reuse=False,
                                       arguments=["--model_name", model_name, "--model_path", model_data],
                                       inputs=[model_data],
                                       compute_target=compute_target,
                                       runconfig=aml_run_config)
```

## <a name="create-and-run-your-automated-ml-pipeline"></a>Crie e execute o seu pipeline ML automatizado

Criar e executar um oleoduto que contenha um `AutoMLStep` não é diferente de um gasoduto normal. 

```python
from azureml.pipeline.core import Pipeline
from azureml.core import Experiment

pipeline = Pipeline(ws, [dataprep_step, train_step, register_step])

experiment = Experiment(workspace=ws, name='titanic_automl')

run = experiment.submit(pipeline, show_output=True)
run.wait_for_completion()
```

O código acima combina a preparação de dados, ml automatizado e passos de registo de modelos em um `Pipeline` objeto. Em seguida, cria um `Experiment` objeto. O `Experiment` construtor recuperará a experiência nomeada se existir ou a criará se necessário. Submete-se `Pipeline` ao `Experiment` , criando um `Run` objeto que vai assíncroneamente executar o oleoduto. A `wait_for_completion()` função bloqueia até que a execução esteja concluída.

### <a name="examine-pipeline-results"></a>Examinar os resultados do gasoduto 

Uma vez `run` concluídos, pode recuperar `PipelineData` objetos que lhes tenham sido atribuídos `pipeline_output_name` um . Pode descarregar os resultados e carregá-los para posterior processamento.  

```python
metrics_output_port = run.get_pipeline_output('metrics_output')
model_output_port = run.get_pipeline_output('model_output')

metrics_output_port.download('.', show_progress=True)
model_output_port.download('.', show_progress=True)
```

Os ficheiros descarregados são escritos para o subdiretório `azureml/{run.id}/` . O ficheiro métrico é formatado por JSON e pode ser convertido num dataframe pandas para exame.

Para o processamento local, poderá ser necessário instalar pacotes relevantes, como pandas, pickles, AzureML SDK, etc. Para este exemplo, é provável que o melhor modelo encontrado pelo ML automatizado dependa do XGBoost.

```bash
!pip install xgboost==0.90
```

```python
import pandas as pd
import json

metrics_filename = metrics_output._path_on_datastore
# metrics_filename = path to downloaded file
with open(metrics_filename) as f:
   metrics_output_result = f.read()
   
deserialized_metrics_output = json.loads(metrics_output_result)
df = pd.DataFrame(deserialized_metrics_output)
df
```

O corte de código acima mostra o ficheiro de métricas a ser carregado a partir da sua localização na loja de dados Azure. Também pode carregá-lo a partir do ficheiro descarregado, como mostrado no comentário. Uma vez deserrializado e convertido num DataFrame pandas, pode ver métricas detalhadas para cada uma das iterações do passo ML automatizado.

O ficheiro do modelo pode ser desseeralizado num `Model` objeto que pode usar para inferenizar, análise de métricas adicionais, etc. 

```python
import pickle

model_filename = model_output._path_on_datastore
# model_filename = path to downloaded file

with open(model_filename, "rb" ) as f:
    best_model = pickle.load(f)

# ... inferencing code not shown ...
```

Para obter mais informações sobre o carregamento e o trabalho com os modelos existentes, consulte [Utilizar um modelo existente com Azure Machine Learning](how-to-deploy-existing-model.md).

### <a name="download-the-results-of-an-automated-ml-run"></a>Descarregue os resultados de uma corrida automática de ML

Se estiver a seguir o artigo, terá um objeto `run` instantâneo. Mas também pode recuperar `Run` objetos completos a partir de `Workspace` um `Experiment` objeto.

O espaço de trabalho contém um registo completo de todas as suas experiências e execuções. Pode utilizar o portal para encontrar e descarregar as saídas de experiências ou usar código. Para aceder aos registos de uma corrida histórica, use a Azure Machine Learning para encontrar o ID da corrida em que está interessado. Com esse ID, pode escolher o específico `run` através do `Workspace` `Experiment` e.

```python
# Retrieved from Azure Machine Learning web UI
run_id = 'aaaaaaaa-bbbb-cccc-dddd-0123456789AB'
experiment = ws.experiments['titanic_automl']
run = next(run for run in ex.get_runs() if run.id == run_id)
```

Teria que mudar as cordas do código acima para as especificidades da sua execução histórica. O corte acima assume que você atribuiu `ws` ao relevante com o normal `Workspace` `from_config()` . A experiência de interesse é diretamente recuperada e, em seguida, o código encontra o `Run` interesse correspondendo ao `run.id` valor.

Uma vez que tenha um `Run` objeto, pode baixar as métricas e o modelo. 

```python
automl_run = next(r for r in run.get_children() if r.name == 'AutoML_Classification')
outputs = automl_run.get_outputs()
metrics = outputs['default_metrics_AutoML_Classification']
model = outputs['default_model_AutoML_Classification']

metrics.get_port_data_reference().download('.')
model.get_port_data_reference().download('.')
```

Cada `Run` objeto contém `StepRun` objetos que contêm informações sobre o passo do gasoduto individual. `run`Procura-se o `StepRun` objeto `AutoMLStep` para. As métricas e o modelo são recuperados usando os seus nomes predefinidos, que estão disponíveis mesmo que não passe `PipelineData` objetos para o `outputs` parâmetro do `AutoMLStep` . 

Finalmente, as métricas e o modelo reais são descarregados para a sua máquina local, como foi discutido na secção "Examine os resultados do pipeline" acima.

## <a name="next-steps"></a>Passos Seguintes

- Executar este caderno Jupyter mostrando um [exemplo completo de ML automatizado em um oleoduto](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/nyc-taxi-data-regression-model-building/nyc-taxi-data-regression-model-building.ipynb) que usa regressão para prever tarifas de táxi
- [Criar experiências automatizadas de ML sem código de escrita](how-to-use-automated-ml-for-ml-models.md)
- Explore uma variedade de [cadernos Jupyter demonstrando ML automatizado](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)
- Leia sobre a integração do seu oleoduto em [MLOps de ponta a ponta](./concept-model-management-and-deployment.md#automate-the-ml-lifecycle) ou investigue o [repositório MLOps GitHub](https://github.com/Microsoft/MLOpspython)
