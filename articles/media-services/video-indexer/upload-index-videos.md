---
title: Carregar e indexar vídeos com o Video Indexer
titleSuffix: Azure Media Services
description: Este tópico demonstra como utilizar APIs para carregar e indexar vídeos com o Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 02/18/2020
ms.author: juliako
ms.openlocfilehash: 245eabdf4d77682c87062c2581239a554112d748
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77468767"
---
# <a name="upload-and-index-your-videos"></a>Carregar e indexar vídeos  

Ao carregar vídeos com API do Indexer de Vídeo, tem as seguintes opções de upload: 

* carregar o vídeo a partir de um URL (preferencial),
* enviar o ficheiro de vídeo como um matriz byte no corpo de pedido,
* Utilize o ativo existente dos Serviços De Mídia Azure fornecendo o ID do [ativo](https://docs.microsoft.com/azure/media-services/latest/assets-concept) (suportado apenas em contas pagas).

Uma vez que o seu vídeo foi carregado, o Indexer de Vídeo (opcionalmente) codifica o vídeo (discutido no artigo). Quando criar uma conta do Video Indexer, pode optar por uma conta de avaliação gratuita (através da qual obtém um determinado número de minutos de indexação gratuitos) ou uma opção paga (não fica limitado pela quota). Com a avaliação gratuita, o Video Indexer fornece até 600 minutos de indexação gratuita a utilizadores de sites e até 2400 minutos de indexação gratuita a utilizadores de APIs. Com opção paga, cria uma conta De Indexer de Vídeo que está [ligada à sua subscrição Azure e a uma conta Azure Media Services.](connect-to-azure.md) Irá pagar pelos minutos indexados, bem como pelas cobranças relacionadas com a Conta de Multimédia. 

O artigo mostra como carregar e indexar os seus vídeos com estas opções:

* [O site do Indexer de Vídeo](#website) 
* [As APIs do Indexer de Vídeo](#apis)

## <a name="uploading-considerations-and-limitations"></a>Upload de considerações e limitações
 
- Um nome do vídeo não deve ter mais de 80 caracteres.
- Ao carregar o seu vídeo com base no URL (preferido) o ponto final deve ser fixado com TLS 1.2 (ou superior).
- O tamanho do upload com a opção URL está limitado a 30GB.
- O comprimento do URL de pedido é limitado a 6144 caracteres onde o comprimento url de corda de consulta é limitado a 4096 caracteres .
- O tamanho do upload com a opção byte array é limitado a 2GB.
- A opção byte array vezes fora após 30 min.
- O URL fornecido `videoURL` no param precisa de ser codificado.
- Indexar os ativos dos Media Services tem a mesma limitação que a indexação a partir de URL.
- O Indexer de vídeo tem um limite máximo de duração de 4 horas para um único ficheiro.
- O URL tem de ser acessível (por exemplo, um URL público). 

    Se for um URL privado, o sinal de acesso deve ser fornecido no pedido.
- O URL tem de apontar para um ficheiro de suporte válido e `www.youtube.com` não para uma página web, como um link para a página.
- Numa conta paga pode fazer upload até 50 filmes por minuto, e numa conta experimental até 5 filmes por minuto.

> [!Tip]
> Recomenda-se a utilização da versão 4.6.2 do .NET Framework ou superior porque as versões .NET Framework mais antigas não têm como predefinição o TLS 1.2.
>
> Se tiver de utilizar versões do .NET Framework mais antigas, adicione uma linha ao seu código antes de fazer a chamada à API REST:  <br/> System.Net.ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls | SecurityProtocolType.Tls11 | SecurityProtocolType.Tls12;

## <a name="supported-file-formats-for-video-indexer"></a>Formatos de ficheirosuportados para Indexer de Vídeo

Consulte o artigo de [formatos de recipiente/ficheiro](../latest/media-encoder-standard-formats.md#input-containerfile-formats) de entrada para obter uma lista de formatos de ficheiros que pode utilizar com o Indexer de Vídeo.

## <a name="upload-and-index-a-video-using-the-video-indexer-website"></a><a id="website"/>Upload e indexar um vídeo usando o site do Indexer de Vídeo

> [!NOTE]
> Um nome do vídeo não deve ter mais de 80 caracteres.

1. Inicie sessão no site do [Video Indexer](https://www.videoindexer.ai/).
2. Para carregar um vídeo, selecione o botão ou ligação **Upload** (Carregar)

    ![Carregar](./media/video-indexer-get-started/video-indexer-upload.png)

    Após carregar o seu vídeo, o Video Indexer iniciará a indexação e análise do vídeo.

    ![Carregado](./media/video-indexer-get-started/video-indexer-uploaded.png) 

    Quando o Video Indexer concluir a análise, será apresentada uma notificação com uma ligação para o seu vídeo juntamente com uma breve descrição dos conteúdos encontrados no mesmo. Por exemplo: people (pessoas), topics (tópicos), OCRs.

## <a name="upload-and-index-with-api"></a><a id="apis"/>Upload e índice com API

Utilize a API de [vídeo upload](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) e indexe os seus vídeos com base num URL. A amostra de código que se segue inclui o código comentado que mostra como carregar a matriz byte. 

### <a name="configurations-and-params"></a>Configurações e parâmetros

Esta secção descreve alguns dos parâmetros opcionais e quando deve defini-los.

#### <a name="externalid"></a>externalID 

Este parâmetro permite-lhe especificar um ID que será associado ao vídeo. O ID pode ser aplicado à integração de sistemas externa de "Gestão de Conteúdo de Vídeo" (VCM). Os vídeos localizados no portal do Video Indexer podem ser procurados com o ID externo especificado.

#### <a name="callbackurl"></a>callbackUrl

Um URL que é utilizado para notificar o cliente (usando um pedido POST) sobre os seguintes eventos:

- Indexação da mudança de estado: 
    - Propriedades:    
    
        |Nome|Descrição|
        |---|---|
        |ID|O ID de vídeo|
        |state|O estado de vídeo|  
    - Exemplo: https:\//test.com/notifyme?projectName=MyProject&id=1234abcd&estado=Processado
- Pessoa identificada em vídeo:
  - Propriedades
    
      |Nome|Descrição|
      |---|---|
      |ID| O ID de vídeo|
      |faceId|O ID facial que aparece no índice de vídeo|
      |conhecidoPersonId|O ID da pessoa que é único dentro de um modelo facial|
      |nome da pessoa|O nome da pessoa|
        
    - Exemplo: https:\//test.com/notifyme?projectName=MyProject&id=1234abcd&faceid=12&conhecidoPersonId=CCA84350-89B7-4262-861C-3CAC796542A5&pessoaName=Inigo_Montoya 

##### <a name="notes"></a>Notas

- O Indexer de vídeo devolve todos os parâmetros existentes fornecidos no URL original.
- O URL fornecido deve ser codificado.

#### <a name="indexingpreset"></a>indexingPreset

Utilize este parâmetro se as gravações não processadas ou externas contiverem ruído de fundo. Este parâmetro é utilizado para configurar o processo de indexação. Pode especificar os seguintes valores:

- `AudioOnly` – indexe e extraia informações apenas com áudio (ignorar vídeo)
- `VideoOnly`- Indexar e extrair insights usando apenas vídeo (ignorando áudio)
- `Default` – indexe e extraia informações com áudio e vídeo
- `DefaultWithNoiseReduction` – indexe e extraia informações de áudio e vídeo, enquanto aplica algoritmos de redução de ruído na transmissão de áudio

> [!NOTE]
> O Indexer de vídeo cobre até duas faixas de áudio. Se houver mais faixas áudio no ficheiro, serão tratadas como uma faixa.<br/>
Se quiser indexar as faixas separadamente, terá de extrair o `AudioOnly`ficheiro áudio relevante e indexá-lo como .

O preço varia consoante a opção de indexação selecionada.  

#### <a name="priority"></a>prioridade

Os vídeos são indexados pelo Indexer de Vídeo de acordo com a sua prioridade. Utilize o parâmetro **prioritário** para especificar a prioridade do índice. Os seguintes valores são válidos: **Baixo,** **Normal** (predefinido) e **Alto**.

**O** parâmetro prioritário só é suportado para contas pagas.

#### <a name="streamingpreset"></a>streamingPreset

Assim que o seu vídeo for carregado, o Video Indexer codifica opcionalmente o vídeo. Em seguida, indexa e analisa o vídeo. Quando o Video Indexer terminar de analisar, irá receber uma notificação com o ID do vídeo.  

Ao utilizar a API [Carregar vídeo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) ou [Reindexar Vídeo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?), um dos parâmetros opcionais é `streamingPreset`. Se definir o parâmetro `streamingPreset` para `Default`, `SingleBitrate` ou `AdaptiveBitrate`, o processo de codificação é acionado. Assim que as tarefas de indexação e codificação estiverem concluídas, o vídeo é publicado para que também o possa transmitir em fluxo. O Ponto Final de Transmissão em Fluxo a partir do qual quer transmitir o vídeo tem de estar no estado **Em execução**.

Para executar as tarefas de indexação e codificação, a [conta dos Serviços de Multimédia do Azure associada à sua conta do Video Indexer](connect-to-azure.md) necessita de Unidades Reservadas. Para obter mais informações, veja [Scaling Media Processing](https://docs.microsoft.com/azure/media-services/previous/media-services-scale-media-processing-overview) (Dimensionar o Processamento de Multimédia). Como estas são tarefas de computação intensiva, o tipo de unidade S3 é altamente recomendado. O número de RUs define o número máximo de tarefas que podem ser executadas em paralelo. A recomendação de linha de base é de 10 RUs S3. 

Se quiser indexar o seu vídeo mas não quiser codificá-lo, defina o parâmetro `streamingPreset` para `NoStreaming`.

#### <a name="videourl"></a>videoUrl

Um URL do ficheiro de vídeo/áudio a ser indexado. O URL tem de apontar para um ficheiro de multimédia (não são suportadas páginas HTML). O ficheiro pode ser protegido por um token de acesso fornecido como parte do URI e o ponto final que entrega o ficheiro tem de estar protegido pelo TLS 1.2 ou superior. O URL tem de ser codificado. 

Se o parâmetro `videoUrl` não for especificado, o Video Indexer espera que transmita o ficheiro como um conteúdo de corpo de formulário/com várias partes.

### <a name="code-sample"></a>Exemplo de código

O seguinte fragmento de código C# demonstra a utilização de todas as APIs do Video Indexer em conjunto.

#### <a name="instructions-for-running-this-code-sample"></a>Instruções para executar esta amostra de código

Depois de copiar este código para a sua plataforma de desenvolvimento, terá de fornecer dois parâmetros: chave de autenticação de gestão API e URL de vídeo.

* Chave API – A chave API é a sua chave de subscrição de gestão de API pessoal, que lhe permitirá obter um sinal de acesso para realizar operações na sua conta De Indexer de Vídeo. 

    Para obter a sua chave API, passe por este fluxo:

    * Navegar parahttps://api-portal.videoindexer.ai/
    * Iniciar sessão
    * Ir à**assinatura de Autorização** de**Autorização** -> de **Produtos** -> 
    * Copiar a **chave primária**
* URL de vídeo – Um URL do ficheiro vídeo/áudio a ser indexado. O URL tem de apontar para um ficheiro de multimédia (não são suportadas páginas HTML). O ficheiro pode ser protegido por um token de acesso fornecido como parte do URI e o ponto final que entrega o ficheiro tem de estar protegido pelo TLS 1.2 ou superior. O URL tem de ser codificado.

O resultado de executar com sucesso a amostra de código incluirá um URL de widget insight e um URL de widget do jogador que lhe permitirá examinar as insights e o vídeo enviado respectivamente. 


```csharp
public async Task Sample()
{
    var apiUrl = "https://api.videoindexer.ai";
    var apiKey = "..."; // replace with API key taken from https://aka.ms/viapi

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
    
    // take the relevant account, here we simply take the first, 
    // you can also get the account via accounts.First(account => account.Id == <GUID>);
    var accountInfo = accounts.First();

    // we will use the access token from here on, no need for the apim key
    client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

    // upload a video
    var content = new MultipartFormDataContent();
    Console.WriteLine("Uploading...");
    // get the video from URL
    var videoUrl = "VIDEO_URL"; // replace with the video URL

    // as an alternative to specifying video URL, you can upload a file.
    // remove the videoUrl parameter from the query params below and add the following lines:
    //FileStream video =File.OpenRead(Globals.VIDEOFILE_PATH);
    //byte[] buffer =new byte[video.Length];
    //video.Read(buffer, 0, buffer.Length);
    //content.Add(new ByteArrayContent(buffer));

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

    // get the video ID from the upload result
    string videoId = JsonConvert.DeserializeObject<dynamic>(uploadResult)["id"];
    Console.WriteLine("Uploaded");
    Console.WriteLine("Video ID:");
    Console.WriteLine(videoId);

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

        Console.WriteLine("");
        Console.WriteLine("State:");
        Console.WriteLine(processingState);

        // job is finished
        if (processingState != "Uploaded" && processingState != "Processing")
        {
            Console.WriteLine("");
            Console.WriteLine("Full JSON:");
            Console.WriteLine(videoGetIndexResult);
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
    Console.WriteLine("");
    Console.WriteLine("Search:");
    Console.WriteLine(searchResult);

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
    Console.WriteLine("Insights Widget url:");
    Console.WriteLine(insightsWidgetLink);

    // get player widget url
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", videoAccessToken},
        });
    var playerWidgetRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/PlayerWidget?{queryParams}");
    var playerWidgetLink = playerWidgetRequestResult.Headers.Location;
     Console.WriteLine("");
     Console.WriteLine("Player Widget url:");
     Console.WriteLine(playerWidgetLink);
     Console.WriteLine("\nPress Enter to exit...");
     String line = Console.ReadLine();
     if (line == "enter")
     {
         System.Environment.Exit(0);
     }

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

### <a name="common-errors"></a>Erros comuns

Os códigos de estado indicados na tabela seguinte podem ser devolvidos pela operação de Carregamento.

|Código de estado|ErrorType (no corpo da resposta)|Descrição|
|---|---|---|
|409|VIDEO_INDEXING_IN_PROGRESS|O mesmo vídeo já está a ser processado na conta específica.|
|400|VIDEO_ALREADY_FAILED|Falha ao processar o mesmo vídeo na conta específica há menos de 2 horas. Os clientes da API devem aguardar pelo menos 2 horas antes de voltarem a carregar um vídeo.|
|429||As contas do julgamento são permitidas 5 uploads por minuto. As contas pagas são permitidas 50 uploads por minuto.|

## <a name="next-steps"></a>Passos seguintes

[Examine a saída do Indexer de Vídeo Azure produzida pela API](video-indexer-output-json-v2.md)
