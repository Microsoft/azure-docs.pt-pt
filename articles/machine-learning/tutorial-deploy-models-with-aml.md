---
title: 'Tutorial de classificação de imagem: Implementar modelos'
titleSuffix: Azure Machine Learning
description: Este tutorial mostra como usar a Azure Machine Learning para implementar um modelo de classificação de imagem com scikit-learn em um caderno Python Jupyter.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sdgilley
ms.author: sgilley
ms.date: 03/18/2020
ms.custom: seodec18
ms.openlocfilehash: 0981d325b3c5982793ada480a87afc48bf58acf7
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106066583"
---
# <a name="tutorial-deploy-an-image-classification-model-in-azure-container-instances"></a>Tutorial: Implementar um modelo de classificação de imagem em Instâncias de Contentores Azure


Este tutorial é a **segunda parte de uma série composta por duas partes**. No [tutorial anterior](tutorial-train-models-with-aml.md), preparou os modelos de machine learning e registou um modelo na sua área de trabalho na cloud.  Agora está pronto para implementar o modelo como um serviço web. Um serviço web é uma imagem, neste caso uma imagem do Docker. Encapsula a lógica de pontuação e o próprio modelo. 

Nesta parte do tutorial, utiliza-se a Azure Machine Learning para as seguintes tarefas:

> [!div class="checklist"]
> * Configurar o seu ambiente de testes.
> * Recupere o modelo do seu espaço de trabalho.
> * Desloque o modelo para as instâncias do contentor.
> * Teste o modelo implantado.

As instâncias de contentores são uma ótima solução para testar e compreender o fluxo de trabalho. Relativamente a implementação de produção dimensionáveis, considere utilizar o Azure Kubernetes Service. Para mais informações, consulte [como implementar e onde.](how-to-deploy-and-where.md)

>[!NOTE]
> O código deste artigo foi testado com a versão 1.0.83 do Azure Machine Learning SDK.

## <a name="prerequisites"></a>Pré-requisitos

Para executar o caderno, primeiro complete o modelo de formação em [Tutorial (parte 1): Treine um modelo de classificação de imagem.](tutorial-train-models-with-aml.md)   Em seguida, abra o caderno *img-classification-part2-deploy.ipynb* na sua pasta *de tutoriais clonados/classificação de imagem-mnist-data.*

Este tutorial também está disponível no [GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials) se desejar usá-lo no seu próprio [ambiente local.](how-to-configure-environment.md#local)  Certifique-se de que instalou `matplotlib` e `scikit-learn` no seu ambiente. 

> [!Important]
> O resto deste artigo contém o mesmo conteúdo que se vê no caderno.  
>
> Mude agora para o caderno Jupyter se quiser ler enquanto executar o código.
> Para executar uma única célula de código num bloco de notas, clique na célula de código e clique no **Shift+Enter**. Ou, executar todo o caderno escolhendo **Executar tudo** a partir da barra de ferramentas superior.

## <a name="set-up-the-environment"></a><a name="start"></a>Configurar o ambiente

Comece por configurar um ambiente de teste.

### <a name="import-packages"></a>Importar pacotes

Importe os pacotes de Python necessários para este tutorial.


```python
%matplotlib inline
import numpy as np
import matplotlib.pyplot as plt
 
import azureml.core

# Display the core SDK version number
print("Azure ML SDK Version: ", azureml.core.VERSION)
```

## <a name="deploy-as-web-service"></a>Implementar como serviço Web

Implemente o modelo como um serviço web hospedado no ACI. 

Para criar o ambiente certo para o ACI, forneça o seguinte:
* Um script de classificação para mostrar como utilizar o modelo
* Um ficheiro de configuração para criar o ACI
* O modelo que preparou anteriormente

### <a name="create-scoring-script"></a>Criar o script de classificação

Crie o script de classificação, denominado score.py, que a chamada do serviço Web utiliza para mostrar como o modelo deve ser utilizado.

Tem de incluir duas funções obrigatórias no script de classificação:
* A função `init()`, que, geralmente, carrega o modelo para um objeto global. Esta função só é executada uma vez, quando o contentor do Docker é iniciado. 

* A função `run(input_data)` utiliza o modelo para prever um valor com base nos dados de entrada. Regra geral, as entradas e as saídas da execução utilizam JSON para a serialização e a desserialização, mas são suportados outros formatos.

```python
%%writefile score.py
import json
import numpy as np
import os
import pickle
import joblib

def init():
    global model
    # AZUREML_MODEL_DIR is an environment variable created during deployment.
    # It is the path to the model folder (./azureml-models/$MODEL_NAME/$VERSION)
    # For multiple models, it points to the folder containing all deployed models (./azureml-models)
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    model = joblib.load(model_path)

def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # make prediction
    y_hat = model.predict(data)
    # you can return any data type as long as it is JSON-serializable
    return y_hat.tolist()
```

### <a name="create-configuration-file"></a>Criar um ficheiro de configuração

Crie um ficheiro de configuração de implementação e indique o número de CPUs e de gigabytes de RAM necessários para o contentor do ACI. Embora dependa do seu modelo, a predefinição de 1 núcleo e 1 gigabyte de RAM costuma ser suficiente para a maioria dos modelos. Se precisar de adicionar mais no futuro, terá de recriar a imagem e reimplementar o serviço.


```python
from azureml.core.webservice import AciWebservice

aciconfig = AciWebservice.deploy_configuration(cpu_cores=1, 
                                               memory_gb=1, 
                                               tags={"data": "MNIST",  "method" : "sklearn"}, 
                                               description='Predict MNIST with sklearn')
```

### <a name="deploy-in-aci"></a>Implementar no ACI
Tempo estimado para completar: **cerca de 2-5 minutos**

Configure a imagem e implemente. O código abaixo realiza estes passos:

1. Criar objetos ambientais que contenham dependências necessárias pelo modelo utilizando o ambiente ( ) salvo durante o `tutorial-env` treino.
1. Criar configuração de inferência necessária para implementar o modelo como um serviço web utilizando:
   * O ficheiro de classificação (`score.py`)
   * objeto de ambiente criado em passo anterior
1. Desloque o modelo para o recipiente ACI.
1. Obtenha o ponto final HTTP do serviço Web.


```python
%%time
from azureml.core.webservice import Webservice
from azureml.core.model import InferenceConfig
from azureml.core.environment import Environment
from azureml.core import Workspace
from azureml.core.model import Model

ws = Workspace.from_config()
model = Model(ws, 'sklearn_mnist')


myenv = Environment.get(workspace=ws, name="tutorial-env", version="1")
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

service = Model.deploy(workspace=ws, 
                       name='sklearn-mnist-svc3', 
                       models=[model], 
                       inference_config=inference_config, 
                       deployment_config=aciconfig)

service.wait_for_deployment(show_output=True)
```

Obtenha o ponto final HTTP do serviço Web de classificação, que aceita chamadas do cliente REST. Esse ponto final pode ser partilhado com qualquer pessoa que queira testar o serviço Web ou integrá-lo numa aplicação.


```python
print(service.scoring_uri)
```

## <a name="test-the-model"></a>Testar o modelo


### <a name="download-test-data"></a>Baixar dados de teste
Faça o download dos dados do teste para o **./data/diretório**


```python
import os
from azureml.core import Dataset
from azureml.opendatasets import MNIST

data_folder = os.path.join(os.getcwd(), 'data')
os.makedirs(data_folder, exist_ok=True)

mnist_file_dataset = MNIST.get_file_dataset()
mnist_file_dataset.download(data_folder, overwrite=True)
```

### <a name="load-test-data"></a>Carregar os dados de teste

Carregue os dados de teste a partir do diretório **./data/** que foi criado durante o tutorial de preparação.


```python
from utils import load_data
import os
import glob

data_folder = os.path.join(os.getcwd(), 'data')
# note we also shrink the intensity values (X) from 0-255 to 0-1. This helps the neural network converge faster
X_test = load_data(glob.glob(os.path.join(data_folder,"**/t10k-images-idx3-ubyte.gz"), recursive=True)[0], False) / 255.0
y_test = load_data(glob.glob(os.path.join(data_folder,"**/t10k-labels-idx1-ubyte.gz"), recursive=True)[0], True).reshape(-1)
```

### <a name="predict-test-data"></a>Prever os dados de teste

Insira o conjunto de dados de teste no modelo, para obter previsões.


O código abaixo realiza estes passos:
1. Envie os dados como uma matriz JSON ao serviço Web alojado no ACI. 

1. Utilize a API `run` do SDK para invocar o serviço. Também pode fazer chamadas diretas com qualquer ferramenta HTTP, como curl.


```python
import json
test = json.dumps({"data": X_test.tolist()})
test = bytes(test, encoding='utf8')
y_hat = service.run(input_data=test)
```

###  <a name="examine-the-confusion-matrix"></a>Examinar a matriz de confusão

Gere uma matriz de confusão para ver quantas amostras do conjunto de teste foram classificadas corretamente. Observe o valor “mal classificada” nas previsões incorretas.


```python
from sklearn.metrics import confusion_matrix

conf_mx = confusion_matrix(y_test, y_hat)
print(conf_mx)
print('Overall accuracy:', np.average(y_hat == y_test))
```

A saída mostra a matriz de confusão:

```output
[[ 960    0    1    2    1    5    6    3    1    1]
 [   0 1112    3    1    0    1    5    1   12    0]
 [   9    8  920   20   10    4   10   11   37    3]
 [   4    0   17  921    2   21    4   12   20    9]
 [   1    2    5    3  915    0   10    2    6   38]
 [  10    2    0   41   10  770   17    7   28    7]
 [   9    3    7    2    6   20  907    1    3    0]
 [   2    7   22    5    8    1    1  950    5   27]
 [  10   15    5   21   15   27    7   11  851   12]
 [   7    8    2   13   32   13    0   24   12  898]]
Overall accuracy: 0.9204
```

Utilize `matplotlib` para apresentar a matriz de confusão como um gráfico. Neste gráfico, o eixo X representa os valores reais e o Y os valores previstos. A cor em cada grelha representa a taxa de erros. Quanto mais clara for a cor, mais alta é a taxa de erros. Por exemplo, muitos 5 foram mal classificados como 3. Assim, vê-se uma grelha brilhante em (5,3).

```python
# normalize the diagonal cells so that they don't overpower the rest of the cells when visualized
row_sums = conf_mx.sum(axis=1, keepdims=True)
norm_conf_mx = conf_mx / row_sums
np.fill_diagonal(norm_conf_mx, 0)

fig = plt.figure(figsize=(8, 5))
ax = fig.add_subplot(111)
cax = ax.matshow(norm_conf_mx, cmap=plt.cm.bone)
ticks = np.arange(0, 10, 1)
ax.set_xticks(ticks)
ax.set_yticks(ticks)
ax.set_xticklabels(ticks)
ax.set_yticklabels(ticks)
fig.colorbar(cax)
plt.ylabel('true labels', fontsize=14)
plt.xlabel('predicted values', fontsize=14)
plt.savefig('conf.png')
plt.show()
```

![Gráfico mostrando matriz de confusão](./media/tutorial-deploy-models-with-aml/confusion.png)


## <a name="show-predictions"></a>Mostrar previsões

Teste o modelo implantado com uma amostra aleatória de 30 imagens dos dados do teste.  


1. Imprima as predições devolvidas e represente-as juntamente com as imagens de entrada. Para realçar as amostras mal classificadas, é utilizado um tipo de letra vermelho e uma imagem inversa (branca ou preta). 

 Uma vez que a precisão do modelo é elevada, poderá ter de executar o seguinte código algumas vezes até conseguir ver uma amostra mal classificada.



```python
import json

# find 30 random samples from test set
n = 30
sample_indices = np.random.permutation(X_test.shape[0])[0:n]

test_samples = json.dumps({"data": X_test[sample_indices].tolist()})
test_samples = bytes(test_samples, encoding='utf8')

# predict using the deployed model
result = service.run(input_data=test_samples)

# compare actual value vs. the predicted values:
i = 0
plt.figure(figsize = (20, 1))

for s in sample_indices:
    plt.subplot(1, n, i + 1)
    plt.axhline('')
    plt.axvline('')
    
    # use different color for misclassified sample
    font_color = 'red' if y_test[s] != result[i] else 'black'
    clr_map = plt.cm.gray if y_test[s] != result[i] else plt.cm.Greys
    
    plt.text(x=10, y=-10, s=result[i], fontsize=18, color=font_color)
    plt.imshow(X_test[s].reshape(28, 28), cmap=clr_map)
    
    i = i + 1
plt.show()
```

Também pode enviar um pedido HTTP direto para testar o serviço Web.


```python
import requests

# send a random row from the test set to score
random_index = np.random.randint(0, len(X_test)-1)
input_data = "{\"data\": [" + str(list(X_test[random_index])) + "]}"

headers = {'Content-Type': 'application/json'}

# for AKS deployment you'd need to the service key in the header as well
# api_key = service.get_key()
# headers = {'Content-Type':'application/json',  'Authorization':('Bearer '+ api_key)} 

resp = requests.post(service.scoring_uri, input_data, headers=headers)

print("POST to url", service.scoring_uri)
#print("input data:", input_data)
print("label:", y_test[random_index])
print("prediction:", resp.text)
```

## <a name="clean-up-resources"></a>Limpar os recursos

Para manter o grupo de recursos e o espaço de trabalho para outros tutoriais e exploração, pode eliminar apenas a implantação de Instâncias de Contentores utilizando esta chamada API:

```python
service.delete()
```

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]


## <a name="next-steps"></a>Passos seguintes

+ Saiba mais sobre todas as opções de [implementação para Azure Machine Learning](how-to-deploy-and-where.md).
+ Saiba como [criar clientes para o serviço web.](how-to-consume-web-service.md)
+  [Faça previsões sobre grandes quantidades de dados](./tutorial-pipeline-batch-scoring-classification.md) assíncroneamente.
+ Monitorize os seus modelos de aprendizagem automática Azure com [Insights de Aplicação](how-to-enable-app-insights.md).
+ Experimente o tutorial [de seleção automática de algoritmos.](tutorial-auto-train-models.md)