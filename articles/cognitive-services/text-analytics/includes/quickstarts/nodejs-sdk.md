---
title: 'Início Rápido: Biblioteca de cliente de Análise de Texto v3 para Node.js | Microsoft Docs'
description: Comece a utilizar a biblioteca de cliente de Análise de Texto v3 para Node.js.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/06/2020
ms.author: aahi
ms.reviewer: sumeh, assafi
ms.openlocfilehash: 4ce2da3070105f6e098373108164b6f590d423c6
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2020
ms.locfileid: "78925866"
---
<a name="HOLTop"></a>

#### <a name="version-30-preview"></a>[Versão 3.0-pré-visualização](#tab/version-3)

[Documentação de referência v3](https://aka.ms/azsdk-js-textanalytics-ref-docs) | [Código fonte da Biblioteca v3](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics) | [Pacote v3 (NPM)](https://www.npmjs.com/package/@azure/ai-text-analytics) | [Exemplos v3](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics/samples)


#### <a name="version-21"></a>[Versão 2.1](#tab/version-2)

[Documentação de referência v2](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics) | [Código fonte da Biblioteca v2](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/cognitiveServicesTextAnalytics) | [Pacote v2 (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-textanalytics) | [Exemplos v2](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/)

---

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição do Azure - [Criar uma gratuitamente](https://azure.microsoft.com/free/)
* A versão atual do [Node.js](https://nodejs.org/).
* Assim que tiver a sua subscrição do Azure, pode <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Criar um recurso de Análise de Texto"  target="_blank">criar um recurso de Análise de Texto <span class="docon docon-navigate-external x-hidden-focus"></span></a> no portal do Azure para obter a chave e o ponto final. 
    * Necessitará da chave e do ponto final do recurso criado para ligar a sua aplicação à API de Análise de Texto. Fará isto mais adiante no início rápido.
    * Pode utilizar o escalão de preço gratuito para experimentar o serviço e atualizar mais tarde para um escalão pago para produção.

## <a name="setting-up"></a>Configuração

### <a name="create-a-new-nodejs-application"></a>Criar uma nova aplicação Node.js

> [!NOTE]
> Também pode executar esta versão da biblioteca de cliente de Análise de Texto [no seu browser](https://github.com/Azure/azure-sdk-for-js/blob/master/documentation/Bundling.md).

Numa janela de consola (como cmd, PowerShell ou Bash), crie um novo diretório para a sua aplicação e navegue para a mesma. 

```console
mkdir myapp && cd myapp
```

Execute o comando `npm init` para criar uma aplicação de nó com um ficheiro `package.json`. 

```console
npm init
```
### <a name="install-the-client-library"></a>Instalar a biblioteca de cliente

#### <a name="version-30-preview"></a>[Versão 3.0-pré-visualização](#tab/version-3)

Instale os pacotes NPM `@azure/ai-text-analytics`:

```console
npm install --save @azure/ai-text-analytics
```

#### <a name="version-21"></a>[Versão 2.1](#tab/version-2)

Instale os pacotes NPM `@azure/cognitiveservices-textanalytics`:

```console
npm install --save @azure/cognitiveservices-textanalytics
```

O ficheiro `package.json` da sua aplicação será atualizado com as dependências.

Crie um ficheiro com o nome `index.js` e adicione as seguintes bibliotecas:

---

O ficheiro `package.json` da sua aplicação será atualizado com as dependências.
Crie um ficheiro com o nome `index.js` e adicione as seguintes bibliotecas:

#### <a name="version-30-preview"></a>[Versão 3.0-pré-visualização](#tab/version-3)

```javascript
"use strict";

const { TextAnalyticsClient, TextAnalyticsApiKeyCredential } = require("@azure/ai-text-analytics");
```

#### <a name="version-21"></a>[Versão 2.1](#tab/version-2)

```javascript
"use strict";

const { TextAnalyticsClient, CognitiveServicesCredential } = require("@azure/cognitiveservices-textanalytics");
```
---

Crie variáveis para o ponto final e a chave do Azure do seu recurso.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```javascript
const key = '<paste-your-text-analytics-key-here>';
const endpoint = `<paste-your-text-analytics-endpoint-here>`;
```

## <a name="object-model"></a>Modelo de objeto

O cliente de Análise de Texto é um objeto `TextAnalyticsClient` que faz a autenticação no Azure com a sua chave. O cliente fornece vários métodos para analisar o texto, como uma única cadeia ou um lote.

O texto é enviado para a API como uma lista de `documents`, que são objetos `dictionary` com uma combinação de atributos `id`, `text` e `language` consoante o método utilizado. O atributo `text` armazena o texto a analisar no `language`, de origem e o `id` pode ser qualquer valor. 

O objeto de resposta é uma lista que contém as informações de análise de cada documento. 

## <a name="code-examples"></a>Exemplos de código

* [Autenticação de Cliente](#client-authentication)
* [Análise de Sentimentos](#sentiment-analysis) 
* [Deteção de idioma](#language-detection)
* [Reconhecimento de entidades nomeadas](#named-entity-recognition-ner)
* [Associação de entidades](#entity-linking)
* [Extração de expressões-chave](#key-phrase-extraction)

## <a name="client-authentication"></a>Autenticação de Cliente

#### <a name="version-30-preview"></a>[Versão 3.0-pré-visualização](#tab/version-3)

Crie um novo objeto `TextAnalyticsClient` com a chave e o ponto final como parâmetros.

```javascript
const textAnalyticsClient = new TextAnalyticsClient(endpoint,  new TextAnalyticsApiKeyCredential(key));
```

#### <a name="version-21"></a>[Versão 2.1](#tab/version-2)

Crie um novo objeto [TextAnalyticsClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient) com `credentials` e `endpoint` como parâmetros.

[!code-javascript[Authentication and client creation](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=authentication)]

---

## <a name="sentiment-analysis"></a>Análise de sentimentos

#### <a name="version-30-preview"></a>[Versão 3.0-pré-visualização](#tab/version-3)

Crie uma matriz de cadeias com o documento que quer analisar. Chame o método `analyzeSentiment()` do cliente e obtenha o objeto `SentimentBatchResult` devolvido. Itere através da lista de resultados e imprima o ID de cada documento, sentimento ao nível do documento com classificações de confiança. Para cada documento, o resultado contém o sentimento ao nível da frase juntamente com desvios, comprimento e classificações de confiança.

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
        console.log(`\t\tPositive: ${document.sentimentScores.positive.toFixed(2)} \tNegative: ${document.sentimentScores.negative.toFixed(2)} \tNeutral: ${document.sentimentScores.neutral.toFixed(2)}`);
        console.log(`\tSentences Sentiment(${document.sentences.length}):`);
        document.sentences.forEach(sentence => {
            console.log(`\t\tSentence sentiment: ${sentence.sentiment}`)
            console.log(`\t\tSentences Scores:`);
            console.log(`\t\tPositive: ${sentence.sentimentScores.positive.toFixed(2)} \tNegative: ${sentence.sentimentScores.negative.toFixed(2)} \tNeutral: ${sentence.sentimentScores.neutral.toFixed(2)}`);
            console.log(`\t\tLength: ${sentence.length}, Offset: ${sentence.offset}`);
        })
    });
}
sentimentAnalysis(textAnalyticsClient)
```

Execute o código com `node index.js` na janela da consola.

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
                Length: 30, Offset: 0
                Sentence sentiment: neutral
                Sentences Scores:
                Positive: 0.21  Negative: 0.02  Neutral: 0.77
                Length: 30, Offset: 31
```

#### <a name="version-21"></a>[Versão 2.1](#tab/version-2)

Crie uma lista de objetos de dicionário, com os documentos que quer analisar. Chame o método [sentiment()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#sentiment-models-textanalyticsclientsentimentoptionalparams-) do cliente e obtenha o objeto [SentimentBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/sentimentbatchresult) devolvido. Itere através da lista de resultados e imprima o ID de cada documento e classificação de confiança. Uma classificação próxima de 0 indica um sentimento negativo, enquanto que uma classificação próxima de 1 indica um sentimento positivo.

[!code-javascript[Sentiment analysis](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=sentimentAnalysis)]

Execute o código com `node index.js` na janela da consola.

### <a name="output"></a>Saída

```console
[ { id: '1', score: 0.87 } ]
[ { id: '2', score: 0.11 } ]
[ { id: '3', score: 0.44 } ]
[ { id: '4', score: 1.00 } ]
```

---

## <a name="language-detection"></a>Deteção de idioma

#### <a name="version-30-preview"></a>[Versão 3.0-pré-visualização](#tab/version-3)

Crie uma matriz de cadeias com o documento que quer analisar. Chame o método `detectLanguage()` do cliente e obtenha o objeto `DetectLanguageResultCollection` devolvido. Em seguida, itere através dos resultados e imprima o ID de cada documento com a respetiva linguagem primária.

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

Execute o código com `node index.js` na janela da consola.

### <a name="output"></a>Saída

```console
ID: 0
        Primary Language French
```

#### <a name="version-21"></a>[Versão 2.1](#tab/version-2)

Crie uma lista de objetos de dicionário com os seus documentos. Chame o método [detectLanguage()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#detectlanguage-models-textanalyticsclientdetectlanguageoptionalparams-) do cliente e obtenha o objeto [LanguageBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/languagebatchresult) devolvido. Em seguida, itere através dos resultados e imprima o ID de cada documento e linguagem.

[!code-javascript[Language detection](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=languageDetection)]

Execute o código com `node index.js` na janela da consola.

### <a name="output"></a>Saída

```console
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

---

## <a name="named-entity-recognition-ner"></a>Reconhecimento de Entidades Nomeadas (NER)

#### <a name="version-30-preview"></a>[Versão 3.0-pré-visualização](#tab/version-3)

> [!NOTE]
> Na versão `3.0-preview`:
> * o NER inclui métodos separados para detetar informações pessoais. 
> * A associação de entidades é um pedido separado do NER.

Crie uma matriz de cadeias com o documento que quer analisar. Chame o método `recognizeEntities()` do cliente e obtenha o objeto `RecognizeEntitiesResult`. Itere através da lista de resultados e imprima o nome da entidade, tipo, subtipo, desvio, comprimento e classificação.

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
            console.log(`\tOffset: ${entity.offset}, Length: ${entity.length} \tScore: ${entity.score}`);
        });
    });
}
entityRecognition(textAnalyticsClient);
```

Execute o código com `node index.js` na janela da consola.

### <a name="output"></a>Saída

```console
Document ID: 0
        Name: Microsoft         Category: Organization  Subcategory: N/A
        Offset: 0, Length: 9    Score: 1
        Name: Bill Gates        Category: Person        Subcategory: N/A
        Offset: 25, Length: 10  Score: 0.67
        Name: Paul Allen        Category: Person        Subcategory: N/A
        Offset: 40, Length: 10  Score: 0.81
        Name: April 4, 1975     Category: DateTime      Subcategory: Date
        Offset: 54, Length: 13  Score: 0.8
        Name: interpreters      Category: PersonType    Subcategory: N/A
        Offset: 95, Length: 12  Score: 0.6
        Name: 8800      Category: Quantity      Subcategory: Number
        Offset: 123, Length: 4  Score: 0.8
Document ID: 1
        Name: Microsoft         Category: Organization  Subcategory: N/A
        Offset: 21, Length: 9   Score: 0.96
        Name: Redmond   Category: Location      Subcategory: GPE
        Offset: 60, Length: 7   Score: 0.09
        Name: 21        Category: Quantity      Subcategory: Number
        Offset: 71, Length: 2   Score: 0.8
        Name: Seattle   Category: Location      Subcategory: GPE
```

## <a name="using-ner-to-detect-personal-information"></a>Utilizar o NER para detetar informações pessoais

Crie uma matriz de cadeias com o documento que quer analisar. Chame o método `recognizePiiEntities()` do cliente e obtenha o objeto `EntitiesBatchResult`. Itere através da lista de resultados e imprima o nome da entidade, tipo, subtipo, desvio, comprimento e classificação.


```javascript
async function entityPiiRecognition(client){

    const entityPiiInput = [
        "Insurance policy for SSN on file 123-12-1234 is here by approved."
    ];
    const entityPiiResults = await client.recognizePiiEntities(entityPiiInput);

    entityPiiResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.text} \tCategory: ${entity.category} \tSubcategory: ${entity.subCategory ? entity.subCategory : "N/A"}`);
            console.log(`\tOffset: ${entity.offset}, Length: ${entity.length} \tScore: ${entity.score}`);
        });
    });
}
entityPiiRecognition(textAnalyticsClient);
```

Execute o código com `node index.js` na janela da consola.

### <a name="output"></a>Saída

```console
Document ID: 0
        Name: 123-12-1234       Category: U.S. Social Security Number (SSN)     Subcategory: N/A
        Offset: 33, Length: 11  Score: 0.85
```

## <a name="entity-linking"></a>Ligar à Entidade

Crie uma matriz de cadeias com o documento que quer analisar. Chame o método `recognizeLinkedEntities()` do cliente e obtenha o objeto `RecognizeLinkedEntitiesResult`. Itere através da lista de resultados e imprima o nome da entidade, ID, origem de dados, URL e correspondências. Cada objeto na matriz `matches` conterá o desvio, comprimento e classificação dessa correspondência.

```javascript
async function linkedEntityRecognition(client){

    const linkedEntityInput = [
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800. During his career at Microsoft, Gates held the positions of chairman, chief executive officer, president and chief software architect, while also being the largest individual shareholder until May 2014."
    ];
    const entityResults = await client.recognizeLinkedEntities(linkedEntityInput);

    entityResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.name} \tID: ${entity.id} \tURL: ${entity.url} \tData Source: ${entity.dataSource}`);
            console.log(`\tMatches:`)
            entity.matches.forEach(match => {
                console.log(`\t\tText: ${match.text}`);
                console.log(`\t\tOffset: ${match.offset}, Length: ${match.length} \tScore: ${match.score.toFixed(3)}`);
            });
        });
    });
}
linkedEntityRecognition(textAnalyticsClient);
```

Execute o código com `node index.js` na janela da consola.

### <a name="output"></a>Saída

```console
Document ID: 0
        Name: Altair 8800       ID: Altair 8800         URL: https://en.wikipedia.org/wiki/Altair_8800  Data Source: Wikipedia
        Matches:
                Text: Altair 8800
                Offset: 116, Length: 11         Score: 0.777
        Name: Bill Gates        ID: Bill Gates  URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates
                Offset: 25, Length: 10  Score: 0.555
                Text: Gates
                Offset: 161, Length: 5  Score: 0.555
        Name: Paul Allen        ID: Paul Allen  URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen
                Offset: 40, Length: 10  Score: 0.533
        Name: Microsoft         ID: Microsoft   URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft
                Offset: 0, Length: 9    Score: 0.469
                Text: Microsoft
                Offset: 150, Length: 9  Score: 0.469
        Name: April 4   ID: April 4     URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4
                Offset: 54, Length: 7   Score: 0.248
        Name: BASIC     ID: BASIC       URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC
                Offset: 89, Length: 5   Score: 0.281
```

#### <a name="version-21"></a>[Versão 2.1](#tab/version-2)

> [!NOTE]
> Na versão 2.1, a associação de entidades está incluída na resposta do NER.

Crie uma lista de objetos com os seus documentos. Chame o método [entities()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#entities-models-textanalyticscliententitiesoptionalparams-) do cliente e obtenha o objeto [EntitiesBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/entitiesbatchresult). Itere através da lista de resultados e imprima o ID de cada documento. Para cada entidade detetada, imprima o nome na Wikipédia, o tipo e os subtipos (se existirem), bem como as localizações no texto original.

[!code-javascript[Entity recognition](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=entityRecognition)]

Execute o código com `node index.js` na janela da consola.

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

#### <a name="version-30-preview"></a>[Versão 3.0-pré-visualização](#tab/version-3)

Crie uma matriz de cadeias com o documento que quer analisar. Chame o método `extractKeyPhrases()` do cliente e obtenha o objeto `ExtractKeyPhrasesResult` devolvido. Itere através dos resultados e imprima o ID de cada documento e quaisquer expressões-chave detetadas.

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

Execute o código com `node index.js` na janela da consola.

### <a name="output"></a>Saída

```console
ID: 0
        Document Key Phrases: cat,veterinarian
```

#### <a name="version-21"></a>[Versão 2.1](#tab/version-2)

Crie uma lista de objetos com os seus documentos. Chame o método [keyPhrases()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#keyphrases-models-textanalyticsclientkeyphrasesoptionalparams-) do cliente e obtenha o objeto [KeyPhraseBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/keyphrasebatchresult) devolvido. Itere através dos resultados e imprima o ID de cada documento e quaisquer expressões-chave detetadas.

[!code-javascript[Key phrase extraction](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=keyPhraseExtraction)]

Execute o código com `node index.js` na janela da consola.

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

Execute a aplicação com o comando `node` no seu ficheiro de início rápido.

```console
node index.js
```
