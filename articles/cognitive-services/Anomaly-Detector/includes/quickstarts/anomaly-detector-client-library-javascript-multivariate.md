---
title: Início rápido da biblioteca de clientes JavaScript multivariado do detetor de anomalias
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/06/2021
ms.author: mbullwin
ms.openlocfilehash: 656270c80e8da0ece83bb04190fa7e5710a0203e
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107880087"
---
Começa com a biblioteca multivariada do Detetor de Anomalias para o JavaScript. Siga estes passos para instalar o pacote e comece a utilizar os algoritmos fornecidos pelo serviço. As novas APIs de deteção de anomalias multivariadas permitem aos desenvolvedores integrar facilmente a IA avançada para detetar anomalias de grupos de métricas, sem necessidade de conhecimentos de aprendizagem automática ou dados rotulados. As dependências e as inter-correlações entre diferentes sinais são automaticamente contabilizadas como factores-chave. Isto ajuda-o a proteger proativamente os seus sistemas complexos de falhas.

Utilize a biblioteca multivariada do Detetor de Anomalias para o JavaScript para:

* Detete anomalias de nível do sistema de um grupo de séries temporais.
* Quando qualquer série de tempo individual não lhe dirá muito e tem que olhar todos os sinais para detetar um problema.
* Manutenção pré-ativa de ativos físicos dispendiosos com dezenas a centenas de diferentes tipos de sensores medindo vários aspetos da saúde do sistema.

[Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/anomalydetector/ai-anomaly-detector)  |  [Pacote (npm)](https://www.npmjs.com/package/@azure/ai-anomaly-detector)  |  [Código de amostra](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/anomalydetector/ai-anomaly-detector/samples/v3/javascript/sample_multivariate_detection.js)

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services)
* A versão atual de [Node.js](https://nodejs.org/)
* Assim que tiver a subscrição do Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title=" crie um recurso de Detetor de "  target="_blank"> Anomalias crie um recurso de Detetor de </a> Anomalias no portal Azure para obter a sua chave e ponto final. Aguarde que seja implantado e clique no botão Go para o botão **de recursos.**
    * Necessitará da chave e ponto final do recurso que criar para ligar a sua aplicação à API do Detetor de Anomalias. Colará a chave e o ponto final no código abaixo mais tarde no arranque rápido.
    Pode utilizar o nível de preços gratuitos `F0` para experimentar o serviço e fazer upgrade mais tarde para um nível pago para produção.

## <a name="setting-up"></a>Configuração

### <a name="create-a-new-nodejs-application"></a>Criar uma nova aplicação Node.js

Numa janela de consola (como cmd, PowerShell ou Bash), crie um novo diretório para a sua aplicação e navegue até ela. 

```console
mkdir myapp && cd myapp
```

Executar o `npm init` comando para criar uma aplicação de nó com um `package.json` ficheiro. 

```console
npm init
```

Criar um ficheiro com o nome `index.js` e importar as seguintes bibliotecas: '
```javascript
'use strict'

const fs = require('fs');
const parse = require("csv-parse/lib/sync");
const { AnomalyDetectorClient } = require('@azure/ai-anomaly-detector');
const { AzureKeyCredential } = require('@azure/core-auth');
```

Crie variáveis no ponto final e chave Azure do seu recurso. Crie outra variável para o ficheiro de dados de exemplo.

```javascript
const apiKey = "YOUR_API_KEY";
const endpoint = "YOUR_ENDPOINT";
const data_source = "YOUR_SAMPLE_ZIP_FILE_LOCATED_IN_AZURE_BLOB_STORAGE_WITH_SAS";
```

 Para usar as APIs multivariadas do Detetor de Anomalias, precisamos de treinar o nosso próprio modelo antes de usarmos a deteção. Os dados utilizados para o treino são um lote de séries de tempo, cada série de tempo deve estar em formato CSV com duas colunas, relógio e valor. Todas as séries horadas devem ser fechadas num ficheiro zip e ser enviadas para [o armazenamento da Azure Blob](../../../../storage/blobs/storage-blobs-introduction.md). Por predefinição, o nome do ficheiro será utilizado para representar a variável para a série de tempo. Alternativamente, um meta.jsextra no ficheiro pode ser incluído no ficheiro zip se desejar que o nome da variável seja diferente do nome de ficheiro .zip. Assim que gerarmos [URL BLOB SAS (Assinaturas de acesso partilhado), podemos](../../../../storage/common/storage-sas-overview.md)usar o url para o ficheiro zip para o treino.

### <a name="install-the-client-library"></a>Instalar a biblioteca do cliente

Instale os `ms-rest-azure` pacotes e `azure-ai-anomalydetector` NPM. A biblioteca csv-parse também é usada neste arranque rápido:

```console
npm install @azure/ai-anomaly-detector csv-parse
```

O ficheiro da sua aplicação `package.json` será atualizado com as dependências.

## <a name="code-examples"></a>Exemplos de código

Estes fragmentos de código mostram-lhe como fazer o seguinte com a biblioteca de clientes do Detetor de Anomalias para Node.js:

* [Autenticar o cliente](#authenticate-the-client)
* [Preparar um modelo](#train-a-model)
* [Detetar anomalias](#detect-anomalies)
* [Modelo de exportação](#export-model)
* [Eliminar modelo](#delete-model)

## <a name="authenticate-the-client"></a>Autenticar o cliente

Instantiar um `AnomalyDetectorClient` objeto com o seu ponto final e credenciais.

```javascript
const client = new AnomalyDetectorClient(endpoint, new AzureKeyCredential(apiKey));
```

## <a name="train-a-model"></a>Preparar um modelo

### <a name="construct-a-model-result"></a>Construa um resultado de modelo

Primeiro, temos de construir um pedido de modelo. Certifique-se de que o tempo de início e fim se alinha com a sua fonte de dados.

```javascript
const Modelrequest = {
      source: data_source,
      startTime: new Date(2021,0,1,0,0,0),
      endTime: new Date(2021,0,2,12,0,0),
      slidingWindow:200
    };    
```

### <a name="train-a-new-model"></a>Treine um novo modelo

Terá de passar o seu pedido de modelo ao método do cliente do Detetor de `trainMultivariateModel` Anomalias.

```javascript
console.log("Training a new model...")
const train_response = await client.trainMultivariateModel(Modelrequest)
const model_id = train_response.location?.split("/").pop() ?? ""
console.log("New model ID: " + model_id)
```

Para verificar se o treino do seu modelo está completo, pode acompanhar o estado do modelo:

```javascript
let model_response = await client.getMultivariateModel(model_id)
let model_status = model_response.modelInfo?.status

while (model_status != 'READY'){
    await sleep(10000).then(() => {});
    model_response = await client.getMultivariateModel(model_id)
    model_status = model_response.modelInfo?.status
}

console.log("TRAINING FINISHED.")
```

## <a name="detect-anomalies"></a>Detetar anomalias

Utilize as `detectAnomaly` funções e `getDectectionResult` as funções para determinar se existem anomalias dentro da sua fonte de dados.

```javascript
console.log("Start detecting...")
const detect_request = {
    source: data_source,
    startTime: new Date(2021,0,2,12,0,0),
    endTime: new Date(2021,0,3,0,0,0)
};
const result_header = await client.detectAnomaly(model_id, detect_request)
const result_id = result_header.location?.split("/").pop() ?? ""
let result = await client.getDetectionResult(result_id)
let result_status = result.summary.status

while (result_status != 'READY'){
    await sleep(2000).then(() => {});
    result = await client.getDetectionResult(result_id)
    result_status = result.summary.status
}
```

## <a name="export-model"></a>Modelo de exportação

Para exportar o seu modelo treinado utilize a `exportModel` função.

```javascript
const export_result = await client.exportModel(model_id)
const model_path = "model.zip"
const destination = fs.createWriteStream(model_path)
export_result.readableStreamBody?.pipe(destination)
console.log("New model has been exported to "+model_path+".")
```

## <a name="delete-model"></a>Eliminar modelo

Para eliminar um modelo existente que esteja disponível para o recurso atual, utilize a `deleteMultivariateModel` função.

```javascript
client.deleteMultivariateModel(model_id)
console.log("New model has been deleted.")
```

## <a name="run-the-application"></a>Executar a aplicação

Antes de executar a aplicação, pode ser útil verificar o seu código com o [código de amostrato](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/anomalydetector/ai-anomaly-detector/samples/v3/javascript/sample_multivariate_detection.js)

Execute a aplicação com o `node` comando no seu ficheiro quickstart.

```console
node index.js
```

## <a name="next-steps"></a>Passos seguintes

* [As melhores práticas do Detetor de Anomalias](../../concepts/best-practices-multivariate.md)
