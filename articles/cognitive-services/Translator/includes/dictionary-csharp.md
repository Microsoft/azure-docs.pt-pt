---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 55ad3591a8c2e7d5de6d1efe255e0f3a4b3c11bd
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "69907051"
---
[!INCLUDE [Prerequisites](prerequisites-csharp.md)]

[!INCLUDE [Setup and use environment variables](setup-env-variables.md)]

## <a name="create-a-net-core-project"></a>Criar um projeto .NET Core

Abra um novo pedido de comando (ou sessão terminal) e execute estes comandos:

```console
dotnet new console -o alternate-sample
cd alternate-sample
```

O primeiro comando faz duas coisas. Cria uma nova aplicação de consola .NET, e cria um diretório chamado `alternate-sample`. O segundo comando muda para o diretório para o seu projeto.

Em seguida, terá de instalar Json.Net. A partir do diretório do seu projeto, corra:

```console
dotnet add package Newtonsoft.Json --version 11.0.2
```

## <a name="add-required-namespaces-to-your-project"></a>Adicione espaços de nome sinuosos necessários ao seu projeto

O `dotnet new console` comando que dirigiu anteriormente `Program.cs`criou um projeto, incluindo. Este ficheiro é onde vai colocar o seu código de inscrição. Abra `Program.cs`e substitua os existentes usando declarações. Estas declarações garantem que tem acesso a todos os tipos necessários para construir e executar a aplicação de amostras.

```csharp
using System;
using System.Net.Http;
using System.Text;
using Newtonsoft.Json;
```

## <a name="get-subscription-information-from-environment-variables"></a>Obtenha informações de subscrição de variáveis ambientais

Adicione as seguintes `Program` linhas à aula. Estas linhas lêem a sua chave de subscrição e ponto final a partir de variáveis ambientais, e lança um erro se você encontrar algum problema.

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

Dentro `Program` da classe, crie uma função chamada `AltTranslation`. Esta classe encapsula o código usado para chamar o recurso dicionário e imprime o resultado para consolar.

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

Adicione estas linhas `AltTranslation` à função. Vai notar que, juntamente com os.dois `api-version`parâmetros adicionais foram declarados. Estes parâmetros são utilizados para definir a entrada e saída de tradução. Nesta amostra, estes são`en`ingleses`es`e espanhóis .

```csharp
string route = "/dictionary/lookup?api-version=3.0";
static string params_ = "from=en&to=es";
static string uri = endpoint + path + params_;
```

Em seguida, precisamos criar e serializar o objeto JSON que inclui o texto que pretende traduzir. Lembre-se, pode passar mais do `body` que um objeto na matriz.

```csharp
System.Object[] body = new System.Object[] { new { Text = @"Elephants" } };
var requestBody = JsonConvert.SerializeObject(body);
```

## <a name="instantiate-the-client-and-make-a-request"></a>Instantie o cliente e faça um pedido

Estas linhas instantaneamente `HttpClient` `HttpRequestMessage`o e o:

```csharp
using (var client = new HttpClient())
using (var request = new HttpRequestMessage())
{
  // In the next few sections you'll add code to construct the request.
}
```

## <a name="construct-the-request-and-print-the-response"></a>Construir o pedido e imprimir a resposta

Dentro `HttpRequestMessage` do seu vai:

* Declarar o método HTTP
* Construa o pedido URI
* Insira o corpo de pedido (objeto JSON serializado)
* Adicione cabeçalhos necessários
* Faça um pedido assíncrono
* Imprimir a resposta

Adicione este código `HttpRequestMessage`ao :

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

Se estiver a utilizar uma subscrição multi-serviço `Ocp-Apim-Subscription-Region` de Serviços Cognitivos, também deve incluir os parâmetros do seu pedido. [Saiba mais sobre autenticação com a subscrição de vários serviços.](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication)

## <a name="put-it-all-together"></a>Juntar tudo

O último passo `AltTranslation()` é `Main` chamar a função. Localize `static void Main(string[] args)` e adicione estas linhas:

```csharp
AltTranslation();
Console.WriteLine("Press any key to continue.");
Console.ReadKey();
```

## <a name="run-the-sample-app"></a>Execute a aplicação de exemplo

Está pronto para executar a sua aplicação de amostras. A partir da linha de comando (ou sessão terminal), navegue até ao seu diretório de projeto e corra:

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

## <a name="clean-up-resources"></a>Limpar recursos

Certifique-se de remover quaisquer informações confidenciais do código fonte da sua aplicação de amostra, como as chaves de subscrição.

## <a name="next-steps"></a>Passos seguintes

Veja a referência da API para entender tudo o que pode fazer com a API de Texto tradutor.

> [!div class="nextstepaction"]
> [Referência da API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
