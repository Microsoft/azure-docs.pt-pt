---
title: Início rápido da biblioteca de clientes do Detetor de Anomalias .NET
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/22/2020
ms.author: mbullwin
ms.openlocfilehash: 14b361ae2163636864b37d2a063ad10886a9e3c8
ms.sourcegitcommit: dd45ae4fc54f8267cda2ddf4a92ccd123464d411
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/29/2020
ms.locfileid: "93029502"
---
Começa com a biblioteca de clientes do Detetor de Anomalias para .NET. Siga estes passos para instalar o pacote comece a usar os algoritmos fornecidos pelo serviço. O serviço Detetor de Anomalias permite-lhe encontrar anomalias nos dados da sua série de tempo utilizando automaticamente os modelos mais adequados, independentemente da indústria, cenário ou volume de dados.

Utilize a biblioteca do cliente do Detetor de Anomalias para .NET para:

* Detetar anomalias ao longo do conjunto de dados da série de tempo, como um pedido de lote
* Detetar o estado de anomalia do último ponto de dados da sua série de tempo
* Detete pontos de mudança de tendência no seu conjunto de dados.

[Documentação de referência da biblioteca](https://aka.ms/anomaly-detector-dotnet-ref)  |  [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/AnomalyDetector)  |  [Pacote (NuGet)](https://www.nuget.org/packages/Azure.AI.AnomalyDetector/3.0.0-preview.2)  |  [Encontre o código no GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/sdk/csharp-sdk-sample.cs)

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services)
* A versão atual de [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)
* Assim que tiver a subscrição do Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title=" crie um recurso de Detetor de "  target="_blank"> Anomalias crie um recurso de Detetor de <span class="docon docon-navigate-external x-hidden-focus"></span> </a> Anomalias no portal Azure para obter a sua chave e ponto final. Aguarde que seja implantado e clique no botão Go para o botão **de recursos.**
    * Necessitará da chave e ponto final do recurso que criar para ligar a sua aplicação à API do Detetor de Anomalias. Colará a chave e o ponto final no código abaixo mais tarde no arranque rápido.
    Pode utilizar o nível de preços gratuitos `F0` para experimentar o serviço e fazer upgrade mais tarde para um nível pago para produção.

## <a name="setting-up"></a>Configuração

[!INCLUDE [anomaly-detector-environment-variables](../environment-variables.md)]

### <a name="create-a-new-net-core-application"></a>Criar uma nova aplicação .NET Core

Numa janela de consola (como cmd, PowerShell ou Bash), utilize o `dotnet new` comando para criar uma nova aplicação de consola com o nome `anomaly-detector-quickstart` . Este comando cria um projeto simples "Hello World" com um único ficheiro de origem C#: *Program.cs* .

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

### <a name="install-the-client-library"></a>Instalar a biblioteca do cliente

Dentro do diretório de aplicações, instale a biblioteca cliente do Detetor de Anomalias para .NET com o seguinte comando:

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.AnomalyDetector
```

A partir do diretório do projeto, abra o ficheiro *program.cs* e adicione o seguinte `directives` utilizando:

[!code-csharp[using statements](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=usingStatements)]

No método da `main()` aplicação, crie variáveis para a localização Azure do seu recurso e a sua chave como variável ambiental. Se criou a variável ambiental após o lançamento da aplicação, o editor, o IDE ou o shell running terá de ser fechado e recarregado para aceder à variável.

[!code-csharp[Main method](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=mainMethod)]

## <a name="object-model"></a>Modelo de objeto

O cliente do Detetor de Anomalias é um objeto [AnomalyDetectorClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclient) que autentica a Azure usando [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.apikeyserviceclientcredentials), que contém a sua chave. O cliente pode fazer a deteção de anomalias em todo um conjunto de dados utilizando [o WholeDetectAsync()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.entiredetectasync)ou no último ponto de dados usando [o LastDetectAsync()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.lastdetectasync). O método [ChangePointDetectAsync](https://aka.ms/anomaly-detector-dotnet-ref) deteta pontos que marcam alterações numa tendência.

Os dados da série de tempo são enviados como uma série de [pontos](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request.series?view=azure-dotnet-preview#Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_Series) num objeto [Request.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request) O `Request` objeto contém propriedades para descrever os dados[(Granularidade,](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request.granularity) por exemplo), e parâmetros para a deteção de anomalias.

A resposta do Detetor de Anomalias é ou um [TotalDetectResponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse), [LastDetectResponse,](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse)ou alterar o [objetoPointDetectResponse,](https://aka.ms/anomaly-detector-dotnet-ref) dependendo do método utilizado.

## <a name="code-examples"></a>Exemplos de código

Estes fragmentos de código mostram-lhe como fazer o seguinte com a biblioteca de clientes do Detetor de Anomalias para .NET:

* [Autenticar o cliente](#authenticate-the-client)
* [Carregue um conjunto de dados da série de tempo a partir de um ficheiro](#load-time-series-data-from-a-file)
* [Detetar anomalias em todo o conjunto de dados](#detect-anomalies-in-the-entire-data-set)
* [Detetar o estado de anomalia do último ponto de dados](#detect-the-anomaly-status-of-the-latest-data-point)
* [Detetar os pontos de alteração no conjunto de dados](#detect-change-points-in-the-data-set)

## <a name="authenticate-the-client"></a>Autenticar o cliente

Num novo método, instantaneamente um cliente com o seu ponto final e chave. Crie um objeto [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.apikeyserviceclientcredentials?view=azure-dotnet-preview) com a sua chave e use-o com o seu ponto final para criar um objeto [AnomalyDetectorClient.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-dotnet-preview)

[!code-csharp[Client authentication function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=createClient)]

## <a name="load-time-series-data-from-a-file"></a>Carregar dados da série de tempo a partir de um ficheiro

Descarregue os dados de exemplo para este arranque rápido do [GitHub:](https://github.com/Azure-Samples/AnomalyDetector/blob/master/example-data/request-data.csv)
1. No seu navegador, clique com o botão direito **Raw.**
2. Clique **em Guardar link como** .
3. Guarde o ficheiro para o seu diretório de candidaturas, como ficheiro .csv.

Estes dados da série de tempo são formatados como um ficheiro .csv, e serão enviados para a API do Detetor de Anomalias.

Crie um novo método para ler os dados da série de tempo e adicioná-lo a um objeto [Request.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request?view=azure-dotnet-preview) Ligue `File.ReadAllLines()` com o caminho do ficheiro e crie uma lista de objetos [point](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.point?view=azure-dotnet-preview) e despoja quaisquer caracteres de linha nova. Extraia os valores e separe o datestamp do seu valor numérico e adicione-os a um novo `Point` objeto.

Faça um `Request` objeto com a série de pontos, e para a `Granularity.Daily` [Granularidade](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.granularity?view=azure-dotnet-preview) (ou periodicidade) dos pontos de dados.

[!code-csharp[load the time series data file](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=GetSeriesFromFile)]

## <a name="detect-anomalies-in-the-entire-data-set"></a>Detetar anomalias em todo o conjunto de dados

Crie um método para ligar para o método [WholeDetectAsync do](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.entiredetectasync?view=azure-dotnet-preview#Microsoft_Azure_CognitiveServices_AnomalyDetector_AnomalyDetectorClientExtensions_EntireDetectAsync_Microsoft_Azure_CognitiveServices_AnomalyDetector_IAnomalyDetectorClient_Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_System_Threading_CancellationToken_) cliente com o `Request` objeto e aguardar a resposta como um objeto [TotalDetectResponse.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-dotnet-preview) Se a série de tempos contiver anomalias, itera através dos valores [IsAnomaly](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse.isanomaly?view=azure-dotnet-preview) da resposta e imprima-as que sejam `true` . Estes valores correspondem ao índice de pontos de dados anómalos, caso se encontrem.

[!code-csharp[EntireDetectSampleAsync() function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=entireDatasetExample)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Detetar o estado de anomalia do último ponto de dados

Crie um método para ligar para o método [LastDetectAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.lastdetectasync?view=azure-dotnet-preview#Microsoft_Azure_CognitiveServices_AnomalyDetector_AnomalyDetectorClientExtensions_LastDetectAsync_Microsoft_Azure_CognitiveServices_AnomalyDetector_IAnomalyDetectorClient_Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_System_Threading_CancellationToken_) do cliente com o `Request` objeto e aguardar a resposta como um objeto [LastDetectResponse.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-dotnet-preview) Verifique o atributo [IsAnomaly](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse.isanomaly?view=azure-dotnet-preview) da resposta para determinar se o último ponto de dados enviado foi ou não uma anomalia.

[!code-csharp[LastDetectSampleAsync() function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=latestPointExample)]

## <a name="detect-change-points-in-the-data-set"></a>Detetar pontos de alteração no conjunto de dados

Crie um método para ligar para o método [DetectChangePointAsync](https://aka.ms/anomaly-detector-dotnet-ref) do cliente com o `Request` objeto e aguardar a resposta como um objeto [ChangePointDetectResponse.](https://aka.ms/anomaly-detector-dotnet-ref) Verifique os valores isChangePoint da resposta e imprima os que forem `true` . Estes valores correspondem a pontos de mudança de tendência, se algum foi encontrado.

[!code-csharp[DetectChangePoint() function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=changePointExample)]

## <a name="run-the-application"></a>Executar a aplicação

Execute a aplicação com o `dotnet run` comando do seu diretório de candidaturas.

```dotnetcli
dotnet run
```

[!INCLUDE [anomaly-detector-next-steps](../quickstart-cleanup-next-steps.md)]
