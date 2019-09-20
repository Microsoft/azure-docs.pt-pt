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
ms.date: 09/17/2019
ms.author: aahi
ms.openlocfilehash: 320c690eb873f760af89b7514893f14ecc209323
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71106961"
---
# <a name="quickstart-anomaly-detector-client-library-for-nodejs"></a>Início rápido: Biblioteca de cliente do detector de anomalias para node. js

Introdução à biblioteca de cliente do detector de anomalias para node. js. Siga estas etapas para instalar o pacote e experimentar o código de exemplo para tarefas básicas. O serviço de detector de anomalias permite que você encontre anormalidades em seus dados de série temporal usando automaticamente os modelos de melhor ajuste, independentemente do setor, cenário ou volume de dados.

Use a biblioteca de cliente do detector de anomalias para node. js para:

* Detectar anomalias em todo o seu conjunto de data de série temporal, como uma solicitação em lote
* Detectar o status de anomalia do último ponto de dados em sua série temporal

[](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/?view=azure-node-latest) | [Exemplos](https://github.com/Azure-Samples/anomalydetector) [de NPM (pacote de](https://www.npmjs.com/package/@azure/cognitiveservices-anomalydetector) | [código](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/AnomalyDetector) | -fonte de biblioteca de documentação de referência)

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure- [crie uma gratuitamente](https://azure.microsoft.com/free/)
* A versão atual do [node. js](https://nodejs.org/)

## <a name="setting-up"></a>Configurando

### <a name="create-an-anomaly-detector-azure-resource"></a>Criar um recurso do Azure detector de anomalias

Os serviços cognitivas do Azure são representados pelos recursos do Azure que você assina. Crie um recurso para o detector de anomalias usando o [portal do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) ou [CLI do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) no computador local. Também pode:

* Obtenha uma [chave de avaliação](https://azure.microsoft.com/try/cognitive-services/#decision) válida por 7 dias gratuitamente. Depois de inscrever-se, ele estará disponível no [site do Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Exiba seu recurso no [portal do Azure](https://portal.azure.com/).

Depois de obter uma chave de sua assinatura ou recurso de avaliação, [crie uma variável de ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para a chave `ANOMALY_DETECTOR_KEY`, denominada. Em seguida, crie um para o ponto de `ANOMALY_DETECTOR_ENDPOINT`extremidade do Azure, chamado.
 
### <a name="create-a-new-nodejs-application"></a>Criar uma nova aplicação Node.js

Em uma janela de console (como cmd, PowerShell ou bash), crie um novo diretório para seu aplicativo e navegue até ele. 

```console
mkdir myapp && cd myapp
```

Execute o `npm init` comando para criar um aplicativo de nó com `package.json` um arquivo. 

```console
npm init
```

Crie um arquivo chamado `index.js` e importe as seguintes bibliotecas:

[!code-javascript[Import statements](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=imports)]

Crie variáveis do ponto de extremidade do Azure e da chave do recurso. Se você criou a variável de ambiente depois de iniciar o aplicativo, será necessário fechar e reabrir o editor, IDE ou shell que o executa para acessar a variável. Crie outra variável para o arquivo de dados de exemplo que você vai baixar em uma etapa posterior. Em seguida, crie um objeto ApiKeyCredentials para conter a chave.

[!code-javascript[Initial variables](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=vars)]

### <a name="install-the-client-library"></a>Instalar a biblioteca de cliente

Instale os `ms-rest-azure` pacotes `azure-cognitiveservices-anomalydetector` e NPM. A biblioteca de análise de CSV também é usada neste guia de início rápido:

```console
npm install  @azure/cognitiveservices-anomalydetector ms-rest-azure csv-parse
```

O arquivo do `package.json` aplicativo será atualizado com as dependências.

## <a name="object-model"></a>Modelo de objeto

O cliente do detector de anomalias é um objeto [AnomalyDetectorClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest) que se autentica no Azure usando sua chave. O cliente fornece dois métodos de detecção de anomalias: Em um conjunto de dados inteiro usando [entireDetect ()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest#entiredetect-request--servicecallback-entiredetectresponse--)e no último ponto de data usando [LastDetect ()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest#lastdetect-request--msrest-requestoptionsbase-). 

Os dados de série temporal são enviados como uma série de [pontos](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/point?view=azure-node-latest) em um objeto de [solicitação](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/request?view=azure-node-latest) . O `Request` objeto contém propriedades para descrever os dados ([granularidade](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/request?view=azure-node-latest#granularity) , por exemplo) e parâmetros para a detecção de anomalias. 

A resposta do detector de anomalias é um objeto [LastDetectResponse](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/lastdetectresponse?view=azure-node-latest) ou [EntireDetectResponse](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/entiredetectresponse?view=azure-node-latest) dependendo do método usado. 

## <a name="code-examples"></a>Exemplos de código 

Esses trechos de código mostram como fazer o seguinte com a biblioteca de cliente do detector de anomalias para node. js:

* [Autenticar o cliente](#authenticate-the-client)
* [Carregar um conjunto de dados de série temporal de um arquivo](#load-time-series-data-from-a-file)
* [Detectar anomalias em todo o conjunto de dados](#detect-anomalies-in-the-entire-data-set) 
* [Detectar o status de anomalia do último ponto de dados](#detect-the-anomaly-status-of-the-latest-data-point)

## <a name="authenticate-the-client"></a>Autenticar o cliente

> [!NOTE]
> Este início rápido pressupõe que você [criou uma variável de ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para a chave do detector de `ANOMALY_DETECTOR_KEY`anomalias, denominada.

Crie uma instância de um objeto [AnomalyDetectorClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest) com seu ponto de extremidade e suas credenciais.

[!code-javascript[Authentication](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=authentication)]

## <a name="load-time-series-data-from-a-file"></a>Carregar dados de série temporal de um arquivo

Baixe os dados de exemplo para este guia de início rápido do [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/AnomalyDetector/request-data.csv):
1. No navegador, clique com o botão direito do mouse em **RAW**.
2. Clique em **Salvar link como**.
3. Salve o arquivo em seu diretório de aplicativo, como um arquivo. csv.

Esses dados de série temporal são formatados como um arquivo. csv e serão enviados para a API do detector de anomalias.

Leia o arquivo de dados com o método da `readFileSync()` biblioteca CSV-Parse e analise o arquivo com. `parse()` Para cada linha, envie por push um objeto [Point](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/point?view=azure-node-latest) contendo o carimbo de data e hora e o valor numérico.

[!code-javascript[Read the data file](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=readFile)]

## <a name="detect-anomalies-in-the-entire-data-set"></a>Detectar anomalias em todo o conjunto de dados 

Chame a API para detectar anomalias por meio de toda a série temporal como um lote com o método [entireDetect ()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest#entiredetect-request--msrest-requestoptionsbase-) do cliente. Armazene o objeto [EntireDetectResponse](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/entiredetectresponse?view=azure-node-latest) retornado. Itere na lista da `isAnomaly` resposta e imprima o índice de quaisquer `true` valores. Esses valores correspondem ao índice de pontos de dados anormais, se algum for encontrado.

[!code-javascript[Batch detection function](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=batchCall)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Detectar o status de anomalia do último ponto de dados

Chame a API do detector de anomalias para determinar se o último ponto de dados é uma anomalia usando o método [lastDetect ()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest#lastdetect-request--msrest-requestoptionsbase-) do cliente e armazene o objeto [LastDetectResponse](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/lastdetectresponse?view=azure-node-latest) retornado. O valor da `isAnomaly` resposta é um booliano que especifica o status de anomalia do ponto.  

[!code-javascript[Last point detection function](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=lastDetection)]

## <a name="run-the-application"></a>Executar a aplicação

Execute o aplicativo com o `node` comando no seu arquivo de início rápido.

```console
node index.js
```

## <a name="clean-up-resources"></a>Limpar recursos

Se você quiser limpar e remover uma assinatura de serviços cognitivas, poderá excluir o recurso ou grupo de recursos. Excluir o grupo de recursos também exclui todos os outros recursos associados a ele.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
>[Detecção de anomalias de streaming com Azure Databricks](../tutorials/anomaly-detection-streaming-databricks.md)

* O que é a [API do detector de anomalias?](../overview.md)
* [Práticas recomendadas](../concepts/anomaly-detection-best-practices.md) ao usar a API do detector de anomalias.
* O código-fonte para este exemplo pode ser encontrado no [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/sdk/csharp-sdk-sample.cs).
