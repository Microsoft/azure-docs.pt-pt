---
title: 'Início rápido: Com o node. js para chamar a API de análise de texto'
titleSuffix: Azure Cognitive Services
description: Obtenha informações e exemplos de código para o ajudar a começar a utilizar rapidamente a API de Análise de Texto.
services: cognitive-services
author: raymondl
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 06/11/2019
ms.author: shthowse
ms.openlocfilehash: 7e43d53c0916cf7fdc684c9e044e632015662c3b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67081520"
---
# <a name="quickstart-using-nodejs-to-call-the-text-analytics-cognitive-service"></a>Início rápido: Com o node. js para chamar o serviço cognitivos de análise de texto
<a name="HOLTop"></a>

Utilize este guia de introdução para começar a analisar o idioma com o SDK de análise de texto para node. js. Embora o [análise de texto](//go.microsoft.com/fwlink/?LinkID=759711) REST API é compatível com a maioria das linguagens de programação, o SDK fornece uma forma fácil de integrar o serviço aos seus aplicativos. O código-fonte para este exemplo pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/textAnalytics.js).

Veja as [definições de API](//go.microsoft.com/fwlink/?LinkID=759346) para ter acesso à documentação técnica sobre APIs.

## <a name="prerequisites"></a>Pré-requisitos

* [Node.js](https://nodejs.org/)
* A análise de texto [SDK para node. js](https://www.npmjs.com/package/azure-cognitiveservices-textanalytics) pode instalar o SDK com:

    `npm install azure-cognitiveservices-textanalytics`

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

Também tem de ter a [chave de ponto final e acesso](../How-tos/text-analytics-how-to-access-key.md) que foi gerada automaticamente durante a sua inscrição.

## <a name="create-a-nodejs-application-and-install-the-sdk"></a>Criar uma aplicação node. js e instale o SDK

Depois de instalar o node. js, crie um projeto de nó. Criar um novo diretório para a sua aplicação e navegue para o seu diretório.

```mkdir myapp && cd myapp```

Executar ```npm init``` para criar uma aplicação de nó com um ficheiro Package JSON. Instalar o `ms-rest-azure` e `azure-cognitiveservices-textanalytics` pacotes NPM:

```npm install azure-cognitiveservices-textanalytics ms-rest-azure```

Ficheiro Package JSON da sua aplicação será atualizada com as dependências.

## <a name="authenticate-your-credentials"></a>Autenticar as suas credenciais

Crie um novo ficheiro `index.js` no projeto raiz e importar as bibliotecas instaladas

```javascript
const CognitiveServicesCredentials = require("ms-rest-azure").CognitiveServicesCredentials;
const TextAnalyticsAPIClient = require("azure-cognitiveservices-textanalytics");
```

Crie uma variável para a sua chave de subscrição de análise de texto.

```javascript
let credentials = new CognitiveServicesCredentials(
    "enter-your-key-here"
);
```

> [!Tip]
> Para Implantações seguras de segredos em sistemas de produção, recomendamos utilizar [do Azure Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-net).
>

## <a name="create-a-text-analytics-client"></a>Criar um cliente de análise de texto

Criar uma nova `TextAnalyticsClient` objeto com `credentials` como um parâmetro. Utilize a região do Azure correta para a sua subscrição de análise de texto.

```javascript
//Replace 'westus' with the correct region for your Text Analytics subscription
let client = new TextAnalyticsAPIClient(
    credentials,
    "https://westus.api.cognitive.microsoft.com/"
);
```

## <a name="sentiment-analysis"></a>Análise de sentimentos

Crie uma lista de objetos, que contém os documentos que pretende analisar. O payload para a API consiste numa lista de `documents`, que contêm um `id`, `language`, e `text` atributo. O `text` o texto a ser analisados, os arquivos de atributo `language` é o idioma do documento e o `id` pode ser qualquer valor. 

```javascript
const inputDocuments = {documents:[
    {language:"en", id:"1", text:"I had the best day of my life."},
    {language:"en", id:"2", text:"This was a waste of my time. The speaker put me to sleep."},
    {language:"es", id:"3", text:"No tengo dinero ni nada que dar..."},
    {language:"it", id:"4", text:"L'hotel veneziano era meraviglioso. È un bellissimo pezzo di architettura."}
]}
```

Chamar `client.sentiment` e obter o resultado. Em seguida, iterar os resultados e imprimir o ID de cada documento e classificação de sentimento. Uma pontuação mais próximo de 0 indica um sentimento negativo, enquanto uma pontuação mais perto de 1 indica um sentimento positivo.

```javascript
const operation = client.sentiment({multiLanguageBatchInput: inputDocuments})
operation
.then(result => {
    console.log(result.documents);
})
.catch(err => {
    throw err;
});
```

Executar o seu código com `node index.js` na janela da consola.

### <a name="output"></a>Saída

```console
[ { id: '1', score: 0.8723785877227783 },
  { id: '2', score: 0.1059873104095459 },
  { id: '3', score: 0.43635445833206177 },
  { id: '4', score: 1 } ]
```

## <a name="language-detection"></a>Deteção de idioma

Crie uma lista de objetos que contém os seus documentos. O payload para a API consiste numa lista de `documents`, que contêm um `id` e `text` atributo. O `text` o texto a ser analisados, os arquivos de atributo e o `id` pode ser qualquer valor.

```javascript
// The documents to be submitted for language detection. The ID can be any value.
const inputDocuments = {
    documents: [
        { id: "1", text: "This is a document written in English." },
        { id: "2", text: "Este es un document escrito en Español." },
        { id: "3", text: "这是一个用中文写的文件" }
    ]
    };
```

Chamar `client.detectLanguage()` e obter o resultado. Em seguida, iterar os resultados e imprimir o ID de cada documento e o primeiro idioma retornado.

```javascript
const operation = client.detectLanguage({
    languageBatchInput: inputDocuments
});
operation
    .then(result => {
    result.documents.forEach(document => {
        console.log(`ID: ${document.id}`);
        document.detectedLanguages.forEach(language =>
        console.log(`\tLanguage: ${language.name}`)
        );
    });
    })
    .catch(err => {
    throw err;
    });
```

Executar o seu código com `node index.js` na janela da consola.

### <a name="output"></a>Saída

```console
===== LANGUAGE EXTRACTION ======
ID: 1 Language English
ID: 2 Language Spanish
ID: 3 Language Chinese_Simplified
```

## <a name="entity-recognition"></a>Reconhecimento de entidades

Crie uma lista de objetos, que contém os seus documentos. O payload para a API consiste numa lista de `documents`, que contêm um `id`, `language`, e `text` atributo. O `text` o texto a ser analisados, os arquivos de atributo `language` é o idioma do documento e o `id` pode ser qualquer valor.

```javascript

    const inputDocuments = {documents:[
        {language:"en", id:"1", text:"Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800"},
        {language:"es", id:"2", text:"La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle."},
        ]}

}
```

Chamar `client.entities()` e obter o resultado. Em seguida, iterar os resultados e imprimir o ID de. cada documento Para cada detetada entidade, imprimir o nome da wikipedia, o tipo e tipos secundárias (se existir), bem como as localizações no texto original.

```javascript
const operation = client.entities({
    multiLanguageBatchInput: inputDocuments
});
operation
    .then(result => {
    result.documents.forEach(document => {
        console.log(`Document ID: ${document.id}`)
        document.entities.forEach(e =>{
        console.log(`\tName: ${e.name} Type: ${e.type} Sub Type: ${e.type}`)
        e.matches.forEach(match => (
            console.log(`\t\tOffset: ${match.offset} Length: ${match.length} Score: ${match.entityTypeScore}`)
        ))
        })
    });
    })
    .catch(err => {
    throw err;
    });
```

Executar o seu código com `node index.js` na janela da consola.

### <a name="output"></a>Saída

```console
Document ID: 1
    Name: Microsoft Type: Organization Sub Type: Organization
            Offset: 0 Length: 9 Score: 1
    Name: Bill Gates Type: Person Sub Type: Person
            Offset: 25 Length: 10 Score: 0.999786376953125
    Name: Paul Allen Type: Person Sub Type: Person
            Offset: 40 Length: 10 Score: 0.9988105297088623
    Name: April 4 Type: Other Sub Type: Other
            Offset: 54 Length: 7 Score: 0.8
    Name: April 4, 1975 Type: DateTime Sub Type: DateTime
            Offset: 54 Length: 13 Score: 0.8
    Name: BASIC Type: Other Sub Type: Other
            Offset: 89 Length: 5 Score: 0.8
    Name: Altair 8800 Type: Other Sub Type: Other
            Offset: 116 Length: 11 Score: 0.8
Document ID: 2
    Name: Microsoft Type: Organization Sub Type: Organization
            Offset: 21 Length: 9 Score: 0.999755859375
    Name: Redmond (Washington) Type: Location Sub Type: Location
            Offset: 60 Length: 7 Score: 0.9911284446716309
    Name: 21 kilómetros Type: Quantity Sub Type: Quantity
            Offset: 71 Length: 13 Score: 0.8
    Name: Seattle Type: Location Sub Type: Location
            Offset: 88 Length: 7 Score: 0.9998779296875
```

## <a name="key-phrase-extraction"></a>Extração de expressões chave

Crie uma lista de objetos, que contém os seus documentos. O payload para a API consiste numa lista de `documents`, que contêm um `id`, `language`, e `text` atributo. O `text` o texto a ser analisados, os arquivos de atributo `language` é o idioma do documento e o `id` pode ser qualquer valor.

```javascript
    let inputLanguage = {
    documents: [
        {language:"ja", id:"1", text:"猫は幸せ"},
        {language:"de", id:"2", text:"Fahrt nach Stuttgart und dann zum Hotel zu Fu."},
        {language:"en", id:"3", text:"My cat might need to see a veterinarian."},
        {language:"es", id:"4", text:"A mi me encanta el fútbol!"}
    ]
    };
```

Chamar `client.keyPhrases()` e obter o resultado. Em seguida, iterar os resultados e imprimir o ID de cada documento e qualquer expressões chave detetados.

```javascript
    let operation = client.keyPhrases({
    multiLanguageBatchInput: inputLanguage
    });
    operation
    .then(result => {
        console.log(result.documents);
    })
    .catch(err => {
        throw err;
    });
```

Executar o seu código com `node index.js` na janela da consola.

### <a name="output"></a>Saída

```console
[ 
    { id: '1', keyPhrases: [ '幸せ' ] },
    { id: '2', keyPhrases: [ 'Stuttgart', 'Hotel', 'Fahrt', 'Fu' ] },
    { id: '3', keyPhrases: [ 'cat', 'veterinarian' ] },
    { id: '4', keyPhrases: [ 'fútbol' ] } 
]
```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Análise de Texto com o Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Consulte também

 [Descrição geral da análise de texto](../overview.md) [perguntas mais frequentes sobre (FAQ)](../text-analytics-resource-faq.md)
