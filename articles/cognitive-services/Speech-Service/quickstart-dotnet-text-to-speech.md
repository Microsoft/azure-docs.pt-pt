---
title: 'Início rápido: Converter a voz, .NET Core - Serviços de voz'
titleSuffix: Azure Cognitive Services
description: Neste início rápido, irá aprender como converter voz com a API de REST de voz. O texto de exemplo incluído neste guia está estruturado como Speech Synthesis Markup Language (SSML). Isto permite-lhe escolher a voz e o idioma da resposta de voz.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: erhopf
ms.openlocfilehash: 656ed50b9104fec48ff3eddb3c291608f8f87294
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57534042"
---
# <a name="quickstart-convert-text-to-speech-using-net-core"></a>Início rápido: Converter texto para voz com o .NET Core

Neste início rápido, irá aprender como converter texto para voz com .NET Core e a API de REST de voz. O texto de exemplo incluído neste guia está estruturado como [Speech Synthesis Markup Language (SSML)](speech-synthesis-markup.md), que permite-lhe escolher a voz e o idioma da resposta.

Este início rápido requer uma [conta dos serviços cognitivos do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com um recurso de serviço de voz. Se não tiver uma conta, pode utilizar a [avaliação gratuita](https://azure.microsoft.com/try/cognitive-services/) para obter uma chave de subscrição.

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido requer:

* [SDK .NET Core](https://dotnet.microsoft.com/download)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download), ou no seu editor de texto favorito
* Uma chave de subscrição do Azure para o serviço de voz

## <a name="create-a-net-core-project"></a>Criar um projeto .NET Core

Abra uma nova linha de comandos (ou sessão de terminal) e execute estes comandos:

```console
dotnet new console -o tts-sample
cd tts-sample
```

O primeiro comando faz duas coisas. Cria uma nova aplicação de consola .NET e cria um diretório chamado `tts-sample`. O segundo comando altera-se para o diretório para o seu projeto.

## <a name="select-the-c-language-version"></a>Selecione o C# versão de idioma

Este início rápido requer C# 7.1 ou posterior. Existem algumas formas de alterar o C# versão para o seu projeto. Neste guia, vamos mostrar-lhe como ajustar o `tts-sample.csproj` ficheiro. Para todas as opções disponíveis, como alterar o idioma no Visual Studio, consulte [selecione o C# versão de idioma](https://docs.microsoft.com/dotnet/csharp/language-reference/configure-language-version).

Abra o projeto, em seguida, abra `tts-sample.csproj`. Certifique-se de que `LangVersion` está definido para o 7.1 ou posterior. Se não existir um grupo de propriedades para a versão de idioma, adicione estas linhas:

```csharp
<PropertyGroup>
   <LangVersion>7.1</LangVersion>
</PropertyGroup>
```

Certifique-se guardar as alterações.

## <a name="add-required-namespaces-to-your-project"></a>Adicionar espaços de nomes necessários ao seu projeto

O `dotnet new console` comando que executou anteriormente criado um projeto, incluindo `Program.cs`. Este ficheiro é onde vai pôr o código da aplicação. Abra `Program.cs`e substitua as instruções de utilização existentes. Essas instruções Certifique-se de que tem acesso a todos os tipos necessários para criar e executar a aplicação de exemplo.

```csharp
using System;
using System.Net.Http;
using System.Text;
using System.IO;
using System.Threading.Tasks;
```

## <a name="create-a-class-for-token-exchange"></a>Criar uma classe para troca de tokens

A API REST do texto para discurso exige um token de acesso para a autenticação. Para obter um token de acesso, um exchange é necessário. Este exemplo troca sua chave de subscrição do serviço de voz para um token de acesso através do `issueToken` ponto final.

Este exemplo parte do princípio de que a sua subscrição do serviço de voz está na região E.U.A. oeste. Se estiver a utilizar uma região diferente, atualize o valor para `FetchTokenUri`. Para obter uma lista completa, consulte [regiões](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

```csharp
public class Authentication
{
    private string subscriptionKey;
    private string tokenFetchUri;

    public Authentication(string tokenFetchUri, string subscriptionKey)
    {
        if (string.IsNullOrWhiteSpace(tokenFetchUri))
        {
            throw new ArgumentNullException(nameof(tokenFetchUri));
        }
        if (string.IsNullOrWhiteSpace(subscriptionKey))
        {
            throw new ArgumentNullException(nameof(subscriptionKey));
        }
        this.tokenFetchUri = tokenFetchUri;
        this.subscriptionKey = subscriptionKey;
    }

    public async Task<string> FetchTokenAsync()
    {
        using (var client = new HttpClient())
        {
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", this.subscriptionKey);
            UriBuilder uriBuilder = new UriBuilder(this.tokenFetchUri);

            var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null).ConfigureAwait(false);
            return await result.Content.ReadAsStringAsync().ConfigureAwait(false);
        }
    }
}
```

> [!NOTE]
> Para obter mais informações sobre autenticação, consulte [autenticar com um token de acesso](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-an-authentication-token).

## <a name="get-an-access-token-and-set-the-host-url"></a>Obter um token de acesso e definir o URL de anfitrião

Localize `static void Main(string[] args)` e substitua-o por `static async Task Main(string[] args)`.

Em seguida, copie este código para o método principal. Ele faz algumas coisas, mas mais importante, aceita texto como uma entrada e de chamadas a `Authentication` função para a sua chave de subscrição para um token de acesso do exchange. Se algo der errado, o erro está impresso no console.

Certifique-se de adicionar a chave de subscrição antes de executar a aplicação.

```csharp
// Prompts the user to input text for TTS conversion
Console.Write("What would you like to convert to speech? ");
string text = Console.ReadLine();

// Gets an access token
string accessToken;
Console.WriteLine("Attempting token exchange. Please wait...\n");

// Add your subscription key here
// If your resource isn't in WEST US, change the endpoint
Authentication auth = new Authentication("https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken", "YOUR_SUBSCRIPTION_KEY");
try
{
    accessToken = await auth.FetchTokenAsync().ConfigureAwait(false);
    Console.WriteLine("Successfully obtained an access token. \n");
}
catch (Exception ex)
{
    Console.WriteLine("Failed to obtain an access token.");
    Console.WriteLine(ex.ToString());
    Console.WriteLine(ex.Message);
    return;
}
```

Em seguida, defina o anfitrião e a rota para voz:

```csharp
string host = "https://westus.tts.speech.microsoft.com/cognitiveservices/v1";
```

## <a name="build-the-ssml-request"></a>Criar o pedido SSML

Texto é enviado como o corpo de um `POST` pedido. Com SSML, pode especificar a voz e linguagem. Neste início rápido, vamos utilizar SSML com o idioma definido como `en-US` e a voz como `ZiraRUS`. Vamos construir SSML para o seu pedido:

```csharp
string body = @"<speak version='1.0' xmlns='https://www.w3.org/2001/10/synthesis' xml:lang='en-US'>
              <voice name='Microsoft Server Speech Text to Speech Voice (en-US, ZiraRUS)'>" +
              text + "</voice></speak>";
```

> [!NOTE]
> Este exemplo utiliza o `ZiraRUS` tipo de voz. Para uma lista completa de Microsoft fornecidas vozes/idiomas, consulte [suporte de idiomas](language-support.md). Se estiver interessado em criar uma voz exclusiva, reconhecível para sua marca, veja [criar tipos de voz personalizada](how-to-customize-voice-font.md).

## <a name="instantiate-the-client-make-a-request-and-save-synthesized-audio-to-a-file"></a>Criar uma instância do cliente, fazer um pedido e salvar áudio sintetizado num arquivo

Há muita coisa acontece no código de exemplo. Vamos analisar rapidamente o que acontece:

* O cliente e pedido são instanciadas.
* O método HTTP está definido como `POST`.
* Cabeçalhos necessários são adicionados ao pedido.
* A solicitação é enviada e o código de estado é verificado.
* A resposta é ler de forma assíncrona e escrita num ficheiro denominado sample.wav.

Copie esse código no seu projeto. Lembre-se de que substitua o valor do `User-Agent` cabeçalho com o nome do seu recurso no portal do Azure.

```csharp
using (var client = new HttpClient())
{
    using (var request = new HttpRequestMessage())
    {
        // Set the HTTP method
        request.Method = HttpMethod.Post;
        // Construct the URI
        request.RequestUri = new Uri(host);
        // Set the content type header
        request.Content = new StringContent(body, Encoding.UTF8, "application/ssml+xml");
        // Set additional header, such as Authorization and User-Agent
        request.Headers.Add("Authorization", "Bearer " + accessToken);
        request.Headers.Add("Connection", "Keep-Alive");
        // Update your resource name
        request.Headers.Add("User-Agent", "YOUR_RESOURCE_NAME");
        request.Headers.Add("X-Microsoft-OutputFormat", "riff-24khz-16bit-mono-pcm");
        // Create a request
        Console.WriteLine("Calling the TTS service. Please wait... \n");
        using (var response = await client.SendAsync(request).ConfigureAwait(false))
        {
            response.EnsureSuccessStatusCode();
            // Asynchronously read the response
            using (var dataStream = await response.Content.ReadAsStreamAsync().ConfigureAwait(false))
            {
                Console.WriteLine("Your speech file is being written to file...");
                using (var fileStream = new FileStream(@"sample.wav", FileMode.Create, FileAccess.Write, FileShare.Write))
                {
                    await dataStream.CopyToAsync(fileStream).ConfigureAwait(false);
                    fileStream.Close();
                }
                Console.WriteLine("\nYour file is ready. Press any key to exit.");
                Console.ReadLine();
            }
        }
    }
}
```

## <a name="run-the-sample-app"></a>Execute a aplicação de exemplo

É isso, está pronto para executar a aplicação de texto para voz. A partir da linha de comandos (ou sessão de terminal), navegue para o diretório de projeto e execute:

```console
dotnet run
```

Se tiver êxito, o ficheiro de voz é guardado na pasta do projeto. Reproduzi-lo usando o leitor de multimédia favorito.

## <a name="clean-up-resources"></a>Limpar recursos

Se codificou a chave de subscrição no seu programa, certifique-se de que remove a chave de subscrição quando tiver terminado este início rápido.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Explore exemplos de .NET no GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/NETCore)

## <a name="see-also"></a>Ver também

* [Referência da API de conversão de texto em voz](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#text-to-speech-api)
* [Criar tipos de voz personalizada](how-to-customize-voice-font.md)
* [Exemplos de voz de registo para criar uma voz personalizada](record-custom-voice-samples.md)
