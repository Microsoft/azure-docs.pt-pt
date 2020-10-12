---
title: Eventos ao vivo e conceitos de saídas ao vivo no Azure Media Services v3
titleSuffix: Azure Media Services
description: Este tópico fornece uma visão geral de eventos ao vivo e saídas ao vivo no Azure Media Services v3.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 9a32cd4db9a4c4dbd2b5f36c16feef4717790c3c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89291471"
---
# <a name="live-events-and-live-outputs-in-media-services"></a>Eventos ao vivo e saídas ao vivo em serviços de mídia

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

A Azure Media Services permite-lhe entregar eventos ao vivo aos seus clientes na nuvem Azure. Para configurar os seus eventos de streaming ao vivo nos Media Services v3, precisa entender os conceitos discutidos neste artigo.

> [!TIP]
> Para os clientes que migram dos Serviços de Media v2 APIs, a entidade **live event** substitui o **Canal** em v2 e a **Live Output** substitui o **Programa**.

## <a name="live-events"></a>Eventos em Direto

Os [Eventos em Direto](/rest/api/media/liveevents) são responsáveis pela ingestão e o processamento dos feeds de vídeos em direto. Quando cria um Evento Ao Vivo, é criado um ponto final de entrada primário e secundário que pode utilizar para enviar um sinal ao vivo a partir de um codificar remoto. O codificadora ao vivo remoto envia o feed de contribuição para esse ponto final de entrada utilizando o protocolo de entrada [RTMP](https://www.adobe.com/devnet/rtmp.html) ou [Smooth Streaming](/openspecs/windows_protocols/ms-sstr/8383f27f-7efe-4c60-832a-387274457251) (fragmentado-MP4). Para o protocolo rtmp ingest, o conteúdo pode ser enviado de forma clara `rtmp://` ou encriptada de forma segura no `rtmps://` fio. Para o protocolo Smooth Streaming ingest, os sistemas de URL suportados são `http://` ou `https://` .  

## <a name="live-event-types"></a>Tipos de eventos ao vivo

Um [Evento Ao Vivo](/rest/api/media/liveevents) pode ser configurado para um passe *através* (um codificadora ao vivo no local envia um fluxo bitrate múltiplo) ou *codificação ao vivo* (um codificadora ao vivo no local envia um único fluxo de bitrate). Os tipos são definidos durante a criação usando [LiveEventEncodingType](/rest/api/media/liveevents/create#liveeventencodingtype):

* **LiveEventEncodingType.None**: Um codificador ao vivo no local envia um fluxo bitrate múltiplo. O fluxo ingerido passa pelo Live Event sem qualquer processamento adicional. Também chamado de modo de passagem.
* **LiveEventEncodingType.Standard**: Um codificador ao vivo no local envia um único fluxo bitrate para o Live Event e Media Services cria vários streams bitrate. Se o feed de contribuição for de 720p ou resolução superior, a predefinição Predefinição **Default720p** codificará um conjunto de pares de resolução/bitrates de 6.
* **LiveEventEncodingType.Premium1080p**: Um codificador ao vivo no local envia um único fluxo bitrate para o Live Event e Media Services cria vários fluxos bitrate. A predefinição predefinida 1080p especifica o conjunto de saída dos pares de resolução/bitrates.

### <a name="pass-through"></a>Pass-through

![evento ao vivo com diagrama de exemplo de serviços de mídia](./media/live-streaming/pass-through.svg)

Quando utilizar o pass-through **Evento em Direto**, conta com o codificador em direto no local para gerar uma transmissão em fluxo de vídeo de velocidade múltipla e enviá-la como um feed de contribuição para o Evento em Direto (através do protocolo RTMP ou MP4 fragmentado). O Evento em Direto realiza as transmissões em fluxo de vídeo de entrada sem qualquer processamento adicional. Este evento live pass-through é otimizado para eventos ao vivo de longa duração ou 24x365 streaming linear ao vivo. Quando criar este tipo de Evento em Direto, especifique None (LiveEventEncodingType.None).

Pode enviar o feed de contribuição em resoluções até 4 K e a uma velocidade de 60 fotogramas/segundo, com os codecs de vídeo H.264/AVC ou H.265/HEVC e o codec de áudio AAC (AAC-LC, HE-AACv1 ou HE-AACv2). Para obter mais informações, consulte [a comparação dos tipos de Eventos Ao Vivo.](live-event-types-comparison.md)

> [!NOTE]
> Usar um método de passagem é a forma mais económica de fazer streaming ao vivo quando se está a fazer vários eventos durante um longo período de tempo, e já investiu em codificadores no local. Consulte os detalhes dos [preços](https://azure.microsoft.com/pricing/details/media-services/).
>

Consulte um exemplo de código .NET no [MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126).

### <a name="live-encoding"></a>Live Encoding  

![codificação ao vivo com diagrama de exemplo dos Serviços de Media](./media/live-streaming/live-encoding.svg)

Ao utilizar a codificação ao vivo com os Media Services, configura o seu codificar ao vivo no local para enviar um único vídeo bitrate como feed de contribuição para o Live Event (utilizando o protocolo RTMP ou Fragmented-Mp4). Em seguida, configura um Live Event para codificar o fluxo bitrate único para um [fluxo de vídeo bitrate múltiplo](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming), e disponibiliza a saída para a entrega para reproduzir dispositivos através de protocolos como MPEG-DASH, HLS e Smooth Streaming.

Quando utilizar codificação ao vivo, só pode enviar o feed de contribuição em resoluções até 1080p de resolução a uma taxa de fotogramas de 30 fotogramas/segundo, com código de áudio H.264/AVC e AAC (AAC-LC, HE-AACv1 ou HE-AACv2). Note que os Eventos Ao Vivo podem suportar resoluções até 4K a 60 fotogramas/segundo. Para obter mais informações, consulte [a comparação dos tipos de Eventos Ao Vivo.](live-event-types-comparison.md)

As resoluções e bitrates contidas na saída do codificadores vivos são determinadas pela predefinição. Se utilizar um codificador **standard** ao vivo (LiveEventEncodingType.Standard), então o predefinição *padrão 720p* especifica um conjunto de seis pares de velocidade de resolução/bit, passando de 720p a 3,5 Mbps para 192p a 200 kbps. Caso contrário, se utilizar um codificador ao vivo **Premium1080p** (LiveEventEncodingType.Premium1080p), então a predefinição *Predefinida 1080p* especifica um conjunto de seis pares de tarifas de resolução/bit, passando de 1080p a 3,5 Mbps para 180p a 200 kbps. Para obter informações, veja [System presets](live-event-types-comparison.md#system-presets) (Predefinições do sistema).

> [!NOTE]
> Se precisar de personalizar a predefinição de codificação ao vivo, abra um bilhete de apoio através do portal Azure. Especifique a tabela de resolução e bitrates desejada. Verifique se existe apenas uma camada a 720p (se solicitar uma predefinição para um codificadora Standard ao vivo) ou a 1080p (se solicitar uma predefinição para um codificadora ao vivo Premium1080p) e 6 camadas no máximo.

## <a name="creating-live-events"></a>Criação de Eventos Ao Vivo

### <a name="options"></a>Opções

Ao criar um Evento Ao Vivo, pode especificar as seguintes opções:

* O protocolo de streaming para o Live Event (atualmente, os protocolos RTMP e Smooth Streaming são suportados).<br/>Não é possível alterar a opção de protocolo enquanto o Live Event ou as suas saídas ao vivo associadas estiverem em execução. Se necessitar de protocolos diferentes, crie um Evento Ao Vivo separado para cada protocolo de streaming.  
* Ao criar o evento, pode especificar para o iniciar automaticamente. <br/>Quando o arranque automático estiver definido para ser verdade, o Live Event será iniciado após a criação. A faturação começa assim que o Live Event começa a funcionar. Tem de ligar explicitamente para parar o recurso Live Event para travar a faturação adicional. Em alternativa, pode iniciar o evento quando estiver pronto para começar o streaming.

    Para mais informações, consulte [os estados do Live Event e a faturação.](live-event-states-billing.md)

* Restrições de IP na ingestão e na pré-visualização. Pode definir os endereços IP que podem ingerir um vídeo neste Evento Ao Vivo. Os endereços IP permitidos podem ser especificados como um endereço IP único (por exemplo "10.0.0.1"), um intervalo de IP com um endereço IP e uma máscara de sub-rede CIDR (por exemplo, ' 10.0.0.1/22') ou um intervalo de IP com um endereço IP e uma máscara de sub-rede de ponto decimal (por exemplo , ' 10.0.0.1(255.255.252.0)').<br/>Se não forem especificados endereços IP e não houver definição de regra, nenhum endereço IP será permitido. Para permitir um endereço IP, crie uma regra e defina 0.0.0.0/0.<br/>Os endereços IP têm de estar num dos seguintes formatos: endereço IpV4 com quatro números ou intervalo de endereços CIDR.

    Se pretender ativar determinados IPs nas suas próprias firewalls ou pretender limitar as entradas aos seus eventos ao vivo para endereços IP Azure, descarregue um ficheiro JSON a partir dos intervalos de endereço IP do [Azure Datacenter](https://www.microsoft.com/download/details.aspx?id=41653). Para mais detalhes sobre este ficheiro, selecione a secção **Detalhes** na página.
    
* Ao criar o evento, pode optar por ligar transcrições ao vivo. <br/> Por predefinição, a transcrição ao vivo é desativada. Não é possível alterar esta propriedade enquanto o Live Event ou as suas saídas ao vivo associadas estiverem em execução. 

### <a name="naming-rules"></a>Regras de nomenclatura

* Max live event name é 32 caracteres.
* O nome deve seguir este padrão [regex:](/dotnet/standard/base-types/regular-expression-language-quick-reference) `^[a-zA-Z0-9]+(-*[a-zA-Z0-9])*$` .

Consulte também [as convenções de nomeação de Endpoints streaming.](streaming-endpoint-concept.md#naming-convention)

> [!TIP]
> Para garantir a singularidade do nome do seu evento ao vivo, pode gerar um GUID e, em seguida, remover todos os hífens e suportes encaracolados (se houver). A corda será única em todos os eventos ao vivo e o seu comprimento é garantido ser 32.

## <a name="live-event-ingest-urls"></a>URLs de ingestão de Eventos em Direto

Uma vez criado o Live Event, poderá obter URLs ingeridos que irá fornecer ao codificadores ao vivo no local. O codificador em direto utiliza esses URLs para introduzir uma transmissão um fluxo direto. Para mais informações, consulte [os codificadores ao vivo recomendados no local.](recommended-on-premises-live-encoders.md)

Pode utilizar URLs intuitivos ou não intuitivos.

> [!NOTE] 
> Para que um URL inger seja preditivo, desa ajuste o modo "vaidade".

* URL de não vaidade

    URL de não vaidade é o modo predefinido nos Serviços de Mídia v3. Potencialmente, recebe o Evento em Direto mais depressa, embora o URL de ingestão só seja conhecido quando esse evento é iniciado. Se parar/iniciar o Evento em Direto, o URL mudará. <br/>A Non-Vanity é útil em cenários em que um utilizador final quer transmitir através de uma app onde a app quer obter um evento ao vivo o mais rápido possível e ter um URL de ingestão dinâmico não é um problema.

    Se uma aplicação de cliente não precisar de gerar um URL de ingestão antes da criação do Evento Ao Vivo, deixe que os Serviços de Comunicação Social autogeram o Token de Acesso para o evento ao vivo.

* URL de vaidade

    O modo vaidade é preferido por grandes emissores de mídia que usam codificadores de transmissão de hardware e não querem reconfigurar os seus codificadores quando iniciam o Live Event. Estes emissores querem uma URL ingerível preditiva que não muda com o tempo.
    
    > [!NOTE]
    > No portal Azure, o URL de vaidade é nomeado "*URL de entrada persistente*".

    Para especificar este modo na API, definido `vanityUrl` para o momento da `true` criação (o padrão é `false` ). Você também precisa passar seu próprio token de acesso ( `LiveEventInput.accessToken` ) na hora da criação. Especifica o valor simbólico para evitar um token aleatório na URL. O token de acesso tem de ser uma corda GUID válida (com ou sem hífens). Uma vez definido o modo, não pode ser atualizado.

    O token de acesso tem de ser único no seu centro de dados. Se a sua aplicação precisar de usar um URL de vaidade, é aconselhável criar sempre uma nova instância GUID para o seu token de acesso (em vez de reutilizar qualquer GUID existente).

    Utilize as seguintes APIs para ativar o URL da Vaidade e desaperte o token de acesso a um GUID válido (por exemplo, `"accessToken": "1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"` ).  

    |Idioma|Ativar URL de vaidade|Definir o token de acesso|
    |---|---|---|
    |REST|[propriedades.vanityUrl](/rest/api/media/liveevents/create#liveevent)|[LiveEventInput.accessToken](/rest/api/media/liveevents/create#liveeventinput)|
    |CLI|[--vaidade-url](/cli/azure/ams/live-event?view=azure-cli-latest#az-ams-live-event-create)|[--token de acesso](/cli/azure/ams/live-event?view=azure-cli-latest#optional-parameters)|
    |.NET|[LiveEvent.VanityUrl](/dotnet/api/microsoft.azure.management.media.models.liveevent.vanityurl?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_LiveEvent_VanityUrl)|[LiveEventInput.AccessToken](/dotnet/api/microsoft.azure.management.media.models.liveeventinput.accesstoken?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_LiveEventInput_AccessToken)|
    
### <a name="live-ingest-url-naming-rules"></a>Regras de nomenclatura dos URLs de ingestão em direto

* A cadeia *aleatória* abaixo é um número hexadecimal de 128 bits (que é composto por 32 carateres de 0-9 e a-f).
* *o seu token de acesso*: A cadeia GUID válida que definiu ao utilizar o modo de vaidade. Por exemplo, `"1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"`.
* *nome do fluxo*: Indica o nome do fluxo para uma ligação específica. O valor do nome de fluxo é normalmente adicionado pelo codificar vivo que você usa. Pode configurar o codificader ao vivo para usar qualquer nome para descrever a ligação, por exemplo: "video1_audio1", "video2_audio1", "stream".

#### <a name="non-vanity-url"></a>URL de não vaidade

##### <a name="rtmp"></a>RTMP

`rtmp://<random 128bit hex string>.channel.media.azure.net:1935/live/<auto-generated access token>/<stream name>`<br/>
`rtmp://<random 128bit hex string>.channel.media.azure.net:1936/live/<auto-generated access token>/<stream name>`<br/>
`rtmps://<random 128bit hex string>.channel.media.azure.net:2935/live/<auto-generated access token>/<stream name>`<br/>
`rtmps://<random 128bit hex string>.channel.media.azure.net:2936/live/<auto-generated access token>/<stream name>`<br/>

##### <a name="smooth-streaming"></a>Transmissão em Fluxo Uniforme

`http://<random 128bit hex string>.channel.media.azure.net/<auto-generated access token>/ingest.isml/streams(<stream name>)`<br/>
`https://<random 128bit hex string>.channel.media.azure.net/<auto-generated access token>/ingest.isml/streams(<stream name>)`<br/>

#### <a name="vanity-url"></a>URL de vaidade

##### <a name="rtmp"></a>RTMP

`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1935/live/<your access token>/<stream name>`<br/>
`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1936/live/<your access token>/<stream name>`<br/>
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2935/live/<your access token>/<stream name>`<br/>
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2936/live/<your access token>/<stream name>`<br/>

##### <a name="smooth-streaming"></a>Transmissão em Fluxo Uniforme

`http://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<your access token>/ingest.isml/streams(<stream name>)`<br/>
`https://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<your access token>/ingest.isml/streams(<stream name>)`<br/>

## <a name="live-event-preview-url"></a>URL de pré-visualização do evento ao vivo

Assim que o Live Event começar a receber o feed de contribuição, pode utilizar o seu ponto final de pré-visualização para visualizar e validar que está a receber o live stream antes de publicar mais. Depois de verificar que o stream de pré-visualização é bom, pode utilizar o Live Event para disponibilizar o stream ao vivo para entrega através de um ou mais pontos de streaming (pré-criados). Para isso, crie uma nova [Produção Ao Vivo](/rest/api/media/liveoutputs) no Live Event.

> [!IMPORTANT]
> Certifique-se de que o vídeo está a fluir para o URL de pré-visualização antes de continuar!

## <a name="live-event-long-running-operations"></a>Operações de longo prazo do Live Event

Para mais detalhes, consulte [operações de longa duração.](media-services-apis-overview.md#long-running-operations)

## <a name="live-outputs"></a>Saídas em Direto

Assim que tiver o stream fluindo para o Live Event, pode iniciar o evento de streaming criando um [Ativo,](/rest/api/media/assets) [Live Output](/rest/api/media/liveoutputs)e [Streaming Localizador.](/rest/api/media/streaminglocators) A Live Output irá arquivar o fluxo e disponibilizá-lo aos espectadores através do [Streaming Endpoint.](/rest/api/media/streamingendpoints)  

Para obter informações detalhadas sobre as saídas ao vivo, consulte [utilizando um DVR em nuvem](live-event-cloud-dvr.md).

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

Consulte o artigo [perguntas frequentes.](frequently-asked-questions.md#live-streaming)

## <a name="ask-questions-and-get-updates"></a>Faça perguntas e obtenha atualizações

Consulte o artigo da [comunidade Azure Media Services](media-services-community.md) para ver diferentes formas de fazer perguntas, dar feedback e obter atualizações sobre os Media Services.

## <a name="next-steps"></a>Passos seguintes

[Tutorial de streaming ao vivo](stream-live-tutorial-with-api.md)
