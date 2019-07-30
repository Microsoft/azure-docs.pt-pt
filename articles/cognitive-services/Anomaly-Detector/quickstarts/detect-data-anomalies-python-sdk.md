---
title: 'Início rápido: Detectar anomalias de dados usando a biblioteca de cliente do detector de anomalias para Python'
titleSuffix: Azure Cognitive Services
description: Use a API do detector de anomalias para detectar anormalidades em sua série de dados, seja como um lote ou em dados de streaming.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 07/26/2019
ms.author: aahi
ms.openlocfilehash: b78d19841bdca100211378f71e45a41dd37aad28
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639336"
---
# <a name="quickstart-anomaly-detector-client-library-for-python"></a>Início rápido: Biblioteca de cliente do detector de anomalias para Python

Introdução à biblioteca de cliente do detector de anomalias para .NET. Siga estas etapas para instalar o pacote e experimentar o código de exemplo para tarefas básicas. O serviço de detector de anomalias permite que você encontre anormalidades em seus dados de série temporal usando automaticamente os modelos de melhor ajuste, independentemente do setor, cenário ou volume de dados.

Use a biblioteca de cliente do detector de anomalias para Python para:

* Detectar anomalias em todo o seu conjunto de data de série temporal, como uma solicitação em lote
* Detectar o status de anomalia do último ponto de dados em sua série temporal

[Referência de biblioteca documentação](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector?view=azure-python) | de biblioteca de | [código-fonte](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-anomalydetector)[(PyPi)](https://pypi.org/project/azure-cognitiveservices-anomalydetector/) | [amostras](https://github.com/Azure-Samples/anomalydetector)

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure- [crie uma gratuitamente](https://azure.microsoft.com/free/)
* [Python 3. x](https://www.python.org/)
* A [biblioteca de análise de dados](https://pandas.pydata.org/) do pandas
 
## <a name="setting-up"></a>Configurando

### <a name="create-an-anomaly-detector-resource"></a>Criar um recurso de detector de anomalias

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

Depois de obter uma chave de sua assinatura ou recurso de avaliação, [crie uma variável de ambiente](../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) para a chave `ANOMALY_DETECTOR_KEY`, denominada.

### <a name="create-a-new-python-application"></a>Criar um novo aplicativo Python

 Crie um novo aplicativo Python em seu editor ou IDE preferido. Em seguida, importe as bibliotecas a seguir.

[!code-python[import declarations](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=imports)]

Crie variáveis para sua chave como uma variável de ambiente, o caminho para um arquivo de dados de série temporal e o local do Azure da sua assinatura. Por exemplo, `westus2`. 

[!code-python[Vars for the key, path location and data path](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=initVars)]

### <a name="install-the-client-library"></a>Instalar a biblioteca de cliente

Depois de instalar o Python, você pode instalar a biblioteca de cliente com:

```console
pip install --upgrade azure-cognitiveservices-anomalydetector
```

## <a name="object-model"></a>Modelo de objeto

O cliente do detector de anomalias é um objeto [AnomalyDetectorClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python) que se autentica no Azure usando sua chave. O cliente fornece dois métodos de detecção de anomalias: Em um conjunto de dados inteiro usando [entire_detect ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#entire-detect-body--custom-headers-none--raw-false----operation-config-)e no último ponto de data usando [Last_detect ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#last-detect-body--custom-headers-none--raw-false----operation-config-). 

Os dados de série temporal são enviados como uma série de [pontos](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.point(class)?view=azure-python) em um objeto de [solicitação](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.request(class)?view=azure-python) . O `Request` objeto contém propriedades para descrever os dados ([granularidade](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.granularity?view=azure-python) , por exemplo) e parâmetros para a detecção de anomalias. 

A resposta do detector de anomalias é um objeto [LastDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-python) ou [EntireDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-python) dependendo do método usado. 

## <a name="code-examples"></a>Exemplos de código 

Esses trechos de código mostram como fazer o seguinte com a biblioteca de cliente do detector de anomalias para .NET:

* [Autenticar o cliente](#authenticate-the-client)
* [Carregar um conjunto de dados de série temporal de um arquivo](#load-time-series-data-from-a-file)
* [Detectar anomalias em todo o conjunto de dados](#detect-anomalies-in-the-entire-data-set) 
* [Detectar o status de anomalia do último ponto de dados](#detect-the-anomaly-status-of-the-latest-data-point)

### <a name="authenticate-the-client"></a>Autenticar o cliente

Adicione sua variável de local do Azure ao ponto de extremidade e autentique o cliente com sua chave.

[!code-python[Client authentication](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=client)]

### <a name="load-time-series-data-from-a-file"></a>Carregar dados de série temporal de um arquivo

Baixe os dados de exemplo para este guia de início rápido do [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/example-data/request-data.csv):
1. No navegador, clique com o botão direito do mouse em **RAW**.
2. Clique em **Salvar link como**.
3. Salve o arquivo em seu diretório de aplicativo, como um arquivo. csv.

Esses dados de série temporal são formatados como um arquivo. csv e serão enviados para a API do detector de anomalias.

Carregue o arquivo de dados com o método da `read_csv()` biblioteca pandas e crie uma variável de lista vazia para armazenar sua série de dados. Itere pelo arquivo e acrescente os dados como um objeto [Point](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.point%28class%29?view=azure-python) . Esse objeto conterá o carimbo de data/hora e o valor numérico das linhas do arquivo de dados. csv. 

[!code-python[Load the data file](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=loadDataFile)]

Crie um objeto de [solicitação](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.request%28class%29?view=azure-python) com sua série temporal e a [granularidade](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.granularity?view=azure-python) (ou periodicidade) de seus pontos de dados. Por exemplo, `Granularity.daily`.

[!code-python[Create the request object](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=request)]

### <a name="detect-anomalies-in-the-entire-data-set"></a>Detectar anomalias em todo o conjunto de dados 

Chame a API para detectar anomalias por meio de todos os dados de série temporal usando o método [entire_detect ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#entire-detect-body--custom-headers-none--raw-false----operation-config-) do cliente. Armazene o objeto [EntireDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-python) retornado. Itere na lista da `is_anomaly` resposta e imprima o índice de quaisquer `true` valores. Esses valores correspondem ao índice de pontos de dados anormais, se algum for encontrado.

[!code-python[Batch anomaly detection sample](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=detectAnomaliesBatch)]

### <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Detectar o status de anomalia do último ponto de dados

Chame a API do detector de anomalias para determinar se o último ponto de dados é uma anomalia usando o método [last_detect ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#last-detect-body--custom-headers-none--raw-false----operation-config-) do cliente e armazene o objeto [LastDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-python) retornado. O valor da `is_anomaly` resposta é um booliano que especifica o status de anomalia do ponto.  

[!code-python[Batch anomaly detection sample](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=latestPointDetection)]

## <a name="run-the-application"></a>Executar a aplicação

Execute o aplicativo no IDE ou na linha de comando com o `python` comando e o nome do arquivo.
 
## <a name="clean-up-resources"></a>Limpar recursos

Se você quiser limpar e remover uma assinatura de serviços cognitivas, poderá excluir o recurso ou grupo de recursos. Excluir o grupo de recursos também exclui todos os outros recursos associados ao grupo de recursos.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
>[Detecção de anomalias de streaming com Azure Databricks](../tutorials/anomaly-detection-streaming-databricks.md)

* O que é a [API do detector de anomalias?](../overview.md)
* [Práticas recomendadas](../concepts/anomaly-detection-best-practices.md) ao usar a API do detector de anomalias.
* O código-fonte para este exemplo pode ser encontrado no [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/sdk/csharp-sdk-sample.cs).
