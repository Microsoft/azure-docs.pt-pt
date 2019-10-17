---
title: Sinalizar faixas de áudio descritivas com os serviços de mídia do Azure v3 | Microsoft Docs
description: Siga as etapas deste tutorial para carregar um arquivo, codificar o vídeo, adicionar faixas de áudio descritivas e transmitir seu conteúdo com os serviços de mídia v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 09/25/2019
ms.author: juliako
ms.openlocfilehash: 0d8f88e6c2fe273efa969278146de67ba18eaecf
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72392192"
---
# <a name="signal-descriptive-audio-tracks"></a>Sinalizar faixas de áudio descritivas

Você pode adicionar uma faixa de narração ao seu vídeo para ajudar os clientes com deficiência visual a seguirem a gravação de vídeo ouvindo a narração. Nos serviços de mídia v3, você sinaliza faixas de áudio descritivas anotando a faixa de áudio no arquivo de manifesto.

Este artigo mostra como codificar um vídeo, carregar um arquivo MP4 somente de áudio (codec AAC) que contém áudio descritivo no ativo de saída e editar o arquivo. ISM para incluir o áudio descritivo.

## <a name="prerequisites"></a>Pré-requisitos

- [Crie uma conta dos serviços de mídia](create-account-cli-how-to.md).
- Siga as etapas em [acessar a API dos serviços de mídia do Azure com o CLI do Azure](access-api-cli-how-to.md) e salve as credenciais. Você precisará usá-las para acessar a API.
- Examine o [empacotamento dinâmico](dynamic-packaging-overview.md).
- Examine o tutorial [carregar, codificar e transmitir vídeos](stream-files-tutorial-with-api.md) .

## <a name="create-an-input-asset-and-upload-a-local-file-into-it"></a>Criar um elemento de entrada e carregar um ficheiro local para ele 

A função **CreateInputAsset** cria um novo [Elemento](https://docs.microsoft.com/rest/api/media/assets) de entrada e carrega o ficheiro de vídeo local especificado para ele. Esse **ativo** é usado como a entrada para seu trabalho de codificação. Nos serviços de mídia v3, a entrada para um **trabalho** pode ser um **ativo**ou pode ser o conteúdo que você disponibiliza para sua conta de serviços de mídia por meio de URLs HTTPS. 

Se você quiser saber como codificar a partir de uma URL HTTPS, consulte [Este artigo](job-input-from-http-how-to.md) .  

Nos Serviços de Multimédia v3, deverá utilizar as APIs do Armazenamento do Azure para carregar os ficheiros. O seguinte fragmento de .NET mostra como.

A função seguinte executa estes passos:

* Cria um **ativo** 
* Obtém uma [URL SAS](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) gravável para o contêiner do ativo [no armazenamento](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet#upload-blobs-to-a-container)
* Carrega o ficheiro para o contentor de armazenamento através do URL de SAS

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

Se você precisar passar o nome do ativo de entrada criado para outros métodos, certifique-se de usar a propriedade `Name` no objeto de ativo retornado de `CreateInputAssetAsync`, por exemplo, inputAsset.Name. 

## <a name="create-an-output-asset-to-store-the-result-of-the-encoding-job"></a>Criar um ativo de saída para armazenar o resultado do trabalho de codificação

A saída [Asset](https://docs.microsoft.com/rest/api/media/assets) armazena o resultado da tarefa de codificação. A função a seguir mostra como criar um ativo de saída.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateOutputAsset)]

Se você precisar passar o nome do ativo de saída criado para outros métodos, certifique-se de usar a propriedade `Name` no objeto de ativo retornado de `CreateIOutputAssetAsync`, por exemplo, outputAsset.Name. 

No caso deste artigo, passe o valor `outputAsset.Name` para as funções `SubmitJobAsync` e `UploadAudioIntoOutputAsset`.

## <a name="create-a-transform-and-a-job-that-encodes-the-uploaded-file"></a>Criar uma transformação e um trabalho que codifica o arquivo carregado

Ao codificar ou processar conteúdos nos Serviços de Multimédia, é comum configurar as definições de codificação como uma receita. Em seguida, deverá submeter uma **Tarefa** para aplicar essa receita a um vídeo. Ao enviar novos trabalhos para cada novo vídeo, você está aplicando essa receita a todos os vídeos em sua biblioteca. Uma receita nos Serviços de Multimédia chama-se uma **Transformação**. Para obter mais informações, veja [Transforms and Jobs](transform-concept.md) (Transformações e Trabalhos). O exemplo descrito neste tutorial define uma receita que codifica o vídeo para transmiti-lo numa variedade de dispositivos iOS e Android. 

O exemplo a seguir cria uma transformação (se não houver uma).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#EnsureTransformExists)]

A função a seguir envia um trabalho.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>Aguarde até que a tarefa termine

A tarefa demora algum tempo a terminar, por isso irá querer receber uma notificação quando for concluída. É recomendável usar a grade de eventos para aguardar a conclusão do trabalho.

O trabalho geralmente passa pelos seguintes Estados: **agendado**, em **fila**, **processamento**, **concluído** (o estado final). Se a tarefa encontrar um erro, obterá um estado de **Erro**. Se a tarefa estiver a ser cancelada, verá **A cancelar** e **Cancelada** quando terminar.

Para obter mais informações, consulte [lidando com eventos de grade de eventos](reacting-to-media-services-events.md).

## <a name="upload-the-audio-only-mp4-file"></a>Carregar o arquivo MP4 somente de áudio

Carregue o arquivo MP4 adicional somente de áudio (codec AAC) que contém o áudio descritivo no ativo de saída.  

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

Aqui está um exemplo de uma chamada para a função `UpoadAudioIntoOutputAsset`:

```csharp
await UpoadAudioIntoOutputAsset(client, config.ResourceGroup, config.AccountName, outputAsset.Name, "audio_description.m4a");
```

## <a name="edit-the-ism-file"></a>Editar o arquivo. ISM

Quando o trabalho de codificação for concluído, o ativo de saída conterá os arquivos gerados pelo trabalho de codificação. 

1. Na portal do Azure, navegue até a conta de armazenamento associada à sua conta de serviços de mídia. 
1. Localize o contêiner com o nome do seu ativo de saída. 
1. No contêiner, localize o arquivo. ISM e clique em **Editar blob** (na janela à direita). 
1. Edite o arquivo. ISM adicionando as informações sobre o arquivo MP4 somente de áudio carregado (codec AAC) que contém áudio descritivo e pressione **salvar** quando terminar.

    Para sinalizar as faixas de áudio descritivas, você precisa adicionar os parâmetros "acessibilidade" e "função" ao arquivo. ISM. É sua responsabilidade definir esses parâmetros corretamente para sinalizar uma faixa de áudio como descrição de áudio. Por exemplo, adicione `<param name="accessibility" value="description" />` e `<param name="role" value="alternate" />` ao arquivo. ISM para uma faixa de áudio específica, conforme mostrado no exemplo a seguir.
 
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

## <a name="get-a-streaming-locator"></a>Obter um localizador de streaming

Depois de concluída a codificação, o passo seguinte consiste em disponibilizar o vídeo no Elemento de saída para reprodução para os clientes. Você pode fazer isso em duas etapas: primeiro, criar um [localizador de streaming](https://docs.microsoft.com/rest/api/media/streaminglocators)e, em segundo lugar, criar as URLs de streaming que os clientes podem usar. 

O processo de criação de um **localizador de streaming** é chamado publicação. Por padrão, o **localizador de streaming** é válido imediatamente depois que você faz as chamadas à API e dura até que ela seja excluída, a menos que você configure as horas de início e término opcionais. 

Ao criar um [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators), terá de especificar o **StreamingPolicyName** pretendido. Neste exemplo, vai transmitir conteúdo pronto a enviar (ou não encriptado), por isso é utilizada a política de transmissão de pronto a enviar predefinida (**PredefinedStreamingPolicy.ClearStreamingOnly**).

> [!IMPORTANT]
> Ao usar uma [política de streaming](https://docs.microsoft.com/rest/api/media/streamingpolicies)personalizada, você deve criar um conjunto limitado de tais políticas para sua conta de serviço de mídia e reutilizá-las para o StreamingLocators sempre que forem necessárias as mesmas opções de criptografia e protocolos. Sua conta de serviço de mídia tem uma cota para o número de entradas de política de streaming. Você não deve criar uma nova política de streaming para cada localizador de streaming.

O código seguinte parte do princípio de que está a chamar a função com um locatorName exclusivo.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateStreamingLocator)]

Embora o exemplo neste tópico discuta o streaming, você pode usar a mesma chamada para criar um localizador de streaming para entregar vídeo por meio de download progressivo.

### <a name="get-streaming-urls"></a>Obter os URLs de transmissão

Agora que o [localizador de streaming](https://docs.microsoft.com/rest/api/media/streaminglocators) foi criado, você pode obter as URLs de streaming, conforme mostrado em **GetStreamingURLs**. Para criar uma URL, você precisa concatenar o nome do host do [ponto de extremidade de streaming](https://docs.microsoft.com/rest/api/media/streamingendpoints) e o caminho do **localizador de streaming** . Neste exemplo, o **ponto de extremidade de streaming** *padrão* é usado. Quando você cria uma conta de serviço de mídia pela primeira vez, esse **ponto de extremidade de streaming** *padrão* estará em um estado parado, portanto, você precisará chamar **Start**.

> [!NOTE]
> Nesse método, você precisa do locatorname que foi usado ao criar o **localizador de streaming** para o ativo de saída.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#GetStreamingURLs)]

## <a name="test-with-azure-media-player"></a>Testar com o Leitor de Multimédia do Azure

Para testar a transmissão, este artigo utiliza o Leitor de Multimédia do Azure. 

> [!NOTE]
> Se um leitor estiver alojado num site de https, confirme que atualiza o URL para “https”.

1. Abra um browser e navegue para [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/).
2. Na caixa **URL:** , Cole um dos valores de URL de streaming obtidos de seu aplicativo. 
 
     Você pode colar a URL no formato HLS, Dash ou Smooth e Player de Mídia do Azure mudará para um protocolo de streaming apropriado para reprodução em seu dispositivo automaticamente.
3. Prima **Atualizar Leitor**.

O Leitor de Multimédia do Azure pode ser utilizado para fins de teste, mas não deve ser utilizado num ambiente de produção. 

## <a name="next-steps"></a>Passos seguintes

[Analisar vídeos](analyze-videos-tutorial-with-api.md)
