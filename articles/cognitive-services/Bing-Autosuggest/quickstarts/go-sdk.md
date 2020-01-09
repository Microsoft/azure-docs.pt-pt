---
title: 'Início rápido: biblioteca de cliente do Sugestão Automática do Bing para o go | Microsoft Docs'
description: Comece a usar a biblioteca de cliente do Sugestão Automática do Bing para o Go e obtenha sugestões de pesquisa com base em cadeias de caracteres de consulta parciais.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: quickstart
ms.date: 12/18/2019
ms.author: aahi
ms.openlocfilehash: e826d69bbf0f94d02799e9b93b26db8d4cbbba54
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75474089"
---
# <a name="quickstart-bing-autosuggest-client-library-for-go"></a>Início rápido: biblioteca de cliente do Sugestão Automática do Bing para o go

Introdução à biblioteca de cliente do Sugestão Automática do Bing para o go. Siga estas etapas para instalar a biblioteca e experimentar nossos exemplos para tarefas básicas. 

Use a biblioteca de cliente Sugestão Automática do Bing para obter sugestões de pesquisa com base em cadeias de caracteres de consulta parciais.

[Documentação de referência](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/autosuggest) | [código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics) | código de [exemplo](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/BingAutoSuggest/BingAutoSuggestQuickstart.go)

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure- [crie uma gratuitamente](https://azure.microsoft.com/free/)
* A versão mais recente do [go](https://golang.org/dl/)

## <a name="setting-up"></a>Configurando

### <a name="create-an-azure-resource"></a>Criar um recurso do Azure 

Comece a usar a biblioteca de cliente Sugestão Automática do Bing criando um recurso do Azure. Escolha o tipo de recurso abaixo, ideal para você:

[!INCLUDE [cognitive-services-bing-autosuggest-signup-requirements](../../../../includes/cognitive-services-bing-autosuggest-signup-requirements.md)]

### <a name="create-an-environment-variable"></a>Criar uma variável de ambiente

>[!NOTE]
> Os pontos de extremidade para recursos de não avaliação criados após 1º de julho de 2019 usam o formato de subdomínio personalizado mostrado abaixo. Para obter mais informações e uma lista completa de pontos de extremidade regionais, consulte [nomes de subdomínio personalizados para serviços cognitivas](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains). 

Usando a chave e o ponto de extremidade do recurso que você criou, crie duas variáveis de ambiente para autenticação:
<!-- replace the below variable names with the names expected in the code sample.-->
* `AUTOSUGGEST_SUBSCRIPTION_KEY`-a chave de recurso para autenticar suas solicitações.
* `AUTOSUGGEST_ENDPOINT`-o ponto de extremidade de recurso para enviar solicitações de API. Ele terá a seguinte aparência: 
  * `https://<your-custom-subdomain>.api.cognitive.microsoft.com` 

Use as instruções para seu sistema operacional.
<!-- replace the below endpoint and key examples -->
#### <a name="windowstabwindows"></a>[Windows](#tab/windows)

```console
setx BING_AUTOSUGGEST_SUBSCRIPTION_KEY <replace-with-your-autosuggest-api-key>
setx BING_AUTOSUGGEST_ENDPOINT <replace-with-your-autosuggest-api-endpoint>
```

Depois de adicionar a variável de ambiente, reinicie a janela do console.

#### <a name="linuxtablinux"></a>[Linux](#tab/linux)

```bash
export AUTOSUGGEST_SUBSCRIPTION_KEY=<replace-with-your-autosuggest-api-key>
export AUTOSUGGEST_ENDPOINT=<replace-with-your-autosuggest-api-endpoint>
```

Depois de adicionar a variável de ambiente, execute `source ~/.bashrc` a partir da janela da consola para que as alterações entrem em vigor.

#### <a name="macostabunix"></a>[macOS](#tab/unix)

Edite seu `.bash_profile`e adicione a variável de ambiente:

```bash
export AUTOSUGGEST_SUBSCRIPTION_KEY=<replace-with-your-autosuggest-api-key>
export AUTOSUGGEST_ENDPOINT=<replace-with-your-autosuggest-api-endpoint>
```

Depois de adicionar a variável de ambiente, execute `source .bash_profile` a partir da janela da consola para que as alterações entrem em vigor.
***

### <a name="create-a-new-go-project"></a>Criar um novo projeto Go

Em uma janela de console (cmd, PowerShell, terminal, bash), crie um novo espaço de trabalho para seu projeto Go e navegue até ele. Seu espaço de trabalho conterá três pastas: 

* **src** -esse diretório contém o código-fonte e os pacotes. Todos os pacotes instalados com o comando `go get` residirão aqui.
* **pkg** -esse diretório contém os objetos de pacote go compilados. Todos esses arquivos têm uma extensão `.a`.
* **bin** -esse diretório contém os arquivos executáveis binários que são criados quando você executa o `go install`.

> [!TIP]
> Saiba mais sobre a estrutura de um [espaço de trabalho go](https://golang.org/doc/code.html#Workspaces). Este guia inclui informações para definir `$GOPATH` e `$GOROOT`.

Vamos criar um espaço de trabalho chamado `my-app` e os subdiretórios necessários para `src`, `pkg`e `bin`:

```
$ mkdir -p my-app/{src, bin, pkg}  
$ cd my-app
```

### <a name="install-the-client-library-for-go"></a>Instalar a biblioteca de cliente para o go

Agora, vamos instalar a biblioteca de cliente para o Go: 

```bash
$ go get -u <library-location-or-url>
```

ou, se você usar o Dep, na execução do seu repositório:

```bash
$ dep ensure -add <library-location-or-url>
```

### <a name="create-your-go-application"></a>Criar seu aplicativo go

Em seguida, vamos criar um arquivo chamado `src/sample-app.go`:

```bash
$ cd src
$ touch sample-app.go
```

Abra `sample-app.go` e adicione o nome do pacote e importe as seguintes bibliotecas:

```Go
package main

import (
    "context"
    "fmt"
    "github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/autosuggest"
    "github.com/Azure/go-autorest/autorest"
    "log"
    "os"
)
```

Crie uma função chamada `main`. Em seguida, crie variáveis de ambiente para sua chave de Sugestão Automática do Bing e ponto de extremidade.

```go
func main() {
    // Add your Bing Autosuggest subscription key to your environment variables.
    if "" == os.Getenv("BING_AUTOSUGGEST_SUBSCRIPTION_KEY") {
        log.Fatal("Please set/export the environment variable BING_AUTOSUGGEST_SUBSCRIPTION_KEY.")
    }
    // Add your Bing Autosuggest endpoint to your environment variables.
    var subscription_key string = os.Getenv("BING_AUTOSUGGEST_ENDPOINT")
    if "" == os.Getenv("BING_AUTOSUGGEST_ENDPOINT") {
        log.Fatal("Please set/export the environment variable BING_AUTOSUGGEST_ENDPOINT.")
    }
    var endpoint string = os.Getenv("BING_AUTOSUGGEST_ENDPOINT")
}
```

## <a name="code-examples"></a>Exemplos de código

Estes exemplos de código mostram como concluir tarefas básicas usando a biblioteca de cliente do Sugestão Automática do Bing para o Go:

* [Autenticar o cliente](#authenticate-the-client)
* [Enviar uma solicitação de API](#send-an-api-request)

## <a name="authenticate-the-client"></a>Autenticar o cliente

> [!NOTE] 
> Este início rápido pressupõe que você [criou uma variável de ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para sua chave de sugestão automática do Bing, chamada `BING_AUTOSUGGEST_SUBSCRIPTION_KEY`e outra para o ponto de extremidade chamado `BING_AUTOSUGGEST_ENDPOINT`.

Na função `main()`, crie uma instância de um cliente com o ponto de extremidade e a chave. 

```go
// Get the context, which is required by the SDK methods.
ctx := context.Background()

client := autosuggest.New()
// Set the subscription key on the client.
client.Authorizer = autorest.NewCognitiveServicesAuthorizer(subscription_key)
client.Endpoint = endpoint
```

## <a name="send-an-api-request"></a>Enviar uma solicitação de API

No mesmo método, use o método [AutoSuggestMethodAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.autosuggestclientextensions.autosuggestmethodasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Search_AutoSuggest_AutoSuggestClientExtensions_AutoSuggestMethodAsync_Microsoft_Azure_CognitiveServices_Search_AutoSuggest_IAutoSuggestClient_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_Collections_Generic_IList_System_String__System_Threading_CancellationToken_) do cliente para enviar uma consulta ao Bing. Em seguida, itere sobre a resposta de [sugestões](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.models.suggestions?view=azure-dotnet) e imprima a primeira sugestão.

```Go
// This should return the query suggestion "xbox."
result, err := client.AutoSuggest (ctx, "xb", "", "", "", "", "", "", "", "", "", "", []autosuggest.ResponseFormat{"Json"})
if nil != err {
    log.Fatal(err)
}

groups := *result.SuggestionGroups
if len(groups) > 0 {
    group, _ := groups[0].AsSuggestionsSuggestionGroup()
    fmt.Printf ("First suggestion group: %s\n", (*group).Name)
    suggestions := *(*group).SearchSuggestions
    if len(suggestions) > 0 {
        fmt.Println("First suggestion:")
        fmt.Printf("Query: %s\n", *suggestions[0].Query)
        fmt.Printf("Display text: %s\n", *suggestions[0].DisplayText)
    } else {
        fmt.Println("No suggestions found in this group.")
    }
} else {
    fmt.Println("No suggestions found.")
}
```

## <a name="run-the-application"></a>Executar a aplicação

Execute o aplicativo Go com o comando `go run [arguments]` no diretório do aplicativo.

```Go
go run sample-app.go
```

## <a name="clean-up-resources"></a>Limpar recursos

Se você quiser limpar e remover uma assinatura de serviços cognitivas, poderá excluir o recurso ou grupo de recursos. Excluir o grupo de recursos também exclui todos os outros recursos associados a ele.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial da Sugestão Automática do Bing](../tutorials/autosuggest.md)

## <a name="see-also"></a>Ver também

- [O que é a Sugestão Automática do Bing?](../get-suggested-search-terms.md)
- [Referência da API de Sugestão Automática do Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference)
