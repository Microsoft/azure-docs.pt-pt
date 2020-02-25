---
title: 'Quickstart: Biblioteca de clientes de Análise de Texto para Go / Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Neste arranque rápido, detete a linguagem utilizando a biblioteca de clientes Go Text Analytics da Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 01/29/2020
ms.author: aahi
ms.openlocfilehash: 5623be028cfe10c0ea6786b24bf75ae2bf9c5255
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2020
ms.locfileid: "77560791"
---
# <a name="quickstart-use-the-text-analytics-client-library-for-go"></a>Quickstart: Use a biblioteca de clientes de Análise de Texto para Go

[Documentação de referência](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/textanalytics?view=azure-python) | [Biblioteca código](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-textanalytics) de origem | [Pacote (GitHub)](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics) [amostras](https://github.com/Azure-Samples/cognitive-services-quickstart-code) de | 

> [!NOTE]
> Este quickstart aplica-se apenas à versão 2.1 do Text Analytics. Atualmente, uma biblioteca de clientes V3 para Go não está disponível.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição Azure - [crie uma gratuitamente](https://azure.microsoft.com/free/)
* A versão mais recente de [Go](https://golang.org/dl/)

## <a name="setting-up"></a>Configuração

### <a name="create-a-text-analytics-azure-resource"></a>Criar um recurso Text Analytics Azure 

[!INCLUDE [text-analytics-resource-creation](../includes/quickstarts/resource-creation.md)]

### <a name="create-a-new-go-project"></a>Criar um novo projeto Go

Numa janela de consola (cmd, PowerShell, Terminal, Bash), crie um novo espaço de trabalho para o seu projeto Go e navegue até ele. O seu espaço de trabalho conterá três pastas: 

* **sRC** - Este diretório contém código fonte e pacotes. Quaisquer pacotes instalados com o comando `go get` residem aqui.
* **pkg** - Este diretório contém os objetos de pacote Go compilados. Todos estes ficheiros têm uma extensão `.a`.
* **bin** - Este diretório contém os ficheiros executáveis binários que são criados quando executa `go install`.

> [!TIP]
> Saiba mais sobre a estrutura de um espaço de [trabalho Go.](https://golang.org/doc/code.html#Workspaces) Este guia inclui informações para a definição de `$GOPATH` e `$GOROOT`.

Crie um espaço de trabalho chamado `my-app` e os subdiretórios necessários para `src`, `pkg`e `bin`:

```console
$ mkdir -p my-app/{src, bin, pkg}  
$ cd my-app
```

### <a name="install-the-text-analytics-client-library-for-go"></a>Instale a biblioteca de clientes Text Analytics para Go

Instale a biblioteca de clientes para Go: 

```console
$ go get -u <https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics>
```

ou se utilizar o dep, dentro da sua corrida de repo:

```console
$ dep ensure -add <https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics>
```

### <a name="create-your-go-application"></a>Crie a sua aplicação Go

Em seguida, crie um ficheiro chamado `src/quickstart.go`:

```bash
$ cd src
$ touch quickstart.go
```

Abra `quickstart.go` no seu IDE favorito ou editor de texto. Em seguida, adicione o nome do pacote e importe as seguintes bibliotecas:

[!code-go[Import statements](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=imports)]

## <a name="object-model"></a>Modelo de objeto 

O cliente Text Analytics é um objeto [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#New) que autentica o Azure usando a sua chave. O cliente fornece vários métodos para analisar texto, como uma única corda, ou um lote. 

O texto é enviado para a API como uma lista de `documents`, que são `dictionary` objetos que contêm uma combinação de `id`, `text`, e `language` atributos dependendo do método utilizado. O atributo `text` armazena o texto a analisar na origem `language`, e o `id` pode ser qualquer valor. 

O objeto de resposta é uma lista que contém as informações de análise de cada documento. 

## <a name="code-examples"></a>Exemplos de código

Estes fragmentos de código mostram-lhe como fazer o seguinte com a biblioteca de clientes text Analytics para Python:

* [Autenticar o cliente](#authenticate-the-client)
* [Análise de Sentimentos](#sentiment-analysis)
* [Deteção de linguagem](#language-detection)
* [Reconhecimento de entidades](#entity-recognition)
* [Extração de frase-chave](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Autenticar o cliente


Numa nova função, crie variáveis para o ponto final do seu recurso Azure e chave de subscrição.

[!INCLUDE [text-analytics-find-resource-information](../includes/find-azure-resource-info.md)]

Crie um novo objeto [BaseClient.](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#New) Passe a chave para o [encosto. A função NewCognitiveServicesAuthorizer()](https://godoc.org/github.com/Azure/go-autorest/autorest#NewCognitiveServicesAuthorizer) que será depois passada para a propriedade `authorizer` do cliente.

```go
func GetTextAnalyticsClient() textanalytics.BaseClient {
    var key string = "<paste-your-text-analytics-key-here>"
    var endpoint string = "<paste-your-text-analytics-endpoint-here>"

    textAnalyticsClient := textanalytics.New(endpoint)
    textAnalyticsClient.Authorizer = autorest.NewCognitiveServicesAuthorizer(key)

    return textAnalyticsClient
}
```

## <a name="sentiment-analysis"></a>Análise de sentimentos

Crie uma nova função chamada `SentimentAnalysis()` e crie um cliente usando o método `GetTextAnalyticsClient()` criado anteriormente. Crie uma lista de objetos [MultiLanguageInput,](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput) contendo os documentos que pretende analisar. Cada objeto conterá um `id`, `Language` e um atributo `text`. O `text` atributo armazena o texto a analisar, `language` é a linguagem do documento, e o `id` pode ser qualquer valor. 

Ligue para a função [sentimentais](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.Sentiment) do cliente e obtenha o resultado. Em seguida, iterar através dos resultados, e imprimir a identificação de cada documento, e pontuação de sentimento. Uma pontuação mais próxima de 0 indica um sentimento negativo, enquanto uma pontuação mais próxima de 1 indica um sentimento positivo.

[!code-go[Sentiment analysis sample](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=sentimentAnalysis)]

chame `SentimentAnalysis()` no seu projeto.

### <a name="output"></a>Saída

```console
Document ID: 1 , Sentiment Score: 0.87
Document ID: 2 , Sentiment Score: 0.11
Document ID: 3 , Sentiment Score: 0.44
Document ID: 4 , Sentiment Score: 1.00
```

## <a name="language-detection"></a>Deteção de idioma

Crie uma nova função chamada `LanguageDetection()` e crie um cliente usando o método `GetTextAnalyticsClient()` criado anteriormente. Crie uma lista de objetos [LanguageInput,](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#LanguageInput) contendo os documentos que pretende analisar. Cada objeto conterá um `id` e um atributo `text`. O atributo `text` armazena o texto a ser analisado, e o `id` pode ser qualquer valor. 

Ligue para o [DetectLanguage](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.DetectLanguage) do cliente e obtenha o resultado. Em seguida, iterar através dos resultados, e imprimir a identificação de cada documento, e linguagem detetada.

[!code-go[Language detection sample](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=languageDetection)]

chame `LanguageDetection()` no seu projeto.

### <a name="output"></a>Saída

```console
Document ID: 0 , Language: English 
Document ID: 1 , Language: Spanish
Document ID: 2 , Language: Chinese_Simplified
```

## <a name="entity-recognition"></a>Reconhecimento de entidades

Crie uma nova função chamada `ExtractEntities()` e crie um cliente usando o método `GetTextAnalyticsClient()` criado anteriormente. Crie uma lista de objetos [MultiLanguageInput,](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput) contendo os documentos que pretende analisar. Cada objeto conterá um `id`, `language`, e um atributo `text`. O `text` atributo armazena o texto a analisar, `language` é a linguagem do documento, e o `id` pode ser qualquer valor. 

Ligue para as [Entidades](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.Entities) do Cliente e obtenha o resultado. Em seguida, iterar através dos resultados, e imprimir a identificação de cada documento, e entidades extraídas pontuam.

[!code-go[entity recognition sample](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=entityRecognition)]

chame `ExtractEntities()` no seu projeto.

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

## <a name="key-phrase-extraction"></a>Extração de expressões-chave

Crie uma nova função chamada `ExtractKeyPhrases()` e crie um cliente usando o método `GetTextAnalyticsClient()` criado anteriormente. Crie uma lista de objetos [MultiLanguageInput,](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput) contendo os documentos que pretende analisar. Cada objeto conterá um `id`, `language`, e um atributo `text`. O `text` atributo armazena o texto a analisar, `language` é a linguagem do documento, e o `id` pode ser qualquer valor.

Ligue para as [Frases-Chave](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.KeyPhrases) do cliente e obtenha o resultado. Em seguida, iterar através dos resultados, e imprimir o ID de cada documento, e extraído frases-chave.

[!code-go[key phrase extraction sample](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=keyPhrases)]

chame `ExtractKeyPhrases()` no seu projeto.

### <a name="output"></a>Saída

```console
Document ID: 1
         Key phrases:
                幸せ
Document ID: 2
         Key phrases:
                Stuttgart
                Hotel
                Fahrt
                Fu
Document ID: 3
         Key phrases:
                cat
                veterinarian
Document ID: 4
         Key phrases:
                fútbol
```
