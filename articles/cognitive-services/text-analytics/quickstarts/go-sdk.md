---
title: 'Início rápido: Análise de Texto a biblioteca de cliente para go | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Obtenha informações e exemplos de código para ajudá-lo a começar rapidamente a usar o API de Análise de Texto nos serviços cognitivas do Azure.
services: cognitive-services
author: laramume
manager: assafi
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 08/28/2019
ms.author: aahi
ms.openlocfilehash: 1ba2ec6b5c0c59be7b7264f7558fbb393adcc2d8
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70142795"
---
# <a name="quickstart-text-analytics-client-library-for-go"></a>Início rápido: Biblioteca de cliente de análise de texto para go

Introdução à biblioteca de cliente do Análise de Texto para o go. Siga estas etapas para instalar o pacote e experimentar o código de exemplo para tarefas básicas. 

Use a biblioteca de cliente do Análise de Texto para o Go para executar:

* Análise de sentimentos
* Deteção de idioma
* Reconhecimento de entidade
* Extração de expressões chave

[](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/textanalytics?view=azure-python) | [Exemplos](https://github.com/Azure-Samples/cognitive-services-quickstart-code) [do GitHub (biblioteca de](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics) | [código](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-textanalytics) | -fonte de documentação) de referência

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure- [crie uma gratuitamente](https://azure.microsoft.com/free/)
* A versão mais recente do [go](https://golang.org/dl/)

## <a name="setting-up"></a>Configurando

### <a name="create-a-text-analytics-azure-resource"></a>Criar um Análise de Texto recurso do Azure 

Os serviços cognitivas do Azure são representados pelos recursos do Azure que você assina. Crie um recurso para Análise de Texto usando o [portal do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) ou [CLI do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) no computador local. Também pode:

* Obtenha uma [chave de avaliação](https://azure.microsoft.com/try/cognitive-services/#decision) válida por 7 dias gratuitamente. Depois de inscrever-se, ele estará disponível no [site do Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).
* Exiba seu recurso no [portal do Azure](https://portal.azure.com).

Depois de obter uma chave de sua assinatura ou recurso de avaliação, [crie uma variável de ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para a chave `TEXT_ANALYTICS_SUBSCRIPTION_KEY`, denominada.

### <a name="create-a-new-go-project"></a>Criar um novo projeto Go

Em uma janela de console (cmd, PowerShell, terminal, bash), crie um novo espaço de trabalho para seu projeto Go e navegue até ele. Seu espaço de trabalho conterá três pastas: 

* **src** -esse diretório contém o código-fonte e os pacotes. Todos os pacotes instalados com `go get` o comando residirão aqui.
* **pkg** -esse diretório contém os objetos de pacote go compilados. Todos esses arquivos têm uma `.a` extensão.
* **bin** -esse diretório contém os arquivos executáveis binários que são criados quando `go install`você executa o.

> [!TIP]
> Saiba mais sobre a estrutura de um [espaço de trabalho go](https://golang.org/doc/code.html#Workspaces). Este guia inclui informações para configuração `$GOPATH` e `$GOROOT`.

Crie um espaço de `my-app` trabalho chamado e os subdiretórios `pkg`necessários para `bin` `src`, e:

```console
$ mkdir -p my-app/{src, bin, pkg}  
$ cd my-app
```

### <a name="install-the-text-analytics-client-library-for-go"></a>Instalar a biblioteca de cliente do Análise de Texto para o go

Instale a biblioteca de cliente para o Go: 

```console
$ go get -u <https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics>
```

ou, se você usar o Dep, na execução do seu repositório:

```console
$ dep ensure -add <https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics>
```

### <a name="create-your-go-application"></a>Criar seu aplicativo go

Em seguida, crie um arquivo `src/quickstart.go`chamado:

```bash
$ cd src
$ touch quickstart.go
```

Abra `quickstart.go` em seu IDE ou editor de texto favorito. Em seguida, adicione o nome do pacote e importe as seguintes bibliotecas:

```golang
import (
    "context"
    "encoding/json"
    "fmt"
    "github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics"
    "github.com/Azure/go-autorest/autorest"
    "log"
    "os"    
)
```

Adicione as seguintes funções ao seu projeto, pois a maioria dos parâmetros e propriedades para este início rápido esperam cadeia de caracteres e ponteiros bool.

```golang
// returns a pointer to the string value passed in.
func StringPointer(v string) *string {
    return &v
}

// returns a pointer to the bool value passed in.
func BoolPointer(v bool) *bool {
    return &v
}
```

Na função do `main` seu aplicativo, crie variáveis para o ponto de extremidade do Azure do recurso e a chave de assinatura. Obtenha esses valores das variáveis de ambiente TEXT_ANALYTICS_SUBSCRIPTION_KEY e TEXT_ANALYTICS_ENDPOINT. Se você criou essas variáveis de ambiente depois de começar a editar o aplicativo, será necessário fechar e reabrir o editor, IDE ou shell que você está usando para acessar as variáveis.

[!INCLUDE [text-analytics-find-resource-information](../includes/find-azure-resource-info.md)]

```golang
var subscriptionKeyVar string = "TEXT_ANALYTICS_SUBSCRIPTION_KEY"
if "" == os.Getenv(subscriptionKeyVar) {
    log.Fatal("Please set/export the environment variable " + subscriptionKeyVar + ".")
}
var subscriptionKey string = os.Getenv(subscriptionKeyVar)
var endpointVar string = "TEXT_ANALYTICS_ENDPOINT"
if "" == os.Getenv(endpointVar) {
    log.Fatal("Please set/export the environment variable " + endpointVar + ".")
}
var endpoint string = os.Getenv(endpointVar)
```

## <a name="object-model"></a>Modelo de objeto 

O Análise de Texto Client é um objeto [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#New) que se autentica no Azure usando sua chave. O cliente fornece vários métodos para analisar o texto, como uma única cadeia de caracteres ou um lote. 

O texto é enviado para a API como uma lista `documents`de, que `dictionary` são objetos que contêm uma `id`combinação `text`de atributos `language` , e, dependendo do método usado. O `text` atributo armazena o texto a ser analisado na origem `language`e `id` pode ser qualquer valor. 

O objeto de resposta é uma lista que contém as informações de análise de cada documento. 

## <a name="code-examples"></a>Exemplos de código

Esses trechos de código mostram como fazer o seguinte com a biblioteca de cliente Análise de Texto para Python:

* [Autenticar o cliente](#authenticate-the-client)
* [Análise de sentimentos](#sentiment-analysis)
* [Detecção de idioma](#language-detection)
* [Reconhecimento de entidade](#entity-recognition)
* [Extração de frases-chave](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Autenticar o cliente

Na função principal do seu projeto, crie um novo objeto [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#New) . Passe sua chave para o [REST. A função NewCognitiveServicesAuthorizer ()](https://godoc.org/github.com/Azure/go-autorest/autorest#NewCognitiveServicesAuthorizer) , que será passada para a propriedade do `authorizer` cliente.

```golang
textAnalyticsClient := textanalytics.New(endpoint)
textAnalyticsClient.Authorizer = autorest.NewCognitiveServicesAuthorizer(subscriptionKey)
```

## <a name="sentiment-analysis"></a>Análise de sentimentos

Crie uma nova função chamada `SentimentAnalysis()` que usa o cliente criado anteriormente. Crie uma lista de objetos [MultiLanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput) , contendo os documentos que você deseja analisar. Cada objeto conterá um `id` `Language` e um `text` atributo. O `text` atributo armazena o texto a ser analisado `language` , é o idioma do documento e `id` pode ser qualquer valor. 

```golang
func SentimentAnalysis(textAnalyticsclient textanalytics.BaseClient) {

    ctx := context.Background()
    inputDocuments := []textanalytics.MultiLanguageInput {
        textanalytics.MultiLanguageInput {
            Language: StringPointer("en"),
            ID:StringPointer("0"),
            Text:StringPointer("I had the best day of my life."),
        },
    }

    batchInput := textanalytics.MultiLanguageBatchInput{Documents:&inputDocuments}
}
```

Na mesma função, chame a função de [sentimentos ()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.Sentiment) do cliente e obtenha o resultado. Em seguida, itere pelos resultados e imprima a ID de cada documento e a pontuação de sentimentos. Uma pontuação mais próxima de 0 indica uma observação negativa, enquanto uma pontuação mais próxima de 1 indica uma observação positiva.

```golang
result, err := textAnalyticsclient.Sentiment(ctx, BoolPointer(false), &batchInput)
if err != nil { log.Fatal(err) }

batchResult := textanalytics.SentimentBatchResult{}
jsonString, _ := json.Marshal(result.Value)
json.Unmarshal(jsonString, &batchResult)

// Printing sentiment results
for _,document := range *batchResult.Documents {
    fmt.Printf("Document ID: %s\n", *document.ID)
    fmt.Printf("Sentiment Score: %f\n",*document.Score)
}

// Printing document errors
fmt.Println("Document Errors:")
for _,error := range *batchResult.Errors {
    fmt.Printf("Document ID: %s Message : %s\n" ,*error.ID, *error.Message)
}
fmt.Println()
```

Na função principal do seu projeto, chame `SentimentAnalysis()`.

### <a name="output"></a>Output

```console
Document ID: 1 , Sentiment Score: 0.87
```

## <a name="language-detection"></a>Deteção de idioma

Crie uma nova função chamada `LanguageDetection()` que usa o cliente criado anteriormente. Crie uma lista de objetos [LanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#LanguageInput) , contendo os documentos que você deseja analisar. Cada objeto conterá um `id` atributo e `text` um. O `text` atributo armazena o texto a ser analisado `id` e pode ser qualquer valor. 

```golang
func LanguageDetection(textAnalyticsclient textanalytics.BaseClient) {

    ctx := context.Background()
    inputDocuments := []textanalytics.LanguageInput {
        textanalytics.LanguageInput {
            ID:StringPointer("0"),
            Text:StringPointer("This is a document written in English."),
        },
    }

    batchInput := textanalytics.LanguageBatchInput{Documents:&inputDocuments}
}
```

Na mesma função, chame o DetectLanguage do cliente [()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.DetectLanguage) e obtenha o resultado. Em seguida, itere os resultados e imprima a ID de cada documento e a linguagem detectada.

```golang
result, err := textAnalyticsclient.DetectLanguage(ctx, BoolPointer(false), &batchInput)
if err != nil { log.Fatal(err) }

// Printing language detection results
for _,document := range *result.Documents {
    fmt.Printf("Document ID: %s " , *document.ID)
    fmt.Printf("Detected Languages with Score: ")
    for _,language := range *document.DetectedLanguages{
        fmt.Printf("%s %f,",*language.Name, *language.Score)
    }
    fmt.Println()
}

// Printing document errors
fmt.Println("Document Errors:")
for _,error := range *result.Errors {
    fmt.Printf("Document ID: %s Message : %s\n" ,*error.ID, *error.Message)
}
fmt.Println()
```

Na função principal do seu projeto, chame `LanguageDetection()`.

### <a name="output"></a>Output

```console
Document ID: 0 Detected Languages with Score: English 1.000000
```

## <a name="entity-recognition"></a>Reconhecimento de entidade

Crie uma nova função chamada `ExtractEntities()` que usa o cliente criado anteriormente. Crie uma lista de objetos [MultiLanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput) , contendo os documentos que você deseja analisar. Cada objeto conterá um `id`, `language`e um `text` atributo. O `text` atributo armazena o texto a ser analisado `language` , é o idioma do documento e `id` pode ser qualquer valor. 

```golang
func ExtractEntities(textAnalyticsclient textanalytics.BaseClient) {

    ctx := context.Background()
    inputDocuments := []textanalytics.MultiLanguageInput {
        textanalytics.MultiLanguageInput {
            Language: StringPointer("en"),
            ID:StringPointer("0"),
            Text:StringPointer("Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800."),
        },
    }

    batchInput := textanalytics.MultiLanguageBatchInput{Documents:&inputDocuments}
}
```

Na mesma função, chame as entidades do cliente [()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.Entities) e obtenha o resultado. Em seguida, itere pelos resultados e imprima a ID de cada documento e a pontuação de entidades extraídas.

```golang
    result, err := textAnalyticsclient.Entities(ctx, BoolPointer(false), &batchInput)
    if err != nil { log.Fatal(err) }

    // Printing extracted entities results
    for _,document := range *result.Documents {
        fmt.Printf("Document ID: %s\n" , *document.ID)
        fmt.Printf("\tExtracted Entities:\n")
        for _,entity := range *document.Entities{
            fmt.Printf("\t\tName: %s\tType: %s",*entity.Name, *entity.Type)
            if entity.SubType != nil{
                fmt.Printf("\tSub-Type: %s\n", *entity.SubType)
            }
            fmt.Println()
            for _,match := range *entity.Matches{
                fmt.Printf("\t\t\tOffset: %v\tLength: %v\tScore: %f\n", *match.Offset, *match.Length, *match.EntityTypeScore)
            }
        }
        fmt.Println()
    }

    // Printing document errors
    fmt.Println("Document Errors:")
    for _,error := range *result.Errors {
        fmt.Printf("Document ID: %s Message : %s\n" ,*error.ID, *error.Message)
    }
    fmt.Println()
```

Na função principal do seu projeto, chame `ExtractEntities()`.

### <a name="output"></a>Output

```console
Document ID: 0
    Extracted Entities:
        Name: Microsoft Type: Organization
            Offset: 0   Length: 9   Score: 1.000000
        Name: Bill Gates    Type: Person
            Offset: 25  Length: 10  Score: 0.999847
        Name: Paul Allen    Type: Person
            Offset: 40  Length: 10  Score: 0.998841
        Name: April 4   Type: Other
            Offset: 54  Length: 7   Score: 0.800000
        Name: April 4, 1975 Type: DateTime  Sub-Type: Date

            Offset: 54  Length: 13  Score: 0.800000
        Name: BASIC Type: Other
            Offset: 89  Length: 5   Score: 0.800000
        Name: Altair 8800   Type: Other
            Offset: 116 Length: 11  Score: 0.800000
```

## <a name="key-phrase-extraction"></a>Extração de expressões chave

Crie uma nova função chamada `ExtractKeyPhrases()` que usa o cliente criado anteriormente. Crie uma lista de objetos [MultiLanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput) , contendo os documentos que você deseja analisar. Cada objeto conterá um `id`, `language`e um `text` atributo. O `text` atributo armazena o texto a ser analisado `language` , é o idioma do documento e `id` pode ser qualquer valor.

```golang
func ExtractKeyPhrases(textAnalyticsclient textanalytics.BaseClient) {

    ctx := context.Background()
    inputDocuments := []textanalytics.MultiLanguageInput {
        textanalytics.MultiLanguageInput {
            Language: StringPointer("en"),
            ID:StringPointer("0"),
            Text:StringPointer("My cat might need to see a veterinarian."),
        },
    }

    batchInput := textanalytics.MultiLanguageBatchInput{Documents:&inputDocuments}
}
```

Na mesma função, chame as prefrases do cliente [()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.KeyPhrases) e obtenha o resultado. Em seguida, itere pelos resultados e imprima a ID de cada documento e as frases-chave extraídas.

```golang
    result, err := textAnalyticsclient.KeyPhrases(ctx, BoolPointer(false), &batchInput)
    if err != nil { log.Fatal(err) }

    // Printing extracted key phrases results
    for _,document := range *result.Documents {
        fmt.Printf("Document ID: %s\n" , *document.ID)
        fmt.Printf("\tExtracted Key Phrases:\n")
        for _,keyPhrase := range *document.KeyPhrases{
            fmt.Printf("\t\t%s\n",keyPhrase)
        }
        fmt.Println()
    }

    // Printing document errors
    fmt.Println("Document Errors:")
    for _,error := range *result.Errors {
        fmt.Printf("Document ID: %s Message : %s\n" ,*error.ID, *error.Message)
    }
    fmt.Println()
```

Na função principal do seu projeto, chame `ExtractKeyPhrases()`.

### <a name="output"></a>Output

```console
Document ID: 0
    Extracted Key Phrases:
        cat
        veterinarian
```

## <a name="clean-up-resources"></a>Limpar recursos

Se você quiser limpar e remover uma assinatura de serviços cognitivas, poderá excluir o recurso ou grupo de recursos. Excluir o grupo de recursos também exclui todos os outros recursos associados ao grupo de recursos.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Passos Seguintes


> [!div class="nextstepaction"]
> [Análise de Texto com o Power BI](../tutorials/tutorial-power-bi-key-phrases.md)


* [Descrição Geral da Análise de Texto](../overview.md)
* [Análise de sentimentos](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Reconhecimento de entidade](../how-tos/text-analytics-how-to-entity-linking.md)
* [Detectar idioma](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [Reconhecimento de idioma](../how-tos/text-analytics-how-to-language-detection.md)
* O código-fonte para este exemplo pode ser encontrado no [GitHub](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/cognitiveservices).
