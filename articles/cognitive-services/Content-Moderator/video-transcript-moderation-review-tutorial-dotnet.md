---
title: 'Tutorial: Vídeos moderados e transcrições no .NET - Content Moderator'
titlesuffix: Azure Cognitive Services
description: Este tutorial ajuda-o a compreender como criar uma solução de moderação de vídeo e transcrição completa com moderação assistida por computador e a criação de revisão humana no loop.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: tutorial
ms.date: 03/11/2019
ms.author: pafarley
ms.openlocfilehash: 504f79186eb69fb6e6c23c1a0cd9dfd7584bb128
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57904342"
---
# <a name="tutorial-video-and-transcript-moderation"></a>Tutorial: Moderação de vídeo e transcrição

Neste tutorial, irá aprender como criar uma solução de moderação de vídeo e transcrição completa com moderação assistida por computador e a criação de revisão humana no loop.

Este tutorial mostrar-lhe como:

> [!div class="checklist"]
> - Comprimir o(s) vídeo(s) de entrada para acelerar o processamento
> - Moderar o vídeo para obter imagens e fotogramas com informações
> - Utilizar os carimbos de data/hora de fotogramas para criar miniaturas (imagens)
> - Submeter carimbos de data/hora e miniaturas para criar revisões de vídeo
> - Converter voz em texto (transcrição) com a API do Media Indexer
> - Moderar a transcrição com o serviço de moderação de textos
> - Adicionar a transcrição moderada à revisão de vídeo

## <a name="prerequisites"></a>Pré-requisitos

- Inscreva-se a [ferramenta de revisão de moderador de conteúdo](https://contentmoderator.cognitive.microsoft.com/) web site e criar etiquetas personalizadas. Ver [utilizar etiquetas](Review-Tool-User-Guide/tags.md) se precisar de ajuda com este passo.

    ![captura de ecrã de etiquetas personalizadas de moderação de vídeo](images/video-tutorial-custom-tags.png)
- Para executar o aplicativo de exemplo, precisa de uma conta do Azure, um recurso de serviços de multimédia do Azure, um recurso do Azure Content Moderator e as credenciais do Azure Active Directory. Para obter instruções sobre como obter estes, consulte a [API de moderação de vídeo](video-moderation-api.md) guia.
- Transfira o [aplicação de consola de revisão de vídeo](https://github.com/MicrosoftContentModerator/VideoReviewConsoleApp) do GitHub.

## <a name="enter-credentials"></a>Introduzir credenciais

Editar a `App.config` de ficheiros e adicionar o nome de inquilino do Active Directory, pontos finais de serviço e as chaves de subscrição indicadas pelo `#####`. Precisará das seguintes informações:

    |Chave|Descrição|
    |-|-|
    |`AzureMediaServiceRestApiEndpoint`|Ponto final da API dos Serviços de Multimédia do Azure (AMS)|
    |`ClientSecret`|Chave de subscrição dos Serviços de Multimédia do Azure|
    |`ClientId`|ID de cliente dos Serviços de Multimédia do Azure|
    |`AzureAdTenantName`|Nome de inquilino do Active Directory que representa a sua organização|
    |`ContentModeratorReviewApiSubscriptionKey`|Chave de subscrição da API de revisão do Content Moderator|
    |`ContentModeratorApiEndpoint`|Ponto final da API do Content Moderator|
    |`ContentModeratorTeamId`|ID de equipa do Content Moderator|

## <a name="examine-the-main-code"></a>Examinar o código principal

A classe `Program` em `Program.cs` é o principal ponto de entrada da aplicação de moderação de vídeos.

### <a name="methods-of-program-class"></a>Métodos da classe de programa

|Método|Descrição|
|-|-|
|`Main`|Analisa a linha de comandos, reúne as introduções dos utilizadores e dá início ao processamento.|
|`ProcessVideo`|Comprime, carrega, modera e cria revisões de vídeo.|
|`CreateVideoStreamingRequest`|Cria um fluxo para carregar um vídeo|
|`GetUserInputs`|Reúne as introduções dos utilizadores; utilizado quando não estão presentes opções de linha de comandos|
|`Initialize`|Inicializa os objetos necessários para o processo de moderação|

### <a name="the-main-method"></a>O método Main

`Main()` é onde começa a execução, pelo que é o lugar onde se deve começar a compreender o processo de moderação de vídeos.

[!code-csharp[Main](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/Program.cs?range=20-24,42-52,54-74)]

`Main()` lida com os seguintes argumentos de linha de comandos:

- O caminho para um diretório que contém ficheiros de vídeo MPEG-4 que serão submetidos para moderação. Todos os ficheiros `*.mp4` neste diretório e respetivos subdiretórios são submetidos para moderação.
- Opcionalmente, um sinalizador booleano (verdadeiro/falso) que indica se as transcrições de texto devem ser geradas para efeitos de moderação de áudio.

Se não estiver presente nenhum argumento de linha de comandos, `Main()` chamará `GetUserInputs()`. Este método pede ao utilizador para introduzir o caminho para um único ficheiro de vídeo e para especificar se deve ser gerada uma transcrição de texto.

> [!NOTE]
> A aplicação de consola utiliza a [API do Azure Media Indexer](https://docs.microsoft.com/azure/media-services/media-services-process-content-with-indexer2) para gerar transcrições a partir da faixa de áudio do vídeo carregado. Os resultados são fornecidos no formato WebVTT. Para obter mais informações sobre este formato, veja [Formato de Faixas de Texto de Vídeo Web](https://developer.mozilla.org/en-US/docs/Web/API/WebVTT_API).

### <a name="initialize-and-processvideo-methods"></a>Métodos Initialize e ProcessVideo

Independentemente de as opções do programa provirem da linha de comandos ou das introduções interativas dos utilizadores, `Main()` chama em seguida `Initialize()` para criar as seguintes instâncias:

|Classe|Descrição|
|-|-|
|`AMSComponent`|Comprime ficheiros de vídeo antes de os submeter para moderação.|
|`AMSconfigurations`|Interface para os dados de configuração da aplicação encontrados em `App.config`.|
|`VideoModerator`| Carregamento, codificação, encriptação e moderação com o SDK dos Serviços de Multimédia do Azure|
|`VideoReviewApi`|Gere revisões de vídeo no serviço do Content Moderator|

Estas classes (exceto `AMSConfigurations`, que é simples) são abordadas mais detalhadamente nas próximas secções deste tutorial.

Por fim, os ficheiros de vídeo são processados um de cada vez ao chamar `ProcessVideo()` para cada um.

[!code-csharp[ProcessVideo](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/Program.cs?range=76-118)]

O método `ProcessVideo()` é relativamente simples. Permite realizar as seguintes operações pela ordem apresentada:

- Comprime o vídeo
- Carrega o vídeo para um elemento dos Serviços de Multimédia do Azure
- Cria uma tarefa dos Serviços de Multimédia do Azure para moderar o vídeo
- Cria uma revisão de vídeo no Content Moderator

As seguintes secções abordam mais detalhadamente alguns dos processos individuais invocados por `ProcessVideo()`. 

## <a name="compress-the-video"></a>Comprimir o vídeo

Para minimizar o tráfego de rede, a aplicação converte os ficheiros de vídeo no formato H.264 (MPEG-4 AVC) e dimensiona-os até estes ficarem com uma largura máxima de 640 píxeis. O codec H.264 é recomendado devido à sua elevada eficiência (taxa de compressão). A compressão é realizada com recurso à ferramenta de linha de comandos `ffmpeg` gratuita que está incluída na pasta `Lib` da solução Visual Studio. O formato dos ficheiros de entrada pode ser qualquer formato suportado por `ffmpeg`, incluindo os codecs e formatos de ficheiros de vídeo mais comuns.

> [!NOTE]
> Quando iniciar o programa com as opções da linha de comandos, especificará um diretório que contém os ficheiros de vídeo que serão submetidos para moderação. Todos os ficheiros neste diretório com a extensão de nome de ficheiro `.mp4` serão processados. Para processar outras extensões de nome de ficheiro, atualize o método `Main()` em `Program.cs` de forma a incluir as extensões pretendidas.

O código que comprime um único ficheiro de vídeo é a classe `AmsComponent` em `AMSComponent.cs`. O método responsável por esta funcionalidade é `CompressVideo()`, apresentado aqui.

[!code-csharp[CompressVideo](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/AMSComponent.cs?range=31-59)]

O código realiza os seguintes passos:

- Efetua verificações para garantir que a configuração em `App.config` contém todos os dados necessários
- Efetua verificações para garantir que o binário `ffmpeg` está presente
- Compila o nome de ficheiro de saída ao anexar `_c.mp4` ao nome de base do ficheiro (tal como `Example.mp4` -> `Example_c.mp4`)
- Cria uma cadeia de linha de comandos para realizar a conversão
- Inicia um processo `ffmpeg` com recurso à linha de comandos
- Aguarda que o vídeo seja processado

> [!NOTE]
> Se souber que os seus vídeos já foram comprimidos com o codec H.264 e têm as dimensões adequadas, pode reescrever `CompressVideo()` para ignorar a compressão.

O método devolve o nome de ficheiro do ficheiro de saída comprimido.

## <a name="upload-and-moderate-the-video"></a>Carregar e moderar o vídeo

É necessário armazenar o vídeo nos Serviços de Multimédia do Azure antes de o serviço do Content Moderator o processar. A classe `Program` em `Program.cs` dispõe de um método `CreateVideoStreamingRequest()` curto que devolve um objeto que representa o pedido de transmissão em fluxo para carregar o vídeo.

[!code-csharp[CreateVideoStreamingRequest](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/Program.cs?range=120-133)]

O objeto `UploadVideoStreamRequest` resultante é definido em `UploadVideoStreamRequest.cs` (e o respetivo principal `UploadVideoRequest` é definido em `UploadVideoRequest.cs`). Estas classes não são apresentadas aqui, dado serem curtas e servirem apenas para armazenar os dados de vídeo comprimidos e as informações sobre os mesmos. `UploadAssetResult` (`UploadAssetResult.cs`) é outra classe apenas de dados que é utilizada para armazenar os resultados do processo de carregamento. Agora é possível compreender estas linhas em `ProcessVideo()`:

[!code-csharp[ProcessVideoSnippet](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/Program.cs?range=91-104)]

Estas linhas realizam as seguintes tarefas:

- Criar um `UploadVideoStreamRequest` para carregar o vídeo comprimido
- Definir o sinalizador `GenerateVTT` do pedido se o utilizador tiver pedido uma transcrição de texto
- Chamar `CreateAzureMediaServicesJobToModerateVideo()` para realizar o carregamento e receber o resultado

## <a name="examine-video-moderation-code"></a>Examinar o código de moderação de vídeos

O método `CreateAzureMediaServicesJobToModerateVideo()` está em `VideoModerator.cs`, que contém a maior parte do código que interage com os Serviços de Multimédia do Azure. O código fonte do método é apresentado no seguinte excerto.

[!code-csharp[CreateAzureMediaServicesJobToModerateVideo](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoModerator.cs?range=230-283)]

Este código realiza as seguintes tarefas:

- Cria uma tarefa dos Serviços de Multimédia do Azure para concluir o processamento
- Adiciona tarefas para codificar o ficheiro de vídeo, para o moderar e para gerar uma transcrição texto
- Submete a tarefa ao carregar o ficheiro e iniciar o processamento
- Obtém os resultados da moderação, a transcrição de texto (se for pedida) e outras informações

## <a name="sample-video-moderation-output"></a>Saída de moderação de vídeos de exemplo

O resultado da tarefa de moderação de vídeos (veja [o guia de início rápido da moderação de vídeos](video-moderation-api.md)) é uma estrutura de dados JSON que contém os resultados de moderação. Estes resultados incluem uma discriminação dos fragmentos (imagens) que compõem o vídeo, cada um dos quais com eventos (clipes) que têm fotogramas chave que foram sinalizados para revisão. Cada fotograma chave é classificado em função da probabilidade de conter conteúdos inadequados ou para adultos. O seguinte exemplo mostra uma resposta JSON:

```json
{
    "version": 2,
    "timescale": 90000,
    "offset": 0,
    "framerate": 50,
    "width": 1280,
    "height": 720,
    "totalDuration": 18696321,
    "fragments": [
    {
        "start": 0,
        "duration": 18000
    },
    {
        "start": 18000,
        "duration": 3600,
        "interval": 3600,
        "events": [
        [
        {
            "reviewRecommended": false,
            "adultScore": 0.00001,
            "racyScore": 0.03077,
            "index": 5,
            "timestamp": 18000,
            "shotIndex": 0
        }
        ]
    ]
    },
    {
        "start": 18386372,
        "duration": 119149,
        "interval": 119149,
        "events": [
        [
        {
            "reviewRecommended": true,
            "adultScore": 0.00000,
            "racyScore": 0.91902,
            "index": 5085,
            "timestamp": 18386372,
            "shotIndex": 62
        }
    ]
    ]
    }
]
}
```

Também é produzida uma transcrição de áudio a partir do vídeo quando o sinalizador `GenerateVTT` é definido.

> [!NOTE]
> A aplicação de consola utiliza a [API do Azure Media Indexer](https://docs.microsoft.com/azure/media-services/media-services-process-content-with-indexer2) para gerar transcrições a partir da faixa de áudio do vídeo carregado. Os resultados são fornecidos no formato WebVTT. Para obter mais informações sobre este formato, veja [Formato de Faixas de Texto de Vídeo Web](https://developer.mozilla.org/en-US/docs/Web/API/WebVTT_API).

## <a name="create-a-the-human-in-the-loop-review"></a>Criar um a revisão de humanos em loop

O processo de moderação devolve uma lista de fotogramas chave do vídeo, juntamente com uma transcrição das respetivas faixas de áudio. O próximo passo consiste em criar uma revisão na ferramenta de revisão do Content Moderator para moderadores humanos. Se regressar ao método `ProcessVideo()` em `Program.cs`, verá a chamada para o método `CreateVideoReviewInContentModerator()`. Este método está na classe `videoReviewApi`, que está em `VideoReviewAPI.cs` e é apresentada aqui.

[!code-csharp[CreateVideoReviewInContentModerator](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoReviewAPI.cs?range=42-69)]

`CreateVideoReviewInContentModerator()` chama vários outros métodos para realizar as seguintes tarefas:

> [!NOTE]
> A aplicação de consola utiliza a biblioteca [FFmpeg](https://ffmpeg.org/) para gerar miniaturas. Estas miniaturas (imagens) correspondem aos carimbos de data/hora dos fotogramas na [saída de moderação de vídeos](#sample-video-moderation-response).

|Tarefa|Métodos|Ficheiro|
|-|-|-|
|Extrair os fotogramas chave do vídeo e criar imagens em miniatura dos mesmos|`CreateVideoFrames()`<br>`GenerateFrameImages()`|`FrameGeneratorServices.cs`|
|Analisar a transcrição de texto (se esta estiver disponível) para localizar áudio inadequado ou para adultos|`GenerateTextScreenProfanity()`| `VideoReviewAPI.cs`|
|Preparar e submeter um pedido de revisão de vídeo para efeitos de inspeção manual|`CreateReviewRequestObject()`<br> `ExecuteCreateReviewApi()`<br>`CreateAndPublishReviewInContentModerator()`|`VideoReviewAPI.cs`|

O seguinte ecrã mostra os resultados dos passos anteriores.

![Vista predefinida de revisão de vídeo](images/video-tutorial-default-view.PNG)

## <a name="process-the-transcript"></a>Processo de transcrição

Até agora, o código apresentado neste tutorial incidiu nos conteúdos visuais. A revisão dos conteúdos de voz é um processo separado e opcional que, conforme mencionado anteriormente, utiliza uma transcrição gerada a partir do áudio. Agora é altura de ver como é que as transcrições de texto são criadas e utilizadas no processo de revisão. É o serviço do [Azure Media Indexer](https://docs.microsoft.com/azure/media-services/media-services-index-content) que realiza a tarefa de geração da transcrição.

A aplicação realiza as seguintes tarefas:

|Tarefa|Métodos|Ficheiro|
|-|-|-|
|Determinar se serão geradas transcrições de texto|`Main()`<br>`GetUserInputs()`|`Program.cs`|
|Se forem, submeter uma tarefa de transcrição como parte da moderação|`ConfigureTranscriptTask()`|`VideoModerator.cs`|
|Obter uma cópia local da transcrição|`GenerateTranscript()`|`VideoModerator.cs`|
|Sinalizar fotogramas do vídeo que contenham áudio inadequado|`GenerateTextScreenProfanity()`<br>`TextScreen()`|`VideoReviewAPI.cs`|
|Adicionar os resultados à revisão|`UploadScreenTextResult()`<br>`ExecuteAddTranscriptSupportFile()`|`VideoReviewAPI.cs`|

### <a name="task-configuration"></a>Configuração das tarefas

Vamos avançar para a submissão da tarefa de transcrição. `CreateAzureMediaServicesJobToModerateVideo()` (descrito anteriormente) chama `ConfigureTranscriptTask()`.

[!code-csharp[ConfigureTranscriptTask](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoModerator.cs?range=295-304)]

A configuração da tarefa de transcrição é lida a partir do ficheiro `MediaIndexerConfig.json` que se encontra na pasta `Lib` da solução. Os elementos dos Serviços de Multimédia do Azure são criados para o ficheiro de configuração e para a saída do processo de transcrição. Quando a tarefa dos Serviços de Multimédia do Azure é executada, cria uma transcrição de texto a partir da faixa de áudio do ficheiro de vídeo.

> [!NOTE]
> O exemplo de aplicação só reconhece voz em inglês dos E.U.A.

### <a name="transcript-generation"></a>Geração de transcrições

A transcrição é publicada sob a forma de elemento dos Serviços de Multimédia do Azure. Para analisar a transcrição de forma a identificar eventuais conteúdos ofensivos, a aplicação transfere o elemento a partir dos Serviços de Multimédia do Azure. `CreateAzureMediaServicesJobToModerateVideo()` chama `GenerateTranscript()`, conforme mostrado aqui, para obter o ficheiro.

[!code-csharp[GenerateTranscript](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoModerator.cs?range=351-370)]

Após alguma configuração necessária dos Serviços de Multimédia do Azure, a transferência efetiva é realizada ao chamar `DownloadAssetToLocal()`, uma função genérica que copia um elemento dos AMS para um ficheiro local.

## <a name="moderate-the-transcript"></a>Moderar transcrição

Com a transcrição à disposição, esta é analisada e utilizada na revisão. Criar a revisão é da competência de `CreateVideoReviewInContentModerator()`, que chama `GenerateTextScreenProfanity()` para realizar a tarefa. Este método, por sua vez, chama `TextScreen()`, que contém a maior parte da funcionalidade.

`TextScreen()` realiza as seguintes tarefas:

- Analisar a transcrição para identificar carimbos de data/hora e legendas
- Submeter todas a legendas para efeitos de moderação de textos
- Sinalizar os fotogramas com conteúdos de voz ofensivos

Vamos agora examinar cada uma destas tarefas de forma mais detalhada:

### <a name="initialize-the-code"></a>Inicializar o código

Em primeiro lugar, deve inicializar todas as variáveis e coleções.

[!code-csharp[TextScreen](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoReviewAPI.cs?range=515-527)]

### <a name="parse-the-transcript-for-captions"></a>Analisar a transcrição para identificar legendas

Em seguida, deve analisar a transcrição em formato VTT para identificar legendas e carimbos de data/hora. A ferramenta de revisão apresenta estas legendas no separador Transcript (Transcrição) que se encontra no ecrã de revisão de vídeo. Os carimbos de data/hora são utilizados para sincronizar as legendas com os fotogramas do vídeo correspondentes.

[!code-csharp[TextScreen2](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoReviewAPI.cs?range=528-567)]

### <a name="moderate-captions-with-the-text-moderation-service"></a>Moderar legendas através do serviço de moderação de textos

Em seguida, é necessário analisar as legendas de texto analisadas com recurso à API de texto do Content Moderator.

> [!NOTE]
> A chave do serviço do Content Moderator tem um limite de velocidade de pedidos por segundo (RPS). Se ultrapassar o limite, o SDK emite uma exceção com o código de erro 429.
>
> Uma chave de escalão gratuito tem um limite de velocidade de um RPS.

[!code-csharp[TextScreen3](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoReviewAPI.cs?range=568-653)]

### <a name="text-moderation-breakdown"></a>Divisão de moderação de texto

`TextScreen()` é um método substancial, por isso vamos analisá-lo por partes.

1. Em primeiro lugar, o método lê o ficheiro de transcrição linha por linha. O método ignora linhas em branco e as linhas que contêm uma `NOTE` com uma pontuação de confiança. Extrai os carimbos de data/hora e os itens de texto a partir de *pistas* que constam no ficheiro. As pistas representam o texto da faixa de áudio e incluem horas de início e fim. As pistas começam com a linha do carimbo de data/hora com a cadeia de caracteres `-->`. A isto segue-se uma ou mais linhas de texto.

1. As instâncias de `CaptionScreentextResult` (definido em `TranscriptProfanity.cs`) são utilizadas para armazenar as informações analisadas de cada pista.  Quando se deteta uma nova linha de carimbo de data/hora ou se atinge um comprimento do texto máximo de 1024 carateres, é adicionado um novo `CaptionScreentextResult` a `csrList`. 

1. Em seguida, o método submete cada pista à API de Moderação de Textos. O método chama `ContentModeratorClient.TextModeration.DetectLanguageAsync()` e `ContentModeratorClient.TextModeration.ScreenTextWithHttpMessagesAsync()`, que são definidas na assemblagem `Microsoft.Azure.CognitiveServices.ContentModerator`. Para evitar o limite de velocidade, o método faz uma pausa durante um segundo antes de submeter cada pista.

1. Após receber os resultados do serviço de Moderação de Textos, o método analisa-os para verificar se cumprem limiares de confiança. Estes valores são estabelecidos em `App.config` como `OffensiveTextThreshold`, `RacyTextThreshold` e `AdultTextThreshold`. Por fim, os termos ofensivos também são armazenados. Todos os fotogramas que se encontram dentro do intervalo de tempo da pista são sinalizados como contendo texto ofensivo, inadequado e/ou para adultos.

1. `TextScreen()` devolve uma instância `TranscriptScreenTextResult` que contém o resultado da moderação do texto do vídeo como um todo. Este objeto inclui sinalizadores e pontuações para os vários tipos de conteúdos ofensivos, juntamente com uma lista de todos os termos ofensivos. O chamador, `CreateVideoReviewInContentModerator()`, chama `UploadScreenTextResult()` para anexar estas informações à revisão de modo a estarem disponível aos revisores humanos.

O seguinte ecrã mostra o resultado dos passos de geração de transcrições e de moderação.

![Vista de transcrição da moderação de vídeos](images/video-tutorial-transcript-view.PNG)

## <a name="program-output"></a>Saída do programa

A seguinte saída da linha de comandos do programa mostra as diversas tarefas à medida que são concluídas. Além disso, o resultado da moderação (em formato JSON) e a transcrição de voz estão disponíveis no mesmo diretório dos ficheiros de vídeo originais.

```console
Microsoft.ContentModerator.AMSComponentClient
Enter the fully qualified local path for Uploading the video :
"Your File Name.MP4"
Generate Video Transcript? [y/n] : y

Video compression process started...
Video compression process completed...

Video moderation process started...
Video moderation process completed...

Video review process started...
Video Frames Creation inprogress...
Frames(83) created successfully.
Review Created Successfully and the review Id 201801va8ec2108d6e043229ba7a9e6373edec5
Video review successfully completed...

Total Elapsed Time: 00:05:56.8420355
```

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, configurou um aplicativo que moderates conteúdo de vídeo&mdash;incluindo conteúdo de transcrição&mdash;e cria as revisões na ferramenta de revisão. Em seguida, saiba mais sobre os detalhes de moderação de vídeo.

> [!div class="nextstepaction"]
> [Moderação de vídeo](./video-moderation-human-review.md)
