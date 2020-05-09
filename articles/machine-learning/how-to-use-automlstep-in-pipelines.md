---
title: Utilizar ML automatizado em gasodutos ML
titleSuffix: Azure Machine Learning
description: O AutoMLStep permite-lhe utilizar machine learning automatizado nos seus pipelines.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
manager: cgronlun
ms.date: 04/28/2020
ms.openlocfilehash: 9bf17512d0b14c7106101d98598e2914020afc7a
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82857950"
---
# <a name="use-automated-ml-in-an-azure-machine-learning-pipeline-in-python"></a>Utilize ML automatizado num oleoduto azure de machine learning em Python
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

A capacidade automática de ML da Azure Machine Learning ajuda-o a descobrir modelos de alto desempenho sem que realimplemente todas as abordagens possíveis. Combinado com os oleodutos Azure Machine Learning, pode criar fluxos de trabalho implantáveis que podem rapidamente descobrir o algoritmo que funciona melhor para os seus dados. Este artigo irá mostrar-lhe como aderir eficientemente a um passo de preparação de dados para um passo ml automatizado. Ml automatizado pode rapidamente descobrir o algoritmo que funciona melhor para os seus dados, ao mesmo tempo que o coloca na estrada para MLOps e modelar a operacionalização do ciclo de vida com os oleodutos.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma conta gratuita antes de começar. Experimente hoje a [versão gratuita ou paga do Azure Machine Learning.](https://aka.ms/AMLFree)

* Uma área de trabalho do Azure Machine Learning. Consulte Criar um espaço de [trabalho de aprendizagem automática Azure](how-to-manage-workspace.md).  

* Familiaridade básica com as instalações [automatizadas](concept-automated-ml.md) de machine learning e [machine learning](concept-ml-pipelines.md) do Azure e sdK.

## <a name="review-automated-mls-central-classes"></a>Rever as classes centrais automatizadas do ML

Ml automatizado num oleoduto é `AutoMLStep` representado por um objeto. A `AutoMLStep` classe é uma `PipelineStep`subclasse de. Um gráfico `PipelineStep` de objetos define um `Pipeline`.

Há várias subclasses de `PipelineStep`. Além do `AutoMLStep`, este artigo `PythonScriptStep` mostrará um para preparação de dados e outro para o registo do modelo.

A forma preferida de _into_ mover os dados `Dataset` inicialmente para um oleoduto ML é com objetos. Para mover dados _entre_ passos, `PipelineData` o caminho preferido é com objetos. Para ser `AutoMLStep`utilizado, `PipelineData` o objeto deve `PipelineOutputTabularDataset` ser transformado num objeto. Para mais informações, consulte [os dados de entrada e saída dos gasodutos ML](how-to-move-data-in-out-of-pipelines.md).

O `AutoMLStep` é configurado `AutoMLConfig` através de um objeto. `AutoMLConfig`é uma classe flexível, como discutido em [Configure experiências automatizadas de ML em Python](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train#configure-your-experiment-settings). 

Uma `Pipeline` corrida `Experiment`em um . O `Run` oleoduto tem, para cada `StepRun`passo, uma criança. As saídas do `StepRun` ML automatizado são as métricas de treino e o modelo de maior desempenho.

Para tornar as coisas concretas, este artigo cria um simples oleoduto para uma tarefa de classificação. A tarefa é prever a sobrevivência do Titanic, mas não discutiremos os dados ou a tarefa a não ser de passagem.

## <a name="get-started"></a>Introdução

### <a name="retrieve-initial-dataset"></a>Recuperar o conjunto de dados inicial

Muitas vezes, um fluxo de trabalho ML começa com dados de base pré-existentes. Este é um bom cenário para um conjunto de dados registado. Os conjuntos de dados são visíveis em todo o espaço de trabalho, versão de suporte e podem ser explorados interativamente. Existem muitas formas de criar e povoar um conjunto de dados, como discutido nos conjuntos de [dados Create Azure Machine Learning.](how-to-create-register-datasets.md) Uma vez que vamos usar o Python SDK para criar o nosso pipeline, use o SDK para descarregar dados de base e registá-lo com o nome 'titanic_ds'.

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

O código entra primeiro no espaço de trabalho Azure Machine Learning definido em **config.json** (para uma explicação, consulte [Tutorial: Começar a criar a sua primeira experiência ML com o Python SDK).](tutorial-1st-experiment-sdk-setup.md) Se já não há um `'titanic_ds'` conjunto de dados nomeado registado, então cria um. O código descarrega dados CSV da Web, `TabularDataset` utiliza-os para instantaneamente um e, em seguida, regista o conjunto de dados com o espaço de trabalho. Finalmente, a `Dataset.get_by_name()` função `Dataset` `titanic_ds`atribui a . 

### <a name="configure-your-storage-and-compute-target"></a>Configure o seu alvo de armazenamento e cálculo

Os recursos adicionais que o gasoduto necessitará são de armazenamento e, em geral, os recursos computacionais da Azure Machine Learning. 

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

Os dados intermédios entre a preparação de dados e a etapa automática de ML podem ser armazenados na `get_default_datastore()` loja `Workspace` de dados padrão do espaço de trabalho, pelo que não precisamos de fazer mais do que chamar o objeto. 

Depois disso, o código verifica se `'cpu-cluster'` o objetivo de computação AML já existe. Caso contrário, especificamos que queremos um pequeno alvo de computação baseado em CPU. Se planeia utilizar as funcionalidades de aprendizagem profunda automatizadas do ML (por exemplo, a funcionalidade de texto com suporte dNN) deve escolher uma computação com forte suporte de GPU, conforme descrito em tamanhos de [máquina virtual otimizados](https://docs.microsoft.com/azure/virtual-machines/sizes-gpu)de GPU . 

O código bloqueia até que o alvo seja provisionado e, em seguida, imprima alguns detalhes do alvo computacional recém-criado. Finalmente, o alvo computacional nomeado é recuperado do `compute_target`espaço de trabalho e atribuído a . 

### <a name="configure-the-training-run"></a>Configure o treino

O próximo passo é garantir que o treino remoto tem todas as dependências que são necessárias pelas etapas de treino. As dependências e o contexto de tempo `RunConfiguration` de execução são definidos através da criação e configuração de um objeto. 

```python
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies

aml_run_config = RunConfiguration()
# Use just-specified compute target ("cpu-cluster")
aml_run_config.target = compute_target
aml_run_config.environment.python.user_managed_dependencies = False

# Add some packages relied on by data prep step
aml_run_config.environment.python.conda_dependencies = CondaDependencies.create(
    conda_packages=['pandas','scikit-learn'], 
    pip_packages=['azureml-sdk[automl,explain]', 'azureml-dataprep[fuse,pandas]'], 
    pin_sdk_version=False)
```

## <a name="prepare-data-for-automated-machine-learning"></a>Preparar dados para aprendizagem automática de máquinas

### <a name="write-the-data-preparation-code"></a>Escreva o código de preparação de dados

O conjunto de dados do Titanic de base consiste em dados numéricos e de texto mistos, faltando alguns valores. Para prepará-lo para aprendizagem automática de máquinas, o passo do gasoduto de preparação de dados:

- Preencha os dados em falta com dados aleatórios ou uma categoria correspondente a "Desconhecido"
- Transformar dados categóricos em inteiros
- Largue colunas que não pretendemos usar
- Divida os dados em conjuntos de treino e teste
- Escreva os dados `PipelineData` transformados para os caminhos de saída

```python
%%writefile dataprep.py
from azureml.core import Run

import pandas as pd 
import numpy as np 
import pyarrow as pa
import pyarrow.parquet as pq
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

os.makedirs(os.path.dirname(args.output_path), exist_ok=True)
pq.write_table(pa.Table.from_pandas(df), args.output_path)

print(f"Wrote test to {args.output_path} and train to {args.output_path}")
```

O código acima é um exemplo completo, mas mínimo, de preparação de dados para os dados do Titanic. O corte começa com um "comando mágico" jupyter para forrelá-lo para um ficheiro. Se não estiver a usar um caderno Jupyter, retire essa linha e crie o ficheiro manualmente.

As `prepare_` várias funções no corte acima modificam a coluna relevante no conjunto de dados de entrada. Estas funções funcionam nos dados uma vez que `DataFrame` foram transformados num objeto Pandas. Em cada caso, os dados em falta são preenchidos com dados aleatórios representativos ou dados categóricos que indicam "Desconhecido". Os dados categóricos baseados em texto são mapeados para inteiros. As colunas já não necessárias são substituídas ou largadas. 

Depois de o código definir as funções de preparação de dados, o código analisa o argumento de entrada, que é o caminho para o qual queremos escrever os nossos dados. (Estes valores serão determinados por `PipelineData` objetos que serão discutidos no próximo passo.) O código recupera `'titanic_cs'` `Dataset`o registado, converte-o para um Pandas, `DataFrame`e chama as várias funções de preparação de dados. 

Uma `output_path` vez que a `os.makedirs()` função é totalmente qualificada, a função é utilizada para preparar a estrutura do diretório. Neste ponto, pode `DataFrame.to_csv()` utilizar para escrever os dados de saída, mas os ficheiros Parquet são mais eficientes. Esta eficiência seria provavelmente irrelevante com um conjunto de dados tão `from_pandas()` `write_table()` pequeno, mas usar as funções do pacote `to_csv()` **PyArrow** são apenas mais algumas teclas do que .

Os ficheiros Parquet são apoiados de forma nativa pela etapa automática ml discutida abaixo, pelo que não é necessário um processamento especial para os consumir. 

### <a name="write-the-data-preparation-pipeline-step-pythonscriptstep"></a>Escreva o passo`PythonScriptStep`do gasoduto de preparação de dados ( )

O código de preparação de dados `PythonScripStep` acima descrito deve estar associado a um objeto a utilizar com um gasoduto. O caminho para o qual a saída de preparação `PipelineData` de dados parquet é escrita é gerado por um objeto. Os recursos preparados anteriormente, `RunConfig`tais como `'titanic_ds' Dataset` o, `ComputeTarget`o , e os são usados para completar a especificação.

```python
from azureml.pipeline.core import PipelineData
from azureml.pipeline.steps import PythonScriptStep

prepped_data_path = PipelineData("titanic_train", datastore).as_dataset()
prepped_data_path = PipelineData("titanic_train", datastore).as_dataset()

dataprep_step = PythonScriptStep(
    name="dataprep", 
    script_name="dataprep.py", 
    compute_target=compute_target, 
    runconfig=aml_run_config,
    arguments=["--output_path", prepped_data_path],
    inputs=[titanic_ds.as_named_input("titanic_ds")],
    outputs=[prepped_data_path],
    allow_reuse=True
)
```

O `prepped_data_path` objeto é `PipelineOutputFileDataset`de tipo. Note que é especificado `arguments` tanto `outputs` nos argumentos como nos argumentos. Se rever o passo anterior, verá que dentro do código de `'--output_path'` preparação de dados, o valor do argumento é o caminho do ficheiro para o qual o ficheiro Parquet foi escrito. 

## <a name="train-with-automlstep"></a>Comboio com AutoMLStep

Configurar um passo de pipeline `AutoMLConfig` ML automatizado é feito com a classe. Esta classe flexível é descrita em [experiências automatizadas](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train)de CONFIGURE ML em Python . A entrada e a saída de dados são os únicos aspetos de configuração que requerem uma atenção especial num gasoduto ML. A entrada e `AutoMLConfig` a saída para os gasodutos são discutidas em pormenor abaixo. Além dos dados, uma vantagem dos oleodutos ML é a capacidade de usar diferentes alvos computacionais para diferentes passos. Pode optar por utilizar `ComputeTarget` um processo ml mais potente apenas para o processo ml automatizado. Fazê-lo é tão simples como `RunConfiguration` atribuir `AutoMLConfig` um `run_configuration` mais poderoso ao parâmetro do objeto.

### <a name="send-data-to-automlstep"></a>Enviar dados para`AutoMLStep`

Num gasoduto ML, os dados de `Dataset` entrada devem ser um objeto. A forma de mais desempenho é fornecer os `PipelineOutputTabularDataset` dados de entrada sob a forma de objetos. Cria-se um objeto desse `parse_parquet_files()` `parse_delimited_files()` tipo `PipelineOutputFileDataset`com ou `prepped_data_path` em a, como o objeto.

```python
# type(prepped_data_path) == PipelineOutputFileDataset
# type(prepped_data) == PipelineOutputTabularDataset
prepped_data = prepped_data_path.parse_parquet_files(file_extension=None)
```

O corte acima cria um `PipelineOutputTabularDataset` alto `PipelineOutputFileDataset` desempenho a partir da saída do passo de preparação de dados.

Outra opção `Dataset` é utilizar objetos registados no espaço de trabalho:

```python
prepped_data = Dataset.get_by_name(ws, 'Data_prepared')
```

Comparando as duas técnicas:

| Técnica |  | 
|-|-|
|`PipelineOutputTabularDataset`| Maior desempenho | 
|| Rota natural de`PipelineData` | 
|| Os dados não são persistidos após a execução do gasoduto |
|| [Técnica `PipelineOutputTabularDataset` de exibição de caderno](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/nyc-taxi-data-regression-model-building/nyc-taxi-data-regression-model-building.ipynb) |
| Registado`Dataset` | Menor desempenho |
| | Pode ser gerado de muitas maneiras | 
| | Os dados persistem e são visíveis em todo o espaço de trabalho |
| | [Caderno mostrando técnica registada `Dataset`](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/continuous-retraining/auto-ml-continuous-retraining.ipynb)

### <a name="specify-automated-ml-outputs"></a>Especificar saídas automáticas de ML

As saídas `AutoMLStep` do são as pontuações métricas finais do modelo de maior desempenho e desse próprio modelo. Para utilizar estas saídas em `PipelineData` etapas adicionais do gasoduto, prepare objetos para recebê-los.

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

O corte acima cria `PipelineData` os dois objetos para as métricas e a saída do modelo. Cada um é nomeado, atribuído à loja de dados predefinida `type` recuperada anteriormente, e associado com o particular do `TrainingOutput` `AutoMLStep`. Como `pipeline_output_name` atribuímos `PipelineData` estes objetos, os seus valores estarão disponíveis não só a partir do passo do gasoduto individual, mas do oleoduto como um todo, como será discutido abaixo na secção "Examinar os resultados do gasoduto". 

### <a name="configure-and-create-the-automated-ml-pipeline-step"></a>Configure e crie o passo automatizado do gasoduto ML

Uma vez definidas as inputs e saídas, `AutoMLConfig` `AutoMLStep`é hora de criar o e . Os detalhes da configuração dependerão da sua tarefa, conforme descrito em [Configure experiências ml automatizadas em Python](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train). Para a tarefa de classificação de sobrevivência do Titanic, o seguinte corte demonstra uma configuração simples.

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
    allow_reuse=True)
```
O corte mostra uma idioma `AutoMLConfig`comumente usada com . Os argumentos mais fluidos (hiperparâmetros)são especificados num dicionário separado, enquanto os valores menos `AutoMLConfig` propensos a mudar são especificados diretamente no construtor. Neste caso, `automl_settings` o especificar uma breve execução: a corrida para após apenas 2 iterações ou 15 minutos, o que vier primeiro.

O `automl_settings` dicionário é passado `AutoMLConfig` para o construtor como kwargs. Os outros parâmetros não são complexos:

- `task`é definido `classification` para este exemplo. Outros valores `regression` válidos são e`forecasting`
- `path`e `debug_log` descrever o caminho para o projeto e um arquivo local para o qual informações depuração serão escritas 
- `compute_target`é o previamente definido `compute_target` que, neste exemplo, é uma máquina baseada em CPU barata. Se estiver a usar as instalações de Deep Learning da AutoML, gostaria de alterar o alvo da computação para ser baseado em GPU
- `featurization`está definido `auto`para . Mais detalhes podem ser encontrados na secção de Funcionalidade de [Dados](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train#data-featurization) do documento automatizado de configuração ML 
- `training_data`é definido `PipelineOutputTabularDataset` para os objetos feitos a partir das saídas do passo de preparação de dados 
- `label_column_name`indica que coluna estamos interessados em prever 

O `AutoMLStep` próprio pega `AutoMLConfig` o e tem, `PipelineData` como saídas, os objetos criados para segurar as métricas e os dados do modelo. 

>[!Important]
> Tem de `passthru_automl_config` `False` se `AutoMLStep` definir `PipelineOutputTabularDataset` se estiver a utilizar objetos para entrada.

Neste exemplo, o processo ml automatizado realizará `training_data`validações cruzadas na . Pode controlar o número de validações `n_cross_validations` cruzadas com o argumento. Se já dividiu os seus dados de treino como parte `validation_data` dos `Dataset`seus passos de preparação de dados, pode definir os seus próprios .

Pode ocasionalmente ver `X` a utilização de funcionalidades de dados e `y` de etiquetas de dados. Esta técnica é depreciada e `training_data` deve utilizar para a entrada. 

## <a name="register-the-model-generated-by-automated-ml"></a>Registe o modelo gerado por ML automatizado 

O último passo de um gasoduto ML básico é registar o modelo criado. Ao adicionar o modelo ao registo de modelos do espaço de trabalho, estará disponível no portal e poderá ser versão. Para registar o modelo, escreva outro `PythonScriptStep` que leve a `model_data` saída do `AutoMLStep`.

### <a name="write-the-code-to-register-the-model"></a>Escreva o código para registar o modelo

Um modelo está `Workspace`registado em . Você provavelmente está `Workspace.from_config()` familiarizado com o uso para iniciar sessão no seu espaço de trabalho na sua máquina local, mas há outra maneira de obter o espaço de trabalho dentro de um oleoduto ML em execução. O `Run.get_context()` que recupera `Run`o ativo. Este `run` objeto fornece acesso a muitos `Workspace` objetos importantes, incluindo o usado aqui.

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

O registo de `PythonScriptStep` modelos utiliza um `PipelineParameter` dos seus argumentos. Os parâmetros do gasoduto são argumentos para gasodutos que podem ser facilmente definidos no tempo de submissão de execução. Uma vez declarados, são aprovados como argumentos normais. 

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

Criar e executar um `AutoMLStep` oleoduto que contenha um não é diferente de um oleoduto normal. 

```python
from azureml.pipeline.core import Pipeline
from azureml.core import Experiment

pipeline = Pipeline(ws, [dataprep_step, train_step, register_step])

experiment = Experiment(workspace=ws, name='titanic_automl')

run = experiment.submit(pipeline, show_output=True)
run.wait_for_completion()
```

O código acima combina a preparação de dados, ml `Pipeline` automatizado e os passos de registo de modelos num objeto. Cria então `Experiment` um objeto. O `Experiment` construtor recuperará a experiência nomeada se ela existir ou criá-la se necessário. Submete-se `Pipeline` ao `Experiment`, criando `Run` um objeto que irá sincronicamente executar o oleoduto. A `wait_for_completion()` função bloqueia até que a execução esteja completa.

### <a name="examine-pipeline-results"></a>Examinar os resultados do gasoduto 

Uma `run` vez concluído, pode `PipelineData` recuperar objetos que `pipeline_output_name`tenham sido atribuídos a . Pode descarregar os resultados e carregá-los para posterior processamento.  

```python
metrics_output_port = run.get_pipeline_output('metrics_output')
model_output_port = run.get_pipeline_output('model_output')

metrics_output_port.download('.', show_progress=True)
model_output_port.download('.', show_progress=True)
```

Os ficheiros descarregados são escritos para o subdiretório `azureml/{run.id}/`. O ficheiro de métricas é formado por JSON e pode ser convertido num quadro de dados pandas para exame.

Para processamento local, poderá ser necessário instalar pacotes relevantes, como Pandas, Pickle, o AzureML SDK, etc. Para este exemplo, é provável que o melhor modelo encontrado por ML automatizado dependa do XGBoost.

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

O código acima mostra que o ficheiro de métricas está a ser carregado a partir da sua localização na loja de dados Azure. Também pode carregá-lo a partir do ficheiro descarregado, como mostra o comentário. Uma vez desserializado e convertido num Pandas DataFrame, pode ver métricas detalhadas para cada uma das iterações do passo ML automatizado.

O ficheiro modelo pode ser `Model` desserializado num objeto que pode utilizar para inferência, análise de métricas adicionais, etc. 

```python
import pickle

model_filename = model_output._path_on_datastore
# model_filename = path to downloaded file

with open(model_filename, "rb" ) as f:
    best_model = pickle.load(f)

# ... inferencing code not shown ...
```

Para obter mais informações sobre o carregamento e o trabalho com os modelos existentes, consulte [Utilize um modelo existente com o Azure Machine Learning.](how-to-deploy-existing-model.md)

### <a name="download-the-results-of-an-automated-ml-run"></a>Descarregue os resultados de uma execução automática de ML

Se tem acompanhado o artigo, terá um `run` objeto instantâneo. Mas também pode recuperar `Run` objetos `Workspace` completos `Experiment` a partir de um objeto.

O espaço de trabalho contém um registo completo de todas as suas experiências e execuções. Pode utilizar o portal para encontrar e descarregar as saídas de experiências ou usar código. Para aceder aos registos de uma corrida histórica, utilize o Azure Machine Learning para encontrar a identificação da corrida em que está interessado. Com essa identificação, pode `run` escolher o `Workspace` `Experiment`específico através do e .

```python
# Retrieved from Azure Machine Learning web UI
run_id = 'aaaaaaaa-bbbb-cccc-dddd-0123456789AB'
experiment = ws.experiments['titanic_automl']
run = next(run for run in ex.get_runs() if run.id == run_id)
```

Teria de mudar as cordas do código acima para as especificidades da sua execução histórica. O corte acima assume que atribuiu `ws` ao relevante `Workspace` com `from_config()`o normal . A experiência de interesse é diretamente recuperada `Run` e, em `run.id` seguida, o código encontra o interesse correspondendo ao valor.

Uma vez `Run` que tenha um objeto, pode baixar as métricas e o modelo. 

```python
automl_run = next(r for r in run.get_children() if r.name == 'AutoML_Classification')
outputs = automl_run.get_outputs()
metrics = outputs['default_metrics_AutoML_Classification']
model = outputs['default_model_AutoML_Classification']

metrics.get_port_data_reference().download('.')
model.get_port_data_reference().download('.')
```

Cada `Run` objeto `StepRun` contém objetos que contêm informações sobre o passo do gasoduto individual. O `run` é procurado `StepRun` pelo objeto `AutoMLStep`para o . As métricas e o modelo são recuperados utilizando os seus nomes predefinidos, que estão disponíveis mesmo que não passem `PipelineData` objetos para o `outputs` parâmetro da . `AutoMLStep` 

Finalmente, as métricas e modelos reais são descarregados para a sua máquina local, como foi discutido na secção "Examinar os resultados do gasoduto" acima.

## <a name="next-steps"></a>Passos Seguintes

- Execute este caderno Jupyter mostrando um [exemplo completo de ML automatizado em um oleoduto](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/nyc-taxi-data-regression-model-building/nyc-taxi-data-regression-model-building.ipynb) que usa regressão para prever tarifas de táxi
- [Criar experiências ml automatizadas sem código de escrita](how-to-use-automated-ml-for-ml-models.md)
- Explore uma variedade de [cadernos Jupyter demonstrando ML automatizado](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)
- Leia sobre a integração do seu pipeline em [MLOps de ponta a ponta](https://docs.microsoft.com/azure/machine-learning/concept-model-management-and-deployment#automate-the-ml-lifecycle) ou investigue o [repositório MLOps GitHub](https://github.com/Microsoft/MLOpspython) 
