---
title: 'Quickstart: Text Analytics v3 biblioteca de clientes para Node.js | Microsoft Docs'
description: Começa com a biblioteca de clientes V3 Text Analytics para Node.js.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 04/19/2021
ms.author: aahi
ms.reviewer: sumeh, assafi
ms.custom: devx-track-js
ms.openlocfilehash: 72ca331546d53f85ca82f33ec6a02558d91f1c1e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765108"
---
<a name="HOLTop"></a>

# <a name="version-31-preview"></a>[Visualização da versão 3.1](#tab/version-3-1)

[v3 Documentação](/javascript/api/overview/azure/ai-text-analytics-readme?preserve-view=true&view=azure-node-preview)  |  de referência [v3 Código](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics)  |  fonte da biblioteca [v3 Pacote (NPM)](https://www.npmjs.com/package/@azure/ai-text-analytics)  |  [v3 Amostras](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics/samples)


# <a name="version-30"></a>[Versão 3.0](#tab/version-3)

[v3 Documentação](/javascript/api/overview/azure/ai-text-analytics-readme)  |  de referência [v3 Código](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics)  |  fonte da biblioteca [v3 Pacote (NPM)](https://www.npmjs.com/package/@azure/ai-text-analytics)  |  [v3 Amostras](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics/samples)


---

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services)
* A versão atual de [Node.js. ](https://nodejs.org/)
* Assim que tiver a subscrição do Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title=" Crie um recurso Text Analytics crie um recurso Text Analytics no portal "  target="_blank"> </a> Azure para obter a sua chave e ponto final. Depois de implementar, clique em **Ir para o recurso**.
    * Necessitará da chave e ponto final do recurso que criar para ligar a sua aplicação à API de Análise de Texto. Colará a chave e o ponto final no código abaixo mais tarde no arranque rápido.
    * Pode utilizar o nível de preços gratuitos `F0` para experimentar o serviço e fazer upgrade mais tarde para um nível pago para produção.
* Para utilizar a função Analisar, necessitará de um recurso Text Analytics com o nível de preços padrão (S).

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
npm install --save @azure/ai-text-analytics@5.1.0-beta.5
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
* [Deteção linguística](#language-detection)
* [Reconhecimento de entidade nomeada](#named-entity-recognition-ner)
* [Ligação de entidades](#entity-linking)
* Informação Identificativa
* [Extração de frase-chave](#key-phrase-extraction)

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
  const results = await client.analyzeSentiment(sentimentInput, { includeOpinionMining: true });

  for (let i = 0; i < results.length; i++) {
    const result = results[i];
    console.log(`- Document ${result.id}`);
    if (!result.error) {
      console.log(`\tDocument text: ${sentimentInput[i].text}`);
      console.log(`\tOverall Sentiment: ${result.sentiment}`);
      console.log("\tSentiment confidence scores:", result.confidenceScores);
      console.log("\tSentences");
      for (const { sentiment, confidenceScores, opinions } of result.sentences) {
        console.log(`\t- Sentence sentiment: ${sentiment}`);
        console.log("\t  Confidence scores:", confidenceScores);
        console.log("\t  Mined opinions");
        for (const { target, assessments } of opinions) {
          console.log(`\t\t- Target text: ${target.text}`);
          console.log(`\t\t  Target sentiment: ${target.sentiment}`);
          console.log("\t\t  Target confidence scores:", target.confidenceScores);
          console.log("\t\t  Target assessments");
          for (const { text, sentiment } of assessments) {
            console.log(`\t\t\t- Text: ${text}`);
            console.log(`\t\t\t  Sentiment: ${sentiment}`);
          }
        }
      }
    } else {
      console.error(`\tError: ${result.error}`);
    }
  }
}
sentimentAnalysisWithOpinionMining(textAnalyticsClient)
```

Execute o seu código `node index.js` na janela da consola.

### <a name="output"></a>Saída

```console
- Document 0
        Document text: The food and service were unacceptable, but the concierge were nice
        Overall Sentiment: positive
        Sentiment confidence scores: { positive: 0.84, neutral: 0, negative: 0.16 }
        Sentences
        - Sentence sentiment: positive
          Confidence scores: { positive: 0.84, neutral: 0, negative: 0.16 }
          Mined opinions
                - Target text: food
                  Target sentiment: negative
                  Target confidence scores: { positive: 0.01, negative: 0.99 }
                  Target assessments
                        - Text: unacceptable
                          Sentiment: negative
                - Target text: service
                  Target sentiment: negative
                  Target confidence scores: { positive: 0.01, negative: 0.99 }
                  Target assessments
                        - Text: unacceptable
                          Sentiment: negative
                - Target text: concierge
                  Target sentiment: positive
                  Target confidence scores: { positive: 1, negative: 0 }
                  Target assessments
                        - Text: nice
                          Sentiment: positive
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


---

## <a name="use-the-api-asynchronously-with-the-analyze-operation"></a>Utilize a API assíncronea com a operação Análise

# <a name="version-31-preview"></a>[Visualização da versão 3.1](#tab/version-3-1)

[!INCLUDE [Analyze Batch Action pricing](../analyze-operation-pricing-caution.md)]

Criar uma nova função chamada `analyze_example()` , que chama a `beginAnalyze()` função. O resultado será uma operação de longa duração que será sondada para obter resultados.

```javascript
async function analyze_example(client) {
  const documents = [
    "Microsoft was founded by Bill Gates and Paul Allen.",
  ];

  const actions = {
    recognizeEntitiesActions: [{ modelVersion: "latest" }],
  };
  const poller = await client.beginAnalyzeBatchActions(documents, actions, "en");

  console.log(
    `The analyze batch actions operation was created on ${poller.getOperationState().createdOn}`
  );
  console.log(
    `The analyze batch actions operation results will expire on ${
      poller.getOperationState().expiresOn
    }`
  );
  const resultPages = await poller.pollUntilDone();
  for await (const page of resultPages) {
    const entitiesAction = page.recognizeEntitiesResults[0];
    if (!entitiesAction.error) {
      for (const doc of entitiesAction.results) {
        console.log(`- Document ${doc.id}`);
        if (!doc.error) {
          console.log("\tEntities:");
          for (const entity of doc.entities) {
            console.log(`\t- Entity ${entity.text} of type ${entity.category}`);
          }
        } else {
          console.error("\tError:", doc.error);
        }
      }
    }
  }
}

analyze_example(textAnalyticsClient);
```

### <a name="output"></a>Saída

```console
The analyze batch actions operation was created on Fri Mar 12 2021 09:53:49 GMT-0800 (Pacific Standard Time)
The analyze batch actions operation results will expire on Sat Mar 13 2021 09:53:49 GMT-0800 (Pacific Standard Time)
- Document 0
        Entities:
        - Entity Microsoft of type Organization
        - Entity Bill Gates of type Person
        - Entity Paul Allen of type Person
```

Também pode utilizar a operação De analisar para detetar PII, reconhecer entidades ligadas e extração de frases-chave. Consulte as amostras de Análise para [JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics/samples/v5/javascript) e [TypeScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics/samples/v5/typescript/src) no GitHub.

# <a name="version-30"></a>[Versão 3.0](#tab/version-3)

Esta funcionalidade não está disponível na versão 3.0.

---

## <a name="run-the-application"></a>Executar a aplicação

Execute a aplicação com o `node` comando no seu ficheiro quickstart.

```console
node index.js
```
