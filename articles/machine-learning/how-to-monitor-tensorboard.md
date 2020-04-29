---
title: Visualizar experimentações com TensorBoard
titleSuffix: Azure Machine Learning
description: Lance o TensorBoard para visualizar as histórias de experimentação e identifique potenciais áreas para afinação e reconversão de hiperparâmetros.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: maxluk
ms.author: maxluk
ms.date: 02/27/2020
ms.openlocfilehash: b6b7e47acdbc5bd059e17e512731bd09c8580798
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78195384"
---
# <a name="visualize-experiment-runs-and-metrics-with-tensorboard-and-azure-machine-learning"></a>Visualizar as experiências e métricas com tensorBoard e Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, aprende a ver as suas experiências e métricas no TensorBoard utilizando [o `tensorboard` pacote](https://docs.microsoft.com/python/api/azureml-tensorboard/?view=azure-ml-py) no Principal SDK de Aprendizagem automática azure. Depois de inspecionar as suas experiências, pode sintonizar melhor e retreinar os seus modelos de aprendizagem automática.

[TensorBoard](https://www.tensorflow.org/tensorboard/r1/overview) é um conjunto de aplicações web para inspecionar e compreender a sua estrutura e desempenho de experiência.

A forma como lança sonorboard com experiências de Machine Learning Azure depende do tipo de experiência:
+ Se a sua experiência for de forma nativa, produz ficheiros de registo que são consumíveis pela TensorBoard, tais como experiências PyTorch, Chainer e TensorFlow, então pode lançar o [TensorBoard diretamente](#direct) a partir do histórico de execução da experiência. 

+ Para experiências que não produzam ficheiros consumíveis de forma nativa do TensorBoard, como as experiências de Scikit-learn ou Azure Machine Learning, utilize [o `export_to_tensorboard()` método](#export) para exportar as histórias de execução como troncos TensorBoard e lançar o TensorBoard a partir daí. 

> [!TIP]
> A informação neste documento é principalmente para cientistas de dados e desenvolvedores que querem monitorizar o processo de formação de modelos. Se for um administrador interessado em monitorizar a utilização de recursos e eventos da Aprendizagem de Máquinas Azure, tais como quotas, execuções de formação concluídas ou implementações de modelos concluídas, consulte [Monitorização da Aprendizagem automática azure](monitor-azure-machine-learning.md).

## <a name="prerequisites"></a>Pré-requisitos

* Para lançar o TensorBoard e ver as suas histórias de execução de experiências, as suas experiências precisam de ter ativado previamente a exploração madeireira para acompanhar as suas métricas e desempenho.  

* O código deste documento pode ser executado em qualquer um dos seguintes ambientes: 

    * Instância computacional Azure Machine Learning - sem downloads ou instalação necessária

        * Complete o [Tutorial: Ambiente de configuração e espaço de trabalho](tutorial-1st-experiment-sdk-setup.md) para criar um servidor de portátil dedicado pré-carregado com o SDK e o repositório de amostras.

        * Na pasta de amostras no servidor de cadernos, encontre dois cadernos completos e expandidos navegando para estes diretórios:
            * **como-usar-azureml > formação-com-aprendizagem profunda > exportação-run-history-to-tensorboard > export-run-history-to-tensorboard.ipynb**

            * **how-to-use-azureml > track-and-monitor-experiments > tensorboard.ipynb**

    * O seu próprio servidor de cadernos Juptyer
       * [Instale o SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) de `tensorboard` Aprendizagem automática Azure com o extra
        * [Crie um espaço de trabalho azure machine learning.](how-to-manage-workspace.md)  
        * Criar um ficheiro de [configuração do espaço de trabalho.](how-to-configure-environment.md#workspace)
  
<a name="direct"></a>

## <a name="option-1-directly-view-run-history-in-tensorboard"></a>Opção 1: Visualizar diretamente o histórico de execução no TensorBoard

Esta opção funciona para experiências que de forma nativa produzficheiros de registo consumíveis pela TensorBoard, tais como experiências PyTorch, Chainer e TensorFlow. Se não for esse o caso da sua experiência, utilize [ `export_to_tensorboard()` o método.](#export)

O seguinte código de exemplo utiliza a [experiência de demonstração MNIST](https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py) do repositório da TensorFlow num alvo de computação remota, a Azure Machine Learning Compute. Em seguida, treinamos o nosso modelo com o [estimador tensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)personalizado do SDK, e em seguida, iniciar o TensorBoard contra esta experiência TensorFlow, isto é, uma experiência que produz de forma nativa ficheiros de eventos TensorBoard.

### <a name="set-experiment-name-and-create-project-folder"></a>Definir nome da experiência e criar pasta de projeto

Aqui damos o nome da experiência e criamos a sua pasta. 
 
```python
from os import path, makedirs
experiment_name = 'tensorboard-demo'

# experiment folder
exp_dir = './sample_projects/' + experiment_name

if not path.exists(exp_dir):
    makedirs(exp_dir)

```

### <a name="download-tensorflow-demo-experiment-code"></a>Baixe o código de experiência de demonstração TensorFlow

O repositório da TensorFlow tem uma demonstração mNIST com extensa instrumentação TensorBoard. Não alteramos, nem precisamos, nenhum código desta demo para que funcione com o Azure Machine Learning. No seguinte código, descarregamos o código MNIST e guardamo-lo na nossa pasta de experiênciarecém-criada.

```python
import requests
import os

tf_code = requests.get("https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py")
with open(os.path.join(exp_dir, "mnist_with_summaries.py"), "w") as file:
    file.write(tf_code.text)
```
Ao longo do ficheiro de código MNIST, mnist_with_summaries.py, note que existem linhas que ligam, `tf.summary.scalar()` `tf.summary.histogram()` `tf.summary.FileWriter()` etc. Estes métodos group, log e tag métricas chave das suas experiências para o histórico de execução. O `tf.summary.FileWriter()` é especialmente importante pois serializa os dados das suas métricas de experiência registadas, o que permite que o TensorBoard gere visualizações fora deles.

 ### <a name="configure-experiment"></a>Configurar experiência

No seguinte, configuramos a nossa experiência e configuramos diretórios para registos e dados. Estes registos serão enviados para o Serviço de Artefactos, a que a TensorBoard acede mais tarde.

>[!Note]
> Para este exemplo TensorFlow, terá de instalar o TensorFlow na sua máquina local. Além disso, o módulo TensorBoard (isto é, o incluído com TensorFlow) deve estar acessível ao núcleo deste bloco de notas, uma vez que a máquina local é o que funciona o TensorBoard.

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

### <a name="create-a-cluster-for-your-experiment"></a>Crie um cluster para a sua experiência
Criamos um cluster AmlCompute para esta experiência, no entanto as suas experiências podem ser criadas em qualquer ambiente e ainda é capaz de lançar o TensorBoard contra a história da execução de experiências. 

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

### <a name="submit-run-with-tensorflow-estimator"></a>Submeter execução com estimativa tensorFlow

O estimador TensorFlow fornece uma forma simples de lançar um trabalho de formação TensorFlow num alvo de cálculo. É implementado através da [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) classe genérica, que pode ser usado para apoiar qualquer enquadramento. Para obter mais informações sobre modelos de formação utilizando o estimador genérico, consulte modelos de [comboio com Machine Learning Azure utilizando estimador](how-to-train-ml-models.md)

```Python
from azureml.train.dnn import TensorFlow
script_params = {"--log_dir": "./logs"}

tf_estimator = TensorFlow(source_directory=exp_dir,
                          compute_target=compute_target,
                          entry_script='mnist_with_summaries.py',
                          script_params=script_params)

run = exp.submit(tf_estimator)
```

### <a name="launch-tensorboard"></a>Lançar TensorBoard

Pode lançar o TensorBoard durante a sua execução ou após o seu completo. No seguinte, criamos uma instância de `tb`objeto TensorBoard, que `run`leva o histórico de execução `start()` de experiências carregado no , e depois lança o TensorBoard com o método. 
  
O [construtor TensorBoard](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py) faz uma série de corridas, por isso certifique-se e passe-o como uma matriz de um único elemento.

```python
from azureml.tensorboard import Tensorboard

tb = Tensorboard([run])

# If successful, start() returns a string with the URI of the instance.
tb.start()

# After your job completes, be sure to stop() the streaming otherwise it will continue to run. 
tb.stop()
```

>[!Note]
 Embora este exemplo tenha usado o TensorFlow, o TensorBoard pode ser utilizado tão facilmente com os modelos PyTorch ou Chainer. O TensorFlow deve estar disponível na máquina que executa o TensorBoard, mas não é necessário na máquina que faça computações pyTorch ou Chainer. 


<a name="export"></a>

## <a name="option-2-export-history-as-log-to-view-in-tensorboard"></a>Opção 2: Histórico de exportação como registo a ver no TensorBoard

O código seguinte configura uma experiência de amostra, inicia o processo de exploração de madeira utilizando as APIs de história de corrida de máquinas azure, e exporta a experiência executar história em registos consumíveis pela TensorBoard para visualização. 

### <a name="set-up-experiment"></a>Configurar a experiência

O código seguinte estabelece uma nova experiência e `root_run`dá nomes ao diretório de execução . 

```python
from azureml.core import Workspace, Experiment
import azuremml.core

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

### <a name="run-experiment-and-log-metrics"></a>Executar experimentos e métricas de log

Para este código, treinamos um modelo linear de regressão e `alpha`registamos métricas chave, o coeficiente alfa, e erro quadrado médio, `mse`na história da execução.

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

### <a name="export-runs-to-tensorboard"></a>Exportação vai para TensorBoard

Com o método [export_to_tensorboard()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.export?view=azure-ml-py) do SDK, podemos exportar o histórico de execução da nossa experiência de aprendizagem automática Azure em registos TensorBoard, para que possamos vê-los via TensorBoard.  

No seguinte código, criamos `logdir` a pasta no nosso atual diretório de trabalho. Esta pasta é onde exportaremos o histórico `root_run` de experiências e registos de e, em seguida, marcar que funcionam como concluídos. 

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
 Também pode exportar uma determinada corrida para a TensorBoard, especificando o nome da execução`export_to_tensorboard(run_name, logdir)`

### <a name="start-and-stop-tensorboard"></a>Iniciar e parar tensorBoard
Assim que o nosso histórico de execução para esta experiência for exportado, podemos lançar o TensorBoard com o método [de início().](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py#start-start-browser-false-) 

```Python
from azureml.tensorboard import Tensorboard

# The TensorBoard constructor takes an array of runs, so be sure and pass it in as a single-element array here
tb = Tensorboard([], local_root=logdir, port=6006)

# If successful, start() returns a string with the URI of the instance.
tb.start()
```

Quando terminar, certifique-se de que chama o método de [paragem ()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py#stop--) do objeto TensorBoard. Caso contrário, o TensorBoard continuará a funcionar até desligar o núcleo de caderno. 

```python
tb.stop()
```

## <a name="next-steps"></a>Passos seguintes

Neste how-to-to, criou duas experiências e aprendeu a lançar o TensorBoard contra as suas histórias de execução para identificar áreas para potencial afinação e reconversão. 

* Se está satisfeito com o seu modelo, [dirija-se](how-to-deploy-and-where.md) ao nosso Como implementar um artigo modelo. 
* Saiba mais sobre [a finação do hiperparâmetro.](how-to-tune-hyperparameters.md) 
