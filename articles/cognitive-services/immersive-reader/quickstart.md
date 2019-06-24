---
title: 'Início rápido: Criar uma aplicação web que inicia o leitor de imersão (C#)'
titlesuffix: Azure Cognitive Services
description: Neste início rápido, criar uma aplicação web a partir do zero e adicionar a funcionalidade de leitor envolvente e experimental.
services: cognitive-services
author: metanMSFT
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: quickstart
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: 77d95383c801038c256ccb2bf386ddf06048cf78
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/21/2019
ms.locfileid: "67311797"
---
# <a name="quickstart-create-a-web-app-that-launches-the-immersive-reader-c"></a>Início rápido: Criar uma aplicação web que inicia o leitor de imersão (C#)

O [leitor envolventes](https://www.onenote.com/learningtools) é uma ferramenta projetada, inclusivamente que implementa técnicas comprovadas para melhorar a compreensão de leitura.

Neste início rápido, criar uma aplicação web a partir do zero e integrar o leitor envolventes com o SDK de leitor envolvente e experimental. Um completo exemplo funcional deste início rápido está disponível [aqui](https://github.com/Microsoft/immersive-reader-sdk/samples/quickstart-csharp).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads)
* Uma chave de subscrição para o leitor envolvente. Obter uma ao seguir [estas instruções](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account).

## <a name="create-a-web-app-project"></a>Criar um projeto de aplicação web

Crie um novo projeto no Visual Studio, utilizando o modelo de aplicação Web ASP.NET Core com incorporada Model-View-Controller.

![Novo Projeto](./media/vswebapp.png)

![Nova aplicação de Web do ASP.NET Core](./media/vsmvc.png)

## <a name="acquire-an-access-token"></a>Adquirir um token de acesso

Precisa de sua chave de subscrição e o ponto final para a próxima etapa. Pode encontrar a chave de subscrição na página chaves do seu recurso de leitor de imersão no portal do Azure. Pode encontrar o ponto final na página de descrição geral.

Com o botão direito no projeto no _Explorador de soluções_ e escolha **gerir segredos do utilizador**. Esta ação irá abrir um arquivo chamado _secrets.json_. Substitua o conteúdo desse ficheiro com o seguinte, fornecendo a sua chave de subscrição e o ponto final, quando apropriado.

```json
{
  "SubscriptionKey": YOUR_SUBSCRIPTION_KEY,
  "Endpoint": YOUR_ENDPOINT
}
```

Open _Controllers\HomeController.cs_e substitua o `HomeController` classe com o código a seguir.

```csharp
public class HomeController : Controller
{
    private readonly string SubscriptionKey;
    private readonly string Endpoint;

    public HomeController(Microsoft.Extensions.Configuration.IConfiguration configuration)
    {
        SubscriptionKey = configuration["SubscriptionKey"];
        Endpoint = configuration["Endpoint"];

        if (string.IsNullOrEmpty(Endpoint) || string.IsNullOrEmpty(SubscriptionKey))
        {
            throw new ArgumentNullException("Endpoint or subscriptionKey is null!");
        }
    }

    public IActionResult Index()
    {
        return View();
    }

    [Route("token")]
    public async Task<string> Token()
    {
        return await GetTokenAsync();
    }

    /// <summary>
    /// Exchange your Azure subscription key for an access token
    /// </summary>
    private async Task<string> GetTokenAsync()
    {
        using (var client = new System.Net.Http.HttpClient())
        {
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", SubscriptionKey);
            using (var response = await client.PostAsync(Endpoint, null))
            {
                return await response.Content.ReadAsStringAsync();
            }
        }
    }
}
```

## <a name="add-sample-content"></a>Adicionar conteúdo de exemplo

Agora, vamos adicionar alguns conteúdos de exemplo para esta aplicação web. Open _Views\Home\Index.cshtml_ e substitua o código gerado automaticamente com este exemplo:

```html
<h1 id='title'>Geography</h1>
<span id='content'>
    <p>The study of Earth's landforms is called physical geography. Landforms can be mountains and valleys. They can also be glaciers, lakes or rivers. Landforms are sometimes called physical features. It is important for students to know about the physical geography of Earth. The seasons, the atmosphere and all the natural processes of Earth affect where people are able to live. Geography is one of a combination of factors that people use to decide where they want to live.</p>
</span>

<div class='immersive-reader-button' data-button-style='iconAndText' onclick='launchImmersiveReader()'></div>

@section scripts {
<script type='text/javascript' src='https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.0.0.1.js'></script>
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

    async function launchImmersiveReader() {
        const content = {
            title: document.getElementById('title').innerText,
            chunks: [ {
                content: document.getElementById('content').innerText,
                lang: 'en'
            } ]
        };

        const token = await getImmersiveReaderTokenAsync();
        ImmersiveReader.launchAsync(token, content, { uiZIndex: 1000000 });
    }
</script>
}
```

## <a name="build-and-run-the-app"></a>Compilar e executar a aplicação

Na barra de menus, selecione **depurar > Iniciar depuração**, ou prima **F5** para iniciar o aplicativo.

No seu navegador, deverá ver:

![Aplicação de exemplo](./media/quickstart-result.png)

Ao clicar no botão "Leitor de imersão", verá o leitor de imersão iniciado com o conteúdo na página.

![Leitura Avançada](./media/quickstart-immersive-reader.png)

## <a name="next-steps"></a>Passos Seguintes

* Ver os [tutorial](./tutorial.md) para ver o que mais pode fazer com o SDK de leitor envolventes
* Explorar o [leitor envolvente SDK](https://github.com/Microsoft/immersive-reader-sdk) e o [envolventes leitor a referência do SDK](./reference.md)