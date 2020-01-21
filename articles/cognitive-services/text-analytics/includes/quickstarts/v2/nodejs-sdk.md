---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 10/28/2019
ms.author: aahi
ms.openlocfilehash: 7cb3dce31592771deb5c57b9d73670465f7424fe
ms.sourcegitcommit: d9ec6e731e7508d02850c9e05d98d26c4b6f13e6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/20/2020
ms.locfileid: "76281370"
---
<a name="HOLTop"></a>

[Documentação de referência](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics) |  | [(NPM)](https://www.npmjs.com/package/azure-cognitiveservices-textanalytics) | [amostras](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/) do [código-fonte de biblioteca](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/cognitiveServicesTextAnalytics)

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure- [crie uma gratuitamente](https://azure.microsoft.com/free/)
* A versão atual do [node. js](https://nodejs.org/).

## <a name="setting-up"></a>Configurando

### <a name="create-a-text-analytics-azure-resource"></a>Criar um Análise de Texto recurso do Azure

[!INCLUDE [text-analytics-resource-creation](../resource-creation.md)]

### <a name="create-a-new-nodejs-application"></a>Criar uma nova aplicação Node.js

Em uma janela de console (como cmd, PowerShell ou bash), crie um novo diretório para seu aplicativo e navegue até ele. 

```console
mkdir myapp && cd myapp
```

Execute o comando `npm init` para criar um aplicativo de nó com um arquivo de `package.json`. 

```console
npm init
```

Crie um arquivo chamado `index.js` e adicione as seguintes bibliotecas:

[!code-javascript[Const statements](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=constStatements)]

Crie variáveis para a chave de assinatura e o ponto de extremidade do Azure do recurso.

[!INCLUDE [text-analytics-find-resource-information](../../find-azure-resource-info-v2.md)]

```javascript
const subscription_key = '<paste-your-text-analytics-key-here>';
const endpoint = '<paste-your-text-analytics-endpoint-here>';
```

### <a name="install-the-client-library"></a>Instalar a biblioteca de cliente

Instale o `@azure/ms-rest-js` e `@azure/cognitiveservices-textanalytics` pacotes NPM:

```console
npm install @azure/cognitiveservices-textanalytics @azure/ms-rest-js
```

O arquivo de `package.json` do seu aplicativo será atualizado com as dependências.

## <a name="object-model"></a>Modelo de objeto

O Análise de Texto Client é um objeto [TextAnalyticsClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient) que se autentica no Azure usando sua chave. O cliente fornece vários métodos para analisar o texto, como uma única cadeia de caracteres ou um lote.

O texto é enviado para a API como uma lista de `documents`, que são `dictionary` objetos que contêm uma combinação de atributos `id`, `text`e `language` dependendo do método usado. O atributo `text` armazena o texto a ser analisado no `language`de origem e o `id` pode ser qualquer valor. 

O objeto de resposta é uma lista que contém as informações de análise de cada documento. 

## <a name="code-examples"></a>Exemplos de código

* [Autenticar o cliente](#authenticate-the-client)
* [Análise de sentimentos](#sentiment-analysis)
* [Detecção de idioma](#language-detection)
* [Reconhecimento de entidade](#entity-recognition)
* [Extração de frases-chave](#key-phrase-extraction)


## <a name="authenticate-the-client"></a>Autenticar o cliente

Crie um novo objeto [TextAnalyticsClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient) com `credentials` e `endpoint` como um parâmetro.

[!code-javascript[Authentication and client creation](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=authentication)]


## <a name="sentiment-analysis"></a>Análise de sentimentos

Crie uma lista de objetos Dictionary, contendo os documentos que você deseja analisar. Chame o método de [sentimentos ()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#sentiment-models-textanalyticsclientsentimentoptionalparams-) do cliente e obtenha o [SentimentBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/sentimentbatchresult)retornado. Itere na lista de resultados e imprima a ID e a pontuação de sentimentos de cada documento. Uma pontuação mais próxima de 0 indica uma observação negativa, enquanto uma pontuação mais próxima de 1 indica uma observação positiva.

[!code-javascript[Sentiment analysis](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=sentimentAnalysis)]

Execute o código com `node index.js` na janela do console.

### <a name="output"></a>Saída

```console
[ { id: '1', score: 0.87 } ]
[ { id: '2', score: 0.11 } ]
[ { id: '3', score: 0.44 } ]
[ { id: '4', score: 1.00 } ]
```

## <a name="language-detection"></a>Deteção de idioma

Crie uma lista de objetos de dicionário contendo seus documentos. Chame o método [detectLanguage ()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#detectlanguage-models-textanalyticsclientdetectlanguageoptionalparams-) do cliente e obtenha o [LanguageBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/languagebatchresult)retornado. Em seguida, itere os resultados e imprima a ID e o idioma de cada documento.

[!code-javascript[Language detection](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=languageDetection)]

Execute o código com `node index.js` na janela do console.

### <a name="output"></a>Saída

```console
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

## <a name="entity-recognition"></a>Reconhecimento de entidades

Crie uma lista de objetos, contendo seus documentos. Chame o método [Entities ()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#entities-models-textanalyticscliententitiesoptionalparams-) do cliente e obtenha o objeto [EntitiesBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/entitiesbatchresult) . Itere na lista de resultados e imprima a ID de cada documento. Para cada entidade detectada, imprima seu nome da Wikipédia, o tipo e os subtipos (se existir), bem como os locais no texto original.

[!code-javascript[Entity recognition](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=entityRecognition)]

Execute o código com `node index.js` na janela do console.

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

Crie uma lista de objetos, contendo seus documentos. Chame o método [prefrases ()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#keyphrases-models-textanalyticsclientkeyphrasesoptionalparams-) do cliente e obtenha o objeto [KeyPhraseBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/keyphrasebatchresult) retornado. Itere os resultados e imprima a ID de cada documento e as frases-chave detectadas.

[!code-javascript[Key phrase extraction](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=keyPhraseExtraction)]

Execute o código com `node index.js` na janela do console.

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

Execute o aplicativo com o comando `node` em seu arquivo de início rápido.

```console
node index.js
```
