---
title: 'Início rápido: Usando o Node. js para chamar o API de Análise de Texto'
titleSuffix: Azure Cognitive Services
description: Obtenha informações e exemplos de código para o ajudar a começar a utilizar rapidamente a API de Análise de Texto.
services: cognitive-services
author: raymondl
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 07/30/2019
ms.author: shthowse
ms.openlocfilehash: 9b8a713d58d5753e04de050e0bc961b5e8388123
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68697486"
---
# <a name="quickstart-using-nodejs-to-call-the-text-analytics-cognitive-service"></a>Início rápido: Usando o Node. js para chamar o serviço de Análise de Texto cognitiva
<a name="HOLTop"></a>

Use este guia de início rápido para começar a analisar a linguagem com o SDK do Análise de Texto para node. js. Embora a API REST do [análise de texto](//go.microsoft.com/fwlink/?LinkID=759711) seja compatível com a maioria das linguagens de programação, o SDK fornece uma maneira fácil de integrar o serviço em seus aplicativos. O código-fonte para este exemplo pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/textAnalytics.js).

Veja as [definições de API](//go.microsoft.com/fwlink/?LinkID=759346) para ter acesso à documentação técnica sobre APIs.

## <a name="prerequisites"></a>Pré-requisitos

* [Node.js](https://nodejs.org/)
* O SDK do Análise de Texto [para node. js](https://www.npmjs.com/package/azure-cognitiveservices-textanalytics) você pode instalar o SDK com:

    `npm install azure-cognitiveservices-textanalytics`

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

Também tem de ter a [chave de ponto final e acesso](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) que foi gerada automaticamente durante a sua inscrição.

## <a name="create-a-nodejs-application-and-install-the-sdk"></a>Criar um aplicativo node. js e instalar o SDK

Depois de instalar o Node. js, crie um projeto de nó. Crie um novo diretório para seu aplicativo e navegue até seu diretório.

```mkdir myapp && cd myapp```

Execute ```npm init``` para criar um aplicativo de nó com um arquivo Package. JSON. Instale os `ms-rest-azure` pacotes `azure-cognitiveservices-textanalytics` e NPM:

```npm install azure-cognitiveservices-textanalytics ms-rest-azure```

O arquivo Package. JSON do seu aplicativo será atualizado com as dependências.

## <a name="authenticate-your-credentials"></a>Autenticar suas credenciais

Criar um novo arquivo `index.js` na raiz do projeto e importar as bibliotecas instaladas

```javascript
const CognitiveServicesCredentials = require("ms-rest-azure").CognitiveServicesCredentials;
const TextAnalyticsAPIClient = require("azure-cognitiveservices-textanalytics");
```

Crie uma variável para sua chave de assinatura do Análise de Texto.

```javascript
let credentials = new CognitiveServicesCredentials(
    "enter-your-key-here"
);
```

> [!Tip]
> Para a implantação segura de segredos em sistemas de produção, é recomendável usar [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-net).
>

## <a name="create-a-text-analytics-client"></a>Criar um cliente Análise de Texto

Crie um novo `TextAnalyticsClient` objeto com `credentials` como um parâmetro. Use a região do Azure correta para sua assinatura do Análise de Texto.

```javascript
//Replace 'westus' with the correct region for your Text Analytics subscription
let client = new TextAnalyticsAPIClient(
    credentials,
    "https://westus.api.cognitive.microsoft.com/"
);
```

## <a name="sentiment-analysis"></a>Análise de sentimentos

Crie uma lista de objetos, contendo os documentos que você deseja analisar. A carga para a API consiste em uma lista de `documents`, que contém um `id`atributo `language`, e `text` . O `text` atributo armazena o texto a ser analisado `language` , é o idioma do documento e `id` pode ser qualquer valor. 

```javascript
const inputDocuments = {documents:[
    {language:"en", id:"1", text:"I had the best day of my life."},
    {language:"en", id:"2", text:"This was a waste of my time. The speaker put me to sleep."},
    {language:"es", id:"3", text:"No tengo dinero ni nada que dar..."},
    {language:"it", id:"4", text:"L'hotel veneziano era meraviglioso. È un bellissimo pezzo di architettura."}
]}
```

Chame `client.sentiment` e obtenha o resultado. Em seguida, itere pelos resultados e imprima a ID de cada documento e a pontuação de sentimentos. Uma pontuação mais próxima de 0 indica uma observação negativa, enquanto uma pontuação mais próxima de 1 indica uma observação positiva.

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

Execute o código com `node index.js` na janela do console.

### <a name="output"></a>Output

```console
[ { id: '1', score: 0.8723785877227783 },
  { id: '2', score: 0.1059873104095459 },
  { id: '3', score: 0.43635445833206177 },
  { id: '4', score: 1 } ]
```

## <a name="language-detection"></a>Deteção de idioma

Crie uma lista de objetos que contêm seus documentos. A carga para a API consiste em uma lista de `documents`, que contém um `id` atributo `text` e. O `text` atributo armazena o texto a ser analisado `id` e pode ser qualquer valor.

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

Chame `client.detectLanguage()` e obtenha o resultado. Em seguida, itere pelos resultados e imprima a ID de cada documento e o primeiro idioma retornado.

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

Execute o código com `node index.js` na janela do console.

### <a name="output"></a>Output

```console
===== LANGUAGE EXTRACTION ======
ID: 1 Language English
ID: 2 Language Spanish
ID: 3 Language Chinese_Simplified
```

## <a name="entity-recognition"></a>Reconhecimento de entidade

Crie uma lista de objetos, contendo seus documentos. A carga para a API consiste em uma lista de `documents`, que contém um `id`atributo `language`, e `text` . O `text` atributo armazena o texto a ser analisado `language` , é o idioma do documento e `id` pode ser qualquer valor.

```javascript

    const inputDocuments = {documents:[
        {language:"en", id:"1", text:"Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800"},
        {language:"es", id:"2", text:"La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle."},
        ]}

}
```

Chame `client.entities()` e obtenha o resultado. Em seguida, itere pelos resultados e imprima a ID de cada documento. Para cada entidade detectada, imprima seu nome da Wikipédia, o tipo e os subtipos (se existir), bem como os locais no texto original.

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

Execute o código com `node index.js` na janela do console.

### <a name="output"></a>Output

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

Crie uma lista de objetos, contendo seus documentos. A carga para a API consiste em uma lista de `documents`, que contém um `id`atributo `language`, e `text` . O `text` atributo armazena o texto a ser analisado `language` , é o idioma do documento e `id` pode ser qualquer valor.

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

Chame `client.keyPhrases()` e obtenha o resultado. Em seguida, itere os resultados e imprima a ID de cada documento e quaisquer frases-chave detectadas.

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

Execute o código com `node index.js` na janela do console.

### <a name="output"></a>Output

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

 [Visão geral de análise de texto](../overview.md) [Perguntas frequentes (FAQ)](../text-analytics-resource-faq.md)
