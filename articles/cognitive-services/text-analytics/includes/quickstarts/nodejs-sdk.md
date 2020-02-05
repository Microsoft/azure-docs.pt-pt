---
title: 'Início rápido: biblioteca de cliente do Análise de Texto V3 para node. js | Microsoft Docs'
description: Introdução à biblioteca de cliente do Análise de Texto V3 para node. js.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 01/13/2020
ms.author: aahi
ms.reviewer: sumeh, assafi
ms.openlocfilehash: c50326cf308d7d68f08fa5282f2baaa6b490d543
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76987922"
---
<a name="HOLTop"></a>

#### <a name="version-30-previewtabversion-3"></a>[Versão 3.0-pré-visualização](#tab/version-3)

[v3 Documentação de referência](https://aka.ms/azsdk-js-textanalytics-ref-docs) | [v3 Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics) | pacote [v3 (NPM)](https://www.npmjs.com/package/@azure/ai-text-analytics) | [v3 Amostras](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics/samples)


#### <a name="version-21tabversion-2"></a>[Versão 2.1](#tab/version-2)

[v2 Documentação de referência](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics) | [v2 Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/cognitiveServicesTextAnalytics) | pacote [v2 (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-textanalytics) | [amostras v2](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/)

---

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure- [crie uma gratuitamente](https://azure.microsoft.com/free/)
* A versão atual do [node. js](https://nodejs.org/).

[!INCLUDE [text-analytics-resource-creation](resource-creation.md)]

## <a name="setting-up"></a>Configurando

### <a name="create-a-new-nodejs-application"></a>Criar uma nova aplicação Node.js

> [!NOTE]
> Também pode executar esta versão da biblioteca de clientes Text Analytics [no seu navegador.](https://github.com/Azure/azure-sdk-for-js/blob/master/documentation/Bundling.md)

Em uma janela de console (como cmd, PowerShell ou bash), crie um novo diretório para seu aplicativo e navegue até ele. 

```console
mkdir myapp && cd myapp
```

Execute o comando `npm init` para criar um aplicativo de nó com um arquivo de `package.json`. 

```console
npm init
```
### <a name="install-the-client-library"></a>Instalar a biblioteca de cliente

Instale os pacotes do `@azure/cognitiveservices-textanalytics` NPM:

```console
npm install --save @azure/cognitiveservices-textanalytics
```

O arquivo de `package.json` do seu aplicativo será atualizado com as dependências.

Crie um arquivo chamado `index.js` e adicione as seguintes bibliotecas:

```javascript
"use strict";

const { TextAnalyticsClient, CognitiveServicesCredential } = require("@azure/cognitiveservices-textanalytics");
```

Crie variáveis para o ponto de extremidade e a chave do Azure do recurso.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```javascript
const key = '<paste-your-text-analytics-key-here>';
const endpoint = `<paste-your-text-analytics-endpoint-here>`;
```

## <a name="object-model"></a>Modelo de objeto

O cliente Análise de Texto é um objeto `TextAnalyticsClient` que se autentica no Azure usando sua chave. O cliente fornece vários métodos para analisar o texto, como uma única cadeia de caracteres ou um lote.

O texto é enviado para a API como uma lista de `documents`, que são `dictionary` objetos que contêm uma combinação de atributos `id`, `text`e `language` dependendo do método usado. O atributo `text` armazena o texto a ser analisado no `language`de origem e o `id` pode ser qualquer valor. 

O objeto de resposta é uma lista que contém as informações de análise de cada documento. 

## <a name="code-examples"></a>Exemplos de código

* [Autenticação do Cliente](#client-authentication)
* [Análise de sentimentos](#sentiment-analysis) 
* [Detecção de idioma](#language-detection)
* [Reconhecimento de Entidade Nomeada](#named-entity-recognition-ner)
* [Vinculação de entidade](#entity-linking)
* [Extração de frases-chave](#key-phrase-extraction)

## <a name="client-authentication"></a>Autenticação do Cliente

#### <a name="version-30-previewtabversion-3"></a>[Versão 3.0-pré-visualização](#tab/version-3)

Crie um novo objeto `TextAnalyticsClient` com sua chave e ponto de extremidade como parâmetros.

```javascript
const client = new TextAnalyticsClient(endpoint,  new CognitiveServicesCredential(key));
```

#### <a name="version-21tabversion-2"></a>[Versão 2.1](#tab/version-2)

Crie um novo objeto [TextAnalyticsClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient) com `credentials` e `endpoint` como um parâmetro.

[!code-javascript[Authentication and client creation](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=authentication)]

---

## <a name="sentiment-analysis"></a>Análise de sentimentos

#### <a name="version-30-previewtabversion-3"></a>[Versão 3.0-pré-visualização](#tab/version-3)

Crie uma matriz de cadeias de caracteres que contenha o documento que você deseja analisar. Chame o método de `analyzeSentiment()` do cliente e obtenha o objeto de `SentimentBatchResult` retornado. Itere na lista de resultados e imprima a ID de cada documento, a opinião de nível de documento com pontuações de confiança. Para cada documento, o resultado contém uma opinião de nível de frase junto com os deslocamentos, o comprimento e as pontuações de confiança.

```javascript
async function sentimentAnalysis(client){

    const sentimentInput = [
        "I had the best day of my life. I wish you were there with me."
    ]

    const sentimentResult = await client.analyzeSentiment(sentimentInput);
    result.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Sentiment: ${document.sentiment}`);
        console.log(`\tDocument Scores:`);
        console.log(`\t\tPositive: ${document.documentScores.positive.toFixed(2)} \tNegative: ${document.documentScores.negative.toFixed(2)} \tNeutral: ${document.documentScores.neutral.toFixed(2)}`);
        console.log(`\tSentences Sentiment(${document.sentences.length}):`);
        document.sentences.forEach(sentence => {
            console.log(`\t\tSentence sentiment: ${sentence.sentiment}`)
            console.log(`\t\tSentences Scores:`);
            console.log(`\t\tPositive: ${sentence.sentenceScores.positive.toFixed(2)} \tNegative: ${sentence.sentenceScores.negative.toFixed(2)} \tNeutral: ${sentence.sentenceScores.neutral.toFixed(2)}`);
            console.log(`\t\tLength: ${sentence.length}, Offset: ${sentence.offset}`);
        })
    });
}
sentimentAnalysis(textAnalyticsClient)
```

Execute o código com `node index.js` na janela do console.

### <a name="output"></a>Saída

```console
ID: 0
        Document Sentiment: positive
        Document Scores:
                Positive: 0.61  Negative: 0.01  Neutral: 0.39
        Sentences Sentiment(2):
                Sentence sentiment: positive
                Sentences Scores:
                Positive: 1.00  Negative: 0.00  Neutral: 0.00
                Length: 30, Offset: 0
                Sentence sentiment: neutral
                Sentences Scores:
                Positive: 0.21  Negative: 0.02  Neutral: 0.77
                Length: 30, Offset: 31
```

#### <a name="version-21tabversion-2"></a>[Versão 2.1](#tab/version-2)

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

---

## <a name="language-detection"></a>Deteção de idioma

#### <a name="version-30-previewtabversion-3"></a>[Versão 3.0-pré-visualização](#tab/version-3)

Crie uma matriz de cadeias de caracteres que contenha o documento que você deseja analisar. Chame o método de `detectLanguages()` do cliente e obtenha o `DetectLanguageResult`retornado. Em seguida, itere pelos resultados e imprima a ID de cada documento, com o respectivo idioma principal e o detectado.

```javascript
async function languageDetection(client) {

    const languageInputArray = [
        "Ce document est rédigé en Français."
    ]

    const languageResult = await client.detectLanguages(languageInputArray);

    result.forEach(document => {
        console.log(`ID: ${document.id}`);
        document.detectedLanguages.forEach(language =>
        console.log(`\tDetected Language ${language.name}`)
        );
        console.log(`\tPrimary Language ${document.primaryLanguage.name}`)
    });
}
languageDetection(textAnalyticsClient);
```

Execute o código com `node index.js` na janela do console.

### <a name="output"></a>Saída

```console
ID: 0
        Detected Language French
        Primary Language French
```

#### <a name="version-21tabversion-2"></a>[Versão 2.1](#tab/version-2)

Crie uma lista de objetos de dicionário contendo seus documentos. Chame o método [detectLanguage ()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#detectlanguage-models-textanalyticsclientdetectlanguageoptionalparams-) do cliente e obtenha o [LanguageBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/languagebatchresult)retornado. Em seguida, itere os resultados e imprima a ID e o idioma de cada documento.

[!code-javascript[Language detection](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=languageDetection)]

Execute o código com `node index.js` na janela do console.

### <a name="output"></a>Saída

```console
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

---

## <a name="named-entity-recognition-ner"></a>Reconhecimento de entidade nomeada (NER)

#### <a name="version-30-previewtabversion-3"></a>[Versão 3.0-pré-visualização](#tab/version-3)

> [!NOTE]
> Na versão `3.0-preview`:
> * O NER inclui métodos separados para detetar informações pessoais. 
> * A ligação de entidades é um pedido separado do NER.

Crie uma matriz de cadeias de caracteres que contenha o documento que você deseja analisar. Chame o método de `recognizeEntities()` do cliente e obtenha o objeto `RecognizeEntitiesResult`. Itere na lista de resultados e imprima o nome da entidade, o tipo, o subtipo, o deslocamento, o comprimento e a pontuação.

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
            console.log(`\tName: ${entity.text} \tType: ${entity.type} \tSub Type: ${entity.subtype != "" ? entity.subtype : "N/A"}`);
            console.log(`\tOffset: ${entity.offset}, Length: ${entity.length} \tScore: ${entity.score}`);
        });
    });
}
entityRecognition(textAnalyticsClient);
```

Execute o código com `node index.js` na janela do console.

### <a name="output"></a>Saída

```console
Document ID: 0
        Name: Microsoft         Type: Organization      Sub Type: N/A
        Offset: 0, Length: 9    Score: 1
        Name: Bill Gates        Type: Person    Sub Type: N/A
        Offset: 25, Length: 10  Score: 0.999786376953125
        Name: Paul Allen        Type: Person    Sub Type: N/A
        Offset: 40, Length: 10  Score: 0.9988105297088623
        Name: April 4, 1975     Type: DateTime  Sub Type: Date
        Offset: 54, Length: 13  Score: 0.8
        Name: Altair    Type: Organization      Sub Type: N/A
        Offset: 116, Length: 6  Score: 0.7996330857276917
        Name: 8800      Type: Quantity  Sub Type: Number
        Offset: 123, Length: 4  Score: 0.8
Document ID: 1
        Name: Microsoft         Type: Organization      Sub Type: N/A
        Offset: 21, Length: 9   Score: 0.9837456345558167
        Name: 21        Type: Quantity  Sub Type: Number
        Offset: 71, Length: 2   Score: 0.8
```

## <a name="using-ner-to-detect-personal-information"></a>Utilização do NER para detetar informações pessoais

Crie uma matriz de cadeias de caracteres que contenha o documento que você deseja analisar. Chame o método de `recognizePiiEntities()` do cliente e obtenha o objeto `EntitiesBatchResult`. Itere na lista de resultados e imprima o nome da entidade, o tipo, o subtipo, o deslocamento, o comprimento e a pontuação.


```javascript
async function entityPiiRecognition(client){

    const entityPiiInput = [
        "Insurance policy for SSN on file 123-12-1234 is here by approved."
    ]
    const entityResults = await client.recognizePiiEntities(entityPiiInput);

    result.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.text} \tType: ${entity.type} \tSub Type: ${entity.subtype != "" ? entity.subtype : "N/A"}`);
            console.log(`\tOffset: ${entity.offset}, Length: ${entity.length} \tScore: ${entity.score}`);
        });
    });
}
entityPiiRecognition(textAnalyticsClient);
```

Execute o código com `node index.js` na janela do console.

### <a name="output"></a>Saída

```console
Document ID: 0
        Name: 123-12-1234       Type: U.S. Social Security Number (SSN)         Sub Type: N/A
        Offset: 33, Length: 11  Score: 0.85
```

## <a name="entity-linking"></a>Ligação de Entidades

Crie uma matriz de cadeias de caracteres que contenha o documento que você deseja analisar. Chame o método de `recognizeLinkedEntities()` do cliente e obtenha o objeto `RecognizeLinkedEntitiesResult`. Itere na lista de resultados e imprima o nome da entidade, a ID, a fonte de dados, a URL e as correspondências. Cada objeto na matriz de `matches` conterá o deslocamento, o comprimento e a pontuação dessa correspondência.

```javascript
async function linkedEntityRecognition(client){

    const linkedEntityInput = [
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800. During his career at Microsoft, Gates held the positions of chairman, chief executive officer, president and chief software architect, while also being the largest individual shareholder until May 2014."
    ]
    const entityResults = await client.recognizeLinkedEntities(linkedEntityInput);

    entityResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.name} \tID: ${entity.id} \tURL: ${entity.url} \tData Source: ${entity.dataSource}`);
            console.log(`\tMatches:`)
            entity.matches.forEach(match => {
                console.log(`\t\tText: ${match.text}`);
                console.log(`\t\tOffset: ${match.offset}, Length: ${match.length} \tScore: ${match.score.toFixed(3)}`);
            })
        });
    });
}
linkedEntityRecognition(textAnalyticsClient);
```

Execute o código com `node index.js` na janela do console.

### <a name="output"></a>Saída

```console
Document ID: 0
        Name: Altair 8800       ID: Altair 8800         URL: https://en.wikipedia.org/wiki/Altair_8800  Data Source: Wikipedia
        Matches:
                Text: Altair 8800
                Offset: 116, Length: 11         Score: 0.650
        Name: Bill Gates        ID: Bill Gates  URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates
                Offset: 25, Length: 10  Score: 0.243
                Text: Gates
                Offset: 161, Length: 5  Score: 0.243
        Name: Paul Allen        ID: Paul Allen  URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen
                Offset: 40, Length: 10  Score: 0.174
        Name: Microsoft         ID: Microsoft   URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft
                Offset: 0, Length: 9    Score: 0.196
                Text: Microsoft
                Offset: 150, Length: 9  Score: 0.196
        Name: April 4   ID: April 4     URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4
                Offset: 54, Length: 7   Score: 0.137
        Name: BASIC     ID: BASIC       URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC
                Offset: 89, Length: 5   Score: 0.052
```

#### <a name="version-21tabversion-2"></a>[Versão 2.1](#tab/version-2)

> [!NOTE]
> Na versão 2.1, a ligação da entidade está incluída na resposta NER.

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

---

## <a name="key-phrase-extraction"></a>Extração de expressões chave

#### <a name="version-30-previewtabversion-3"></a>[Versão 3.0-pré-visualização](#tab/version-3)

Crie uma matriz de cadeias de caracteres que contenha o documento que você deseja analisar. Chame o método de `extractKeyPhrases()` do cliente e obtenha o objeto de `ExtractKeyPhrasesResult` retornado. Itere os resultados e imprima a ID de cada documento e as frases-chave detectadas.

```javascript
async function keyPhraseExtraction(client){

    const keyPhrasesInput = [
        "My cat might need to see a veterinarian.",
    ]

    const result = await client.extractKeyPhrases(keyPhrasesInput)


    result.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Key Phrases: ${document.keyPhrases}`);
    });
}
keyPhraseExtraction(textAnalyticsClient);
```

Execute o código com `node index.js` na janela do console.

### <a name="output"></a>Saída

```console
ID: 0
        Document Key Phrases: cat,veterinarian
```

#### <a name="version-21tabversion-2"></a>[Versão 2.1](#tab/version-2)

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

---

## <a name="run-the-application"></a>Executar a aplicação

Execute o aplicativo com o comando `node` em seu arquivo de início rápido.

```console
node index.js
```
