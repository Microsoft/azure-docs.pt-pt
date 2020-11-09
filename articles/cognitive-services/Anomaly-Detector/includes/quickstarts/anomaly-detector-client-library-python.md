---
title: Início rápido da biblioteca do cliente do detetor de anomalia Python
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/22/2020
ms.author: mbullwin
ms.openlocfilehash: c06cb7143756eed3d50fe1d2d03ce7ba8d6d9d4d
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2020
ms.locfileid: "94372017"
---
Começa com a biblioteca de clientes do Detetor de Anomalias para python. Siga estes passos para instalar o pacote comece a usar os algoritmos fornecidos pelo serviço. O serviço Detetor de Anomalias permite-lhe encontrar anomalias nos dados da sua série de tempo utilizando automaticamente os modelos mais adequados, independentemente da indústria, cenário ou volume de dados.

Utilize a biblioteca de clientes do Detetor de Anomalias para Python para:

* Detetar anomalias ao longo do conjunto de dados da série de tempo, como um pedido de lote
* Detetar o estado de anomalia do último ponto de dados da sua série de tempo
* Detete pontos de mudança de tendência no seu conjunto de dados.

[Documentação de referência da biblioteca](https://go.microsoft.com/fwlink/?linkid=2090370)  |  [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-anomalydetector)  |  [Pacote (PyPi)](https://pypi.org/project/azure-ai-anomalydetector/)  |  [Encontre o código de amostra no GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/sdk/python-sdk-sample.py)

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

[!code-python[import declarations](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=imports)]

Crie variáveis para a sua chave como variável ambiental, o caminho para um ficheiro de dados da série de tempo e a localização Azure da sua subscrição. Por exemplo, `westus2`.

[!code-python[Vars for the key, path location and data path](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=initVars)]

### <a name="install-the-client-library"></a>Instalar a biblioteca do cliente

Depois de instalar o Python, pode instalar a biblioteca do cliente com:

```console
pip install --upgrade azure-ai-anomalydetector
```

## <a name="object-model"></a>Modelo de objeto

O cliente do Detetor de Anomalias é um objeto [AnomalyDetectorClient](/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python) que autentica a Azure usando a sua chave. O cliente pode fazer a deteção de anomalias num conjunto de dados inteiro utilizando [entire_detect()](/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#entire-detect-body--custom-headers-none--raw-false----operation-config-)ou no último ponto de dados utilizando [Last_detect()](/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#last-detect-body--custom-headers-none--raw-false----operation-config-). A função [ChangePointDetectAsync](https://go.microsoft.com/fwlink/?linkid=2090370) deteta pontos que marcam alterações numa tendência.

Os dados da série de tempo são enviados como uma série de [pontos](/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.point?view=azure-python) num objeto [Request.](/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.request?view=azure-python) O `Request` objeto contém propriedades para descrever os dados[(Granularidade,](/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.granularity?view=azure-python) por exemplo), e parâmetros para a deteção de anomalias.

A resposta do Detetor de Anomalias é um objeto [LastDetectResponse](/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-python), [TotalDetectResponse,](/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-python)ou [ChangePointDetectResponse,](https://go.microsoft.com/fwlink/?linkid=2090370) dependendo do método utilizado.

## <a name="code-examples"></a>Exemplos de código

Estes fragmentos de código mostram-lhe como fazer o seguinte com a biblioteca de clientes do Detetor de Anomalias para Python:

* [Autenticar o cliente](#authenticate-the-client)
* [Carregue um conjunto de dados da série de tempo a partir de um ficheiro](#load-time-series-data-from-a-file)
* [Detetar anomalias em todo o conjunto de dados](#detect-anomalies-in-the-entire-data-set)
* [Detetar o estado de anomalia do último ponto de dados](#detect-the-anomaly-status-of-the-latest-data-point)
* [Detetar os pontos de alteração no conjunto de dados](#detect-change-points-in-the-data-set)

## <a name="authenticate-the-client"></a>Autenticar o cliente

Adicione a sua variável de localização Azure ao ponto final e autense o cliente com a sua chave.

[!code-python[Client authentication](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=client)]

## <a name="load-time-series-data-from-a-file"></a>Carregar dados da série de tempo a partir de um ficheiro

Descarregue os dados de exemplo para este arranque rápido do [GitHub:](https://github.com/Azure-Samples/AnomalyDetector/blob/master/example-data/request-data.csv)
1. No seu navegador, clique com o botão direito **Raw.**
2. Clique **em Guardar link como**.
3. Guarde o ficheiro para o seu diretório de candidaturas, como ficheiro .csv.

Estes dados da série de tempo são formatados como um ficheiro .csv, e serão enviados para a API do Detetor de Anomalias.

Carregue o seu ficheiro de dados com o método da biblioteca pandas `read_csv()` e faça uma variável de lista vazia para armazenar as suas séries de dados. Iterate através do ficheiro, e anexar os dados como um objeto [Point.](/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.point?view=azure-python) Este objeto conterá o valor de tempotadismo e numérico das linhas do seu ficheiro de dados .csv.

[!code-python[Load the data file](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=loadDataFile)]

Crie um objeto [Request](/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.request?view=azure-python) com as suas séries temporais e a [granularidade](/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.granularity?view=azure-python) (ou periodicidade) dos seus pontos de dados. Por exemplo, `Granularity.daily`.

[!code-python[Create the request object](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=request)]

## <a name="detect-anomalies-in-the-entire-data-set"></a>Detetar anomalias em todo o conjunto de dados

Ligue para a API para detetar anomalias através de todos os dados da série de tempo usando o método [de entire_detect do cliente.).](/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#entire-detect-body--custom-headers-none--raw-false----operation-config-) Guarde o objeto [Deresponse TotalDetect.](/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-python) Iterar através da lista de `is_anomaly` resposta, e imprimir o índice de quaisquer `true` valores. Estes valores correspondem ao índice de pontos de dados anómalos, caso se encontrem.

[!code-python[Batch anomaly detection sample](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=detectAnomaliesBatch)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Detetar o estado de anomalia do último ponto de dados

Ligue para a API do Detetor de Anomalias para determinar se o seu último ponto de dados é uma anomalia utilizando o método [de last_detect do](/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#last-detect-body--custom-headers-none--raw-false----operation-config-) cliente e guarde o objeto [BackDetectResponse](/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-python) devolvido. O valor da resposta `is_anomaly` é um booleano que especifica o estado de anomalia desse ponto.  

[!code-python[Batch anomaly detection sample](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=latestPointDetection)]

## <a name="detect-change-points-in-the-data-set"></a>Detetar pontos de alteração no conjunto de dados

Ligue para a API para detetar pontos de alteração nos dados da série de tempo usando o método [de detect_change_point do cliente.](https://go.microsoft.com/fwlink/?linkid=2090370) Guarde o objeto [ChangePointDetectResponse](https://go.microsoft.com/fwlink/?linkid=2090370) devolvido. Iterar através da lista de `is_change_point` resposta, e imprimir o índice de quaisquer `true` valores. Estes valores correspondem aos índices de variação de tendência, se algum for encontrado.

[!code-python[detect change points](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=changePointDetection)]

## <a name="run-the-application"></a>Executar a aplicação

Execute o pedido com o `python` comando e o nome do seu ficheiro.

[!INCLUDE [anomaly-detector-next-steps](../quickstart-cleanup-next-steps.md)]