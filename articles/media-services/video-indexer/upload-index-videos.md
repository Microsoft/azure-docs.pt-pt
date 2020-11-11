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
ms.date: 11/10/2020
ms.author: juliako
ms.custom: devx-track-csharp
ms.openlocfilehash: a5106e1089e2353d2db884977eb51a4fd2717b99
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94506180"
---
# <a name="upload-and-index-your-videos"></a>Carregar e indexar vídeos  

Ao carregar vídeos com a API do Video Indexer, tem as seguintes opções de carregamento: 

* carregar o vídeo a partir de um URL (preferencial),
* enviar o ficheiro de vídeo como um conjunto de byte no corpo de pedido,
* Utilizar o recurso existente dos Serviços de Multimédia do Azure ao disponibilizar o [ID do recurso](../latest/assets-concept.md) (suportado apenas em contas pagas).

Uma vez que o seu vídeo tenha sido carregado, o Video Indexer (opcionalmente) codifica o vídeo (discutido no artigo). Quando criar uma conta do Video Indexer, pode optar por uma conta de avaliação gratuita (através da qual obtém um determinado número de minutos de indexação gratuitos) ou uma opção paga (não fica limitado pela quota). Com a avaliação gratuita, o Video Indexer fornece até 600 minutos de indexação gratuita a utilizadores de sites e até 2400 minutos de indexação gratuita a utilizadores de APIs. Com a opção paga, pode criar uma conta do Video Indexer, [ligada à subscrição do Azure, e uma conta dos Serviços de Multimédia do Azure](connect-to-azure.md). Você paga por minutos indexados, para mais informações, consulte [os preços dos Serviços de Mídia](https://azure.microsoft.com/pricing/details/media-services/).

O artigo mostra como carregar e indexar os seus vídeos com estas opções:

* [Site do Video Indexer](#website) 
* [APIs do Video Indexer](#apis)

## <a name="uploading-considerations-and-limitations"></a>Considerações e limitações de carregamento
 
- O nome do vídeo não pode ter mais de 80 carateres.
- Ao carregar o vídeo com base no URL (preferencial), o ponto final tem de estar protegido pelo TLS 1.2 (ou posterior).
- O tamanho do carregamento com a opção de URL está limitado a 30 GB.
- O comprimento do URL do pedido está limitado a 6144 carateres, ao passo que o comprimento do URL da cadeia de consulta está limitado a 4096 caracteres.
- O tamanho do carregamento com a opção de matriz de bytes está limitado a 2 GB.
- A opção de matriz de bytes excede o tempo limite após 30 min.
- A URL fornecida no `videoURL` param precisa de ser codificada.
- Indexar os recursos dos Serviços de Multimédia tem a mesma limitação que a indexação do URL.
- O Video Indexer tem uma duração máxima de 4 horas para um único ficheiro.
- O URL tem de estar acessível (por exemplo, um URL público). 

    Se for um URL privado, o token de acesso terá ser disponibilizado no pedido.
- O URL tem de apontar para um ficheiro de mídia válido e não para uma página web, como um link para a `www.youtube.com` página.
- Numa conta paga, pode carregar até 50 filmes por minuto e, numa conta de avaliação, até 5 filmes por minuto.

> [!Tip]
> Recomenda-se a utilização da versão 4.6.2 do .NET Framework ou superior porque as versões .NET Framework mais antigas não têm como predefinição o TLS 1.2.
>
> Se tiver de utilizar versões do .NET Framework mais antigas, adicione uma linha ao seu código antes de fazer a chamada à API REST:  <br/> System.Net.ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls | SecurityProtocolType.Tls11 | SecurityProtocolType.Tls12;

## <a name="supported-file-formats-for-video-indexer"></a>Formatos de ficheiros suportados para Indexador de Vídeo

Consulte o artigo [de formatos de recipiente/ficheiro](../latest/media-encoder-standard-formats.md#input-containerfile-formats) de entrada para obter uma lista de formatos de ficheiros que pode utilizar com o Video Indexer.

## <a name="video-files-storage"></a>Armazenamento de ficheiros de vídeo

- Com uma conta de Indexer de Vídeo pago, cria uma conta de Indexer de Vídeo que está ligada à sua subscrição Azure e a uma conta Azure Media Services. Para obter mais informações, consulte [Criar uma conta de Indexer de Vídeo ligada ao Azure](connect-to-azure.md).
- Os ficheiros de vídeo são armazenados no armazém da Azure media Services. Não há limitação de tempo.
- Pode sempre eliminar os seus ficheiros de vídeo e áudio, bem como quaisquer metadados e insights extraídos dos mesmos pelo Video Indexer. Uma vez eliminado um ficheiro do Indexer de Vídeo, o ficheiro e os seus metadados e insights são permanentemente removidos do Indexador de Vídeo. No entanto, se implementou a sua própria solução de backup no armazenamento Azure, o ficheiro permanece no seu armazenamento Azure.
- A persistência de um vídeo é idêntica, independentemente de o upload for feito formar o website do Indexer de Vídeo ou utilizar a API de upload.
   
## <a name="upload-and-index-a-video-using-the-video-indexer-website"></a><a name="website"></a>Faça upload e indexe um vídeo utilizando o site do Indexer de Vídeo

> [!NOTE]
> O nome do vídeo não pode ter mais de 80 carateres.

1. Inicie sessão no site do [Video Indexer](https://www.videoindexer.ai/).
1. Para carregar um vídeo, selecione o botão ou ligação **Upload** (Carregar)

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/video-indexer-get-started/video-indexer-upload.png" alt-text="Carregar":::
1. Após carregar o seu vídeo, o Video Indexer iniciará a indexação e análise do vídeo.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/video-indexer-get-started/progress.png" alt-text="Progresso do upload":::
1. Assim que o Video Indexer terminar de analisar, receberá um e-mail com um link para o seu vídeo e uma breve descrição do que foi encontrado no seu vídeo. Por exemplo: people (pessoas), topics (tópicos), OCRs.

## <a name="upload-and-index-with-api"></a><a name="apis"></a>Upload e índice com API

Utilize a API [de vídeo upload](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) para carregar e indexar os seus vídeos com base num URL. A amostra de código que se segue inclui o código comentado que mostra como carregar o conjunto byte. 

### <a name="configurations-and-params"></a>Configurações e parâmetros

Esta secção descreve alguns dos parâmetros opcionais e quando deve defini-los.

#### <a name="externalid"></a>externalID 

Este parâmetro permite-lhe especificar um ID que será associado ao vídeo. O ID pode ser aplicado à integração de sistemas externa de "Gestão de Conteúdo de Vídeo" (VCM). Os vídeos localizados no portal do Video Indexer podem ser procurados com o ID externo especificado.

#### <a name="callbackurl"></a>callbackUrl

Um URL que é usado para notificar o cliente (usando um pedido POST) sobre os seguintes eventos:

- Alteração do estado de indexação: 
    - Propriedades:    
    
        |Nome|Descrição|
        |---|---|
        |ID|A iD de vídeo|
        |state|O estado do vídeo|  
    - Exemplo: https: \/ /test.com/notifyme?projectName=MyProject&id=1234abcd&state=Processado
- Pessoa identificada em vídeo:
  - Propriedades
    
      |Nome|Descrição|
      |---|---|
      |ID| A iD de vídeo|
      |faceId|O ID do rosto que aparece no índice de vídeo|
      |conhecidoPersonId|A pessoa ID que é única dentro de um modelo de rosto|
      |nome de pessoa|O nome da pessoa|
        
    - Exemplo: https: \/ /test.com/notifyme?projectName=MyProject&id=1234abcd&faceid=12&conhecidoPersonId=CCA84350-89B7-4262-861C-3CAC796542A5&personName=Inigo_Montoya 

##### <a name="other-considerations"></a>Outras considerações

- O Indexante de Vídeo devolve quaisquer parâmetros existentes fornecidos no URL original.
- A URL fornecida deve ser codificada.

#### <a name="indexingpreset"></a>indexingPreset

Utilize este parâmetro se as gravações não processadas ou externas contiverem ruído de fundo. Este parâmetro é utilizado para configurar o processo de indexação. Pode especificar os seguintes valores:

- `AudioOnly` – indexe e extraia informações apenas com áudio (ignorar vídeo)
- `VideoOnly` - Informações de índice e extrato usando apenas vídeo (ignorando áudio)
- `Default` – indexe e extraia informações com áudio e vídeo
- `DefaultWithNoiseReduction` – indexe e extraia informações de áudio e vídeo, enquanto aplica algoritmos de redução de ruído na transmissão de áudio

> [!NOTE]
> O Indexer de Vídeo cobre até duas faixas de áudio. Se houver mais faixas de áudio no ficheiro, serão tratadas como uma faixa.<br/>
Se pretender indexar as faixas separadamente, terá de extrair o ficheiro áudio relevante e indexá-lo como `AudioOnly` .

O preço varia consoante a opção de indexação selecionada.  

#### <a name="priority"></a>prioridade

Os vídeos são indexados pelo Video Indexer de acordo com a sua prioridade. Utilize o parâmetro **prioritário** para especificar a prioridade do índice. Os seguintes valores são válidos: **Baixo,** **Normal** (padrão) e **Alto**.

**O** parâmetro prioritário só é suportado para contas pagas.

#### <a name="streamingpreset"></a>streamingPreset

Assim que o seu vídeo for carregado, o Video Indexer codifica opcionalmente o vídeo. Em seguida, indexa e analisa o vídeo. Quando o Video Indexer terminar de analisar, irá receber uma notificação com o ID do vídeo.  

Ao utilizar a API [Carregar vídeo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) ou [Reindexar Vídeo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?), um dos parâmetros opcionais é `streamingPreset`. Se definir o parâmetro `streamingPreset` para `Default`, `SingleBitrate` ou `AdaptiveBitrate`, o processo de codificação é acionado. Assim que as tarefas de indexação e codificação estiverem concluídas, o vídeo é publicado para que também o possa transmitir em fluxo. O Ponto Final de Transmissão em Fluxo a partir do qual quer transmitir o vídeo tem de estar no estado **Em execução**.

Para o SingleBitrate, o custo padrão do codificador será aplicado por saída. Se a altura do vídeo for maior ou igual a 720, o Video Indexer codifica-o como 1280x720. Caso contrário, como 640x468.
A definição predefinição é [codificação de conteúdo consciente](../latest/content-aware-encoding.md).

Para executar as tarefas de indexação e codificação, a [conta dos Serviços de Multimédia do Azure associada à sua conta do Video Indexer](connect-to-azure.md) necessita de Unidades Reservadas. Para obter mais informações, veja [Scaling Media Processing](../previous/media-services-scale-media-processing-overview.md) (Dimensionar o Processamento de Multimédia). Como estas são tarefas de computação intensiva, o tipo de unidade S3 é altamente recomendado. O número de RUs define o número máximo de tarefas que podem ser executadas em paralelo. A recomendação de linha de base é de 10 RUs S3. 

Se quiser indexar o seu vídeo mas não quiser codificá-lo, defina o parâmetro `streamingPreset` para `NoStreaming`.

#### <a name="videourl"></a>videoUrl

Um URL do ficheiro de vídeo/áudio a ser indexado. O URL tem de apontar para um ficheiro de multimédia (não são suportadas páginas HTML). O ficheiro pode ser protegido por um token de acesso fornecido como parte do URI e o ponto final que entrega o ficheiro tem de estar protegido pelo TLS 1.2 ou superior. O URL tem de ser codificado. 

Se o parâmetro `videoUrl` não for especificado, o Video Indexer espera que transmita o ficheiro como um conteúdo de corpo de formulário/com várias partes.

### <a name="code-sample"></a>Exemplo de código

O seguinte fragmento de código C# demonstra a utilização de todas as APIs do Video Indexer em conjunto.

**Instruções para executar a seguinte amostra de código**

Depois de copiar este código na sua plataforma de desenvolvimento, terá de fornecer dois parâmetros: chave de autenticação de API Management e URL de vídeo.

* Chave API – A chave API é a sua chave de subscrição de gestão de API pessoal, que lhe permitirá obter um token de acesso para realizar operações na sua conta de Indexer de Vídeo. 

    Para obter a sua chave API, passe por este fluxo:

    * Navegue para https://api-portal.videoindexer.ai/
    * Iniciar sessão
    * Ir **Products** para a assinatura de Autorização de  ->  **Autorização**  ->  **de** Produtos
    * Copiar a **chave primária**
* URL de vídeo – UM URL do ficheiro vídeo/áudio a ser indexado. O URL tem de apontar para um ficheiro de multimédia (não são suportadas páginas HTML). O ficheiro pode ser protegido por um token de acesso fornecido como parte do URI e o ponto final que entrega o ficheiro tem de estar protegido pelo TLS 1.2 ou superior. O URL tem de ser codificado.

O resultado de uma execução com sucesso da amostra de código incluirá um URL widget de insight e um URL widget do jogador que lhe permitirá examinar as insights e o vídeo carregados respectivamente. 


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
|429||As contas de julgamento são permitidas 5 uploads por minuto. As contas pagas são permitidas 50 uploads por minuto.|

## <a name="next-steps"></a>Passos seguintes

[Examinar a produção do Azure Video Indexer produzido pela API](video-indexer-output-json-v2.md)
