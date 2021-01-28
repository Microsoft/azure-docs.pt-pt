---
title: Início rápido da biblioteca do cliente do detetor de anomalia Python
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/25/2020
ms.author: mbullwin
ms.openlocfilehash: f6206ad2f88983396fa7d0be323daad327e4d235
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98947132"
---
Começa com a biblioteca de clientes do Detetor de Anomalias para python. Siga estes passos para instalar o pacote comece a usar os algoritmos fornecidos pelo serviço. O serviço Detetor de Anomalias permite-lhe encontrar anomalias nos dados da sua série de tempo utilizando automaticamente os modelos mais adequados, independentemente da indústria, cenário ou volume de dados.

Utilize a biblioteca de clientes do Detetor de Anomalias para Python para:

* Detetar anomalias ao longo do conjunto de dados da série de tempo, como um pedido de lote
* Detetar o estado de anomalia do último ponto de dados da sua série de tempo
* Detete pontos de mudança de tendência no seu conjunto de dados.

[Documentação de referência da biblioteca](https://go.microsoft.com/fwlink/?linkid=2090370)  |  [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-anomalydetector)  |  [Pacote (PyPi)](https://pypi.org/project/azure-ai-anomalydetector/)  |  [Encontre o código de amostra no GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/anomalydetector/azure-ai-anomalydetector/samples)

## <a name="prerequisites"></a>Pré-requisitos

* [Python 3.x](https://www.python.org/)
* A [biblioteca de análise de dados do Pandas](https://pandas.pydata.org/)
* Subscrição Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services)
* Assim que tiver a subscrição do Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title=" crie um recurso de Detetor de "  target="_blank"> Anomalias crie um recurso de Detetor de <span class="docon docon-navigate-external x-hidden-focus"></span> </a> Anomalias no portal Azure para obter a sua chave e ponto final. Aguarde que seja implantado e clique no botão Go para o botão **de recursos.**
    * Necessitará da chave e ponto final do recurso que criar para ligar a sua aplicação à API do Detetor de Anomalias. Colará a chave e o ponto final no código abaixo mais tarde no arranque rápido.
    Pode utilizar o nível de preços gratuitos `F0` para experimentar o serviço e fazer upgrade mais tarde para um nível pago para produção.


## <a name="setting-up"></a>Configuração

[!INCLUDE [anomaly-detector-environment-variables](../environment-variables.md)]

### <a name="create-a-new-python-application"></a>Criar uma nova aplicação python

 Crie um novo ficheiro Python e importe as seguintes bibliotecas.

```python
import os
from azure.ai.anomalydetector import AnomalyDetectorClient
from azure.ai.anomalydetector.models import DetectRequest, TimeSeriesPoint, TimeGranularity, \
    AnomalyDetectorError
from azure.core.credentials import AzureKeyCredential
import pandas as pd
```

Crie variáveis para a sua chave como variável ambiental, o caminho para um ficheiro de dados da série de tempo e a localização Azure da sua subscrição. Por exemplo, `westus2`.

```python
SUBSCRIPTION_KEY = os.environ["ANOMALY_DETECTOR_KEY"]
ANOMALY_DETECTOR_ENDPOINT = os.environ["ANOMALY_DETECTOR_ENDPOINT"]
TIME_SERIES_DATA_PATH = os.path.join("./sample_data", "request-data.csv")
```

### <a name="install-the-client-library"></a>Instalar a biblioteca do cliente

Depois de instalar o Python, pode instalar a biblioteca do cliente com:

```console
pip install --upgrade azure-ai-anomalydetector
```

## <a name="object-model"></a>Modelo de objeto

O cliente do Detetor de Anomalias é um objeto [AnomalyDetectorClient](https://github.com/Azure/azure-sdk-for-python/blob/0b8622dc249969c2f01c5d7146bd0bb36bb106dd/sdk/cognitiveservices/azure-cognitiveservices-anomalydetector/azure/cognitiveservices/anomalydetector/_anomaly_detector_client.py) que autentica a Azure usando a sua chave. O cliente pode fazer a deteção de anomalias num conjunto de dados inteiro utilizando [detect_entire_series,](https://github.com/Azure/azure-sdk-for-python/blob/bf9d44f2a50aea46a59c4cb83ccfccaff5e2b218/sdk/anomalydetector/azure-ai-anomalydetector/azure/ai/anomalydetector/operations/_anomaly_detector_client_operations.py#L26)ou no último ponto de dados utilizando [detect_last_point](https://github.com/Azure/azure-sdk-for-python/blob/bf9d44f2a50aea46a59c4cb83ccfccaff5e2b218/sdk/anomalydetector/azure-ai-anomalydetector/azure/ai/anomalydetector/operations/_anomaly_detector_client_operations.py#L87). A função [detect_change_point](https://github.com/Azure/azure-sdk-for-python/blob/bf9d44f2a50aea46a59c4cb83ccfccaff5e2b218/sdk/anomalydetector/azure-ai-anomalydetector/azure/ai/anomalydetector/aio/operations_async/_anomaly_detector_client_operations_async.py#L142) deteta pontos que marcam alterações numa tendência.

Os dados da série de tempo são enviados como uma série de [objetos TimeSeriesPoints.](https://github.com/Azure/azure-sdk-for-python/blob/bf9d44f2a50aea46a59c4cb83ccfccaff5e2b218/sdk/anomalydetector/azure-ai-anomalydetector/azure/ai/anomalydetector/models/_models_py3.py#L370) O `DetectRequest` objeto contém propriedades para descrever os dados, por `TimeGranularity` exemplo, e parâmetros para a deteção de anomalias.

A resposta do Detetor de Anomalias é um objeto [LastDetectResponse](/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse), [TotalDetectResponse,](/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse)ou [ChangePointDetectResponse,](https://github.com/Azure/azure-sdk-for-python/blob/bf9d44f2a50aea46a59c4cb83ccfccaff5e2b218/sdk/anomalydetector/azure-ai-anomalydetector/azure/ai/anomalydetector/models/_models_py3.py#L107) dependendo do método utilizado.

## <a name="code-examples"></a>Exemplos de código

Estes fragmentos de código mostram-lhe como fazer o seguinte com a biblioteca de clientes do Detetor de Anomalias para Python:

* [Autenticar o cliente](#authenticate-the-client)
* [Carregue um conjunto de dados da série de tempo a partir de um ficheiro](#load-time-series-data-from-a-file)
* [Detetar anomalias em todo o conjunto de dados](#detect-anomalies-in-the-entire-data-set)
* [Detetar o estado de anomalia do último ponto de dados](#detect-the-anomaly-status-of-the-latest-data-point)
* [Detetar os pontos de alteração no conjunto de dados](#detect-change-points-in-the-data-set)

## <a name="authenticate-the-client"></a>Autenticar o cliente

Adicione a sua variável de localização Azure ao ponto final e autense o cliente com a sua chave.

```python
client = AnomalyDetectorClient(AzureKeyCredential(SUBSCRIPTION_KEY), ANOMALY_DETECTOR_ENDPOINT)
```

## <a name="load-time-series-data-from-a-file"></a>Carregar dados da série de tempo a partir de um ficheiro

Descarregue os dados de exemplo para este arranque rápido do [GitHub:](https://github.com/Azure-Samples/AnomalyDetector/blob/master/example-data/request-data.csv)
1. No seu navegador, clique com o botão direito **Raw.**
2. Clique **em Guardar link como**.
3. Guarde o ficheiro para o seu diretório de candidaturas, como um ficheiro .csv.

Estes dados da série de tempo são formatados como um ficheiro .csv e serão enviados para a API do Detetor de Anomalias.

Carregue o seu ficheiro de dados com o método da biblioteca pandas `read_csv()` e faça uma variável de lista vazia para armazenar as suas séries de dados. Iterate através do ficheiro, e anexar os dados como um `TimeSeriesPoint` objeto. Este objeto conterá o valor de tempotad e numérico das linhas do seu ficheiro de dados .csv.

```python
series = []
data_file = pd.read_csv(TIME_SERIES_DATA_PATH, header=None, encoding='utf-8', parse_dates=[0])
for index, row in data_file.iterrows():
    series.append(TimeSeriesPoint(timestamp=row[0], value=row[1]))
```

Crie um `DetectRequest` objeto com as suas séries temporítmicas e os `TimeGranularity` (ou periódicos) dos seus pontos de dados. Por exemplo, `TimeGranularity.daily`.

```python
request = DetectRequest(series=series, granularity=TimeGranularity.daily)
```

## <a name="detect-anomalies-in-the-entire-data-set"></a>Detetar anomalias em todo o conjunto de dados

Ligue para a API para detetar anomalias através de todos os dados da série de tempo usando o método do `detect_entire_series` cliente. Guarde o objeto [Deresponse TotalDetect.](/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse) Iterar através da lista de `is_anomaly` resposta, e imprimir o índice de quaisquer `true` valores. Estes valores correspondem ao índice de pontos de dados anómalos, caso se encontrem.

```python
print('Detecting anomalies in the entire time series.')

try:
    response = client.detect_entire_series(request)
except AnomalyDetectorError as e:
    print('Error code: {}'.format(e.error.code), 'Error message: {}'.format(e.error.message))
except Exception as e:
    print(e)

if any(response.is_anomaly):
    print('An anomaly was detected at index:')
    for i, value in enumerate(response.is_anomaly):
        if value:
            print(i)
else:
    print('No anomalies were detected in the time series.')
```

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Detetar o estado de anomalia do último ponto de dados

Ligue para a API do Detetor de Anomalias para determinar se o seu último ponto de dados é uma anomalia utilizando o método do cliente `detect_last_point` e guarde o `LastDetectResponse` objeto devolvido. O valor da resposta `is_anomaly` é um booleano que especifica o estado de anomalia desse ponto.  

```python
print('Detecting the anomaly status of the latest data point.')

try:
    response = client.detect_last_point(request)
except AnomalyDetectorError as e:
    print('Error code: {}'.format(e.error.code), 'Error message: {}'.format(e.error.message))
except Exception as e:
    print(e)

if response.is_anomaly:
    print('The latest point is detected as anomaly.')
else:
    print('The latest point is not detected as anomaly.')
```

## <a name="detect-change-points-in-the-data-set"></a>Detetar pontos de alteração no conjunto de dados

Ligue para a API para detetar pontos de alteração nos dados da série de tempo usando o método do `detect_change_point` cliente. Guarde o `ChangePointDetectResponse` objeto devolvido. Iterar através da lista de `is_change_point` resposta, e imprimir o índice de quaisquer `true` valores. Estes valores correspondem aos índices de variação de tendência, se algum for encontrado.

```python
print('Detecting change points in the entire time series.')

try:
    response = client.detect_change_point(request)
except AnomalyDetectorError as e:
    print('Error code: {}'.format(e.error.code), 'Error message: {}'.format(e.error.message))
except Exception as e:
    print(e)

if any(response.is_change_point):
    print('An change point was detected at index:')
    for i, value in enumerate(response.is_change_point):
        if value:
            print(i)
else:
    print('No change point were detected in the time series.')
```

## <a name="run-the-application"></a>Executar a aplicação

Execute o pedido com o `python` comando e o nome do seu ficheiro.

[!INCLUDE [anomaly-detector-next-steps](../quickstart-cleanup-next-steps.md)]
