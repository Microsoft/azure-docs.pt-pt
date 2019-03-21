---
title: 'Tutorial de classificação de imagem: Implementar modelos'
titleSuffix: Azure Machine Learning service
description: Este tutorial mostra como utilizar o serviço do Azure Machine Learning para implementar um modelo de classificação de imagens com scikit-learn num bloco de notas Jupyter em Python. Este tutorial é a segunda de uma série de duas partes.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sdgilley
ms.author: sgilley
ms.date: 01/29/2019
ms.custom: seodec18
ms.openlocfilehash: a8f3a5c6a1c7adaff620f8675fcffa4018eb9874
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58133098"
---
# <a name="tutorial-deploy-an-image-classification-model-in-azure-container-instances"></a>Tutorial: Implementar um modelo de classificação de imagem no Azure Container Instances

Este tutorial é a **segunda parte de uma série composta por duas partes**. No [tutorial anterior](tutorial-train-models-with-aml.md), preparou os modelos de machine learning e registou um modelo na sua área de trabalho na cloud.  

Agora, está pronto para implementar o modelo como um serviço web no [Azure Container Instances](https://docs.microsoft.com/azure/container-instances/). Um serviço web é uma imagem, neste caso uma imagem do Docker. Ele encapsula a lógica de classificação e o próprio modelo. 

Nesta parte do tutorial, vai utilizar o serviço Azure Machine Learning para as seguintes tarefas:

> [!div class="checklist"]
> * Configure o ambiente de teste.
> * Obter o modelo de sua área de trabalho.
> * Teste o modelo localmente.
> * Implemente o modelo para o Container Instances.
> * Teste o modelo implementado.

Instâncias de contentor é uma ótima solução para testar e compreender o fluxo de trabalho. Dimensionável para implementações de produção, considere utilizar o Azure Kubernetes Service. Para obter mais informações, consulte [como implementar e, em que](how-to-deploy-and-where.md).

>[!NOTE]
> Código neste artigo foi testado com o Azure Machine Learning SDK versão 1.0.8.

## <a name="prerequisites"></a>Pré-requisitos
Avance para o [defina o ambiente de desenvolvimento](#start) para ler os passos de bloco de notas.  

Para executar o bloco de notas, concluir a preparação de modelos em [Tutorial (parte 1): Preparar um modelo de classificação de imagem com o serviço Azure Machine Learning](tutorial-train-models-with-aml.md).   Em seguida, execute o **tutoriais/img-classificação-parte 2-deploy.ipynb** bloco de notas com o mesmo servidor de bloco de notas.


## <a name="start"></a>Configurar o ambiente

Comece por configurar um ambiente de teste.

### <a name="import-packages"></a>Importar pacotes

Importe os pacotes Python necessários para este tutorial:

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

No tutorial anterior, registou um modelo na sua área de trabalho. Agora, esta área de trabalho de carga e transfira o modelo para o diretório no local:


```python
from azureml.core import Workspace
from azureml.core.model import Model
import os 
ws = Workspace.from_config()
model=Model(ws, 'sklearn_mnist')

model.download(target_dir=os.getcwd(), exist_ok=True)

# verify the downloaded model file
file_path = os.path.join(os.getcwd(), "sklearn_mnist_model.pkl")

os.stat(file_path)
```

## <a name="test-the-model-locally"></a>Testar o modelo localmente

Antes de implementar, certifique-se de que o seu modelo está a funcionar localmente:
* Dados de teste de carga.
* Prever os dados de teste.
* Examine a matriz de confusão.

### <a name="load-test-data"></a>Carregar os dados de teste

Carregar os dados de teste a partir da **. /data/** diretório criado durante o tutorial de treinamento:

```python
from utils import load_data
import os

data_folder = os.path.join(os.getcwd(), 'data')
# note we also shrink the intensity values (X) from 0-255 to 0-1. This helps the neural network converge faster
X_test = load_data(os.path.join(data_folder, 'test-images.gz'), False) / 255.0
y_test = load_data(os.path.join(data_folder, 'test-labels.gz'), True).reshape(-1)
```

### <a name="predict-test-data"></a>Prever os dados de teste

Para obter previsões de indisponibilidade, feed o conjunto de dados de teste para o modelo:

```python
import pickle
from sklearn.externals import joblib

clf = joblib.load( os.path.join(os.getcwd(), 'sklearn_mnist_model.pkl'))
y_hat = clf.predict(X_test)
```

###  <a name="examine-the-confusion-matrix"></a>Examinar a matriz de confusão

Gere uma matriz de confusão para ver quantas amostras do conjunto de teste foram classificadas corretamente. Tenha em atenção o valor misclassified para previsões incorretas: 

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
   

Utilize `matplotlib` para apresentar a matriz de confusão como um gráfico. Neste gráfico, o eixo x mostra os valores reais e o eixo y mostra os valores previstos. A cor em cada grelha mostra a taxa de erros. Quanto mais clara for a cor, mais alta é a taxa de erros. Por exemplo, o número 5 são classificadas erroneamente como do 3. Para ver uma grade brilhante em (5,3):

```python
# normalize the diagonal cells so that they don't overpower the rest of the cells when visualized
row_sums = conf_mx.sum(axis=1, keepdims=True)
norm_conf_mx = conf_mx / row_sums
np.fill_diagonal(norm_conf_mx, 0)

fig = plt.figure(figsize=(8,5))
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

![Matriz de confusão do gráfico que mostra](./media/tutorial-deploy-models-with-aml/confusion.png)

## <a name="deploy-as-a-web-service"></a>Implementar como um serviço web

Depois de testado o modelo e estiver satisfeito com os resultados, implemente o modelo como um serviço web hospedado no Container Instances. 

Para criar o ambiente correto para o Container Instances, forneça os seguintes componentes:
* Um script de classificação para mostrar como utilizar o modelo.
* Um ficheiro de ambiente para mostrar o que pacotes precisam ser instalados.
* Um ficheiro de configuração para criar a instância de contentor.
* O modelo preparado anteriormente.

<a name="make-script"></a>

### <a name="create-scoring-script"></a>Criar o script de classificação

Criar o script de classificação, chamado **score.py**. A chamada de serviço web utiliza este script para mostrar como utilizar o modelo.

Inclua essas duas funções necessárias no script de classificação:
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

Em seguida, crie um ficheiro de ambiente, chamado **myenv.yml**, que especifica que todas as dependências de pacote do script. Este ficheiro é utilizado para se certificar de que todas essas dependências estão instaladas na imagem do Docker. Este modelo necessita `scikit-learn` e `azureml-sdk`:

```python
from azureml.core.conda_dependencies import CondaDependencies 

myenv = CondaDependencies()
myenv.add_conda_package("scikit-learn")

with open("myenv.yml","w") as f:
    f.write(myenv.serialize_to_string())
```
Reveja o conteúdo a `myenv.yml` ficheiro:

```python
with open("myenv.yml","r") as f:
    print(f.read())
```

### <a name="create-a-configuration-file"></a>Criar um ficheiro de configuração

Crie um ficheiro de configuração de implementação. Especifique o número de CPUs e gigabytes de RAM necessário para o contentor de instâncias de contentor. Embora ele depende do seu modelo, a predefinição de um núcleo e 1 gigabyte de RAM são suficientes para número de modelos. Se precisar de mais licenças posteriormente, terá de voltar a criar a imagem e reimplemente o serviço.

```python
from azureml.core.webservice import AciWebservice

aciconfig = AciWebservice.deploy_configuration(cpu_cores=1, 
                                               memory_gb=1, 
                                               tags={"data": "MNIST",  "method" : "sklearn"}, 
                                               description='Predict MNIST with sklearn')
```

### <a name="deploy-in-container-instances"></a>Implementar no Container Instances
O tempo estimado para concluir a implementação é **cerca de sete a oito minutos**.

Configure a imagem e implemente. O código abaixo realiza estes passos:

1. Crie uma imagem com esses arquivos:
   * O ficheiro de classificação, `score.py`.
   * O ficheiro de ambiente, `myenv.yml`.
   * O ficheiro de modelo.
1. Registre-se a imagem na área de trabalho. 
1. Envie a imagem para o contentor de instâncias de contentor.
1. Inicie um contentor nas instâncias de contentor utilizando a imagem.
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

Obtenha o ponto final HTTP do serviço Web de classificação, que aceita chamadas do cliente REST. Pode partilhar este ponto final com qualquer pessoa que queira testar o serviço web ou integrá-lo num aplicativo: 

```python
print(service.scoring_uri)
```


## <a name="test-the-deployed-service"></a>Testar o serviço implementado

Anteriormente, classificada a todos os dados de teste com a versão local do modelo. Agora pode testar o modelo implementado com uma amostra aleatória das 30 imagens a partir de dados de teste.  

O código abaixo realiza estes passos:
1. Enviar os dados como um JSON de matriz ao serviço web alojado no Container Instances. 

1. Utilize a API `run` do SDK para invocar o serviço. Também pode fazer chamadas não processadas com qualquer ferramenta HTTP, tal como **curl**.

1. Imprima as predições devolvidas e represente-as juntamente com as imagens de entrada. Tipo de letra vermelho e imagem inversa, branca em preto, é utilizado para realçar os exemplos misclassified. 

Uma vez que a precisão do modelo é elevada, poderá ter de executar o código a seguir algumas vezes antes de pode ver um exemplo misclassified:

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
    
    plt.text(x=10, y =-10, s=result[i], fontsize=18, color=font_color)
    plt.imshow(X_test[s].reshape(28, 28), cmap=clr_map)
    
    i = i + 1
plt.show()
```

Este resultado é de uma amostra aleatória das imagens de teste:

![Gráfico com resultados](./media/tutorial-deploy-models-with-aml/results.png)

Também pode enviar um pedido HTTP não processado para testar o serviço web:

```python
import requests

# send a random row from the test set to score
random_index = np.random.randint(0, len(X_test)-1)
input_data = "{\"data\": [" + str(list(X_test[random_index])) + "]}"

headers = {'Content-Type':'application/json'}

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

Para manter o grupo de recursos e a área de trabalho para outros tutoriais e a exploração, pode eliminar apenas a implementação de instâncias de contentor utilizando esta chamada à API:

```python
service.delete()
```

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]


## <a name="next-steps"></a>Passos Seguintes

+ Saiba mais sobre todos os [opções de implementação para o serviço Azure Machine Learning](how-to-deploy-and-where.md).
+ Saiba como [criar clientes para o serviço web](how-to-consume-web-service.md).
+  [Fazer previsões sobre grandes quantidades de dados](how-to-run-batch-predictions.md) forma assíncrona.
+ Monitorizar os seus modelos do Azure Machine Learning com [Application Insights](how-to-enable-app-insights.md).
+ Experimente o [seleção de algoritmo automática](tutorial-auto-train-models.md) tutorial. 
