---
título: Stream live with Media Services v3 : Azure Media Services descrição: Saiba como transmitir ao vivo com a Azure Media Services v3.
serviços: media-services documentationcenter: '' autor: IngridAtMicrosoft manager: femila editor: ''

ms.service: media-services ms.workload: media ms.tgt_pltfrm: na ms.devlang: na ms.topic: tutorial ms.custom: "mvc, devx-track-csharp" ms.date: 06/13/2019 ms.author: inhenkel

---

# <a name="tutorial-stream-live-with-media-services"></a>Tutorial: Stream ao vivo com serviços de mídia

> [!NOTE]
> Mesmo que o tutorial utilize exemplos [.NET SDK,](/dotnet/api/microsoft.azure.management.media.models.liveevent?view=azure-dotnet) os passos gerais são os mesmos para [REST API,](/rest/api/media/liveevents) [CLI,](/cli/azure/ams/live-event?view=azure-cli-latest)ou [outros SDKs suportados.](media-services-apis-overview.md#sdks)

Na Azure Media Services, [os Live Events](/rest/api/media/liveevents) são responsáveis pelo processamento de conteúdos de streaming ao vivo. Um Evento Ao Vivo fornece um ponto final de entrada (ingest URL) que você fornece a um codificadores ao vivo. O Live Event recebe streams de entrada ao vivo a partir do codificadores ao vivo e disponibiliza-o para streaming através de um ou mais [Pontos De Streaming](/rest/api/media/streamingendpoints). Os Live Events também fornecem um ponto final de pré-visualização (URL de pré-visualização) que utiliza para visualizar e validar o seu fluxo antes de ser processado e entregue. Este tutorial mostra como utilizar um canal .NET Core para criar um evento em direto do tipo **pass-through**.

Este tutorial mostra-lhe como:

> [!div class="checklist"]
> * Descarregue a aplicação de amostra descrita no tópico.
> * Examine o código que executa o streaming ao vivo.
> * Assista ao evento com [o Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) em [https://ampdemo.azureedge.net](https://ampdemo.azureedge.net) .
> * Limpe os recursos.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

São necessários os itens seguintes para concluir o tutorial:

- Instale o Visual Studio Code ou o Visual Studio.
- [Criar uma conta de Serviços de Comunicação](./create-account-howto.md)Social.<br/>Lembre-se dos valores que utiliza para o nome do grupo de recursos e nome da conta media.
- Siga os passos na [Access Azure Media Services API com o Azure CLI](./access-api-howto.md) e guarde as credenciais. Terá de usá-los para aceder à API.
- Uma câmara ou um dispositivo (como um portátil) que é usado para transmitir um evento.
- Um codificadora ao vivo no local que converte sinais da câmara para os streams que são enviados para o serviço de streaming ao vivo dos Media Services, ver [codificadores ao vivo recomendados no local.](recommended-on-premises-live-encoders.md) O fluxo tem de ser em formato **RTMP** ou de **transmissão em fluxo uniforme**.

> [!TIP]
> Certifique-se de que revê [Transmissão em fluxo em direto com os Serviços de Multimédia v3](live-streaming-overview.md) antes de continuar. 

## <a name="download-and-configure-the-sample"></a>Descarregue e configuure a amostra

Clone o repositório do GitHub que contém o exemplo de .NET de transmissão para o computador com o comando seguinte:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials.git
 ```

O exemplo de transmissão em fluxo em direto está localizado na pasta [Dinâmica](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/Live/MediaV3LiveApp).

Abra [appsettings.jsno](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/appsettings.json) seu projeto descarregado. Substitua os valores pelas credenciais que obteve de [aceder às APIs.](./access-api-howto.md)

> [!IMPORTANT]
> Esta amostra utiliza um sufixo único para cada recurso. Se cancelar a depurar ou terminar a aplicação sem a executar, acabará com vários Eventos Ao Vivo na sua conta. <br/>Certifique-se de parar os eventos ao vivo. Caso contrário, será **cobrado!**

## <a name="examine-the-code-that-performs-live-streaming"></a>Examinar o código que efetua a transmissão em fluxo em direto

Esta secção examina funções definidas no ficheiro [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs) do projeto *MediaV3LiveApp*.

A amostra cria um sufixo único para cada recurso para que não tenha colisões de nomes se executar a amostra várias vezes sem limpar.

> [!IMPORTANT]
> Esta amostra utiliza um sufixo único para cada recurso. Se cancelar a depurar ou terminar a aplicação sem a executar, acabará com vários Eventos Ao Vivo na sua conta. <br/>
> Certifique-se de parar os eventos ao vivo. Caso contrário, será **cobrado!**

### <a name="start-using-media-services-apis-with-net-sdk"></a>Começar a utilizar APIs dos Serviços de Multimédia com SDK do .NET

Para começar a utilizar as APIs dos Serviços de Multimédia com o .NET, tem de criar um objeto **AzureMediaServicesClient**. Para criar o objeto, tem de fornecer as credenciais necessárias para o cliente se ligar ao Azure com o Azure AD. No código que clonou no início do artigo, a função **GetCredentialsAsync** cria o objeto ServiceClientCredentials com base nas credenciais fornecidas no ficheiro de configuração local. 

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateMediaServicesClient)]

### <a name="create-a-live-event"></a>Criar um evento em direto

Esta secção mostra como criar um tipo de evento live(LiveEventEncodType definido para Nenhum).  Para obter mais informações sobre os tipos disponíveis de Eventos Ao Vivo, consulte [os tipos de Eventos Ao Vivo.](live-events-outputs-concept.md#live-event-types) 
 
Algumas coisas que você pode querer especificar ao criar o evento ao vivo são:

* Localização dos Serviços de Comunicação Social.
* O protocolo de streaming para o Live Event (atualmente, os protocolos RTMP e Smooth Streaming são suportados).<br/>Não é possível alterar a opção de protocolo enquanto o Live Event ou as suas saídas ao vivo associadas estiverem em execução. Se necessitar de protocolos diferentes, crie evento ao vivo separado para cada protocolo de streaming.  
* Restrições de IP na ingestão e na pré-visualização. Pode definir os endereços IP que podem ingerir um vídeo neste Evento Ao Vivo. Os endereços IP permitidos podem ser especificados como um endereço IP único (por exemplo "10.0.0.1"), um intervalo de IP com um endereço IP e uma máscara de sub-rede CIDR (por exemplo, ' 10.0.0.1/22') ou um intervalo de IP com um endereço IP e uma máscara de sub-rede de ponto decimal (por exemplo , ' 10.0.0.1(255.255.252.0)').<br/>Se não forem especificados endereços IP e não houver definição de regra, nenhum endereço IP será permitido. Para permitir um endereço IP, crie uma regra e defina 0.0.0.0/0.<br/>Os endereços IP têm de estar num dos seguintes formatos: endereço IpV4 com quatro números ou intervalo de endereços CIDR.
* Ao criar o evento, pode especificar para o iniciar automaticamente. <br/>Quando o arranque automático estiver definido para ser verdade, o Live Event será iniciado após a criação. Isso significa que a faturação começa assim que o Evento Ao Vivo começa a funcionar. Tem de ligar explicitamente para parar o recurso Live Event para travar a faturação adicional. Para mais informações, consulte [os estados do Live Event e a faturação.](live-event-states-billing.md)
* Para que um URL inger seja preditivo, desa ajuste o modo "vaidade". Para obter informações [detalhadas, consulte Live Event ingerir URLs](live-events-outputs-concept.md#live-event-ingest-urls).

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateLiveEvent)]

### <a name="get-ingest-urls"></a>Obter URLs de inserção

Uma vez criado o Live Event, poderás obter URLs ingeridos que irás fornecer ao codificadores ao vivo. O codificador utiliza estes URLs para exibir uma transmissão um fluxo direto.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#GetIngestURL)]

### <a name="get-the-preview-url"></a>Obter o URL de pré-visualização

Utilize o previewEndpoint para pré-visualizar e certifique-se de que a entrada do codificador está a ser recebida.

> [!IMPORTANT]
> Certifique-se de que o vídeo está a fluir para o URL de pré-visualização antes de continuar.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#GetPreviewURLs)]

### <a name="create-and-manage-live-events-and-live-outputs"></a>Criar e gerir Eventos Ao Vivo e Saídas Ao Vivo

Assim que tiver o stream fluindo para o Live Event, pode iniciar o evento de streaming criando um Localizador de Ativos, Live Output e Streaming. Isto irá arquivar a transmissão em fluxo e torná-la disponível para os espetadores através do Ponto Final de Transmissão em Fluxo.

#### <a name="create-an-asset"></a>Criar um Elemento

Crie um Ativo para a saída ao vivo utilizar.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateAsset)]

#### <a name="create-a-live-output"></a>Criar uma saída ao vivo

As Saídas Ao Vivo começam na criação e param quando apagadas. Quando elimina a Saída Ao Vivo, não está a excluir o Ativo subjacente e o conteúdo do ativo.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateLiveOutput)]

#### <a name="create-a-streaming-locator"></a>Criar um localizador de streaming

> [!NOTE]
> Quando a sua conta De Serviços de Media é criada, um ponto final de streaming **predefinido** é adicionado à sua conta no estado **Stop.** Para começar a transmitir o seu conteúdo e tirar partido da [embalagem dinâmica](dynamic-packaging-overview.md) e da encriptação dinâmica, o ponto final de streaming a partir do qual pretende transmitir conteúdo tem de estar no estado **de Funcionamento.**

Quando publicar o ativo Live Output utilizando um Localizador de Streaming, o Evento Ao Vivo (até ao comprimento da janela do DVR) continuará a ser visível até ao termo ou supressão do Localizador de Streaming, o que vier primeiro.

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

Se já terminou os eventos de streaming e quer limpar os recursos a provisionados mais cedo, siga o seguinte procedimento:

* Termine o envio da transmissão em fluxo do codificador.
* Pare o evento ao vivo. Uma vez que o Evento Ao Vivo seja interrompido, não incorrerá em quaisquer acusações. Quando quiser reiniciar a transmissão, esta terá o mesmo URL de inserção, desta forma, não terá de reconfigurar o codificador.
* Pode parar o seu Ponto Final de Transmissão em Fluxo, a menos que pretenda continuar a fornecer o arquivo do seu evento em direto como uma transmissão em fluxo a pedido. Se o Evento Ao Vivo estiver num estado parado, não incorrerá em nenhuma acusação.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CleanupLiveEventAndOutput)]

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CleanupLocatorAssetAndStreamingEndpoint)]

## <a name="watch-the-event"></a>Ver o evento

Para assistir ao evento, copie o URL de streaming que obteve quando executou o código descrito no Create a Streaming Localizador. Podes usar um media player à tua escolha. [O Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) está disponível para testar o seu stream em https://ampdemo.azureedge.net .

O Live Event converte automaticamente eventos em conteúdo sonoro quando parado. Mesmo depois de parar e apagar o evento, os utilizadores podem transmitir o seu conteúdo arquivado como um vídeo a pedido enquanto não eliminar o ativo. Um ativo não pode ser eliminado se for usado por um evento; o evento deve ser apagado primeiro.

## <a name="clean-up-resources"></a>Limpar os recursos

Se já não precisa de nenhum dos recursos presentes no seu grupo de recursos, incluindo as contas de armazenamento que criou e os Serviços de Multimédia que carregou neste tutorial, elimine o grupo de recursos que criou anteriormente.

Execute o seguinte comando CLI:

```azurecli-interactive
az group delete --name amsResourceGroup
```

> [!IMPORTANT]
> Deixar o Evento Ao Vivo a funcionar incorre nos custos de faturação. Esteja ciente, se o projeto/programa falhar ou for fechado por qualquer motivo, pode deixar o Evento Ao Vivo em funcionamento em estado de faturação.

## <a name="ask-questions-give-feedback-get-updates"></a>Faça perguntas, dê feedback, obtenha atualizações

Consulte o artigo da [comunidade Azure Media Services](media-services-community.md) para ver diferentes formas de fazer perguntas, dar feedback e obter atualizações sobre os Media Services.

## <a name="next-steps"></a>Próximos passos

[Transmitir ficheiros em fluxo](stream-files-tutorial-with-api.md)
 
