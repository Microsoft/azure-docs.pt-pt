---
title: 'Tutorial de classificação de imagem: Modelos de comboio'
titleSuffix: Azure Machine Learning
description: Use o Azure Machine Learning para treinar um modelo de classificação de imagem com scikit-learn num caderno Python Jupyter. Este tutorial é a primeira parte de dois.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sdgilley
ms.author: sgilley
ms.date: 03/18/2020
ms.custom: seodec18
ms.openlocfilehash: bcc9e748cb5f88084b9cd3254654f9dc0fbc8aa1
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82115572"
---
# <a name="tutorial-train-image-classification-models-with-mnist-data-and-scikit-learn"></a>Tutorial: Treinar modelos de classificação de imagem com dados MNIST e scikit-learn 
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste tutorial, você treina um modelo de aprendizagem automática em recursos de computação remota. Você usará o fluxo de trabalho de treino e implantação para Azure Machine Learning em um caderno Python Jupyter.  Depois, pode utilizar o bloco de notas como um modelo para preparar o seu próprio modelo de machine learning com os seus dados. Este tutorial é a **primeira parte de uma série composta por duas partes**.  

Este tutorial treina uma simples regressão logística utilizando o conjunto de dados [MNIST](http://yann.lecun.com/exdb/mnist/) e [scikit-learn](https://scikit-learn.org) com o Azure Machine Learning. O MNIST é um conjunto de dados popular que consiste em 70 000 imagens em tons de cinzento. Cada imagem é um dígito manuscrito de 28 x 28 pixels, representando um número de zero a nove. O objetivo é criar um classificador multiclasses para identificar o dígito que uma determinada imagem representa.

Aprenda a tomar as seguintes ações:

> [!div class="checklist"]
> * Instale o seu ambiente de desenvolvimento.
> * Aceda e examine os dados.
> * Treine um modelo de regressão logística simples num aglomerado remoto.
> * Reveja os resultados da formação e registe o melhor modelo.

Aprende-se a selecionar um modelo e a implantá-lo na [segunda parte deste tutorial.](tutorial-deploy-models-with-aml.md)

Se não tiver uma subscrição do Azure, crie uma conta gratuita antes de começar. Experimente hoje a [versão gratuita ou paga do Azure Machine Learning.](https://aka.ms/AMLFree)

>[!NOTE]
> O código neste artigo foi testado com a versão 1.0.83 [do Azure Machine Learning SDK.](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)

## <a name="prerequisites"></a>Pré-requisitos

* Complete o [Tutorial: Começar a criar a sua primeira experiência Azure ML](tutorial-1st-experiment-sdk-setup.md) para:
    * Criar uma área de trabalho
    * Clone o caderno de tutoriais para a sua pasta no espaço de trabalho.
    * Crie uma instância computacional baseada em nuvem.

* Nos seus *tutoriais clonados/pasta de dados de classificação de imagem-mnist-mnist,* abra o caderno *img-classification-part1-training.ipynb.* 


O tutorial e o arquivo **de utils.py** acompanhante também estão disponíveis no [GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials) se desejar usá-lo no seu próprio [ambiente local.](how-to-configure-environment.md#local) Corra `pip install azureml-sdk[notebooks] azureml-opendatasets matplotlib` para instalar dependências para este tutorial.

> [!Important]
> O resto deste artigo contém o mesmo conteúdo que se vê no caderno.  
>
> Mude para o caderno Jupyter agora se quiser ler enquanto executa o código. 
> Para executar uma única célula de código num caderno, clique na célula de código e clique no **Shift+Enter**. Ou, executar todo o caderno escolhendo **Executar tudo** a partir da barra de ferramentas superior.

## <a name="set-up-your-development-environment"></a><a name="start"></a>Configurar o ambiente de desenvolvimento

Toda a configuração para o seu trabalho de desenvolvimento pode ser feita num bloco de notas Python. A configuração inclui as seguintes ações:

* Importar pacotes Python.
* Ligue-se a um espaço de trabalho, para que o seu computador local possa comunicar com recursos remotos.
* Crie uma experiência para rastrear todas as suas corridas.
* Crie um alvo de computação remota para usar para o treino.

### <a name="import-packages"></a>Importar pacotes

Importe os pacotes Python de que precisa nesta sessão. Exiba também a versão SDK de Aprendizagem automática Azure:

```python
%matplotlib inline
import numpy as np
import matplotlib.pyplot as plt

import azureml.core
from azureml.core import Workspace

# check core SDK version number
print("Azure ML SDK Version: ", azureml.core.VERSION)
```

### <a name="connect-to-a-workspace"></a>Ligar a um espaço de trabalho

Crie um objeto de área de trabalho a partir da área de trabalho existente. `Workspace.from_config()`lê o ficheiro **config.json** e coloca `ws`os detalhes num objeto chamado:

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

Ao utilizar a Azure Machine Learning Compute, um serviço gerido, os cientistas de dados podem treinar modelos de aprendizagem automática em conjuntos de máquinas virtuais Azure. Exemplos incluem VMs com suporte de GPU. Neste tutorial, cria a Computação de Aprendizagem automática Azure como ambiente de treino. Submeterá o código Python para executar este VM mais tarde no tutorial. 

O código abaixo cria os clusters de computação para si se eles já não existirem no seu espaço de trabalho.

 **A criação do alvo da computação demora cerca de cinco minutos.** Se o recurso computacional já estiver no espaço de trabalho, o código usa-o e ignora o processo de criação.

```python
from azureml.core.compute import AmlCompute
from azureml.core.compute import ComputeTarget
import os

# choose a name for your cluster
compute_name = os.environ.get("AML_COMPUTE_CLUSTER_NAME", "cpucluster")
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

Antes de treinar um modelo, precisa de compreender os dados que utiliza para treiná-lo. Nesta secção, vai aprender a:

* Descarregue o conjunto de dados MNIST.
* Mostrar algumas imagens da amostra.

### <a name="download-the-mnist-dataset"></a>Transferir o conjunto de dados MNIST

Utilize conjuntos de dados abertos azure para obter os ficheiros de dados mNIST crus. [Os Conjuntos de Dados Abertos do Azure](https://docs.microsoft.com/azure/open-datasets/overview-what-are-open-datasets) são conjuntos de dados públicos com curadoria que pode utilizar para adicionar funcionalidades específicas ao cenário a soluções de aprendizagem automática para modelos mais precisos. Cada conjunto de dados `MNIST` tem uma classe correspondente, neste caso, para recuperar os dados de diferentes formas.

Este código recupera os `FileDataset` dados como um objeto, `Dataset`que é uma subclasse de . Uma `FileDataset` referência de ficheiros únicos ou múltiplos de qualquer formato nas suas lojas de dados ou urls públicos. A classe fornece-lhe a capacidade de descarregar ou montar os ficheiros para a sua computação, criando uma referência à localização da fonte de dados. Além disso, regista o Dataset no seu espaço de trabalho para facilitar a recuperação durante o treino.

Siga o [como](how-to-create-register-datasets.md) saber mais sobre os Datasets e o seu uso no SDK.

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

Carregue os ficheiros comprimidos para matrizes `numpy`. Em seguida, utilize `matplotlib` para desenhar 30 imagens aleatórias do conjunto de dados com as respetivas etiquetas acima das mesmas. Este passo requer `load_data` uma função que `util.py` está incluída num ficheiro. Este ficheiro está incluído na pasta de exemplo. Certifique-se de que está colocado na mesma pasta que este caderno. A `load_data` função simplesmente analisa os ficheiros comprimidos em matrizes dormentes.

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

Para esta tarefa, submete-se ao trabalho para executar o grupo de treino remoto que criou anteriormente.  Para submeter um trabalho, tem de:
* Criar um diretório
* Criar um script de preparação
* Criar um objeto estimador
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

+ O roteiro de treino lê um argumento para encontrar o diretório que contém os dados. Quando submete o trabalho posteriormente, tem de apontar para o arquivo de dados deste argumento: ```parser.add_argument('--data-folder', type=str, dest='data_folder', help='data directory mounting point')```

+ O guião de treino salva o seu modelo num diretório chamado **outputs**. Tudo o que for escrito neste diretório é carregado automaticamente para a sua área de trabalho. Acede ao seu modelo a partir deste diretório mais tarde no tutorial. `joblib.dump(value=clf, filename='outputs/sklearn_mnist_model.pkl')`

+ O script de treino `utils.py` requer que o ficheiro carregue corretamente o conjunto de dados. As seguintes `utils.py` `script_folder` cópias de código para que o ficheiro possa ser acedido juntamente com o script de treino no recurso remoto.

  ```python
  import shutil
  shutil.copy('utils.py', script_folder)
  ```

### <a name="create-an-estimator"></a>Criar simulador

Para submeter a execução, é utilizado um objeto de simulador. O Azure Machine Learning tem estimativas pré-configuradas para quadros comuns de aprendizagem automática, bem como estimativas genéricas. Criar um estimador especificando


* O nome do objeto de avaliador, `est`.
* O diretório que contém os seus scripts. Todos os ficheiros neste diretório são carregados para os nós do cluster, para execução.
* O destino de computação. Neste caso, irá utilizar o cluster de computação do Azure Machine Learning que criou.
* O nome do guião de treino, **train.py.**
* Um ambiente que contém as bibliotecas necessárias para executar o guião.
* Os parâmetros necessários do script de preparação.

Neste tutorial, este alvo é amlCompute. Todos os ficheiros da pasta de script são enviados para os nós do cluster para serem executados. O **data_folder** está definido para usar o conjunto de dados. "Em primeiro lugar, criar o ambiente que contém: a biblioteca de aprendizagem de scikit, a preparação de dados em azureml necessária para aceder ao conjunto de dados, e os incumprimentos em azul que contêm as dependências para a exploração de métricas. Os incumprimentos em azureml também contêm as dependências necessárias para a implementação do modelo como um serviço web mais tarde na parte 2 do tutorial.

Uma vez definido o ambiente, registe-o com o Espaço de Trabalho para o reutilizar na parte 2 do tutorial.

```python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies

# to install required packages
env = Environment('tutorial-env')
cd = CondaDependencies.create(pip_packages=['azureml-dataprep[pandas,fuse]>=1.1.14', 'azureml-defaults'], conda_packages = ['scikit-learn==0.22.1'])

env.python.conda_dependencies = cd

# Register environment to re-use later
env.register(workspace = ws)
```

Em seguida, crie o estimador com o seguinte código.

```python
from azureml.train.estimator import Estimator

script_params = {
    # to mount files referenced by mnist dataset
    '--data-folder': mnist_file_dataset.as_named_input('mnist_opendataset').as_mount(),
    '--regularization': 0.5
}

est = Estimator(source_directory=script_folder,
              script_params=script_params,
              compute_target=compute_target,
              environment_definition=env,
              entry_script='train.py')
```

### <a name="submit-the-job-to-the-cluster"></a>Submeter o trabalho para o cluster

Executar a experiência submetendo o objeto estimador:

```python
run = exp.submit(config=est)
run
```

Como a chamada é assíncrona, devolve um estado de **preparação** ou **execução** assim que o trabalho é iniciado.

## <a name="monitor-a-remote-run"></a>Monitorizar uma execução remota

No total, a primeira corrida leva cerca de **10 minutos.** Mas para os ensaios subsequentes, desde que as dependências do guião não mudem, a mesma imagem é reutilizada. Assim, o tempo de arranque do contentor é muito mais rápido.

O que acontece enquanto espera:

- **Criação de imagem**: É criada uma imagem Do Docker que corresponde ao ambiente Python especificado pelo estimador. A imagem é carregada para a área de trabalho. A criação de imagem e o upload demoram cerca de **cinco minutos**.

  Esta fase acontece uma vez para cada ambiente Python porque o recipiente está em cache para corridas subsequentes. Durante a criação da imagem, os registos são transmitidos para o histórico de execuções. Pode monitorizar o progresso da criação de imagem utilizando estes registos.

- **Escalação**: Se o cluster remoto necessitar de mais nós para fazer o funcionamento do que atualmente disponível, os nós adicionais são adicionados automaticamente. O escalonamento normalmente leva cerca de **cinco minutos.**

- **Execução**: Nesta fase, os scripts e ficheiros necessários são enviados para o alvo da computação. Em seguida, as lojas de dados são montadas ou copiadas. E então o **entry_script** é executado. Enquanto o trabalho está em execução, o **stdout** e o diretório **./logs** são transmitidos para a história da execução. Pode monitorizar o progresso da corrida utilizando estes registos.

- **Pós-processamento**: O diretório **./outputs** da corrida é copiado para o histórico de execução no seu espaço de trabalho, para que possa aceder a estes resultados.

Pode verificar o progresso de um trabalho em execução de várias maneiras. Este tutorial usa um widget `wait_for_completion` Jupyter e um método.

### <a name="jupyter-widget"></a>Widget Jupyter

Assista ao progresso da corrida com um [widget Jupyter.](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py) Tal como a submissão de execução, o widget é assíncrono e fornece atualizações ao vivo a cada 10 a 15 segundos até que o trabalho termine:

```python
from azureml.widgets import RunDetails
RunDetails(run).show()
```

O widget será parecido com o seguinte no final do treino:

![Widget de caderno](./media/tutorial-train-models-with-aml/widget.png)

Se precisar cancelar uma corrida, pode seguir [estas instruções.](https://aka.ms/aml-docs-cancel-run)

### <a name="get-log-results-upon-completion"></a>Obter resultados do registo após a conclusão

A preparação e a monitorização do modelo são feitas em segundo plano. Espere até que o modelo termine o treino antes de executar mais código. Use `wait_for_completion` para mostrar quando o treino do modelo está terminado:

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

O último passo no guião `outputs/sklearn_mnist_model.pkl` de treino `outputs` escreveu o ficheiro num diretório nomeado no VM do cluster onde o trabalho é executado. `outputs`é um diretório especial na medida em que todos os conteúdos deste diretório são automaticamente enviados para o seu espaço de trabalho. Esses conteúdos aparecem no registo de execução na experimentação, na área de trabalho. Assim, o ficheiro modelo está agora também disponível no seu espaço de trabalho.

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

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

Também pode eliminar apenas o cluster Azure Machine Learning Compute. No entanto, a escala automática é ligada, e o mínimo de cluster é zero. Portanto, este recurso em particular não incorrerá em encargos adicionais de cálculo quando não estiver em uso:

```python
# Optionally, delete the Azure Machine Learning Compute cluster
compute_target.delete()
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial de Aprendizagem automática Azure, utilizou python para as seguintes tarefas:

> [!div class="checklist"]
> * Instale o seu ambiente de desenvolvimento.
> * Aceda e examine os dados.
> * Treine vários modelos em um cluster remoto usando a popular scikit-learn machine learning biblioteca
> * Reveja os detalhes do treino e registe o melhor modelo.

Está pronto para implementar este modelo registado utilizando as instruções na próxima parte da série tutorial:

> [!div class="nextstepaction"]
> [Tutorial 2 - Deploy models](tutorial-deploy-models-with-aml.md) (Tutorial 2 - Implementar modelos)
