---
title: 'Quickstart: Text Analytics v3 biblioteca de clientes para Node.js Microsoft Docs'
description: Começa com a biblioteca de clientes V3 Text Analytics para Node.js.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 10/07/2020
ms.author: aahi
ms.reviewer: sumeh, assafi
ms.custom: devx-track-js
ms.openlocfilehash: a4428d200cf99457f9370d7516dccf2e344e3319
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94816639"
---
<a name="HOLTop"></a>

# <a name="version-31-preview"></a>[Visualização da versão 3.1](#tab/version-3-1)

[v3 Documentação](/javascript/api/overview/azure/ai-text-analytics-readme)  |  de referência [v3 Código](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics)  |  fonte da biblioteca [v3 Pacote (NPM)](https://www.npmjs.com/package/@azure/ai-text-analytics)  |  [v3 Amostras](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics/samples)


# <a name="version-30"></a>[Versão 3.0](#tab/version-3)

[v3 Documentação](/javascript/api/overview/azure/ai-text-analytics-readme)  |  de referência [v3 Código](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics)  |  fonte da biblioteca [v3 Pacote (NPM)](https://www.npmjs.com/package/@azure/ai-text-analytics)  |  [v3 Amostras](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics/samples)


# <a name="version-21"></a>[Versão 2.1](#tab/version-2)

[v2 Documentação](/javascript/api/@azure/cognitiveservices-textanalytics)  |  de referência [v2 Código](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/cognitiveServicesTextAnalytics)  |  fonte da biblioteca [v2 Pacote (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-textanalytics)  |  [v2 Amostras](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/)

---

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services)
* A versão atual de [Node.js. ](https://nodejs.org/)
* Assim que tiver a subscrição do Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title=" Crie um recurso Text Analytics crie um recurso Text Analytics no portal "  target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> </a> Azure para obter a sua chave e ponto final. Depois de implementar, clique em **Ir para o recurso**.
    * Necessitará da chave e ponto final do recurso que criar para ligar a sua aplicação à API de Análise de Texto. Colará a chave e o ponto final no código abaixo mais tarde no arranque rápido.
    * Pode utilizar o nível de preços gratuitos `F0` para experimentar o serviço e fazer upgrade mais tarde para um nível pago para produção.

## <a name="setting-up"></a>Configuração

### <a name="create-a-new-nodejs-application"></a>Criar uma nova aplicação Node.js

Numa janela de consola (como cmd, PowerShell ou Bash), crie um novo diretório para a sua aplicação e navegue até ela. 

```console
mkdir myapp 

cd myapp
```

Executar o `npm init` comando para criar uma aplicação de nó com um `package.json` ficheiro. 

```console
npm init
```
### <a name="install-the-client-library"></a>Instalar a biblioteca do cliente

# <a name="version-31-preview"></a>[Visualização da versão 3.1](#tab/version-3-1)

Instale os `@azure/ai-text-analytics` pacotes NPM:

```console
npm install --save @azure/ai-text-analytics@5.1.0-beta.1
```

> [!TIP]
> Quer ver todo o ficheiro de código de arranque rápido de uma vez? Pode encontrá-lo [no GitHub,](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/TextAnalytics/text-analytics-v3-client-library.js)que contém os exemplos de código neste arranque rápido. 


# <a name="version-30"></a>[Versão 3.0](#tab/version-3)

Instale os `@azure/ai-text-analytics` pacotes NPM:

```console
npm install --save @azure/ai-text-analytics@5.0.0
```

> [!TIP]
> Quer ver todo o ficheiro de código de arranque rápido de uma vez? Pode encontrá-lo [no GitHub,](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/TextAnalytics/text-analytics-v3-client-library.js)que contém os exemplos de código neste arranque rápido. 

# <a name="version-21"></a>[Versão 2.1](#tab/version-2)

Instale os `@azure/cognitiveservices-textanalytics` pacotes NPM:

```console
npm install --save @azure/cognitiveservices-textanalytics @azure/ms-rest-js
```

> [!TIP]
> Quer ver todo o ficheiro de código de arranque rápido de uma vez? Pode encontrá-lo [no GitHub,](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/textAnalytics.js)que contém os exemplos de código neste arranque rápido. 

---

O ficheiro da sua aplicação `package.json` será atualizado com as dependências.
Crie um ficheiro com o nome `index.js` e adicione o seguinte:

# <a name="version-31-preview"></a>[Visualização da versão 3.1](#tab/version-3-1)

```javascript
"use strict";

const { TextAnalyticsClient, AzureKeyCredential } = require("@azure/ai-text-analytics");
```

# <a name="version-30"></a>[Versão 3.0](#tab/version-3)

```javascript
"use strict";

const { TextAnalyticsClient, AzureKeyCredential } = require("@azure/ai-text-analytics");
```

# <a name="version-21"></a>[Versão 2.1](#tab/version-2)

```javascript
"use strict";

const os = require("os");
const CognitiveServicesCredentials = require("@azure/ms-rest-js");
const TextAnalyticsAPIClient = require("@azure/cognitiveservices-textanalytics");
```
---

Crie variáveis para o ponto final e chave Azure do seu recurso.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```javascript
const key = '<paste-your-text-analytics-key-here>';
const endpoint = '<paste-your-text-analytics-endpoint-here>';
```

## <a name="object-model"></a>Modelo de objeto

O cliente Text Analytics é um `TextAnalyticsClient` objeto que autentica a Azure usando a sua chave. O cliente fornece vários métodos para analisar texto, como uma única corda, ou um lote.

O texto é enviado para a API como uma lista de `documents` , que são `dictionary` objetos que contêm uma combinação de `id` , e `text` `language` atributos dependendo do método utilizado. O `text` atributo armazena o texto a ser analisado na origem , e `language` pode ser qualquer `id` valor. 

O objeto de resposta é uma lista que contém as informações de análise de cada documento. 

## <a name="code-examples"></a>Exemplos de código

* [Autenticação do Cliente](#client-authentication)
* [Análise de Sentimentos](#sentiment-analysis) 
* [Mineração de opinião](#opinion-mining)
* [Deteção de idioma](#language-detection)
* [Reconhecimento de entidade nomeada](#named-entity-recognition-ner)
* [Ligação de entidades](#entity-linking)
* Informação Identificativa
* [Extração de expressões-chave](#key-phrase-extraction)

## <a name="client-authentication"></a>Autenticação de Cliente

# <a name="version-31-preview"></a>[Visualização da versão 3.1](#tab/version-3-1)

Crie um `TextAnalyticsClient` novo objeto com a sua chave e ponto final como parâmetros.

```javascript
const textAnalyticsClient = new TextAnalyticsClient(endpoint,  new AzureKeyCredential(key));
```

# <a name="version-30"></a>[Versão 3.0](#tab/version-3)

Crie um `TextAnalyticsClient` novo objeto com a sua chave e ponto final como parâmetros.

```javascript
const textAnalyticsClient = new TextAnalyticsClient(endpoint,  new AzureKeyCredential(key));
```

# <a name="version-21"></a>[Versão 2.1](#tab/version-2)

Crie um novo objeto [TextAnalyticsClient](/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient) com `credentials` e como `endpoint` parâmetro.

[!code-javascript[Authentication and client creation](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=authentication)]

---

## <a name="sentiment-analysis"></a>Análise de sentimentos

# <a name="version-31-preview"></a>[Visualização da versão 3.1](#tab/version-3-1)

Crie uma série de cordas que contenham o documento que pretende analisar. Ligue para o método do cliente `analyzeSentiment()` e pegue o objeto `SentimentBatchResult` devolvido. Iterate através da lista de resultados, e imprima o ID de cada documento, sentimento de nível de documento com pontuações de confiança. Para cada documento, o resultado contém o sentimento do nível da frase juntamente com compensações, comprimento e pontuações de confiança.

```javascript
async function sentimentAnalysis(client){

    const sentimentInput = [
        "I had the best day of my life. I wish you were there with me."
    ];
    const sentimentResult = await client.analyzeSentiment(sentimentInput);

    sentimentResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Sentiment: ${document.sentiment}`);
        console.log(`\tDocument Scores:`);
        console.log(`\t\tPositive: ${document.confidenceScores.positive.toFixed(2)} \tNegative: ${document.confidenceScores.negative.toFixed(2)} \tNeutral: ${document.confidenceScores.neutral.toFixed(2)}`);
        console.log(`\tSentences Sentiment(${document.sentences.length}):`);
        document.sentences.forEach(sentence => {
            console.log(`\t\tSentence sentiment: ${sentence.sentiment}`)
            console.log(`\t\tSentences Scores:`);
            console.log(`\t\tPositive: ${sentence.confidenceScores.positive.toFixed(2)} \tNegative: ${sentence.confidenceScores.negative.toFixed(2)} \tNeutral: ${sentence.confidenceScores.neutral.toFixed(2)}`);
        });
    });
}
sentimentAnalysis(textAnalyticsClient)
```

Execute o seu código `node index.js` na janela da consola.

### <a name="output"></a>Saída

```console
ID: 0
        Document Sentiment: positive
        Document Scores:
                Positive: 1.00  Negative: 0.00  Neutral: 0.00
        Sentences Sentiment(2):
                Sentence sentiment: positive
                Sentences Scores:
                Positive: 1.00  Negative: 0.00  Neutral: 0.00
                Sentence sentiment: neutral
                Sentences Scores:
                Positive: 0.21  Negative: 0.02  Neutral: 0.77
```

### <a name="opinion-mining"></a>Mineração de opinião

Para fazer análise de sentimento com a mineração de opinião, crie uma variedade de cordas contendo o documento que pretende analisar. Ligue para o método do cliente `analyzeSentiment()` adicionando a bandeira de opção `includeOpinionMining: true` e obtenha o objeto `SentimentBatchResult` devolvido. Iterate através da lista de resultados, e imprima o ID de cada documento, sentimento de nível de documento com pontuações de confiança. Para cada documento, o resultado contém não só o sentimento do nível da frase como acima, mas também o aspeto e o sentimento de nível de opinião.

```javascript
async function sentimentAnalysisWithOpinionMining(client){

    const sentimentInput = [
        {
            text: "The food and service were unacceptable, but the concierge were nice",
            id: "0",
            language: "en"
        }
    ];
    const sentimentResult = await client.analyzeSentiment(sentimentInput, { includeOpinionMining: true });

    sentimentResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Sentiment: ${document.sentiment}`);
        console.log(`\tDocument Scores:`);
        console.log(`\t\tPositive: ${document.confidenceScores.positive.toFixed(2)} \tNegative: ${document.confidenceScores.negative.toFixed(2)} \tNeutral: ${document.confidenceScores.neutral.toFixed(2)}`);
        console.log(`\tSentences Sentiment(${document.sentences.length}):`);
        document.sentences.forEach(sentence => {
            console.log(`\t\tSentence sentiment: ${sentence.sentiment}`)
            console.log(`\t\tSentences Scores:`);
            console.log(`\t\tPositive: ${sentence.confidenceScores.positive.toFixed(2)} \tNegative: ${sentence.confidenceScores.negative.toFixed(2)} \tNeutral: ${sentence.confidenceScores.neutral.toFixed(2)}`);
            console.log("    Mined opinions");
            for (const { aspect, opinions } of sentence.minedOpinions) {
                console.log(`      - Aspect text: ${aspect.text}`);
                console.log(`        Aspect sentiment: ${aspect.sentiment}`);
                console.log("        Aspect confidence scores:", aspect.confidenceScores);
                console.log("        Aspect opinions");
                for (const { text, sentiment } of opinions) {
                console.log(`        - Text: ${text}`);
                console.log(`          Sentiment: ${sentiment}`);
                }
            }
        });
    });
}
sentimentAnalysisWithOpinionMining(textAnalyticsClient)
```

Execute o seu código `node index.js` na janela da consola.

### <a name="output"></a>Saída

```console
ID: 0
        // Document Sentiment: positive
        // Document Scores:
                // Positive: 0.84  Negative: 0.16  Neutral: 0.00
        // Sentences Sentiment(1):
                // Sentence sentiment: positive
                // Sentences Scores:
                // Positive: 0.84  Negative: 0.16  Neutral: 0.00
    // Mined opinions
      // - Aspect text: food
        // Aspect sentiment: negative
        // Aspect confidence scores: { positive: 0.01, negative: 0.99 }
        // Aspect opinions
        // - Text: unacceptable
          // Sentiment: negative
      // - Aspect text: service
        // Aspect sentiment: negative
        // Aspect confidence scores: { positive: 0.01, negative: 0.99 }
        // Aspect opinions
        // - Text: unacceptable
          // Sentiment: negative
      // - Aspect text: concierge
        // Aspect sentiment: positive
        // Aspect confidence scores: { positive: 1, negative: 0 }
        // Aspect opinions
        // - Text: nice
          // Sentiment: positive
```

# <a name="version-30"></a>[Versão 3.0](#tab/version-3)

Crie uma série de cordas que contenham o documento que pretende analisar. Ligue para o método do cliente `analyzeSentiment()` e pegue o objeto `SentimentBatchResult` devolvido. Iterate através da lista de resultados, e imprima o ID de cada documento, sentimento de nível de documento com pontuações de confiança. Para cada documento, o resultado contém o sentimento do nível da frase juntamente com compensações, comprimento e pontuações de confiança.

```javascript
async function sentimentAnalysis(client){

    const sentimentInput = [
        "I had the best day of my life. I wish you were there with me."
    ];
    const sentimentResult = await client.analyzeSentiment(sentimentInput);

    sentimentResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Sentiment: ${document.sentiment}`);
        console.log(`\tDocument Scores:`);
        console.log(`\t\tPositive: ${document.confidenceScores.positive.toFixed(2)} \tNegative: ${document.confidenceScores.negative.toFixed(2)} \tNeutral: ${document.confidenceScores.neutral.toFixed(2)}`);
        console.log(`\tSentences Sentiment(${document.sentences.length}):`);
        document.sentences.forEach(sentence => {
            console.log(`\t\tSentence sentiment: ${sentence.sentiment}`)
            console.log(`\t\tSentences Scores:`);
            console.log(`\t\tPositive: ${sentence.confidenceScores.positive.toFixed(2)} \tNegative: ${sentence.confidenceScores.negative.toFixed(2)} \tNeutral: ${sentence.confidenceScores.neutral.toFixed(2)}`);
        });
    });
}
sentimentAnalysis(textAnalyticsClient)
```

Execute o seu código `node index.js` na janela da consola.

### <a name="output"></a>Saída

```console
ID: 0
        Document Sentiment: positive
        Document Scores:
                Positive: 1.00  Negative: 0.00  Neutral: 0.00
        Sentences Sentiment(2):
                Sentence sentiment: positive
                Sentences Scores:
                Positive: 1.00  Negative: 0.00  Neutral: 0.00
                Sentence sentiment: neutral
                Sentences Scores:
                Positive: 0.21  Negative: 0.02  Neutral: 0.77
```

# <a name="version-21"></a>[Versão 2.1](#tab/version-2)

Crie uma lista de objetos dicionários que contenham os documentos que pretende analisar. Ligue para o método de sentimento do [cliente](/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#sentiment-models-textanalyticsclientsentimentoptionalparams-) e obtenha o [SentimentBatchResult devolvido](/javascript/api/@azure/cognitiveservices-textanalytics/sentimentbatchresult). Iterate através da lista de resultados, e imprima o ID de cada documento e a pontuação de sentimento. Uma pontuação mais próxima de 0 indica um sentimento negativo, enquanto uma pontuação mais próxima de 1 indica um sentimento positivo.

[!code-javascript[Sentiment analysis](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=sentimentAnalysis)]

Execute o seu código `node index.js` na janela da consola.

### <a name="output"></a>Saída

```console
[ { id: '1', score: 0.87 } ]
[ { id: '2', score: 0.11 } ]
[ { id: '3', score: 0.44 } ]
[ { id: '4', score: 1.00 } ]
```

---

## <a name="language-detection"></a>Deteção de idioma

# <a name="version-31-preview"></a>[Visualização da versão 3.1](#tab/version-3-1)

Crie uma série de cordas que contenham o documento que pretende analisar. Ligue para o método do cliente `detectLanguage()` e obtenha o de `DetectLanguageResultCollection` volta. Em seguida, iterar através dos resultados, e imprimir o ID de cada documento com a respetiva linguagem primária.

```javascript
async function languageDetection(client) {

    const languageInputArray = [
        "Ce document est rédigé en Français."
    ];
    const languageResult = await client.detectLanguage(languageInputArray);

    languageResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tPrimary Language ${document.primaryLanguage.name}`)
    });
}
languageDetection(textAnalyticsClient);
```

Execute o seu código `node index.js` na janela da consola.

### <a name="output"></a>Saída

```console
ID: 0
        Primary Language French
```

# <a name="version-30"></a>[Versão 3.0](#tab/version-3)

Crie uma série de cordas que contenham o documento que pretende analisar. Ligue para o método do cliente `detectLanguage()` e obtenha o de `DetectLanguageResultCollection` volta. Em seguida, iterar através dos resultados, e imprimir o ID de cada documento com a respetiva linguagem primária.

```javascript
async function languageDetection(client) {

    const languageInputArray = [
        "Ce document est rédigé en Français."
    ];
    const languageResult = await client.detectLanguage(languageInputArray);

    languageResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tPrimary Language ${document.primaryLanguage.name}`)
    });
}
languageDetection(textAnalyticsClient);
```

Execute o seu código `node index.js` na janela da consola.

### <a name="output"></a>Saída

```console
ID: 0
        Primary Language French
```

# <a name="version-21"></a>[Versão 2.1](#tab/version-2)

Crie uma lista de objetos dicionários que contenham os seus documentos. Ligue para o método [de deteção](/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#detectlanguage-models-textanalyticsclientdetectlanguageoptionalparams-) de Línguas do cliente e obtenha o [Estudo de Línguas Devolvido](/javascript/api/@azure/cognitiveservices-textanalytics/languagebatchresult). Em seguida, iterar através dos resultados, e imprimir o ID de cada documento, e linguagem.

[!code-javascript[Language detection](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=languageDetection)]

Execute o seu código `node index.js` na janela da consola.

### <a name="output"></a>Saída

```console
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

---

## <a name="named-entity-recognition-ner"></a>Reconhecimento de Entidades Nomeadas (NER)

# <a name="version-31-preview"></a>[Visualização da versão 3.1](#tab/version-3-1)

> [!NOTE]
> Na `3.1` versão:
> * A ligação da entidade é um pedido separado do que o NER.

Crie uma série de cordas que contenham o documento que pretende analisar. Ligue para o método do cliente `recognizeEntities()` e pegue o `RecognizeEntitiesResult` objeto. Iterar através da lista de resultados, e imprimir o nome da entidade, tipo, subtipo, offset, comprimento e pontuação.

```javascript
async function entityRecognition(client){

    const entityInputs = [
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800",
        "La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle."
    ];
    const entityResults = await client.recognizeEntities(entityInputs);

    entityResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.text} \tCategory: ${entity.category} \tSubcategory: ${entity.subCategory ? entity.subCategory : "N/A"}`);
            console.log(`\tScore: ${entity.confidenceScore}`);
        });
    });
}
entityRecognition(textAnalyticsClient);
```

Execute o seu código `node index.js` na janela da consola.

### <a name="output"></a>Saída

```console
Document ID: 0
        Name: Microsoft         Category: Organization  Subcategory: N/A
        Score: 0.29
        Name: Bill Gates        Category: Person        Subcategory: N/A
        Score: 0.78
        Name: Paul Allen        Category: Person        Subcategory: N/A
        Score: 0.82
        Name: April 4, 1975     Category: DateTime      Subcategory: Date
        Score: 0.8
        Name: 8800      Category: Quantity      Subcategory: Number
        Score: 0.8
Document ID: 1
        Name: 21        Category: Quantity      Subcategory: Number
        Score: 0.8
        Name: Seattle   Category: Location      Subcategory: GPE
        Score: 0.25
```

### <a name="entity-linking"></a>Ligar à Entidade

Crie uma série de cordas que contenham o documento que pretende analisar. Ligue para o método do cliente `recognizeLinkedEntities()` e pegue o `RecognizeLinkedEntitiesResult` objeto. Iterar através da lista de resultados, e imprimir o nome da entidade, ID, fonte de dados, url e fósforos. Todos os objetos em `matches` matriz conterão offset, comprimento e pontuação para esta partida.

```javascript
async function linkedEntityRecognition(client){

    const linkedEntityInput = [
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800. During his career at Microsoft, Gates held the positions of chairman, chief executive officer, president and chief software architect, while also being the largest individual shareholder until May 2014."
    ];
    const entityResults = await client.recognizeLinkedEntities(linkedEntityInput);

    entityResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.name} \tID: ${entity.dataSourceEntityId} \tURL: ${entity.url} \tData Source: ${entity.dataSource}`);
            console.log(`\tMatches:`)
            entity.matches.forEach(match => {
                console.log(`\t\tText: ${match.text} \tScore: ${match.confidenceScore.toFixed(2)}`);
        })
        });
    });
}
linkedEntityRecognition(textAnalyticsClient);
```

Execute o seu código `node index.js` na janela da consola.

### <a name="output"></a>Saída

```console
Document ID: 0
        Name: Altair 8800       ID: Altair 8800         URL: https://en.wikipedia.org/wiki/Altair_8800  Data Source: Wikipedia
        Matches:
                Text: Altair 8800       Score: 0.88
        Name: Bill Gates        ID: Bill Gates  URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates        Score: 0.63
                Text: Gates     Score: 0.63
        Name: Paul Allen        ID: Paul Allen  URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen        Score: 0.60
        Name: Microsoft         ID: Microsoft   URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft         Score: 0.55
                Text: Microsoft         Score: 0.55
        Name: April 4   ID: April 4     URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4   Score: 0.32
        Name: BASIC     ID: BASIC       URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC     Score: 0.33
```

### <a name="personally-identifying-information-pii-recognition"></a>Reconhecimento de Informação de Identificação Pessoal (PII)

Crie uma série de cordas que contenham o documento que pretende analisar. Ligue para o método do cliente `recognizePiiEntities()` e pegue o `RecognizePIIEntitiesResult` objeto. Iterar através da lista de resultados, e imprimir o nome, tipo e pontuação da entidade.

```javascript
async function piiRecognition(client) {

    const documents = [
        "The employee's phone number is (555) 555-5555."
    ];

    const results = await client.recognizePiiEntities(documents, "en");
    for (const result of results) {
        if (result.error === undefined) {
            console.log("Redacted Text: ", result.redactedText);
            console.log(" -- Recognized PII entities for input", result.id, "--");
            for (const entity of result.entities) {
                console.log(entity.text, ":", entity.category, "(Score:", entity.confidenceScore, ")");
            }
        } else {
            console.error("Encountered an error:", result.error);
        }
    }
}
piiRecognition(textAnalyticsClient)
```

Execute o seu código `node index.js` na janela da consola.

### <a name="output"></a>Saída

```console
Redacted Text:  The employee's phone number is **************.
 -- Recognized PII entities for input 0 --
(555) 555-5555 : Phone Number (Score: 0.8 )
```

# <a name="version-30"></a>[Versão 3.0](#tab/version-3)

> [!NOTE]
> Na `3.0` versão:
> * A ligação da entidade é um pedido separado do que o NER.

Crie uma série de cordas que contenham o documento que pretende analisar. Ligue para o método do cliente `recognizeEntities()` e pegue o `RecognizeEntitiesResult` objeto. Iterar através da lista de resultados, e imprimir o nome da entidade, tipo, subtipo, offset, comprimento e pontuação.

```javascript
async function entityRecognition(client){

    const entityInputs = [
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800",
        "La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle."
    ];
    const entityResults = await client.recognizeEntities(entityInputs);

    entityResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.text} \tCategory: ${entity.category} \tSubcategory: ${entity.subCategory ? entity.subCategory : "N/A"}`);
            console.log(`\tScore: ${entity.confidenceScore}`);
        });
    });
}
entityRecognition(textAnalyticsClient);
```

Execute o seu código `node index.js` na janela da consola.

### <a name="output"></a>Saída

```console
Document ID: 0
        Name: Microsoft         Category: Organization  Subcategory: N/A
        Score: 0.29
        Name: Bill Gates        Category: Person        Subcategory: N/A
        Score: 0.78
        Name: Paul Allen        Category: Person        Subcategory: N/A
        Score: 0.82
        Name: April 4, 1975     Category: DateTime      Subcategory: Date
        Score: 0.8
        Name: 8800      Category: Quantity      Subcategory: Number
        Score: 0.8
Document ID: 1
        Name: 21        Category: Quantity      Subcategory: Number
        Score: 0.8
        Name: Seattle   Category: Location      Subcategory: GPE
        Score: 0.25
```

### <a name="entity-linking"></a>Ligar à Entidade

Crie uma série de cordas que contenham o documento que pretende analisar. Ligue para o método do cliente `recognizeLinkedEntities()` e pegue o `RecognizeLinkedEntitiesResult` objeto. Iterar através da lista de resultados, e imprimir o nome da entidade, ID, fonte de dados, url e fósforos. Todos os objetos em `matches` matriz conterão offset, comprimento e pontuação para esta partida.

```javascript
async function linkedEntityRecognition(client){

    const linkedEntityInput = [
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800. During his career at Microsoft, Gates held the positions of chairman, chief executive officer, president and chief software architect, while also being the largest individual shareholder until May 2014."
    ];
    const entityResults = await client.recognizeLinkedEntities(linkedEntityInput);

    entityResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.name} \tID: ${entity.dataSourceEntityId} \tURL: ${entity.url} \tData Source: ${entity.dataSource}`);
            console.log(`\tMatches:`)
            entity.matches.forEach(match => {
                console.log(`\t\tText: ${match.text} \tScore: ${match.confidenceScore.toFixed(2)}`);
        })
        });
    });
}
linkedEntityRecognition(textAnalyticsClient);
```

Execute o seu código `node index.js` na janela da consola.

### <a name="output"></a>Saída

```console
Document ID: 0
        Name: Altair 8800       ID: Altair 8800         URL: https://en.wikipedia.org/wiki/Altair_8800  Data Source: Wikipedia
        Matches:
                Text: Altair 8800       Score: 0.88
        Name: Bill Gates        ID: Bill Gates  URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates        Score: 0.63
                Text: Gates     Score: 0.63
        Name: Paul Allen        ID: Paul Allen  URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen        Score: 0.60
        Name: Microsoft         ID: Microsoft   URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft         Score: 0.55
                Text: Microsoft         Score: 0.55
        Name: April 4   ID: April 4     URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4   Score: 0.32
        Name: BASIC     ID: BASIC       URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC     Score: 0.33
```

# <a name="version-21"></a>[Versão 2.1](#tab/version-2)

> [!NOTE]
> Na versão 2.1, a ligação da entidade está incluída na resposta NER.

Crie uma lista de objetos que contenham os seus documentos. Ligue para as [entidades](/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#entities-models-textanalyticscliententitiesoptionalparams-) do cliente e obtenha o objeto [EntitiesBatchResult.](/javascript/api/@azure/cognitiveservices-textanalytics/entitiesbatchresult) Iterar através da lista de resultados, e imprimir o ID de cada documento. Para cada entidade detetada, imprima o seu nome na Wikipédia, o tipo e subtipos (se existir) bem como as localizações no texto original.

[!code-javascript[Entity recognition](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=entityRecognition)]

Execute o seu código `node index.js` na janela da consola.

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

---

## <a name="key-phrase-extraction"></a>Extração de expressões-chave

# <a name="version-31-preview"></a>[Visualização da versão 3.1](#tab/version-3-1)

Crie uma série de cordas que contenham o documento que pretende analisar. Ligue para o método do cliente `extractKeyPhrases()` e pegue o objeto `ExtractKeyPhrasesResult` devolvido. Iterar através dos resultados e imprimir o ID de cada documento, e quaisquer frases-chave detetadas.

```javascript
async function keyPhraseExtraction(client){

    const keyPhrasesInput = [
        "My cat might need to see a veterinarian.",
    ];
    const keyPhraseResult = await client.extractKeyPhrases(keyPhrasesInput);
    
    keyPhraseResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Key Phrases: ${document.keyPhrases}`);
    });
}
keyPhraseExtraction(textAnalyticsClient);
```

Execute o seu código `node index.js` na janela da consola.

### <a name="output"></a>Saída

```console
ID: 0
        Document Key Phrases: cat,veterinarian
```

# <a name="version-30"></a>[Versão 3.0](#tab/version-3)

Crie uma série de cordas que contenham o documento que pretende analisar. Ligue para o método do cliente `extractKeyPhrases()` e pegue o objeto `ExtractKeyPhrasesResult` devolvido. Iterar através dos resultados e imprimir o ID de cada documento, e quaisquer frases-chave detetadas.

```javascript
async function keyPhraseExtraction(client){

    const keyPhrasesInput = [
        "My cat might need to see a veterinarian.",
    ];
    const keyPhraseResult = await client.extractKeyPhrases(keyPhrasesInput);
    
    keyPhraseResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Key Phrases: ${document.keyPhrases}`);
    });
}
keyPhraseExtraction(textAnalyticsClient);
```

Execute o seu código `node index.js` na janela da consola.

### <a name="output"></a>Saída

```console
ID: 0
        Document Key Phrases: cat,veterinarian
```

# <a name="version-21"></a>[Versão 2.1](#tab/version-2)

Crie uma lista de objetos que contenham os seus documentos. Ligue para o método [keyPhrases do](/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#keyphrases-models-textanalyticsclientkeyphrasesoptionalparams-) cliente e obtenha o objeto     [KeyPhraseBatchResult](/javascript/api/@azure/cognitiveservices-textanalytics/keyphrasebatchresult) devolvido. Iterar através dos resultados e imprimir o ID de cada documento, e quaisquer frases-chave detetadas.

[!code-javascript[Key phrase extraction](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=keyPhraseExtraction)]

Execute o seu código `node index.js` na janela da consola.

### <a name="output"></a>Saída

```console
[
    { id: '1', keyPhrases: [ '幸せ' ] }
    { id: '2', keyPhrases: [ 'Stuttgart', "hotel", "Fahrt", "Fu" ] }
    { id: '3', keyPhrases: [ 'cat', 'veterinarian' ] }
    { id: '3', keyPhrases: [ 'fútbol' ] }
]
```

---

## <a name="run-the-application"></a>Executar a aplicação

Execute a aplicação com o `node` comando no seu ficheiro quickstart.

```console
node index.js
```