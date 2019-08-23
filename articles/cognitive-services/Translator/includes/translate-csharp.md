---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 64a934196bb964561f36b9d95a2467b149847225
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69906751"
---
[!INCLUDE [Prerequisites](prerequisites-csharp.md)]

[!INCLUDE [Set up and use environment variables](setup-env-variables.md)]

## <a name="create-a-net-core-project"></a>Criar um projeto .NET Core

Abra uma nova linha de comandos (ou sessão de terminal) e execute estes comandos:

```console
dotnet new console -o translate-sample
cd translate-sample
```

O primeiro comando faz duas coisas. Cria uma nova aplicação de consola .NET e cria um diretório chamado `translate-sample`. O segundo comando altera-se para o diretório para o seu projeto.

Em seguida, você precisará instalar o Json.Net. No diretório do seu projeto, execute:

```console
dotnet add package Newtonsoft.Json --version 11.0.2
```

## <a name="select-the-c-language-version"></a>Selecione o C# versão de idioma

Este início rápido requer C# 7.1 ou posterior. Existem algumas formas de alterar o C# versão para o seu projeto. Neste guia, vamos mostrar-lhe como ajustar o `translate-sample.csproj` ficheiro. Para todas as opções disponíveis, como alterar o idioma no Visual Studio, consulte [selecione o C# versão de idioma](https://docs.microsoft.com/dotnet/csharp/language-reference/configure-language-version).

Abra o projeto, em seguida, abra `translate-sample.csproj`. Certifique-se de que `LangVersion` está definido para o 7.1 ou posterior. Se não existir um grupo de propriedades para a versão de idioma, adicione estas linhas:

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

Em seguida, vamos criar um conjunto de classes que são usadas ao desserializar a resposta JSON retornada pelo API de Tradução de Texto.

```csharp
/// <summary>
/// The C# classes that represents the JSON returned by the Translator Text API.
/// </summary>
public class TranslationResult
{
    public DetectedLanguage DetectedLanguage { get; set; }
    public TextResult SourceText { get; set; }
    public Translation[] Translations { get; set; }
}

public class DetectedLanguage
{
    public string Language { get; set; }
    public float Score { get; set; }
}

public class TextResult
{
    public string Text { get; set; }
    public string Script { get; set; }
}

public class Translation
{
    public string Text { get; set; }
    public TextResult Transliteration { get; set; }
    public string To { get; set; }
    public Alignment Alignment { get; set; }
    public SentenceLength SentLen { get; set; }
}

public class Alignment
{
    public string Proj { get; set; }
}

public class SentenceLength
{
    public int[] SrcSentLen { get; set; }
    public int[] TransSentLen { get; set; }
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

## <a name="create-a-function-to-translate-text"></a>Criar uma função para traduzir o texto

Na classe, crie uma função assíncrona chamada `TranslateTextRequest()`. `Program` Essa função usa quatro argumentos: `subscriptionKey` `route`, `host`, e `inputText`.

```csharp
// This sample requires C# 7.1 or later for async/await.
// Async call to the Translator Text API
static public async Task TranslateTextRequest(string subscriptionKey, string endpoint, string route, string inputText)
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
* Imprima a resposta usando as classes que você criou anteriormente

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
TranslationResult[] deserializedOutput = JsonConvert.DeserializeObject<TranslationResult[]>(result);
// Iterate over the deserialized results.
foreach (TranslationResult o in deserializedOutput)
{
    // Print the detected input language and confidence score.
    Console.WriteLine("Detected input language: {0}\nConfidence score: {1}\n", o.DetectedLanguage.Language, o.DetectedLanguage.Score);
    // Iterate over the results and print each translation.
    foreach (Translation t in o.Translations)
    {
        Console.WriteLine("Translated to {0}: {1}", t.To, t.Text);
    }
}
```

Se você estiver usando uma assinatura de vários serviços cognitivas, também deverá incluir o `Ocp-Apim-Subscription-Region` em seus parâmetros de solicitação. [Saiba mais sobre como autenticar com a assinatura de vários serviços](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="put-it-all-together"></a>Juntar tudo

A última etapa é chamar `TranslateTextRequest()` a `Main` função. Neste exemplo, estamos convertendo para alemão`de`(), italiano (`it`), japonês (`ja`) e tailandês (`th`). Localize `static void Main(string[] args)` e substitua-o por este código:

```csharp
static async Task Main(string[] args)
{
    // This is our main function.
    // Output languages are defined in the route.
    // For a complete list of options, see API reference.
    // https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate
    string route = "/translate?api-version=3.0&to=de&to=it&to=ja&to=th";
    // Prompts you for text to translate. If you'd prefer, you can
    // provide a string as textToTranslate.
    Console.Write("Type the phrase you'd like to translate? ");
    string textToTranslate = Console.ReadLine();
    await TranslateTextRequest(subscriptionKey, endpoint, route, textToTranslate);
    Console.WriteLine("Press any key to continue.");
    Console.ReadKey();
}
```

Você observará que, `Main`em, está declarando `subscriptionKey`, `route` `endpoint`e. Além disso, você está solicitando que o usuário insira `Console.Readline()` e atribua o `textToTranslate`valor a.

## <a name="run-the-sample-app"></a>Execute a aplicação de exemplo

É isso, você está pronto para executar seu aplicativo de exemplo. A partir da linha de comandos (ou sessão de terminal), navegue para o diretório de projeto e execute:

```console
dotnet run
```

## <a name="sample-response"></a>Resposta de amostra

Depois de executar o exemplo, você deverá ver o seguinte impresso no terminal:

```bash
Detected input language: en
Confidence score: 1

Translated to de: Hallo Welt!
Translated to it: Salve, mondo!
Translated to ja: ハローワールド！
Translated to th: หวัดดีชาวโลก!
```

Essa mensagem é criada a partir do JSON bruto, que terá a seguinte aparência:

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
      },
      {
        "text": "ハローワールド！",
        "to": "ja"
      },
      {
        "text": "หวัดดีชาวโลก!",
        "to": "th"
      }
    ]
  }
]
```

## <a name="clean-up-resources"></a>Limpar recursos

Não se esqueça de remover quaisquer informações confidenciais do código-fonte da sua aplicação de exemplo, como chaves de subscrição.

## <a name="next-steps"></a>Passos Seguintes

Dê uma olhada na referência da API para entender tudo o que você pode fazer com o API de Tradução de Texto.

> [!div class="nextstepaction"]
> [Referência da API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
