---
title: Utilizar a API do Video Indexer - Azure
titlesuffix: Azure Media Services
description: Este artigo mostra como pode começar a utilizar a API do Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 03/25/2019
ms.author: juliako
ms.openlocfilehash: 4c2c22d2d3d68080b1d4e05410121ea1d1be283d
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58439191"
---
# <a name="tutorial-use-the-video-indexer-api"></a>Tutorial: Utilizar a API do Video Indexer

> [!Note]
> A API do Video Indexer V1 foi preterida no dia 1 de agosto de 2018. Agora, deve utilizar a API do Video Indexer v2. <br/>Para programar com APIs do Video Indexer v2, veja as instruções que se encontram [aqui](https://api-portal.videoindexer.ai/). 

O Video Indexer combina várias tecnologias de inteligência artificial (IA) de áudio e vídeo disponibilizadas pela Microsoft num serviço integrado, o que facilita a programação. As APIs foram concebidas para permitir que os programadores se foquem no consumo de tecnologias de IA multimédia sem se preocuparem com a escala, o alcance global, a disponibilidade e a fiabilidade da plataforma na cloud. Pode utilizar a API para carregar ficheiros, obter informações de vídeo detalhadas, obter URLs de widgets de informações e de leitor para incorporá-los na sua aplicação, entre outras tarefas.

Quando criar uma conta do Video Indexer, pode optar por uma conta de avaliação gratuita (através da qual obtém um determinado número de minutos de indexação gratuitos) ou uma opção paga (não fica limitado pela quota). Com a avaliação gratuita, o Video Indexer fornece até 600 minutos de indexação gratuita a utilizadores de sites e até 2400 minutos de indexação gratuita a utilizadores de APIs. Com uma opção paga, cria uma conta do Video Indexer que seja [ligado à sua subscrição do Azure e uma conta de Media Services do Azure](connect-to-azure.md). Irá pagar pelos minutos de indexação e pelas cobranças relacionadas com a conta dos Serviços de Multimédia do Azure. 

Este artigo mostra como os programadores podem tirar partido da [API do Video Indexer](https://api-portal.videoindexer.ai/).

## <a name="subscribe-to-the-api"></a>Subscrever a API

1. Inicie sessão no [Video Indexer Developer Portal](https://api-portal.videoindexer.ai/).
    
    ![Iniciar sessão](./media/video-indexer-use-apis/video-indexer-api01.png)

   > [!Important]
   > * Tem de utilizar o mesmo fornecedor que utilizou quando se inscreveu no Video Indexer.
   > * Só é possível utilizar contas Google e Microsoft (Outlook/Live) pessoais no caso das contas de avaliação. As contas ligadas ao Azure necessitam do Azure AD.
   > * Só pode existir uma conta ativa por e-mail. Se um utilizador tentar iniciar sessão com o endereço de e-mail user@gmail.com no LinkedIn e, em seguida, com o mesmo endereço user@gmail.com no Google, será apresentada uma página de erro no Google a indicar que o utilizador já existe.

2. Subscreva.

    Selecione o separador [Products](https://api-portal.videoindexer.ai/products) (Produtos). Em seguida, selecione Authorization (Autorização) e subscreva. 
    
    ![Inscrever-se](./media/video-indexer-use-apis/video-indexer-api02.png)

    > [!NOTE]
    > Os utilizadores novos estão automaticamente subscritos em Autorização.
    
    Após subscrever, poderá ver a sua subscrição e as suas chaves primárias e secundárias. As chaves devem ser protegidas. As chaves só devem ser utilizadas pelo seu código do servidor. As chaves não devem estar disponíveis do lado do cliente (.js, .html, etc.).

    ![Inscrever-se](./media/video-indexer-use-apis/video-indexer-api03.png)

> [!TIP]
> O utilizador do Video Indexer pode utilizar uma chave de subscrição individual para ligar a várias contas do Video Indexer. Em seguida, pode associar estas contas do Video Indexer a diferentes contas dos Serviços de Multimédia.

## <a name="obtain-access-token-using-the-authorization-api"></a>Obter o token de acesso com a API de Autorização

Depois de subscrever a API de Autorização, poderá obter tokens de acesso. Utilizam-se estes tokens de acesso para efetuar a autenticação na API de Operações. 

Cada chamada efetuada para a API de Operações deve estar associada a um token de acesso que corresponda ao âmbito de autorização da chamada.

- Nível de utilizador – os tokens de acesso de nível de utilizador permitem realizar operações ao nível de **utilizador**. Permitem, por exemplo, obter contas associadas.
- Nível de conta – os tokens de acesso de nível de conta permitem realizar operações ao nível de **conta** ou de **vídeo**. Permitem, por exemplo, carregar vídeos, listar todos os vídeos, obter informações de vídeo, etc.
- Nível de vídeo – os tokens de acesso de nível de vídeo permitem realizar operações num **vídeo** específico. Permitem, por exemplo, obter informações de vídeo, transferir legendas, obter widgets, etc. 

Pode controlar se estes tokens são só de leitura ou se permitem a edição ao especificar **allowEdit=true/false**.

Na maioria dos cenários de servidor a servidor, provavelmente utilizará o mesmo token de **conta**, uma vez que este token abrange tanto as operações de **conta** como de **vídeo**. No entanto, se estiver a prever efetuar chamadas do lado do cliente para o Video Indexer (por exemplo, a partir de javascript), recomenda-se a utilização de um token de **vídeo** para impedir que os clientes obtenham acesso a toda a conta. Este também é o motivo pelo qual tem de fornecer um token de acesso de **vídeo** quando incorporar o código de cliente do Video Indexer no seu cliente (por exemplo, através da API **Get Insights Widget** [Obter Widget de Informações] ou **Get Player Widget** [Obter Widget de Leitor]).

Para facilitar o processo, pode aceder a APIS > **Authorization** (Autorização) > **Get Accounts** (Obter Contas) para obter as suas contas sem ter de obter primeiro um token de utilizador. Também pode pedir para obter as contas com tokens válidos, o que evita a realização de uma chamada adicional para obter um token de conta.

Os tokens de acesso expiram após uma hora. Certifique-se de que o seu token de acesso é válido antes de utilizar a API de Operações. Se o token expirar, chame novamente a API de Autorização para obter um token de acesso novo.
 
Está pronto para começar a integrar com a API. Veja [a descrição detalhada de cada API REST do Video Indexer](https://api-portal.videoindexer.ai/).

## <a name="account-id"></a>ID da Conta 

O parâmetro de ID da Conta é necessário em todas as chamadas à API operacionais. O ID da Conta é um GUID que pode ser obtido de uma das seguintes formas:

* Utilize o **site do Video Indexer** para obter o ID da Conta:

    1. Aceda ao site do [Video Indexer](https://www.videoindexer.ai/) e inicie sessão.
    2. Navegue para a página **Settings** (Definições).
    3. Copie o ID da Conta.

        ![ID da Conta](./media/video-indexer-use-apis/account-id.png)

* Utilize o **Video Indexer Developer Portal** para obter o ID da Conta através de programação.

    Utilize a API [Get Accounts](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Accounts?) (Obter Contas).
    
    > [!TIP]
    > Pode gerar tokens de acesso para as contas ao definir `generateAccessTokens=true`.
    
* Obtenha o ID da Conta a partir do URL de uma página do leitor na sua conta.

    Quando vê um vídeo, o ID aparece a seguir à secção `accounts` e antes da secção `videos`.

    ```
    https://www.videoindexer.ai/accounts/00000000-f324-4385-b142-f77dacb0a368/videos/d45bf160b5/
    ```

## <a name="recommendations"></a>Recomendações

Esta secção lista algumas recomendações que deve ter em conta durante a utilização da API do Video Indexer.

- Se tencionar carregar um vídeo, recomenda-se que coloque o ficheiro numa localização de rede pública (por exemplo, no OneDrive). Obtenha a ligação do vídeo e forneça o URL enquanto parâmetro de carregamento de ficheiro. 

    O URL fornecido ao Video Indexer tem de apontar para um ficheiro de multimédia (áudio ou vídeo). Algumas das ligações geradas pelo OneDrive encaminham para uma página HTML que contém o ficheiro. Para confirmar facilmente que não existe nenhum problema com o URL, pode colá-lo num browser. Se o ficheiro começar a ser transferido, é provável que o URL esteja a funcionar. Se o browser apresentar algum elemento visual, é provável que não seja uma ligação para um ficheiro, mas sim uma página HTML.
    
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
  //byte[] buffer =newbyte[video.Length];
  //video.Read(buffer, 0, buffer.Length);
  //content.Add(newByteArrayContent(buffer));

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

## <a name="next-steps"></a>Passos Seguintes

- [Analise os detalhes da saída JSON](video-indexer-output-json-v2.md).
- [Descrição geral do Video Indexer](video-indexer-overview.md)
- [Regiões](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)
