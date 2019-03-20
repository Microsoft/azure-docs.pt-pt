---
title: 'Início rápido: Obter lista de idiomas suportados, C# -API de texto do tradutor'
titleSuffix: Azure Cognitive Services
description: Neste início rápido, obtém uma lista de idiomas com suporte para tradução, Transliteração e pesquisa de dicionário com a API de texto do Translator.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 02/07/2019
ms.author: erhopf
ms.openlocfilehash: dea66cfe63f566afc1f0186f0386525f90564708
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58175739"
---
# <a name="quickstart-use-the-translator-text-api-to-get-a-list-of-supported-languages-using-c"></a>Início rápido: Utilizar a API de texto do Translator para obter uma lista dos idiomas com suporte a utilizarC#

Neste início rápido, obtém uma lista de idiomas com suporte para tradução, Transliteração e pesquisa de dicionário com a API de texto do Translator.

## <a name="prerequisites"></a>Pré-requisitos

* [SDK do .NET](https://www.microsoft.com/net/learn/dotnet/hello-world-tutorial)
* [Pacote de NuGet Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download), ou no seu editor de texto favorito

## <a name="create-a-net-core-project"></a>Criar um projeto .NET Core

Abra uma nova linha de comandos (ou sessão de terminal) e execute estes comandos:

```console
dotnet new console -o languages-sample
cd languages-sample
```

O primeiro comando faz duas coisas. Cria uma nova aplicação de consola .NET e cria um diretório chamado `languages-sample`. O segundo comando altera-se para o diretório para o seu projeto.

Em seguida, terá de instalar o Json.Net. A partir do diretório do seu projeto, execute:

```console
dotnet add package Newtonsoft.Json --version 11.0.2
```

## <a name="add-required-namespaces-to-your-project"></a>Adicionar espaços de nomes necessários ao seu projeto

O `dotnet new console` comando que executou anteriormente criado um projeto, incluindo `Program.cs`. Este ficheiro é onde vai pôr o código da aplicação. Abra `Program.cs`e substitua as instruções de utilização existentes. Essas instruções Certifique-se de que tem acesso a todos os tipos necessários para criar e executar a aplicação de exemplo.

```csharp
using System;
using System.Net.Http;
using System.Text;
using Newtonsoft.Json;
```

## <a name="create-a-function-to-get-a-list-of-languages"></a>Criar uma função para obter uma lista de idiomas

Dentro de `Program` classe, crie uma função chamada `GetLanguages`. Essa classe encapsula o código usado para chamar o recurso de linguagens e imprima o resultado à consola.

```csharp
static void GetLanguages()
{
  /*
   * The code for your call to the translation service will be added to this
   * function in the next few sections.
   */
}
```

## <a name="set-the-host-name-and-path"></a>Definir o nome de anfitrião e o caminho

Adicionar estas linhas para o `GetLanguages` função.

```csharp
string host = "https://api.cognitive.microsofttranslator.com";
string route = "/languages?api-version=3.0";
```

## <a name="instantiate-the-client-and-make-a-request"></a>Criar uma instância do cliente e fazer um pedido

Estas linhas instanciar o `HttpClient` e o `HttpRequestMessage`:

```csharp
using (var client = new HttpClient())
using (var request = new HttpRequestMessage())
{
  // In the next few sections you'll add code to construct the request.
}
```

## <a name="construct-the-request-and-print-the-response"></a>Construa a solicitação e resposta de impressão

Dentro do `HttpRequestMessage` irá:

* Declarar o método HTTP
* Construir o URI do pedido
* Adicionar cabeçalhos necessários
* Fazer uma solicitação assíncrona
* Imprimir a resposta

Adicione este código para o `HttpRequestMessage`:

```csharp
// Set the method to GET
request.Method = HttpMethod.Get;
// Construct the full URI
request.RequestUri = new Uri(host + route);
// Send request, get response
var response = client.SendAsync(request).Result;
var jsonResponse = response.Content.ReadAsStringAsync().Result;
// Pretty print the response
Console.WriteLine(PrettyPrint(jsonResponse));
Console.WriteLine("Press any key to continue.");
```

Para imprimir a resposta com "Bastante imprimir" (formatação para a resposta), adicione esta função para sua classe de programa:
```
static string PrettyPrint(string s)
{
    return JsonConvert.SerializeObject(JsonConvert.DeserializeObject(s), Formatting.Indented);
}
```

## <a name="put-it-all-together"></a>Juntar tudo

A última etapa é chamar `GetLanguages()` no `Main` função. Localize `static void Main(string[] args)` e adicionar estas linhas:

```csharp
GetLanguages();
Console.ReadLine();
```

## <a name="run-the-sample-app"></a>Execute a aplicação de exemplo

É isso, está pronto para executar a aplicação de exemplo. A partir da linha de comandos (ou sessão de terminal), navegue para o diretório de projeto e execute:

```console
dotnet run
```

## <a name="sample-response"></a>Resposta de amostra

Encontre a abreviatura de país nesta [lista de idiomas](https://docs.microsoft.com/en-us/azure/cognitive-services/translator/language-support).

```json
{
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
    },
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
    }
}
```

## <a name="clean-up-resources"></a>Limpar recursos

Não se esqueça de remover quaisquer informações confidenciais do código-fonte da sua aplicação de exemplo, como chaves de subscrição.

## <a name="next-steps"></a>Passos Seguintes

Explore o código de exemplo neste início rápido e outros, incluindo a transliteração e a identificação do idioma, assim como outros projetos de Tradução de Texto no GitHub.

> [!div class="nextstepaction"]
> [Explorar exemplos do C# no GitHub](https://aka.ms/TranslatorGitHub?type=&language=c%23)

## <a name="see-also"></a>Consulte também

* [Traduzir texto](quickstart-csharp-translate.md)
* [Transliterar texto](quickstart-csharp-transliterate.md)
* [Identificar o idioma por entrada](quickstart-csharp-detect.md)
* [Obter traduções alternativas](quickstart-csharp-dictionary.md)
* [Determinar o comprimento das frases a partir de uma entrada](quickstart-csharp-sentences.md)
