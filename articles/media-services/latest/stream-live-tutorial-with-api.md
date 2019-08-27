---
title: Transmitir ao vivo com os serviços de mídia do Azure v3 | Microsoft Docs
description: Este tutorial orienta você pelas etapas de streaming Live com os serviços de mídia v3.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 06/13/2019
ms.author: juliako
ms.openlocfilehash: b69bd62cb9bbe44fb37b3f3660c2f20f3965384e
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051572"
---
# <a name="tutorial-stream-live-with-media-services"></a>Tutorial: Transmitir ao vivo com os serviços de mídia

> [!NOTE]
> Embora o tutorial use os exemplos do [SDK do .net](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveevent?view=azure-dotnet) , as etapas gerais são as mesmas para a [API REST](https://docs.microsoft.com/rest/api/media/liveevents), a [CLI](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest)ou outros [SDKs](media-services-apis-overview.md#sdks)com suporte.

Nos serviços de mídia do Azure, os [eventos ao vivo](https://docs.microsoft.com/rest/api/media/liveevents) são responsáveis pelo processamento de conteúdo de transmissão ao vivo. Um evento ao vivo fornece um ponto de extremidade de entrada (URL de ingestão) que você então fornece a um codificador ao vivo. O evento ao vivo recebe fluxos de entrada ao vivo do codificador ao vivo e os disponibiliza para streaming por meio de um ou mais [pontos de extremidade de streaming](https://docs.microsoft.com/rest/api/media/streamingendpoints). Eventos ao vivo também fornecem um ponto de extremidade de visualização (URL de visualização) que você usa para visualizar e validar seu fluxo antes de processar e entregar. Este tutorial mostra como utilizar um canal .NET Core para criar um evento em direto do tipo **pass-through**. 

Este tutorial mostra-lhe como:    

> [!div class="checklist"]
> * Baixe o aplicativo de exemplo descrito no tópico
> * Examinar o código que efetua a transmissão em fluxo em direto
> * Observar o evento com o [Leitor de Multimédia do Azure](https://amp.azure.net/libs/amp/latest/docs/index.html) em https://ampdemo.azureedge.net
> * Limpar recursos

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

O seguinte é necessário para concluir o tutorial.

- Instale o Visual Studio Code ou o Visual Studio.
- [Criar uma conta de Media Services](create-account-cli-how-to.md).<br/>Lembre-se de lembrar os valores que você usou para o nome do grupo de recursos e o nome da conta dos serviços de mídia.
- Siga as etapas em [acessar a API dos serviços de mídia do Azure com o CLI do Azure](access-api-cli-how-to.md) e salve as credenciais. Você precisará usá-las para acessar a API.
- Uma câmara ou um dispositivo (como um computador portátil) que é utilizado para transmitir um evento.
- Um codificador em direto no local que converte sinais da câmara para transmissões em fluxos que são enviadas para o serviço de transmissão em fluxo em direto dos Serviços de Multimédia. O fluxo tem de ser em formato **RTMP** ou de **transmissão em fluxo uniforme**.

> [!TIP]
> Certifique-se de que revê [Transmissão em fluxo em direto com os Serviços de Multimédia v3](live-streaming-overview.md) antes de continuar. 

## <a name="download-and-configure-the-sample"></a>Baixar e configurar o exemplo

Clone o repositório do GitHub que contém o exemplo de .NET de transmissão para o computador com o comando seguinte:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials.git
 ```

O exemplo de transmissão em fluxo em direto está localizado na pasta [Dinâmica](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/Live/MediaV3LiveApp).

Abra [appSettings. JSON](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/appsettings.json) no projeto baixado. Substitua os valores por credenciais obtidas de [acessar APIs](access-api-cli-how-to.md).

> [!IMPORTANT]
> Este exemplo utiliza o sufixo exclusivo para cada recurso. Se você cancelar a depuração ou encerrar o aplicativo sem executá-lo, você acabará com vários eventos ao vivo em sua conta. <br/>Certifique-se de interromper os eventos ao vivo em execução. Se não o fizer, estes ser-lhe-ão **faturados**!

## <a name="examine-the-code-that-performs-live-streaming"></a>Examinar o código que efetua a transmissão em fluxo em direto

Esta secção examina funções definidas no ficheiro [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs) do projeto *MediaV3LiveApp*.

O exemplo cria um sufixo exclusivo para cada recurso para não ocorrerem colisões de nomes, se executar o exemplo várias vezes sem proceder a uma limpeza.

> [!IMPORTANT]
> Este exemplo utiliza o sufixo exclusivo para cada recurso. Se você cancelar a depuração ou encerrar o aplicativo sem executá-lo, você acabará com vários eventos ao vivo em sua conta. <br/>
> Certifique-se de interromper os eventos ao vivo em execução. Se não o fizer, estes ser-lhe-ão **faturados**!
 
### <a name="start-using-media-services-apis-with-net-sdk"></a>Começar a utilizar as APIs dos Serviços de Multimédia com o SDK .NET

Para começar a utilizar as APIs dos Serviços de Multimédia com o .NET, tem de criar um objeto **AzureMediaServicesClient**. Para criar o objeto, tem de fornecer as credenciais necessárias para o cliente se ligar ao Azure com o Azure AD. No código que clonou no início do artigo, a função **GetCredentialsAsync** cria o objeto ServiceClientCredentials com base nas credenciais fornecidas no ficheiro de configuração local. 

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateMediaServicesClient)]

### <a name="create-a-live-event"></a>Criar evento live

Esta seção mostra como criar um tipo de **passagem** de evento ao vivo (LiveEventEncodingType definido como None). Para obter mais informações sobre os tipos disponíveis de eventos ao vivo, consulte [tipos de eventos ao vivo](live-events-outputs-concept.md#live-event-types). 
 
Algumas coisas que você pode querer especificar ao criar o evento ao vivo são:

* A localização dos Serviços de Multimédia 
* O protocolo de streaming para o evento ao vivo (atualmente, há suporte para os protocolos RTMP e Smooth Streaming).<br/>Você não pode alterar a opção de protocolo enquanto o evento ao vivo ou suas saídas dinâmicas associadas estiverem em execução. Se você precisar de protocolos diferentes, deverá criar um evento ao vivo separado para cada protocolo de streaming.  
* Restrições de IP na ingestão e na pré-visualização. Você pode definir os endereços IP que têm permissão para ingerir um vídeo para esse evento ao vivo. Os endereços IP permitidos podem ser especificados como um endereço IP único (por exemplo "10.0.0.1"), um intervalo de IP com um endereço IP e uma máscara de sub-rede CIDR (por exemplo, ' 10.0.0.1/22') ou um intervalo de IP com um endereço IP e uma máscara de sub-rede de ponto decimal (por exemplo , ' 10.0.0.1(255.255.252.0)').<br/>Se não for especificado qualquer endereço IP e se não existir nenhuma definição de regra, não será permitido nenhum endereço IP. Para permitir um endereço IP, crie uma regra e defina 0.0.0.0/0.<br/>Os endereços IP devem estar em um dos seguintes formatos: Endereço IpV4 com 4 números, intervalo de endereços CIDR.
* Ao criar o evento, poderá especificar o início automático do mesmo. <br/>Quando AutoStart é definido como true, o evento ao vivo será iniciado após a criação. Isso significa que a cobrança começa assim que o evento ao vivo começa a ser executado. Você deve chamar Stop explicitamente no recurso de evento ao vivo para interromper mais cobranças. Para obter mais informações, consulte [Live Event States e Billing](live-event-states-billing.md).
* Para que uma URL de ingestão seja preditiva, defina o modo "intuitivo". Para obter informações detalhadas, consulte URLs de ingestão de [eventos ao vivo](live-events-outputs-concept.md#live-event-ingest-urls).

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateLiveEvent)]

### <a name="get-ingest-urls"></a>Obter URLs de inserção

Depois que o evento ao vivo for criado, você poderá obter URLs de ingestão que serão fornecidas ao codificador ao vivo. O codificador utiliza estes URLs para exibir uma transmissão um fluxo direto.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#GetIngestURL)]

### <a name="get-the-preview-url"></a>Obter o URL de pré-visualização

Utilize o previewEndpoint para pré-visualizar e certifique-se de que a entrada do codificador está a ser recebida.

> [!IMPORTANT]
> Certifique-se de que o vídeo está a fluir para o URL de pré-visualização antes de continuar!

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#GetPreviewURLs)]

### <a name="create-and-manage-live-events-and-live-outputs"></a>Criar e gerenciar eventos ao vivo e saídas ao vivo

Depois que o fluxo fluir para o evento ao vivo, você poderá iniciar o evento de streaming criando um ativo, uma saída ao vivo e um localizador de streaming. Isto irá arquivar a transmissão em fluxo e torná-la disponível para os espetadores através do Ponto Final de Transmissão em Fluxo. 

#### <a name="create-an-asset"></a>Criar um Elemento

Crie um ativo para a saída em tempo real a ser usado.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateAsset)]

#### <a name="create-a-live-output"></a>Criar uma saída ao vivo

As saídas ao vivo começam na criação e param quando excluídas. Ao excluir a saída ao vivo, você não está excluindo o ativo subjacente e o conteúdo no ativo.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateLiveOutput)]

#### <a name="create-a-streaming-locator"></a>Criar um localizador de streaming

> [!NOTE]
> Quando a conta dos Serviços de Multimédia é criada, é adicionado um ponto final de transmissão em fluxo **predefinido** à mesma, no estado **Parado**. Para começar a transmitir seu conteúdo e aproveitar o [empacotamento dinâmico](dynamic-packaging-overview.md) e a criptografia dinâmica, o ponto de extremidade de streaming do qual você deseja transmitir o conteúdo deve estar no estado **executando** . 

Quando você publicar o ativo de saída ao vivo usando um localizador de streaming, o evento ao vivo (até o comprimento da janela DVR) continuará a ser exibido até a expiração ou a exclusão do localizador de streaming, o que ocorrer primeiro.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateStreamingLocator)]

```csharp

// Get the url to stream the output
ListPathsResponse paths = await client.StreamingLocators.ListPathsAsync(resourceGroupName, accountName, locatorName);

foreach (StreamingPath path in paths.StreamingPaths)
{
    UriBuilder uriBuilder = new UriBuilder();
    uriBuilder.Scheme = "https";
    uriBuilder.Host = streamingEndpoint.HostName;

    uriBuilder.Path = path.Paths[0];
    // Get the URL from the uriBuilder: uriBuilder.ToString()
}
```

### <a name="cleaning-up-resources-in-your-media-services-account"></a>Limpar os recursos na sua conta de Serviços de Multimédia

Se terminar a transmissão em fluxo de eventos e pretende limpar os recursos aprovisionados anteriormente, siga o procedimento seguinte.

* Termine o envio da transmissão em fluxo do codificador.
* Pare o evento ao vivo. Depois que o evento ao vivo for interrompido, ele não incorrerá em nenhum encargo. Quando quiser reiniciar a transmissão, esta terá o mesmo URL de inserção, desta forma, não terá de reconfigurar o codificador.
* Pode parar o seu Ponto Final de Transmissão em Fluxo, a menos que pretenda continuar a fornecer o arquivo do seu evento em direto como uma transmissão em fluxo a pedido. Se o evento ao vivo estiver no estado parado, ele não incorrerá em nenhum encargo.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CleanupLiveEventAndOutput)]

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CleanupLocatorAssetAndStreamingEndpoint)]

## <a name="watch-the-event"></a>Ver o evento

Para assistir ao evento, copie a URL de streaming que você obteve quando executou o código descrito em criar um localizador de streaming e use um jogador de sua escolha. Pode utilizar o [Leitor de Multimédia do Azure](https://amp.azure.net/libs/amp/latest/docs/index.html) para testar a sua transmissão em fluxo em https://ampdemo.azureedge.net. 

O evento ao vivo converte automaticamente os eventos em conteúdo sob demanda quando interrompido. Mesmo depois de parar e eliminar o evento, os utilizadores conseguirão transmitir o seu conteúdo arquivado como um vídeo a pedido, desde que não elimine o elemento. Não é possível eliminar um elemento se este é utilizado por um evento; o evento deve ser eliminado primeiro. 

## <a name="clean-up-resources"></a>Limpar recursos

Se já não precisar de nenhum dos recursos presentes no seu grupo de recursos, incluindo a conta dos Serviços de Multimédia e a conta de armazenamento que criou para este tutorial, elimine o grupo de recursos anteriormente criado.

Execute o seguinte comando da CLI:

```azurecli-interactive
az group delete --name amsResourceGroup
```

> [!IMPORTANT]
> Deixar a execução do evento ao vivo gera custos de cobrança. Lembre-se de que, se o projeto/programa falhar ou for fechado por qualquer motivo, ele poderá deixar o evento ao vivo em execução em um estado de cobrança.

## <a name="ask-questions-give-feedback-get-updates"></a>Faça perguntas, envie comentários, obtenha atualizações

Confira o artigo [da Comunidade dos serviços de mídia do Azure](media-services-community.md) para ver diferentes maneiras que você pode fazer perguntas, fornecer comentários e obter atualizações sobre os serviços de mídia.

## <a name="next-steps"></a>Passos Seguintes

[Transmitir ficheiros em fluxo](stream-files-tutorial-with-api.md)
 
