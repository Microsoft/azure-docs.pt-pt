---
title: 'Tutorial de classificação de imagem: Preparar modelos'
titleSuffix: Azure Machine Learning service
description: Este tutorial mostra como utilizar o serviço do Azure Machine Learning para preparar um modelo de classificação de imagens com scikit-learn num bloco de notas do Jupyter em Python. Este tutorial é a primeira parte de uma série composta por duas partes.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sdgilley
ms.author: sgilley
ms.date: 01/28/2019
ms.custom: seodec18
ms.openlocfilehash: cc0631052fa915ae946e791c907e405d70713ad2
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57860568"
---
# <a name="tutorial-train-an-image-classification-model-with-azure-machine-learning-service"></a>Tutorial: Preparar um modelo de classificação de imagem com o serviço Azure Machine Learning

Neste tutorial, preparar um modelo de machine learning nos recursos de computação remota. Vai utilizar o fluxo de trabalho de preparação e implementação do serviço do Azure Machine Learning (pré-visualização) num bloco de notas do Jupyter em Python.  Depois, pode utilizar o bloco de notas como um modelo para preparar o seu próprio modelo de machine learning com os seus dados. Este tutorial é a **primeira parte de uma série composta por duas partes**.  

Este tutorial prepara um regressão logística simple utilizando a [MNIST](http://yann.lecun.com/exdb/mnist/) conjunto de dados e [scikit-Saiba](https://scikit-learn.org) com o serviço Azure Machine Learning. O MNIST é um conjunto de dados popular que consiste em 70 000 imagens em tons de cinzento. Cada imagem é um dígito de manuscrito de pixels de 28 x 28, que representa um número entre zero e nove. O objetivo é criar um classificador de várias classes para identificar o dígito uma determinada imagem representa. 

Saiba como efetuar as seguintes ações:

> [!div class="checklist"]
> * Configure o ambiente de desenvolvimento.
> * Aceder e examinar os dados.
> * Prepare um modelo de regressão logística simples num cluster remoto.
> * Reveja os resultados de treinamento e registar o melhor modelo.

Saiba como selecionar um modelo e implementá-la no [parte dois deste tutorial](tutorial-deploy-models-with-aml.md). 

Se não tiver uma subscrição do Azure, crie uma conta gratuita antes de começar. Experimente o [uma versão gratuita ou paga do serviço Azure Machine Learning](https://aka.ms/AMLFree) hoje mesmo.

>[!NOTE]
> Código neste artigo foi testado com o Azure Machine Learning SDK versão 1.0.8.

## <a name="prerequisites"></a>Pré-requisitos

Avance para o [configurar o ambiente de desenvolvimento](#start) para ler os passos de bloco de notas ou utilize as instruções abaixo para obter o bloco de notas e executá-lo em blocos de notas do Azure ou no seu próprio servidor de bloco de notas.  Para executar o bloco de notas, terá de:

* Um servidor de bloco de notas do Python 3.6 com o seguinte instalado:
    * O Azure Machine Learning SDK para Python
    * `matplotlib` e `scikit-learn`
* O bloco de notas do tutorial e utils.py o ficheiro
* Uma área de trabalho do machine learning 
* O ficheiro de configuração para a área de trabalho no mesmo diretório como o bloco de notas 

Obter todos os estes pré-requisitos a partir de qualquer uma das seções abaixo.
 
* Utilize [blocos de notas do Azure](#azure) 
* Utilize [seu próprio servidor de bloco de notas](#server)

### <a name="azure"></a>Utilize blocos de notas do Azure: Blocos de notas do Jupyter gratuitos na cloud

É fácil começar com blocos de notas do Azure! O [do Azure Machine Learning SDK para Python](https://aka.ms/aml-sdk) já está instalado e configurado para si no [blocos de notas do Azure](https://notebooks.azure.com/). A instalação e as futuras atualizações são geridas automaticamente por meio de serviços do Azure.

Depois de concluir os passos abaixo, execute o **tutoriais/img-classificação-part1-training.ipynb** bloco de notas no seu **introdução** projeto.

[!INCLUDE [aml-azure-notebooks](../../../includes/aml-azure-notebooks.md)]


### <a name="server"></a>Utilizar o seu próprio servidor de bloco de notas do Jupyter

Utilize estes passos para criar um servidor de bloco de notas Jupyter local no seu computador.  Depois de concluir os passos, execute o **tutoriais/img-classificação-part1-training.ipynb** bloco de notas.

[!INCLUDE [aml-your-server](../../../includes/aml-your-server.md)]

## <a name="start"></a>Configurar o ambiente de desenvolvimento

Toda a configuração para o seu trabalho de desenvolvimento pode ser feita num bloco de notas Python. A configuração inclui as seguintes ações:

* Importe pacotes de Python.
* Ligar a uma área de trabalho, para que seu computador local pode comunicar com recursos remotos.
* Crie uma experimentação para controlar todas as suas execuções.
* Crie um destino de computação remota para utilizar para formação.

### <a name="import-packages"></a>Importar pacotes

Importe os pacotes Python de que precisa nesta sessão. Também mostra a versão do SDK do Azure Machine Learning:

```python
%matplotlib inline
import numpy as np
import matplotlib.pyplot as plt

import azureml.core
from azureml.core import Workspace

# check core SDK version number
print("Azure ML SDK Version: ", azureml.core.VERSION)
```

### <a name="connect-to-a-workspace"></a>Ligar a uma área de trabalho

Crie um objeto de área de trabalho a partir da área de trabalho existente. `Workspace.from_config()` lê o arquivo **config** e carrega os detalhes num objeto chamado `ws`:

```python
# load workspace configuration from the config.json file in the current folder.
ws = Workspace.from_config()
print(ws.name, ws.location, ws.resource_group, ws.location, sep = '\t')
```

### <a name="create-an-experiment"></a>Criar uma experimentação

Crie uma experimentação para acompanhar as execuções na sua área de trabalho. Uma área de trabalho pode ter múltiplas experimentações: 

```python
experiment_name = 'sklearn-mnist'

from azureml.core import Experiment
exp = Experiment(workspace=ws, name=experiment_name)
```

### <a name="create-or-attach-an-existing-compute-resource"></a>Criar ou ligar um recurso de computação existente

Ao utilizar a computação do Azure Machine Learning, um serviço gerido, cientistas de dados podem preparar os modelos de machine learning em clusters de máquinas virtuais do Azure. Os exemplos incluem VMs com suporte GPU. Neste tutorial, vai criar a computação do Azure Machine Learning como o seu ambiente de treinamento. O código abaixo cria os clusters de cálculo para caso ainda não existam na sua área de trabalho.

 **Criação da computação demora cerca de cinco minutos.** Se a computação já estiver na área de trabalho, o código utiliza-o e ignora o processo de criação.


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
    provisioning_config = AmlCompute.provisioning_configuration(vm_size = vm_size,
                                                                min_nodes = compute_min_nodes, 
                                                                max_nodes = compute_max_nodes)

    # create the cluster
    compute_target = ComputeTarget.create(ws, compute_name, provisioning_config)
    
    # can poll for a minimum number of nodes and for a specific timeout. 
    # if no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
     # For a more detailed view of current AmlCompute status, use get_status()
    print(compute_target.get_status().serialize())
```

Tem agora os pacotes e os recursos de computação necessários para preparar um modelo na cloud. 

## <a name="explore-data"></a>Explorar dados

Antes de preparar um modelo, precisa entender os dados que utilizam formá-lo. Também terá de copiar os dados para a cloud. Em seguida, pode ser acedido pelo seu ambiente de treinamento em nuvem. Nesta secção, irá aprender a efetuar as seguintes ações:

* Baixe o conjunto de dados MNIST.
* Exiba algumas imagens de exemplo.
* Carregar dados para a cloud.

### <a name="download-the-mnist-dataset"></a>Transferir o conjunto de dados MNIST

Transfira o conjunto de dados MNIST e guarde os ficheiros num diretório `data` localmente. Imagens e etiquetas para formação e teste são transferidas:


```python
import urllib.request
import os

data_folder = os.path.join(os.getcwd(), 'data')
os.makedirs(data_folder, exist_ok = True)

urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz', filename=os.path.join(data_folder, 'train-images.gz'))
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz', filename=os.path.join(data_folder, 'train-labels.gz'))
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz', filename=os.path.join(data_folder, 'test-images.gz'))
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz', filename=os.path.join(data_folder, 'test-labels.gz'))
```
Verá a saída semelhante a este: ```('./data/test-labels.gz', <http.client.HTTPMessage at 0x7f40864c77b8>)```

### <a name="display-some-sample-images"></a>Apresentar algumas imagens de exemplo

Carregue os ficheiros comprimidos para matrizes `numpy`. Em seguida, utilize `matplotlib` para desenhar 30 imagens aleatórias do conjunto de dados com as respetivas etiquetas acima das mesmas. Este passo requer uma `load_data` função que está incluída num `util.py` ficheiro. Este ficheiro está incluído na pasta de exemplo. Certifique-se de que é colocado na mesma pasta que este bloco de notas. O `load_data` função simplesmente analisa os arquivos compactados em matrizes de numpy:



```python
# make sure utils.py is in the same directory as this code
from utils import load_data

# note we also shrink the intensity values (X) from 0-255 to 0-1. This helps the model converge faster.
X_train = load_data(os.path.join(data_folder, 'train-images.gz'), False) / 255.0
X_test = load_data(os.path.join(data_folder, 'test-images.gz'), False) / 255.0
y_train = load_data(os.path.join(data_folder, 'train-labels.gz'), True).reshape(-1)
y_test = load_data(os.path.join(data_folder, 'test-labels.gz'), True).reshape(-1)

# now let's show some randomly chosen images from the traininng set.
count = 0
sample_size = 30
plt.figure(figsize = (16, 6))
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

![amostra aleatória das imagens](./media/tutorial-train-models-with-aml/digits.png)

Agora, tem uma ideia do aspeto destas imagens e do resultado previsto da predição.

### <a name="upload-data-to-the-cloud"></a>Carregar os dados para a cloud

Agora torne os dados acessível remotamente através do carregamento de dados do seu computador local para o Azure. Em seguida, pode ser acessado para treinamento remoto. O arquivo de dados é uma construção conveniente associada à sua área de trabalho para que possa carregar ou transferir dados. Também pode interagir com o mesmo de seus destinos de computação remota. Ele conta com uma conta de armazenamento de Blobs do Azure.

Os ficheiros MNIST são carregados num diretório chamado `mnist` na raiz do arquivo de dados:

```python
ds = ws.get_default_datastore()
print(ds.datastore_type, ds.account_name, ds.container_name)

ds.upload(src_dir=data_folder, target_path='mnist', overwrite=True, show_progress=True)
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
script_folder  = os.path.join(os.getcwd(), "sklearn-mnist")
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
clf = LogisticRegression(C=1.0/args.reg, random_state=42)
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

+ O script de treinamento lê um argumento para localizar o diretório que contém os dados. Quando submete o trabalho mais tarde, apontar para o arquivo de dados para este argumento: `parser.add_argument('--data-folder', type=str, dest='data_folder', help='data directory mounting point')`.

+ O script de treinamento salva seu modelo num diretório chamado **produz**: <br/>
`joblib.dump(value=clf, filename='outputs/sklearn_mnist_model.pkl')`.<br/>
Tudo o que for escrito neste diretório é carregado automaticamente para a sua área de trabalho. Aceder a seu modelo a partir deste diretório mais tarde no tutorial.
O ficheiro `utils.py` é referenciado a partir do script de preparação para carregar o conjunto de dados devidamente. Copie este script para a pasta de script, para que possa ser acedida, juntamente com o script de treinamento sobre o recurso remoto.


```python
import shutil
shutil.copy('utils.py', script_folder)
```


### <a name="create-an-estimator"></a>Criar simulador

Para submeter a execução, é utilizado um objeto de simulador. Crie o avaliador de executando o seguinte código para definir estes itens:

* O nome do objeto de calculadora, `est`.
* O diretório que contém os seus scripts. Todos os ficheiros neste diretório são carregados para os nós do cluster, para execução. 
* O destino de computação. Neste caso, usar o cluster de computação do Azure Machine Learning que criou.
* O nome de script de formação **train.py**.
* Parâmetros necessários do script do treinamento. 
* Pacotes de Python necessários para treinamento.

Neste tutorial, este destino é AmlCompute. Todos os ficheiros na pasta de script são carregados os nós do cluster para a execução. O **data_folder** está definido para utilizar o arquivo de dados, `ds.path('mnist').as_mount()`:

```python
from azureml.train.estimator import Estimator

script_params = {
    '--data-folder': ds.path('mnist').as_mount(),
    '--regularization': 0.8
}

est = Estimator(source_directory=script_folder,
                script_params=script_params,
                compute_target=compute_target,
                entry_script='train.py',
                conda_packages=['scikit-learn'])
```


### <a name="submit-the-job-to-the-cluster"></a>Submeter o trabalho para o cluster

Execute a experimentação ao enviar o objeto de Calculadora:

```python
run = exp.submit(config=est)
run
```

Uma vez que a chamada é assíncrona, ela retorna um **preparando** ou **em execução** estado assim que a tarefa é iniciada.

## <a name="monitor-a-remote-run"></a>Monitorizar uma execução remota

No total, a primeira execução leva **cerca de 10 minutos**. Mas, para as execuções subsequentes, desde que não altere as dependências de script, a mesma imagem é reutilizada. Portanto, o tempo de inicialização do contentor é muito mais rápido.

O que acontece enquanto espera:

- **Criação de imagem**: É criada uma imagem do Docker que corresponde ao ambiente de Python especificado pelo avaliador de. A imagem é carregada para a área de trabalho. Criação e a demora a carregar imagem **cerca de cinco minutos**. 

  Nesta fase ocorre uma vez para cada ambiente de Python, uma vez que o contêiner é colocado em cache para as execuções subsequentes. Durante a criação da imagem, os registos são transmitidos para o histórico de execuções. Pode monitorizar o progresso da criação da imagem ao utilizar estes registos.

- **Dimensionamento**: Se o cluster remoto requer mais nós para fazer a execução do que no momento, nós adicionais são adicionados automaticamente. Dimensionamento normalmente demora **cerca de cinco minutos.**

- **Executar**: Nesta fase, os scripts necessários e os ficheiros são enviados para o destino de computação. Em seguida, arquivos de dados são montados ou copiados. E, em seguida, o **entry_script** é executado. Enquanto a tarefa está em execução, **stdout** e o **. / registos** diretório são transmitidas em fluxo para o histórico de execuções. Pode monitorizar o progresso da execução utilizando estes registos.

- **Pós-processamento**: O **. / produz** diretório da execução é copiado para o histórico de execuções em sua área de trabalho, para que possa aceder esses resultados.


Pode verificar o progresso de uma tarefa de execução de várias formas. Este tutorial utiliza um widget do Jupyter e um `wait_for_completion` método. 

### <a name="jupyter-widget"></a>Widget Jupyter

Veja o progresso da execução com um widget Jupyter. Como o envio de execução, o widget é assíncrono e fornece as atualizações dinâmicas cada 10 a 15 segundos até que a tarefa é concluída:


```python
from azureml.widgets import RunDetails
RunDetails(run).show()
```

Isto ainda instantâneo é o widget mostrado no final de treinamento:

![widget de bloco de notas](./media/tutorial-train-models-with-aml/widget.png)

Se precisar de cancelar uma execução, pode seguir [estas instruções](https://aka.ms/aml-docs-cancel-run).

### <a name="get-log-results-upon-completion"></a>Obter resultados do registo após a conclusão

A preparação e a monitorização do modelo são feitas em segundo plano. Aguarde até que o modelo foi concluída treinamento antes de executar mais de código. Utilize `wait_for_completion` para mostrar terminou a preparação de modelos: 


```python
run.wait_for_completion(show_output=False) # specify True for a verbose log
```

### <a name="display-run-results"></a>Apresentar os resultados da execução

Tem agora um modelo preparado num cluster remoto. Obtenha a precisão do modelo:

```python
print(run.get_metrics())
```
O resultado mostra que o modelo de remoto tem a precisão das 0.9204:

`{'regularization rate': 0.8, 'accuracy': 0.9204}`

O próximo tutorial, explorar esse modelo em mais detalhes.

## <a name="register-model"></a>Registar o modelo

A última etapa no script de treinamento escreveu o ficheiro `outputs/sklearn_mnist_model.pkl` num diretório chamado `outputs` na VM do cluster onde a tarefa é executada. `outputs` é um diretório especial que todo o conteúdo neste diretório é automaticamente carregado para a área de trabalho. Esses conteúdos aparecem no registo de execução na experimentação, na área de trabalho. Portanto, o ficheiro de modelo agora também está disponível na sua área de trabalho.

Pode ver os ficheiros associados que executam:

```python
print(run.get_file_names())
```

Registe o modelo na área de trabalho, para que ou outros colaboradores podem posteriormente consultar, examinar e implementar este modelo:

```python
# register model 
model = run.register_model(model_name='sklearn_mnist', model_path='outputs/sklearn_mnist_model.pkl')
print(model.name, model.id, model.version, sep = '\t')
```

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

Também pode eliminar apenas o cluster de computação do Azure Machine Learning. No entanto, o dimensionamento automático está ativado e o cluster mínimo for igual a zero. Portanto, esses recursos não irão incorrer em custos de computação adicional quando não está em utilização:


```python
# optionally, delete the Azure Machine Learning Compute cluster
compute_target.delete()
```

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial de serviço do Azure Machine Learning, utilizou Python para as seguintes tarefas:

> [!div class="checklist"]
> * Configure o ambiente de desenvolvimento.
> * Aceder e examinar os dados.
> * Utilizar vários modelos num cluster remoto com a popular scikit-saiba a biblioteca de machine learning
> * Veja os detalhes de treinamento e registar o melhor modelo.

Está pronto para implementar este modelo registado com as instruções na próxima parte da série do tutorial:

> [!div class="nextstepaction"]
> [Tutorial 2 - Deploy models](tutorial-deploy-models-with-aml.md) (Tutorial 2 - Implementar modelos)
