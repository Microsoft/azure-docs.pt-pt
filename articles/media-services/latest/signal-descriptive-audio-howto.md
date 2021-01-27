---
title: Faixas de áudio descritivas de sinal com Azure Media Services v3
description: Siga os passos deste tutorial para fazer o upload de um ficheiro, codificar o vídeo, adicionar faixas de áudio descritivas e transmitir o seu conteúdo com o Media Services v3.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: how-to
ms.custom: devx-track-csharp
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 155e79f8d42988d8498c52c0f930bc03bc599cf0
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98898227"
---
# <a name="signal-descriptive-audio-tracks"></a>Faixas de áudio descritivas de sinal

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Pode adicionar uma faixa de narração ao seu vídeo para ajudar os clientes com deficiência visual a seguirem a gravação de vídeo ouvindo a narração. Nos Serviços de Comunicação social v3, sinaliza faixas de áudio descritivas anotando a faixa áudio no ficheiro manifesto.

Este artigo mostra como codificar um vídeo, carregar um ficheiro MP4 (código AAC) contendo áudio descritivo no ativo de saída e editar o ficheiro .ism para incluir o áudio descritivo.

## <a name="prerequisites"></a>Pré-requisitos

- [Criar uma conta de Serviços de Comunicação](./create-account-howto.md)Social.
- Siga os passos na [Access Azure Media Services API com o Azure CLI](./access-api-howto.md) e guarde as credenciais. Terá de usá-los para aceder à API.
- Rever [embalagem dinâmica.](dynamic-packaging-overview.md)
- Reveja o [tutorial de upload, codificação e stream](stream-files-tutorial-with-api.md) de vídeos.

## <a name="create-an-input-asset-and-upload-a-local-file-into-it"></a>Criar um elemento de entrada e carregar um ficheiro local para ele 

A função **CreateInputAsset** cria um novo [Elemento](/rest/api/media/assets) de entrada e carrega o ficheiro de vídeo local especificado para ele. Este **Ativo** é utilizado como entrada para o seu trabalho de codificação. Nos Serviços de Comunicação social v3, a entrada para um **Job** pode ser um **Ativo,** ou pode ser conteúdo que disponibiliza à sua conta de Media Services através de URLs HTTPS. 

Se quiser aprender a codificar a partir de um URL HTTPS, consulte [este artigo](job-input-from-http-how-to.md) .  

Nos Serviços de Multimédia v3, deverá utilizar as APIs do Armazenamento do Azure para carregar os ficheiros. O seguinte fragmento de .NET mostra como.

A função seguinte executa estes passos:

* Cria um **Ativo** 
* Obtém um [URL SAS](../../storage/common/storage-sas-overview.md) writable para o contentor do ativo [no armazenamento](../../storage/blobs/storage-quickstart-blobs-dotnet.md#upload-blobs-to-a-container)
* Carrega o ficheiro para o contentor de armazenamento através do URL de SAS

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

Se precisar de passar o nome do ativo de entrada criado para outros métodos, certifique-se de utilizar a `Name` propriedade no objeto do ativo devolvido de , por `CreateInputAssetAsync` exemplo, inputAsset.Name. 

## <a name="create-an-output-asset-to-store-the-result-of-the-encoding-job"></a>Criar um ativo de saída para armazenar o resultado do trabalho de codificação

A saída [Asset](/rest/api/media/assets) armazena o resultado da tarefa de codificação. A seguinte função mostra como criar um ativo de saída.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateOutputAsset)]

Se precisar de passar o nome do ativo de saída criado para outros métodos, certifique-se de utilizar a `Name` propriedade no objeto do ativo devolvido de , por `CreateIOutputAssetAsync` exemplo, outputAsset.Name. 

No caso deste artigo, passe o `outputAsset.Name` valor para as `SubmitJobAsync` `UploadAudioIntoOutputAsset` funções e funções.

## <a name="create-a-transform-and-a-job-that-encodes-the-uploaded-file"></a>Crie uma transformação e um trabalho que codifica o ficheiro carregado

Ao codificar ou processar conteúdos nos Serviços de Multimédia, é um padrão comum configurar as definições de codificação como uma receita. Em seguida, deverá submeter uma **Tarefa** para aplicar essa receita a um vídeo. Ao apresentar novos empregos para cada novo vídeo, está a aplicar essa receita em todos os vídeos da sua biblioteca. Uma receita nos Serviços de Multimédia chama-se uma **Transformação**. Para obter mais informações, veja [Transforms and Jobs](./transforms-jobs-concept.md) (Transformações e Trabalhos). O exemplo descrito neste tutorial define uma receita que codifica o vídeo para transmiti-lo numa variedade de dispositivos iOS e Android. 

O exemplo a seguir cria uma transformação (se não existir).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#EnsureTransformExists)]

A seguinte função submete um emprego.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>Aguarde até que a tarefa termine

A tarefa demora algum tempo a terminar, por isso irá querer receber uma notificação quando for concluída. Recomendamos a utilização da Grade de Eventos para aguardar que o trabalho esteja concluído.

O trabalho geralmente passa pelos seguintes estados: **Programado,** **Fila,** **Processamento,** **Terminado** (o estado final). Se a tarefa encontrar um erro, obterá um estado de **Erro**. Se a tarefa estiver a ser cancelada, verá **A cancelar** e **Cancelada** quando terminar.

Para obter mais informações, consulte [eventos de Grelha de Eventos de Gestão](reacting-to-media-services-events.md).

## <a name="upload-the-audio-only-mp4-file"></a>Faça o upload do ficheiro MP4 apenas áudio

Faça o upload do ficheiro MP4 adicional apenas áudio (codec AAC) contendo áudio descritivo no ativo de saída.  

```csharp
private static async Task UpoadAudioIntoOutputAsset(
    IAzureMediaServicesClient client,
    string resourceGroupName,
    string accountName,
    string outputAssetName,
    string fileToUpload)
{
    // Use the Assets.Get method to get the existing asset. 
    // In Media Services v3, the Get method on entities returns null 
    // if the entity doesn't exist (a case-insensitive check on the name).

    // Call Media Services API to create an Asset.
    // This method creates a container in storage for the Asset.
    // The files (blobs) associated with the asset will be stored in this container.
    Asset asset = await client.Assets.GetAsync(resourceGroupName, accountName, outputAssetName);
    
    if (asset != null)
    {
      // Use Media Services API to get back a response that contains
      // SAS URL for the Asset container into which to upload blobs.
      // That is where you would specify read-write permissions 
      // and the exparation time for the SAS URL.
      var response = await client.Assets.ListContainerSasAsync(
          resourceGroupName,
          accountName,
          outputAssetName,
          permissions: AssetContainerPermission.ReadWrite,
          expiryTime: DateTime.UtcNow.AddHours(4).ToUniversalTime());

      var sasUri = new Uri(response.AssetContainerSasUrls.First());

      // Use Storage API to get a reference to the Asset container
      // that was created by calling Asset's CreateOrUpdate method.  
      CloudBlobContainer container = new CloudBlobContainer(sasUri);
      var blob = container.GetBlockBlobReference(Path.GetFileName(fileToUpload));

      // Use Strorage API to upload the file into the container in storage.
      await blob.UploadFromFileAsync(fileToUpload);
    }
}
```

Aqui está um exemplo de uma chamada para a `UpoadAudioIntoOutputAsset` função:

```csharp
await UpoadAudioIntoOutputAsset(client, config.ResourceGroup, config.AccountName, outputAsset.Name, "audio_description.m4a");
```

## <a name="edit-the-ism-file"></a>Editar o ficheiro .ism

Quando o seu trabalho de codificação estiver feito, o ativo de saída conterá os ficheiros gerados pelo trabalho de codificação. 

1. No portal Azure, navegue para a conta de armazenamento associada à sua conta de Media Services. 
1. Encontre o recipiente com o nome do seu ativo de saída. 
1. No recipiente, encontre o ficheiro .ism e clique em **Editar blob** (na janela direita). 
1. Editar o ficheiro .ism adicionando as informações sobre o ficheiro MP4 (código AAC) carregado apenas com áudio e pressione **Guardar** quando for feito.

    Para sinalizar as faixas de áudio descritivas, é necessário adicionar parâmetros de "acessibilidade" e "função" ao ficheiro .ism. É da sua responsabilidade definir corretamente estes parâmetros para sinalizar uma faixa áudio como descrição áudio. Por exemplo, adicione `<param name="accessibility" value="description" />` e `<param name="role" value="alternate" />` ao ficheiro .ism para uma faixa áudio específica, como mostra o exemplo seguinte.
 
```xml
<?xml version="1.0" encoding="utf-8"?>
<smil xmlns="http://www.w3.org/2001/SMIL20/Language">
  <head>
    <meta name="clientManifestRelativePath" content="ignite.ismc" />
    <meta name="formats" content="mp4-v3" />
  </head>
  <body>
    <switch>
      <audio src="ignite_320x180_AACAudio_381.mp4" systemBitrate="128041" systemLanguage="eng">
        <param name="systemBitrate" value="128041" valuetype="data" />
        <param name="trackID" value="2" valuetype="data" />
        <param name="trackName" value="aac_eng_2_128041_2_1" valuetype="data" />
        <param name="systemLanguage" value="eng" valuetype="data" />
        <param name="trackIndex" value="ignite_320x180_AACAudio_381_2.mpi" valuetype="data" />
      </audio>
      <audio src="audio_description.m4a" systemBitrate="194000" systemLanguage="eng">
        <param name="trackName" value="aac_eng_audio_description" />
        <param name="accessibility" value="description" />
        <param name="role" value="alternate" />     
      </audio>          
      <video src="ignite_1280x720_AACAudio_3549.mp4" systemBitrate="3549855">
        <param name="systemBitrate" value="3549855" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_1280x720_AACAudio_3549_1.mpi" valuetype="data" />
      </video>
      <video src="ignite_960x540_AACAudio_2216.mp4" systemBitrate="2216764">
        <param name="systemBitrate" value="2216764" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_960x540_AACAudio_2216_1.mpi" valuetype="data" />
      </video>
      <video src="ignite_640x360_AACAudio_1154.mp4" systemBitrate="1154569">
        <param name="systemBitrate" value="1154569" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_640x360_AACAudio_1154_1.mpi" valuetype="data" />
      </video>
      <video src="ignite_480x270_AACAudio_721.mp4" systemBitrate="721893">
        <param name="systemBitrate" value="721893" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_480x270_AACAudio_721_1.mpi" valuetype="data" />
      </video>
      <video src="ignite_320x180_AACAudio_381.mp4" systemBitrate="381027">
        <param name="systemBitrate" value="381027" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_320x180_AACAudio_381_1.mpi" valuetype="data" />
      </video>
    </switch>
  </body>
</smil>
```

## <a name="get-a-streaming-locator"></a>Obtenha um localizador de streaming

Depois de concluída a codificação, o passo seguinte consiste em disponibilizar o vídeo no Elemento de saída para reprodução para os clientes. Pode fazê-lo em dois passos: primeiro, criar um [Localizador de Streaming](/rest/api/media/streaminglocators)e, em segundo lugar, construir os URLs de streaming que os clientes podem usar. 

O processo de criação de um **Localizador de Streaming** chama-se publicação. Por predefinição, o **Localizador de Streaming** é válido imediatamente após a edição da API e dura até ser eliminado, a menos que configuure os tempos de início e fim opcionais. 

Ao criar um [StreamingLocator](/rest/api/media/streaminglocators), terá de especificar o **StreamingPolicyName** pretendido. Neste exemplo, estará a transmitir conteúdo limpo (ou não encriptado) para que seja utilizada a política de streaming clara **predefinida (PredefinedStreamingPolicy.ClearStreamingOnly).**

> [!IMPORTANT]
> Ao utilizar uma [Política de Streaming](/rest/api/media/streamingpolicies)personalizada, deverá conceber um conjunto limitado de tais políticas para a sua conta de Media Service e reutilizá-las para os seus StreamingLocators sempre que forem necessárias as mesmas opções e protocolos de encriptação. A sua conta de Media Service tem uma quota para o número de entradas na Política de Streaming. Não deve criar uma nova Política de Streaming para cada Localizador de Streaming.

O código seguinte parte do princípio de que está a chamar a função com um locatorName exclusivo.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateStreamingLocator)]

Enquanto a amostra deste tópico discute o streaming, você pode usar a mesma chamada para criar um Localizador de Streaming para entregar vídeo através de download progressivo.

### <a name="get-streaming-urls"></a>Obter os URLs de transmissão

Agora que o [Localizador de Streaming](/rest/api/media/streaminglocators) foi criado, pode obter os URLs de streaming, como mostrado no **GetStreamingURLs**. Para construir um URL, é necessário concatenar o nome do anfitrião [streaming Endpoint](/rest/api/media/streamingendpoints) e o caminho **do localizador de streaming.** Nesta amostra, é utilizado o ponto final de streaming *predefinido.*  Quando criar uma conta de Serviço de Mídia, este **ponto final de streaming** *predefinido* estará num estado parado, pelo que tem de ligar para **o Start**.

> [!NOTE]
> Neste método, você precisa do nome localizador que foi usado ao criar o **Localizador de Streaming** para o Ativo de saída.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#GetStreamingURLs)]

## <a name="test-with-azure-media-player"></a>Testar com o Leitor de Multimédia do Azure

Para testar a transmissão, este artigo utiliza o Leitor de Multimédia do Azure. 

> [!NOTE]
> Se um leitor estiver alojado num site de https, confirme que atualiza o URL para “https”.

1. Abra um navegador web e navegue para [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/) .
2. No **URL:** caixa, cole um dos valores de URL de streaming que obteve da sua aplicação. 
 
     Pode colar o URL no formato HLS, Dash ou Smooth e o Azure Media Player mudará para um protocolo de streaming apropriado para reprodução no seu dispositivo automaticamente.
3. Prima **Atualizar Leitor**.

O Leitor de Multimédia do Azure pode ser utilizado para fins de teste, mas não deve ser utilizado num ambiente de produção. 

## <a name="next-steps"></a>Próximos passos

[Analisar vídeos](analyze-videos-tutorial-with-api.md)
