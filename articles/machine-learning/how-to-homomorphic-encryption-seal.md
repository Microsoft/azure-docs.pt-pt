---
title: Implementar um serviço de inferenculação encriptado (pré-visualização)
titleSuffix: Azure Machine Learning
description: Saiba como usar o Microsoft SEAL para implementar um serviço de previsão encriptado para a classificação de imagem
author: luisquintanilla
ms.author: luquinta
ms.date: 07/09/2020
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-python, deploy, responsible-ml
ms.openlocfilehash: a2c8b765f109408b8cb08f78a4b3ad2283f35eb9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "98223589"
---
# <a name="how-to-deploy-an-encrypted-inferencing-web-service-preview"></a>Como implementar um serviço web de inferenculação encriptado (pré-visualização)

Aprenda a implementar um modelo de classificação de imagem como um serviço web de inferenização encriptado em [Instâncias de Contentores Azure](../container-instances/index.yml) (ACI). O serviço web é uma imagem de recipiente Docker que contém a lógica do modelo e da pontuação.

Neste guia, utiliza o serviço Azure Machine Learning para:

> [!div class="checklist"]
> * Configure os seus ambientes
> * Implementar serviço web de inferição encriptado
> * Preparar dados de teste
> * Fazer previsões encriptadas
> * Limpar os recursos

O ACI é uma excelente solução para testar e compreender o fluxo de trabalho de implementação do modelo. Relativamente a implementação de produção dimensionáveis, considere utilizar o Azure Kubernetes Service. Para mais informações, consulte [como implementar e onde.](./how-to-deploy-and-where.md)

O método de encriptação utilizado nesta amostra é [encriptação homomórfica.](https://github.com/Microsoft/SEAL#homomorphic-encryption) A encriptação homomórfica permite que os cálculos sejam feitos em dados encriptados sem exigir o acesso a uma chave secreta (desencriptação). Os resultados dos cálculos são encriptados e só podem ser revelados pelo proprietário da chave secreta. 

## <a name="prerequisites"></a>Pré-requisitos

Este guia pressupõe que tem um modelo de classificação de imagem registado no Azure Machine Learning. Caso contrário, registe o modelo utilizando um [modelo pré-treinado](https://github.com/Azure/MachineLearningNotebooks/raw/master/tutorials/image-classification-mnist-data/sklearn_mnist_model.pkl) ou crie o seu próprio, completando ao [comboio um modelo de classificação de imagem com tutorial de Aprendizagem automática Azure.](tutorial-train-models-with-aml.md)

## <a name="configure-local-environment"></a>Configure o ambiente local

Em um caderno Jupyter

1. Importe os pacotes Python necessários para esta amostra.

    ```python
    %matplotlib inline
    import numpy as np
    import matplotlib.pyplot as plt

    import azureml.core

    # display the core SDK version number
    print("Azure ML SDK Version: ", azureml.core.VERSION)
    ```

2. Instale uma biblioteca homomórfica para uma inferencção segura.

    > [!NOTE]
    > O `encrypted-inference` pacote está atualmente em pré-visualização.

    [`encrypted-inference`](https://pypi.org/project/encrypted-inference) é uma biblioteca que contém encadernações para inferenculação encriptada com base no [Microsoft SEAL](https://github.com/Microsoft/SEAL).

    ```python
    !pip install encrypted-inference==0.9
    ```

## <a name="configure-the-inferencing-environment"></a>Configure o ambiente de inferenculação

Crie um ambiente para inferenar e adicione `encrypted-inference` o pacote como uma dependência conda.

```python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies

# to install required packages
env = Environment('tutorial-env')
cd = CondaDependencies.create(pip_packages=['azureml-dataprep[pandas,fuse]>=1.1.14', 'azureml-defaults', 'azure-storage-blob', 'encrypted-inference==0.9'], conda_packages = ['scikit-learn==0.22.1'])

env.python.conda_dependencies = cd

# Register environment to re-use later
env.register(workspace = ws)
```

## <a name="deploy-encrypted-inferencing-web-service"></a>Implementar serviço web de inferição encriptado

Implemente o modelo como um serviço web hospedado no ACI.

Para criar o ambiente certo para o ACI, forneça o seguinte:

* Um script de classificação para mostrar como utilizar o modelo
* Um ficheiro de configuração para criar o ACI
* Um modelo treinado

### <a name="create-scoring-script"></a>Criar o script de classificação

Crie o script de pontuação `score.py` utilizado pelo serviço web para inferenar.

Tem de incluir duas funções obrigatórias no script de classificação:

* A função `init()`, que, geralmente, carrega o modelo para um objeto global. Esta função só é executada uma vez, quando o contentor do Docker é iniciado.
* A função `run(input_data)` utiliza o modelo para prever um valor com base nos dados de entrada. Regra geral, as entradas e as saídas da execução utilizam JSON para a serialização e a desserialização, mas são suportados outros formatos. A função respende chaves públicas homomórficas baseadas em encriptação que são carregadas pelo chamador de serviço.

```python
%%writefile score.py
import json
import os
import pickle
import joblib
from azure.storage.blob import BlobServiceClient, BlobClient, ContainerClient, PublicAccess
from encrypted.inference.eiserver import EIServer

def init():
    global model
    # AZUREML_MODEL_DIR is an environment variable created during deployment.
    # It is the path to the model folder (./azureml-models/$MODEL_NAME/$VERSION)
    # For multiple models, it points to the folder containing all deployed models (./azureml-models)
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    model = joblib.load(model_path)

    global server
    server = EIServer(model.coef_, model.intercept_, verbose=True)

def run(raw_data):

    json_properties = json.loads(raw_data)

    key_id = json_properties['key_id']
    conn_str = json_properties['conn_str']
    container = json_properties['container']
    data = json_properties['data']

    # download the public keys from blob storage
    blob_service_client = BlobServiceClient.from_connection_string(conn_str=conn_str)
    blob_client = blob_service_client.get_blob_client(container=container, blob=key_id)
    public_keys = blob_client.download_blob().readall()

    result = {}
    # make prediction
    result = server.predict(data, public_keys)

    # you can return any data type as long as it is JSON-serializable
    return result
```

### <a name="create-configuration-file"></a>Criar um ficheiro de configuração

Crie um ficheiro de configuração de implementação e indique o número de CPUs e de gigabytes de RAM necessários para o contentor do ACI. Embora dependa do seu modelo, a predefinição de 1 núcleo e 1 gigabyte de RAM costuma ser suficiente para a maioria dos modelos. Se precisar de adicionar mais no futuro, terá de recriar a imagem e reimplementar o serviço.

```python
from azureml.core.webservice import AciWebservice

aciconfig = AciWebservice.deploy_configuration(cpu_cores=1, 
                                               memory_gb=1, 
                                               tags={"data": "MNIST",  "method" : "sklearn"}, 
                                               description='Encrypted Predict MNIST with sklearn + SEAL')
```

### <a name="deploy-to-azure-container-instances"></a>Implementar no Azure Container Instances

Tempo estimado para completar: **cerca de 2-5 minutos**

Configure a imagem e implemente. O código abaixo realiza estes passos:

1. Criar objetos ambientais que contenham dependências necessárias pelo modelo utilizando o ficheiro ambiente `myenv.yml` ()
1. Criar configuração de inferência necessária para implementar o modelo como um serviço web utilizando:
   * O ficheiro de classificação (`score.py`)
   * Objeto ambiente criado no passo anterior
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

myenv = Environment.get(workspace=ws, name="tutorial-env")
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

service = Model.deploy(workspace=ws,
                       name='sklearn-encrypted-mnist-svc',
                       models=[model],
                       inference_config=inference_config,
                       deployment_config=aciconfig)

service.wait_for_deployment(show_output=True)
```

Obtenha o ponto final HTTP do serviço Web de classificação, que aceita chamadas do cliente REST. Esse ponto final pode ser partilhado com qualquer pessoa que queira testar o serviço Web ou integrá-lo numa aplicação.

```python
print(service.scoring_uri)
```

## <a name="prepare-test-data"></a>Preparar dados de teste

1. Descarregue os dados do teste. Neste caso, é guardado num diretório chamado *dados.*

    ```python
    import os
    from azureml.core import Dataset
    from azureml.opendatasets import MNIST
    
    data_folder = os.path.join(os.getcwd(), 'data')
    os.makedirs(data_folder, exist_ok=True)
    
    mnist_file_dataset = MNIST.get_file_dataset()
    mnist_file_dataset.download(data_folder, overwrite=True)
    ```

1. Carregue os dados do teste a partir do diretório de *dados.*

    ```python
    from utils import load_data
    import os
    import glob
    
    data_folder = os.path.join(os.getcwd(), 'data')
    # note we also shrink the intensity values (X) from 0-255 to 0-1. This helps the neural network converge faster
    X_test = load_data(glob.glob(os.path.join(data_folder,"**/t10k-images-idx3-ubyte.gz"), recursive=True)[0], False) / 255.0
    y_test = load_data(glob.glob(os.path.join(data_folder,"**/t10k-labels-idx1-ubyte.gz"), recursive=True)[0], True).reshape(-1)
    ```

## <a name="make-encrypted-predictions"></a>Fazer previsões encriptadas

Utilize o conjunto de dados de teste com o modelo para obter previsões.

Para fazer previsões encriptadas:

1. Crie um novo `EILinearRegressionClient` cliente homomórcórfico baseado em encriptação e chaves públicas.

    ```python
    from encrypted.inference.eiclient import EILinearRegressionClient

    # Create a new Encrypted inference client and a new secret key.
    edp = EILinearRegressionClient(verbose=True)

    public_keys_blob, public_keys_data = edp.get_public_keys()
    ```

1. O upload da encriptação homomórfica gerou chaves públicas para a loja de bolhas padrão do espaço de trabalho. Isto permitir-lhe-á partilhar as chaves com o servidor de inferência.

    ```python
    import azureml.core
    from azureml.core import Workspace, Datastore
    import os

    ws = Workspace.from_config()

    datastore = ws.get_default_datastore()
    container_name=datastore.container_name

    # Create a local file and write the keys to it
    public_keys = open(public_keys_blob, "wb")
    public_keys.write(public_keys_data)
    public_keys.close()

    # Upload the file to blob store
    datastore.upload_files([public_keys_blob])

    # Delete the local file
    os.remove(public_keys_blob)
    ```

1. Criptografe os dados do teste

    ```python
    #choose any one sample from the test data 
    sample_index = 1

    #encrypt the data
    raw_data = edp.encrypt(X_test[sample_index])

    ```

1. Utilize a API da SDK `run` para invocar o serviço e fornecer o conjunto de dados de teste ao modelo para obter previsões. Precisamos enviar o fio de ligação para o armazenamento de bolhas onde as chaves públicas foram carregadas.

    ```python
    import json
    from azureml.core import Webservice

    service = Webservice(ws, 'sklearn-encrypted-mnist-svc')

    #pass the connection string for blob storage to give the server access to the uploaded public keys 
    conn_str_template = 'DefaultEndpointsProtocol={};AccountName={};AccountKey={};EndpointSuffix=core.windows.net'
    conn_str = conn_str_template.format(datastore.protocol, datastore.account_name, datastore.account_key)

    #build the json 
    data = json.dumps({"data": raw_data, "key_id" : public_keys_blob, "conn_str" : conn_str, "container" : container_name })
    data = bytes(data, encoding='ASCII')

    print ('Making an encrypted inference web service call ')
    eresult = service.run(input_data=data)

    print ('Received encrypted inference results')
    ```

1. Use o cliente para desencriptar os resultados.

    ```python
    import numpy as np

    results = edp.decrypt(eresult)

    print ('Decrypted the results ', results)

    #Apply argmax to identify the prediction result
    prediction = np.argmax(results)

    print ( ' Prediction : ', prediction)
    print ( ' Actual Label : ', y_test[sample_index])
    ```

## <a name="clean-up-resources"></a>Limpar os recursos

Eliminar o serviço web criado nesta amostra:

```python
service.delete()
```

Se já não planeias usar os recursos do Azure que criaste, apaga-os. Isto impede-o de ser cobrado por recursos não utetilizados que ainda estão em funcionamento. Consulte este guia sobre como [limpar recursos](how-to-manage-workspace.md#clean-up-resources) para saber mais.