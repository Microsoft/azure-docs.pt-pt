---
title: 'Início rápido: Detetar anomalias de dados usando a biblioteca de detetor de anomalias e o Python | Documentos da Microsoft'
titleSuffix: Azure Cognitive Services
description: Utilize a API de detetor de anomalias para detetar anomalias na sua série de dados como um lote ou nos dados de transmissão em fluxo.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 07/01/2019
ms.author: aahi
ms.openlocfilehash: 1d89ed8f40547142d41af9c587fc8fc000fa4dd9
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2019
ms.locfileid: "67503677"
---
# <a name="quickstart-anomaly-detector-client-library-for-python"></a>Início rápido: Biblioteca de clientes de detetor de anomalias para Python

Introdução à biblioteca de cliente detetor de anomalias para .NET. Siga estes passos para instalar o pacote e experimentar o código de exemplo para tarefas básicas. O serviço de detetor de anomalias permite-lhe encontrar anomalias nos seus dados de séries de tempo automaticamente com os modelos de oferta fitting no mesmo, independentemente da indústria, o cenário ou o volume de dados.

## <a name="key-concepts"></a>Conceitos-chave

Utilize a biblioteca de cliente de detetor de anomalias para o Python para:

* Detetar anomalias em todo o conjunto de dados de séries de tempo, como um pedido de lote
* Detectar o status de anomalias do ponto de dados mais recentes na sua série de tempo

[Documentação de referência da biblioteca](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector?view=azure-python) | [código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-anomalydetector) | [pacote (PyPi)](https://pypi.org/project/azure-cognitiveservices-anomalydetector/) | [exemplos](https://github.com/Azure-Samples/anomalydetector)

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição do Azure - [criá-lo gratuitamente](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)
* O [biblioteca de análise de dados de Pandas](https://pandas.pydata.org/)
 
## <a name="setting-up"></a>Como configurar

### <a name="create-an-anomaly-detector-resource"></a>Criar um recurso de detetor de anomalias

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

### <a name="install-the-client-library"></a>Instalar a biblioteca de cliente

Depois de instalar o Python, pode instalar a biblioteca de cliente com:

```console
pip install --upgrade azure-cognitiveservices-anomalydetector
```

## <a name="object-model"></a>Modelo de objeto

O cliente de detetor de anomalias é um [AnomalyDetectorClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python) objeto que autentica para o Azure utilizando a sua chave. O cliente fornece dois métodos de deteção de anomalias: Sobre como utilizar um conjunto de dados completo [entire_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#entire-detect-body--custom-headers-none--raw-false----operation-config-)e sobre os dados mais recentes apontar usando [Last_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#last-detect-body--custom-headers-none--raw-false----operation-config-). 

Dados de séries de tempo são enviados como uma série de [pontos](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.point(class)?view=azure-python) num [pedir](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.request(class)?view=azure-python) objeto. O `Request` objeto contém propriedades para descrever os dados ([granularidade](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.granularity?view=azure-python) por exemplo) e os parâmetros para a deteção de anomalias. 

A resposta de detetor de anomalias é um [LastDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-python) ou [EntireDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-python) objeto consoante o método utilizado. 

## <a name="getting-started"></a>Introdução

Crie uma aplicação Python nova no seu editor preferencial ou IDE. Em seguida, adicione as seguintes declarações de importação ao ficheiro. 

[!code-python[import declarations](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=imports)]

> [!NOTE]
> Este guia de introdução pressupõe que [criou uma variável de ambiente](../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) para a sua chave de detetor de anomalias, com o nome `ANOMALY_DETECTOR_KEY`.

Crie variáveis para a sua chave como uma variável de ambiente, o caminho para um ficheiro de dados de séries de tempo e a localização do azure da sua subscrição. Por exemplo, `westus2`. 

[!code-python[Vars for the key, path location and data path](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=initVars)]

## <a name="code-examples"></a>Exemplos de código 

Estes trechos de código mostram-lhe como fazer o seguinte com a biblioteca de cliente detetor de anomalias para .NET:

* [Autenticar o cliente](#authenticate-the-client)
* [Carregar um conjunto de dados de séries de tempo de um arquivo](#load-time-series-data-from-a-file)
* [Detetar anomalias em todo o conjunto de dados](#detect-anomalies-in-the-entire-data-set) 
* [Detectar o status de anomalias do ponto de dados mais recentes](#detect-the-anomaly-status-of-the-latest-data-point)

### <a name="authenticate-the-client"></a>Autenticar o cliente

Adicione a variável de localização do azure para o ponto final e autenticar o cliente com a sua chave.

[!code-python[Client authentication](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=client)]

### <a name="load-time-series-data-from-a-file"></a>Carregar dados de séries de tempo de um arquivo

Transferir os dados de exemplo para este início rápido da [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/example-data/request-data.csv):
1. No seu browser, clique com botão direito **Raw**
2. Clique em **link de guardar como**
3. Guarde o ficheiro para o diretório de aplicações, como um ficheiro. csv.

Estes dados de séries de tempo são formatados como um ficheiro. csv e serão enviados para a API de detetor de anomalias.

Carregar o ficheiro de dados com a biblioteca de Pandas `read_csv()` método e faça uma variável para armazenar sua série de dados de lista vazia. Iterar por meio do arquivo e acrescentar os dados como um [ponto](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.point%28class%29?view=azure-python) objeto. Este objeto irá conter o timestamp e um valor numérico de linhas do seu arquivo de dados. csv. 

[!code-python[Load the data file](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=loadDataFile)]

Criar uma [pedir](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.request%28class%29?view=azure-python) objeto com sua série de tempo e o [granularidade](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.granularity?view=azure-python) (ou de periodicidade) dos respetivos pontos de dados. Por exemplo, `Granularity.daily`.

[!code-python[Create the request object](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=request)]

### <a name="detect-anomalies-in-the-entire-data-set"></a>Detetar anomalias em todo o conjunto de dados 

Chamar a API para detetar anomalias através dos dados de série de tempo inteira com o cliente [entire_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#entire-detect-body--custom-headers-none--raw-false----operation-config-) método. Store retornado [EntireDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-python) objeto. A resposta a iteração `is_anomaly` listar e imprimir o índice de qualquer `true` valores. Esses valores correspondem ao índice de pontos de dados anómalas, se for detetada qualquer.

[!code-python[Batch anomaly detection sample](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=detectAnomaliesBatch)]

### <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Detectar o status de anomalias do ponto de dados mais recentes

Chamar a API de detetor de anomalias para determinar se o seu ponto de dados mais recentes é uma anomalia através do cliente [last_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#last-detect-body--custom-headers-none--raw-false----operation-config-) método e o arquivo retornado [LastDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-python) objeto. A resposta `is_anomaly` valor é um valor booleano que especifica o estado de anomalias desse ponto.  

[!code-python[Batch anomaly detection sample](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=latestPointDetection)]

## <a name="run-the-application"></a>Executar a aplicação

Executar a aplicação no seu IDE ou na linha de comandos com o `python` comando e o seu nome de ficheiro.
 
## <a name="clean-up-resources"></a>Limpar recursos

Se quiser limpar e remover uma subscrição de serviços cognitivos, é possível eliminar o recurso ou grupo de recursos. Eliminar o grupo de recursos também elimina a quaisquer outros recursos associados ao grupo de recursos.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

Também pode executar o seguinte comando do shell de cloud para remover o grupo de recursos e os respetivos recursos associados. Isto pode demorar alguns minutos a concluir. 

```azurecli-interactive
az group delete --name example-anomaly-detector-resource-group
```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
>[Deteção de anomalias de transmissão em fluxo com o Azure Databricks](../tutorials/anomaly-detection-streaming-databricks.md)

* O que é o [anomalias detetor de API?](../overview.md)
* [Melhores práticas](../concepts/anomaly-detection-best-practices.md) ao utilizar a API de detetor de anomalias.
* O código-fonte para este exemplo pode ser encontrado no [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/sdk/csharp-sdk-sample.cs).
