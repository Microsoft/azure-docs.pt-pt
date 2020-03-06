---
title: 'Quickstart: Detete anomalias nos dados da série de tempo utilizando a biblioteca cliente do Detetor de Anomalias para .NET'
titleSuffix: Azure Cognitive Services
description: Este quickstart mostra como usar a API do Detetor de Anomalias para detetar anomalias na sua série de dados, quer como um lote ou em dados de streaming.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 11/19/2019
ms.author: aahi
ms.openlocfilehash: fdb35edc35e07ed4ee718281942565a8f1d061d4
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78402686"
---
# <a name="quickstart-anomaly-detector-client-library-for-net"></a>Quickstart: Biblioteca de clientes do Detetor de Anomalias para .NET

Começa com a biblioteca de clientes do Detetor de Anomalias para .NET. Siga estes passos para instalar a embalagem e experimente o código de exemplo para tarefas básicas. O serviço Anomaly Detetor permite-lhe encontrar anomalias nos dados da série de tempo utilizando automaticamente os modelos mais adequados, independentemente da indústria, cenário ou volume de dados.

Utilize a biblioteca cliente do Detetor de Anomalias para .NET para:

* Detete anomalias ao longo do conjunto de dados da série de tempo, como um pedido de lote
* Detete o estado da anomalia do último ponto de dados da sua série de tempo

[Documentação de referência da biblioteca](https://docs.microsoft.com/dotnet/api/Microsoft.Azure.CognitiveServices.AnomalyDetector?view=azure-dotnet-preview) | código de origem da [biblioteca](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/AnomalyDetector) | [Pacote (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.AnomalyDetector/) | [Encontrar o código no GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/sdk/csharp-sdk-sample.cs)

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura Azure - [Criar uma gratuitamente](https://azure.microsoft.com/free/)
* A versão atual de [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)
* Uma chave e ponto final do detetor de anomalias

## <a name="setting-up"></a>Configuração

### <a name="create-an-anomaly-detector-resource"></a>Criar um recurso do Detetor de Anomalias

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

### <a name="create-a-new-net-core-application"></a>Criar uma nova aplicação .NET Core

Numa janela de consola (como cmd, PowerShell ou Bash), utilize o comando `dotnet new` para criar uma nova aplicação de consola com o nome `anomaly-detector-quickstart`. Este comando cria um simples projeto "Hello World" com um único C# ficheiro fonte: *Program.cs*. 

```dotnetcli
dotnet new console -n anomaly-detector-quickstart
```

Mude o seu diretório para a pasta de aplicações recém-criada. Pode construir a aplicação com:

```dotnetcli
dotnet build
```

A saída de construção não deve conter avisos ou erros. 

```output
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>Instale a biblioteca do cliente

Dentro do diretório de aplicações, instale a biblioteca cliente do Detetor de Anomalias para .NET com o seguinte comando:

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.AnomalyDetector --version 0.8.0-preview
```

A partir do diretório do projeto, abra o ficheiro *program.cs* e adicione o seguinte utilizando `directives`:

[!code-csharp[using statements](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=usingStatements)]

No método `main()` da aplicação, crie variáveis para a localização Azure do seu recurso, e a sua chave como variável ambiental. Se criou a variável ambiental após o lançamento da aplicação, o editor, IDE ou shell running terá de ser fechado e recarregado para aceder à variável.

[!code-csharp[Main method](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=mainMethod)]

## <a name="object-model"></a>Modelo de objeto

O cliente do Detetor de Anomalias é um objeto [AnomalyDetectorClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclient) que autentica o Azure utilizando [a ApiKeyServiceClientCredentials,](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.apikeyserviceclientcredentials)que contém a sua chave. O cliente fornece dois métodos de deteção de anomalias: Em todo um conjunto de dados utilizando [WholeDetectAsync()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.entiredetectasync)e no último ponto de dados utilizando [lastDetectAsync()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.lastdetectasync). 

Os dados da série time são enviados como uma série de [Pontos](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request.series?view=azure-dotnet-preview#Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_Series) num objeto [de Pedido.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request) O `Request` objeto contém propriedades para descrever os dados[(granularidade,](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request.granularity) por exemplo), e parâmetros para a deteção de anomalias. 

A resposta do Detetor de Anomalias é um objeto [InteiraDetectResponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse) ou [LastDetectResponse,](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse) dependendo do método utilizado. 

## <a name="code-examples"></a>Exemplos de código

Estes fragmentos de código mostram-lhe como fazer o seguinte com a biblioteca de clientes do Detetor de Anomalias para .NET:

* [Autenticar o cliente](#authenticate-the-client)
* [Carregue um conjunto de dados da série de tempo a partir de um ficheiro](#load-time-series-data-from-a-file)
* [Detetar anomalias em todo o conjunto de dados](#detect-anomalies-in-the-entire-data-set) 
* [Detete o estado da anomalia do último ponto de dados](#detect-the-anomaly-status-of-the-latest-data-point)

## <a name="authenticate-the-client"></a>Autenticar o cliente

Num novo método, instanteie um cliente com o seu ponto final e chave. Crie um objeto [ApiKeyServiceClientCredenciais](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.apikeyserviceclientcredentials?view=azure-dotnet-preview) com a sua chave e use-o com o seu ponto final para criar um objeto [AnomalyDetectorClient.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-dotnet-preview) 

[!code-csharp[Client authentication function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=createClient)]
    
## <a name="load-time-series-data-from-a-file"></a>Dados da série de tempo de carga de um ficheiro

Descarregue os dados de exemplo para este arranque rápido a partir do [GitHub:](https://github.com/Azure-Samples/AnomalyDetector/blob/master/example-data/request-data.csv)
1. No seu navegador, clique em **Raw.**
2. Clique no **link Guardar como**.
3. Guarde o ficheiro para o seu diretório de candidatura, como ficheiro .csv.

Estes dados da série de tempo são formatados como um ficheiro .csv, e serão enviados para a API do Detetor de Anomalias.

Crie um novo método para ler os dados da série de tempo e adicioná-lo a um objeto [De Pedido.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request?view=azure-dotnet-preview) Ligue `File.ReadAllLines()` com o caminho do ficheiro e crie uma lista de objetos [Point](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.point?view=azure-dotnet-preview) e despete quaisquer caracteres de linha nova. Extrair os valores e separar a data do seu valor numérico e adicioná-los a um novo objeto `Point`. 

Faça um `Request` oposição com a série de pontos, e `Granularity.Daily` para a [granularidade](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.granularity?view=azure-dotnet-preview) (ou periodicidade) dos pontos de dados.

[!code-csharp[load the time series data file](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=GetSeriesFromFile)]

## <a name="detect-anomalies-in-the-entire-data-set"></a>Detetar anomalias em todo o conjunto de dados 

Crie um método para ligar para o método [WholeDetectAsync()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.entiredetectasync?view=azure-dotnet-preview#Microsoft_Azure_CognitiveServices_AnomalyDetector_AnomalyDetectorClientExtensions_EntireDetectAsync_Microsoft_Azure_CognitiveServices_AnomalyDetector_IAnomalyDetectorClient_Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_System_Threading_CancellationToken_) do cliente com o objeto `Request` e aguarde a resposta como um objeto [InteiraDetectResponse.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-dotnet-preview) Se a série de tempo contiver quaisquer anomalias, iterar através dos valores [isAnomaly](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse.isanomaly?view=azure-dotnet-preview) da resposta e imprimir quaisquer que sejam `true`. Estes valores correspondem ao índice de pontos de dados anómalos, caso se encontre algum.

[!code-csharp[EntireDetectSampleAsync() function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=entireDatasetExample)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Detete o estado da anomalia do último ponto de dados

Crie um método para ligar para o método [LastDetectAsync()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.lastdetectasync?view=azure-dotnet-preview#Microsoft_Azure_CognitiveServices_AnomalyDetector_AnomalyDetectorClientExtensions_LastDetectAsync_Microsoft_Azure_CognitiveServices_AnomalyDetector_IAnomalyDetectorClient_Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_System_Threading_CancellationToken_) do cliente com o `Request` objeto e aguarde a resposta como um objeto [LastDetectResponse.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-dotnet-preview) Verifique o atributo [isAnomaly](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse.isanomaly?view=azure-dotnet-preview) da resposta para determinar se o último ponto de dados enviado foi uma anomalia ou não. 

[!code-csharp[LastDetectSampleAsync() function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=latestPointExample)]

## <a name="run-the-application"></a>Executar a aplicação

Execute a aplicação com o comando `dotnet run` do seu diretório de aplicações.

```dotnetcli
dotnet run
```

[!INCLUDE [anomaly-detector-next-steps](../includes/quickstart-cleanup-next-steps.md)]
