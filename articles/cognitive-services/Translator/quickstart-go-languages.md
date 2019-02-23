---
title: 'Início rápido: Obter suporte idiomas, ouça - API de texto do tradutor'
titleSuffix: Azure Cognitive Services
description: Neste guia de início rápido, obtém uma lista de idiomas com suporte para tradução, transliteração e pesquisa no dicionário e exemplos que utilizam a API de Texto do Microsoft Translator com o Go.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 02/21/2019
ms.author: erhopf
ms.openlocfilehash: 3bccf50d9236fb5b51b89f31ffc2ec9b0d2c74cd
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56727286"
---
# <a name="quickstart-use-the-translator-text-api-to-get-a-list-of-supported-languages-using-go"></a>Início rápido: Utilize a API de texto do Translator para obter uma lista de idiomas suportados através de Go

Neste início rápido, irá aprender como fazer um pedido GET que devolve uma lista de idiomas suportados através de Go e a API de REST de texto do Translator.

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido requer:

* [Go](https://golang.org/doc/install)

## <a name="create-a-project-and-import-required-modules"></a>Criar um projeto e importar os módulos exigidos

Crie um novo projeto de Go usando o seu IDE ou editor favorito. Em seguida, copie este fragmento de código para o seu projeto num ficheiro com o nome `get-languages.go`.

```go
package main

import (
    "encoding/json"
    "fmt"
    "log"
    "net/http"
    "net/url"
)
```

## <a name="create-the-main-function"></a>Criar a função principal

Vamos criar a função main de nosso aplicativo. Perceberá é uma única linha de código. Isso ocorre porque estamos a criar uma única função para obter e imprimir a lista de idiomas suportados para o texto do Translator.

Copie este código para o seu projeto:

```go
func main() {
    /*
     * This calls our getLanguages function, which we'll
     * create in the next section. It takes a single argument,
     * the subscription key.
     */
    getLanguages()
}
```

## <a name="create-a-function-to-get-a-list-of-supported-languages"></a>Criar uma função para obter uma lista dos idiomas suportados

Vamos criar uma função para obter uma lista de idiomas suportados.

```go
func getLanguages() {
    /*  
     * In the next few sections, we'll add code to this
     * function to make a request and handle the response.
     */
}
```

Em seguida, vamos construir o URL. O URL é criado com o `Parse()` e `Query()` métodos.

Copie este código para o `getLanguages` função.

```go
// Build the request URL. See: https://golang.org/pkg/net/url/#example_URL_Parse
u, _ := url.Parse("https://api.cognitive.microsofttranslator.com/languages")
q := u.Query()
q.Add("api-version", "3.0")
u.RawQuery = q.Encode()
```

>[!NOTE]
> Para obter mais informações sobre pontos de extremidade, rotas e parâmetros do pedido, consulte [3.0 de API de texto de tradutor: Idiomas](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages).

## <a name="build-the-request"></a>Criar o pedido

Agora que já codificado o corpo do pedido como JSON, pode criar o seu pedido POST e chamar a API de texto do Translator.

```go
// Build the HTTP GET request
req, err := http.NewRequest("GET", u.String(), nil)
if err != nil {
    log.Fatal(err)
}
// Add required headers
req.Header.Add("Content-Type", "application/json")

// Call the Translator Text API
res, err := http.DefaultClient.Do(req)
if err != nil {
    log.Fatal(err)
}
```

## <a name="handle-and-print-the-response"></a>Processar e a resposta de impressão

Adicione este código para o `getLanguages` função para descodificar a resposta JSON e, em seguida, formate e imprima o resultado.

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
go run get-languages.go
```

Se quiser comparar o seu código com o nosso, o exemplo completo está disponível no [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Go).

## <a name="sample-response"></a>Resposta de amostra

É devolvida uma resposta com êxito em JSON, tal como apresentado no exemplo seguinte:

```json
{
  "dictionary": {
    "af": {
      "name": "Afrikaans",
      "nativeName": "Afrikaans",
      "dir": "ltr",
      "translations": [
        {
          "name": "English",
          "nativeName": "English",
          "dir": "ltr",
          "code": "en"
        }
      ]
    },
    "ar": {
      "name": "Arabic",
      "nativeName": "العربية",
      "dir": "rtl",
      "translations": [
        {
          "name": "English",
          "nativeName": "English",
          "dir": "ltr",
          "code": "en"
        }
      ]
    },
...
  },
  "translation": {
    "af": {
      "name": "Afrikaans",
      "nativeName": "Afrikaans",
      "dir": "ltr"
    },
    "ar": {
      "name": "Arabic",
      "nativeName": "العربية",
      "dir": "rtl"
    },
...
  },
  "transliteration": {
    "ar": {
      "name": "Arabic",
      "nativeName": "العربية",
      "scripts": [
        {
          "code": "Arab",
          "name": "Arabic",
          "nativeName": "العربية",
          "dir": "rtl",
          "toScripts": [
            {
              "code": "Latn",
              "name": "Latin",
              "nativeName": "اللاتينية",
              "dir": "ltr"
            }
          ]
        },
        {
          "code": "Latn",
          "name": "Latin",
          "nativeName": "اللاتينية",
          "dir": "ltr",
          "toScripts": [
            {
              "code": "Arab",
              "name": "Arabic",
              "nativeName": "العربية",
              "dir": "rtl"
            }
          ]
        }
      ]
    },
...
  }
}
```

## <a name="next-steps"></a>Passos Seguintes

Explore os pacotes Go para APIs Serviços Cognitivos a partir do [SDK do Azure para Go](https://github.com/Azure/azure-sdk-for-go) no GitHub.

> [!div class="nextstepaction"]
> [Explorar os pacotes Go no GitHub](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices)

## <a name="see-also"></a>Consulte também

Saiba como utilizar a API de texto do Translator para:

* [Traduzir texto](quickstart-go-translate.md)
* [Transliterar texto](quickstart-go-transliterate.md)
* [Identificar o idioma por entrada](quickstart-go-detect.md)
* [Obter traduções alternativas](quickstart-go-dictionary.md)
* [Determinar o comprimento das frases a partir de uma entrada](quickstart-go-sentences.md)
