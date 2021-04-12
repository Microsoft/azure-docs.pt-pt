---
título: Stream live with Media Services v3 : Azure Media Services descrição: Saiba como transmitir ao vivo com a Azure Media Services v3.
serviços: media-services documentationcenter: '' autor: IngridAtMicrosoft manager: femila editor: ''

ms.service: media-services ms.workload: media ms.tgt_pltfrm: na ms.devlang: na ms.topic: tutorial ms.custom: "mvc, devx-track-csharp" ms.date: 06/13/2019 ms.author: inhenkel

---

# <a name="tutorial-stream-live-with-media-services"></a>Tutorial: Stream ao vivo com serviços de mídia

> [!NOTE]
> Mesmo que o tutorial utilize exemplos [.NET SDK,](/dotnet/api/microsoft.azure.management.media.models.liveevent) os passos gerais são os mesmos para [REST API,](/rest/api/media/liveevents) [CLI,](/cli/azure/ams/live-event)ou [outros SDKs suportados.](media-services-apis-overview.md#sdks) 

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
- [Criar uma conta de Serviços de Comunicação](./account-create-how-to.md)Social.<br/>Certifique-se de copiar os dados do API Access no formato JSON ou armazenar os valores necessários para ligar à conta dos Serviços de Mídia no formato ficheiro .env utilizado nesta amostra.
- Siga os passos na [Access Azure Media Services API com o Azure CLI](./access-api-howto.md) e guarde as credenciais. Você precisará usá-los para aceder à API nesta amostra, ou inseri-los no formato de ficheiro .env. 
- Uma câmara ou um dispositivo (como um portátil) que é usado para transmitir um evento.
- Um codificadora de software no local que codifica o fluxo de câmaras e o envia para o serviço de streaming ao vivo dos Media Services utilizando o protocolo RTMP, consulte [codificadores ao vivo recomendados no local.](encode-recommended-on-premises-live-encoders.md) O fluxo tem de ser em formato **RTMP** ou de **transmissão em fluxo uniforme**.  
- Para esta amostra, recomenda-se começar com um codificador de software como o [Open Broadcast Software OBS Studio](https://obsproject.com/download) para tornar simples o arranque. 

> [!TIP]
> Certifique-se de que revê [Transmissão em fluxo em direto com os Serviços de Multimédia v3](stream-live-streaming-concept.md) antes de continuar. 

## <a name="download-and-configure-the-sample"></a>Descarregue e configuure a amostra

Clone o seguinte repositório Git Hub que contém a amostra de streaming ao vivo .NET para a sua máquina utilizando o seguinte comando:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet.git
 ```

O exemplo de transmissão em fluxo em direto está localizado na pasta [Dinâmica](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/Live).

Abra [appsettings.jsno](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/Live/LiveEventWithDVR/appsettings.json) seu projeto descarregado. Substitua os valores pelas credenciais que obteve de [aceder às APIs.](./access-api-howto.md)

Note que também pode utilizar o formato de ficheiro .env na raiz do projeto para definir as variáveis ambientais apenas uma vez para todos os projetos no repositório de amostras .NET. Basta copiar o ficheiro sample.env, preencher as informações que obtém na página API Access do portal Azure Media Services ou no Azure CLI.  Mude o nome do ficheiro sample.env para apenas ".env" para usá-lo em todos os projetos.
O ficheiro .gitignore já está configurado para evitar a publicação do conteúdo deste ficheiro no seu repositório forcado. 

> [!IMPORTANT]
> Esta amostra utiliza um sufixo único para cada recurso. Se cancelar a depurar ou terminar a aplicação sem a executar, acabará com vários Eventos Ao Vivo na sua conta. <br/>Certifique-se de parar os eventos ao vivo. Caso contrário, será **cobrado!**

## <a name="examine-the-code-that-performs-live-streaming"></a>Examinar o código que efetua a transmissão em fluxo em direto

Esta secção examina as funções definidas no ficheiro [.cs Programa](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/Live/LiveEventWithDVR/Program.cs) do projeto *LiveEventWithDVR.*

A amostra cria um sufixo único para cada recurso para que não tenha colisões de nomes se executar a amostra várias vezes sem limpar.


### <a name="start-using-media-services-apis-with-net-sdk"></a>Começar a utilizar APIs dos Serviços de Multimédia com SDK do .NET

Para começar a utilizar as APIs dos Serviços de Multimédia com o .NET, tem de criar um objeto **AzureMediaServicesClient**. Para criar o objeto, tem de fornecer as credenciais necessárias para o cliente se ligar ao Azure com o Azure AD. No código que clonou no início do artigo, a função **GetCredentialsAsync** cria o objeto ServiceClientCredentials com base nas credenciais fornecidas no ficheiro de configuração local (appsettings.js) ou através do ficheiro de variáveis ambientais .env localizado na raiz do repositório.

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#CreateMediaServicesClient)]

### <a name="create-a-live-event"></a>Criar um evento em direto

Esta secção mostra como criar um tipo de evento live(LiveEventEncodType definido para Nenhum).  Para obter mais informações sobre os outros tipos disponíveis de Eventos Ao Vivo, consulte [os tipos de Eventos Ao Vivo.](live-event-outputs-concept.md#live-event-types) Além de passar, pode utilizar um Evento Ao Vivo de transcoding ao vivo para codificação de nuvem bitrate adaptativa de 720P ou 1080P. 
 
Algumas coisas que você pode querer especificar ao criar o evento ao vivo são:

* O protocolo de ingestão para o Live Event (atualmente, os protocolos RTMP(S) e Smooth Streaming são suportados).<br/>Não é possível alterar a opção de protocolo enquanto o Live Event ou as suas saídas ao vivo associadas estiverem em execução. Se necessitar de protocolos diferentes, crie evento ao vivo separado para cada protocolo de streaming.  
* Restrições de IP na ingestão e na pré-visualização. Pode definir os endereços IP que podem ingerir um vídeo neste Evento Ao Vivo. Os endereços IP permitidos podem ser especificados como um endereço IP único (por exemplo "10.0.0.1"), um intervalo de IP com um endereço IP e uma máscara de sub-rede CIDR (por exemplo, ' 10.0.0.1/22') ou um intervalo de IP com um endereço IP e uma máscara de sub-rede de ponto decimal (por exemplo , ' 10.0.0.1(255.255.252.0)').<br/>Se não forem especificados endereços IP e não houver definição de regra, nenhum endereço IP será permitido. Para permitir um endereço IP, crie uma regra e defina 0.0.0.0/0.<br/>Os endereços IP têm de estar num dos seguintes formatos: endereço IpV4 com quatro números ou intervalo de endereços CIDR.
* Ao criar o evento, pode especificar para o iniciar automaticamente. <br/>Quando o arranque automático estiver definido para ser verdade, o Live Event será iniciado após a criação. Isso significa que a faturação começa assim que o Evento Ao Vivo começa a funcionar. Tem de ligar explicitamente para parar o recurso Live Event para travar a faturação adicional. Para mais informações, consulte [os estados do Live Event e a faturação.](live-event-states-billing-concept.md)
Existem também modos de espera disponíveis para iniciar o Live Event num estado de custo mais baixo 'atribuído' que torna mais rápido a mudança para um estado de 'Running'. Isto é útil para situações como hotpools que precisam distribuir canais rapidamente para streamers.
* Para que um URL inger seja preditivo e mais fácil de manter num codificader ao vivo baseado em hardware, desapedaça a propriedade "useStaticHostname" para ser verdadeira. Para obter informações [detalhadas, consulte Live Event ingerir URLs](live-event-outputs-concept.md#live-event-ingest-urls).

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#CreateLiveEvent)]

### <a name="get-ingest-urls"></a>Obter URLs de inserção

Uma vez criado o Live Event, poderás obter URLs ingeridos que irás fornecer ao codificadores ao vivo. O codificador utiliza estes URLs para exibir uma transmissão um fluxo direto.

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#GetIngestURL)]

### <a name="get-the-preview-url"></a>Obter o URL de pré-visualização

Utilize o previewEndpoint para pré-visualizar e certifique-se de que a entrada do codificador está a ser recebida.

> [!IMPORTANT]
> Certifique-se de que o vídeo está a fluir para o URL de pré-visualização antes de continuar.

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#GetPreviewURLs)]

### <a name="create-and-manage-live-events-and-live-outputs"></a>Criar e gerir Eventos Ao Vivo e Saídas Ao Vivo

Assim que tiver o stream fluindo para o Live Event, pode iniciar o evento de streaming criando um Localizador de Ativos, Live Output e Streaming. Isto irá arquivar a transmissão em fluxo e torná-la disponível para os espetadores através do Ponto Final de Transmissão em Fluxo.

Ao aprender estes conceitos, o melhor é pensar no objeto "Ativo" como a fita que você inseriria num gravador de vídeo nos velhos tempos. A "Saída Ao Vivo" é a máquina de gravação. O "Live Event" é apenas o sinal de vídeo que entra na parte de trás da máquina.

Primeiro cria-se o sinal criando o "Live Event".  O sinal não flui até iniciar o Live Event e ligar o codificante à entrada.

A fita pode ser criada a qualquer momento. É apenas um "Ativo" vazio que irá entregar ao objeto De saída ao vivo, o gravador nesta analogia.

O gravador pode ser criado a qualquer momento. O que significa que pode criar uma saída ao vivo antes de iniciar o fluxo de sinal, ou depois. Se precisar de acelerar as coisas, às vezes é útil criá-la antes de iniciar o fluxo de sinal.

Para parar o gravador, ligue para apagar o LiveOutput. Isto não apaga o conteúdo da fita "Ativo".  O Ativo é sempre mantido com o conteúdo de vídeo arquivado até que ligue para apagar explicitamente no próprio Ativo.

A próxima secção caminhará pela criação do Ativo ("fita") e da Saída Ao Vivo ("gravador de cassetes").

#### <a name="create-an-asset"></a>Criar um Elemento

Crie um Ativo para a saída ao vivo utilizar. Na analogia acima, esta será a nossa fita em que gravamos o sinal de vídeo ao vivo. Os espectadores poderão ver o conteúdo ao vivo ou a pedido desta fita virtual.

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#CreateAsset)]

#### <a name="create-a-live-output"></a>Criar uma saída ao vivo

As Saídas Ao Vivo começam na criação e param quando apagadas. Este vai ser o "gravador" do nosso evento. Quando elimina a Saída Ao Vivo, não está a excluir o Ativo ou conteúdo subjacente no ativo. Pense nisso como ejetar a fita. O Ativo com a gravação durará o tempo que quiser, e quando for ejetado (ou seja, quando a Saída Ao Vivo for eliminada) estará disponível para visualização a pedido imediatamente. 

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#CreateLiveOutput)]

#### <a name="create-a-streaming-locator"></a>Criar um localizador de streaming

> [!NOTE]
> Quando a sua conta De Serviços de Media é criada, um ponto final de streaming **predefinido** é adicionado à sua conta no estado **Stop.** Para começar a transmitir o seu conteúdo e tirar partido da [embalagem dinâmica](encode-dynamic-packaging-concept.md) e da encriptação dinâmica, o ponto final de streaming a partir do qual pretende transmitir conteúdo tem de estar no estado **de Funcionamento.**

Quando publicar o Ativo utilizando um Localizador de Streaming, o Evento Ao Vivo (até ao comprimento da janela DVR) continuará a ser visível até ao termo ou supressão do Localizador de Streaming, o que vier primeiro. É assim que disponibiliza a gravação virtual de "fita" para o seu público ver ao vivo e a pedido. O mesmo URL pode ser usado para assistir ao evento ao vivo, janela DVR ou o ativo a pedido quando a gravação estiver completa (quando a Saída Ao Vivo é eliminada.)

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#CreateStreamingLocator)]

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

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#CleanupLiveEventAndOutput)]

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#CleanupLocatorAssetAndStreamingEndpoint)]

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

## <a name="next-steps"></a>Passos seguintes

[Transmitir ficheiros em fluxo](stream-files-tutorial-with-api.md)
 
