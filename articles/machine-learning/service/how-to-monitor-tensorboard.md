---
title: Visualizar experimentações com TensorBoard
titleSuffix: Azure Machine Learning
description: Inicie o TensorBoard para visualizar os históricos de execução de experimento e identificar áreas potenciais para ajuste e readaptação de hiperparâmetro.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: maxluk
ms.author: maxluk
ms.date: 06/28/2019
ms.openlocfilehash: a45548d3698d28a0189be4f46c26e418da8c91ef
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73489632"
---
# <a name="visualize-experiment-runs-and-metrics-with-tensorboard-and-azure-machine-learning"></a>Visualizar execuções e métricas de experimento com TensorBoard e Azure Machine Learning
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, você aprende a exibir suas execuções e métricas de experimento no TensorBoard usando [o pacote de `tensorboard`](https://docs.microsoft.com/python/api/azureml-tensorboard/?view=azure-ml-py) no SDK do Azure Machine Learning principal. Depois de inspecionar suas execuções de experimento, você pode ajustar e treinar novamente seus modelos de aprendizado de máquina.

O [TensorBoard](https://www.tensorflow.org/tensorboard/r1/overview) é um pacote de aplicativos Web para inspecionar e compreender sua estrutura e desempenho de experimentos.

A maneira como você inicia o TensorBoard com Azure Machine Learning experimentos depende do tipo de experimento:
+ Se seu experimento gera arquivos de log de forma nativa que são consumíveis pelo TensorBoard, como PyTorch, chainer e TensorFlow experimentos, você pode [iniciar o TensorBoard diretamente](#direct) do histórico de execução do experimento. 

+ Para experimentos que não geram de forma nativa arquivos de consumo TensorBoard, como como Scikit-Learn ou Azure Machine Learning experimentos, use [o método `export_to_tensorboard()`](#export) para exportar os históricos de execução como logs de TensorBoard e inicie o TensorBoard a partir daí. 

## <a name="prerequisites"></a>Pré-requisitos

* Para iniciar o TensorBoard e exibir seus históricos de execução de experimento, seus experimentos precisam ter o registro em log habilitado anteriormente para controlar suas métricas e desempenho.  

* O código neste "como" pode ser executado em qualquer um dos seguintes ambientes: 

    * Instância de computação de Azure Machine Learning-nenhum download ou instalação é necessário

        * Conclua o [tutorial: ambiente de instalação e espaço de trabalho](tutorial-1st-experiment-sdk-setup.md) para criar um servidor de notebook dedicado pré-carregado com o SDK e o repositório de exemplo.

        * Na pasta Samples no servidor do notebook, encontre dois blocos de anotações concluídos e expandidos navegando até este diretório: **instruções-uso-azureml > treinamento com aprendizado profundo**.
        * Export-execute-History-to-execute-History. ipynb
        * tensorboard. ipynb

    * Seu próprio servidor do Juptyer Notebook
          * [Instale o SDK do Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) com o `tensorboard` extra
          * [Crie um espaço de trabalho Azure Machine Learning](how-to-manage-workspace.md).  
          * [Crie um arquivo de configuração de espaço de trabalho](how-to-configure-environment.md#workspace).
  
<a name="direct"></a>
## <a name="option-1-directly-view-run-history-in-tensorboard"></a>Opção 1: exibir o histórico de execução diretamente no TensorBoard

Essa opção funciona para experimentos que geram nativamente arquivos de log consumíveis por TensorBoard, como experimentos PyTorch, chainer e TensorFlow. Se esse não for o caso do seu experimento, use [o método `export_to_tensorboard()`](#export) em vez disso.

O código de exemplo a seguir usa o [experimento de demonstração do MNIST](https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py) do repositório do TensorFlow em um destino de computação remota, Azure Machine Learning computação. Em seguida, treinamos nosso modelo com o [estimador de TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)personalizado do SDK e, em seguida, começamos a TensorBoard nesse experimento de TensorFlow, ou seja, um experimento que produza nativamente arquivos de evento TensorBoard.

### <a name="set-experiment-name-and-create-project-folder"></a>Definir o nome do experimento e criar a pasta do projeto

Aqui, nomeamos o experimento e criamos sua pasta. 
 
```python
from os import path, makedirs
experiment_name = 'tensorboard-demo'

# experiment folder
exp_dir = './sample_projects/' + experiment_name

if not path.exists(exp_dir):
    makedirs(exp_dir)

```

### <a name="download-tensorflow-demo-experiment-code"></a>Baixar o código de teste de demonstração do TensorFlow

O repositório de TensorFlow tem uma demonstração de MNIST com uma ampla Instrumentação de TensorBoard. Nós não, nem precisamos, altero o código desta demonstração para que ele funcione com Azure Machine Learning. No código a seguir, baixamos o código MNIST e o salvamos em nossa pasta experimento recém-criada.

```python
import requests
import os

tf_code = requests.get("https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py")
with open(os.path.join(exp_dir, "mnist_with_summaries.py"), "w") as file:
    file.write(tf_code.text)
```
Em todo o arquivo de código MNIST, mnist_with_summaries. py, observe que há linhas que chamam `tf.summary.scalar()`, `tf.summary.histogram()`, `tf.summary.FileWriter()`, etc. Esses métodos agrupam, registram e marcam métricas principais de suas experiências no histórico de execução. A `tf.summary.FileWriter()` é especialmente importante, pois ela serializa os dados de suas métricas de teste registradas, o que permite que o TensorBoard gere visualizações para eles.

 ### <a name="configure-experiment"></a>Configurar experimento

A seguir, configuramos nosso experimento e configuramos diretórios para logs e dados. Esses logs serão carregados no serviço de artefato, que TensorBoard será acessado mais tarde.

>[!Note]
> Para este exemplo de TensorFlow, será necessário instalar o TensorFlow no computador local. Além disso, o módulo TensorBoard (ou seja, aquele incluído com TensorFlow) deve ser acessível para o kernel desse notebook, pois o computador local é o que executa o TensorBoard.

```Python
import azureml.core
from azureml.core import Workspace
from azureml.core import Experiment

ws = Workspace.from_config()

# create directories for experiment logs and dataset
logs_dir = os.path.join(os.curdir, "logs")
data_dir = os.path.abspath(os.path.join(os.curdir, "mnist_data"))

if not path.exists(data_dir):
    makedirs(data_dir)

os.environ["TEST_TMPDIR"] = data_dir

# Writing logs to ./logs results in their being uploaded to Artifact Service,
# and thus, made accessible to our TensorBoard instance.
arguments_list = ["--log_dir", logs_dir]

# Create an experiment
exp = Experiment(ws, experiment_name)
```

### <a name="create-a-cluster-for-your-experiment"></a>Criar um cluster para o experimento
Criamos um cluster AmlCompute para esse experimento, no entanto, seus experimentos podem ser criados em qualquer ambiente e você ainda pode iniciar o TensorBoard em relação ao histórico de execução do experimento. 

```Python
from azureml.core.compute import ComputeTarget, AmlCompute

cluster_name = "cpucluster"

cts = ws.compute_targets
found = False
if cluster_name in cts and cts[cluster_name].type == 'AmlCompute':
   found = True
   print('Found existing compute target.')
   compute_target = cts[cluster_name]
if not found:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2', 
                                                           max_nodes=4)

    # create the cluster
    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

compute_target.wait_for_completion(show_output=True, min_node_count=None)

# use get_status() to get a detailed status for the current cluster. 
# print(compute_target.get_status().serialize())
```

### <a name="submit-run-with-tensorflow-estimator"></a>Enviar execução com o estimador TensorFlow

O estimador do TensorFlow fornece uma maneira simples de iniciar um trabalho de treinamento do TensorFlow em um destino de computação. Ele é implementado por meio da classe de [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) genérica, que pode ser usada para dar suporte a qualquer estrutura. Para obter mais informações sobre modelos de treinamento usando o estimador genérico, consulte [treinar modelos com Azure Machine Learning usando o estimador](how-to-train-ml-models.md)

```Python
from azureml.train.dnn import TensorFlow
script_params = {"--log_dir": "./logs"}

tf_estimator = TensorFlow(source_directory=exp_dir,
                          compute_target=compute_target,
                          entry_script='mnist_with_summaries.py',
                          script_params=script_params)

run = exp.submit(tf_estimator)
```

### <a name="launch-tensorboard"></a>Iniciar o TensorBoard

Você pode iniciar o TensorBoard durante a execução ou após a conclusão. A seguir, criamos uma instância do objeto TensorBoard, `tb`, que usa o histórico de execução do experimento carregado na `run`e, em seguida, inicia o TensorBoard com o método `start()`. 
  
O [Construtor TensorBoard](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py) usa uma matriz de execuções, portanto, certifique-se de passá-lo como uma matriz de elemento único.

```python
from azureml.tensorboard import Tensorboard

tb = Tensorboard([run])

# If successful, start() returns a string with the URI of the instance.
tb.start()

# After your job completes, be sure to stop() the streaming otherwise it will continue to run. 
tb.stop()
```

<a name="export"></a>

## <a name="option-2-export-history-as-log-to-view-in-tensorboard"></a>Opção 2: exportar o histórico como log para exibição no TensorBoard

O código a seguir configura um experimento de exemplo, inicia o processo de log usando as APIs de histórico de Azure Machine Learning de execução e exporta o histórico de execução do experimento para logs consumíveis pelo TensorBoard para visualização. 

### <a name="set-up-experiment"></a>Configurar experimento

O código a seguir configura um novo experimento e nomeia o diretório de execução `root_run`. 

```python
from azureml.core import Workspace, Experiment
import azuremml.core

# set experiment name and run name
ws = Workspace.from_config()
experiment_name = 'export-to-tensorboard'
exp = Experiment(ws, experiment_name)
root_run = exp.start_logging()
```

Aqui, carregamos o conjunto de diabetes, um pequeno conjunto de informações interno que vem com scikit-Learn e o dividimos em conjuntos de teste e treinamento.

```Python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
X, y = load_diabetes(return_X_y=True)
columns = ['age', 'gender', 'bmi', 'bp', 's1', 's2', 's3', 's4', 's5', 's6']
x_train, x_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=0)
data = {
    "train":{"x":x_train, "y":y_train},        
    "test":{"x":x_test, "y":y_test}
}
```

### <a name="run-experiment-and-log-metrics"></a>Executar métricas de teste e log

Para esse código, treinamos um modelo de regressão linear e métricas de chave de log, o coeficiente alfa, `alpha` e erro de quadrado médio, `mse`, no histórico de execuções.

```Python
from tqdm import tqdm
alphas = [.1, .2, .3, .4, .5, .6 , .7]
# try a bunch of alpha values in a Linear Regression (aka Ridge regression) mode
for alpha in tqdm(alphas):
  # create child runs and fit lines for the resulting models
  with root_run.child_run("alpha" + str(alpha)) as run
 
   reg = Ridge(alpha=alpha)
   reg.fit(data["train"]["x"], data["train"]["y"])    
 
   preds = reg.predict(data["test"]["x"])
   mse = mean_squared_error(preds, data["test"]["y"])
   # End train and eval

# log alpha, mean_squared_error and feature names in run history
   root_run.log("alpha", alpha)
   root_run.log("mse", mse)
```

### <a name="export-runs-to-tensorboard"></a>Exportar execuções para TensorBoard

Com o método [export_to_tensorboard ()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.export?view=azure-ml-py) do SDK, podemos exportar o histórico de execução de nosso experimento do Azure Machine Learning para logs do tensorboard, para que possamos exibi-los via tensorboard.  

No código a seguir, criamos a pasta `logdir` em nosso diretório de trabalho atual. Essa pasta é onde exportaremos nosso histórico e logs de execução de experimento de `root_run` e, em seguida, marcaremos executar como concluído. 

```Python
from azureml.tensorboard.export import export_to_tensorboard
import os

logdir = 'exportedTBlogs'
log_path = os.path.join(os.getcwd(), logdir)
try:
    os.stat(log_path)
except os.error:
    os.mkdir(log_path)
print(logdir)

# export run history for the project
export_to_tensorboard(root_run, logdir)

root_run.complete()
```

>[!Note]
 Você também pode exportar uma execução específica para TensorBoard especificando o nome da execução `export_to_tensorboard(run_name, logdir)`

### <a name="start-and-stop-tensorboard"></a>Iniciar e parar o TensorBoard
Quando o histórico de execuções desse experimento é exportado, podemos iniciar o TensorBoard com o método [Start ()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py#start-start-browser-false-) . 

```Python
from azureml.tensorboard import Tensorboard

# The TensorBoard constructor takes an array of runs, so be sure and pass it in as a single-element array here
tb = Tensorboard([], local_root=logdir, port=6006)

# If successful, start() returns a string with the URI of the instance.
tb.start()
```

Quando terminar, certifique-se de chamar o método [Stop ()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py#stop--) do objeto TensorBoard. Caso contrário, o TensorBoard continuará a ser executado até que você desligue o kernel do notebook. 

```python
tb.stop()
```

## <a name="next-steps"></a>Passos seguintes

Nestas instruções, você criou dois experimentos e aprendeu a iniciar o TensorBoard em relação a seus históricos de execução para identificar áreas para ajuste e readaptação em potencial. 

* Se você estiver satisfeito com seu modelo, vá até nosso artigo [como implantar um modelo](how-to-deploy-and-where.md) . 
* Saiba mais sobre [ajuste de hiperparâmetro](how-to-tune-hyperparameters.md). 
