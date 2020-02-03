---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 01/22/2020
ms.author: aahi
ms.openlocfilehash: d5197cfe749ae27b9f8807ba62825c8a73371c85
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76748911"
---
<a name="HOLTop"></a>

[Documentação de referência](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics) | [biblioteca código](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/cognitiveServicesTextAnalytics) de origem | [Pacote (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-textanalytics) | [Amostras](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/)

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura Azure - [Criar uma gratuitamente](https://azure.microsoft.com/free/)
* A versão atual do [Node.js.](https://nodejs.org/)

## <a name="setting-up"></a>Configurando

### <a name="create-a-text-analytics-azure-resource"></a>Criar um Análise de Texto recurso do Azure

[!INCLUDE [text-analytics-resource-creation](../resource-creation.md)]

### <a name="create-a-new-nodejs-application"></a>Criar uma nova aplicação Node.js

Em uma janela de console (como cmd, PowerShell ou bash), crie um novo diretório para seu aplicativo e navegue até ele.

```console
mkdir myapp && cd myapp
```

Execute o comando `npm init` para criar uma aplicação de nó com um ficheiro `package.json`.

```console
npm init
```

Crie um ficheiro com o nome `index.js` e adicione as seguintes bibliotecas:

[!code-javascript[Const statements](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=constStatements)]

Crie variáveis para a chave de assinatura e o ponto de extremidade do Azure do recurso.

[!INCLUDE [text-analytics-find-resource-information](../../find-azure-resource-info-v2.md)]

```javascript
const subscription_key = '<paste-your-text-analytics-key-here>';
const endpoint = '<paste-your-text-analytics-endpoint-here>';
```

### <a name="install-the-client-library"></a>Instalar a biblioteca de cliente

Instale os pacotes `@azure/ms-rest-js` e `@azure/cognitiveservices-textanalytics` NPM:

```console
npm install @azure/cognitiveservices-textanalytics @azure/ms-rest-js
```

O ficheiro `package.json` da sua aplicação será atualizado com as dependências.

## <a name="object-model"></a>Modelo de objeto

O cliente Text Analytics é um objeto [TextAnalyticsClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient) que autentica o Azure usando a sua chave. O cliente fornece vários métodos para analisar o texto, como uma única cadeia de caracteres ou um lote.

O texto é enviado para a API como uma lista de `documents`, que são `dictionary` objetos que contêm uma combinação de `id`, `text`, e `language` atributos dependendo do método utilizado. O atributo `text` armazena o texto a analisar na origem `language`, e o `id` pode ser qualquer valor.

O objeto de resposta é uma lista que contém as informações de análise de cada documento.

## <a name="code-examples"></a>Exemplos de código

* [Autenticar o cliente](#authenticate-the-client)
* [Análise de Sentimentos](#sentiment-analysis)
* [Deteção de linguagem](#language-detection)
* [Reconhecimento de entidades](#entity-recognition)
* [Extração de frase-chave](#key-phrase-extraction)


## <a name="authenticate-the-client"></a>Autenticar o cliente

Crie um novo objeto [TextAnalyticsClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient) com `credentials` e `endpoint` como parâmetro.

[!code-javascript[Authentication and client creation](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=authentication)]


## <a name="sentiment-analysis"></a>Análise de sentimentos

Crie uma lista de objetos Dictionary, contendo os documentos que você deseja analisar. Ligue para o método de [sentimento](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#sentiment-models-textanalyticsclientsentimentoptionalparams-) do cliente e obtenha o [SentimentBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/sentimentbatchresult)devolvido. Itere na lista de resultados e imprima a ID e a pontuação de sentimentos de cada documento. Uma pontuação mais próxima de 0 indica uma observação negativa, enquanto uma pontuação mais próxima de 1 indica uma observação positiva.

[!code-javascript[Sentiment analysis](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=sentimentAnalysis)]

Execute o seu código com `node index.js` na janela da consola.

### <a name="output"></a>Saída

```console
[ { id: '1', score: 0.87 } ]
[ { id: '2', score: 0.11 } ]
[ { id: '3', score: 0.44 } ]
[ { id: '4', score: 1.00 } ]
```

## <a name="language-detection"></a>Deteção de idioma

Crie uma lista de objetos de dicionário contendo seus documentos. Ligue para o método de deteção do [clienteLanguage()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#detectlanguage-models-textanalyticsclientdetectlanguageoptionalparams-) e obtenha o [idioma devolvidoBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/languagebatchresult). Em seguida, itere os resultados e imprima a ID e o idioma de cada documento.

[!code-javascript[Language detection](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=languageDetection)]

Execute o seu código com `node index.js` na janela da consola.

### <a name="output"></a>Saída

```console
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

## <a name="entity-recognition"></a>Reconhecimento de entidades

Crie uma lista de objetos, contendo seus documentos. Ligue para o método () das [entidades](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#entities-models-textanalyticscliententitiesoptionalparams-) do cliente e obtenha o objeto [EntidadesBatchResult.](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/entitiesbatchresult) Itere na lista de resultados e imprima a ID de cada documento. Para cada entidade detectada, imprima seu nome da Wikipédia, o tipo e os subtipos (se existir), bem como os locais no texto original.

[!code-javascript[Entity recognition](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=entityRecognition)]

Execute o seu código com `node index.js` na janela da consola.

### <a name="output"></a>Saída

```console
Document ID: 1
    Name: Microsoft,        Type: Organization,     Sub-Type: N/A
    Offset: 0, Length: 9,   Score: 1.0
    Name: Bill Gates,       Type: Person,   Sub-Type: N/A
    Offset: 25, Length: 10, Score: 0.999847412109375
    Name: Paul Allen,       Type: Person,   Sub-Type: N/A
    Offset: 40, Length: 10, Score: 0.9988409876823425
    Name: April 4,  Type: Other,    Sub-Type: N/A
    Offset: 54, Length: 7,  Score: 0.8
    Name: April 4, 1975,    Type: DateTime, Sub-Type: Date
    Offset: 54, Length: 13, Score: 0.8
    Name: BASIC,    Type: Other,    Sub-Type: N/A
    Offset: 89, Length: 5,  Score: 0.8
    Name: Altair 8800,      Type: Other,    Sub-Type: N/A
    Offset: 116, Length: 11,        Score: 0.8

Document ID: 2
    Name: Microsoft,        Type: Organization,     Sub-Type: N/A
    Offset: 21, Length: 9,  Score: 0.999755859375
    Name: Redmond (Washington),     Type: Location, Sub-Type: N/A
    Offset: 60, Length: 7,  Score: 0.9911284446716309
    Name: 21 kilómetros,    Type: Quantity, Sub-Type: Dimension
    Offset: 71, Length: 13, Score: 0.8
    Name: Seattle,  Type: Location, Sub-Type: N/A
    Offset: 88, Length: 7,  Score: 0.9998779296875
```

## <a name="key-phrase-extraction"></a>Extração de expressões chave

Crie uma lista de objetos, contendo seus documentos. Ligue para o método [chavePhrases()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#keyphrases-models-textanalyticsclientkeyphrasesoptionalparams-) do cliente e obtenha o objeto [KeyPhraseBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/keyphrasebatchresult) devolvido. Itere os resultados e imprima a ID de cada documento e as frases-chave detectadas.

[!code-javascript[Key phrase extraction](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=keyPhraseExtraction)]

Execute o seu código com `node index.js` na janela da consola.

### <a name="output"></a>Saída

```console
[
    { id: '1', keyPhrases: [ '幸せ' ] }
    { id: '2', keyPhrases: [ 'Stuttgart', "hotel", "Fahrt", "Fu" ] }
    { id: '3', keyPhrases: [ 'cat', 'veterinarian' ] }
    { id: '3', keyPhrases: [ 'fútbol' ] }
]
```

## <a name="run-the-application"></a>Executar a aplicação

Faça o pedido com o comando `node` no seu ficheiro de arranque rápido.

```console
node index.js
```
