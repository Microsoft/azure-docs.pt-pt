---
title: 'Tutorial de classificação de imagem: Implementar modelos'
titleSuffix: Azure Machine Learning
description: Este tutorial, segundo de uma série de duas partes, mostra como usar o Azure Machine Learning para implementar um modelo de classificação de imagem com scikit-learn num caderno Python Jupyter.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sdgilley
ms.author: sgilley
ms.date: 02/10/2020
ms.custom: seodec18
ms.openlocfilehash: 81e02492f7e79b87e1513a910afe4719908adbbb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "80159090"
---
# <a name="tutorial-deploy-an-image-classification-model-in-azure-container-instances"></a>Tutorial: Implementar um modelo de classificação de imagem em instâncias de contentores azure
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Este tutorial é a **segunda parte de uma série composta por duas partes**. No [tutorial anterior](tutorial-train-models-with-aml.md), preparou os modelos de machine learning e registou um modelo na sua área de trabalho na cloud.  Agora está pronto para implementar o modelo como um serviço web. Um serviço web é uma imagem, neste caso uma imagem do Docker. Encapsula a lógica de pontuação e o próprio modelo. 

Nesta parte do tutorial, utiliza-se o Azure Machine Learning para as seguintes tarefas:

> [!div class="checklist"]
> * Instale o seu ambiente de teste.
> * Recupere o modelo do seu espaço de trabalho.
> * Teste o modelo localmente.
> * Desloque o modelo para instâncias de contentores.
> * Teste o modelo implantado.

As Instâncias de Contentores são uma ótima solução para testar e compreender o fluxo de trabalho. Relativamente a implementação de produção dimensionáveis, considere utilizar o Azure Kubernetes Service. Para mais informações, veja [como implementar e onde.](how-to-deploy-and-where.md)

>[!NOTE]
> O código neste artigo foi testado com a versão 1.0.41 do Azure Machine Learning SDK.

## <a name="prerequisites"></a>Pré-requisitos

Para executar o caderno, complete primeiro o model training em [Tutorial (parte 1): Treine um modelo](tutorial-train-models-with-aml.md)de classificação de imagem .   Em seguida, abra o notebook *img-classification-part2-deploy.ipynb* nos seus *tutoriais clonados/pasta de dados mnist-de classificação de imagem.*

Este tutorial também está disponível no [GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials) se desejar usá-lo no seu próprio [ambiente local.](how-to-configure-environment.md#local)  Certifique-se de `matplotlib` que `scikit-learn` instalou e no seu ambiente. 

> [!Important]
> O resto deste artigo contém o mesmo conteúdo que se vê no caderno.  
>
> Mude para o caderno Jupyter agora se quiser ler enquanto executa o código.
> Para executar uma única célula de código num caderno, clique na célula de código e clique no **Shift+Enter**. Ou, executar todo o caderno escolhendo **Executar tudo** a partir da barra de ferramentas superior.

## <a name="set-up-the-environment"></a><a name="start"></a>Configurar o ambiente

Comece por configurar um ambiente de teste.

### <a name="import-packages"></a>Importar pacotes

Importar os pacotes Python necessários para este tutorial:

```python
%matplotlib inline
import numpy as np
import matplotlib
import matplotlib.pyplot as plt
 
import azureml
from azureml.core import Workspace, Run

# Display the core SDK version number
print("Azure ML SDK Version: ", azureml.core.VERSION)
```

### <a name="retrieve-the-model"></a>Obter o modelo

No tutorial anterior, registou um modelo na sua área de trabalho. Agora carregue este espaço de trabalho e descarregue o modelo para o seu diretório local:


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

Antes de ser implementado, certifique-se de que o seu modelo está a funcionar localmente:
* Carregar dados de teste.
* Prever os dados do teste.
* Examine a matriz de confusão.

### <a name="load-test-data"></a>Carregar os dados de teste

Carregue os dados de teste a partir do **./data/diretório** criado durante o tutorial de formação:

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

Para obter previsões, alimente o conjunto de dados de teste para o modelo:

```python
import pickle
from sklearn.externals import joblib

clf = joblib.load(os.path.join(os.getcwd(), 'sklearn_mnist_model.pkl'))
y_hat = clf.predict(X_test)
```

###  <a name="examine-the-confusion-matrix"></a>Examinar a matriz de confusão

Gere uma matriz de confusão para ver quantas amostras do conjunto de teste foram classificadas corretamente. Note o valor desclassificado para as previsões incorretas: 

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
   

Utilize `matplotlib` para apresentar a matriz de confusão como um gráfico. Neste gráfico, o eixo x mostra os valores reais, e o eixo y mostra os valores previstos. A cor em cada grelha mostra a taxa de erro. Quanto mais clara for a cor, mais alta é a taxa de erros. Por exemplo, muitos 5's são mal classificados como 3.000. Então você vê uma grelha brilhante em (5,3):

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

## <a name="deploy-as-a-web-service"></a>Implementar como um serviço web

Depois de testar o modelo e ficar satisfeito com os resultados, implemente o modelo como um serviço web hospedado em Instâncias de Contentores. 

Para construir o ambiente correto para instâncias de contentores, forneça os seguintes componentes:
* Um roteiro de pontuação para mostrar como usar o modelo.
* Um ficheiro ambiental para mostrar quais os pacotes que precisam de ser instalados.
* Um ficheiro de configuração para construir a instância do recipiente.
* O modelo que treinou anteriormente.

<a name="make-script"></a>

### <a name="create-scoring-script"></a>Criar o script de classificação

Crie o roteiro de pontuação, chamado **score.py.** A chamada do serviço web usa este script para mostrar como usar o modelo.

Inclua estas duas funções necessárias no script de pontuação:
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
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
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

Em seguida, crie um arquivo ambiental, chamado **myenv.yml,** que especifica todas as dependências do pacote do script. Este ficheiro é usado para garantir que todas essas dependências estão instaladas na imagem do Docker. Este modelo precisa de `scikit-learn` e `azureml-sdk`. Todos os ficheiros ambientais personalizados precisam de listar os incumprimentos em azul com verion >= 1,0,45 como dependência do pip. Este pacote contém a funcionalidade necessária para hospedar o modelo como um serviço web.

```python
from azureml.core.conda_dependencies import CondaDependencies

myenv = CondaDependencies()
myenv.add_conda_package("scikit-learn")
myenv.add_pip_package("azureml-defaults")

with open("myenv.yml", "w") as f:
    f.write(myenv.serialize_to_string())
```
Reveja o `myenv.yml` conteúdo do ficheiro:

```python
with open("myenv.yml", "r") as f:
    print(f.read())
```

### <a name="create-a-configuration-file"></a>Criar um ficheiro de configuração

Criar um ficheiro de configuração de implementação. Especifique o número de CPUs e gigabytes de RAM necessários para o seu recipiente de instâncias de contentores. Embora dependa do seu modelo, o padrão de um núcleo e 1 gigabyte de RAM é suficiente para muitos modelos. Se precisar de mais tarde, tem de recriar a imagem e recolocar o serviço.

```python
from azureml.core.webservice import AciWebservice

aciconfig = AciWebservice.deploy_configuration(cpu_cores=1, 
                                               memory_gb=1, 
                                               tags={"data": "MNIST",  
                                                     "method": "sklearn"},
                                               description='Predict MNIST with sklearn')
```

### <a name="deploy-in-container-instances"></a>Implantação em instâncias de contentores
O tempo estimado para terminar a implantação é de **cerca de 7 a 8 minutos.**

Configure a imagem e implemente. O código abaixo realiza estes passos:

1. Construa uma imagem utilizando estes ficheiros:
   * O ficheiro de `score.py`pontuação, .
   * O arquivo `myenv.yml`do ambiente, .
   * O ficheiro modelo.
1. Registe a imagem sob o espaço de trabalho. 
1. Envie a imagem para o recipiente de instâncias de contentores.
1. Ligue um recipiente em Instâncias de Contentores utilizando a imagem.
1. Obtenha o ponto final HTTP do serviço Web.

Por favor, note que se estiver a definir o seu próprio ficheiro ambiental, deve listar os incumprimentos em azul com a versão >= 1.0,45 como dependência do pip. Este pacote contém a funcionalidade necessária para hospedar o modelo como um serviço web.

```python
%%time
from azureml.core.webservice import Webservice
from azureml.core.model import InferenceConfig
from azureml.core.environment import Environment

myenv = Environment.from_conda_specification(name="myenv", file_path="myenv.yml")
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

service = Model.deploy(workspace=ws,
                       name='sklearn-mnist-svc',
                       models=[model], 
                       inference_config=inference_config,
                       deployment_config=aciconfig)

service.wait_for_deployment(show_output=True)
```

Obtenha o ponto final HTTP do serviço Web de classificação, que aceita chamadas do cliente REST. Pode partilhar este ponto final com qualquer pessoa que queira testar o serviço web ou integrá-lo numa aplicação: 

```python
print(service.scoring_uri)
```

## <a name="test-the-deployed-service"></a>Testar o serviço implantado

Anteriormente, obteve todos os dados de teste com a versão local do modelo. Agora pode testar o modelo implantado com uma amostra aleatória de 30 imagens dos dados do teste.  

O código abaixo realiza estes passos:
1. Envie os dados como um array JSON para o serviço web hospedado em Instâncias de Contentores. 

1. Utilize a API `run` do SDK para invocar o serviço. Também pode fazer chamadas cruas utilizando qualquer ferramenta HTTP, como **o caracol**.

1. Imprima as predições devolvidas e represente-as juntamente com as imagens de entrada. Fonte vermelha e imagem inversa, branca em preto, é usada para destacar as amostras mal classificadas. 

Como a precisão do modelo é elevada, pode ter de executar o seguinte código algumas vezes antes de poder ver uma amostra mal classificada:

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

Este resultado é de uma amostra aleatória de imagens de teste:

![Resultados gráficos de exibição](./media/tutorial-deploy-models-with-aml/results.png)

Também pode enviar um pedido http cru para testar o serviço web:

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

Para manter o grupo de recursos e espaço de trabalho para outros tutoriais e exploração, você pode eliminar apenas a implementação de Instâncias de Contentores usando esta chamada API:

```python
service.delete()
```

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]


## <a name="next-steps"></a>Passos seguintes

+ Conheça todas as opções de [implementação para O Machine Learning Azure.](how-to-deploy-and-where.md)
+ Saiba como [criar clientes para o serviço web.](how-to-consume-web-service.md)
+  [Faça previsões sobre grandes quantidades de dados](how-to-use-parallel-run-step.md) assincronicamente.
+ Monitorize os seus modelos de Aprendizagem automática Azure com Insights de [Aplicação](how-to-enable-app-insights.md).
+ Experimente o tutorial de seleção automática de [algoritmos.](tutorial-auto-train-models.md) 
