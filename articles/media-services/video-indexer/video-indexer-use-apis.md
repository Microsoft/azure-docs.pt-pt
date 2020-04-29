---
title: Utilizar a API do Video Indexer
titleSuffix: Azure Media Services
description: Este artigo descreve como começar com a API do Indexer de Vídeo da Azure Media Services.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 04/13/2020
ms.author: juliako
ms.openlocfilehash: 82bdb177cf4d9c400d1b13ba7178658089950557
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81314341"
---
# <a name="tutorial-use-the-video-indexer-api"></a>Tutorial: Utilizar a API do Video Indexer

O Video Indexer consolida várias tecnologias de inteligência artificial áudio e vídeo (IA) oferecidas pela Microsoft num único serviço integrado, tornando o desenvolvimento mais simples. As APIs são projetadas para permitir que os desenvolvedores se concentrem em consumir tecnologias de IA dos Media sem se preocuparem com a escala, alcance global, disponibilidade e fiabilidade das plataformas cloud. Pode utilizar a API para carregar os seus ficheiros, obter informações detalhadas sobre o vídeo, obter URLs de insights incorporados e widgets de jogadores, e muito mais.

Ao criar uma conta de Indexer de Vídeo, pode escolher uma conta de teste gratuita (onde obtém um certo número de minutos de indexação gratuitos) ou uma opção paga (onde não está limitado pela quota). Com um teste gratuito, o Video Indexer fornece até 600 minutos de indexação gratuita aos utilizadores do site e até 2400 minutos de indexação gratuita aos utilizadores de API. Com uma opção paga, cria uma conta De Indexer de Vídeo que está [ligada à sua subscrição Azure e a uma conta Azure Media Services.](connect-to-azure.md) Irá pagar pelos minutos de indexação e pelas cobranças relacionadas com a conta dos Serviços de Multimédia do Azure.

Este artigo mostra como os programadores podem tirar partido da [API do Video Indexer](https://api-portal.videoindexer.ai/).

## <a name="subscribe-to-the-api"></a>Subscrever a API

1. Inicie sessão no [Video Indexer Developer Portal](https://api-portal.videoindexer.ai/).
    
    ![Inscreva-se no Portal de Desenvolvimento do Indexante de Vídeo](./media/video-indexer-use-apis/video-indexer-api01.png)

   > [!Important]
   > * Tem de utilizar o mesmo fornecedor que utilizou quando se inscreveu no Video Indexer.
   > * As contas pessoais do Google e da Microsoft (Outlook/Live) só podem ser utilizadas para contas de teste. As contas ligadas ao Azure necessitam do Azure AD.
   > * Só pode haver uma conta ativa por e-mail. Se um utilizador tentar user@gmail.com iniciar sessão com user@gmail.com o LinkedIn e mais tarde com o Google, este apresentará uma página de erro, dizendo que o utilizador já existe.

2. Subscreva.

    Selecione o separador [Produtos.](https://api-portal.videoindexer.ai/products) Em seguida, selecione Autorização e subscreva.
    
    ![Separador de produtos no portal de desenvolvedor de indicadores de vídeo](./media/video-indexer-use-apis/video-indexer-api02.png)

    > [!NOTE]
    > Os utilizadores novos estão automaticamente subscritos em Autorização.
    
    Assim que se inscrever, pode ver a sua subscrição e as suas chaves primárias e secundárias. As chaves devem ser protegidas. As chaves só devem ser utilizadas pelo seu código do servidor. Não devem estar disponíveis do lado do cliente (.js, .html, e assim por diante).

    ![Subscrição e chaves no Portal de Desenvolvimento do Indexante de Vídeo](./media/video-indexer-use-apis/video-indexer-api03.png)

> [!TIP]
> O utilizador do Video Indexer pode utilizar uma chave de subscrição individual para ligar a várias contas do Video Indexer. Em seguida, pode associar estas contas do Video Indexer a diferentes contas dos Serviços de Multimédia.

## <a name="obtain-access-token-using-the-authorization-api"></a>Obter o token de acesso com a API de Autorização

Uma vez subscrita a API de Autorização, pode obter fichas de acesso. Utilizam-se estes tokens de acesso para efetuar a autenticação na API de Operações.

Cada chamada efetuada para a API de Operações deve estar associada a um token de acesso que corresponda ao âmbito de autorização da chamada.

- Nível de utilizador: As fichas de acesso ao nível do utilizador permitem-lhe realizar operações ao nível do **utilizador.** Permitem, por exemplo, obter contas associadas.
- Nível de conta: Fichas de acesso ao nível da conta permitem-lhe realizar operações ao nível da **conta** ou ao nível de **vídeo.** Por exemplo, faça upload de vídeo, liste todos os vídeos, obtenha informações de vídeo, e assim por diante.
- Nível de vídeo: Fichas de acesso ao nível de vídeo permitem-lhe realizar operações num **vídeo**específico . Por exemplo, obtenha informações de vídeo, baixe legendas, obtenha widgets, e assim por diante.

Pode controlar se estas fichas são apenas de leitura ou se permitem a edição especificando **allowEdit=true/false**.

Para a maioria dos cenários servidor-a-servidor, provavelmente utilizará o mesmo token de **conta,** uma vez que abrange operações de **conta** e operações de **vídeo.** No entanto, se planeia fazer chamadas do lado do cliente para o Indexer de Vídeo (por exemplo, a partir do JavaScript), gostaria de usar um sinal de acesso de **vídeo** para impedir que os clientes tenham acesso a toda a conta. Essa é também a razão pela qual ao incorporar o código de cliente do Indexer de Vídeo no seu cliente (por exemplo, usando **O Widget Get Insights** ou Get Player **Widget),** deve fornecer um sinal de acesso de **vídeo.**

Para facilitar o processo, pode aceder a APIS > **Authorization** (Autorização) > **Get Accounts** (Obter Contas) para obter as suas contas sem ter de obter primeiro um token de utilizador. Também pode pedir para obter as contas com tokens válidos, o que evita a realização de uma chamada adicional para obter um token de conta.

Os tokens de acesso expiram após uma hora. Certifique-se de que o seu token de acesso é válido antes de utilizar a API de Operações. Se expirar, ligue novamente para a API de Autorização para obter um novo sinal de acesso.

Está pronto para começar a integrar-se com a API. Veja [a descrição detalhada de cada API REST do Video Indexer](https://api-portal.videoindexer.ai/).

## <a name="account-id"></a>ID da Conta

O parâmetro de ID da Conta é necessário em todas as chamadas à API operacionais. O ID da Conta é um GUID que pode ser obtido de uma das seguintes formas:

* Utilize o **site do Video Indexer** para obter o ID da Conta:

    1. Aceda ao site do [Video Indexer](https://www.videoindexer.ai/) e inicie sessão.
    2. Navegue para a página **Settings** (Definições).
    3. Copie o ID da Conta.

        ![Definições do Indexante de Vídeo e ID da conta](./media/video-indexer-use-apis/account-id.png)

* Utilize o **Video Indexer Developer Portal** para obter o ID da Conta através de programação.

    Use a [Conta Get](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Account?) API.

    > [!TIP]
    > Pode gerar tokens de acesso para as contas ao definir `generateAccessTokens=true`.

* Obtenha o ID da Conta a partir do URL de uma página do leitor na sua conta.

    Quando vê um vídeo, o ID aparece a seguir à secção `accounts` e antes da secção `videos`.

    ```
    https://www.videoindexer.ai/accounts/00000000-f324-4385-b142-f77dacb0a368/videos/d45bf160b5/
    ```

## <a name="recommendations"></a>Recomendações

Esta secção lista algumas recomendações que deve ter em conta durante a utilização da API do Video Indexer.

- Se estiver a planear fazer o upload de um vídeo, é aconselhável colocar o ficheiro em algum local de rede pública (por exemplo, OneDrive). Obtenha a ligação do vídeo e forneça o URL enquanto parâmetro de carregamento de ficheiro.

    O URL fornecido ao Video Indexer tem de apontar para um ficheiro de multimédia (áudio ou vídeo). Algumas das ligações geradas pelo OneDrive encaminham para uma página HTML que contém o ficheiro. Uma verificação fácil para o URL é colá-lo em um navegador - se o ficheiro começar a descarregar, é provavelmente um bom URL. Se o navegador está a fazer alguma visualização, é provável que não seja um link para um ficheiro, mas para uma página HTML.

- Quando chama a API que obtém informações de vídeo para o vídeo especificado, obtém uma saída JSON detalhada como conteúdo de resposta. [Veja informações detalhadas sobre o JSON devolvido neste tópico](video-indexer-output-json-v2.md).

## <a name="code-sample"></a>Exemplo de código

O seguinte fragmento de código C# demonstra a utilização de todas as APIs do Video Indexer em conjunto.

```csharp
var apiUrl = "https://api.videoindexer.ai";
var accountId = "..."; 
var location = "westus2";
var apiKey = "..."; 

System.Net.ServicePointManager.SecurityProtocol = System.Net.ServicePointManager.SecurityProtocol | System.Net.SecurityProtocolType.Tls12;

// create the http client
var handler = new HttpClientHandler(); 
handler.AllowAutoRedirect = false; 
var client = new HttpClient(handler);
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey); 

// obtain account access token
var accountAccessTokenRequestResult = client.GetAsync($"{apiUrl}/auth/{location}/Accounts/{accountId}/AccessToken?allowEdit=true").Result;
var accountAccessToken = accountAccessTokenRequestResult.Content.ReadAsStringAsync().Result.Replace("\"", "");

client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

// upload a video
var content = new MultipartFormDataContent();
Debug.WriteLine("Uploading...");
// get the video from URL
var videoUrl = "VIDEO_URL"; // replace with the video URL

// as an alternative to specifying video URL, you can upload a file.
// remove the videoUrl parameter from the query string below and add the following lines:
  //FileStream video =File.OpenRead(Globals.VIDEOFILE_PATH);
  //byte[] buffer = new byte[video.Length];
  //video.Read(buffer, 0, buffer.Length);
  //content.Add(new ByteArrayContent(buffer));

var uploadRequestResult = client.PostAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos?accessToken={accountAccessToken}&name=some_name&description=some_description&privacy=private&partition=some_partition&videoUrl={videoUrl}", content).Result;
var uploadResult = uploadRequestResult.Content.ReadAsStringAsync().Result;

// get the video id from the upload result
var videoId = JsonConvert.DeserializeObject<dynamic>(uploadResult)["id"];
Debug.WriteLine("Uploaded");
Debug.WriteLine("Video ID: " + videoId);           

// obtain video access token
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);
var videoTokenRequestResult = client.GetAsync($"{apiUrl}/auth/{location}/Accounts/{accountId}/Videos/{videoId}/AccessToken?allowEdit=true").Result;
var videoAccessToken = videoTokenRequestResult.Content.ReadAsStringAsync().Result.Replace("\"", "");

client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

// wait for the video index to finish
while (true)
{
  Thread.Sleep(10000);

  var videoGetIndexRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/Index?accessToken={videoAccessToken}&language=English").Result;
  var videoGetIndexResult = videoGetIndexRequestResult.Content.ReadAsStringAsync().Result;

  var processingState = JsonConvert.DeserializeObject<dynamic>(videoGetIndexResult)["state"];

  Debug.WriteLine("");
  Debug.WriteLine("State:");
  Debug.WriteLine(processingState);

  // job is finished
  if (processingState != "Uploaded" && processingState != "Processing")
  {
      Debug.WriteLine("");
      Debug.WriteLine("Full JSON:");
      Debug.WriteLine(videoGetIndexResult);
      break;
  }
}

// search for the video
var searchRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/Search?accessToken={accountAccessToken}&id={videoId}").Result;
var searchResult = searchRequestResult.Content.ReadAsStringAsync().Result;
Debug.WriteLine("");
Debug.WriteLine("Search:");
Debug.WriteLine(searchResult);

// get insights widget url
var insightsWidgetRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/InsightsWidget?accessToken={videoAccessToken}&widgetType=Keywords&allowEdit=true").Result;
var insightsWidgetLink = insightsWidgetRequestResult.Headers.Location;
Debug.WriteLine("Insights Widget url:");
Debug.WriteLine(insightsWidgetLink);

// get player widget url
var playerWidgetRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/PlayerWidget?accessToken={videoAccessToken}").Result;
var playerWidgetLink = playerWidgetRequestResult.Headers.Location;
Debug.WriteLine("");
Debug.WriteLine("Player Widget url:");
Debug.WriteLine(playerWidgetLink);

```

## <a name="see-also"></a>Consulte também

- [Descrição geral do Video Indexer](video-indexer-overview.md)
- [Regiões](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)

## <a name="next-steps"></a>Passos seguintes

- [Examinar detalhes da saída JSON](video-indexer-output-json-v2.md)
- Confira o código da [amostra](https://github.com/Azure-Samples/media-services-video-indexer) que demonstra um aspeto importante de carregar e indexar um vídeo. Seguir o código dar-lhe-á uma boa ideia de como usar a nossa API para funcionalidades básicas. Leia os comentários inline e note os nossos conselhos de boas práticas.

