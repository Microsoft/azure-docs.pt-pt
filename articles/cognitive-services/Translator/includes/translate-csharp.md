---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 77242ab35416fb24a3a815e7137280e5f3698c30
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83586657"
---
[!INCLUDE [Prerequisites](prerequisites-csharp.md)]

[!INCLUDE [Set up and use environment variables](setup-env-variables.md)]

## <a name="create-a-net-core-project"></a>Criar um projeto .NET Core

Abra um novo pedido de comando (ou sessão terminal) e execute estes comandos:

```console
dotnet new console -o translate-sample
cd translate-sample
```

O primeiro comando faz duas coisas. Cria uma nova aplicação de consola .NET, e cria um diretório chamado `translate-sample` . O segundo comando muda para o diretório para o seu projeto.

Em seguida, terá de instalar Json.Net. A partir do diretório do seu projeto, corra:

```console
dotnet add package Newtonsoft.Json --version 11.0.2
```

## <a name="select-the-c-language-version"></a>Selecione a versão em língua C#

Este quickstart requer C# 7.1 ou mais tarde. Existem algumas formas de alterar a versão C# para o seu projeto. Neste guia, vamos mostrar-lhe como ajustar o `translate-sample.csproj` ficheiro. Para todas as opções disponíveis, tais como alterar o idioma no Estúdio Visual, consulte [Selecione a versão em língua C#](https://docs.microsoft.com/dotnet/csharp/language-reference/configure-language-version).

Abra o seu projeto e `translate-sample.csproj` abra. Certifique-se de que `LangVersion` está definido para 7.1 ou mais tarde. Se não houver um grupo imobiliário para a versão linguística, adicione estas linhas:

```xml
<PropertyGroup>
   <LangVersion>7.1</LangVersion>
</PropertyGroup>
```

## <a name="add-required-namespaces-to-your-project"></a>Adicione espaços de nome sinuosos necessários ao seu projeto

O `dotnet new console` comando que dirigiu anteriormente criou um projeto, incluindo. `Program.cs` Este ficheiro é onde vai colocar o seu código de inscrição. Abra `Program.cs` e substitua os existentes usando declarações. Estas declarações garantem que tem acesso a todos os tipos necessários para construir e executar a aplicação de amostras.

```csharp
using System;
using System.Net.Http;
using System.Text;
using System.Threading.Tasks;
// Install Newtonsoft.Json with NuGet
using Newtonsoft.Json;
```

## <a name="create-classes-for-the-json-response"></a>Criar aulas para a resposta json

Em seguida, vamos criar um conjunto de aulas que são usadas quando desserializar a resposta JSON devolvida pelo Tradutor.

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

## <a name="get-subscription-information-from-environment-variables"></a>Obtenha informações de subscrição de variáveis ambientais

Adicione as seguintes linhas à `Program` aula. Estas linhas lêem a sua chave de subscrição e ponto final a partir de variáveis ambientais, e lança um erro se você encontrar algum problema.

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

Na `Program` aula, crie uma função assíncrona chamada `TranslateTextRequest()` . Esta função requer quatro argumentos: `subscriptionKey` `host` , , e `route` `inputText` .

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

## <a name="instantiate-the-client-and-make-a-request"></a>Instantie o cliente e faça um pedido

Estas linhas instantaneamente o `HttpClient` e `HttpRequestMessage` o:

```csharp
using (var client = new HttpClient())
using (var request = new HttpRequestMessage())
{
  // In the next few sections you'll add code to construct the request.
}
```

## <a name="construct-the-request-and-print-the-response"></a>Construir o pedido e imprimir a resposta

Dentro do `HttpRequestMessage` seu vai:

* Declarar o método HTTP
* Construa o pedido URI
* Insira o corpo de pedido (objeto JSON serializado)
* Adicione cabeçalhos necessários
* Faça um pedido assíncrono
* Imprima a resposta usando as classes que criou anteriormente

Adicione este código ao `HttpRequestMessage` :

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

Se estiver a utilizar uma subscrição multi-serviço de Serviços Cognitivos, também deve incluir os `Ocp-Apim-Subscription-Region` parâmetros do seu pedido. [Saiba mais sobre autenticação com a subscrição de vários serviços.](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication)

## <a name="put-it-all-together"></a>Juntar tudo

O último passo é chamar `TranslateTextRequest()` a `Main` função. Nesta amostra, estamos traduzindo para alemão `de` , italiano , `it` japonês ( e `ja` tailandês). `th` `static void Main(string[] args)`Localize-o e substitua-o por este código:

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

Vais reparar `Main` nisso, estás a `subscriptionKey` `endpoint` declarar, `route` e. Além disso, está a pedir ao utilizador para que entre `Console.Readline()` e atribua o valor a `textToTranslate` .

## <a name="run-the-sample-app"></a>Execute a aplicação de exemplo

Está pronto para executar a sua aplicação de amostras. A partir da linha de comando (ou sessão terminal), navegue até ao seu diretório de projeto e corra:

```console
dotnet run
```

## <a name="sample-response"></a>Resposta de amostra

Depois de executar a amostra, deve ver o seguinte impresso no terminal:

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

## <a name="clean-up-resources"></a>Limpar recursos

Certifique-se de remover quaisquer informações confidenciais do código fonte da sua aplicação de amostra, como as chaves de subscrição.

## <a name="next-steps"></a>Passos seguintes

Veja a referência da API para entender tudo o que pode fazer com o Tradutor.

> [!div class="nextstepaction"]
> [Referência da API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
