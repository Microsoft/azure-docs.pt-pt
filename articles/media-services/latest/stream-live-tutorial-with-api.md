---
title: Stream em direto com Media Services do Azure v3 através do .NET | Documentos da Microsoft
description: Este tutorial explica os passos da transmissão em fluxo em direto com os Serviços de Multimédia v3 com .NET Core.
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
ms.date: 03/01/2019
ms.author: juliako
ms.openlocfilehash: 3500463cc57fb236de2a591181a5dc7054780404
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57240699"
---
# <a name="tutorial-stream-live-with-media-services-v3-using-net"></a>Tutorial: Stream em direto com serviços de multimédia v3 através do .NET

Nos serviços de multimédia do Azure, [eventos ao vivo](https://docs.microsoft.com/rest/api/media/liveevents) são responsáveis por processar o conteúdo de transmissão em fluxo em direto. Um evento em direto fornece um ponto de final de entrada (URL de ingestão) que, em seguida, forneça a um codificador em direto. O evento Live recebe a transmissão em direto de entrada do codificador em direto e disponibiliza-o para transmissão em fluxo através de um ou mais [pontos finais de transmissão em fluxo](https://docs.microsoft.com/rest/api/media/streamingendpoints). Eventos em direto também fornecem um ponto de extremidade pré-visualização (URL de pré-visualização) que utilizar para pré-visualizar e validar a sua transmissão antes de mais processamentos e entregas. Este tutorial mostra como utilizar um canal .NET Core para criar um evento em direto do tipo **pass-through**. 

> [!NOTE]
> Certifique-se de que revê [Transmissão em fluxo em direto com os Serviços de Multimédia v3](live-streaming-overview.md) antes de continuar. 

Este tutorial mostra-lhe como:    

> [!div class="checklist"]
> * Transferir a aplicação de exemplo descrita no tópico
> * Examinar o código que efetua a transmissão em fluxo em direto
> * Observar o evento com o [Leitor de Multimédia do Azure](http://amp.azure.net/libs/amp/latest/docs/index.html) em http://ampdemo.azureedge.net
> * Limpar recursos

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

O seguinte é necessário para concluir o tutorial.

- Instale o Visual Studio Code ou o Visual Studio.
- [Criar uma conta de Media Services](create-account-cli-how-to.md).<br/>Lembre-se de que não se esqueça dos valores que utilizou para o nome do grupo de recursos e o nome de conta de serviços de multimédia.
- Siga os passos em [acesso à API de serviços de multimédia do Azure com a CLI do Azure](access-api-cli-how-to.md) e guarde as credenciais. Terá de utilizá-los para aceder à API.
- Uma câmara ou um dispositivo (como um computador portátil) que é utilizado para transmitir um evento.
- Um codificador em direto no local que converte sinais da câmara para transmissões em fluxos que são enviadas para o serviço de transmissão em fluxo em direto dos Serviços de Multimédia. O fluxo tem de ser em formato **RTMP** ou de **transmissão em fluxo uniforme**.

## <a name="download-and-configure-the-sample"></a>Transferir e configurar o exemplo

Clone o repositório do GitHub que contém o exemplo de .NET de transmissão para o computador com o comando seguinte:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials.git
 ```

O exemplo de transmissão em fluxo em direto está localizado na pasta [Dinâmica](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/Live/MediaV3LiveApp).

Open [appSettings](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/appsettings.json) no que transferiu o projeto. Substitua os valores com as credenciais que obteve da [aceder a APIs](access-api-cli-how-to.md).

> [!IMPORTANT]
> Este exemplo utiliza o sufixo exclusivo para cada recurso. Se cancelar a depuração ou encerrar o aplicativo sem a executá-lo por meio, acabará com vários eventos em direto na sua conta. <br/>Certifique-se parar os eventos em direto em execução. Se não o fizer, estes ser-lhe-ão **faturados**!

## <a name="examine-the-code-that-performs-live-streaming"></a>Examinar o código que efetua a transmissão em fluxo em direto

Esta secção examina funções definidas no ficheiro [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs) do projeto *MediaV3LiveApp*.

O exemplo cria um sufixo exclusivo para cada recurso para não ocorrerem colisões de nomes, se executar o exemplo várias vezes sem proceder a uma limpeza.

> [!IMPORTANT]
> Este exemplo utiliza o sufixo exclusivo para cada recurso. Se cancelar a depuração ou encerrar o aplicativo sem a executá-lo por meio, acabará com vários eventos em direto na sua conta. <br/>
> Certifique-se parar os eventos em direto em execução. Se não o fizer, estes ser-lhe-ão **faturados**!
 
### <a name="start-using-media-services-apis-with-net-sdk"></a>Começar a utilizar as APIs dos Serviços de Multimédia com o SDK .NET

Para começar a utilizar as APIs dos Serviços de Multimédia com o .NET, tem de criar um objeto **AzureMediaServicesClient**. Para criar o objeto, tem de fornecer as credenciais necessárias para o cliente se ligar ao Azure com o Azure AD. No código que clonou no início do artigo, a função **GetCredentialsAsync** cria o objeto ServiceClientCredentials com base nas credenciais fornecidas no ficheiro de configuração local. 

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateMediaServicesClient)]

### <a name="create-a-live-event"></a>Criar um evento em direto

Esta secção mostra como criar uma **pass-through** tipo de evento em direto (LiveEventEncodingType definida como None). Para obter mais informações sobre os tipos de eventos em direto disponíveis, consulte [tipos de evento em direto](live-events-outputs-concept.md#live-event-types). 
 
Algumas coisas que talvez queira especificar ao criar o evento em direto são:

* A localização dos Serviços de Multimédia 
* O protocolo de transmissão em fluxo para o evento em direto (atualmente, são suportados os protocolos RTMP e Smooth Streaming).<br/>Não é possível alterar a opção de protocolo enquanto o evento em direto ou suas saídas associadas em direto estão em execução. Se necessitar de protocolos diferentes, deve criar evento Live separado para cada protocolo de transmissão em fluxo.  
* Restrições de IP na ingestão e na pré-visualização. Pode definir os endereços IP que estão autorizados a ingerir um vídeo para este evento em direto. Os endereços IP permitidos podem ser especificados como um endereço IP único (por exemplo "10.0.0.1"), um intervalo de IP com um endereço IP e uma máscara de sub-rede CIDR (por exemplo, ' 10.0.0.1/22') ou um intervalo de IP com um endereço IP e uma máscara de sub-rede de ponto decimal (por exemplo , ' 10.0.0.1(255.255.252.0)').<br/>Se não for especificado qualquer endereço IP e se não existir nenhuma definição de regra, não será permitido nenhum endereço IP. Para permitir um endereço IP, crie uma regra e defina 0.0.0.0/0.<br/>Os endereços IP tem de estar em um dos seguintes formatos: Endereço IpV4 com 4 números, o intervalo de endereços CIDR.
* Ao criar o evento, poderá especificar o início automático do mesmo. <br/>Quando o início automático está definido como true, o evento em direto será iniciado após a criação. Isso significa que, o faturação começa logo que o startsrunning de evento em direto. Tem de chamar explicitamente Stop do recurso de evento em direto para parar a faturação ainda mais. Para obter mais informações, consulte [Estados de evento em direto e de faturação](live-event-states-billing.md).

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateLiveEvent)]

### <a name="get-ingest-urls"></a>Obter URLs de inserção

Depois de criar o evento em direto, pode obter URLs de inserção que fornecerá ao codificador em direto. O codificador utiliza estes URLs para exibir uma transmissão um fluxo direto.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#GetIngestURL)]

### <a name="get-the-preview-url"></a>Obter o URL de pré-visualização

Utilize o previewEndpoint para pré-visualizar e certifique-se de que a entrada do codificador está a ser recebida.

> [!IMPORTANT]
> Certifique-se de que o vídeo está a fluir para o URL de pré-visualização antes de continuar!

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#GetPreviewURLs)]

### <a name="create-and-manage-live-events-and-live-outputs"></a>Criar e gerir eventos ao vivo e saídas em direto

Assim que tiver o fluxo a ser encaminhados para o evento em direto, pode começar o evento de transmissão em fluxo através da criação de um recurso, a saída de Live e o localizador de transmissão em fluxo. Isto irá arquivar a transmissão em fluxo e torná-la disponível para os espetadores através do Ponto Final de Transmissão em Fluxo. 

#### <a name="create-an-asset"></a>Criar um Elemento

Crie um elemento para a saída em direto utilizar.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateAsset)]

#### <a name="create-a-live-output"></a>Criar uma saída em direto

Início de saídas após a criação do Live e param quando eliminado. Ao eliminar a saída em direto, não serão eliminados os ativos e conteúdo no elemento subjacente.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateLiveOutput)]

#### <a name="create-a-streaming-locator"></a>Criar um localizador de transmissão em fluxo

> [!NOTE]
> Quando a conta dos Serviços de Multimédia é criada, é adicionado um ponto final de transmissão em fluxo **predefinido** à mesma, no estado **Parado**. Para iniciar o seu conteúdo de transmissão em fluxo e tirar partido das [empacotamento dinâmico](dynamic-packaging-overview.md) e a encriptação dinâmica, o ponto final de transmissão em fluxo do qual pretende transmitir conteúdo tem de estar no **em execução** estado. 

Quando publica o elemento de saída em direto com um localizador de transmissão em fluxo, o evento em direto (até o tamanho de janela DVR) irá continuar a ser visualizado até expiração o localizador de transmissão em fluxo ou eliminação, o que ocorrer primeiro.

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
* Pare o evento em direto. Assim que o evento Live é parado, não será cobrado qualquer custo. Quando quiser reiniciar a transmissão, esta terá o mesmo URL de inserção, desta forma, não terá de reconfigurar o codificador.
* Pode parar o seu Ponto Final de Transmissão em Fluxo, a menos que pretenda continuar a fornecer o arquivo do seu evento em direto como uma transmissão em fluxo a pedido. Se o evento em direto está no estado parado, não será cobrado qualquer custo.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CleanupLiveEventAndOutput)]

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CleanupLocatorAssetAndStreamingEndpoint)]

O código seguinte mostra como limpar a sua conta de todos os eventos em direto:

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CleanupAccount)]   

## <a name="watch-the-event"></a>Ver o evento

Para ver o evento, copie o URL de transmissão em fluxo que obteve quando executou o código descrito em criar um localizador de transmissão em fluxo e utilize um leitor da sua preferência. Pode utilizar o [Leitor de Multimédia do Azure](http://amp.azure.net/libs/amp/latest/docs/index.html) para testar a sua transmissão em fluxo em http://ampdemo.azureedge.net. 

Evento em direto converte automaticamente os eventos para conteúdo a pedido quando parado. Mesmo depois de parar e eliminar o evento, os utilizadores conseguirão transmitir o seu conteúdo arquivado como um vídeo a pedido, desde que não elimine o elemento. Não é possível eliminar um elemento se este é utilizado por um evento; o evento deve ser eliminado primeiro. 

## <a name="clean-up-resources"></a>Limpar recursos

Se já não precisar de nenhum dos recursos presentes no seu grupo de recursos, incluindo a conta dos Serviços de Multimédia e a conta de armazenamento que criou para este tutorial, elimine o grupo de recursos anteriormente criado.

Execute o seguinte comando da CLI:

```azurecli-interactive
az group delete --name amsResourceGroup
```

> [!IMPORTANT]
> Deixar o evento em direto em execução, incorre em custos de faturas. Tenha em atenção, se o programa/projeto falhar ou for fechado por qualquer motivo, que pode deixar o evento em direto em execução num Estado de faturação.

## <a name="next-steps"></a>Passos Seguintes

[Transmitir ficheiros em fluxo](stream-files-tutorial-with-api.md)
 
