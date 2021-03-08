---
title: Início rápido da biblioteca de clientes JavaScript do detetor de anomalias
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/22/2020
ms.author: mbullwin
ms.custom: devx-track-js
ms.openlocfilehash: ca842a3d235cd58cb12bf9d8d80419e42ddf8441
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102444969"
---
Começa com a biblioteca de clientes do Detetor de Anomalias para o JavaScript. Siga estes passos para instalar o pacote comece a usar os algoritmos fornecidos pelo serviço. O serviço Detetor de Anomalias permite-lhe encontrar anomalias nos dados da sua série de tempo utilizando automaticamente os modelos mais adequados, independentemente da indústria, cenário ou volume de dados.

Utilize a biblioteca de clientes do Detetor de Anomalias para o JavaScript para:

* Detetar anomalias ao longo do conjunto de dados da série de tempo, como um pedido de lote
* Detetar o estado de anomalia do último ponto de dados da sua série de tempo
* Detete pontos de mudança de tendência no seu conjunto de dados.

[Documentação de referência da biblioteca](https://go.microsoft.com/fwlink/?linkid=2090788)  |  [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/AnomalyDetector)  |  [Pacote (npm)](https://www.npmjs.com/package/%40azure/ai-anomaly-detector)  |  [Encontre o código no GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/javascript/AnomalyDetector)

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services)
* A versão atual de [Node.js](https://nodejs.org/)
* Assim que tiver a subscrição do Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title=" crie um recurso de Detetor de "  target="_blank"> Anomalias crie um recurso de Detetor de </a> Anomalias no portal Azure para obter a sua chave e ponto final. Aguarde que seja implantado e clique no botão Go para o botão **de recursos.**
    * Necessitará da chave e ponto final do recurso que criar para ligar a sua aplicação à API do Detetor de Anomalias. Colará a chave e o ponto final no código abaixo mais tarde no arranque rápido.
    Pode utilizar o nível de preços gratuitos `F0` para experimentar o serviço e fazer upgrade mais tarde para um nível pago para produção.

## <a name="setting-up"></a>Configuração

[!INCLUDE [anomaly-detector-environment-variables](../environment-variables.md)]

### <a name="create-a-new-nodejs-application"></a>Criar uma nova aplicação Node.js

Numa janela de consola (como cmd, PowerShell ou Bash), crie um novo diretório para a sua aplicação e navegue até ela. 

```console
mkdir myapp && cd myapp
```

Executar o `npm init` comando para criar uma aplicação de nó com um `package.json` ficheiro. 

```console
npm init
```

Criar um ficheiro com o nome `index.js` e importar as seguintes bibliotecas:

[!code-javascript[Import statements](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=imports)]

Crie variáveis no ponto final e chave Azure do seu recurso. Se criou a variável ambiental depois de ter lançado a aplicação, terá de fechar e reabrir o editor, o IDE ou a shell que a executa para aceder à variável. Crie outra variável para o ficheiro de dados de exemplo que irá descarregar num passo posterior e uma lista vazia para os pontos de dados. Em seguida, crie um `ApiKeyCredentials` objeto para conter a chave.

[!code-javascript[Initial endpoint and key variables](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=vars)]

### <a name="install-the-client-library"></a>Instalar a biblioteca do cliente

Instale os `ms-rest-azure` pacotes e `azure-cognitiveservices-anomalydetector` NPM. A biblioteca csv-parse também é usada neste arranque rápido:

```console
npm install @azure/ai-anomaly-detector @azure/ms-rest-js csv-parse
```

O ficheiro da sua aplicação `package.json` será atualizado com as dependências.

## <a name="object-model"></a>Modelo de objeto

O cliente do Detetor de Anomalias é um objeto [AnomalyDetectorClient](/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient) que autentica a Azure usando a sua chave. O cliente pode fazer a deteção de anomalias em todo um conjunto de dados utilizando [o Total Deetect()](/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient#entiredetect-request--servicecallback-entiredetectresponse--)ou no último ponto de dados utilizando [o LastDetect()](/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient#lastdetect-request--msrest-requestoptionsbase-). O método [ChangePointDetectAsync](https://go.microsoft.com/fwlink/?linkid=2090788) deteta pontos que marcam alterações numa tendência. 

Os dados da série de tempo são enviados como série de [pontos](/javascript/api/@azure/cognitiveservices-anomalydetector/point) num objeto [Request.](/javascript/api/@azure/cognitiveservices-anomalydetector/request) O `Request` objeto contém propriedades para descrever os dados[(Granularidade,](/javascript/api/@azure/cognitiveservices-anomalydetector/request#granularity) por exemplo), e parâmetros para a deteção de anomalias. 

A resposta do Detetor de Anomalias é um objeto [LastDetectResponse](/javascript/api/@azure/cognitiveservices-anomalydetector/lastdetectresponse), [TotalDetectResponse,](/javascript/api/@azure/cognitiveservices-anomalydetector/entiredetectresponse)ou [ChangePointDetectResponse,](https://go.microsoft.com/fwlink/?linkid=2090788) dependendo do método utilizado. 

## <a name="code-examples"></a>Exemplos de código 

Estes fragmentos de código mostram-lhe como fazer o seguinte com a biblioteca de clientes do Detetor de Anomalias para Node.js:

* [Autenticar o cliente](#authenticate-the-client)
* [Carregue um conjunto de dados da série de tempo a partir de um ficheiro](#load-time-series-data-from-a-file)
* [Detetar anomalias em todo o conjunto de dados](#detect-anomalies-in-the-entire-data-set) 
* [Detetar o estado de anomalia do último ponto de dados](#detect-the-anomaly-status-of-the-latest-data-point)
* [Detetar os pontos de alteração no conjunto de dados](#detect-change-points-in-the-data-set)

## <a name="authenticate-the-client"></a>Autenticar o cliente

Instantiizar um objeto [AnomalyDetectorClient](/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient) com o seu ponto final e credenciais.

[!code-javascript[Authentication](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=authentication)]

## <a name="load-time-series-data-from-a-file"></a>Carregar dados da série de tempo a partir de um ficheiro

Descarregue os dados de exemplo para este arranque rápido do [GitHub:](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/AnomalyDetector/request-data.csv)
1. No seu navegador, clique com o botão direito **Raw.**
2. Clique **em Guardar link como**.
3. Guarde o ficheiro para o seu diretório de candidaturas, como um ficheiro .csv.

Estes dados da série de tempo são formatados como um ficheiro .csv e serão enviados para a API do Detetor de Anomalias.

Leia o seu ficheiro de dados com o método da biblioteca csv-parse `readFileSync()` e analise o ficheiro com `parse()` . Para cada linha, empurre um objeto [point](/javascript/api/@azure/cognitiveservices-anomalydetector/point) contendo a estada de tempo e o valor numérico.

[!code-javascript[Read the data file](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=readFile)]

## <a name="detect-anomalies-in-the-entire-data-set"></a>Detetar anomalias em todo o conjunto de dados 

Ligue para a API para detetar anomalias durante toda a série de tempo como um lote com todo o método [do Doutor().](/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient#entiredetect-request--msrest-requestoptionsbase-) Guarde o objeto [Deresponse TotalDetect.](/javascript/api/@azure/cognitiveservices-anomalydetector/entiredetectresponse) Iterar através da lista de `isAnomaly` resposta, e imprimir o índice de quaisquer `true` valores. Estes valores correspondem ao índice de pontos de dados anómalos, caso se encontrem.

[!code-javascript[Batch detection function](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=batchCall)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Detetar o estado de anomalia do último ponto de dados

Ligue para a API do Detetor de Anomalias para determinar se o seu último ponto de dados é uma anomalia utilizando o [método último do](/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient#lastdetect-request--msrest-requestoptionsbase-) cliente e guarde o objeto [BackDetectResponse](/javascript/api/@azure/cognitiveservices-anomalydetector/lastdetectresponse) devolvido. O valor da resposta `isAnomaly` é um booleano que especifica o estado de anomalia desse ponto.  

[!code-javascript[Last point detection function](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=lastDetection)]

## <a name="detect-change-points-in-the-data-set"></a>Detetar pontos de alteração no conjunto de dados

Ligue para a API para detetar pontos de alteração na série de tempo com o método [detecteChangePoint do cliente.).](https://go.microsoft.com/fwlink/?linkid=2090788) Guarde o objeto [ChangePointDetectResponse](https://go.microsoft.com/fwlink/?linkid=2090788) devolvido. Iterar através da lista de `isChangePoint` resposta, e imprimir o índice de quaisquer `true` valores. Estes valores correspondem aos índices de variação de tendência, se algum for encontrado.

[!code-javascript[detect change points](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=changePointDetection)]

## <a name="run-the-application"></a>Executar a aplicação

Execute a aplicação com o `node` comando no seu ficheiro quickstart.

```console
node index.js
```

[!INCLUDE [anomaly-detector-next-steps](../quickstart-cleanup-next-steps.md)]
