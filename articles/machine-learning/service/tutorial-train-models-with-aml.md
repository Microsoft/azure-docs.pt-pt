---
title: 'Tutorial de classificação de imagem: Preparar modelos'
titleSuffix: Azure Machine Learning service
description: Saiba como treinar um modelo de classificação de imagem com o scikit-Aprenda em um notebook Jupyter do Python com o serviço Azure Machine Learning. Este tutorial é a primeira parte de uma série composta por duas partes.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sdgilley
ms.author: sgilley
ms.date: 08/20/2019
ms.custom: seodec18
ms.openlocfilehash: 90f745d3ef5fd4442a184a51d82cd61b12828e15
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/26/2019
ms.locfileid: "70036198"
---
# <a name="tutorial-train-image-classification-models-with-mnist-data-and-scikit-learn-using-azure-machine-learning"></a>Tutorial: Treinar modelos de classificação de imagem com dados do MNIST e scikit-aprender a usar Azure Machine Learning

Neste tutorial, você treinará um modelo de aprendizado de máquina em recursos de computação remota. Vai utilizar o fluxo de trabalho de preparação e implementação do serviço do Azure Machine Learning (pré-visualização) num bloco de notas do Jupyter em Python.  Depois, pode utilizar o bloco de notas como um modelo para preparar o seu próprio modelo de machine learning com os seus dados. Este tutorial é a **primeira parte de uma série composta por duas partes**.  

Este tutorial treina uma regressão logística simples usando o conjunto de informações [MNIST](http://yann.lecun.com/exdb/mnist/) e o [scikit-Aprenda](https://scikit-learn.org) com o serviço Azure Machine Learning. O MNIST é um conjunto de dados popular que consiste em 70 000 imagens em tons de cinzento. Cada imagem é um dígito manuscrito de 28 x 28 pixels, representando um número de zero a nove. O objetivo é criar um classificador multiclasse para identificar o dígito que uma determinada imagem representa.

Saiba como executar as seguintes ações:

> [!div class="checklist"]
> * Configure seu ambiente de desenvolvimento.
> * Acesse e examine os dados.
> * Treinar um modelo de regressão logística simples em um cluster remoto.
> * Examine os resultados de treinamento e registre o melhor modelo.

Você aprende a selecionar um modelo e implantá-lo na [parte dois deste tutorial](tutorial-deploy-models-with-aml.md).

Se você não tiver uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do serviço de Azure Machine Learning](https://aka.ms/AMLFree) hoje mesmo.

>[!NOTE]
> O código deste artigo foi testado com Azure Machine Learning SDK versão 1.0.41.

## <a name="prerequisites"></a>Pré-requisitos

* Conclua [o tutorial: Comece a criar seu primeiro experimento](tutorial-1st-experiment-sdk-setup.md) de ml para:
    * Criar uma área de trabalho
    * Criar um servidor de notebook de nuvem
    * Iniciar o painel do bloco de anotações do Jupyter

* Depois de iniciar o painel do notebook Jupyter, abra o bloco de anotações **tutoriais/img-Classification-part1-Training. ipynb** .

O tutorial e o arquivo **utils.py** acompanhante também estarão disponíveis no [GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials) se você quiser usá-lo em seu próprio [ambiente local](how-to-configure-environment.md#local).  Verifique se você instalou `matplotlib` o e `scikit-learn` o em seu ambiente.


## <a name="start"></a>Configurar seu ambiente de desenvolvimento

Toda a configuração para o seu trabalho de desenvolvimento pode ser feita num bloco de notas Python. A instalação inclui as seguintes ações:

* Importar pacotes do Python.
* Conecte-se a um espaço de trabalho, para que seu computador local possa se comunicar com recursos remotos.
* Crie um experimento para acompanhar todas as suas execuções.
* Crie um destino de computação remota a ser usado para treinamento.

### <a name="import-packages"></a>Importar pacotes

Importe os pacotes Python de que precisa nesta sessão. Exiba também a versão Azure Machine Learning SDK:

```python
%matplotlib inline
import numpy as np
import matplotlib.pyplot as plt

import azureml.core
from azureml.core import Workspace

# check core SDK version number
print("Azure ML SDK Version: ", azureml.core.VERSION)
```

### <a name="connect-to-a-workspace"></a>Conectar-se a um espaço de trabalho

Crie um objeto de área de trabalho a partir da área de trabalho existente. `Workspace.from_config()`lê o arquivo **config. JSON** e carrega os detalhes em um objeto chamado `ws`:

```python
# load workspace configuration from the config.json file in the current folder.
ws = Workspace.from_config()
print(ws.name, ws.location, ws.resource_group, sep='\t')
```

### <a name="create-an-experiment"></a>Criar uma experimentação

Crie uma experimentação para acompanhar as execuções na sua área de trabalho. Um espaço de trabalho pode ter vários experimentos:

```python
from azureml.core import Experiment
experiment_name = 'sklearn-mnist'

exp = Experiment(workspace=ws, name=experiment_name)
```

### <a name="create-or-attach-an-existing-compute-target"></a>Criar ou anexar um destino de computação existente

Usando Azure Machine Learning computação, um serviço gerenciado, os cientistas de dados podem treinar modelos de aprendizado de máquina em clusters de máquinas virtuais do Azure. Exemplos incluem VMs com suporte a GPU. Neste tutorial, você criará Azure Machine Learning computação como seu ambiente de treinamento. O código a seguir criará os clusters de computadores para você se eles ainda não existirem no seu espaço de trabalho.

 **A criação do destino de computação leva cerca de cinco minutos.** Se o recurso de computação já estiver no espaço de trabalho, o código o usará e ignorará o processo de criação.

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

Antes de treinar um modelo, você precisa entender os dados que usa para treiná-lo. Você também precisa copiar os dados para a nuvem. Em seguida, ele pode ser acessado pelo seu ambiente de treinamento de nuvem. Nesta seção, você aprenderá a executar as seguintes ações:

* Baixe o conjunto de MNIST.
* Exibir algumas imagens de exemplo.
* Carregar dados para a nuvem.

### <a name="download-the-mnist-dataset"></a>Transferir o conjunto de dados MNIST

Transfira o conjunto de dados MNIST e guarde os ficheiros num diretório `data` localmente. Imagens e rótulos para treinamento e teste são baixados:

```python
import urllib.request
import os

data_folder = os.path.join(os.getcwd(), 'data')
os.makedirs(data_folder, exist_ok=True)

urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz',
                           filename=os.path.join(data_folder, 'train-images.gz'))
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz',
                           filename=os.path.join(data_folder, 'train-labels.gz'))
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz',
                           filename=os.path.join(data_folder, 'test-images.gz'))
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz',
                           filename=os.path.join(data_folder, 'test-labels.gz'))
```

Você verá uma saída semelhante a esta:```('./data/test-labels.gz', <http.client.HTTPMessage at 0x7f40864c77b8>)```

### <a name="display-some-sample-images"></a>Apresentar algumas imagens de exemplo

Carregue os ficheiros comprimidos para matrizes `numpy`. Em seguida, utilize `matplotlib` para desenhar 30 imagens aleatórias do conjunto de dados com as respetivas etiquetas acima das mesmas. Esta etapa requer uma `load_data` função que está incluída em um `util.py` arquivo. Este ficheiro está incluído na pasta de exemplo. Verifique se ele está posicionado na mesma pasta que este bloco de anotações. A `load_data` função simplesmente analisa os arquivos compactados em matrizes numpy:

```python
# make sure utils.py is in the same directory as this code
from utils import load_data

# note we also shrink the intensity values (X) from 0-255 to 0-1. This helps the model converge faster.
X_train = load_data(os.path.join(
    data_folder, 'train-images.gz'), False) / 255.0
X_test = load_data(os.path.join(data_folder, 'test-images.gz'), False) / 255.0
y_train = load_data(os.path.join(
    data_folder, 'train-labels.gz'), True).reshape(-1)
y_test = load_data(os.path.join(
    data_folder, 'test-labels.gz'), True).reshape(-1)

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

### <a name="upload-data-to-the-cloud"></a>Carregar os dados para a cloud

Você baixou e usou os dados de treinamento no computador em que seu notebook está sendo executado.  Na próxima seção, você treinará um modelo na computação de Azure Machine Learning remota.  O recurso de computação remota também precisará de acesso aos seus dados. Para fornecer acesso, carregue seus dados em um repositório de armazenamento centralizado associado ao seu espaço de trabalho. Esse repositório de dados fornece acesso rápido aos seus dados ao usar destinos de computação remota na nuvem, como no data center do Azure.

Carregue os arquivos MNIST em um diretório chamado `mnist` na raiz do repositório de armazenamento. Confira [acessar dados de seus armazenamentos](how-to-access-data.md) para obter mais informações.

```python
ds = ws.get_default_datastore()
print(ds.datastore_type, ds.account_name, ds.container_name)

ds.upload(src_dir=data_folder, target_path='mnist',
          overwrite=True, show_progress=True)
```

Tem agora tudo aquilo de que precisa para começar a preparar um modelo.

## <a name="train-on-a-remote-cluster"></a>Preparar num cluster remoto

Para esta tarefa, submeta o trabalho para o cluster de preparação remoto que configurou anteriormente.  Para submeter um trabalho, tem de:
* Criar um diretório
* Criar um script de preparação
* Criar um objeto de Calculadora
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

from sklearn.linear_model import LogisticRegression
from sklearn.externals import joblib

from azureml.core import Run
from utils import load_data

# let user feed in 2 parameters, the location of the data files (from datastore), and the regularization rate of the logistic regression model
parser = argparse.ArgumentParser()
parser.add_argument('--data-folder', type=str, dest='data_folder', help='data folder mounting point')
parser.add_argument('--regularization', type=float, dest='reg', default=0.01, help='regularization rate')
args = parser.parse_args()

data_folder = args.data_folder
print('Data folder:', data_folder)

# load train and test set into numpy arrays
# note we scale the pixel intensity values to 0-1 (by dividing it with 255.0) so the model can converge faster.
X_train = load_data(os.path.join(data_folder, 'train-images.gz'), False) / 255.0
X_test = load_data(os.path.join(data_folder, 'test-images.gz'), False) / 255.0
y_train = load_data(os.path.join(data_folder, 'train-labels.gz'), True).reshape(-1)
y_test = load_data(os.path.join(data_folder, 'test-labels.gz'), True).reshape(-1)
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

+ O script de treinamento lê um argumento para localizar o diretório que contém os dados. Quando submete o trabalho posteriormente, tem de apontar para o arquivo de dados deste argumento: ```parser.add_argument('--data-folder', type=str, dest='data_folder', help='data directory mounting point')```

+ O script de treinamento salva seu modelo em um diretóriochamado Outputs. Tudo o que for escrito neste diretório é carregado automaticamente para a sua área de trabalho. Você acessará seu modelo a partir desse diretório posteriormente no tutorial. `joblib.dump(value=clf, filename='outputs/sklearn_mnist_model.pkl')`

+ O script de treinamento requer que `utils.py` o arquivo carregue o conjunto de os corretamente. O código a seguir `utils.py` copia `script_folder` para que o arquivo possa ser acessado junto com o script de treinamento no recurso remoto.

  ```python
  import shutil
  shutil.copy('utils.py', script_folder)
  ```

### <a name="create-an-estimator"></a>Criar simulador

Um objeto de [estimador SKLearn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) é usado para enviar a execução. Crie seu estimador executando o seguinte código para definir esses itens:

* O nome do objeto estimador, `est`.
* O diretório que contém os seus scripts. Todos os ficheiros neste diretório são carregados para os nós do cluster, para execução.
* O destino de computação. Nesse caso, você usa o cluster de computação Azure Machine Learning que você criou.
* O nome do script de treinamento, **Train.py**.
* Parâmetros necessários do script de treinamento.

Neste tutorial, este destino é AmlCompute. Todos os arquivos na pasta de script são carregados nos nós de cluster para execução. O **DATA_FOLDER** está definido para usar o repositório de armazenamento `ds.path('mnist').as_mount()`,:

```python
from azureml.train.sklearn import SKLearn

script_params = {
    '--data-folder': ds.path('mnist').as_mount(),
    '--regularization': 0.5
}

est = SKLearn(source_directory=script_folder,
              script_params=script_params,
              compute_target=compute_target,
              entry_script='train.py')
```

### <a name="submit-the-job-to-the-cluster"></a>Submeter o trabalho para o cluster

Execute o experimento enviando o objeto estimador:

```python
run = exp.submit(config=est)
run
```

Como a chamada é assíncrona, ela retorna um estado de **preparação** ou de **execução** assim que o trabalho é iniciado.

## <a name="monitor-a-remote-run"></a>Monitorizar uma execução remota

No total, a primeira execução leva **cerca de 10 minutos**. Mas, para execuções subsequentes, desde que as dependências de script não sejam alteradas, a mesma imagem é reutilizada. Portanto, o tempo de inicialização do contêiner é muito mais rápido.

O que acontece enquanto você espera:

- **Criação de imagem**: É criada uma imagem do Docker que corresponde ao ambiente do Python especificado pelo estimador. A imagem é carregada para a área de trabalho. A criação e o carregamento de imagens leva **cerca de cinco minutos**.

  Esse estágio ocorre uma vez para cada ambiente do Python porque o contêiner é armazenado em cache para execuções subsequentes. Durante a criação da imagem, os registos são transmitidos para o histórico de execuções. Você pode monitorar o progresso da criação da imagem usando esses logs.

- **Dimensionamento**: Se o cluster remoto exigir mais nós para executar a execução que está disponível no momento, nós adicionais serão adicionados automaticamente. O dimensionamento normalmente leva **cerca de cinco minutos.**

- **Em execução**: Neste estágio, os scripts e arquivos necessários são enviados para o destino de computação. Em seguida, os repositórios de armazenamento são montados ou copiados. Em seguida, o **entry_script** é executado. Enquanto o trabalho está em execução, **stdout** e o diretório **./logs** são transmitidos para o histórico de execução. Você pode monitorar o progresso da execução usando esses logs.

- **Pós-processamento**: O diretório **./Outputs** da execução é copiado para o histórico de execução em seu espaço de trabalho, para que você possa acessar esses resultados.

Você pode verificar o progresso de um trabalho em execução de várias maneiras. Este tutorial usa um widget Jupyter e um `wait_for_completion` método.

### <a name="jupyter-widget"></a>Widget Jupyter

Observe o progresso da execução com um [widget Jupyter](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py). Como o envio de execução, o widget é assíncrono e fornece atualizações dinâmicas a cada 10 a 15 segundos até que o trabalho seja concluído:

```python
from azureml.widgets import RunDetails
RunDetails(run).show()
```

O widget se parecerá com o seguinte no final do treinamento:

![Widget Notebook](./media/tutorial-train-models-with-aml/widget.png)

Se precisar cancelar uma execução, você poderá seguir [estas instruções](https://aka.ms/aml-docs-cancel-run).

### <a name="get-log-results-upon-completion"></a>Obter resultados do registo após a conclusão

A preparação e a monitorização do modelo são feitas em segundo plano. Aguarde até que o modelo tenha concluído o treinamento antes de executar mais código. Use `wait_for_completion` para mostrar quando o treinamento do modelo foi concluído:

```python
run.wait_for_completion(show_output=False)  # specify True for a verbose log
```

### <a name="display-run-results"></a>Apresentar os resultados da execução

Tem agora um modelo preparado num cluster remoto. Obtenha a precisão do modelo:

```python
print(run.get_metrics())
```

A saída mostra que o modelo remoto tem precisão de 0,9204:

`{'regularization rate': 0.8, 'accuracy': 0.9204}`

No próximo tutorial, você explora esse modelo com mais detalhes.

## <a name="register-model"></a>Registar o modelo

A última etapa no script de treinamento escreveu o arquivo `outputs/sklearn_mnist_model.pkl` em um diretório chamado `outputs` na VM do cluster em que o trabalho é executado. `outputs`é um diretório especial no qual todo o conteúdo desse diretório é carregado automaticamente em seu espaço de trabalho. Esses conteúdos aparecem no registo de execução na experimentação, na área de trabalho. Portanto, o arquivo de modelo agora também está disponível no seu espaço de trabalho.

Você pode ver os arquivos associados a essa execução:

```python
print(run.get_file_names())
```

Registre o modelo no espaço de trabalho, para que você ou outros colaboradores possam consultar, examinar e implantar posteriormente esse modelo:

```python
# register model
model = run.register_model(model_name='sklearn_mnist',
                           model_path='outputs/sklearn_mnist_model.pkl')
print(model.name, model.id, model.version, sep='\t')
```

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

Você também pode excluir apenas o cluster de computação Azure Machine Learning. No entanto, o dimensionamento automático é ativado e o mínimo do cluster é zero. Portanto, esse recurso específico não incorrerá em cobranças de computação adicionais quando não estiver em uso:

```python
# optionally, delete the Azure Machine Learning Compute cluster
compute_target.delete()
```

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial de serviço Azure Machine Learning, você usou o Python para as seguintes tarefas:

> [!div class="checklist"]
> * Configure seu ambiente de desenvolvimento.
> * Acesse e examine os dados.
> * Treinar vários modelos em um cluster remoto usando a popular biblioteca de aprendizado de máquina scikit-Learn
> * Examine os detalhes de treinamento e registre o melhor modelo.

Você está pronto para implantar esse modelo registrado usando as instruções na próxima parte da série de tutoriais:

> [!div class="nextstepaction"]
> [Tutorial 2 - Deploy models](tutorial-deploy-models-with-aml.md) (Tutorial 2 - Implementar modelos)
