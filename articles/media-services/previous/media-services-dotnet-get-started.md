---
title: Introdução à entrega de conteúdo a pedido com o .NET | Microsoft Docs
description: Este tutorial vai ajudá-lo a implementar uma aplicação de distribuição de conteúdos a pedido com os Media Services do Azure utilizando o .NET.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 388b8928-9aa9-46b1-b60a-a918da75bd7b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 2d55af3e9ed3ad64f9ba7726799b31acb6b48580
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57884477"
---
# <a name="get-started-with-delivering-content-on-demand-using-net-sdk"></a>Introdução à distribuição de conteúdos a pedido utilizando o SDK do .NET  

[!INCLUDE [media-services-selector-get-started](../../../includes/media-services-selector-get-started.md)]

Este tutorial explica os passos para implementar um serviço básico de entrega de conteúdos de Vídeo a Pedido (VoD) com a aplicação Azure Media Services (AMS) através do .NET SDK dos Serviços de Multimédia do Azure.

## <a name="prerequisites"></a>Pré-requisitos

O seguinte é necessário para concluir o tutorial:

* Uma conta do Azure. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* Uma conta dos Media Services. Para criar uma conta dos Media Services, consulte [Como Criar uma Conta de Media Services](media-services-portal-create-account.md).
* .NET Framework 4.0 ou posterior.
* Visual Studio.

Este tutorial inclui as seguintes tarefas:

1. Iniciar pontos finais de transmissão em fluxo (com o portal do Azure).
2. Criar e configurar um projeto de Visual Studio.
3. Ligar à conta de Media Services.
2. Carregar um ficheiro de vídeo.
3. Codificar o ficheiro de origem para um conjunto de ficheiros MP4 de velocidade de transmissão adaptável.
4. Publicar o elemento e obter os URLs de transferência progressiva e transmissão em fluxo.  
5. Reproduzir os conteúdos.

## <a name="overview"></a>Descrição geral
Este tutorial vai ajudá-lo a implementar uma aplicação de distribuição de conteúdos de Vídeo a Pedido (VoD) utilizando o SDK do .NET dos Media Services do Azure (AMS).

O tutorial apresenta o fluxo de trabalho básico dos Media Services, os objetos de programação mais comuns e as tarefas necessárias para o desenvolvimento dos Media Services. No final do tutorial, será capaz de transmitir ou transferir progressivamente um ficheiro de multimédia de exemplo que carregou, codificou e transferiu.

### <a name="ams-model"></a>Modelo de AMS

A imagem seguinte mostra alguns objetos mais frequentemente utilizados ao desenvolver aplicações VoD contra o modelo OData dos Serviços de Multimédia.

Clique na imagem para visualizá-lo tamanho completo.  

<a href="./media/media-services-dotnet-get-started/media-services-overview-object-model.png" target="_blank"><img src="./media/media-services-dotnet-get-started/media-services-overview-object-model-small.png"></a> 

Pode ver o modelo de todo [aqui](https://media.windows.net/API/$metadata?api-version=2.15).  

## <a name="start-streaming-endpoints-using-the-azure-portal"></a>Iniciar pontos finais de transmissão em fluxo com o portal do Azure

Ao trabalhar com os Serviços de Multimédia do Azure, uma das situações mais comuns é a entrega de vídeo através de transmissão em fluxo de velocidade de transmissão adaptável. Os Serviços de Multimédia fornecem um empacotamento dinâmico, o que lhe permite entregar os seus conteúdos codificados em MP4 de velocidade de transmissão adaptável em formatos de transmissão em fluxo suportados pelos Serviços de Multimédia (MPEG DASH, HLS, Smooth Streaming) just-in-time, sem ter de armazenar versões pré-empacotadas de cada um destes formatos de transmissão em fluxo.

>[!NOTE]
>Quando a sua conta AMS é criada, é adicionado um ponto final de transmissão em fluxo **predefinido** à sua conta no estado **Parado**. Para começar a transmitir o seu conteúdo em fluxo e a tirar partido do empacotamento e encriptação dinâmicos, o ponto final de transmissão em fluxo a partir do qual quer transmitir conteúdo tem de estar no estado **Em execução**.

Para iniciar o ponto final de transmissão em fluxo, faça o seguinte:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. Na janela Definições, clique em Pontos finais de transmissão em fluxo.
3. Clique no ponto final de transmissão em fluxo predefinido.

    A janela DETALHES DO PONTO FINAL DE TRANSMISSÃO EM FLUXO PREDEFINIDO é apresentada.

4. Clique no ícone Início.
5. Clique no botão Guardar para guardar as alterações.

## <a name="create-and-configure-a-visual-studio-project"></a>Criar e configurar um projeto de Visual Studio

1. Configure o seu ambiente de desenvolvimento e preencha o ficheiro app.config com informações da ligação, conforme descrito em [Media Services development with .NET](media-services-dotnet-how-to-use.md) (Desenvolvimento de Serviços de Multimédia com .NET). 
2. Crie uma nova pasta (a pasta pode estar em qualquer local na unidade local) e copie um ficheiro .mp4 que pretende codificar e transmitir ou transferir progressivamente. Neste exemplo, é utilizado o caminho "C:\VideoFiles".

## <a name="connect-to-the-media-services-account"></a>Ligar à conta de Media Services

Quando utilizar os Media Services com .NET, deve utilizar a classe **CloudMediaContext** na maioria das tarefas de programação dos Media Services: ligar à conta de Media Services; criar, atualizar, aceder e eliminar os seguintes objetos: elementos, ficheiros de elementos, tarefas, políticas de acesso, localizadores, etc.

Substitua a classe Program predefinida com o código a seguir: O código demonstra como ler os valores de ligação a partir do ficheiro App.config e como criar o objeto **CloudMediaContext** para se ligar aos Media Services. Para obter mais informações, veja [connecting to the Media Services API](media-services-use-aad-auth-to-access-ams-api.md) (ligar à API de Serviços de Multimédia).

Certifique-se de atualizar o nome do ficheiro e o caminho onde o ficheiro do suporte de dados se encontra.

Os métodos de chamadas de função **Main** que serão definidos posteriormente nesta secção.

> [!NOTE]
> Receberá erros de compilação até adicionar definições para todas as funções que estão definidas neste artigo.

```csharp
    class Program
    {
        // Read values from the App.config file.
        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
        private static readonly string _AMSClientId =
            ConfigurationManager.AppSettings["AMSClientId"];
        private static readonly string _AMSClientSecret =
            ConfigurationManager.AppSettings["AMSClientSecret"];

        private static CloudMediaContext _context = null;

        static void Main(string[] args)
        {
        try
        {
            AzureAdTokenCredentials tokenCredentials = 
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            // Add calls to methods defined in this section.
            // Make sure to update the file name and path to where you have your media file.
            IAsset inputAsset =
            UploadFile(@"C:\VideoFiles\BigBuckBunny.mp4", AssetCreationOptions.None);

            IAsset encodedAsset =
            EncodeToAdaptiveBitrateMP4s(inputAsset, AssetCreationOptions.None);

            PublishAssetGetURLs(encodedAsset);
        }
        catch (Exception exception)
        {
            // Parse the XML error message in the Media Services response and create a new
            // exception with its content.
            exception = MediaServicesExceptionParser.Parse(exception);

            Console.Error.WriteLine(exception.Message);
        }
        finally
        {
            Console.ReadLine();
        }
        }
```

## <a name="create-a-new-asset-and-upload-a-video-file"></a>Criar um novo elemento e carregar um ficheiro de vídeo

Nos Media Services, pode carregar (ou inserir) os seus ficheiros digitais num elemento. A entidade **Elemento** pode conter ficheiros de vídeo, áudio, imagens, coleções de miniaturas, pistas de texto e legendas (e os metadados relativos a esses ficheiros).  Assim que os ficheiros são carregados, o seu conteúdo é armazenado em segurança na nuvem para processamento adicional e a transmissão em fluxo. Os ficheiros no elemento são denominados **Ficheiros de Elemento**.

O método **UploadFile** definido abaixo chama **CreateFromFile** (definido nas Extensões do SDK do .NET). **CreateFromFile** cria um novo elemento no qual o ficheiro de origem especificado é carregado.

O método **CreateFromFile** recorre a **AssetCreationOptions**, que lhe permite especificar uma das seguintes opções de criação do elemento:

* **Nenhum** - Não é utilizada qualquer encriptação. Este é o valor predefinido. Tenha em atenção que, ao utilizar esta opção, o seu conteúdo não está protegido enquanto estiver em trânsito ou inativo no armazenamento.
  Se planear distribuir um MP4 utilizando uma transferência progressiva, utilize esta opção.
* **StorageEncrypted** - Utilize esta opção para encriptar o seu conteúdo transparente localmente utilizando a encriptação de 256 bit norma AES (Advanced Encryption Standard), posteriormente, esta carrega-o para o Storage do Azure onde é armazenado encriptados e permanece inativo. Os elementos protegidos com Encriptação do Storage são desencriptados automaticamente e colocados num sistema de ficheiros encriptados antes da codificação, sendo opcionalmente encriptados novamente antes de serem carregados novamente como um novo elemento de saída. O principal caso de utilização da Encriptação do Storage ocorre quando pretende proteger os seus ficheiros de multimédia de entrada de alta qualidade inativos no disco com uma encriptação forte.
* **CommonEncryptionProtected** - Utilize esta opção se estiver a carregar conteúdo que já foi encriptado e protegido com Encriptação Comum ou PlayReady DRM (por exemplo, Transmissão em Fluxo Uniforme protegida com PlayReady DRM).
* **EnvelopeEncryptionProtected** – Utilize esta opção se estiver a carregar HLS encriptado com AES. Tenha em atenção que os ficheiros têm de ser codificados e encriptados pelo Gestor de Transformação.

O método **CreateFromFile** também permite especificar uma chamada de retorno para comunicar o progresso do carregamento do ficheiro.

No exemplo seguinte, especificamos **Nenhum** para as opções do elemento.

Adicione o seguinte método à classe Program.

```csharp
    static public IAsset UploadFile(string fileName, AssetCreationOptions options)
    {
        IAsset inputAsset = _context.Assets.CreateFromFile(
            fileName,
            options,
            (af, p) =>
            {
                Console.WriteLine("Uploading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
            });

        Console.WriteLine("Asset {0} created.", inputAsset.Id);

        return inputAsset;
    }
```

## <a name="encode-the-source-file-into-a-set-of-adaptive-bitrate-mp4-files"></a>Codificar o ficheiro de origem para um conjunto de ficheiros MP4 de velocidade de transmissão adaptável
Após a inserção dos elementos nos Media Services, a multimédia pode ser codificada, formatada, receber uma marca de água digital e assim sucessivamente, antes de ser distribuída aos clientes. Estas atividades são agendadas e executadas em várias instâncias de função de segundo plano para assegurar um elevado desempenho e disponibilidade. Estas atividades são denominadas Tarefas, cada uma é composta por Tarefas atómicas que fazem o trabalho real no Ficheiro de elemento.

Tal como mencionado anteriormente, ao trabalhar com os Media Services do Azure, uma das situações mais comuns é a distribuição de transmissão em fluxo de velocidade de transmissão adaptável para os seus clientes. Serviços de multimédia podem empacotar dinamicamente um conjunto de ficheiros MP4 de velocidade de transmissão adaptável dos seguintes formatos: HTTP Live Streaming (HLS), Smooth Streaming e MPEG DASH.

Para tirar partido do empacotamento dinâmico, tem de codificar ou transcodificar o ficheiro (de origem) mezanino para um conjunto de ficheiros MP4 de velocidade de transmissão adaptável ou ficheiros Smooth Streaming de velocidade de transmissão adaptável.  

O código seguinte mostra como submeter uma tarefa de codificação. A tarefa contém uma tarefa que especifica a transcodificação do ficheiro de mezanino num conjunto de MP4s de velocidade de transmissão adaptável utilizando um **Codificador de Multimédia Standard**. O código submete a tarefa e aguarda até que esta esteja concluída.

Assim que a tarefa esteja concluída, será possível transmitir o seu elemento ou transferir progressivamente ficheiros MP4 que foram criados no seguimento da transcodificação

Adicione o seguinte método à classe Program.

```csharp
    static public IAsset EncodeToAdaptiveBitrateMP4s(IAsset asset, AssetCreationOptions options)
    {

        // Prepare a job with a single task to transcode the specified asset
        // into a multi-bitrate asset.

        IJob job = _context.Jobs.CreateWithSingleTask(
            "Media Encoder Standard",
            "Adaptive Streaming",
            asset,
            "Adaptive Bitrate MP4",
            options);

        Console.WriteLine("Submitting transcoding job...");


        // Submit the job and wait until it is completed.
        job.Submit();

        job = job.StartExecutionProgressTask(
            j =>
            {
                Console.WriteLine("Job state: {0}", j.State);
                Console.WriteLine("Job progress: {0:0.##}%", j.GetOverallProgress());
            },
            CancellationToken.None).Result;

        Console.WriteLine("Transcoding job finished.");

        IAsset outputAsset = job.OutputMediaAssets[0];

        return outputAsset;
    }
```

## <a name="publish-the-asset-and-get-urls-for-streaming-and-progressive-download"></a>Publicar o elemento e obter os URLs para transmissão em fluxo e transferência progressiva

Para transmitir ou transferir um elemento, primeiro tem de o "publicar" através da criação de um localizador. Os localizadores fornecem acesso aos ficheiros contidos no elemento. Os Media Services suportam dois tipos de localizadores: Ondemandorigin, utilizados para transmitir multimédia (por exemplo, MPEG DASH, HLS ou transmissão em fluxo uniforme) e localizadores de assinatura de acesso (SAS), utilizados para transferir ficheiros de suporte de dados.

### <a name="some-details-about-url-formats"></a>Alguns detalhes sobre os formatos de URL

Depois de criar os localizadores, pode criar os URLs que seriam utilizados para transmitir ou transferir os seus ficheiros. O exemplo neste tutorial origina URLs que pode colar nos browsers adequados. Esta secção disponibiliza apenas breves exemplos de como será o aspeto dos diferentes formatos.

#### <a name="a-streaming-url-for-mpeg-dash-has-the-following-format"></a>Um URL de transmissão em fluxo para MPEG DASH tem o seguinte formato:

{nome da conta do nome dos serviços multimédia do ponto final de transmissão em fluxo}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest **(format=mpd-time-csf)**

#### <a name="a-streaming-url-for-hls-has-the-following-format"></a>Um URL de transmissão em fluxo para HLS tem o seguinte formato:

{nome da conta do nome dos serviços multimédia do ponto final de transmissão em fluxo}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest **(format=m3u8-aapl)**

#### <a name="a-streaming-url-for-smooth-streaming-has-the-following-format"></a>Um URL de transmissão em fluxo para uma Transmissão em Fluxo Uniforme tem o seguinte formato:

{nome da conta do nome dos serviços multimédia do ponto final de transmissão em fluxo}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest


#### <a name="a-sas-url-used-to-download-files-has-the-following-format"></a>Um URL de SAS utilizado para transferir ficheiros tem o seguinte formato:

{blob container name}/{asset name}/{file name}/{SAS signature}

As extensões do SDK do .NET de Media Services fornecem métodos de programa auxiliar convenientes que devolvem URLs formatados para o elemento publicado.

O código seguinte utiliza Extensões do SDK do .NET para criar localizadores e obter URLs de transmissão em fluxo e transferência progressiva. O código também mostra como transferir ficheiros para uma pasta local.

Adicione o seguinte método à classe Program.

```csharp
    static public void PublishAssetGetURLs(IAsset asset)
    {
        // Publish the output asset by creating an Origin locator for adaptive streaming,
        // and a SAS locator for progressive download.

        _context.Locators.Create(
            LocatorType.OnDemandOrigin,
            asset,
            AccessPermissions.Read,
            TimeSpan.FromDays(30));

        _context.Locators.Create(
            LocatorType.Sas,
            asset,
            AccessPermissions.Read,
            TimeSpan.FromDays(30));


        IEnumerable<IAssetFile> mp4AssetFiles = asset
                .AssetFiles
                .ToList()
                .Where(af => af.Name.EndsWith(".mp4", StringComparison.OrdinalIgnoreCase));

        // Get the Smooth Streaming, HLS and MPEG-DASH URLs for adaptive streaming,
        // and the Progressive Download URL.
        Uri smoothStreamingUri = asset.GetSmoothStreamingUri();
        Uri hlsUri = asset.GetHlsUri();
        Uri mpegDashUri = asset.GetMpegDashUri();

        // Get the URls for progressive download for each MP4 file that was generated as a result
        // of encoding.
        List<Uri> mp4ProgressiveDownloadUris = mp4AssetFiles.Select(af => af.GetSasUri()).ToList();


        // Display  the streaming URLs.
        Console.WriteLine("Use the following URLs for adaptive streaming: ");
        Console.WriteLine(smoothStreamingUri);
        Console.WriteLine(hlsUri);
        Console.WriteLine(mpegDashUri);
        Console.WriteLine();

        // Display the URLs for progressive download.
        Console.WriteLine("Use the following URLs for progressive download.");
        mp4ProgressiveDownloadUris.ForEach(uri => Console.WriteLine(uri + "\n"));
        Console.WriteLine();

        // Download the output asset to a local folder.
        string outputFolder = "job-output";
        if (!Directory.Exists(outputFolder))
        {
            Directory.CreateDirectory(outputFolder);
        }

        Console.WriteLine();
        Console.WriteLine("Downloading output asset files to a local folder...");
        asset.DownloadToFolder(
            outputFolder,
            (af, p) =>
            {
                Console.WriteLine("Downloading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
            });

        Console.WriteLine("Output asset files available at '{0}'.", Path.GetFullPath(outputFolder));
    }
```

## <a name="test-by-playing-your-content"></a>Testar com a reprodução do seu conteúdo

Depois de executar o programa definido na secção anterior, os URLs semelhantes ao seguinte serão apresentados na janela da consola.

URLs de transmissão em fluxo adaptável:

Transmissão em Fluxo Uniforme

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest

HLS

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=m3u8-aapl)

MPEG DASH

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=mpd-time-csf)

URLs de transferência progressiva (áudio e vídeo).

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1500kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1000kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_56kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z


Para transmitir o vídeo, cole o URL na caixa de texto do URL no [Leitor dos Serviços de Multimédia do Azure](https://amsplayer.azurewebsites.net/azuremediaplayer.html).

Para testar as transferências progressivas, cole um URL num browser (por exemplo, Internet Explorer, Chrome ou Safari).

Para obter mais informações, consulte os seguintes tópicos:

- [Reproduzir o conteúdo com os leitores existentes](media-services-playback-content-with-existing-players.md)
- [Desenvolver aplicações de leitor de vídeo](media-services-develop-video-players.md)
- [Incorporar um Vídeo de Transmissão em fluxo Adaptivo MPEG-DASH numa Aplicação HTML5 com DASH.js](media-services-embed-mpeg-dash-in-html5.md)

## <a name="download-sample"></a>Transferir exemplo
O exemplo de código seguinte contém o código que criou neste tutorial: [exemplo](https://azure.microsoft.com/documentation/samples/media-services-dotnet-on-demand-encoding-with-media-encoder-standard/).

## <a name="next-steps"></a>Próximos Passos

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]



<!-- Anchors. -->


<!-- URLs. -->
[Web Platform Installer]: https://go.microsoft.com/fwlink/?linkid=255386
[Portal]: https://portal.azure.com/
