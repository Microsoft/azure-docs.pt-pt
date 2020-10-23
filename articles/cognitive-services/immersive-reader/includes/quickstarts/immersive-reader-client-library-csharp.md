---
title: Início rápido da biblioteca de clientes Imersivo Reader C#
titleSuffix: Azure Cognitive Services
description: Neste quickstart, você constrói uma aplicação web de raiz e adiciona a funcionalidade Imersiva da API do Leitor.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: include
ms.date: 09/14/2020
ms.author: nitinme
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 1c7587a4434a84c6934179393baa5ae684f9b1fd
ms.sourcegitcommit: 93329b2fcdb9b4091dbd632ee031801f74beb05b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92116732"
---
[O Immersive Reader](https://www.onenote.com/learningtools) é uma ferramenta inclusivamente concebida que implementa técnicas comprovadas para melhorar a compreensão da leitura para novos leitores, aprendizes de línguas e pessoas com diferenças de aprendizagem como a dislexia. Pode utilizar o Leitor Imersivo nas suas aplicações para isolar texto para melhorar o foco, exibir imagens para palavras comumente usadas, destacar partes da fala, ler texto selecionado em voz alta, traduzir palavras e frases em tempo real, e muito mais.

Neste quickstart, você constrói uma aplicação web de raiz e integra o Leitor Imersivo usando a biblioteca de clientes Immersive Reader. Uma amostra completa deste arranque rápido está disponível [no GitHub.](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/quickstart-csharp)

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services)
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads)
* Um recurso imersivo do Leitor configurado para a autenticação do Azure Ative Directory. Siga [estas instruções](../../how-to-create-immersive-reader.md) para se preparar. Você precisará de alguns dos valores criados aqui ao configurar as propriedades do projeto de amostra. Guarde a saída da sua sessão num ficheiro de texto para referência futura.

## <a name="create-a-web-app-project"></a>Criar um projeto de aplicação Web

Crie um novo projeto no Visual Studio, utilizando o modelo de aplicação web core ASP.NET com o Model-View-Controller incorporado e ASP.NET Core 2.1. Nomeie o projeto "QuickstartSampleWebApp".

![Novo projeto - C #](../../media/quickstart-csharp/1-createproject.png)

![Configure novo projeto - C #](../../media/quickstart-csharp/2-configureproject.png)

![Aplicação web New ASP.NET Core - C #](../../media/quickstart-csharp/3-createmvc.png)

## <a name="set-up-authentication"></a>Configurar autenticação

### <a name="configure-authentication-values"></a>Configurar valores de autenticação

Clique com o botão direito no projeto no _Solution Explorer_ e escolha Gerir **os Segredos do Utilizador.** Isto abrirá um ficheiro chamado _secrets.js._ Este ficheiro não é controlado pelo controlo de fontes. Sabia mais [aqui](https://docs.microsoft.com/aspnet/core/security/app-secrets?view=aspnetcore-3.1&tabs=windows&preserve-view=true). Substitua o conteúdo de _secrets.js_ pelo seguinte, fornecendo os valores dados quando criou o seu recurso Leitor Imersivo.

```json
{
  "TenantId": "YOUR_TENANT_ID",
  "ClientId": "YOUR_CLIENT_ID",
  "ClientSecret": "YOUR_CLIENT_SECRET",
  "Subdomain": "YOUR_SUBDOMAIN"
}
```

### <a name="install-active-directory-nuget-package"></a>Instalar pacote Ative Directory NuGet

O código a seguir utiliza objetos do pacote **Microsoft.IdentityModel.Clients.ActiveDirectory** NuGet, pelo que terá de adicionar uma referência a esse pacote no seu projeto.

Abra a consola NuGet Package Manager a partir de **Ferramentas -> NuGet Package Manager -> Package Manager e** executar o seguinte comando:

```powershell
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 5.2.0
```

### <a name="update-the-controller-to-acquire-the-token"></a>Atualizar o controlador para adquirir o token 

Abra _os controladores\HomeController.cs_, e adicione o seguinte código após a _utilização_ das declarações na parte superior do ficheiro.

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Agora, vamos configurar o controlador para obter os valores AD Azure a partir de _secrets.jsem_. No topo da classe _HomeController,_ ```public class HomeController : Controller {``` depois, adicione o seguinte código.

```csharp
private readonly string TenantId;     // Azure subscription TenantId
private readonly string ClientId;     // Azure AD ApplicationId
private readonly string ClientSecret; // Azure AD Application Service Principal password
private readonly string Subdomain;    // Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')

public HomeController(Microsoft.Extensions.Configuration.IConfiguration configuration)
{
    TenantId = configuration["TenantId"];
    ClientId = configuration["ClientId"];
    ClientSecret = configuration["ClientSecret"];
    Subdomain = configuration["Subdomain"];

    if (string.IsNullOrWhiteSpace(TenantId))
    {
        throw new ArgumentNullException("TenantId is null! Did you add that info to secrets.json?");
    }

    if (string.IsNullOrWhiteSpace(ClientId))
    {
        throw new ArgumentNullException("ClientId is null! Did you add that info to secrets.json?");
    }

    if (string.IsNullOrWhiteSpace(ClientSecret))
    {
        throw new ArgumentNullException("ClientSecret is null! Did you add that info to secrets.json?");
    }

    if (string.IsNullOrWhiteSpace(Subdomain))
    {
        throw new ArgumentNullException("Subdomain is null! Did you add that info to secrets.json?");
    }
}

/// <summary>
/// Get an Azure AD authentication token
/// </summary>
private async Task<string> GetTokenAsync()
{
    string authority = $"https://login.windows.net/{TenantId}";
    const string resource = "https://cognitiveservices.azure.com/";

    AuthenticationContext authContext = new AuthenticationContext(authority);
    ClientCredential clientCredential = new ClientCredential(ClientId, ClientSecret);

    AuthenticationResult authResult = await authContext.AcquireTokenAsync(resource, clientCredential);

    return authResult.AccessToken;
}

[HttpGet]
public async Task<JsonResult> GetTokenAndSubdomain()
{
    try
    {
        string tokenResult = await GetTokenAsync();

        return new JsonResult(new { token = tokenResult, subdomain = Subdomain });
    }
    catch (Exception e)
    {
        string message = "Unable to acquire Azure AD token. Check the debugger for more information.";
        Debug.WriteLine(message, e);
        return new JsonResult(new { error = message });
    }
}
```

## <a name="add-sample-content"></a>Adicionar conteúdo de amostra
Primeiro, abra _as vistas\Shared\Layout.cshtml_. Antes da linha ```</head>``` , adicione o seguinte código:

```html
@RenderSection("Styles", required: false)
```

Agora, vamos adicionar conteúdo de amostra a esta aplicação web. _Vistas Abertas\Home\Index.cshtml_ e substituir todo o código gerado automaticamente por esta amostra:

```html
@{
    ViewData["Title"] = "Immersive Reader C# Quickstart";
}

@section Styles {
    <style type="text/css">
        .immersive-reader-button {
            background-color: white;
            margin-top: 5px;
            border: 1px solid black;
            float: right;
        }
    </style>
}

<div class="container">
    <button class="immersive-reader-button" data-button-style="iconAndText" data-locale="en"></button>

    <h1 id="ir-title">About Immersive Reader</h1>
    <div id="ir-content" lang="en-us">
        <p>
            Immersive Reader is a tool that implements proven techniques to improve reading comprehension for emerging readers, language learners, and people with learning differences.
            The Immersive Reader is designed to make reading more accessible for everyone. The Immersive Reader
            <ul>
                <li>
                    Shows content in a minimal reading view
                </li>
                <li>
                    Displays pictures of commonly used words
                </li>
                <li>
                    Highlights nouns, verbs, adjectives, and adverbs
                </li>
                <li>
                    Reads your content out loud to you
                </li>
                <li>
                    Translates your content into another language
                </li>
                <li>
                    Breaks down words into syllables
                </li>
            </ul>
        </p>
        <h3>
            The Immersive Reader is available in many languages.
        </h3>
        <p lang="es-es">
            El Lector inmersivo está disponible en varios idiomas.
        </p>
        <p lang="zh-cn">
            沉浸式阅读器支持许多语言
        </p>
        <p lang="de-de">
            Der plastische Reader ist in vielen Sprachen verfügbar.
        </p>
        <p lang="ar-eg" dir="rtl" style="text-align:right">
            يتوفر \"القارئ الشامل\" في العديد من اللغات.
        </p>
    </div>
</div>
```

Note que todo o texto tem um atributo **lang,** que descreve as línguas do texto. Este atributo ajuda o Leitor Imersivo a fornecer características relevantes da linguagem e gramática.

## <a name="add-javascript-to-handle-launching-immersive-reader"></a>Adicione JavaScript para lidar com o lançamento do Leitor Imersivo

A biblioteca Immersive Reader fornece funcionalidades como o lançamento do Leitor Imersivo e a renderização de botões imersivos reader. Sabia mais [aqui](https://docs.microsoft.com/azure/cognitive-services/immersive-reader/reference).

Na parte inferior do _Views\Home\Index.cshtml,_ adicione o seguinte código:

```html
@section Scripts
{
    <script src="https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.1.0.0.js"></script>
    <script>
        function getTokenAndSubdomainAsync() {
            return new Promise(function (resolve, reject) {
                $.ajax({
                    url: "@Url.Action("GetTokenAndSubdomain", "Home")",
                    type: "GET",
                    success: function (data) {
                        if (data.error) {
                            reject(data.error);
                        } else {
                            resolve(data);
                        }
                    },
                    error: function (err) {
                        reject(err);
                    }
                });
            });
        }
    
        $(".immersive-reader-button").click(function () {
            handleLaunchImmersiveReader();
        });
    
        function handleLaunchImmersiveReader() {
            getTokenAndSubdomainAsync()
                .then(function (response) {
                    const token = response["token"];
                    const subdomain = response["subdomain"];
    
                    // Learn more about chunk usage and supported MIME types https://docs.microsoft.com/en-us/azure/cognitive-services/immersive-reader/reference#chunk
                    const data = {
                        title: $("#ir-title").text(),
                        chunks: [{
                            content: $("#ir-content").html(),
                            mimeType: "text/html"
                        }]
                    };
    
                    // Learn more about options https://docs.microsoft.com/en-us/azure/cognitive-services/immersive-reader/reference#options
                    const options = {
                        "onExit": exitCallback,
                        "uiZIndex": 2000
                    };
    
                    ImmersiveReader.launchAsync(token, subdomain, data, options)
                        .catch(function (error) {
                            alert("Error in launching the Immersive Reader. Check the console.");
                            console.log(error);
                        });
                })
                .catch(function (error) {
                    alert("Error in getting the Immersive Reader token and subdomain. Check the console.");
                    console.log(error);
                });
        }
    
        function exitCallback() {
            console.log("This is the callback function. It is executed when the Immersive Reader closes.");
        }
    </script>
}
```

## <a name="build-and-run-the-app"></a>Compilar e executar a aplicação

A partir da barra de menus, selecione **Debug > Start Debugging**, ou prima **F5** para iniciar a aplicação.

No seu navegador, deverá ver:

![App de amostra - C #](../../media/quickstart-csharp/4-buildapp.png)

## <a name="launch-the-immersive-reader"></a>Lançar o Leitor Imersivo

Ao clicar no botão "Leitor Imersivo", verá o Leitor Imersivo lançado com o conteúdo na página.

![Leitor Imersivo - C #](../../media/quickstart-csharp/5-viewimmersivereader.png)

## <a name="next-steps"></a>Passos seguintes

* Explore o [SDK do leitor imersivo](https://github.com/microsoft/immersive-reader-sdk) e a [referência SDK do leitor imersivo](../../reference.md)
