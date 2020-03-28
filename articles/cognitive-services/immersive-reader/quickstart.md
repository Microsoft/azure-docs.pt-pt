---
title: 'Quickstart: Criar uma aplicação web que lança o Leitor Imersivo com C #'
titleSuffix: Azure Cognitive Services
description: Neste arranque rápido, você constrói uma aplicação web de raiz e adiciona a funcionalidade API imersiva do leitor.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: quickstart
ms.date: 01/14/2020
ms.author: metan
ms.openlocfilehash: 8dd8459922caa9f765d59bc28fbf050b86834b46
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76845237"
---
# <a name="quickstart-create-a-web-app-that-launches-the-immersive-reader-c"></a>Quickstart: Criar uma aplicação web que lança o Leitor Imersivo (C#)

O [Leitor Imersivo](https://www.onenote.com/learningtools) é uma ferramenta inclusivamente projetada que implementa técnicas comprovadas para melhorar a compreensão da leitura.

Neste arranque rápido, você constrói uma aplicação web de raiz e integra o Leitor Imersivo utilizando o SDK de leitor imersivo. Uma amostra completa deste arranque rápido está disponível [aqui.](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/quickstart-csharp)

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads)
* Um recurso Imersivo do Leitor configurado para autenticação de Diretório Ativo Azure. Siga [estas instruções](./how-to-create-immersive-reader.md) para ser configurado. Você precisará de alguns dos valores criados aqui ao configurar as propriedades do projeto de amostra. Guarde a saída da sua sessão num ficheiro de texto para referência futura.

## <a name="create-a-web-app-project"></a>Criar um projeto de aplicação web

Crie um novo projeto no Visual Studio, utilizando o modelo de aplicação web ASP.NET Core com model-view-controller incorporado e ASP.NET Core 2.1. Nomeie o projeto "QuickstartSampleWebApp".

![Novo Projeto](./media/quickstart-csharp/1-createproject.png)

![Configurar novo projeto](./media/quickstart-csharp/2-configureproject.png)

![Nova aplicação web do núcleo ASP.NET](./media/quickstart-csharp/3-createmvc.png)

## <a name="set-up-authentication"></a>Configurar autenticação

### <a name="configure-authentication-values"></a>Configurar valores de autenticação

Clique no direito do projeto no Explorador de _Soluções_ e escolha **Gerir segredos**de utilizador . Isto abrirá um ficheiro chamado _Secrets.json._ Este ficheiro não está verificado no controlo de origem. Sabia mais [aqui](https://docs.microsoft.com/aspnet/core/security/app-secrets?view=aspnetcore-3.1&tabs=windows). Substitua o conteúdo do _secrets.json_ pelo seguinte, fornecendo os valores dados quando criou o seu recurso Imersivo leitor.

```json
{
  "TenantId": "YOUR_TENANT_ID",
  "ClientId": "YOUR_CLIENT_ID",
  "ClientSecret": "YOUR_CLIENT_SECRET",
  "Subdomain": "YOUR_SUBDOMAIN"
}
```

### <a name="add-the-microsoftidentitymodelclientsactivedirectory-nuget-package"></a>Adicione o pacote Microsoft.IdentityModel.Clients.ActiveDirectory NuGet

O código seguinte utiliza objetos do pacote **Microsoft.IdentityModel.Clients.ActiveDirectory** NuGet para que tenha de adicionar uma referência a esse pacote no seu projeto.

Abra a consola nuGet Package Manager a partir de **ferramentas -> NuGet Package Manager - > Consola de Gestor** de Pacotes e executar o seguinte comando:

```powershell
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 5.2.0
```

### <a name="update-the-controller-to-acquire-the-token"></a>Atualizar o controlador para adquirir o símbolo 

Controladores _abertos\HomeController.cs_, e adicione o seguinte código após as declarações _de utilização_ na parte superior do ficheiro.

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Agora, vamos configurar o controlador para obter os valores da AD Azure a partir de _secrets.json_. No topo da classe _HomeController,_ depois, ```public class HomeController : Controller {```adicione o seguinte código.

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

## <a name="add-sample-content"></a>Adicionar conteúdo da amostra
Primeiro, _vistas abertas\Shared\Layout.cshtml_. Antes da ```</head>```linha, adicione o seguinte código:

```html
@RenderSection("Styles", required: false)
```

Agora, vamos adicionar conteúdo de amostra a esta aplicação web. Open _Views\Home\Index.cshtml_ e substitua todo o código gerado automaticamente por esta amostra:

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

Note que todo o texto tem um atributo **de lang,** que descreve as línguas do texto. Este atributo ajuda o Leitor Imersivo a fornecer características linguísticas e gramaticais relevantes.

## <a name="add-javascript-to-handle-launching-the-immersive-reader"></a>Adicione javaScript para lidar com o lançamento do Leitor Imersivo

A biblioteca Imersiva reader fornece funcionalidades como o lançamento do Leitor Imersivo e a renderização dos botões Imersivos do Leitor. Sabia mais [aqui](https://docs.microsoft.com/azure/cognitive-services/immersive-reader/reference).

Na parte inferior de _Views\Home\Index.cshtml,_ adicione o seguinte código:

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

A partir da barra de menus, selecione **Debug > Iniciar depuração,** ou prima **F5** para iniciar a aplicação.

No seu navegador, deve ver:

![App de amostras](./media/quickstart-csharp/4-buildapp.png)

## <a name="launch-the-immersive-reader"></a>Lançar o Leitor Imersivo

Quando clicar no botão "Leitor Imersivo", verá o Leitor Imersivo lançado com o conteúdo na página.

![Leitura Avançada](./media/quickstart-csharp/5-viewimmersivereader.png)

## <a name="next-steps"></a>Passos seguintes

* Veja o [Node.js quickstart](./quickstart-nodejs.md) para ver o que mais pode fazer com o SDK de Leitor Imersivo usando Node.js
* Veja o [tutorial](./tutorial-python.md) python para ver o que mais pode fazer com o SDK de Leitor Imersivo usando Python
* Veja o [tutorial do iOS](./tutorial-ios-picture-immersive-reader.md) para ver o que mais pode fazer com o SDK de Leitor Imersivo usando Swift
* Explore o [SDK imersivo do leitor](https://github.com/microsoft/immersive-reader-sdk) e a [referência SDK do leitor imersivo](./reference.md)