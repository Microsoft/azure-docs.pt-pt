---
title: Stream live with Media Services v3
titleSuffix: Azure Media Services
description: Saiba como transmitir em direto com a Azure Media Services v3.
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
ms.openlocfilehash: 0b6667965ddd1fce30bb2da2593e2a9274b595ed
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "79472021"
---
# <a name="tutorial-stream-live-with-media-services"></a>Tutorial: Stream live with Media Services

> [!NOTE]
> Mesmo que o tutorial utilize exemplos [.NET SDK,](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveevent?view=azure-dotnet) os passos gerais são os mesmos para [REST API,](https://docs.microsoft.com/rest/api/media/liveevents) [CLI,](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest)ou outros [SDKs](media-services-apis-overview.md#sdks)suportados .

No Azure Media Services, os [Eventos Ao Vivo](https://docs.microsoft.com/rest/api/media/liveevents) são responsáveis pelo processamento de conteúdos de streaming ao vivo. Um Evento Ao Vivo fornece um ponto final de entrada (URL ingerir) que fornece a um codificador ao vivo. O Live Event recebe streams de entrada ao vivo do codificador ao vivo e disponibiliza-o para streaming através de um ou mais [Streaming Endpoints](https://docs.microsoft.com/rest/api/media/streamingendpoints). Live Events também fornece um ponto final de pré-visualização (URL de pré-visualização) que utiliza para pré-visualizar e validar o seu fluxo antes de continuar a processar e entregar. Este tutorial mostra como utilizar um canal .NET Core para criar um evento em direto do tipo **pass-through**.

Este tutorial mostra-lhe como:

> [!div class="checklist"]
> * Descarregue a aplicação de amostra supor no tópico.
> * Examine o código que executa o streaming ao vivo.
> * Assista ao evento com [o Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) em [https://ampdemo.azureedge.net](https://ampdemo.azureedge.net).
> * Limpe os recursos.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

São necessários os itens seguintes para concluir o tutorial:

- Instale o Código do Estúdio Visual ou o Estúdio Visual.
- [Criar uma conta de Media Services.](create-account-cli-how-to.md)<br/>Lembre-se dos valores que utiliza para o nome do grupo de recursos e nome da conta Media Services.
- Siga os passos na API access [Azure Media Services com o Azure CLI](access-api-cli-how-to.md) e guarde as credenciais. Terá de usá-los para aceder à API.
- Uma câmara ou um dispositivo (como um portátil) que é usado para transmitir um evento.
- Um codificador ao vivo no local que converte sinais da câmara para streams que são enviados para o serviço de streaming ao vivo dos Media Services, consulte [codificadores ao vivo recomendados no local.](recommended-on-premises-live-encoders.md) O fluxo tem de ser em formato **RTMP** ou de **transmissão em fluxo uniforme**.

> [!TIP]
> Certifique-se de que revê [Transmissão em fluxo em direto com os Serviços de Multimédia v3](live-streaming-overview.md) antes de continuar. 

## <a name="download-and-configure-the-sample"></a>Descarregue e configure a amostra

Clone o repositório do GitHub que contém o exemplo de .NET de transmissão para o computador com o comando seguinte:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials.git
 ```

O exemplo de transmissão em fluxo em direto está localizado na pasta [Dinâmica](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/Live/MediaV3LiveApp).

Abra [as aplicações.json](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/appsettings.json) no seu projeto descarregado. Substitua os valores pelas credenciais que obteve ao [aceder a APIs](access-api-cli-how-to.md).

> [!IMPORTANT]
> Esta amostra utiliza um sufixo único para cada recurso. Se cancelar a depuração ou encerrar a aplicação sem a executar, acabará com vários Eventos Ao Vivo na sua conta. <br/>Certifique-se de parar os eventos ao vivo. Caso contrário, será **cobrado!**

## <a name="examine-the-code-that-performs-live-streaming"></a>Examinar o código que efetua a transmissão em fluxo em direto

Esta secção examina funções definidas no ficheiro [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs) do projeto *MediaV3LiveApp*.

A amostra cria um sufixo único para cada recurso para que não tenha colisões de nome se executar a amostra várias vezes sem limpar.

> [!IMPORTANT]
> Esta amostra utiliza um sufixo único para cada recurso. Se cancelar a depuração ou encerrar a aplicação sem a executar, acabará com vários Eventos Ao Vivo na sua conta. <br/>
> Certifique-se de parar os eventos ao vivo. Caso contrário, será **cobrado!**

### <a name="start-using-media-services-apis-with-net-sdk"></a>Começar a utilizar APIs dos Serviços de Multimédia com SDK do .NET

Para começar a utilizar as APIs dos Serviços de Multimédia com o .NET, tem de criar um objeto **AzureMediaServicesClient**. Para criar o objeto, tem de fornecer as credenciais necessárias para o cliente se ligar ao Azure com o Azure AD. No código que clonou no início do artigo, a função **GetCredentialsAsync** cria o objeto ServiceClientCredentials com base nas credenciais fornecidas no ficheiro de configuração local. 

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateMediaServicesClient)]

### <a name="create-a-live-event"></a>Criar um evento em direto

Esta secção mostra como criar um tipo **de evento** ao vivo (LiveEventEncodingType definido para Nenhum). Para obter mais informações sobre os tipos disponíveis de Eventos Ao Vivo, consulte os tipos de [Eventos Ao Vivo](live-events-outputs-concept.md#live-event-types). 
 
Algumas coisas que talvez queira especificar ao criar o evento ao vivo são:

* Localização dos Serviços de Media.
* O protocolo de streaming para o Live Event (atualmente, os protocolos RTMP e Smooth Streaming são suportados).<br/>Não pode alterar a opção de protocolo enquanto o Live Event ou as suas saídas ao vivo associadas estiverem em execução. Se necessitar de protocolos diferentes, crie evento seleto ao vivo para cada protocolo de streaming.  
* Restrições de IP na ingestão e na pré-visualização. Pode definir os endereços IP que são autorizados a ingerir um vídeo para este Evento Ao Vivo. Os endereços IP permitidos podem ser especificados como um endereço IP único (por exemplo "10.0.0.1"), um intervalo de IP com um endereço IP e uma máscara de sub-rede CIDR (por exemplo, ' 10.0.0.1/22') ou um intervalo de IP com um endereço IP e uma máscara de sub-rede de ponto decimal (por exemplo , ' 10.0.0.1(255.255.252.0)').<br/>Se não forem especificados endereços IP e não houver definição de regra, então nenhum endereço IP será permitido. Para permitir um endereço IP, crie uma regra e defina 0.0.0.0/0.<br/>Os endereços IP têm de estar num dos seguintes formatos: endereço IpV4 com quatro números ou intervalo de endereços CIDR.
* Ao criar o evento, pode especificar para iniciar automaticamente. <br/>Quando o autoarranque estiver pronto para ser verdade, o Live Event será iniciado após a criação. Isso significa que a faturação começa assim que o Evento Ao Vivo começar a funcionar. Deve ligar explicitamente para parar o recurso Stop on the Live Event para parar a faturação adicional. Para mais informações, consulte os estados do [Live Event e a faturação.](live-event-states-billing.md)
* Para que um URL ingerir seja preditivo, delineie o modo "vaidade". Para obter informações detalhadas, consulte [O Evento Ao Vivo ingerir URLs](live-events-outputs-concept.md#live-event-ingest-urls).

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateLiveEvent)]

### <a name="get-ingest-urls"></a>Obter URLs de inserção

Assim que o Evento Ao Vivo for criado, pode obter URLs ingerir que irá fornecer ao codificador ao vivo. O codificador utiliza estes URLs para exibir uma transmissão um fluxo direto.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#GetIngestURL)]

### <a name="get-the-preview-url"></a>Obter o URL de pré-visualização

Utilize o previewEndpoint para pré-visualizar e certifique-se de que a entrada do codificador está a ser recebida.

> [!IMPORTANT]
> Certifique-se de que o vídeo está a fluir para o URL de pré-visualização antes de continuar.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#GetPreviewURLs)]

### <a name="create-and-manage-live-events-and-live-outputs"></a>Criar e gerir eventos ao vivo e saídas ao vivo

Assim que tiver o fluxo fluindo para o Live Event, pode iniciar o evento de streaming criando um Localizador de Ativo, Live Output e Streaming. Isto irá arquivar a transmissão em fluxo e torná-la disponível para os espetadores através do Ponto Final de Transmissão em Fluxo.

#### <a name="create-an-asset"></a>Criar um Elemento

Criar um Ativo para a Saída Ao Vivo utilizar.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateAsset)]

#### <a name="create-a-live-output"></a>Criar uma saída ao vivo

As Saídas ao Vivo começam na criação e param quando eliminadas. Ao eliminar a Saída Ao Vivo, não está a eliminar o Ativo subjacente e o conteúdo no ativo.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateLiveOutput)]

#### <a name="create-a-streaming-locator"></a>Criar um localizador de streaming

> [!NOTE]
> Quando a sua conta De serviços de media é criada, um ponto final de streaming **padrão** é adicionado à sua conta no estado **Deter.** Para começar a transmitir o seu conteúdo e tirar partido de [embalagens dinâmicas](dynamic-packaging-overview.md) e encriptação dinâmica, o ponto final de streaming a partir do qual pretende transmitir conteúdo tem de estar no estado **De Execução.**

Quando publicar o ativo Live Output utilizando um Localizador de Streaming, o Evento Ao Vivo (até ao comprimento da janela DVR) continuará a ser visível até à expiração ou eliminação do Localizador de Streaming, o que vier primeiro.

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

Se já terminou os eventos de streaming e pretende limpar os recursos aprovisionados anteriormente, siga o seguinte procedimento:

* Termine o envio da transmissão em fluxo do codificador.
* Pare o Evento Ao Vivo. Uma vez que o Evento Ao Vivo seja interrompido, não incorrerá em nenhuma acusação. Quando quiser reiniciar a transmissão, esta terá o mesmo URL de inserção, desta forma, não terá de reconfigurar o codificador.
* Pode parar o seu Ponto Final de Transmissão em Fluxo, a menos que pretenda continuar a fornecer o arquivo do seu evento em direto como uma transmissão em fluxo a pedido. Se o Evento Ao Vivo estiver em estado de paragem, não incorrerá em nenhuma acusação.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CleanupLiveEventAndOutput)]

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CleanupLocatorAssetAndStreamingEndpoint)]

## <a name="watch-the-event"></a>Ver o evento

Para assistir ao evento, copie o URL de streaming que obteve quando executou o código descrito no Create a Streaming Locator. Podes usar um media player à tua escolha. [O Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) está https://ampdemo.azureedge.netdisponível para testar o seu fluxo em .

Live Event converte automaticamente eventos em conteúdo sonoro quando parado. Mesmo depois de parar e apagar o evento, os utilizadores podem transmitir o seu conteúdo arquivado como um vídeo a pedido enquanto não apagar o ativo. Um ativo não pode ser apagado se for usado por um evento; o evento deve ser eliminado primeiro.

## <a name="clean-up-resources"></a>Limpar recursos

Se já não precisa de nenhum dos recursos presentes no seu grupo de recursos, incluindo as contas de armazenamento que criou e os Serviços de Multimédia que carregou neste tutorial, elimine o grupo de recursos que criou anteriormente.

Executar o seguinte comando CLI:

```azurecli-interactive
az group delete --name amsResourceGroup
```

> [!IMPORTANT]
> Deixar o Live Event a funcionar incorre nos custos de faturação. Esteja ciente de que se o projeto/programa falhar ou for encerrado por qualquer motivo, poderá deixar o Live Event a funcionar em estado de faturação.

## <a name="ask-questions-give-feedback-get-updates"></a>Faça perguntas, dê feedback, obtenha atualizações

Confira o artigo da [comunidade Azure Media Services](media-services-community.md) para ver diferentes formas de fazer perguntas, dar feedback e obter atualizações sobre os Serviços de Media.

## <a name="next-steps"></a>Passos seguintes

[Transmitir ficheiros em fluxo](stream-files-tutorial-with-api.md)
 
