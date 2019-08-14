---
title: 'Início rápido: Criar um aplicativo Web que inicia o leitor de imersão comC#'
titlesuffix: Azure Cognitive Services
description: Neste guia de início rápido, você cria um aplicativo Web do zero e adiciona a funcionalidade da API do leitor de imersão.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: quickstart
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: 0d67385975db13539c28745eede4fb288a0daedd
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68989148"
---
# <a name="quickstart-create-a-web-app-that-launches-the-immersive-reader-c"></a>Início rápido: Criar um aplicativo Web que inicia o leitor de imersãoC#()

O [leitor de imersão](https://www.onenote.com/learningtools) é uma ferramenta projetada de inclusivamente que implementa técnicas comprovadas para melhorar a compreensão da leitura.

Neste guia de início rápido, você cria um aplicativo Web do zero e integra o leitor de imersão usando o SDK do leitor de imersão. Um exemplo funcional completo deste guia de início rápido está disponível [aqui](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/quickstart-csharp).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads)
* Um recurso de leitor de imersão configurado para autenticação do Azure Active Directory (AD do Azure). Siga [estas instruções](./azure-active-directory-authentication.md) para configurar. Você precisará de alguns dos valores criados aqui ao configurar as propriedades do projeto de exemplo. Salve a saída da sessão em um arquivo de texto para referência futura.

## <a name="create-a-web-app-project"></a>Criar um projeto de aplicativo Web

Crie um novo projeto no Visual Studio usando o modelo de aplicativo Web ASP.NET Core com o Model-View-Controller interno.

![Novo Projeto](./media/vswebapp.png)

![Novo ASP.NET Core aplicativo Web](./media/vsmvc.png)

## <a name="acquire-an-azure-ad-authentication-token"></a>Adquirir um token de autenticação do Azure AD

Você precisa de alguns valores da etapa de pré-requisito de configuração de autenticação do Azure AD acima para esta parte. Consulte novamente o arquivo de texto que você salvou dessa sessão.

````text
TenantId     => Azure subscription TenantId
ClientId     => Azure AD ApplicationId
ClientSecret => Azure AD Application Service Principal password
Subdomain    => Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')
````

Clique com o botão direito do mouse no projeto no _Gerenciador de soluções_ e escolha **gerenciar segredos do usuário**. Isso abrirá um arquivo chamado _segredos. JSON_. Substitua o conteúdo desse arquivo pelo seguinte, fornecendo os valores de propriedade personalizada acima.

```json
{
  "TenantId": YOUR_TENANT_ID,
  "ClientId": YOUR_CLIENT_ID,
  "ClientSecret": YOUR_CLIENT_SECRET,
  "Subdomain": YOUR_SUBDOMAIN
}
```

Abra _Controllers\HomeController.cs_e substitua o arquivo pelo código a seguir.

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.IdentityModel.Clients.ActiveDirectory;

namespace QuickstartSampleWebApp.Controllers
{
    public class HomeController : Controller
    {
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

        public IActionResult Index()
        {
            return View();
        }

        [Route("subdomain")]
        public string GetSubdomain()
        {
            return Subdomain;
        }

        [Route("token")]
        public async Task<string> GetToken()
        {
            return await GetTokenAsync();
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
    }
}
```

## <a name="add-the-microsoftidentitymodelclientsactivedirectory-nuget-package"></a>Adicione o pacote NuGet Microsoft. IdentityModel. clients. ActiveDirectory

O código acima usa objetos do pacote NuGet **Microsoft. IdentityModel. clients. ActiveDirectory** para que você precise adicionar uma referência a esse pacote em seu projeto.

Abra o console do Gerenciador de pacotes NuGet em **ferramentas – > Gerenciador de pacotes NuGet-> console do Gerenciador de pacotes** e digite o seguinte:

```powershell
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 5.1.0
```

## <a name="add-sample-content"></a>Adicionar conteúdo de exemplo

Agora, adicionaremos um exemplo de conteúdo a esse aplicativo Web. Abra _Views\Home\Index.cshtml_ e substitua o código gerado automaticamente por este exemplo:

```html
<h1 id='title'>Geography</h1>
<span id='content'>
    <p>The study of Earth's landforms is called physical geography. Landforms can be mountains and valleys. They can also be glaciers, lakes or rivers. Landforms are sometimes called physical features. It is important for students to know about the physical geography of Earth. The seasons, the atmosphere and all the natural processes of Earth affect where people are able to live. Geography is one of a combination of factors that people use to decide where they want to live.</p>
</span>

<div class='immersive-reader-button' data-button-style='iconAndText' onclick='launchImmersiveReader()'></div>

@section scripts {
    <script type='text/javascript' src='https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.0.0.2.js'></script>
    <script type='text/javascript' src='https://code.jquery.com/jquery-3.3.1.min.js'></script>
    <script type='text/javascript'>
    function getImmersiveReaderTokenAsync() {
        return new Promise((resolve) => {
            $.ajax({
                url: '/token',
                type: 'GET',
                success: token => {
                    resolve(token);
                }
            });
        });
    }

    function getSubdomainAsync() {
        return new Promise((resolve) => {
            $.ajax({
                url: '/subdomain',
                type: 'GET',
                success: subdomain => {
                    resolve(subdomain);
                }
            });
        });
    }

    async function launchImmersiveReader() {
        const content = {
            title: document.getElementById('title').innerText,
            chunks: [ {
                content: document.getElementById('content').innerText,
                lang: 'en'
            } ]
        };

        const token = await getImmersiveReaderTokenAsync();
        var subdomain = await getSubdomainAsync();

        ImmersiveReader.launchAsync(token, subdomain, content, { uiZIndex: 1000000 });
    }
    </script>
}
```

## <a name="build-and-run-the-app"></a>Compilar e executar a aplicação

Na barra de menus, selecione **depurar > iniciar depuração**ou pressione **F5** para iniciar o aplicativo.

No navegador, você deve ver:

![Aplicativo de exemplo](./media/quickstart-result.png)

Ao clicar no botão "leitor de imersão", você verá o leitor de imersão iniciado com o conteúdo na página.

![Leitura Avançada](./media/quickstart-immersive-reader.png)

## <a name="next-steps"></a>Passos Seguintes

* Veja o [tutorial do node. js](./tutorial-nodejs.md) para ver o que mais você pode fazer com o SDK do leitor de imersão usando node. js
* Veja o [tutorial do Python](./tutorial-python.md) para ver o que mais você pode fazer com o SDK do leitor de imersão usando o Python
* Veja o [tutorial do IOS](./tutorial-ios-picture-immersive-reader.md) para ver o que mais você pode fazer com o SDK do leitor de imersão usando o Swift
* Explore o [SDK do leitor de imersão](https://github.com/microsoft/immersive-reader-sdk) e a [referência do SDK do leitor de imersão](./reference.md)
