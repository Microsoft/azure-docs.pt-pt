---
title: 'Tutorial de classificação de imagem: Modelos de comboio'
titleSuffix: Azure Machine Learning
description: Use Azure Machine Learning para treinar um modelo de classificação de imagem com scikit-learn em um caderno Python Jupyter. Este tutorial é a primeira parte de dois.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sdgilley
ms.author: sgilley
ms.date: 09/28/2020
ms.custom: seodec18, devx-track-python
ms.openlocfilehash: 40ee7ad74d1a1daaf6df5e76b5e51db52feea304
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91535074"
---
# <a name="tutorial-train-image-classification-models-with-mnist-data-and-scikit-learn"></a>Tutorial: Modelos de classificação de imagem de comboio com dados do MNIST e scikit-learn 


Neste tutorial, treina-se um modelo de aprendizagem automática em recursos de computação remota. Você usará o fluxo de trabalho de treino e implantação para Azure Machine Learning em um caderno Python Jupyter.  Depois, pode utilizar o bloco de notas como um modelo para preparar o seu próprio modelo de machine learning com os seus dados. Este tutorial é a **primeira parte de uma série composta por duas partes**.  

Este tutorial treina uma simples regressão logística utilizando o conjunto de dados [do MNIST](http://yann.lecun.com/exdb/mnist/) e [aprendendo com](https://scikit-learn.org) a Azure Machine Learning. O MNIST é um conjunto de dados popular que consiste em 70 000 imagens em tons de cinzento. Cada imagem é um dígito manuscrito de 28 x 28 pixels, representando um número de zero a nove. O objetivo é criar um classificador multiclasses para identificar o dígito que uma determinada imagem representa.

Saiba como tomar as seguintes ações:

> [!div class="checklist"]
> * Crie o seu ambiente de desenvolvimento.
> * Aceda e examine os dados.
> * Treine um modelo de regressão logística simples num cluster remoto.
> * Reveja os resultados da formação e registe o melhor modelo.

Aprende-se a selecionar um modelo e a implantá-lo na [segunda parte deste tutorial.](tutorial-deploy-models-with-aml.md)

Se não tiver uma subscrição do Azure, crie uma conta gratuita antes de começar. Experimente hoje a [versão gratuita ou paga do Azure Machine Learning.](https://aka.ms/AMLFree)

>[!NOTE]
> O código deste artigo foi testado com a versão 1.13.0 [da Azure Machine Learning SDK.](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true)

## <a name="prerequisites"></a>Pré-requisitos

* Complete o [Tutorial: Começa a criar a tua primeira experiência Azure ML](tutorial-1st-experiment-sdk-setup.md) para:
    * Criar uma área de trabalho
    * Clone o caderno de tutoriais para a sua pasta no espaço de trabalho.
    * Crie uma instância computacional baseada em nuvem.

* Na sua pasta *de tutoriais clonados/classificação de imagem-mnista-dados,* abra o caderno *img-classification-part1-training.ipynb.* 


O tutorial e **acompanhante utils.py** ficheiro também está disponível no [GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials) se desejar usá-lo no seu próprio [ambiente local.](how-to-configure-environment.md#local) Corra `pip install azureml-sdk[notebooks] azureml-opendatasets matplotlib` para instalar dependências para este tutorial.

> [!Important]
> O resto deste artigo contém o mesmo conteúdo que se vê no caderno.  
>
> Mude agora para o caderno Jupyter se quiser ler enquanto executar o código. 
> Para executar uma única célula de código num bloco de notas, clique na célula de código e clique no **Shift+Enter**. Ou, executar todo o caderno escolhendo **Executar tudo** a partir da barra de ferramentas superior.

## <a name="set-up-your-development-environment"></a><a name="start"></a>Configurar o ambiente de desenvolvimento

Toda a configuração para o seu trabalho de desenvolvimento pode ser feita num bloco de notas Python. A configuração inclui as seguintes ações:

* Importar pacotes Python.
* Conecte-se a um espaço de trabalho, para que o computador local possa comunicar com recursos remotos.
* Crie uma experiência para rastrear todas as suas corridas.
* Crie um alvo de computação remota para usar para o treino.

### <a name="import-packages"></a>Importar pacotes

Importe os pacotes Python de que precisa nesta sessão. Também exiba a versão Azure Machine Learning SDK:

```python
%matplotlib inline
import numpy as np
import matplotlib.pyplot as plt

import azureml.core
from azureml.core import Workspace

# check core SDK version number
print("Azure ML SDK Version: ", azureml.core.VERSION)
```

### <a name="connect-to-a-workspace"></a>Ligar-se a um espaço de trabalho

Crie um objeto de área de trabalho a partir da área de trabalho existente. `Workspace.from_config()` lê o ficheiro **config.js** e carrega os detalhes num objeto chamado `ws` :

```python
# load workspace configuration from the config.json file in the current folder.
ws = Workspace.from_config()
print(ws.name, ws.location, ws.resource_group, sep='\t')
```

### <a name="create-an-experiment"></a>Criar uma experimentação

Crie uma experimentação para acompanhar as execuções na sua área de trabalho. Um espaço de trabalho pode ter múltiplas experiências:

```python
from azureml.core import Experiment
experiment_name = 'sklearn-mnist'

exp = Experiment(workspace=ws, name=experiment_name)
```

### <a name="create-or-attach-an-existing-compute-target"></a>Criar ou anexar um alvo de computação existente

Ao utilizar o Azure Machine Learning Compute, um serviço gerido, os cientistas de dados podem treinar modelos de aprendizagem automática em aglomerados de máquinas virtuais Azure. Exemplos incluem VMs com suporte GPU. Neste tutorial, você cria a Azure Machine Learning Compute como seu ambiente de treino. Você submeterá o código Python para executar neste VM mais tarde no tutorial. 

O código abaixo cria os clusters de computação para si se eles já não existirem no seu espaço de trabalho. Configura um cluster que irá reduzir para 0 quando não estiver em uso, e pode escalar até um máximo de 4 nós. 

 **A criação do alvo do cálculo leva cerca de cinco minutos.** Se o recurso computacional já estiver no espaço de trabalho, o código utiliza-o e ignora o processo de criação.

```python
from azureml.core.compute import AmlCompute
from azureml.core.compute import ComputeTarget
import os

# choose a name for your cluster
compute_name = os.environ.get("AML_COMPUTE_CLUSTER_NAME", "cpu-cluster")
compute_min_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MIN_NODES", 0)
compute_max_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MAX_NODES", 4)

# This example uses CPU VM. For using GPU VM, set SKU to STANDARD_NC6
vm_size = os.environ.get("AML_COMPUTE_CLUSTER_SKU", "STANDARD_D2_V2")


if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('found compute target. just use it. ' + compute_name)
else:
    print('creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size=vm_size,
                                                                min_nodes=compute_min_nodes,
                                                                max_nodes=compute_max_nodes)

    # create the cluster
    compute_target = ComputeTarget.create(
        ws, compute_name, provisioning_config)

    # can poll for a minimum number of nodes and for a specific timeout.
    # if no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(
        show_output=True, min_node_count=None, timeout_in_minutes=20)

    # For a more detailed view of current AmlCompute status, use get_status()
    print(compute_target.get_status().serialize())
```

Tem agora os pacotes e os recursos de computação necessários para preparar um modelo na cloud. 

## <a name="explore-data"></a>Explorar dados

Antes de treinar um modelo, precisa entender os dados que usa para treiná-lo. Nesta secção, vai aprender a:

* Descarregue o conjunto de dados do MNIST.
* Mostrar algumas imagens de amostra.

### <a name="download-the-mnist-dataset"></a>Transferir o conjunto de dados MNIST

Utilize conjuntos de dados abertos Azure para obter os ficheiros de dados MNIST em bruto. [Os conjuntos de dados Azure Open](https://docs.microsoft.com/azure/open-datasets/overview-what-are-open-datasets) são conjuntos de dados públicos com curadoria que pode utilizar para adicionar funcionalidades específicas de cenários a soluções de machine learning para modelos mais precisos. Cada conjunto de dados tem uma classe correspondente, `MNIST` neste caso, para recuperar os dados de diferentes maneiras.

Este código recupera os dados como um `FileDataset` objeto, que é uma subclasse de `Dataset` . Uma `FileDataset` referência a ficheiros únicos ou múltiplos de qualquer formato nas suas datastores ou urls públicos. A classe fornece-lhe a capacidade de descarregar ou montar os ficheiros no seu cálculo, criando uma referência à localização da fonte de dados. Além disso, registe o Dataset no seu espaço de trabalho para uma fácil recuperação durante o treino.

Siga o [como](how-to-create-register-datasets.md) saber mais sobre datasets e sua utilização no SDK.

```python
from azureml.core import Dataset
from azureml.opendatasets import MNIST

data_folder = os.path.join(os.getcwd(), 'data')
os.makedirs(data_folder, exist_ok=True)

mnist_file_dataset = MNIST.get_file_dataset()
mnist_file_dataset.download(data_folder, overwrite=True)

mnist_file_dataset = mnist_file_dataset.register(workspace=ws,
                                                 name='mnist_opendataset',
                                                 description='training and test dataset',
                                                 create_new_version=True)
```

### <a name="display-some-sample-images"></a>Apresentar algumas imagens de exemplo

Carregue os ficheiros comprimidos para matrizes `numpy`. Em seguida, utilize `matplotlib` para desenhar 30 imagens aleatórias do conjunto de dados com as respetivas etiquetas acima das mesmas. Este passo requer uma `load_data` função que está incluída num `utils.py` ficheiro. Este ficheiro está incluído na pasta de exemplo. Certifique-se de que está na mesma pasta que este caderno. A `load_data` função simplesmente analisa os ficheiros comprimidos em matrizes numpipiárias.

```python
# make sure utils.py is in the same directory as this code
from utils import load_data
import glob


# note we also shrink the intensity values (X) from 0-255 to 0-1. This helps the model converge faster.
X_train = load_data(glob.glob(os.path.join(data_folder,"**/train-images-idx3-ubyte.gz"), recursive=True)[0], False) / 255.0
X_test = load_data(glob.glob(os.path.join(data_folder,"**/t10k-images-idx3-ubyte.gz"), recursive=True)[0], False) / 255.0
y_train = load_data(glob.glob(os.path.join(data_folder,"**/train-labels-idx1-ubyte.gz"), recursive=True)[0], True).reshape(-1)
y_test = load_data(glob.glob(os.path.join(data_folder,"**/t10k-labels-idx1-ubyte.gz"), recursive=True)[0], True).reshape(-1)


# now let's show some randomly chosen images from the traininng set.
count = 0
sample_size = 30
plt.figure(figsize=(16, 6))
for i in np.random.permutation(X_train.shape[0])[:sample_size]:
    count = count + 1
    plt.subplot(1, sample_size, count)
    plt.axhline('')
    plt.axvline('')
    plt.text(x=10, y=-10, s=y_train[i], fontsize=18)
    plt.imshow(X_train[i].reshape(28, 28), cmap=plt.cm.Greys)
plt.show()
```

Um exemplo aleatório de imagens apresenta:

![Amostra aleatória de imagens](./media/tutorial-train-models-with-aml/digits.png)

Agora, tem uma ideia do aspeto destas imagens e do resultado previsto da predição.

## <a name="train-on-a-remote-cluster"></a>Preparar num cluster remoto

Para esta tarefa, submete o trabalho para executar no cluster de treino remoto que criou anteriormente.  Para submeter um trabalho, tem de:
* Criar um diretório
* Criar um script de preparação
* Criar uma configuração de execução de script
* Submeter o trabalho

### <a name="create-a-directory"></a>Criar um diretório

Crie um diretório para fornecer o código necessário do computador para o recurso remoto.

```python
import os
script_folder = os.path.join(os.getcwd(), "sklearn-mnist")
os.makedirs(script_folder, exist_ok=True)
```

### <a name="create-a-training-script"></a>Criar um script de preparação

Para submeter o trabalho para o cluster, crie primeiro um script de preparação. Execute o código seguinte para criar o script de preparação denominado `train.py` no diretório que acabou de criar.

```python
%%writefile $script_folder/train.py

import argparse
import os
import numpy as np
import glob

from sklearn.linear_model import LogisticRegression
import joblib

from azureml.core import Run
from utils import load_data

# let user feed in 2 parameters, the dataset to mount or download, and the regularization rate of the logistic regression model
parser = argparse.ArgumentParser()
parser.add_argument('--data-folder', type=str, dest='data_folder', help='data folder mounting point')
parser.add_argument('--regularization', type=float, dest='reg', default=0.01, help='regularization rate')
args = parser.parse_args()

data_folder = args.data_folder
print('Data folder:', data_folder)

# load train and test set into numpy arrays
# note we scale the pixel intensity values to 0-1 (by dividing it with 255.0) so the model can converge faster.
X_train = load_data(glob.glob(os.path.join(data_folder, '**/train-images-idx3-ubyte.gz'), recursive=True)[0], False) / 255.0
X_test = load_data(glob.glob(os.path.join(data_folder, '**/t10k-images-idx3-ubyte.gz'), recursive=True)[0], False) / 255.0
y_train = load_data(glob.glob(os.path.join(data_folder, '**/train-labels-idx1-ubyte.gz'), recursive=True)[0], True).reshape(-1)
y_test = load_data(glob.glob(os.path.join(data_folder, '**/t10k-labels-idx1-ubyte.gz'), recursive=True)[0], True).reshape(-1)

print(X_train.shape, y_train.shape, X_test.shape, y_test.shape, sep = '\n')

# get hold of the current run
run = Run.get_context()

print('Train a logistic regression model with regularization rate of', args.reg)
clf = LogisticRegression(C=1.0/args.reg, solver="liblinear", multi_class="auto", random_state=42)
clf.fit(X_train, y_train)

print('Predict the test set')
y_hat = clf.predict(X_test)

# calculate accuracy on the prediction
acc = np.average(y_hat == y_test)
print('Accuracy is', acc)

run.log('regularization rate', np.float(args.reg))
run.log('accuracy', np.float(acc))

os.makedirs('outputs', exist_ok=True)
# note file saved in the outputs folder is automatically uploaded into experiment record
joblib.dump(value=clf, filename='outputs/sklearn_mnist_model.pkl')
```

Repare que o script obtém dados e guarda modelos:

+ O roteiro de formação lê um argumento para encontrar o diretório que contém os dados. Quando submete o trabalho posteriormente, tem de apontar para o arquivo de dados deste argumento: ```parser.add_argument('--data-folder', type=str, dest='data_folder', help='data directory mounting point')```

+ O script de treino guarda o seu modelo num diretório chamado **outputs**. Tudo o que for escrito neste diretório é carregado automaticamente para a sua área de trabalho. Aceda ao seu modelo a partir deste diretório mais tarde no tutorial. `joblib.dump(value=clf, filename='outputs/sklearn_mnist_model.pkl')`

+ O script de treino requer que o ficheiro `utils.py` carregue o conjunto de dados corretamente. O código seguinte copia `utils.py` para que o ficheiro possa ser `script_folder` acedido juntamente com o script de treino no recurso remoto.

  ```python
  import shutil
  shutil.copy('utils.py', script_folder)
  ```

### <a name="configure-the-training-job"></a>Configure o trabalho de formação

Crie um objeto [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py&preserve-view=true) para especificar os detalhes de configuração do seu trabalho de treino, incluindo o seu script de treino, ambiente a utilizar e o alvo de computação para executar. Configure o ScriptRunConfig especificando:

* O diretório que contém os seus scripts. Todos os ficheiros neste diretório são carregados para os nós do cluster, para execução.
* O destino de computação. Neste caso, irá utilizar o cluster de computação do Azure Machine Learning que criou.
* O nome do guião de treino, **train.py.**
* Um ambiente que contém as bibliotecas necessárias para executar o script.
* Argumentos exigidos do roteiro de treino.

Neste tutorial, este alvo é AmlCompute. Todos os ficheiros da pasta de script são enviados para os nós do cluster para serem executados. O **data_folder** está definido para utilizar o conjunto de dados.

Em primeiro lugar, crie o ambiente que contém: a biblioteca scikit-learn, o tempo de execução de dados azureml necessário para aceder ao conjunto de dados, e os padrão azureml que contêm as dependências das métricas de registo. Os azureml-defaults também contêm as dependências necessárias para a implementação do modelo como um serviço web mais tarde na parte 2 do tutorial.

Uma vez definido o ambiente, registe-o com o espaço de trabalho para o reutilizar na parte 2 do tutorial.

```python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies

# to install required packages
env = Environment('tutorial-env')
cd = CondaDependencies.create(pip_packages=['azureml-dataset-runtime[pandas,fuse]', 'azureml-defaults'], conda_packages=['scikit-learn==0.22.1'])

env.python.conda_dependencies = cd

# Register environment to re-use later
env.register(workspace=ws)
```

Em seguida, crie o ScriptRunConfig especificando o script de treino, o alvo do cálculo e o ambiente.

```python
from azureml.core import ScriptRunConfig

args = ['--data-folder', mnist_file_dataset.as_mount(), '--regularization', 0.5]

src = ScriptRunConfig(source_directory=script_folder,
                      script='train.py', 
                      arguments=args,
                      compute_target=compute_target,
                      environment=env)
```

### <a name="submit-the-job-to-the-cluster"></a>Submeter o trabalho para o cluster

Executar a experiência submetendo o objeto ScriptRunConfig:

```python
run = exp.submit(config=src)
run
```

Como a chamada é assíncrona, retorna um estado **de preparação** ou **corrida** assim que o trabalho é iniciado.

## <a name="monitor-a-remote-run"></a>Monitorizar uma execução remota

No total, a primeira corrida demora **cerca de 10 minutos.** Mas para as execuções subsequentes, desde que as dependências do guião não mudem, a mesma imagem é reutilizada. Assim, o tempo de arranque do contentor é muito mais rápido.

O que acontece enquanto espera:

- **Criação de imagem**: É criada uma imagem Docker que corresponde ao ambiente Python especificado pelo ambiente Azure ML. A imagem é carregada para a área de trabalho. A criação e upload de imagens demora cerca de **cinco minutos.**

  Esta fase acontece uma vez para cada ambiente Python porque o recipiente está em cache para as corridas subsequentes. Durante a criação da imagem, os registos são transmitidos para o histórico de execuções. Pode monitorizar o progresso da criação de imagens utilizando estes registos.

- **Dimensionamento**: Se o cluster remoto necessitar de mais nós para fazer a execução do que atualmente disponível, os nós adicionais são adicionados automaticamente. A escala normalmente demora **cerca de cinco minutos.**

- **Execução**: Nesta fase, os scripts e ficheiros necessários são enviados para o alvo do cálculo. Em seguida, as datastores são montadas ou copiadas. E então o **entry_script** é executado. Enquanto o trabalho está em execução, **o stdout** e o diretório **./logs** são transmitidos para o histórico de execução. Pode monitorizar o progresso da execução utilizando estes registos.

- **Pós-processamento**: O diretório **de ./outputs** da execução é copiado para o histórico de execução no seu espaço de trabalho, para que possa aceder a estes resultados.

Pode verificar o progresso de um trabalho em execução de várias maneiras. Este tutorial usa um widget Jupyter e um `wait_for_completion` método.

### <a name="jupyter-widget"></a>Widget Jupyter

Assista ao progresso da corrida com um [widget Jupyter.](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py&preserve-view=true) Tal como a submissão de corrida, o widget é assíncronos e fornece atualizações ao vivo a cada 10 a 15 segundos até que o trabalho termine:

```python
from azureml.widgets import RunDetails
RunDetails(run).show()
```

O widget será o seguinte no final do treino:

![Widget de portátil](./media/tutorial-train-models-with-aml/widget.png)

Se precisar de cancelar uma corrida, pode seguir [estas instruções](https://aka.ms/aml-docs-cancel-run).

### <a name="get-log-results-upon-completion"></a>Obter resultados do registo após a conclusão

A preparação e a monitorização do modelo são feitas em segundo plano. Espere até que o modelo termine o treino antes de executar mais código. Utilize `wait_for_completion` para mostrar quando o treino do modelo estiver terminado:

```python
run.wait_for_completion(show_output=False)  # specify True for a verbose log
```

### <a name="display-run-results"></a>Apresentar os resultados da execução

Tem agora um modelo preparado num cluster remoto. Obtenha a precisão do modelo:

```python
print(run.get_metrics())
```

A saída mostra que o modelo remoto tem uma precisão de 0.9204:

`{'regularization rate': 0.8, 'accuracy': 0.9204}`

No próximo tutorial, você explora este modelo com mais detalhes.

## <a name="register-model"></a>Registar o modelo

O último passo no guião de treino escreveu o ficheiro `outputs/sklearn_mnist_model.pkl` num diretório nomeado `outputs` no VM do cluster onde o trabalho é executado. `outputs` é um diretório especial na medida em que todos os conteúdos deste diretório são automaticamente enviados para o seu espaço de trabalho. Esses conteúdos aparecem no registo de execução na experimentação, na área de trabalho. Assim, o ficheiro modelo também está disponível no seu espaço de trabalho.

Pode ver ficheiros associados a essa execução:

```python
print(run.get_file_names())
```

Registe o modelo no espaço de trabalho, para que você ou outros colaboradores possam consultar, examinar e implementar este modelo:

```python
# register model
model = run.register_model(model_name='sklearn_mnist',
                           model_path='outputs/sklearn_mnist_model.pkl')
print(model.name, model.id, model.version, sep='\t')
```

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

Também pode eliminar apenas o cluster Azure Machine Learning Compute. No entanto, a autoescala é ligada e o mínimo do cluster é zero. Portanto, este recurso em particular não incorrerá em taxas de computação adicionais quando não estiver em uso:

```python
# Optionally, delete the Azure Machine Learning Compute cluster
compute_target.delete()
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial de Aprendizagem automática Azure, utilizou Python para as seguintes tarefas:

> [!div class="checklist"]
> * Crie o seu ambiente de desenvolvimento.
> * Aceda e examine os dados.
> * Treine vários modelos em um cluster remoto usando a popular biblioteca de aprendizagem de máquinas de aprendizagem de scikit-learn
> * Reveja os detalhes da formação e registe o melhor modelo.

Está pronto para implementar este modelo registado utilizando as instruções na próxima parte da série tutorial:

> [!div class="nextstepaction"]
> [Tutorial 2 - Deploy models](tutorial-deploy-models-with-aml.md) (Tutorial 2 - Implementar modelos)
