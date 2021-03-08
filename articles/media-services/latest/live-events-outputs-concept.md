---
title: Eventos ao vivo e conceitos de saídas ao vivo
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
ms.date: 10/23/2020
ms.author: inhenkel
ms.openlocfilehash: a66532856263d31e9070bc99f297ae105ca48312
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/08/2021
ms.locfileid: "102454792"
---
# <a name="live-events-and-live-outputs-in-media-services"></a>Eventos ao vivo e saídas ao vivo nos Serviços de Media

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

A Azure Media Services permite-lhe entregar eventos ao vivo aos seus clientes na nuvem Azure. Para configurar os seus eventos de streaming ao vivo nos Media Services v3, precisa entender os conceitos discutidos neste artigo.

> [!TIP]
> Para os clientes que migram dos Serviços de Media v2 APIs, a entidade **de eventos ao vivo** substitui o **Canal** na V2 e a saída **ao vivo** substitui **o programa**.

## <a name="live-events"></a>Live events (Eventos em direto)

[Os eventos](/rest/api/media/liveevents) ao vivo são responsáveis por ingerir e processar os feeds de vídeo ao vivo. Quando cria um evento ao vivo, é criado um ponto final de entrada primário e secundário que pode usar para enviar um sinal ao vivo a partir de um codificar remoto. O codificadora ao vivo remoto envia o feed de contribuição para esse ponto final de entrada utilizando o protocolo de entrada [RTMP](https://www.adobe.com/devnet/rtmp.html) ou [Smooth Streaming](/openspecs/windows_protocols/ms-sstr/8383f27f-7efe-4c60-832a-387274457251) (fragmentado-MP4). Para o protocolo rtmp ingest, o conteúdo pode ser enviado de forma clara `rtmp://` ou encriptada de forma segura no `rtmps://` fio. Para o protocolo Smooth Streaming ingest, os sistemas de URL suportados são `http://` ou `https://` .  

## <a name="live-event-types"></a>Tipos de eventos ao vivo

Um [evento ao vivo](/rest/api/media/liveevents) pode ser definido para um *passe-through* (um codificadora ao vivo no local envia um fluxo bitrate múltiplo) ou *codificação ao vivo* (um codificante ao vivo no local envia um único fluxo de bitrate). Os tipos são definidos durante a criação usando [LiveEventEncodingType](/rest/api/media/liveevents/create#liveeventencodingtype):

* **LiveEventEncodingType.None**: Um codificador ao vivo no local envia um fluxo bitrate múltiplo. O fluxo ingerido passa pelo evento ao vivo sem qualquer processamento adicional. Também chamado de modo de passagem.
* **LiveEventEncodingType.Standard**: Um codificador ao vivo no local envia um único fluxo bitrate para o evento ao vivo e os Media Services criam vários fluxos bitrate. Se o feed de contribuição for de 720p ou resolução superior, a predefinição Predefinição **Default720p** codificará um conjunto de pares de resolução/bitrates de 6.
* **LiveEventEncodingType.Premium1080p**: Um codificador ao vivo no local envia um único fluxo bitrate para o evento ao vivo e os Media Services criam vários fluxos de bitrate. A predefinição predefinida 1080p especifica o conjunto de saída dos pares de resolução/bitrates.

### <a name="pass-through"></a>Pass-through

![evento ao vivo com diagrama de exemplo dos Media Services](./media/live-streaming/pass-through.svg)

Ao utilizar o **evento ao vivo** de passagem, você confia no seu codificadora ao vivo no local para gerar um fluxo de vídeo bitrate múltiplo e enviar-o como feed de contribuição para o evento ao vivo (usando o protocolo RTMP ou fragmentado-MP4). O evento ao vivo transporta então através dos streams de vídeo que chegam sem qualquer processamento adicional. Este evento ao vivo é otimizado para eventos ao vivo de longa duração ou streaming linear 24x365 ao vivo. Ao criar este tipo de evento ao vivo, especifique Nenhum (LiveEventEncodingType.None).

Pode enviar o feed de contribuição em resoluções até 4 K e a uma velocidade de 60 fotogramas/segundo, com os codecs de vídeo H.264/AVC ou H.265/HEVC e o codec de áudio AAC (AAC-LC, HE-AACv1 ou HE-AACv2). Para obter mais informações, consulte [a comparação de tipos de eventos ao vivo.](live-event-types-comparison.md)

> [!NOTE]
> Usar um método de passagem é a forma mais económica de fazer streaming ao vivo quando se está a fazer vários eventos durante um longo período de tempo, e já investiu em codificadores no local. Consulte os detalhes [dos preços.](https://azure.microsoft.com/pricing/details/media-services/)
>

Consulte o exemplo do código .NET para criar um evento ao vivo em [evento ao vivo com DVR](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/4a436376e77bad57d6cbfdc02d7df6c615334574/Live/LiveEventWithDVR/Program.cs#L214).

### <a name="live-encoding"></a>Live Encoding  

![codificação ao vivo com diagrama de exemplo dos Serviços de Media](./media/live-streaming/live-encoding.svg)

Ao utilizar a codificação ao vivo com os Media Services, configura o seu codificar ao vivo no local para enviar um único vídeo bitrate como feed de contribuição para o evento ao vivo (utilizando o protocolo RTMP ou Fragmented-Mp4). Em seguida, configura um evento ao vivo para codificar o fluxo bitrate único para um [fluxo de vídeo bitrate múltiplo](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming), e disponibiliza a saída para a entrega para reproduzir dispositivos através de protocolos como MPEG-DASH, HLS e Smooth Streaming.

Quando utilizar codificação ao vivo, só pode enviar o feed de contribuição em resoluções até 1080p de resolução a uma taxa de fotogramas de 30 fotogramas/segundo, com código de áudio H.264/AVC e AAC (AAC-LC, HE-AACv1 ou HE-AACv2). Note que eventos ao vivo podem suportar resoluções até 4K a 60 fotogramas/segundo. Para obter mais informações, consulte [a comparação de tipos de eventos ao vivo.](live-event-types-comparison.md)

As resoluções e bitrates contidas na saída do codificadores vivos são determinadas pela predefinição. Se utilizar um codificador **standard** ao vivo (LiveEventEncodingType.Standard), então o predefinição *padrão 720p* especifica um conjunto de seis pares de velocidade de resolução/bit, passando de 720p a 3,5 Mbps para 192p a 200 kbps. Caso contrário, se utilizar um codificador ao vivo **Premium1080p** (LiveEventEncodingType.Premium1080p), então a predefinição *Predefinida 1080p* especifica um conjunto de seis pares de tarifas de resolução/bit, passando de 1080p a 3,5 Mbps para 180p a 200 kbps. Para obter informações, veja [System presets](live-event-types-comparison.md#system-presets) (Predefinições do sistema).

> [!NOTE]
> Se precisar de personalizar a predefinição de codificação ao vivo, abra um bilhete de apoio através do portal Azure. Especifique a tabela de resolução e bitrates desejada. Verifique se existe apenas uma camada a 720p (se solicitar uma predefinição para um codificadora Standard ao vivo) ou a 1080p (se solicitar uma predefinição para um codificadora ao vivo Premium1080p) e 6 camadas no máximo.

## <a name="creating-live-events"></a>Criar eventos ao vivo

### <a name="options"></a>Opções

Ao criar um evento ao vivo, pode especificar as seguintes opções:

* Pode dar ao evento ao vivo um nome e uma descrição.
* A codificação em nuvem inclui pass-through (sem codificação de nuvens), Standard (até 720p) ou Premium (até 1080p). Para codificação Standard e Premium, pode escolher o modo de alongamento do vídeo codificado.
  * Nenhum: Respeita estritamente a resolução de saída especificada na predefinição de codificação sem considerar a relação de aspeto do pixel ou a relação de aspeto do ecrã do vídeo de entrada.
  * Tamanho automático: Substitui a resolução de saída e altera-a para corresponder à relação de aspeto do visor da entrada, sem acolchoamento. Por exemplo, se a entrada for 1920x1080 e a predefinição codificante pedir 1280x1280, então o valor na predefinição é ultrapassado, e a saída será de 1280x720, que mantém o rácio de aspeto de entrada de 16:9.
  * AutoFit: Pade a saída (com caixa de correio ou caixa de pilares) para honrar a resolução de saída, garantindo ao mesmo tempo que a região de vídeo ativa na saída tem o mesmo rácio de aspeto que a entrada. Por exemplo, se a entrada for 1920x1080 e a predefinição codificante pedir 1280x1280, então a saída será de 1280x1280, que contém um retângulo interno de 1280x720 no rácio de aspeto de 16:9, com caixas de pilares 280 pixels de largura à esquerda e à direita.
* O protocolo de streaming (atualmente, os protocolos RTMP e Smooth Streaming são suportados). Não é possível alterar a opção de protocolo enquanto o evento ao vivo ou as suas saídas ao vivo associadas estiverem em execução. Se necessitar de protocolos diferentes, crie um evento ao vivo separado para cada protocolo de streaming.
* ID de entrada que é um identificador globalmente único para o fluxo de entrada de eventos ao vivo.
* Prefixo estático do nome de anfitrião que inclui nenhum (nesse caso será usado um fio hexáxinho aleatório de 128 bits), Use o nome do evento ao vivo ou use o nome personalizado.  Quando escolhe utilizar um nome de cliente, este valor é o prefixo de nome de anfitrião personalizado.
* Pode reduzir a latência de ponta a ponta entre a transmissão ao vivo e a reprodução, definindo o intervalo de quadro da chave de entrada, que é a duração (em segundos) de cada segmento de mídia na saída HLS. O valor deve ser um inteiro não-zero no intervalo de 0,5 a 20 segundos.  O valor desresídeo a 2 segundos se *nenhum* dos intervalos de quadro de entrada ou de saída estiver definido. O intervalo do quadro-chave só é permitido em eventos de passagem.
* Ao criar o evento, pode defini-lo para iniciar automaticamente. Quando o arranque automático for verdadeiro, o evento ao vivo será iniciado após a criação. A faturação começa assim que o evento ao vivo começa a decorrer. Você deve explicitamente ligar para parar o recurso do evento ao vivo para parar mais faturação. Em alternativa, pode iniciar o evento quando estiver pronto para começar o streaming.

> [!NOTE]
> O framerate máximo é de 30 fps para codificação Standard e Premium.

## <a name="standby-mode"></a>Modo StandBy

Quando criar um evento ao vivo, pode defini-lo para o modo StandBy. Enquanto o evento estiver no modo StandBy, pode editar a Descrição, o prefixo estático do nome anfitrião e restringir as definições de entrada e pré-visualização do acesso.  O modo StandBy ainda é um modo de faturação, mas tem um preço diferente de quando inicia um stream ao vivo.

Para mais informações, consulte [estados do evento ao vivo e faturação.](live-event-states-billing.md)

* Restrições de IP na ingestão e na pré-visualização. Pode definir os endereços IP que podem ingerir um vídeo para este evento ao vivo. Os endereços IP permitidos podem ser especificados como um endereço IP único (por exemplo "10.0.0.1"), um intervalo de IP com um endereço IP e uma máscara de sub-rede CIDR (por exemplo, ' 10.0.0.1/22') ou um intervalo de IP com um endereço IP e uma máscara de sub-rede de ponto decimal (por exemplo , ' 10.0.0.1(255.255.252.0)').
<br/><br/>
Se não forem especificados endereços IP e não houver definição de regra, nenhum endereço IP será permitido. Para permitir um endereço IP, crie uma regra e defina 0.0.0.0/0.<br/>Os endereços IP têm de estar num dos seguintes formatos: endereço IpV4 com quatro números ou intervalo de endereços CIDR.
<br/><br/>
Se pretender ativar determinados IPs nas suas próprias firewalls ou pretender limitar as entradas aos seus eventos ao vivo para endereços IP Azure, descarregue um ficheiro JSON a partir dos intervalos de endereço IP do [Azure Datacenter](https://www.microsoft.com/download/details.aspx?id=41653). Para mais detalhes sobre este ficheiro, selecione a secção **Detalhes** na página.

* Ao criar o evento, pode optar por ligar transcrições ao vivo. Por predefinição, a transcrição ao vivo é desativada. Para mais informações sobre transcrição em direto leia [a transcrição em direto.](live-transcription.md)

### <a name="naming-rules"></a>Regras de nomenclatura

* Max live event name é 32 caracteres.
* O nome deve seguir este padrão [regex:](/dotnet/standard/base-types/regular-expression-language-quick-reference) `^[a-zA-Z0-9]+(-*[a-zA-Z0-9])*$` .

Consulte também [as convenções de nomeação de Endpoints streaming.](streaming-endpoint-concept.md#naming-convention)

> [!TIP]
> Para garantir a singularidade do nome do seu evento ao vivo, pode gerar um GUID e, em seguida, remover todos os hífens e suportes encaracolados (se houver). A corda será única em todos os eventos ao vivo e o seu comprimento é garantido ser 32.

## <a name="live-event-ingest-urls"></a>Evento ao vivo ingere URLs

Uma vez criado o evento ao vivo, você pode obter URLs ingeridos que você vai fornecer para o codificadora ao vivo no local. O codificador em direto utiliza esses URLs para introduzir uma transmissão um fluxo direto. Para mais informações, consulte [os codificadores ao vivo recomendados no local.](recommended-on-premises-live-encoders.md)

>[!NOTE]
> A partir do lançamento da API 2020-05-01, os URLs de vaidade são conhecidos como Nomes estáticos do anfitrião

Pode utilizar URLs intuitivos ou não intuitivos.

> [!NOTE]
> Para que um URL inger seja preditivo, desa ajuste o modo "vaidade".

* URL de não vaidade

    URL de não vaidade é o modo predefinido nos Serviços de Mídia v3. Você potencialmente obtém o evento ao vivo rapidamente, mas ingestING URL é conhecido apenas quando o evento ao vivo é iniciado. O URL mudará se parar/iniciar o evento ao vivo. A Non-Vanity é útil em cenários em que um utilizador final quer transmitir através de uma app onde a app quer obter um evento ao vivo o mais rápido possível e ter um URL de ingestão dinâmico não é um problema.

    Se uma aplicação de cliente não precisar de gerar um URL de ingestão antes da criação do evento ao vivo, deixe que os Media Services gerem automaticamente o Access Token para o evento ao vivo.

* URL de vaidade

    O modo vaidade é preferido por grandes emissores de mídia que usam codificadores de transmissão de hardware e não querem reconfigurar os seus codificadores quando iniciam o evento ao vivo. Estes emissores querem uma URL ingerível preditiva que não muda com o tempo.

    > [!NOTE]
    > No portal Azure, o URL de vaidade é nomeado "*Prefixo de nome de hospedeiro estático*".

    Para especificar este modo na API, definido `useStaticHostName` para o momento da `true` criação (o padrão é `false` ). Quando `useStaticHostname` é definido como verdadeiro, o especificado a primeira parte do nome `hostnamePrefix` anfitrião atribuído à pré-visualização do evento ao vivo e ingerir pontos finais. O nome de anfitrião final seria uma combinação deste prefixo, o nome da conta de serviço de mídia e um código curto para o centro de dados Azure Media Services.

    Para evitar um token aleatório no URL, você também precisa passar seu próprio token de acesso `LiveEventInput.accessToken` () na hora da criação.  O token de acesso tem de ser uma corda GUID válida (com ou sem hífens). Uma vez definido o modo, não pode ser atualizado.

    O token de acesso tem de ser único no seu centro de dados. Se a sua aplicação precisar de usar um URL de vaidade, é aconselhável criar sempre uma nova instância GUID para o seu token de acesso (em vez de reutilizar qualquer GUID existente).

    Utilize as seguintes APIs para ativar o URL da Vaidade e desaperte o token de acesso a um GUID válido (por exemplo, `"accessToken": "1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"` ).  

    |Linguagem|Ativar URL de vaidade|Definir o token de acesso|
    |---|---|---|
    |REST|[propriedades.vanityUrl](/rest/api/media/liveevents/create#liveevent)|[LiveEventInput.accessToken](/rest/api/media/liveevents/create#liveeventinput)|
    |CLI|[--vaidade-url](/cli/azure/ams/live-event#az-ams-live-event-create)|[--token de acesso](/cli/azure/ams/live-event#optional-parameters)|
    |.NET|[LiveEvent.VanityUrl](/dotnet/api/microsoft.azure.management.media.models.liveevent#Microsoft_Azure_Management_Media_Models_LiveEvent_VanityUrl)|[LiveEventInput.AccessToken](/dotnet/api/microsoft.azure.management.media.models.liveeventinput.accesstoken#Microsoft_Azure_Management_Media_Models_LiveEventInput_AccessToken)|

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

##### <a name="smooth-streaming"></a>Streaming suave

`http://<random 128bit hex string>.channel.media.azure.net/<auto-generated access token>/ingest.isml/streams(<stream name>)`<br/>
`https://<random 128bit hex string>.channel.media.azure.net/<auto-generated access token>/ingest.isml/streams(<stream name>)`<br/>

#### <a name="vanity-url"></a>URL de vaidade

Nos seguintes caminhos, `<live-event-name>` significa o nome dado ao evento ou o nome personalizado usado na criação do evento ao vivo.

##### <a name="rtmp"></a>RTMP

`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1935/live/<your access token>/<stream name>`<br/>
`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1936/live/<your access token>/<stream name>`<br/>
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2935/live/<your access token>/<stream name>`<br/>
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2936/live/<your access token>/<stream name>`<br/>

##### <a name="smooth-streaming"></a>Streaming suave

`http://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<your access token>/ingest.isml/streams(<stream name>)`<br/>
`https://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<your access token>/ingest.isml/streams(<stream name>)`<br/>

## <a name="live-event-preview-url"></a>URL de pré-visualização de eventos ao vivo

Assim que o evento ao vivo começar a receber o feed de contribuição, pode utilizar o seu ponto final de pré-visualização para visualizar e validar que está a receber o live stream antes de publicar mais. Depois de verificar que o stream de pré-visualização é bom, pode utilizar o evento ao vivo para disponibilizar o stream ao vivo para entrega através de um ou mais pontos de streaming (pré-criados). Para isso, crie uma nova [produção ao vivo](/rest/api/media/liveoutputs) no evento ao vivo.

> [!IMPORTANT]
> Certifique-se de que o vídeo está a fluir para o URL de pré-visualização antes de continuar!

## <a name="live-event-long-running-operations"></a>Operações de longa duração de eventos ao vivo

Para mais detalhes, consulte [operações de longa duração.](media-services-apis-overview.md#long-running-operations)

## <a name="live-outputs"></a>Saídas ao vivo

Assim que tiver o stream fluindo para o evento ao vivo, pode iniciar o evento de streaming criando um [Ativo](/rest/api/media/assets), [saída ao vivo](/rest/api/media/liveoutputs)e [Localizador de Streaming.](/rest/api/media/streaminglocators) a saída ao vivo irá arquivar o fluxo e disponibilizá-lo aos espectadores através do [Streaming Endpoint.](/rest/api/media/streamingendpoints)  

Para obter informações detalhadas sobre as saídas ao vivo, consulte [utilizando um DVR em nuvem](live-event-cloud-dvr.md).

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

Consulte o artigo [perguntas frequentes.](frequently-asked-questions.md#live-streaming)

## <a name="ask-questions-and-get-updates"></a>Faça perguntas e obtenha atualizações

Consulte o artigo da [comunidade Azure Media Services](media-services-community.md) para ver diferentes formas de fazer perguntas, dar feedback e obter atualizações sobre os Media Services.

## <a name="next-steps"></a>Passos seguintes

[Tutorial de streaming ao vivo](stream-live-tutorial-with-api.md)
