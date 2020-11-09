---
title: Bing Autosuggest Go biblioteca cliente quickstart
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/06/2020
ms.author: aahi
ms.openlocfilehash: 8c0715b3570bf60205c83390ab93b272e49e8733
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2020
ms.locfileid: "94371617"
---
Começa com a biblioteca de clientes Bing Autosuggest para o Go. Siga estes passos para instalar a biblioteca e experimente os nossos exemplos para tarefas básicas.

Utilize a biblioteca de clientes Bing Autosuggest para obter sugestões de pesquisa com base em cadeias de consulta parciais.

[Documentação de referência](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/autosuggest)  |  [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics)  |  [Código de amostra](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/BingAutoSuggest/BingAutoSuggestQuickstart.go)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se ainda não tiver uma subscrição do Azure, [pode criar uma gratuitamente.](https://azure.microsoft.com/free/cognitive-services)
* A versão mais recente de [Go](https://golang.org/dl/).

Comece a utilizar a biblioteca de clientes Bing Autosuggest criando um recurso Azure. Escolha o tipo de recurso abaixo que é certo para si:

[!INCLUDE [cognitive-services-bing-autosuggest-signup-requirements](~/includes/cognitive-services-bing-autosuggest-signup-requirements.md)]

## <a name="create-environment-variables"></a>Criar variáveis de ambiente

>[!NOTE]
> Os pontos finais para recursos criados após 1 de julho de 2019 utilizam o formato de subdomínio personalizado apresentado abaixo. Para obter mais informações e uma lista completa de pontos finais regionais, consulte [os nomes de subdomínio personalizados para serviços cognitivos.](../../../cognitive-services-custom-subdomains.md)

Utilizando a sua chave e ponto final a partir do recurso que criou, crie duas variáveis ambientais para a autenticação:
<!-- replace the below variable names with the names expected in the code sample.-->
* `AUTOSUGGEST_SUBSCRIPTION_KEY`: A chave de recursos para autenticar os seus pedidos.
* `AUTOSUGGEST_ENDPOINT`: O ponto final do recurso para o envio de pedidos de API. Devia ser assim: `https://<your-custom-subdomain>.api.cognitive.microsoft.com`

Utilize as instruções para o seu sistema operativo.
<!-- replace the below endpoint and key examples -->
### <a name="windows"></a>[Windows](#tab/windows)

```console
setx BING_AUTOSUGGEST_SUBSCRIPTION_KEY <replace-with-your-autosuggest-api-key>
setx BING_AUTOSUGGEST_ENDPOINT <replace-with-your-autosuggest-api-endpoint>
```

Depois de adicionar a variável ambiente, reinicie a janela da consola.

### <a name="linux"></a>[Linux](#tab/linux)

```bash
export AUTOSUGGEST_SUBSCRIPTION_KEY=<replace-with-your-autosuggest-api-key>
export AUTOSUGGEST_ENDPOINT=<replace-with-your-autosuggest-api-endpoint>
```

Depois de adicionar a variável de ambiente, execute `source ~/.bashrc` a partir da janela da consola para que as alterações entrem em vigor.

### <a name="macos"></a>[macOS](#tab/unix)

Edite a sua `.bash_profile` variável e adicione a variável ambiental:

```bash
export AUTOSUGGEST_SUBSCRIPTION_KEY=<replace-with-your-autosuggest-api-key>
export AUTOSUGGEST_ENDPOINT=<replace-with-your-autosuggest-api-endpoint>
```

Depois de adicionar a variável de ambiente, execute `source .bash_profile` a partir da janela da consola para que as alterações entrem em vigor.
***

## <a name="create-a-new-go-project"></a>Criar um novo projeto Go

Numa janela de consola (cmd, PowerShell, Terminal, Bash), crie um novo espaço de trabalho para o seu projeto Go e navegue até ele. O seu espaço de trabalho conterá três pastas:

* **src** : Este diretório contém código fonte e pacotes. Quaisquer pacotes instalados com o `go get` comando residirão aqui.
* **pkg** : Este diretório contém os objetos de embalagem Go compilados. Todos estes ficheiros têm uma `.a` extensão.
* **bin** : Este diretório contém os ficheiros binários executáveis que são criados quando executa `go install` .

> [!TIP]
> Saiba mais sobre a estrutura de um espaço de [trabalho Go.](https://golang.org/doc/code.html#Workspaces) Este guia inclui informações para a definição `$GOPATH` e `$GOROOT` .

Vamos criar um espaço de trabalho chamado `my-app` e os sub-directórios necessários `src` `pkg` para, e `bin` :

```
$ mkdir -p my-app/{src, bin, pkg}  
$ cd my-app
```

## <a name="install-the-client-library-for-go"></a>Instale a biblioteca do cliente para Go

Agora, vamos instalar a biblioteca de clientes para Go:

```bash
$ go get -u <library-location-or-url>
```

ou se utilizar o dep, dentro da sua corrida de repo:

```bash
$ dep ensure -add <library-location-or-url>
```

## <a name="create-your-go-application"></a>Crie a sua aplicação Go

Em seguida, vamos criar um ficheiro `src/sample-app.go` chamado:

```bash
$ cd src
$ touch sample-app.go
```

Abra `sample-app.go` , adicione o nome do pacote e, em seguida, importe as seguintes bibliotecas:

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

Criar uma função chamada `main` . Em seguida, crie variáveis ambientais para a sua chave Bing Autosuggest e ponto final:

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

Estas amostras de código mostram-lhe como completar tarefas básicas utilizando a biblioteca de clientes Bing Autosuggest para Go:

* [Autenticar o cliente](#authenticate-the-client)
* [Enviar um pedido de API](#send-an-api-request)

### <a name="authenticate-the-client"></a>Autenticar o cliente

> [!NOTE]
> Este quickstart pressupõe que [criou uma variável ambiental](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication)  para a sua chave autosusustrela Bing, nomeada , e uma para o seu ponto final nomeado `BING_AUTOSUGGEST_SUBSCRIPTION_KEY` `BING_AUTOSUGGEST_ENDPOINT` .

Na `main()` função, instantânea um cliente com o seu ponto final e chave.

```go
// Get the context, which is required by the SDK methods.
ctx := context.Background()

client := autosuggest.New()
// Set the subscription key on the client.
client.Authorizer = autorest.NewCognitiveServicesAuthorizer(subscription_key)
client.Endpoint = endpoint
```

### <a name="send-an-api-request"></a>Enviar um pedido de API

No mesmo método, utilize o método [AutoSuggestMethodAsync](/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.autosuggestclientextensions.autosuggestmethodasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Search_AutoSuggest_AutoSuggestClientExtensions_AutoSuggestMethodAsync_Microsoft_Azure_CognitiveServices_Search_AutoSuggest_IAutoSuggestClient_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_Collections_Generic_IList_System_String__System_Threading_CancellationToken_) do cliente para enviar uma consulta ao Bing. Em seguida, iterate sobre a resposta [sugestões,](/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.models.suggestions?view=azure-dotnet) e imprima a primeira sugestão.

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

Execute a sua aplicação Go com o `go run [arguments]` comando do seu diretório de candidaturas.

```Go
go run sample-app.go
```

## <a name="clean-up-resources"></a>Limpar recursos

Se pretender limpar e remover uma subscrição dos Serviços Cognitivos, pode eliminar o grupo de recursos ou recursos. A eliminação do grupo de recursos também elimina quaisquer outros recursos que lhe sejam associados.

* [Eliminar um grupo de recursos no portal Azure](../../../cognitive-services-apis-create-account.md#clean-up-resources).
* [Eliminar um grupo de recursos no Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial da Sugestão Automática do Bing](../../tutorials/autosuggest.md)

## <a name="see-also"></a>Ver também

- [O que é a Sugestão Automática do Bing?](../../get-suggested-search-terms.md)
- [Referência da API de Sugestão Automática do Bing v7](/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference)