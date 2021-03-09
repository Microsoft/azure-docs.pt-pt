---
title: Visualizar experimentações com TensorBoard
titleSuffix: Azure Machine Learning
description: Lançar o TensorBoard para visualizar as histórias de execução de experiências e identificar áreas potenciais para afinação e reconversão de hiperparímetros.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: minxia
ms.author: minxia
ms.date: 02/27/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 2af82734fb9e1571242eec016f36f691411a8f2e
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102518760"
---
# <a name="visualize-experiment-runs-and-metrics-with-tensorboard-and-azure-machine-learning"></a>Visualizar corridas e métricas de experimentação com TensorBoard e Azure Machine Learning


Neste artigo, você aprende a ver as suas experiências e métricas no TensorBoard usando [o `tensorboard` pacote](/python/api/azureml-tensorboard/) no SDK de aprendizagem automática Azure. Uma vez inspecionado as suas experiências, pode afinar e retreinar melhor os seus modelos de aprendizagem automática.

[O TensorBoard](https://www.tensorflow.org/tensorboard/r1/overview) é um conjunto de aplicações web para inspecionar e compreender a sua estrutura e desempenho de experiências.

A forma como lança o TensorBoard com experiências de Aprendizagem automática Azure depende do tipo de experiência:
+ Se a sua experiência descodionar ficheiros de registo que são consumíveis pelo TensorBoard, tais como experiências PyTorch, Chainer e TensorFlow, então pode [lançar o TensorBoard diretamente](#launch-tensorboard) do histórico de execução da experiência. 

+ Para experiências que não produzam ficheiros consumíveis do TensorBoard, tais como experiências de Scikit-learn ou Azure Machine Learning, utilize [o `export_to_tensorboard()` método](#export) para exportar as histórias de execução como registos tensorboard e lançar o TensorBoard a partir daí. 

> [!TIP]
> A informação neste documento é principalmente para cientistas de dados e desenvolvedores que querem monitorizar o processo de formação de modelos. Se você é um administrador interessado em monitorizar a utilização de recursos e eventos da Azure Machine learning, tais como quotas, execuções de formação completas ou implementações de modelos concluídas, consulte [Monitoring Azure Machine Learning](monitor-azure-machine-learning.md).

## <a name="prerequisites"></a>Pré-requisitos

* Para lançar o TensorBoard e ver as suas histórias de execução de experiências, as suas experiências precisam de ter previamente permitido o registo para rastrear as suas métricas e desempenho.  
* O código deste documento pode ser executado em qualquer um dos seguintes ambientes: 
    * Exemplo de computação Azure Machine Learning - sem transferências ou instalação necessárias
        * Complete o [Tutorial: Ambiente de configuração e espaço de trabalho](tutorial-1st-experiment-sdk-setup.md) para criar um servidor de caderno dedicado pré-carregado com o SDK e o repositório de amostras.
        * Na pasta de amostras do servidor de portátil, encontre dois cadernos completos e expandidos navegando para estes diretórios:
            * **como-usar-azureml > experiências de > tensorboard > exportação-run-run-história-a-tensorboard > exportação-run-run-history-to-tensorboard.ipynb**
            * **como-usar-azureml > experiências de > tensorboard > tensorboard > tensorboard.ipynb**
    * O seu próprio servidor de cadernos Juptyer
       * [Instale o Azure Machine Learning SDK](/python/api/overview/azure/ml/install) com o `tensorboard` extra
        * Criar um espaço de trabalho para [aprendizagem automática Azure.](how-to-manage-workspace.md)  
        * [Crie um ficheiro de configuração do espaço de trabalho.](how-to-configure-environment.md#workspace)

## <a name="option-1-directly-view-run-history-in-tensorboard"></a>Opção 1: Ver diretamente a história do run no TensorBoard

Esta opção funciona para experiências que desemboam ficheiros de registos de nativos consumíveis por TensorBoard, tais como experiências PyTorch, Chainer e TensorFlow. Se não for esse o caso da sua experiência, use [o `export_to_tensorboard()` método.](#export)

O seguinte código de exemplo utiliza a experiência de demonstração do [MNIST](https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py) do repositório do TensorFlow num alvo de computação remota, Azure Machine Learning Compute. Em seguida, vamos configurar e começar uma corrida para a formação do modelo TensorFlow, e depois iniciar o TensorBoard contra esta experiência TensorFlow.

### <a name="set-experiment-name-and-create-project-folder"></a>Definir nome de experiência e criar pasta de projeto

Aqui damos o nome à experiência e criamos a sua pasta. 
 
```python
from os import path, makedirs
experiment_name = 'tensorboard-demo'

# experiment folder
exp_dir = './sample_projects/' + experiment_name

if not path.exists(exp_dir):
    makedirs(exp_dir)

```

### <a name="download-tensorflow-demo-experiment-code"></a>Baixe o código de experiência de demonstração de TensorFlow

O repositório do TensorFlow tem uma demonstração de MNIST com extensa instrumentação tensorBoard. Não alteramos, nem precisamos, alterar nenhum dos códigos desta demonstração para que funcione com a Azure Machine Learning. No seguinte código, descarregamos o código MNIST e guardamo-lo na nossa recém-criada pasta de experiências.

```python
import requests
import os

tf_code = requests.get("https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py")
with open(os.path.join(exp_dir, "mnist_with_summaries.py"), "w") as file:
    file.write(tf_code.text)
```
Em todo o ficheiro de código do MNIST, mnist_with_summaries.py, note que há linhas que `tf.summary.scalar()`  `tf.summary.histogram()` ligam, `tf.summary.FileWriter()` etc. Estes métodos agrupam, registam e marcam métricas-chave das suas experiências para a história da execução. O `tf.summary.FileWriter()` é especialmente importante, uma vez que serializa os dados das suas métricas de experiências registadas, o que permite que o TensorBoard gere visualizações fora deles.

 ### <a name="configure-experiment"></a>Experiência de configuração

No seguinte, configuramos a nossa experiência e configuramos diretórios para registos e dados. Estes registos serão enviados para o histórico de execução, a que o TensorBoard acede mais tarde.

> [!Note]
> Para este exemplo tensorFlow, terá de instalar o TensorFlow na sua máquina local. Além disso, o módulo TensorBoard (ou seja, o incluído com o TensorFlow) deve estar acessível ao núcleo deste portátil, uma vez que a máquina local é o que funciona o TensorBoard.

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

# Writing logs to ./logs results in their being uploaded to the run history,
# and thus, made accessible to our TensorBoard instance.
args = ["--log_dir", logs_dir]

# Create an experiment
exp = Experiment(ws, experiment_name)
```

### <a name="create-a-cluster-for-your-experiment"></a>Crie um cluster para a sua experiência
Criamos um cluster AmlCompute para esta experiência, no entanto as suas experiências podem ser criadas em qualquer ambiente e ainda é capaz de lançar o TensorBoard contra o histórico de experiências. 

```Python
from azureml.core.compute import ComputeTarget, AmlCompute

cluster_name = "cpu-cluster"

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

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

### <a name="configure-and-submit-training-run"></a>Configure e submeta a formação

Configure um trabalho de formação criando um objeto ScriptRunConfig.

```Python
from azureml.core import ScriptRunConfig
from azureml.core import Environment

# Here we will use the TensorFlow 2.2 curated environment
tf_env = Environment.get(ws, 'AzureML-TensorFlow-2.2-GPU')

src = ScriptRunConfig(source_directory=exp_dir,
                      script='mnist_with_summaries.py',
                      arguments=args,
                      compute_target=compute_target,
                      environment=tf_env)
run = exp.submit(src)
```

### <a name="launch-tensorboard"></a>Lançar TensorBoard

Pode lançar o TensorBoard durante a sua execução ou depois de concluído. No seguinte, criamos uma instância de objeto tensorBoard, `tb` que leva a experiência executar a história carregada no , e depois lança o `run` TensorBoard com o `start()` método. 
  
O [construtor tensorBoard](/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard) leva uma série de execuções, por isso certifique-se e passe-o como uma matriz de um único elemento.

```python
from azureml.tensorboard import Tensorboard

tb = Tensorboard([run])

# If successful, start() returns a string with the URI of the instance.
tb.start()

# After your job completes, be sure to stop() the streaming otherwise it will continue to run. 
tb.stop()
```

> [!Note]
> Embora este exemplo tenha usado TensorFlow, o TensorBoard pode ser usado tão facilmente com PyTorch ou Chainer. O TensorFlow deve estar disponível na máquina que executa o TensorBoard, mas não é necessário na máquina que faz cálculos PyTorch ou Chainer. 


<a name="export"></a>

## <a name="option-2-export-history-as-log-to-view-in-tensorboard"></a>Opção 2: Histórico de exportação como log para ver no TensorBoard

O código que se segue configura uma experiência de amostra, inicia o processo de registo utilizando as APIs de execução da máquina de Azure, e exporta a experiência executar história em registos consumíveis pelo TensorBoard para visualização. 

### <a name="set-up-experiment"></a>Configurar experiência

O código que se segue estabelece uma nova experiência e dá nomes ao diretório de execução `root_run` . 

```python
from azureml.core import Workspace, Experiment
import azureml.core

# set experiment name and run name
ws = Workspace.from_config()
experiment_name = 'export-to-tensorboard'
exp = Experiment(ws, experiment_name)
root_run = exp.start_logging()
```

Aqui carregamos o conjunto de dados da diabetes... um pequeno conjunto de dados incorporado que vem com scikit-learn, e dividi-lo em conjuntos de teste e treino.

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

### <a name="run-experiment-and-log-metrics"></a>Executar experiência e métricas de log

Para este código, treinamos um modelo linear de regressão e métricas de chave de registo, o coeficiente `alpha` alfa, e erro quadrado médio, `mse` na história da execução.

```Python
from tqdm import tqdm
alphas = [.1, .2, .3, .4, .5, .6 , .7]
# try a bunch of alpha values in a Linear Regression (aka Ridge regression) mode
for alpha in tqdm(alphas):
  # create child runs and fit lines for the resulting models
  with root_run.child_run("alpha" + str(alpha)) as run:
 
   reg = Ridge(alpha=alpha)
   reg.fit(data["train"]["x"], data["train"]["y"])    
 
   preds = reg.predict(data["test"]["x"])
   mse = mean_squared_error(preds, data["test"]["y"])
   # End train and eval

# log alpha, mean_squared_error and feature names in run history
   root_run.log("alpha", alpha)
   root_run.log("mse", mse)
```

### <a name="export-runs-to-tensorboard"></a>Exportação corre para TensorBoard

Com o método de export_to_tensorboard da SDK, [podemos](/python/api/azureml-tensorboard/azureml.tensorboard.export) exportar a história da nossa experiência de aprendizagem automática Azure em registos tensorBoard, para que possamos vê-los através do TensorBoard.  

No seguinte código, criamos a pasta `logdir` no nosso diretório de trabalho atual. Esta pasta é onde exportaremos a nossa experiência executar histórico e registos `root_run` e, em seguida, marcar que correr como concluído. 

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

> [!Note]
> Também pode exportar uma determinada corrida para o TensorBoard especificando o nome da corrida  `export_to_tensorboard(run_name, logdir)`

### <a name="start-and-stop-tensorboard"></a>Iniciar e parar o TensorBoard
Uma vez que o nosso histórico de execução para esta experiência é exportado, podemos lançar o TensorBoard com o método [de arranque().](/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard#start-start-browser-false-) 

```Python
from azureml.tensorboard import Tensorboard

# The TensorBoard constructor takes an array of runs, so be sure and pass it in as a single-element array here
tb = Tensorboard([], local_root=logdir, port=6006)

# If successful, start() returns a string with the URI of the instance.
tb.start()
```

Quando terminar, certifique-se de ligar para o método [stop()](/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard#stop--) do objeto TensorBoard. Caso contrário, o TensorBoard continuará a funcionar até desligar o núcleo do portátil. 

```python
tb.stop()
```

## <a name="next-steps"></a>Passos seguintes

Neste como-para-você, criou duas experiências e aprendeu a lançar o TensorBoard contra as suas histórias de execução para identificar áreas para potenciais afinações e reconversão. 

* Se estiver satisfeito com o seu modelo, dirija-se ao nosso Como implementar um artigo [de modelo.](how-to-deploy-and-where.md) 
* Saiba mais sobre [a sintonização de hiperparímetros.](how-to-tune-hyperparameters.md)