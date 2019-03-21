---
title: 'Início rápido: Traduzir texto, ouça - API de texto do tradutor'
titleSuffix: Azure Cognitive Services
description: Neste início rápido, irá traduzir texto de um idioma para outro usando a API de texto do Translator com Go em menos de 10 minutos.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 02/21/2019
ms.author: erhopf
ms.openlocfilehash: f0690d2e95084712c4260ade9df493ee078979a0
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57863734"
---
# <a name="quickstart-use-the-translator-text-api-to-translate-a-string-using-go"></a>Início rápido: Utilize a API de texto do Translator para traduzir uma cadeia de caracteres com Go

Neste início rápido, irá aprender como converter uma cadeia de texto de inglês, italiano e alemão usando o Go e a API de REST de texto do Translator.

Este início rápido requer uma [conta dos Serviços Cognitivos do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com um recurso de Tradução de Texto. Se não tiver uma conta, pode utilizar a [avaliação gratuita](https://azure.microsoft.com/try/cognitive-services/) para obter uma chave de subscrição.

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido requer:

* [Go](https://golang.org/doc/install)
* Uma chave de subscrição do Azure para Tradução de Texto

## <a name="create-a-project-and-import-required-modules"></a>Criar um projeto e importar os módulos exigidos

Crie um novo projeto de Go usando o seu IDE ou editor favorito. Em seguida, copie este fragmento de código para o seu projeto num ficheiro com o nome `translate-text.go`.

```go
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "log"
    "net/http"
    "net/url"
    "os"
)
```

## <a name="create-the-main-function"></a>Criar a função principal

Este exemplo irá tentar ler a chave de subscrição de Tradução de Texto a partir da variável de ambiente `TRANSLATOR_TEXT_KEY`. Se não estiver familiarizado com variáveis de ambiente, pode definir `subscriptionKey` como cadeia e comentar a instrução condicional.

Copie este código para o seu projeto:

```go
func main() {
    /*
     * Read your subscription key from an env variable.
     * Please note: You can replace this code block with
     * var subscriptionKey = "YOUR_SUBSCRIPTION_KEY" if you don't
     * want to use env variables. If so, be sure to delete the "os" import.
     */
    subscriptionKey := os.Getenv("TRANSLATOR_TEXT_KEY")
    if subscriptionKey == "" {
       log.Fatal("Environment variable TRANSLATOR_TEXT_KEY is not set.")
    }
    /*
     * This calls our translate function, which we'll
     * create in the next section. It takes a single argument,
     * the subscription key.
     */
    translate(subscriptionKey)
}
```

## <a name="create-a-function-to-translate-text"></a>Criar uma função para traduzir texto

Vamos criar uma função para traduzir texto. Esta função irá usar um único argumento, a chave de subscrição de texto do Translator.

```go
func translate(subscriptionKey string) {
    /*  
     * In the next few sections, we'll add code to this
     * function to make a request and handle the response.
     */
}
```

Em seguida, vamos construir o URL. O URL é criado com o `Parse()` e `Query()` métodos. Observará que os parâmetros são adicionados com o `Add()` método. Neste exemplo, estiver traduzir inglês para o alemão e italiano: `de` e `it`.

Copie este código para o `translate` função.

```go
// Build the request URL. See: https://golang.org/pkg/net/url/#example_URL_Parse
u, _ := url.Parse("https://api.cognitive.microsofttranslator.com/translate?api-version=3.0")
q := u.Query()
q.Add("to", "de")
q.Add("to", "it")
u.RawQuery = q.Encode()
```

>[!NOTE]
> Para obter mais informações sobre pontos de extremidade, rotas e parâmetros do pedido, consulte [3.0 de API de texto de tradutor: Traduzir](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate).

## <a name="create-a-struct-for-your-request-body"></a>Criar uma estrutura para o corpo do pedido

Em seguida, criar uma estrutura anônima para o corpo do pedido e codificá-lo como JSON com `json.Marshal()`. Adicione este código para o `translate` função.

```go
// Create an anonymous struct for your request body and encode it to JSON
body := []struct {
    Text string
}{
    {Text: "Hello, world!"},
}
b, _ := json.Marshal(body)
```

## <a name="build-the-request"></a>Criar o pedido

Agora que já codificado o corpo do pedido como JSON, pode criar o seu pedido POST e chamar a API de texto do Translator.

```go
// Build the HTTP POST request
req, err := http.NewRequest("POST", u.String(), bytes.NewBuffer(b))
if err != nil {
    log.Fatal(err)
}
// Add required headers to the request
req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)
req.Header.Add("Content-Type", "application/json")

// Call the Translator Text API
res, err := http.DefaultClient.Do(req)
if err != nil {
    log.Fatal(err)
}
```

## <a name="handle-and-print-the-response"></a>Processar e a resposta de impressão

Adicione este código para o `translate` função para descodificar a resposta JSON e, em seguida, formate e imprima o resultado.

```go
// Decode the JSON response
var result interface{}
if err := json.NewDecoder(res.Body).Decode(&result); err != nil {
    log.Fatal(err)
}
// Format and print the response to terminal
prettyJSON, _ := json.MarshalIndent(result, "", "  ")
fmt.Printf("%s\n", prettyJSON)
```

## <a name="put-it-all-together"></a>Juntar tudo

Já está. Utilizámos um programa simples que irá chamar a API de Texto do Microsoft Translator e devolver uma resposta JSON. Agora, é altura de executar o seu programa:

```console
go run translate-text.go
```

Se quiser comparar o seu código com o nosso, o exemplo completo está disponível no [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Go).

## <a name="sample-response"></a>Resposta de amostra

É devolvida uma resposta com êxito em JSON, tal como apresentado no exemplo seguinte:

```json
[
  {
    "detectedLanguage": {
      "language": "en",
      "score": 1.0
    },
    "translations": [
      {
        "text": "Hallo Welt!",
        "to": "de"
      },
      {
        "text": "Salve, mondo!",
        "to": "it"
      }
    ]
  }
]
```

## <a name="next-steps"></a>Passos Seguintes

Explore exemplos de Go para APIs serviços cognitivos do [Azure SDK para Go](https://github.com/Azure/azure-sdk-for-go) no GitHub.

> [!div class="nextstepaction"]
> [Explore exemplos de Go no GitHub](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices)

## <a name="see-also"></a>Consulte também

Saiba como utilizar a API de texto do Translator para:

* [Transliterar texto](quickstart-go-transliterate.md)
* [Identificar o idioma por entrada](quickstart-go-detect.md)
* [Obter traduções alternativas](quickstart-go-dictionary.md)
* [Obter uma lista de idiomas suportados](quickstart-go-languages.md)
* [Determinar o comprimento das frases a partir de uma entrada](quickstart-go-sentences.md)
