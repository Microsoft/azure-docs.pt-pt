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
ms.date: 02/26/2020
ms.author: aahi
ms.openlocfilehash: f9b5fb1837fdafe818fdb27221f8575d93ec304d
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83701621"
---
# <a name="quickstart-use-the-text-analytics-client-library-for-go"></a>Quickstart: Use a biblioteca de clientes de Análise de Texto para Go

[Documentação de](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics)  |  referência Código fonte [da biblioteca](https://github.com/Azure/azure-sdk-for-go/blob/090dc0ee4d8d2d60e2a9525774d967a4111a2b0c/services/cognitiveservices/v2.1/textanalytics/client.go)  |  [Pacote (GitHub)](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics)  |  [Amostras](https://github.com/Azure-Samples/cognitive-services-quickstart-code)

> [!NOTE]
> Este quickstart aplica-se apenas à versão 2.1 do Text Analytics. Atualmente, uma biblioteca de clientes V3 para Go não está disponível.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição Azure - [crie uma gratuitamente](https://azure.microsoft.com/free/)
* A versão mais recente de [Go](https://golang.org/dl/)
* Assim que tiver a sua subscrição Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title=" crie um recurso Text "  target="_blank"> Analytics criar um recurso Text Analytics no portal <span class="docon docon-navigate-external x-hidden-focus"></span> </a> Azure para obter a sua chave e ponto final. 
    * Necessitará da chave e do ponto final do recurso que cria para ligar a sua aplicação à API textanalytics. Vais fazer isto mais tarde, no início.
    * Você pode usar o nível de preços gratuitos para experimentar o serviço, e atualizar mais tarde para um nível pago para produção.

## <a name="setting-up"></a>Configuração

### <a name="create-a-new-go-project"></a>Criar um novo projeto Go

Numa janela de consola (cmd, PowerShell, Terminal, Bash), crie um novo espaço de trabalho para o seu projeto Go e navegue até ele. O seu espaço de trabalho conterá três pastas: 

* **sRC** - Este diretório contém código fonte e pacotes. Quaisquer pacotes instalados com o `go get` comando residem aqui.
* **pkg** - Este diretório contém os objetos de pacote Go compilados. Todos estes ficheiros têm uma `.a` extensão.
* **bin** - Este diretório contém os ficheiros executáveis binários que são criados quando executa `go install` .

> [!TIP]
> Saiba mais sobre a estrutura de um espaço de [trabalho Go.](https://golang.org/doc/code.html#Workspaces) Este guia inclui informações para a definição `$GOPATH` e `$GOROOT` .

Criar um espaço de trabalho chamado `my-app` e os subdiretórios necessários `src` para, `pkg` `bin` e:

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

Em seguida, crie um ficheiro `src/quickstart.go` chamado:

```bash
$ cd src
$ touch quickstart.go
```

Abra `quickstart.go` no seu IDE favorito ou editor de texto. Em seguida, adicione o nome do pacote e importe as seguintes bibliotecas:

[!code-go[Import statements](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=imports)]

## <a name="object-model"></a>Modelo de objeto 

O cliente Text Analytics é um objeto [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#New) que autentica o Azure usando a sua chave. O cliente fornece vários métodos para analisar texto, como uma única corda, ou um lote. 

O texto é enviado para a API como uma lista `documents` de, que são `dictionary` objetos que contêm uma combinação `id` `text` de, e `language` atributos dependendo do método utilizado. O `text` atributo armazena o texto a ser analisado na `language` origem, e pode `id` ser qualquer valor. 

O objeto de resposta é uma lista que contém as informações de análise de cada documento. 

## <a name="code-examples"></a>Exemplos de código

Estes fragmentos de código mostram-lhe como fazer o seguinte com a biblioteca de clientes text Analytics para Go:

* [Autenticar o cliente](#authenticate-the-client)
* [Análise de Sentimentos](#sentiment-analysis)
* [Deteção de idioma](#language-detection)
* [Reconhecimento de entidades](#entity-recognition)
* [Extração de frase-chave](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Autenticar o cliente


Numa nova função, crie variáveis para o ponto final do seu recurso Azure e chave de subscrição.

[!INCLUDE [text-analytics-find-resource-information](../includes/find-azure-resource-info.md)]

Crie um novo objeto [BaseClient.](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#New) Passe a chave para o [encosto. Função NewCognitiveServicesAuthorizer()](https://godoc.org/github.com/Azure/go-autorest/autorest#NewCognitiveServicesAuthorizer) que será depois passada para a propriedade do `authorizer` cliente.

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

Criar uma nova função chamada `SentimentAnalysis()` e criar um cliente usando o método criado `GetTextAnalyticsClient()` anteriormente. Crie uma lista de objetos [MultiLanguageInput,](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput) contendo os documentos que pretende analisar. Cada objeto conterá um `id` `Language` atributo e um `text` atributo. O `text` atributo armazena o texto a ser analisado, `language` é a linguagem do documento, e pode ser qualquer `id` valor. 

Ligue para a função [sentimentais](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.Sentiment) do cliente e obtenha o resultado. Em seguida, iterar através dos resultados, e imprimir a identificação de cada documento, e pontuação de sentimento. Uma pontuação mais próxima de 0 indica um sentimento negativo, enquanto uma pontuação mais próxima de 1 indica um sentimento positivo.

[!code-go[Sentiment analysis sample](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=sentimentAnalysis)]

ligue `SentimentAnalysis()` para o seu projeto.

### <a name="output"></a>Saída

```console
Document ID: 1 , Sentiment Score: 0.87
Document ID: 2 , Sentiment Score: 0.11
Document ID: 3 , Sentiment Score: 0.44
Document ID: 4 , Sentiment Score: 1.00
```

## <a name="language-detection"></a>Deteção de idioma

Criar uma nova função chamada `LanguageDetection()` e criar um cliente usando o método criado `GetTextAnalyticsClient()` anteriormente. Crie uma lista de objetos [LanguageInput,](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#LanguageInput) contendo os documentos que pretende analisar. Cada objeto conterá um `id` `text` atributo. O `text` atributo armazena o texto a ser analisado, e o `id` pode ser qualquer valor. 

Ligue para o [DetectLanguage](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.DetectLanguage) do cliente e obtenha o resultado. Em seguida, iterar através dos resultados, e imprimir a identificação de cada documento, e linguagem detetada.

[!code-go[Language detection sample](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=languageDetection)]

Ligue `LanguageDetection()` para o seu projeto.

### <a name="output"></a>Saída

```console
Document ID: 0 , Language: English 
Document ID: 1 , Language: Spanish
Document ID: 2 , Language: Chinese_Simplified
```

## <a name="entity-recognition"></a>Reconhecimento de entidades

Criar uma nova função chamada `ExtractEntities()` e criar um cliente usando o método criado `GetTextAnalyticsClient()` anteriormente. Crie uma lista de objetos [MultiLanguageInput,](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput) contendo os documentos que pretende analisar. Cada objeto conterá um `id` `language` , e um `text` atributo. O `text` atributo armazena o texto a ser analisado, `language` é a linguagem do documento, e pode ser qualquer `id` valor. 

Ligue para as [Entidades](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.Entities) do Cliente e obtenha o resultado. Em seguida, iterar através dos resultados, e imprimir a identificação de cada documento, e entidades extraídas pontuam.

[!code-go[entity recognition sample](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=entityRecognition)]

ligue `ExtractEntities()` para o seu projeto.

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

Criar uma nova função chamada `ExtractKeyPhrases()` e criar um cliente usando o método criado `GetTextAnalyticsClient()` anteriormente. Crie uma lista de objetos [MultiLanguageInput,](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput) contendo os documentos que pretende analisar. Cada objeto conterá um `id` `language` , e um `text` atributo. O `text` atributo armazena o texto a ser analisado, `language` é a linguagem do documento, e pode ser qualquer `id` valor.

Ligue para as [Frases-Chave](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.KeyPhrases) do cliente e obtenha o resultado. Em seguida, iterar através dos resultados, e imprimir o ID de cada documento, e extraído frases-chave.

[!code-go[key phrase extraction sample](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=keyPhrases)]

Ligue `ExtractKeyPhrases()` para o seu projeto.

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
