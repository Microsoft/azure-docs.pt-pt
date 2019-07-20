---
title: 'Tutorial de classificação de imagem: Implementar modelos'
titleSuffix: Azure Machine Learning service
description: Este tutorial mostra como utilizar o serviço do Azure Machine Learning para implementar um modelo de classificação de imagens com scikit-learn num bloco de notas Jupyter em Python. Este tutorial é o segundo de uma série de duas partes.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sdgilley
ms.author: sgilley
ms.date: 05/14/2019
ms.custom: seodec18
ms.openlocfilehash: c673fd43abe6808256eb74f435aad48ed8d41539
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359837"
---
# <a name="tutorial-deploy-an-image-classification-model-in-azure-container-instances"></a>Tutorial: Implantar um modelo de classificação de imagem em instâncias de contêiner do Azure

Este tutorial é a **segunda parte de uma série composta por duas partes**. No [tutorial anterior](tutorial-train-models-with-aml.md), preparou os modelos de machine learning e registou um modelo na sua área de trabalho na cloud.  

Agora você está pronto para implantar o modelo como um serviço Web em [instâncias de contêiner do Azure](https://docs.microsoft.com/azure/container-instances/). Um serviço Web é uma imagem, neste caso, uma imagem do Docker. Ele encapsula a lógica de Pontuação e o próprio modelo. 

Nesta parte do tutorial, você usa Azure Machine Learning serviço para as seguintes tarefas:

> [!div class="checklist"]
> * Configure seu ambiente de teste.
> * Recupere o modelo do seu espaço de trabalho.
> * Teste o modelo localmente.
> * Implante o modelo em instâncias de contêiner.
> * Teste o modelo implantado.

As instâncias de contêiner são uma ótima solução para testar e compreender o fluxo de trabalho. Dimensionável para implementações de produção, considere utilizar o Azure Kubernetes Service. Para obter mais informações, consulte [como implantar e onde](how-to-deploy-and-where.md).

>[!NOTE]
> O código deste artigo foi testado com Azure Machine Learning SDK versão 1.0.41.

## <a name="prerequisites"></a>Pré-requisitos
Pule para [definir o ambiente de desenvolvimento](#start) para ler as etapas do bloco de anotações.  

Para executar o bloco de anotações, primeiro conclua o [treinamento do modelo no tutorial (parte 1): Treine um modelo de classificação de imagem com](tutorial-train-models-with-aml.md)o serviço Azure Machine Learning.   Em seguida, execute o bloco de anotações **tutoriais/img-Classification-part2-Deploy. ipynb** usando o mesmo servidor de bloco de anotações.


## <a name="start"></a>Configurar o ambiente

Comece por configurar um ambiente de teste.

### <a name="import-packages"></a>Importar pacotes

Importe os pacotes do python necessários para este tutorial:

```python
%matplotlib inline
import numpy as np
import matplotlib
import matplotlib.pyplot as plt
 
import azureml
from azureml.core import Workspace, Run

# display the core SDK version number
print("Azure ML SDK Version: ", azureml.core.VERSION)
```

### <a name="retrieve-the-model"></a>Obter o modelo

No tutorial anterior, registou um modelo na sua área de trabalho. Agora carregue este espaço de trabalho e baixe o modelo em seu diretório local:


```python
from azureml.core import Workspace
from azureml.core.model import Model
import os
ws = Workspace.from_config()
model = Model(ws, 'sklearn_mnist')

model.download(target_dir=os.getcwd(), exist_ok=True)

# verify the downloaded model file
file_path = os.path.join(os.getcwd(), "sklearn_mnist_model.pkl")

os.stat(file_path)
```

## <a name="test-the-model-locally"></a>Testar o modelo localmente

Antes de implantar, verifique se seu modelo está funcionando localmente:
* Carregar dados de teste.
* Prever dados de teste.
* Examine a matriz de confusão.

### <a name="load-test-data"></a>Carregar os dados de teste

Carregue os dados de teste do diretório **./Data/** criado durante o tutorial de treinamento:

```python
from utils import load_data
import os

data_folder = os.path.join(os.getcwd(), 'data')
# note we also shrink the intensity values (X) from 0-255 to 0-1. This helps the neural network converge faster
X_test = load_data(os.path.join(data_folder, 'test-images.gz'), False) / 255.0
y_test = load_data(os.path.join(
    data_folder, 'test-labels.gz'), True).reshape(-1)
```

### <a name="predict-test-data"></a>Prever os dados de teste

Para obter previsões, alimentar o conjunto de teste ao modelo:

```python
import pickle
from sklearn.externals import joblib

clf = joblib.load(os.path.join(os.getcwd(), 'sklearn_mnist_model.pkl'))
y_hat = clf.predict(X_test)
```

###  <a name="examine-the-confusion-matrix"></a>Examinar a matriz de confusão

Gere uma matriz de confusão para ver quantas amostras do conjunto de teste foram classificadas corretamente. Observe o valor erroneamente classificado para as previsões incorretas: 

```python
from sklearn.metrics import confusion_matrix

conf_mx = confusion_matrix(y_test, y_hat)
print(conf_mx)
print('Overall accuracy:', np.average(y_hat == y_test))
```

A saída mostra a matriz de confusão:

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
   

Utilize `matplotlib` para apresentar a matriz de confusão como um gráfico. Nesse grafo, o eixo x mostra os valores reais e o eixo y mostra os valores previstos. A cor em cada grade mostra a taxa de erros. Quanto mais clara for a cor, mais alta é a taxa de erros. Por exemplo, muitos 5 são classificados erroneamente como 3. Você verá uma grade brilhante em (5, 3):

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

![Gráfico mostrando a matriz de confusão](./media/tutorial-deploy-models-with-aml/confusion.png)

## <a name="deploy-as-a-web-service"></a>Implantar como um serviço Web

Depois de testar o modelo e se você estiver satisfeito com os resultados, implante o modelo como um serviço Web hospedado em instâncias de contêiner. 

Para criar o ambiente correto para as instâncias de contêiner, forneça os seguintes componentes:
* Um script de Pontuação para mostrar como usar o modelo.
* Um arquivo de ambiente para mostrar quais pacotes precisam ser instalados.
* Um arquivo de configuração para criar a instância de contêiner.
* O modelo treinado anteriormente.

<a name="make-script"></a>

### <a name="create-scoring-script"></a>Criar o script de classificação

Crie o script de pontuação, chamado **score.py**. A chamada de serviço Web usa esse script para mostrar como usar o modelo.

Inclua essas duas funções necessárias no script de Pontuação:
* A função `init()`, que, geralmente, carrega o modelo para um objeto global. Esta função só é executada uma vez, quando o contentor do Docker é iniciado. 

* A função `run(input_data)` utiliza o modelo para prever um valor com base nos dados de entrada. Regra geral, as entradas e as saídas da execução utilizam JSON para a serialização e a desserialização, mas são suportados outros formatos.

```python
%%writefile score.py
import json
import numpy as np
import os
import pickle
from sklearn.externals import joblib
from sklearn.linear_model import LogisticRegression

from azureml.core.model import Model

def init():
    global model
    # retrieve the path to the model file using the model name
    model_path = Model.get_model_path('sklearn_mnist')
    model = joblib.load(model_path)

def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # make prediction
    y_hat = model.predict(data)
    # you can return any data type as long as it is JSON-serializable
    return y_hat.tolist()
```

<a name="make-myenv"></a>

### <a name="create-environment-file"></a>Criar o ficheiro de ambiente

Em seguida, crie um arquivo de ambiente, chamado **MyENV. yml**, que especifica todas as dependências de pacote do script. Esse arquivo é usado para garantir que todas essas dependências sejam instaladas na imagem do Docker. Esse modelo precisa `scikit-learn` de `azureml-sdk`e:

```python
from azureml.core.conda_dependencies import CondaDependencies

myenv = CondaDependencies()
myenv.add_conda_package("scikit-learn")

with open("myenv.yml", "w") as f:
    f.write(myenv.serialize_to_string())
```
Examine o conteúdo do `myenv.yml` arquivo:

```python
with open("myenv.yml", "r") as f:
    print(f.read())
```

### <a name="create-a-configuration-file"></a>Criar um arquivo de configuração

Crie um arquivo de configuração de implantação. Especifique o número de CPUs e gigabytes de RAM necessários para o contêiner de instâncias de contêiner. Embora isso dependa de seu modelo, o padrão de um núcleo e 1 gigabyte de RAM é suficiente para muitos modelos. Se precisar de mais informações posteriormente, você precisará recriar a imagem e reimplantar o serviço.

```python
from azureml.core.webservice import AciWebservice

aciconfig = AciWebservice.deploy_configuration(cpu_cores=1,
                                               memory_gb=1,
                                               tags={"data": "MNIST",
                                                     "method": "sklearn"},
                                               description='Predict MNIST with sklearn')
```

### <a name="deploy-in-container-instances"></a>Implantar em instâncias de contêiner
O tempo estimado para concluir a implantação é de **cerca de sete a oito minutos**.

Configure a imagem e implemente. O código abaixo realiza estes passos:

1. Crie uma imagem usando estes arquivos:
   * O arquivo de Pontuação `score.py`,.
   * O arquivo de ambiente `myenv.yml`,.
   * O arquivo de modelo.
1. Registre a imagem no espaço de trabalho. 
1. Envie a imagem para o contêiner de instâncias de contêiner.
1. Inicie um contêiner em instâncias de contêiner usando a imagem.
1. Obtenha o ponto final HTTP do serviço Web.


```python
%%time
from azureml.core.webservice import Webservice
from azureml.core.image import ContainerImage

# configure the image
image_config = ContainerImage.image_configuration(execution_script="score.py", 
                                                  runtime="python", 
                                                  conda_file="myenv.yml")

service = Webservice.deploy_from_model(workspace=ws,
                                       name='sklearn-mnist-svc',
                                       deployment_config=aciconfig,
                                       models=[model],
                                       image_config=image_config)

service.wait_for_deployment(show_output=True)
```

Obtenha o ponto final HTTP do serviço Web de classificação, que aceita chamadas do cliente REST. Você pode compartilhar esse ponto de extremidade com qualquer pessoa que queira testar o serviço Web ou integrá-lo a um aplicativo: 

```python
print(service.scoring_uri)
```


## <a name="test-the-deployed-service"></a>Testar o serviço implantado

Anteriormente, você pontuau todos os dados de teste com a versão local do modelo. Agora você pode testar o modelo implantado com uma amostra aleatória de 30 imagens dos dados de teste.  

O código abaixo realiza estes passos:
1. Envie os dados como uma matriz JSON para o serviço Web hospedado em instâncias de contêiner. 

1. Utilize a API `run` do SDK para invocar o serviço. Você também pode fazer chamadas brutas usando qualquer ferramenta HTTP, como **ondulação**.

1. Imprima as predições devolvidas e represente-as juntamente com as imagens de entrada. A fonte vermelha e a imagem inversa, branca em preto, são usadas para realçar os exemplos de classificação incorretamente. 

Como a precisão do modelo é alta, talvez seja necessário executar o código a seguir algumas vezes antes de ver um exemplo de classificação incorreta:

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
plt.figure(figsize=(20, 1))

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

Esse resultado é de uma amostra aleatória de imagens de teste:

![Gráfico mostrando resultados](./media/tutorial-deploy-models-with-aml/results.png)

Você também pode enviar uma solicitação HTTP bruta para testar o serviço Web:

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

## <a name="clean-up-resources"></a>Limpar recursos

Para manter o grupo de recursos e o espaço de trabalho para outros tutoriais e explorações, você pode excluir apenas a implantação de instâncias de contêiner usando essa chamada à API:

```python
service.delete()
```

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]


## <a name="next-steps"></a>Passos Seguintes

+ Saiba mais sobre todas as [Opções de implantação para Azure Machine Learning serviço](how-to-deploy-and-where.md).
+ Saiba como [criar clientes para o serviço Web](how-to-consume-web-service.md).
+  [Faça previsões em grandes quantidades de dados de](how-to-run-batch-predictions.md) forma assíncrona.
+ Monitore seus modelos de Azure Machine Learning com [Application insights](how-to-enable-app-insights.md).
+ Experimente o tutorial de [seleção de algoritmo automático](tutorial-auto-train-models.md) . 
