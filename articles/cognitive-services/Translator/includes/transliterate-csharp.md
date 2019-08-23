---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 81fb599ca4987adccdb91baa7a74c33ae3af48d4
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69906642"
---
[!INCLUDE [Prerequisites](prerequisites-csharp.md)]

[!INCLUDE [Set up and use environment variables](setup-env-variables.md)]

## <a name="create-a-net-core-project"></a>Criar um projeto .NET Core

Abra uma nova linha de comandos (ou sessão de terminal) e execute estes comandos:

```console
dotnet new console -o transliterate-sample
cd transliterate-sample
```

O primeiro comando faz duas coisas. Cria uma nova aplicação de consola .NET e cria um diretório chamado `transliterate-sample`. O segundo comando altera-se para o diretório para o seu projeto.

Em seguida, você precisará instalar o Json.Net. No diretório do seu projeto, execute:

```console
dotnet add package Newtonsoft.Json --version 11.0.2
```

## <a name="select-the-c-language-version"></a>Selecione o C# versão de idioma

Este início rápido requer C# 7.1 ou posterior. Existem algumas formas de alterar o C# versão para o seu projeto. Neste guia, vamos mostrar-lhe como ajustar o `transliterate-sample.csproj` ficheiro. Para todas as opções disponíveis, como alterar o idioma no Visual Studio, consulte [selecione o C# versão de idioma](https://docs.microsoft.com/dotnet/csharp/language-reference/configure-language-version).

Abra o projeto, em seguida, abra `transliterate-sample.csproj`. Certifique-se de que `LangVersion` está definido para o 7.1 ou posterior. Se não existir um grupo de propriedades para a versão de idioma, adicione estas linhas:

```xml
<PropertyGroup>
   <LangVersion>7.1</LangVersion>
</PropertyGroup>
```

## <a name="add-required-namespaces-to-your-project"></a>Adicionar espaços de nomes necessários ao seu projeto

O `dotnet new console` comando que executou anteriormente criado um projeto, incluindo `Program.cs`. Este ficheiro é onde vai pôr o código da aplicação. Abra `Program.cs`e substitua as instruções de utilização existentes. Essas instruções Certifique-se de que tem acesso a todos os tipos necessários para criar e executar a aplicação de exemplo.

```csharp
using System;
using System.Net.Http;
using System.Text;
using System.Threading.Tasks;
// Install Newtonsoft.Json with NuGet
using Newtonsoft.Json;
```

## <a name="create-classes-for-the-json-response"></a>Criar classes para a resposta JSON

Em seguida, vamos criar uma classe que é usada ao desserializar a resposta JSON retornada pelo API de Tradução de Texto.

```csharp
/// <summary>
/// The C# classes that represents the JSON returned by the Translator Text API.
/// </summary>
public class TransliterationResult
{
    public string Text { get; set; }
    public string Script { get; set; }
}
```

## <a name="get-subscription-information-from-environment-variables"></a>Obter informações de assinatura de variáveis de ambiente

Adicione as linhas a seguir à `Program` classe. Essas linhas lêem a chave de assinatura e o ponto de extremidade de variáveis de ambiente e geram um erro se você encontrar problemas.

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

## <a name="create-a-function-to-transliterate-text"></a>Criar uma função para transliterar texto

Dentro da `Program` classe, crie uma função assíncrona `TransliterateTextRequest()`chamada. Essa função usa quatro argumentos: `subscriptionKey` `route`, `endpoint`, e `inputText`.

```csharp
static public async Task TransliterateTextRequest(string subscriptionKey, string endpoint, string route, string inputText)
{
  /*
   * The code for your call to the translation service will be added to this
   * function in the next few sections.
   */
}
```

## <a name="serialize-the-translation-request"></a>Serializar a solicitação de tradução

Em seguida, precisamos criar e serializar o objeto JSON que inclui o texto que você deseja traduzir. Tenha em mente que você pode passar mais de um objeto no `body`.

```csharp
object[] body = new object[] { new { Text = inputText } };
var requestBody = JsonConvert.SerializeObject(body);
```

## <a name="instantiate-the-client-and-make-a-request"></a>Instanciar o cliente e fazer uma solicitação

Essas linhas instanciam `HttpClient` o e `HttpRequestMessage`o:

```csharp
using (var client = new HttpClient())
using (var request = new HttpRequestMessage())
{
  // In the next few sections you'll add code to construct the request.
}
```

## <a name="construct-the-request-and-print-the-response"></a>Construir a solicitação e imprimir a resposta

Dentro do `HttpRequestMessage` , você vai:

* Declarar o método HTTP
* Construir o URI de solicitação
* Inserir o corpo da solicitação (objeto JSON serializado)
* Adicionar cabeçalhos necessários
* Fazer uma solicitação assíncrona
* Imprimir a resposta

Adicione este código ao `HttpRequestMessage`:

```csharp
// Build the request.
// Set the method to Post.
request.Method = HttpMethod.Post;
// Construct the URI and add headers.
request.RequestUri = new Uri(endpoint + route);
request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);

// Send the request and get response.
HttpResponseMessage response = await client.SendAsync(request).ConfigureAwait(false);
// Read response as a string.
string result = await response.Content.ReadAsStringAsync();
// Deserialize the response using the classes created earlier.
TransliterationResult[] deserializedOutput = JsonConvert.DeserializeObject<TransliterationResult[]>(result);
// Iterate over the deserialized results.
foreach (TransliterationResult o in deserializedOutput)
{
    Console.WriteLine("Transliterated to {0} script: {1}", o.Script, o.Text);
}
```

Se você estiver usando uma assinatura de vários serviços cognitivas, também deverá incluir o `Ocp-Apim-Subscription-Region` em seus parâmetros de solicitação. [Saiba mais sobre como autenticar com a assinatura de vários serviços](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="put-it-all-together"></a>Juntar tudo

A última etapa é chamar `TransliterateTextRequest()` a `Main` função. Neste exemplo, estamos transliterating de japonês para o script latino. Localize `static void Main(string[] args)` e substitua-o por este código:

```csharp
static async Task Main(string[] args)
{
    // This is our main function.
    // Output languages are defined in the route.
    // For a complete list of options, see API reference.
    // https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-transliterate
    string route = "/transliterate?api-version=3.0&language=ja&fromScript=jpan&toScript=latn";
    string textToTransliterate = @"こんにちは";
    await TransliterateTextRequest(subscriptionKey, endpoint, route, textToTransliterate);
    Console.WriteLine("Press any key to continue.");
    Console.ReadKey();
}
```

Você observará que, `Main`em, você está `subscriptionKey`declarando, `endpoint`, `route`e o script a ser `textToTransliterate`transliterado.

## <a name="run-the-sample-app"></a>Execute a aplicação de exemplo

É isso, você está pronto para executar seu aplicativo de exemplo. A partir da linha de comandos (ou sessão de terminal), navegue para o diretório de projeto e execute:

```console
dotnet run
```

## <a name="sample-response"></a>Resposta de amostra

Depois de executar o exemplo, você deverá ver o seguinte impresso no terminal:

```bash
Transliterated to latn script: Kon\'nichiwa
```

Essa mensagem é criada a partir do JSON bruto, que terá a seguinte aparência:

```json
[
    {
        "script": "latn",
        "text": "konnichiwa"
    }
]
```

## <a name="clean-up-resources"></a>Limpar recursos

Não se esqueça de remover quaisquer informações confidenciais do código-fonte da sua aplicação de exemplo, como chaves de subscrição.

## <a name="next-steps"></a>Passos Seguintes

Dê uma olhada na referência da API para entender tudo o que você pode fazer com o API de Tradução de Texto.

> [!div class="nextstepaction"]
> [Referência da API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
