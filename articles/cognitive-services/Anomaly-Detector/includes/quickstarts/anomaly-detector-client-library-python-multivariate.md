---
title: Início rápido da biblioteca de clientes multivariado do Detetor de Anomalia Python
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/25/2020
ms.author: mbullwin
ms.openlocfilehash: 684c61dfb34d55681904943160ca389c19a4c8db
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107732193"
---
Começa com a biblioteca multivariada do Detetor de Anomalias para python. Siga estes passos para instalar o pacote comece a usar os algoritmos fornecidos pelo serviço. As novas APIs de deteção de anomalias multivariadas permitem aos desenvolvedores integrar facilmente a IA avançada para detetar anomalias de grupos de métricas, sem necessidade de conhecimentos de aprendizagem automática ou dados rotulados. As dependências e as inter-correlações entre diferentes sinais são automaticamente contabilizadas como factores-chave. Isto ajuda-o a proteger proativamente os seus sistemas complexos de falhas.

Utilize a biblioteca multivariada do Detetor de Anomalias para Python para:

* Detete anomalias de nível do sistema de um grupo de séries temporais.
* Quando qualquer série de tempo individual não lhe dirá muito e tem que olhar todos os sinais para detetar um problema.
* Manutenção pré-ativa de ativos físicos dispendiosos com dezenas a centenas de diferentes tipos de sensores medindo vários aspetos da saúde do sistema.

[Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/anomalydetector/azure-ai-anomalydetector)  |  [Pacote (PyPi)](https://pypi.org/project/azure-ai-anomalydetector/3.0.0b3/)

## <a name="prerequisites"></a>Pré-requisitos

* [Python 3.x](https://www.python.org/)
* A [biblioteca de análise de dados do Pandas](https://pandas.pydata.org/)
* Subscrição Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services)
* Assim que tiver a subscrição do Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title=" crie um recurso de Detetor de "  target="_blank"> Anomalias crie um recurso de Detetor de </a> Anomalias no portal Azure para obter a sua chave e ponto final. Aguarde que seja implantado e clique no botão Go para o botão **de recursos.**
    * Necessitará da chave e ponto final do recurso que criar para ligar a sua aplicação à API do Detetor de Anomalias. Colará a chave e o ponto final no código abaixo mais tarde no arranque rápido.
    Pode utilizar o nível de preços gratuitos `F0` para experimentar o serviço e fazer upgrade mais tarde para um nível pago para produção.


## <a name="setting-up"></a>Configuração

### <a name="create-a-new-python-application"></a>Criar uma nova aplicação python

 Crie um novo ficheiro Python e importe as seguintes bibliotecas.

```python
import os
import time
from datetime import datetime

from azure.ai.anomalydetector import AnomalyDetectorClient
from azure.ai.anomalydetector.models import DetectionRequest, ModelInfo
from azure.core.credentials import AzureKeyCredential
from azure.core.exceptions import HttpResponseError
```

Crie variáveis para a sua chave como variável ambiental, o caminho para um ficheiro de dados da série de tempo e a localização Azure da sua subscrição. Por exemplo, `westus2`.

```python
subscription_key = "ANOMALY_DETECTOR_KEY"
anomaly_detector_endpoint = "ANOMALY_DETECTOR_ENDPOINT"
```

### <a name="install-the-client-library"></a>Instalar a biblioteca do cliente

Depois de instalar o Python, pode instalar a biblioteca do cliente com:

```console
pip install --upgrade azure-ai-anomalydetector
```

## <a name="code-examples"></a>Exemplos de código

Estes fragmentos de código mostram-lhe como fazer o seguinte com a biblioteca de clientes do Detetor de Anomalias para Python:

* [Autenticar o cliente](#authenticate-the-client)
* [Preparar o modelo](#train-the-model)
* [Detetar anomalias](#detect-anomalies)
* [Modelo de exportação](#export-model)
* [Eliminar modelo](#delete-model)

## <a name="authenticate-the-client"></a>Autenticar o cliente

Para instantaneaizar um novo cliente do Detetor de Anomalias, é necessário passar a chave de subscrição do Detetor de Anomalias e o ponto final associado. Também vamos estabelecer uma fonte de dados.  

Para usar as APIs multivariadas do Detetor de Anomalias, precisamos de treinar o nosso próprio modelo antes de usarmos a deteção. Os dados utilizados para o treino são um lote de séries de tempo, cada série de tempo deve estar em formato CSV com duas colunas, relógio e valor. Todas as séries horadas devem ser fechadas num ficheiro zip e ser enviadas para [o armazenamento da Azure Blob](../../../../storage/blobs/storage-blobs-introduction.md#blobs). Por predefinição, o nome do ficheiro será utilizado para representar a variável para a série de tempo. Alternativamente, um meta.jsextra no ficheiro pode ser incluído no ficheiro zip se desejar que o nome da variável seja diferente do nome de ficheiro .zip. Assim que gerarmos [URL BLOB SAS (Assinaturas de acesso partilhado), podemos](../../../../storage/common/storage-sas-overview.md)usar o url para o ficheiro zip para o treino.

```python
def __init__(self, subscription_key, anomaly_detector_endpoint, data_source=None):
    self.sub_key = subscription_key
    self.end_point = anomaly_detector_endpoint

    # Create an Anomaly Detector client

    # <client>
    self.ad_client = AnomalyDetectorClient(AzureKeyCredential(self.sub_key), self.end_point)
    # </client>

    if not data_source:
        # Datafeed for test only
        self.data_source = "YOUR_SAMPLE_ZIP_FILE_LOCATED_IN_AZURE_BLOB_STORAGE_WITH_SAS"
    else:
        self.data_source = data_source
```

## <a name="train-the-model"></a>Preparar o modelo

Primeiro treinamos o modelo, verificamos o estado do modelo durante o treino para determinar quando o treino está completo, e depois recuperamos o último ID do modelo que vamos precisar quando passarmos à fase de deteção.

```python
def train(self, start_time, end_time, max_tryout=500):

    # Number of models available now
    model_list = list(self.ad_client.list_multivariate_model(skip=0, top=10000))
    print("{:d} available models before training.".format(len(model_list)))
    
    # Use sample data to train the model
    print("Training new model...")
    data_feed = ModelInfo(start_time=start_time, end_time=end_time, source=self.data_source)
    response_header = \
    self.ad_client.train_multivariate_model(data_feed, cls=lambda *args: [args[i] for i in range(len(args))])[-1]
    trained_model_id = response_header['Location'].split("/")[-1]
    
    # Model list after training
    new_model_list = list(self.ad_client.list_multivariate_model(skip=0, top=10000))
    
    # Wait until the model is ready. It usually takes several minutes
    model_status = None
    tryout_count = 0
    while (tryout_count < max_tryout and model_status != "READY"):
        model_status = self.ad_client.get_multivariate_model(trained_model_id).additional_properties["summary"][
            "status"]
        tryout_count += 1
        time.sleep(2)
    
    assert model_status == "READY"
    
    print("Done.", "\n--------------------")
    print("{:d} available models after training.".format(len(new_model_list)))
    
    # Return the latest model id
    return trained_model_id

```

## <a name="detect-anomalies"></a>Detetar anomalias

Utilize o `detect_anomaly` e para determinar se `get_dectection_result` existem anomalias dentro da sua fonte de dados. Terá de passar o ID do modelo para o modelo que acabou de treinar.

```python
def detect(self, model_id, start_time, end_time, max_tryout=500):
    
    # Detect anomaly in the same data source (but a different interval)
    try:
        detection_req = DetectionRequest(source=self.data_source, start_time=start_time, end_time=end_time)
        response_header = self.ad_client.detect_anomaly(model_id, detection_req,
                                                        cls=lambda *args: [args[i] for i in range(len(args))])[-1]
        result_id = response_header['Location'].split("/")[-1]
    
        # Get results (may need a few seconds)
        r = self.ad_client.get_detection_result(result_id)
        tryout_count = 0
        while r.summary.status != "READY" and tryout_count < max_tryout:
            time.sleep(1)
            r = self.ad_client.get_detection_result(result_id)
            tryout_count += 1
    
        if r.summary.status != "READY":
            print("Request timeout after %d tryouts.".format(max_tryout))
            return None
    
    except HttpResponseError as e:
        print('Error code: {}'.format(e.error.code), 'Error message: {}'.format(e.error.message))
    except Exception as e:
        raise e
    
    return r
```

## <a name="export-model"></a>Modelo de exportação

Se quiser exportar um modelo de utilização `export_model` e passar o ID do modelo que pretende exportar:

```python
def export_model(self, model_id, model_path="model.zip"):

    # Export the model
    model_stream_generator = self.ad_client.export_model(model_id)
    with open(model_path, "wb") as f_obj:
        while True:
            try:
                f_obj.write(next(model_stream_generator))
            except StopIteration:
                break
            except Exception as e:
                raise e
```

## <a name="delete-model"></a>Eliminar modelo

Para eliminar uma utilização do modelo `delete_multivariate_model` e passar o ID do modelo pretende eliminar:

```python
def delete_model(self, model_id):

    # Delete the mdoel
    self.ad_client.delete_multivariate_model(model_id)
    model_list_after_delete = list(self.ad_client.list_multivariate_model(skip=0, top=10000))
    print("{:d} available models after deletion.".format(len(model_list_after_delete)))
```

## <a name="run-the-application"></a>Executar a aplicação

Antes de executar a aplicação, precisamos adicionar algum código para ligar para as nossas funções recém-criadas.

```python
if __name__ == '__main__':
    subscription_key = "ANOMALY_DETECTOR_KEY"
    anomaly_detector_endpoint = "ANOMALY_DETECTOR_ENDPOINT"

    # Create a new sample and client
    sample = MultivariateSample(subscription_key, anomaly_detector_endpoint, data_source=None)

    # Train a new model
    model_id = sample.train(datetime(2021, 1, 1, 0, 0, 0), datetime(2021, 1, 2, 12, 0, 0))

    # Reference
    result = sample.detect(model_id, datetime(2021, 1, 2, 12, 0, 0), datetime(2021, 1, 3, 0, 0, 0))
    print("Result ID:\t", result.result_id)
    print("Result summary:\t", result.summary)
    print("Result length:\t", len(result.results))

    # Export model
    sample.export_model(model_id, "model.zip")

    # Delete model
    sample.delete_model(model_id)

```

Execute o pedido com o `python` comando e o nome do seu ficheiro.

[!INCLUDE [anomaly-detector-next-steps](../quickstart-cleanup-next-steps.md)]
