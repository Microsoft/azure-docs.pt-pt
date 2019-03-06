---
title: Carregar e indexar vídeos com o Video Indexer
titlesuffix: Azure Media Services
description: Este tópico demonstra como utilizar APIs para carregar e indexar vídeos com o Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 02/17/2019
ms.author: juliako
ms.openlocfilehash: ddf090664b1e5197603dadc26da92a0a1da1a63d
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57404948"
---
# <a name="upload-and-index-your-videos"></a>Carregar e indexar vídeos  

Ao carregar vídeos com a API do Video Indexer, tem as seguintes opções de carregamento: 

* carregar o vídeo a partir de um URL (preferencial),
* enviar o ficheiro de vídeo como uma matriz de bytes no corpo do pedido,
* Utilizar o recurso de serviços de multimédia do Azure existente, fornecendo a [id de recurso](https://docs.microsoft.com/azure/media-services/latest/assets-concept) (suportado pagas apenas em contas).

O artigo mostra como utilizar a API [Carregar vídeo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) para carregar e indexar os seus vídeos com base num URL. O exemplo de código neste artigo inclui o código comentado que mostra como carregar a matriz de bytes. <br/>O artigo também aborda alguns dos parâmetros que pode definir na API para alterar o processo e o resultado da API.

Assim que o seu vídeo foi carregado, o indexador de vídeos, codifica opcionalmente o vídeo (discutido no artigo). Quando criar uma conta do Video Indexer, pode optar por uma conta de avaliação gratuita (através da qual obtém um determinado número de minutos de indexação gratuitos) ou uma opção paga (não fica limitado pela quota). Com a avaliação gratuita, o Video Indexer fornece até 600 minutos de indexação gratuita a utilizadores de sites e até 2400 minutos de indexação gratuita a utilizadores de APIs. Com a opção paga, pode criar uma conta do Video Indexer que está [ligada à sua subscrição do Azure e uma conta dos Serviços de Multimédia do Azure](connect-to-azure.md). Irá pagar pelos minutos indexados, bem como pelas cobranças relacionadas com a Conta de Multimédia. 

## <a name="uploading-considerations"></a>Considerações de carregamento

- Ao carregar o seu vídeo com base no URL (preferencial), o ponto final tem de estar protegido por TLS 1.2 (ou posterior)
- O tamanho de carregamento com a opção de URL está limitado a 30GB
- Na maioria dos navegadores, o comprimento de URL está limitado a 2.000 caracteres
- O tamanho de carregamento com a opção de matriz de bytes é limitado a 2GB
- A opção de matriz de bytes expira após 30 minutos
- O URL fornecido no parâmetro `videoURL` tem de ser codificado

> [!Tip]
> Recomenda-se a utilização da versão 4.6.2 do .NET Framework ou superior porque as versões .NET Framework mais antigas não têm como predefinição o TLS 1.2.
>
> Se tiver de utilizar versões do .NET Framework mais antigas, adicione uma linha ao seu código antes de fazer a chamada à API REST:  <br/> System.Net.ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls | SecurityProtocolType.Tls11 | SecurityProtocolType.Tls12;

## <a name="configurations-and-params"></a>Configurações e parâmetros

Esta secção descreve alguns dos parâmetros opcionais e quando deve defini-los.

### <a name="externalid"></a>externalID 

Este parâmetro permite-lhe especificar um ID que será associado ao vídeo. O ID pode ser aplicado à integração de sistemas externa de "Gestão de Conteúdo de Vídeo" (VCM). Os vídeos localizados no portal do Video Indexer podem ser procurados com o ID externo especificado.

### <a name="callbackurl"></a>callbackUrl

Um URL que é utilizado para notificar o cliente (usando um pedido POST) sobre os seguintes eventos:

- Alteração de estado de indexação: 
    - Propriedades:    
    
        |Name|Descrição|
        |---|---|
        |ID|O ID do vídeo|
        |state|O estado de vídeo|  
    - Exemplo: https://test.com/notifyme?projectName=MyProject&id=1234abcd&state=Processed
- Pessoa identificada no vídeo:
    - Propriedades
    
        |Name|Descrição|
        |---|---|
        |ID| O ID do vídeo|
        |faceId|O ID de rostos que aparece no índice de vídeos|
        |knownPersonId|O ID da pessoa que seja exclusivo dentro de um modelo de rostos|
        |personName|O nome da pessoa|
        
     - Exemplo: https://test.com/notifyme?projectName=MyProject&id=1234abcd&faceid=12&knownPersonId=CCA84350-89B7-4262-861C-3CAC796542A5&personName=Inigo_Montoya 

#### <a name="notes"></a>Notas

- O Video Indexer devolve todos os parâmetros existentes fornecidos no original URL.
- O URL fornecido tem de ser codificado.

### <a name="indexingpreset"></a>indexingPreset

Utilize este parâmetro se as gravações não processadas ou externas contiverem ruído de fundo. Este parâmetro é utilizado para configurar o processo de indexação. Pode especificar os seguintes valores:

- `Default` – indexe e extraia informações com áudio e vídeo
- `AudioOnly` – indexe e extraia informações apenas com áudio (ignorar vídeo)
- `DefaultWithNoiseReduction` – indexe e extraia informações de áudio e vídeo, enquanto aplica algoritmos de redução de ruído na transmissão de áudio

O preço varia consoante a opção de indexação selecionada.  

### <a name="priority"></a>prioridade

Vídeos são indexados pelo indexador de vídeo, de acordo com sua prioridade. Utilize o **prioridade** parâmetro para especificar a prioridade de índice. Os seguintes valores são válidos: **Baixa**, **Normal** (predefinição), e **elevada**.

**Prioridade** parâmetro só é suportado para contas pagas.

### <a name="streamingpreset"></a>streamingPreset

Assim que o seu vídeo for carregado, o Video Indexer codifica opcionalmente o vídeo. Em seguida, indexa e analisa o vídeo. Quando o Video Indexer terminar de analisar, irá receber uma notificação com o ID do vídeo.  

Ao utilizar a API [Carregar vídeo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) ou [Reindexar Vídeo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?), um dos parâmetros opcionais é `streamingPreset`. Se definir o parâmetro `streamingPreset` para `Default`, `SingleBitrate` ou `AdaptiveBitrate`, o processo de codificação é acionado. Assim que as tarefas de indexação e codificação estiverem concluídas, o vídeo é publicado para que também o possa transmitir em fluxo. O Ponto Final de Transmissão em Fluxo a partir do qual quer transmitir o vídeo tem de estar no estado **Em execução**.

Para executar as tarefas de indexação e codificação, a [conta dos Serviços de Multimédia do Azure associada à sua conta do Video Indexer](connect-to-azure.md) necessita de Unidades Reservadas. Para obter mais informações, veja [Scaling Media Processing](https://docs.microsoft.com/azure/media-services/previous/media-services-scale-media-processing-overview) (Dimensionar o Processamento de Multimédia). Como estas são tarefas de computação intensiva, o tipo de unidade S3 é altamente recomendado. O número de RUs define o número máximo de tarefas que podem ser executadas em paralelo. A recomendação de linha de base é de 10 RUs S3. 

Se quiser indexar o seu vídeo mas não quiser codificá-lo, defina o parâmetro `streamingPreset` para `NoStreaming`.

### <a name="videourl"></a>videoUrl

Um URL do ficheiro de vídeo/áudio a ser indexado. O URL tem de apontar para um ficheiro de multimédia (não são suportadas páginas HTML). O ficheiro pode ser protegido por um token de acesso fornecido como parte do URI e o ponto final que entrega o ficheiro tem de estar protegido pelo TLS 1.2 ou superior. O URL tem de ser codificado. 

Se o parâmetro `videoUrl` não for especificado, o Video Indexer espera que transmita o ficheiro como um conteúdo de corpo de formulário/com várias partes.

## <a name="code-sample"></a>Exemplo de código

O seguinte fragmento de código C# demonstra a utilização de todas as APIs do Video Indexer em conjunto.

```csharp
public async Task Sample()
{
    var apiUrl = "https://api.videoindexer.ai";
    var location = "westus2";
    var apiKey = "...";

    System.Net.ServicePointManager.SecurityProtocol =
        System.Net.ServicePointManager.SecurityProtocol | System.Net.SecurityProtocolType.Tls12;

    // create the http client
    var handler = new HttpClientHandler();
    handler.AllowAutoRedirect = false;
    var client = new HttpClient(handler);
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);

    // obtain account information and access token
    string queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"generateAccessTokens", "true"},
            {"allowEdit", "true"},
        });
    HttpResponseMessage result = await client.GetAsync($"{apiUrl}/auth/trial/Accounts?{queryParams}");
    var json = await result.Content.ReadAsStringAsync();
    var accounts = JsonConvert.DeserializeObject<AccountContractSlim[]>(json);
    // take the relevant account, here we simply take the first
    var accountInfo = accounts.First();

    // we will use the access token from here on, no need for the apim key
    client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

    // upload a video
    var content = new MultipartFormDataContent();
    Debug.WriteLine("Uploading...");
    // get the video from URL
    var videoUrl = "VIDEO_URL"; // replace with the video URL

    // as an alternative to specifying video URL, you can upload a file.
    // remove the videoUrl parameter from the query params below and add the following lines:
    //FileStream video =File.OpenRead(Globals.VIDEOFILE_PATH);
    //byte[] buffer =newbyte[video.Length];
    //video.Read(buffer, 0, buffer.Length);
    //content.Add(newByteArrayContent(buffer));

    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", accountInfo.AccessToken},
            {"name", "video_name"},
            {"description", "video_description"},
            {"privacy", "private"},
            {"partition", "partition"},
            {"videoUrl", videoUrl},
        });
    var uploadRequestResult = await client.PostAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos?{queryParams}", content);
    var uploadResult = await uploadRequestResult.Content.ReadAsStringAsync();

    // get the video id from the upload result
    string videoId = JsonConvert.DeserializeObject<dynamic>(uploadResult)["id"];
    Debug.WriteLine("Uploaded");
    Debug.WriteLine("Video ID:");
    Debug.WriteLine(videoId);

    // wait for the video index to finish
    while (true)
    {
        await Task.Delay(10000);

        queryParams = CreateQueryString(
            new Dictionary<string, string>()
            {
                {"accessToken", accountInfo.AccessToken},
                {"language", "English"},
            });

        var videoGetIndexRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/Index?{queryParams}");
        var videoGetIndexResult = await videoGetIndexRequestResult.Content.ReadAsStringAsync();

        string processingState = JsonConvert.DeserializeObject<dynamic>(videoGetIndexResult)["state"];

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
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", accountInfo.AccessToken},
            {"id", videoId},
        });

    var searchRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/Search?{queryParams}");
    var searchResult = await searchRequestResult.Content.ReadAsStringAsync();
    Debug.WriteLine("");
    Debug.WriteLine("Search:");
    Debug.WriteLine(searchResult);

    // Generate video access token (used for get widget calls)
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);
    var videoTokenRequestResult = await client.GetAsync($"{apiUrl}/auth/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/AccessToken?allowEdit=true");
    var videoAccessToken = (await videoTokenRequestResult.Content.ReadAsStringAsync()).Replace("\"", "");
    client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

    // get insights widget url
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", videoAccessToken},
            {"widgetType", "Keywords"},
            {"allowEdit", "true"},
        });
    var insightsWidgetRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/InsightsWidget?{queryParams}");
    var insightsWidgetLink = insightsWidgetRequestResult.Headers.Location;
    Debug.WriteLine("Insights Widget url:");
    Debug.WriteLine(insightsWidgetLink);

    // get player widget url
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", videoAccessToken},
        });
    var playerWidgetRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/PlayerWidget?{queryParams}");
    var playerWidgetLink = playerWidgetRequestResult.Headers.Location;
    Debug.WriteLine("");
    Debug.WriteLine("Player Widget url:");
    Debug.WriteLine(playerWidgetLink);
}

private string CreateQueryString(IDictionary<string, string> parameters)
{
    var queryParameters = HttpUtility.ParseQueryString(string.Empty);
    foreach (var parameter in parameters)
    {
        queryParameters[parameter.Key] = parameter.Value;
    }

    return queryParameters.ToString();
}

public class AccountContractSlim
{
    public Guid Id { get; set; }
    public string Name { get; set; }
    public string Location { get; set; }
    public string AccountType { get; set; }
    public string Url { get; set; }
    public string AccessToken { get; set; }
}
```
## <a name="common-errors"></a>Erros comuns

Os códigos de estado indicados na tabela seguinte podem ser devolvidos pela operação de Carregamento.

|Código de estado|ErrorType (no corpo da resposta)|Descrição|
|---|---|---|
|400|VIDEO_ALREADY_IN_PROGRESS|O mesmo vídeo já está a ser processado na conta específica.|
|400|VIDEO_ALREADY_FAILED|Falha ao processar o mesmo vídeo na conta específica há menos de 2 horas. Os clientes da API devem aguardar pelo menos 2 horas antes de voltarem a carregar um vídeo.|

## <a name="next-steps"></a>Passos Seguintes

[Examinar o resultado do Video Indexer do Azure produzido pela API V2](video-indexer-output-json-v2.md)
