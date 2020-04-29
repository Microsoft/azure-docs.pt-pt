---
title: Conceitos de Eventos ao Vivo e Saídas ao Vivo em Azure Media Services v3
titleSuffix: Azure Media Services
description: Este tema fornece uma visão geral dos eventos ao vivo e das saídas ao vivo no Azure Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 04/08/2020
ms.author: juliako
ms.openlocfilehash: 8eca95f9fca47fca4d54bacbab35f3a0ffc3ba31
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81010584"
---
# <a name="live-events-and-live-outputs-in-media-services"></a>Eventos ao vivo e saídas ao vivo em serviços de mídia

A Azure Media Services permite-lhe entregar eventos ao vivo aos seus clientes na nuvem Azure. Para configurar os seus eventos de streaming ao vivo em Media Services v3, precisa entender os conceitos discutidos neste artigo.

> [!TIP]
> Para clientes que migram de Media Services v2 APIs, a entidade **live event** substitui o **Canal** em v2 e a **Live Output** substitui o **Programa**.

## <a name="live-events"></a>Eventos em Direto

Os [Eventos em Direto](https://docs.microsoft.com/rest/api/media/liveevents) são responsáveis pela ingestão e o processamento dos feeds de vídeos em direto. Ao criar um Evento Ao Vivo, é criado um ponto final de entrada primário e secundário que pode utilizar para enviar um sinal ao vivo a partir de um codificador remoto. O codificador remoto ao vivo envia o feed de contribuição para esse ponto final de entrada utilizando o protocolo de entrada [RTMP](https://www.adobe.com/devnet/rtmp.html) ou [Smooth Streaming](https://msdn.microsoft.com/library/ff469518.aspx) (MP4 fragmentado). Para o protocolo de ingestão RTMP, o`rtmp://`conteúdo pode ser enviado na`rtmps://`clara ou encriptado de forma segura no fio. Para o protocolo de ingestão de streaming `http://` `https://`suave, os esquemas de URL suportados são ou .  

## <a name="live-event-types"></a>Tipos de eventos ao vivo

Um [Evento Ao Vivo](https://docs.microsoft.com/rest/api/media/liveevents) pode ser definido para um *pass-through* (um codificador ao vivo no local envia um fluxo de bitrate múltiplo) ou *codificação ao vivo* (um codificador ao vivo no local envia um único fluxo de bitrate). Os tipos são definidos durante a criação utilizando [liveEventEncodingType:](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventencodingtype)

* **LiveEventEncodingType.None**: Um codificador ao vivo no local envia um fluxo de bitrate múltiplo. O fluxo ingerido passa pelo Live Event sem qualquer processamento adicional. Também chamado de modo de passagem.
* **LiveEventEncodingType.Standard**: Um codificador ao vivo no local envia um único fluxo bitrate para o Live Event e media Services cria vários streams bitrate. Se o feed de contribuição for de 720p ou uma resolução superior, o predefinido **Predefinido Predefinido Default720p** codificará um conjunto de 6 pares de resolução/bitrates.
* **LiveEventEncodingType.Premium1080p**: Um codificador ao vivo no local envia um único fluxo bitrate para o Live Event e media Services cria vários streams bitrate. O predefinido Predefinido Predefinido Default1080p especifica o conjunto de saída dos pares de resolução/bitrates.

### <a name="pass-through"></a>Pass-through

![pass-through Live Event com o diagrama exemplo de Media Services](./media/live-streaming/pass-through.svg)

Quando utilizar o pass-through **Evento em Direto**, conta com o codificador em direto no local para gerar uma transmissão em fluxo de vídeo de velocidade múltipla e enviá-la como um feed de contribuição para o Evento em Direto (através do protocolo RTMP ou MP4 fragmentado). O Evento em Direto realiza as transmissões em fluxo de vídeo de entrada sem qualquer processamento adicional. Tal evento ao vivo pass-through está otimizado para eventos ao vivo de longa duração ou streaming linear 24x365. Quando criar este tipo de Evento em Direto, especifique None (LiveEventEncodingType.None).

Pode enviar o feed de contribuição em resoluções até 4 K e a uma velocidade de 60 fotogramas/segundo, com os codecs de vídeo H.264/AVC ou H.265/HEVC e o codec de áudio AAC (AAC-LC, HE-AACv1 ou HE-AACv2). Para mais informações, consulte a [comparação](live-event-types-comparison.md)de tipos de eventos ao vivo .

> [!NOTE]
> Usar um método de passagem é a forma mais económica de fazer streaming ao vivo quando se está a fazer vários eventos durante um longo período de tempo, e já investiu em codificadores no local. Consulte os detalhes dos [preços](https://azure.microsoft.com/pricing/details/media-services/).
>

Consulte um exemplo de código .NET no [MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126).

### <a name="live-encoding"></a>Live Encoding  

![codificação ao vivo com o diagrama exemplo de Serviços de Media](./media/live-streaming/live-encoding.svg)

Ao utilizar a codificação ao vivo com os Serviços de Media, configura o seu codificador ao vivo no local para enviar um único vídeo bitrate como feed de contribuição para o Live Event (utilizando o protocolo RTMP ou Fragmentado-Mp4). Em seguida, configura um Live Event para que ele coordeque o fluxo único de bitrate a um fluxo de [vídeo múltiplo](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming), e disponibilize a saída para a entrega para reproduzir dispositivos através de protocolos como MPEG-DASH, HLS e Smooth Streaming.

Quando utilizar codificação ao vivo, só pode enviar o feed de contribuição em resoluções até 1080p a uma taxa de fotogramas de 30 fotogramas/segundo, com código de vídeo H.264/AVC e AAC (AAC-LC, HE-AACv1 ou HE-AACv2). Note que os eventos ao vivo pass-through podem suportar resoluções até 4K a 60 fotogramas/segundo. Para mais informações, consulte a [comparação](live-event-types-comparison.md)de tipos de eventos ao vivo .

As resoluções e bitrates contidos na saída do codificador vivo são determinados pelo predefinido. Se utilizar um codificador **standard** ao vivo (LiveEventEncodingType.Standard), então o predefinido *Predefinido Padrão720p* especifica um conjunto de seis pares de taxa de resolução/bit, passando de 720p a 3,5 Mbps para 192p a 200 kbps. Caso contrário, se utilizar um codificador ao vivo **Premium1080p** (LiveEventEncodingType.Premium1080p), então o predefinido *Predefinido Padrão1080p* especifica um conjunto de seis pares de taxa de resolução/bit, passando de 1080p a 3,5 Mbps para 180p a 200 kbps. Para obter informações, veja [System presets](live-event-types-comparison.md#system-presets) (Predefinições do sistema).

> [!NOTE]
> Se precisar de personalizar o predefinição de codificação ao vivo, abra um bilhete de apoio através do portal Azure. Especifique a tabela de resolução e bitrates desejada. Verifique se há apenas uma camada a 720p (se solicitar um predefinido para um codificador standard ao vivo) ou a 1080p (se solicitar um predefinido para um codificador ao vivo Premium1080p) e 6 camadas no máximo.

## <a name="creating-live-events"></a>Criação de eventos ao vivo

### <a name="options"></a>Opções

Ao criar um Evento Ao Vivo, pode especificar as seguintes opções:

* O protocolo de streaming para o Live Event (atualmente, os protocolos RTMP e Smooth Streaming são suportados).<br/>Não pode alterar a opção de protocolo enquanto o Live Event ou as suas saídas ao vivo associadas estiverem em execução. Se necessitar de protocolos diferentes, crie um Evento Live separado para cada protocolo de streaming.  
* Ao criar o evento, pode especificar para iniciar automaticamente. <br/>Quando o autoarranque estiver pronto para ser verdade, o Live Event será iniciado após a criação. A faturação começa assim que o Evento Ao Vivo começar a correr. Deve ligar explicitamente para parar o recurso Stop on the Live Event para parar a faturação adicional. Em alternativa, pode iniciar o evento quando estiver pronto para começar a transmitir.

    Para mais informações, consulte os estados do [Live Event e a faturação.](live-event-states-billing.md)

* Restrições de IP na ingestão e na pré-visualização. Pode definir os endereços IP que são autorizados a ingerir um vídeo para este Evento Ao Vivo. Os endereços IP permitidos podem ser especificados como um endereço IP único (por exemplo "10.0.0.1"), um intervalo de IP com um endereço IP e uma máscara de sub-rede CIDR (por exemplo, ' 10.0.0.1/22') ou um intervalo de IP com um endereço IP e uma máscara de sub-rede de ponto decimal (por exemplo , ' 10.0.0.1(255.255.252.0)').<br/>Se não forem especificados endereços IP e não houver definição de regra, então nenhum endereço IP será permitido. Para permitir um endereço IP, crie uma regra e defina 0.0.0.0/0.<br/>Os endereços IP têm de estar num dos seguintes formatos: endereço IpV4 com quatro números ou intervalo de endereços CIDR.

    Se pretender ativar determinados IPs nas suas próprias firewalls ou pretender limitar as inputs aos seus eventos ao vivo para endereços IP Do Azure, faça o download de um ficheiro JSON a partir das gamas de endereços IP do [Azure Datacenter](https://www.microsoft.com/download/details.aspx?id=41653). Para mais detalhes sobre este ficheiro, selecione a secção **Detalhes** na página.
    
* Ao criar o evento, pode optar por ativar transcrições ao vivo. <br/> Por defeito, a transcrição ao vivo é desativada. Não pode alterar esta propriedade enquanto o Live Event ou as suas saídas ao vivo associadas estiverem em execução. 

### <a name="naming-rules"></a>Regras de nomenclatura

* O nome do evento ao vivo é de 32 caracteres.
* O nome deve seguir este `^[a-zA-Z0-9]+(-*[a-zA-Z0-9])*$`padrão [regex:](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference) .

Consulte também as convenções de [nomeação de Streaming Endpoints](streaming-endpoint-concept.md#naming-convention).

> [!TIP]
> Para garantir a singularidade do seu nome de evento ao vivo, pode gerar um GUID e depois remover todos os hífenes e suportes encaracolados (se houver). A corda será única em todos os eventos ao vivo e o seu comprimento é garantido ser 32.

## <a name="live-event-ingest-urls"></a>URLs de ingestão de Eventos em Direto

Assim que o Evento Ao Vivo for criado, pode obter URLs ingerir que irá fornecer ao codificador ao vivo no local. O codificador em direto utiliza esses URLs para introduzir uma transmissão um fluxo direto. Para mais informações, consulte [os codificadores ao vivo recomendados no local.](recommended-on-premises-live-encoders.md)

Pode utilizar URLs intuitivos ou não intuitivos.

> [!NOTE] 
> Para que um URL ingerir seja preditivo, delineie o modo "vaidade".

* URL de não vaidade

    URL não-vaidade é o modo padrão nos Serviços de Media v3. Potencialmente, recebe o Evento em Direto mais depressa, embora o URL de ingestão só seja conhecido quando esse evento é iniciado. Se parar/iniciar o Evento em Direto, o URL mudará. <br/>A Não-Vanity é útil em cenários em que um utilizador final quer transmitir usando uma app onde a aplicação quer obter um evento ao vivo o mais rápido possível e ter um URL de ingestão dinâmico não é um problema.

    Se uma aplicação de cliente não precisar de pré-gerar um URL ingerir antes da criação do Evento Ao Vivo, deixe os Media Services autogerarem o Access Token para o evento ao vivo.

* URL de vaidade

    O modo vanity é preferido por grandes emissores de meios de comunicação que usam codificadores de transmissão de hardware e não querem reconfigurar os seus codificadores quando iniciarem o Live Event. Estes emissores querem um URL preditivo que não mude com o tempo.
    
    > [!NOTE]
    > No portal Azure, o URL de vaidade é nomeado " URL de*entrada persistente*".

    Para especificar este modo na `vanityUrl` API, definido `true` para `false`o momento de criação (o padrão é ). Você também precisa passar seu próprio`LiveEventInput.accessToken`sinal de acesso ( ) no momento da criação. Especifica o valor do símbolo para evitar um token aleatório no URL. O símbolo de acesso tem de ser uma corda GUID válida (com ou sem os hífenes). Uma vez definido o modo, não pode ser atualizado.

    O sinal de acesso tem de ser único no seu centro de dados. Se a sua aplicação precisar de usar um URL de vaidade, é aconselhável criar sempre uma nova instância GUID para o seu token de acesso (em vez de reutilizar qualquer GUID existente).

    Utilize as seguintes APIs para ativar o URL da Vaidade `"accessToken": "1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"`e coloque o sinal de acesso a um GUID válido (por exemplo, ).  

    |Idioma|Ativar URL de vaidade|Definir o token de acesso|
    |---|---|---|
    |REST|[propriedades.vanityUrl](https://docs.microsoft.com/rest/api/media/liveevents/create#liveevent)|[LiveEventInput.accessToken](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventinput)|
    |CLI|[--vaidade-url](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest#az-ams-live-event-create)|[--acesso-token](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest#optional-parameters)|
    |.NET|[LiveEvent.VanityUrl](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveevent.vanityurl?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_LiveEvent_VanityUrl)|[LiveEventInput.AccessToken](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveeventinput.accesstoken?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_LiveEventInput_AccessToken)|
    
### <a name="live-ingest-url-naming-rules"></a>Regras de nomenclatura dos URLs de ingestão em direto

* A cadeia *aleatória* abaixo é um número hexadecimal de 128 bits (que é composto por 32 carateres de 0-9 e a-f).
* *ficha de acesso*: A corda GUID válida que definiu ao utilizar o modo de vaidade. Por exemplo, `"1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"`.
* *nome*do fluxo : Indica o nome da corrente para uma ligação específica. O valor do nome do fluxo é geralmente adicionado pelo codificador ao vivo que utiliza. Pode configurar o codificador ao vivo para usar qualquer nome para descrever a ligação, por exemplo: "video1_audio1", "video2_audio1", "stream".

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

Assim que o Live Event começar a receber o feed de contribuição, pode utilizar o seu ponto final de pré-visualização para pré-visualizar e validar que está a receber o live stream antes de publicar novamente. Depois de verificar se o fluxo de pré-visualização é bom, pode utilizar o Live Event para disponibilizar o live stream para entrega através de um ou mais pontos finais de streaming (pré-criados). Para isso, crie uma nova [Produção Ao Vivo](https://docs.microsoft.com/rest/api/media/liveoutputs) no Evento Ao Vivo.

> [!IMPORTANT]
> Certifique-se de que o vídeo está a fluir para o URL de pré-visualização antes de continuar!

## <a name="live-event-long-running-operations"></a>Operações de longa duração do Live Event

Para mais detalhes, consulte [as operações de longo prazo](media-services-apis-overview.md#long-running-operations).

## <a name="live-outputs"></a>Saídas em Direto

Assim que tiver o fluxo fluindo para o Live Event, pode iniciar o evento de streaming criando um [Asset](https://docs.microsoft.com/rest/api/media/assets), [Live Output](https://docs.microsoft.com/rest/api/media/liveoutputs)e [Streaming Locator.](https://docs.microsoft.com/rest/api/media/streaminglocators) A Live Output irá arquivar o stream e disponibilizá-lo aos espectadores através do [Streaming Endpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints).  

Para obter informações detalhadas sobre saídas ao vivo, consulte [Utilizar um DVR em nuvem](live-event-cloud-dvr.md).

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

Consulte o artigo de [perguntas frequentes.](frequently-asked-questions.md#live-streaming)

## <a name="ask-questions-and-get-updates"></a>Faça perguntas e obtenha atualizações

Confira o artigo da [comunidade Azure Media Services](media-services-community.md) para ver diferentes formas de fazer perguntas, dar feedback e obter atualizações sobre os Serviços de Media.

## <a name="next-steps"></a>Passos seguintes

[Tutorial de streaming ao vivo](stream-live-tutorial-with-api.md)
