---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.custom: devx-track-csharp
ms.openlocfilehash: 0c74c3709848e4296f07844278b076fdc7ffa9bc
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88921435"
---
[!INCLUDE [Prerequisites](prerequisites-csharp.md)]

[!INCLUDE [Set up and use environment variables](setup-env-variables.md)]

## <a name="create-a-net-core-project"></a>Criar um projeto .NET Core

Abra uma nova solicitação de comando (ou sessão terminal) e execute estes comandos:

```console
dotnet new console -o translate-sample
cd translate-sample
```

O primeiro comando faz duas coisas. Cria uma nova aplicação de consola .NET e cria um diretório chamado `translate-sample` . O segundo comando muda o diretório para o seu projeto.

Em seguida, terá de instalar Json.Net. Do diretório do seu projeto, corra:

```console
dotnet add package Newtonsoft.Json --version 11.0.2
```

## <a name="select-the-c-language-version"></a>Selecione a versão linguística C#

Este arranque rápido requer C# 7.1 ou mais tarde. Existem algumas formas de mudar a versão C# para o seu projeto. Neste guia, vamos mostrar-lhe como ajustar o `translate-sample.csproj` ficheiro. Para todas as opções disponíveis, como alterar o idioma no Estúdio Visual, consulte [a versão linguística C#](https://docs.microsoft.com/dotnet/csharp/language-reference/configure-language-version).

Abra o seu projeto e, em seguida, `translate-sample.csproj` abra. Certifique-se de que `LangVersion` está definido para 7.1 ou mais tarde. Se não houver um grupo de propriedade para a versão linguística, adicione estas linhas:

```xml
<PropertyGroup>
   <LangVersion>7.1</LangVersion>
</PropertyGroup>
```

## <a name="add-required-namespaces-to-your-project"></a>Adicione espaços de nome necessários ao seu projeto

O `dotnet new console` comando que dirigiu anteriormente criou um projeto, incluindo. `Program.cs` Este ficheiro é onde vai colocar o seu código de inscrição. Abra `Program.cs` , e substitua as declarações existentes. Estas declarações garantem que tem acesso a todos os tipos necessários para construir e executar a aplicação da amostra.

```csharp
using System;
using System.Net.Http;
using System.Text;
using System.Threading.Tasks;
// Install Newtonsoft.Json with NuGet
using Newtonsoft.Json;
```

## <a name="create-classes-for-the-json-response"></a>Criar aulas para a resposta JSON

Em seguida, vamos criar um conjunto de aulas que são usadas para deserizar a resposta JSON devolvida pelo Tradutor.

```csharp
/// <summary>
/// The C# classes that represents the JSON returned by the Translator.
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

## <a name="create-a-function-to-translate-text"></a>Criar uma função para traduzir texto

Na `Program` classe, crie uma função assíncronea chamada `TranslateTextRequest()` . Esta função requer quatro argumentos: `subscriptionKey` `host` , , e `route` `inputText` .

```csharp
// This sample requires C# 7.1 or later for async/await.
// Async call to the Translator
static public async Task TranslateTextRequest(string subscriptionKey, string endpoint, string route, string inputText)
{
  /*
   * The code for your call to the translation service will be added to this
   * function in the next few sections.
   */
}
```

## <a name="serialize-the-translation-request"></a>Serialize o pedido de tradução

Em seguida, precisamos criar e serializar o objeto JSON que inclui o texto que pretende traduzir. Tenha em mente que pode passar mais do que um objeto no `body` .

```csharp
object[] body = new object[] { new { Text = inputText } };
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
* Imprima a resposta usando as classes que criou anteriormente

Adicione este código `HttpRequestMessage` ao:

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

Se estiver a utilizar uma subscrição multi-serviço dos Serviços Cognitivos, também deve incluir os parâmetros do `Ocp-Apim-Subscription-Region` seu pedido. [Saiba mais sobre a autenticação com a subscrição multi-serviço.](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication)

## <a name="put-it-all-together"></a>Juntar tudo

O último passo é chamar `TranslateTextRequest()` a `Main` função. Nesta amostra, estamos a traduzir para alemão `de` (, italiano `it` ( ( ( ( ( ( ( ) e `ja` tailandês ( `th` ). `static void Main(string[] args)`Localize-o e substitua-o por este código:

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

Vais reparar que `Main` em, estás a `subscriptionKey` `endpoint` declarar, `route` e... Além disso, está a solicitar ao utilizador a entrada `Console.Readline()` e a atribuição do valor a `textToTranslate` .

## <a name="run-the-sample-app"></a>Execute a aplicação de exemplo

Está pronto para executar a sua aplicação de amostras. A partir da linha de comando (ou sessão terminal), navegue até ao diretório do projeto e corra:

```console
dotnet run
```

## <a name="sample-response"></a>Resposta de amostra

Depois de analisar a amostra, deverá ver o seguinte impresso no terminal:

```bash
Detected input language: en
Confidence score: 1

Translated to de: Hallo Welt!
Translated to it: Salve, mondo!
Translated to ja: ハローワールド！
Translated to th: หวัดดีชาวโลก!
```

Esta mensagem é construída a partir do JSON cru, que será assim:

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

## <a name="clean-up-resources"></a>Limpar os recursos

Certifique-se de remover qualquer informação confidencial do código fonte da sua aplicação de amostra, como chaves de subscrição.

## <a name="next-steps"></a>Passos seguintes

Veja a referência da API para entender tudo o que pode fazer com o Tradutor.

> [!div class="nextstepaction"]
> [Referência da API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
