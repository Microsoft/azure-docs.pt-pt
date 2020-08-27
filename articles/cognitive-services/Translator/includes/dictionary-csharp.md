---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.custom: devx-track-csharp
ms.openlocfilehash: 4821d4637a90acda12fec5656907ad3eedb24163
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88921477"
---
[!INCLUDE [Prerequisites](prerequisites-csharp.md)]

[!INCLUDE [Setup and use environment variables](setup-env-variables.md)]

## <a name="create-a-net-core-project"></a>Criar um projeto .NET Core

Abra uma nova solicitação de comando (ou sessão terminal) e execute estes comandos:

```console
dotnet new console -o alternate-sample
cd alternate-sample
```

O primeiro comando faz duas coisas. Cria uma nova aplicação de consola .NET e cria um diretório chamado `alternate-sample` . O segundo comando muda o diretório para o seu projeto.

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

## <a name="get-subscription-information-from-environment-variables"></a>Obtenha informações de subscrição a partir de variáveis ambientais

Adicione as seguintes linhas à `Program` classe. Estas linhas lêem a sua chave de subscrição e ponto final a partir de variáveis ambientais, e lançam um erro se encontrar algum problema.

```csharp
private const string key_var = "TRANSLATOR_TEXT_SUBSCRIPTION_KEY";
private static readonly string subscriptionKey = Environment.GetEnvironmentVariable(key_var);

private const string endpoint_var = "TRANSLATOR_TEXT_ENDPOINT";
private static readonly string endpoint = Environment.GetEnvironmentVariable(endpoint_var);

static Program()
{
    if (null == subscriptionKey)
    {
        throw new Exception("Please set/export the environment variable: " + key_var);
    }
    if (null == endpoint)
    {
        throw new Exception("Please set/export the environment variable: " + endpoint_var);
    }
}
// The code in the next section goes here.
```

## <a name="create-a-function-to-get-alternate-translations"></a>Criar uma função para obter traduções alternativas

Dentro da `Program` classe, crie uma função chamada `AltTranslation` . Esta classe encapsula o código utilizado para chamar o recurso Dicionário e imprime o resultado para consolar.

```csharp
static void AltTranslation()
{
  /*
   * The code for your call to the translation service will be added to this
   * function in the next few sections.
   */
}
```

## <a name="construct-the-uri"></a>Construa o URI

Adicione estas linhas à `AltTranslation` função. Notarão que, juntamente com os `api-version` dois parâmetros adicionais, foram declarados dois parâmetros adicionais. Estes parâmetros são utilizados para definir a entrada e a saída de tradução. Nesta amostra, estes são inglês ( `en` ) e espanhol `es` ().

```csharp
string route = "/dictionary/lookup?api-version=3.0";
static string params_ = "from=en&to=es";
static string uri = endpoint + path + params_;
```

Em seguida, precisamos criar e serializar o objeto JSON que inclui o texto que pretende traduzir. Tenha em mente que pode passar mais do que um objeto na `body` matriz.

```csharp
System.Object[] body = new System.Object[] { new { Text = @"Elephants" } };
var requestBody = JsonConvert.SerializeObject(body);
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
* Insira o corpo do pedido (objeto JSON serializado)
* Adicionar cabeçalhos necessários
* Faça um pedido assíncronos
* Imprimir a resposta

Adicione este código `HttpRequestMessage` ao:

```csharp
// Set the method to POST
request.Method = HttpMethod.Post;

// Construct the full URI
request.RequestUri = new Uri(uri);

// Add the serialized JSON object to your request
request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");

// Add the authorization header
request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);

// Send request, get response
var response = client.SendAsync(request).Result;
var jsonResponse = response.Content.ReadAsStringAsync().Result;

// Print the response
Console.WriteLine(PrettyPrint(jsonResponse));
Console.WriteLine("Press any key to continue.");
```

Adicione `PrettyPrint` para adicionar formatação à sua resposta JSON:
```csharp
static string PrettyPrint(string s)
{
    return JsonConvert.SerializeObject(JsonConvert.DeserializeObject(s), Formatting.Indented);
}
```

Se estiver a utilizar uma subscrição multi-serviço dos Serviços Cognitivos, também deve incluir os parâmetros do `Ocp-Apim-Subscription-Region` seu pedido. [Saiba mais sobre a autenticação com a subscrição multi-serviço.](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication)

## <a name="put-it-all-together"></a>Juntar tudo

O último passo é chamar `AltTranslation()` a `Main` função. Localize `static void Main(string[] args)` e adicione estas linhas:

```csharp
AltTranslation();
Console.WriteLine("Press any key to continue.");
Console.ReadKey();
```

## <a name="run-the-sample-app"></a>Execute a aplicação de exemplo

Está pronto para executar a sua aplicação de amostras. A partir da linha de comando (ou sessão terminal), navegue até ao diretório do projeto e corra:

```console
dotnet run
```

## <a name="sample-response"></a>Resposta de amostra

```json
[
    {
        "displaySource": "elephants",
        "normalizedSource": "elephants",
        "translations": [
            {
                "backTranslations": [
                    {
                        "displayText": "elephants",
                        "frequencyCount": 1207,
                        "normalizedText": "elephants",
                        "numExamples": 5
                    }
                ],
                "confidence": 1.0,
                "displayTarget": "elefantes",
                "normalizedTarget": "elefantes",
                "posTag": "NOUN",
                "prefixWord": ""
            }
        ]
    }
]
```

## <a name="clean-up-resources"></a>Limpar os recursos

Certifique-se de remover qualquer informação confidencial do código fonte da sua aplicação de amostra, como chaves de subscrição.

## <a name="next-steps"></a>Passos seguintes

Veja a referência da API para entender tudo o que pode fazer com o Tradutor.

> [!div class="nextstepaction"]
> [Referência da API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
