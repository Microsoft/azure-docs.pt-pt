---
title: Visualizar experimentações com o serviço TensorBoard e o Azure Machine Learning
description: Inicie o TensorBoard para visualizar os históricos de experimentação, executar e identificar potenciais áreas de hiper-parâmetros ajuste e a reparametrização.
services: machine-learning
author: maxluk
ms.author: maxluk
ms.reviewer: nibaccam
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 06/28/2019
ms.openlocfilehash: babd4cdf8b7ed9e04b4bd975d840688b27439c4f
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/04/2019
ms.locfileid: "67560858"
---
# <a name="visualize-experiment-runs-and-metrics-with-tensorboard-and-azure-machine-learning"></a>Visualize execuções da experimentação e de métricas com TensorBoard e o Azure Machine Learning

Neste artigo, irá aprender a ver as execuções da experimentação e métricas TensorBoard usando [a `tensorboard` pacote](https://docs.microsoft.com/python/api/azureml-tensorboard/?view=azure-ml-py) no principal do Azure Machine Learning o SDK do serviço. Assim que tiver inspecionado as execuções da experimentação, pode otimizar melhor e voltar a preparar modelos de aprendizagem.

[TensorBoard](https://www.tensorflow.org/tensorboard/r1/overview) é um conjunto de aplicativos web para inspecionar e compreender a estrutura de experimentação e o desempenho.

Como inicia TensorBoard com as experiências do Azure Machine Learning depende do tipo de experimentação:
+ Se sua experimentação nativamente produz os ficheiros de registo que são consumíveis por TensorBoard, tais como experimentações PyTorch, Chainer e TensorFlow, em seguida, pode [inicie TensorBoard diretamente](#direct) de experimentação histórico de execuções. 

+ Para experimentações que não nativamente ficheiros de saída TensorBoard consumíveis, por exemplo, como Scikit-saiba ou do Azure Machine Learning experimentações, utilize [a `export_to_tensorboard()` método](#export) para exportar os históricos de execução como TensorBoard registos e iniciar TensorBoard a partir daí. 

## <a name="prerequisites"></a>Pré-requisitos

* Para iniciar TensorBoard e ver a sua experimentação históricos de execuções, suas experimentações tem anteriormente tiver ativado o registo controlar o métricas e o desempenho.  

* O código nesta explicação de procedimento pode ser executado em qualquer um dos seguintes ambientes: 

    * Azure Machine Learning bloco de notas do VM - nenhuma downloads ou de uma instalação necessária

        * Concluir o [guia de introdução do bloco de notas com base na cloud](quickstart-run-cloud-notebook.md#create-notebook) para criar um servidor dedicado de bloco de notas pré-carregados com o SDK e o repositório de exemplo.

        * Na pasta de exemplos no servidor do bloco de notas, encontrar duas concluída e expandido blocos de notas ao navegar para este diretório: **procedimentos-to-use-azureml > treinamento-com-aprendizagem profunda**.
        * export-run-history-to-run-history.ipynb
        * tensorboard.ipynb

    * O seu servidor de bloco de notas Juptyer
      * Utilize o [criar um artigo de área de trabalho](setup-create-workspace.md) para
          * [Instalar o SDK do Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) com o `tensorboard` extra
          * Criar uma área de trabalho e seu arquivo de configuração (JSON)
  
<a name="direct"></a>
## <a name="option-1-directly-view-run-history-in-tensorboard"></a>Opção 1: Vista diretamente de histórico de execuções no TensorBoard

Esta opção funciona para as experimentações que ficheiros consumíveis por TensorBoard, tais como PyTorch, Chainer de registo de saídas de forma nativa e experimentações do TensorFlow. Se não for esse o caso da sua experimentação, utilize [a `export_to_tensorboard()` método](#export) em vez disso.

O código de exemplo seguinte utiliza a [experimentação de demonstração MNIST](https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py) do repositório do TensorFlow num destino de computação remota, computação do Azure Machine Learning. Em seguida, para preparar o nosso modelo com personalizado do SDK [estimador de TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py), e, em seguida, iniciar TensorBoard em relação a esta experiência de TensorFlow, ou seja, uma experimentação que produz nativamente TensorBoard arquivos de eventos.

### <a name="set-experiment-name-and-create-project-folder"></a>Defina o nome de experimentação e criar a pasta do projeto

Aqui estamos nomear a experimentação e criar a sua pasta. 
 
```python
from os import path, makedirs
experiment_name = 'tensorboard-demo'

# experiment folder
exp_dir = './sample_projects/' + experiment_name

if not path.exists(exp_dir):
    makedirs(exp_dir)

```

### <a name="download-tensorflow-demo-experiment-code"></a>Baixe o código de experimentação de demonstração do TensorFlow

Repositório do TensorFlow tem uma demonstração MNIST com extensiva TensorBoard instrumentação. Nós não o fizer, nem precisa de alterar qualquer parte do código nesta demonstração, para que funcione com o serviço Azure Machine Learning. No código a seguir, vamos baixar o código MNIST e guardá-lo na nossa pasta de experimentação recém-criado.

```python
import requests
import os

tf_code = requests.get("https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py")
with open(os.path.join(exp_dir, "mnist_with_summaries.py"), "w") as file:
    file.write(tf_code.text)
```
Em todo o arquivo de código MNIST mnist_with_summaries.py, tenha em atenção que existem linhas essa chamada `tf.summary.scalar()`, `tf.summary.histogram()`, `tf.summary.FileWriter()` etc. Esses métodos de grupo, inicie sessão e marque as métricas chave de suas experimentações no histórico de execuções. O `tf.summary.FileWriter()` é especialmente importante como ele serializa os dados a partir de suas métricas de experimentação com sessão iniciada, o que permite TensorBoard gerar visualizações deles.

 ### <a name="configure-experiment"></a>Configurar experimentação

A seguir, vamos configurar nosso experimentação e configurar diretórios para registos e os dados. Estes registos serão carregados para o serviço de artefacto, que TensorBoard acessa mais tarde.

>[!Note]
> Para este exemplo do TensorFlow, terá de instalar o TensorFlow no seu computador local. Além disso, o módulo de TensorBoard (ou seja, aquele acompanha o TensorFlow) tem de estar acessível ao kernel este bloco de notas, que o computador local é o que é executado TensorBoard.

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

### <a name="create-a-cluster-for-your-experiment"></a>Criar um cluster para a experimentação
Vamos criar um cluster de AmlCompute nesta experimentação, no entanto, as experimentações que podem ser criadas em qualquer ambiente e ainda conseguir iniciar TensorBoard contra a experimentação histórico de execuções. 

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

### <a name="submit-run-with-tensorflow-estimator"></a>Submeter a execução com estimador de TensorFlow

O avaliador de TensorFlow fornece uma forma simples de iniciar uma tarefa de formação de TensorFlow num destino de computação. Ele é implementado por meio de genérica [ `estimator` ](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) classe, que pode ser utilizado para suportar qualquer estrutura. Para obter mais informações sobre modelos com o avaliador de genérico de treinamento, consulte [formar modelos com o Azure Machine Learning utilizando estimator](how-to-train-ml-models.md)

```Python
from azureml.train.dnn import TensorFlow
script_params = {"--log_dir": "./logs"}

tf_estimator = TensorFlow(source_directory=exp_dir,
                          compute_target=compute_target,
                          entry_script='mnist_with_summaries.py',
                          script_params=script_params)

run = exp.submit(tf_estimator)
```

### <a name="launch-tensorboard"></a>Iniciar TensorBoard

Pode iniciar TensorBoard durante a execução ou depois de terminar. A seguir, criamos uma instância de objeto de TensorBoard `tb`, que leva a experimentação executar histórico carregado no `run`e, em seguida, inicia TensorBoard com o `start()` método. 
  
O [TensorBoard construtor](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py) assume uma matriz de execuções, por isso, certifique-se e passá-lo como uma matriz de elemento único.

```python
from azureml.tensorboard import Tensorboard

tb = Tensorboard([run])

# If successful, start() returns a string with the URI of the instance.
tb.start()

# After your job completes, be sure to stop() the streaming otherwise it will continue to run. 
tb.stop()
```

<a name="export"></a>

## <a name="option-2-export-history-as-log-to-view-in-tensorboard"></a>Opção 2: Exportar histórico como registo para ver no TensorBoard

O código a seguir configura uma experimentação de exemplo, inicia o processo de registo com o Azure Machine Learning APIs de histórico de execuções e exporta a experimentação histórico de execuções em logs consumíveis por TensorBoard para visualização. 

### <a name="set-up-experiment"></a>Configurar experimentação

O código a seguir configura uma nova experimentação e atribui o diretório de execução `root_run`. 

```python
from azureml.core import Workspace, Experiment
import azuremml.core

# set experiment name and run name
ws = Workspace.from_config()
experiment_name = 'export-to-tensorboard'
exp = Experiment(ws, experiment_name)
root_run = exp.start_logging()
```

Aqui vamos carregar o conjunto de dados de diabetes – um conjunto de dados pequeno incorporado fornecida com scikit-saiba e dividi-lo em conjuntos de formação e teste.

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

### <a name="run-experiment-and-log-metrics"></a>Execute a experimentação e métricas de registo

Para esse código, vamos preparar um modelo de regressão linear e métricas-chave, o coeficiente alfa, de registo `alpha` e significam erros ao quadrado, `mse`, no histórico de execuções.

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

### <a name="export-runs-to-tensorboard"></a>Execuções de exportação para TensorBoard

Com o SDK [export_to_tensorboard()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.export?view=azure-ml-py) método, podemos exportar o histórico de execuções da nossa experimentação do Azure machine learning em TensorBoard logs, para que possa vê-los via TensorBoard.  

O código a seguir, vamos criar a pasta `logdir` no nosso diretório de trabalho atual. Esta pasta é onde vamos exportar nossa experiência histórico de execuções e inicia a partir de `root_run` e, em seguida, marcar que são executados como concluído. 

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
 Também pode exportar uma execução específica para TensorBoard, especificando o nome da execução  `export_to_tensorboard(run_name, logdir)`

Iniciar e parar uma vez TensorBoard que nosso histórico de execuções desta experiência é exportado, podemos iniciar TensorBoard com o [start()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py#start-start-browser-false-) método. 

```Python
from azureml.tensorboard import Tensorboard

# The TensorBoard constructor takes an array of runs, so be sure and pass it in as a single-element array here
tb = Tensorboard([], local_root=logdir, port=6006)

# If successful, start() returns a string with the URI of the instance.
tb.start()
```

Quando tiver terminado, certifique-se chamar o [stop()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py#stop--) método do objeto TensorBoard. Caso contrário, TensorBoard continuará a ser executado até encerrar o kernel de bloco de notas. 

```python
tb.stop()
```

## <a name="next-steps"></a>Passos Seguintes

Neste procedimento, criou duas experiências e ficou a saber como iniciar TensorBoard contra os históricos de execução para identificar as áreas para o potencial de ajuste e reparametrização. 

* Se estiver satisfeito com o seu modelo, aceda à nossa [como implementar um modelo](how-to-deploy-and-where.md) artigo. 
* Saiba mais sobre [otimização de hiper-parâmetros](how-to-tune-hyperparameters.md). 
