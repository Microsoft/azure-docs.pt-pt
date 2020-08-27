---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.custom: devx-track-csharp
ms.openlocfilehash: 9a69c0b7f204fb07e6d4ec94e8a2cecb0a404735
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88921376"
---
[!INCLUDE [Prerequisites](prerequisites-csharp.md)]

[!INCLUDE [Set up and use environment variables](setup-env-variables.md)]

## <a name="create-a-net-core-project"></a>Criar um projeto .NET Core

Abra uma nova solicitação de comando (ou sessão terminal) e execute estes comandos:

```console
dotnet new console -o languages-sample
cd languages-sample
```

O primeiro comando faz duas coisas. Cria uma nova aplicação de consola .NET e cria um diretório chamado `languages-sample` . O segundo comando muda o diretório para o seu projeto.

Em seguida, terá de instalar Json.Net. Do diretório do seu projeto, corra:

```console
dotnet add package Newtonsoft.Json --version 11.0.2
```

## <a name="add-required-namespaces-to-your-project"></a>Adicione espaços de nome necessários ao seu projeto

O `dotnet new console` comando que dirigiu anteriormente criou um projeto, incluindo. `Program.cs` Este ficheiro é onde vai colocar o seu código de inscrição. Abra `Program.cs` , e substitua as declarações existentes. Estas declarações garantem que tem acesso a todos os tipos necessários para construir e executar a aplicação da amostra.

```csharp
using System;
using System.Net.Http;
using System.Text;
using Newtonsoft.Json;
```

## <a name="get-endpoint-information-from-an-environment-variable"></a>Obtenha informações de ponto final de uma variável ambiental

Adicione as seguintes linhas à `Program` classe. Estas linhas lêem a sua chave de subscrição e ponto final a partir de variáveis ambientais, e lançam um erro se encontrar algum problema.

```csharp
private const string endpoint_var = "TRANSLATOR_TEXT_ENDPOINT";
private static readonly string endpoint = Environment.GetEnvironmentVariable(endpoint_var);

static Program()
{
    if (null == endpoint)
    {
        throw new Exception("Please set/export the environment variable: " + endpoint_var);
    }
}
```

## <a name="create-a-function-to-get-a-list-of-languages"></a>Criar uma função para obter uma lista de idiomas

Na `Program` classe, crie uma função chamada `GetLanguages` . Esta classe encapsula o código utilizado para chamar o recurso Languages e imprime o resultado para consolar.

```csharp
static void GetLanguages()
{
  /*
   * The code for your call to the translation service will be added to this
   * function in the next few sections.
   */
}
```

## <a name="set-the-route"></a>Definir a rota

Adicione estas linhas à `GetLanguages` função.

```csharp
string route = "/languages?api-version=3.0";
```

## <a name="instantiate-the-client-and-make-a-request"></a>Instantaneamente o cliente e faça um pedido

Estas linhas instantaneamente o `HttpClient` e `HttpRequestMessage` o:

```csharp
using (var client = new HttpClient())
using (var request = new HttpRequestMessage())
{
  // In the next few sections you'll add code to construct the request.
}
```

## <a name="construct-the-request-and-print-the-response"></a>Construa o pedido e imprima a resposta

Dentro do `HttpRequestMessage` you:ll:

* Declare o método HTTP
* Construa o pedido URI
* Adicionar cabeçalhos necessários
* Faça um pedido assíncronos
* Imprimir a resposta

Adicione este código `HttpRequestMessage` ao:

```csharp
// Set the method to GET
request.Method = HttpMethod.Get;
// Construct the full URI
request.RequestUri = new Uri(endpoint + route);
// Send request, get response
var response = client.SendAsync(request).Result;
var jsonResponse = response.Content.ReadAsStringAsync().Result;
// Pretty print the response
Console.WriteLine(PrettyPrint(jsonResponse));
Console.WriteLine("Press any key to continue.");
```

Se estiver a utilizar uma subscrição multi-serviço dos Serviços Cognitivos, também deve incluir os parâmetros do `Ocp-Apim-Subscription-Region` seu pedido. [Saiba mais sobre a autenticação com a subscrição multi-serviço.](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication)

Para imprimir a resposta com "Pretty Print" (formatação para a resposta), adicione esta função à sua classe Programa:

```csharp
static string PrettyPrint(string s)
{
    return JsonConvert.SerializeObject(JsonConvert.DeserializeObject(s), Formatting.Indented);
}
```

## <a name="put-it-all-together"></a>Juntar tudo

O último passo é chamar `GetLanguages()` a `Main` função. Localize `static void Main(string[] args)` e adicione estas linhas:

```csharp
GetLanguages();
Console.ReadLine();
```

## <a name="run-the-sample-app"></a>Execute a aplicação de exemplo

Está pronto para executar a sua aplicação de amostras. A partir da linha de comando (ou sessão terminal), navegue até ao diretório do projeto e corra:

```console
dotnet run
```

## <a name="sample-response"></a>Resposta de amostra

Encontre a abreviatura país/região nesta [lista de línguas.](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)

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

## <a name="clean-up-resources"></a>Limpar os recursos

Certifique-se de remover qualquer informação confidencial do código fonte da sua aplicação de amostra, como chaves de subscrição.

## <a name="next-steps"></a>Passos seguintes

Veja a referência da API para entender tudo o que pode fazer com o Tradutor.

> [!div class="nextstepaction"]
> [Referência da API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
