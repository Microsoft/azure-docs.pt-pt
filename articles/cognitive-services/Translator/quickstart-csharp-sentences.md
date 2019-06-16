---
title: 'Início rápido: Obter comprimentos de sentença, C# -API de texto do tradutor'
titleSuffix: Azure Cognitive Services
description: Neste início rápido, irá aprender como determinar o comprimento de sentença com .NET Core e a API de texto do Translator.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 06/13/2019
ms.author: erhopf
ms.openlocfilehash: 00fe7ce5558672812be7949b8474a403499de94f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67123383"
---
# <a name="quickstart-use-the-translator-text-api-to-determine-sentence-length-using-c"></a>Início rápido: Utilize a API de texto do Translator para determinar a frase através de comprimentoC#

Neste início rápido, irá aprender como determinar os comprimentos de sentença com o .NET Core, C# 7.1 ou posterior e a API de texto do Translator.

Este início rápido requer uma [conta dos Serviços Cognitivos do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com um recurso de Tradução de Texto. Se não tiver uma conta, pode utilizar a [avaliação gratuita](https://azure.microsoft.com/try/cognitive-services/) para obter uma chave de subscrição.

## <a name="prerequisites"></a>Pré-requisitos

* C#7.1 ou posterior
* [SDK do .NET](https://www.microsoft.com/net/learn/dotnet/hello-world-tutorial)
* [Pacote de NuGet Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download), ou no seu editor de texto favorito
* Uma chave de subscrição do Azure para Tradução de Texto

## <a name="create-a-net-core-project"></a>Criar um projeto .NET Core

Abra uma nova linha de comandos (ou sessão de terminal) e execute estes comandos:

```console
dotnet new console -o sentences-sample
cd sentences-sample
```

O primeiro comando faz duas coisas. Cria uma nova aplicação de consola .NET e cria um diretório chamado `sentences-sample`. O segundo comando altera-se para o diretório para o seu projeto.

Em seguida, terá de instalar o Json.Net. A partir do diretório do seu projeto, execute:

```console
dotnet add package Newtonsoft.Json --version 11.0.2
```

## <a name="select-the-c-language-version"></a>Selecione o C# versão de idioma

Este início rápido requer C# 7.1 ou posterior. Existem algumas formas de alterar o C# versão para o seu projeto. Neste guia, vamos mostrar-lhe como ajustar o `sentences-sample.csproj` ficheiro. Para todas as opções disponíveis, como alterar o idioma no Visual Studio, consulte [selecione o C# versão de idioma](https://docs.microsoft.com/dotnet/csharp/language-reference/configure-language-version).

Abra o projeto, em seguida, abra `sentences-sample.csproj`. Certifique-se de que `LangVersion` está definido para o 7.1 ou posterior. Se não existir um grupo de propriedades para a versão de idioma, adicione estas linhas:

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

Em seguida, vamos criar uma classe que é utilizada quando anular a serialização a resposta JSON devolvido pela API de texto do Translator.

```csharp
/// <summary>
/// The C# classes that represents the JSON returned by the Translator Text API.
/// </summary>
public class BreakSentenceResult
{
    public int[] SentLen { get; set; }
    public DetectedLanguage DetectedLanguage { get; set; }
}

public class DetectedLanguage
{
    public string Language { get; set; }
    public float Score { get; set; }
}
```

## <a name="create-a-function-to-determine-sentence-length"></a>Criar uma função para determinar o comprimento da sentença

Dentro de `Program` classe, crie uma função chamada `BreakSentence()`. Esta função aceita quatro argumentos: `subscriptionKey`, `host`, `route`, e `inputText`.

```csharp
static public async Task BreakSentenceRequest(string subscriptionKey, string host, string route, string inputText)
{
  /*
   * The code for your call to the translation service will be added to this
   * function in the next few sections.
   */
}
```

## <a name="serialize-the-break-sentence-request"></a>Serializar o pedido de sentença break

Em seguida, terá de criar e serializar o objeto JSON que inclua o texto. Tenha em mente, pode passar mais do que um objeto no `body` matriz.

```csharp
object[] body = new object[] { new { Text = inputText } };
var requestBody = JsonConvert.SerializeObject(body);
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
* Insira o corpo do pedido (objeto JSON serializado)
* Adicionar cabeçalhos necessários
* Fazer uma solicitação assíncrona
* Imprimir a resposta

Adicione este código para o `HttpRequestMessage`:

```csharp
// Build the request.
// Set the method to POST
request.Method = HttpMethod.Post;
// Construct the URI and add headers.
request.RequestUri = new Uri(host + route);
request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);

// Send the request and get response.
HttpResponseMessage response = await client.SendAsync(request).ConfigureAwait(false);
// Read response as a string.
string result = await response.Content.ReadAsStringAsync();
// Deserialize the response using the classes created earlier.
BreakSentenceResult[] deserializedOutput = JsonConvert.DeserializeObject<BreakSentenceResult[]>(result);
foreach (BreakSentenceResult o in deserializedOutput)
{
    Console.WriteLine("The detected language is '{0}'. Confidence is: {1}.", o.DetectedLanguage.Language, o.DetectedLanguage.Score);
    Console.WriteLine("The first sentence length is: {0}", o.SentLen[0]);
}
```

## <a name="put-it-all-together"></a>Juntar tudo

A última etapa é chamar `BreakSentenceRequest()` no `Main` função. Localize `static void Main(string[] args)` e substituí-lo com este código:

```csharp
static async Task Main(string[] args)
{
    // This is our main function.
    // Output languages are defined in the route.
    // For a complete list of options, see API reference.
    string subscriptionKey = "YOUR_TRANSLATOR_TEXT_KEY_GOES_HERE";
    string host = "https://api.cognitive.microsofttranslator.com";
    string route = "/breaksentence?api-version=3.0";
    // Feel free to use any string.
    string breakSentenceText = @"How are you doing today? The weather is pretty pleasant. Have you been to the movies lately?";
    await BreakSentenceRequest(subscriptionKey, host, route, breakSentenceText);
}
```

Observará que, na `Main`, está declarando `subscriptionKey`, `host`, `route`e o texto para avaliar `breakSentenceText`.

## <a name="run-the-sample-app"></a>Execute a aplicação de exemplo

É isso, está pronto para executar a aplicação de exemplo. A partir da linha de comandos (ou sessão de terminal), navegue para o diretório de projeto e execute:

```console
dotnet run
```

## <a name="sample-response"></a>Resposta de amostra

Depois de executar o exemplo, deverá ver o seguinte impresso terminal:

```bash
The detected language is \'en\'. Confidence is: 1.
The first sentence length is: 25
```

Esta mensagem é criada a partir de JSON não processado, que terá a seguinte aparência:

```json
[
    {
        "detectedLanguage":
        {
            "language":"en",
            "score":1.0
        },
        "sentLen":[25,32,35]
    }
]
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
* [Obter uma lista de idiomas suportados](quickstart-csharp-languages.md)
* [Determinar o comprimento das frases a partir de uma entrada](quickstart-csharp-sentences.md)
