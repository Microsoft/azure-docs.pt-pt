---
title: 'Início rápido: Detetar anomalias nos dados de séries de tempo com o SDK de detetor de anomalias para .NET'
titleSuffix: Azure Cognitive Services
description: Começar a detetar anomalias nos seus dados de séries de tempo com o serviço de detetor de anomalias.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 07/01/2019
ms.author: aahi
ms.openlocfilehash: c2a8bf10d7c279243c9f53801264ebbe9bfe72c9
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2019
ms.locfileid: "67503477"
---
# <a name="quickstart-anomaly-detector-client-library-for-net"></a>Início rápido: Biblioteca de clientes de detetor de anomalias para .NET

Introdução à biblioteca de cliente detetor de anomalias para .NET. Siga estes passos para instalar o pacote e experimentar o código de exemplo para tarefas básicas. O serviço de detetor de anomalias permite-lhe encontrar anomalias nos seus dados de séries de tempo automaticamente com os modelos de oferta fitting no mesmo, independentemente da indústria, o cenário ou o volume de dados.

Utilize a biblioteca de cliente de detetor de anomalias para .NET para:

* Detetar anomalias como um lote
* Detectar o status de anomalias do ponto de dados mais recentes

[Documentação de referência de API](https://docs.microsoft.com/dotnet/api/Microsoft.Azure.CognitiveServices.AnomalyDetector?view=azure-dotnet-preview) | [código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/AnomalyDetector) | [pacote (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.AnomalyDetector/) | [exemplos](https://github.com/Azure-Samples/anomalydetector)

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição do Azure - [criá-lo gratuitamente](https://azure.microsoft.com/free/)
* A versão atual do [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)

## <a name="setting-up"></a>Como configurar

### <a name="create-an-anomaly-detector-resource"></a>Criar um recurso de detetor de anomalias

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

### <a name="create-a-new-c-app"></a>Criar um novo C# aplicação

Crie uma nova aplicação de .NET Core no seu editor preferencial ou IDE. 

Numa janela de consola (por exemplo, cmd, o PowerShell ou Bash), utilize o dotnet `new` comando para criar uma nova aplicação de consola com o nome `anomaly-detector-quickstart`. Este comando cria um simples "Hello World" C# projeto com um único arquivo de origem: **Program.cs**. 

```console
dotnet new console -n anomaly-detector-quickstart
```

Altere o diretório para a pasta da aplicação criada recentemente. Pode criar a aplicação com:

```console
dotnet build
```

O resultado da compilação não deve conter nenhum aviso ou erro. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>Instalar a biblioteca de cliente

Dentro do diretório de aplicativo, instale a biblioteca de cliente detetor de anomalias para o .NET com o seguinte comando:

```console
dotnet add package Microsoft.Azure.CognitiveServices.AnomalyDetector --version 0.8.0-preview
```

Se estiver a utilizar o IDE do Visual Studio, a biblioteca de clientes está disponível como um pacote NuGet. 

## <a name="object-model"></a>Modelo de objeto

O cliente de detetor de anomalias é um [AnomalyDetectorClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclient) objeto que autentica para o Azure com [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.apikeyserviceclientcredentials), que contém a chave. O cliente fornece dois métodos de deteção de anomalias: Sobre como utilizar um conjunto de dados completo [EntireDetectAsync()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.entiredetectasync)e sobre os dados mais recentes apontar usando [LastDetectAsync()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.lastdetectasync). 

Dados de séries de tempo são enviados como uma série de [pontos](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request.series?view=azure-dotnet-preview#Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_Series) num [pedir](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request) objeto. O `Request` objeto contém propriedades para descrever os dados ([granularidade](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request.granularity) por exemplo) e os parâmetros para a deteção de anomalias. 

A resposta de detetor de anomalias é um [EntireDetectResponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse) ou [LastDetectResponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse) objeto, dependendo do método utilizado. 

## <a name="code-examples"></a>Exemplos de código

Estes trechos de código mostram-lhe como fazer o seguinte com a biblioteca de cliente detetor de anomalias para .NET:

* [Autenticar o cliente](#authenticate-the-client)
* [Carregar um conjunto de dados de séries de tempo de um arquivo](#load-time-series-data-from-a-file)
* [Detetar anomalias em todo o conjunto de dados](#detect-anomalies-in-the-entire-data-set) 
* [Detectar o status de anomalias do ponto de dados mais recentes](#detect-the-anomaly-status-of-the-latest-data-point)

### <a name="add-the-main-method"></a>Adicione o método principal

Do diretório do projeto:

1. Abra o ficheiro Program.cs no seu editor preferencial ou IDE
2. Adicione o seguinte `using` diretivas

[!code-csharp[using statements](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=usingStatements)]

> [!NOTE]
> Este guia de introdução pressupõe que [criou uma variável de ambiente](../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) para a sua chave de detetor de anomalias, com o nome `ANOMALY_DETECTOR_KEY`.

No aplicativo de `main()` método, criar variáveis para a localização do Azure do seu recurso e sua chave como uma variável de ambiente. Se tiver criado a variável de ambiente depois do aplicativo é iniciado, o editor, o IDE ou a shell de executá-lo precisará ser fechado e recarregado para aceder à variável.

[!code-csharp[Main method](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=mainMethod)]

### <a name="authenticate-the-client"></a>Autenticar o cliente

Num novo método, instancio um cliente com o ponto final e a chave. Criar uma [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.apikeyserviceclientcredentials?view=azure-dotnet-preview) com a sua chave de objeto e utilizá-lo com o ponto final para criar um [AnomalyDetectorClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-dotnet-preview) objeto. 

[!code-csharp[Client authentication function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=createClient)]
    
### <a name="load-time-series-data-from-a-file"></a>Carregar dados de séries de tempo de um arquivo

Transferir os dados de exemplo para este início rápido da [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/example-data/request-data.csv):
1. No seu browser, clique com botão direito **Raw**
2. Clique em **link de guardar como**
3. Guarde o ficheiro para o diretório de aplicações, como um ficheiro. csv.

Estes dados de séries de tempo são formatados como um ficheiro. csv e serão enviados para a API de detetor de anomalias.

Criar um novo método de leitura de dados de séries temporais e adicioná-lo para um [pedir](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request?view=azure-dotnet-preview) objeto. Chamar `File.ReadAllLines()` com o caminho do ficheiro e criar uma lista de [ponto](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.point?view=azure-dotnet-preview) objetos e todos os novos de faixa de linhas de carateres. Extrair os valores e separar o datestamp em relação ao valor numérico e adicioná-los para um novo `Point` objeto. 

Efetuar uma `Request` objeto com a série de pontos, e `Granularity.Daily` para o [granularidade](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.granularity?view=azure-dotnet-preview) (ou de periodicidade) dos pontos de dados.

[!code-csharp[load the time series data file](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=GetSeriesFromFile)]

### <a name="detect-anomalies-in-the-entire-data-set"></a>Detetar anomalias em todo o conjunto de dados 

Criar um método para chamar o cliente [EntireDetectAsync()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.entiredetectasync?view=azure-dotnet-preview#Microsoft_Azure_CognitiveServices_AnomalyDetector_AnomalyDetectorClientExtensions_EntireDetectAsync_Microsoft_Azure_CognitiveServices_AnomalyDetector_IAnomalyDetectorClient_Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_System_Threading_CancellationToken_) método com o `Request` de objeto e aguardar a resposta como uma [EntireDetectResponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-dotnet-preview) objeto. Se a série de tempo contiver quaisquer anomalias, iterar por meio da resposta [IsAnomaly](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse.isanomaly?view=azure-dotnet-preview) valores e print quaisquer que sejam `true`. Esses valores correspondem ao índice de pontos de dados anómalas, se for detetada qualquer.

[!code-csharp[EntireDetectSampleAsync() function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=entireDatasetExample)]

### <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Detectar o status de anomalias do ponto de dados mais recentes

Criar um método para chamar o cliente [LastDetectAsync()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.lastdetectasync?view=azure-dotnet-preview#Microsoft_Azure_CognitiveServices_AnomalyDetector_AnomalyDetectorClientExtensions_LastDetectAsync_Microsoft_Azure_CognitiveServices_AnomalyDetector_IAnomalyDetectorClient_Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_System_Threading_CancellationToken_) método com o `Request` de objeto e aguardar a resposta como uma [LastDetectResponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-dotnet-preview) objeto. Verificar a resposta [IsAnomaly](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse.isanomaly?view=azure-dotnet-preview) atributo para determinar se o ponto de dados mais recentes enviado foi uma anomalia ou não. 

[!code-csharp[LastDetectSampleAsync() function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=latestPointExample)]

## <a name="run-the-application"></a>Executar a aplicação

Executar a aplicação com o dotnet `run` comando a partir do seu aplicativo.

```dotnet
dotnet run
```

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
