---
title: Conceitos de transmissão ao vivo nos serviços de mídia do Azure-eventos ao vivo e saídas ao vivo | Microsoft Docs
description: Este artigo fornece uma visão geral dos conceitos de transmissão ao vivo nos serviços de mídia do Azure v3.
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
ms.date: 09/25/2019
ms.author: juliako
ms.openlocfilehash: 7cb158490bd8a8520e101dbe321b8594cad059f9
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/26/2019
ms.locfileid: "71309671"
---
# <a name="live-events-and-live-outputs"></a>Eventos em Direto e Saídas em Direto

Os serviços de mídia do Azure permitem que você forneça eventos ao vivo para seus clientes na nuvem do Azure. Para configurar seus eventos de transmissão ao vivo nos serviços de mídia v3, você precisa entender os conceitos discutidos neste artigo.

> [!TIP]
> Para clientes que estão migrando das APIs do Media Services v2, a entidade de **evento ao vivo** substitui o **canal** em v2 e a **saída ao vivo** substitui o **programa**.

## <a name="live-events"></a>Eventos ao Vivo

Os [Eventos em Direto](https://docs.microsoft.com/rest/api/media/liveevents) são responsáveis pela ingestão e o processamento dos feeds de vídeos em direto. Quando você cria um evento ao vivo, é criado um ponto de extremidade de entrada primário e secundário que você pode usar para enviar um sinal ao vivo de um codificador remoto. O codificador remoto dinâmico envia o feed de contribuição para esse ponto de extremidade de entrada usando o protocolo de entrada [RTMP](https://www.adobe.com/devnet/rtmp.html) ou [Smooth streaming](https://msdn.microsoft.com/library/ff469518.aspx) (fragmentado-MP4). Para o protocolo de ingestão RTMP, o conteúdo pode ser enviado em Clear`rtmp://`() ou criptografado com segurança na conexão`rtmps://`(). Para o Smooth streaming protocolo de ingestão, os esquemas de `http://` URL `https://`com suporte são ou.  

## <a name="live-event-types"></a>Tipos de eventos ao vivo

Um [evento ao vivo](https://docs.microsoft.com/rest/api/media/liveevents) pode ser um dos dois tipos: codificação ativa e de passagem. Os tipos são definidos durante a criação usando [LiveEventEncodingType](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventencodingtype):

* **LiveEventEncodingType. None** -um codificador ao vivo local envia um fluxo de taxas de bits múltiplas. Os fluxos ingeridos passam pelo evento ao vivo sem nenhum processamento adicional. 
* **LiveEventEncodingType. Standard** -um codificador ao vivo local envia um fluxo de taxa de bits única para o evento ao vivo e os serviços de mídia criam fluxos de taxas de bits múltiplas. Se o feed de contribuição for de 720p ou de resolução superior, a predefinição de **default720p** codificará um conjunto de 6 pares de resolução/taxa de bits.
* **LiveEventEncodingType. Premium1080p** -um codificador ao vivo local envia um fluxo de taxa de bits única para o evento ao vivo e os serviços de mídia criam fluxos de taxas de bits múltiplas. A predefinição de default1080p especifica o conjunto de saída de pares de resolução/taxa de bits. 

### <a name="pass-through"></a>Pass-through

![pass-through](./media/live-streaming/pass-through.svg)

Quando utilizar o pass-through **Evento em Direto**, conta com o codificador em direto no local para gerar uma transmissão em fluxo de vídeo de velocidade múltipla e enviá-la como um feed de contribuição para o Evento em Direto (através do protocolo RTMP ou MP4 fragmentado). O Evento em Direto realiza as transmissões em fluxo de vídeo de entrada sem qualquer processamento adicional. Esse evento ao vivo de passagem é otimizado para eventos ao vivo de execução longa ou transmissão ao vivo linear 24x365. Quando criar este tipo de Evento em Direto, especifique None (LiveEventEncodingType.None).

Pode enviar o feed de contribuição em resoluções até 4 K e a uma velocidade de 60 fotogramas/segundo, com os codecs de vídeo H.264/AVC ou H.265/HEVC e o codec de áudio AAC (AAC-LC, HE-AACv1 ou HE-AACv2).  Veja o artigo [Live Event types comparison](live-event-types-comparison.md) (Comparação de tipos de Eventos em Direto) para obter mais detalhes.

> [!NOTE]
> A utilização de um método pass-through é a forma mais económica de realizar uma transmissão em fluxo em direto quando estiver a realizar vários eventos durante um longo período de tempo e já investiu em codificadores no local. Consulte os detalhes dos [preços](https://azure.microsoft.com/pricing/details/media-services/).
> 

Consulte um exemplo de código .NET em [MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126).

### <a name="live-encoding"></a>Live Encoding  

![codificação em direto](./media/live-streaming/live-encoding.svg)

Ao utilizar a codificação em direto com os Serviços de Multimédia, configuraria o codificador em direto no local para enviar um vídeo de velocidade de transmissão única como o feed de contribuição para o Evento em Direto (através dos protocolos RTMP ou Fragmented-Mp4.) Em seguida, você configuraria um evento ao vivo para que ele codificasse o fluxo de taxa de bits única de entrada em um [fluxo de vídeo de várias taxas de bits](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)e disponibilizará a saída para a entrega a fim de reproduzir dispositivos por meio de protocolos como MPEG-Dash, HLS e Smooth streaming.

Ao usar a codificação ativa, você pode enviar o feed de contribuição somente em resoluções até a resolução de uma taxa de quadros de 30 quadros/segundo, com H. 264/AVC Video Codec e o codec de áudio AAC (AAC-LC, HE-AACv1 ou HE-AACv2). Observe que os eventos ao vivo de passagem podem dar suporte a resoluções de até 4K em 60 quadros/segundo. Veja o artigo [Live Event types comparison](live-event-types-comparison.md) (Comparação de tipos de Eventos em Direto) para obter mais detalhes.

As resoluções e taxas de bits contidas na saída do codificador ao vivo são determinadas pela predefinição. Se você estiver usando um codificador ativo **padrão** (LiveEventEncodingType. Standard), a predefinição *default720p* especificará um conjunto de 6 pares de taxa de resolução/bit, indo de 720p a 3,5 mbps para 192P a 200 Kbps. Caso contrário, se estiver usando um codificador **Premium1080p** Live (LiveEventEncodingType. Premium1080p), a predefinição de *default1080p* especificará um conjunto de 6 pares de taxa de resolução/bit, indo de 1080p a 3,5 mbps para 180p a 200 Kbps. Para obter informações, veja [System presets](live-event-types-comparison.md#system-presets) (Predefinições do sistema).

> [!NOTE]
> Se você precisar personalizar a predefinição de codificação ativa, abra um tíquete de suporte via portal do Azure. Deve especificar a tabela de resolução e velocidades de transmissão pretendida. Verifique se há apenas uma camada em 720p (se solicitar uma predefinição para um codificador ao vivo padrão) ou em 1080p (se solicitar uma predefinição para um codificador ao vivo Premium1080p) e no máximo 6 camadas.

## <a name="live-event-creation-options"></a>Opções de criação de eventos ao vivo

Ao criar um evento ao vivo, você pode especificar as seguintes opções:

* O protocolo de streaming para o evento ao vivo (atualmente, há suporte para os protocolos RTMP e Smooth Streaming).<br/>Você não pode alterar a opção de protocolo enquanto o evento ao vivo ou suas saídas dinâmicas associadas estiverem em execução. Se você precisar de protocolos diferentes, deverá criar um evento ao vivo separado para cada protocolo de streaming.  
* Ao criar o evento, poderá especificar o início automático do mesmo. <br/>Quando AutoStart é definido como true, o evento ao vivo será iniciado após a criação. A cobrança começa assim que o evento ao vivo começa a ser executado. Você deve chamar Stop explicitamente no recurso de evento ao vivo para interromper mais cobranças. Como alternativa, você pode iniciar o evento quando estiver pronto para iniciar o streaming. 

    Para obter mais informações, consulte [Live Event States e Billing](live-event-states-billing.md).
* Restrições de IP na ingestão e na pré-visualização. Você pode definir os endereços IP que têm permissão para ingerir um vídeo para esse evento ao vivo. Os endereços IP permitidos podem ser especificados como um endereço IP único (por exemplo "10.0.0.1"), um intervalo de IP com um endereço IP e uma máscara de sub-rede CIDR (por exemplo, ' 10.0.0.1/22') ou um intervalo de IP com um endereço IP e uma máscara de sub-rede de ponto decimal (por exemplo , ' 10.0.0.1(255.255.252.0)').<br/>Se não for especificado qualquer endereço IP e se não existir nenhuma definição de regra, não será permitido nenhum endereço IP. Para permitir um endereço IP, crie uma regra e defina 0.0.0.0/0.<br/>Os endereços IP devem estar em um dos seguintes formatos: Endereço IpV4 com 4 números, intervalo de endereços CIDR.

    Se você quiser habilitar determinados IPs em seus próprios firewalls ou se quiser restringir as entradas aos seus eventos ao vivo para os endereços IP do Azure, baixe um arquivo JSON dos [intervalos de endereços IP do datacenter do Azure](https://www.microsoft.com/download/details.aspx?id=41653). Para obter detalhes sobre esse arquivo, clique na seção **detalhes** na página.
    
## <a name="live-event-ingest-urls"></a>URLs de ingestão de eventos ao vivo

Quando o Evento em Direto estiver criado, pode obter os URLs de ingestão que vai fornecer ao codificador em direto no local. O codificador em direto utiliza esses URLs para introduzir uma transmissão um fluxo direto. Para obter mais informações, consulte [codificadores ativos locais recomendados](recommended-on-premises-live-encoders.md). 

Pode utilizar URLs intuitivos ou não intuitivos. 

> [!NOTE] 
> Para que uma URL de ingestão seja preditiva, defina o modo "intuitivo".

* URL não intuitivo

    A URL não intuitivo é o modo padrão no Media Services V3. Potencialmente, recebe o Evento em Direto mais depressa, embora o URL de ingestão só seja conhecido quando esse evento é iniciado. Se parar/iniciar o Evento em Direto, o URL mudará. <br/>Os URLs não intuitivos são úteis em cenários nos quais o utilizador final quer utilizar uma aplicação para transmitir em fluxo e essa aplicação pretende receber um evento em direto depressa e em que ter um URL de ingestão dinâmica não é problema.
    
    Se um aplicativo cliente não precisar gerar previamente uma URL de ingestão antes que o evento ao vivo seja criado, basta permitir que os serviços de mídia gerem automaticamente o token de acesso para o evento ao vivo.
* URL do intuitivo

    O modo intuitivo é o preferido das grandes empresas de transmissão que utilizam codificadores de transmissão de hardware e não querem reconfigurar os codificadores quando iniciam o Evento em Direto. Essas empresas querem um URL de ingestão preditivo, que não mude ao longo do tempo.
    
    Para especificar esse modo, defina `vanityUrl` como `true` no momento da criação (o padrão `false`é). Você também precisa passar seu próprio token de acesso (`LiveEventInput.accessToken`) no momento da criação. Especifique o valor do token para evitar um token aleatório na URL. O token de acesso deve ser uma cadeia de caracteres GUID válida (com ou sem os hifens). Depois que o modo for definido, ele não poderá ser atualizado.

    O token de acesso precisa ser exclusivo em seu data center. Se seu aplicativo precisar usar uma URL intuitivo, é recomendável sempre criar uma nova instância de GUID para seu token de acesso (em vez de reutilizar qualquer GUID existente). 

    Use as seguintes APIs para habilitar a URL intuitivo e definir o token de acesso para um GUID válido (por `"accessToken": "1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"`exemplo).  
    
    |Idioma|Habilitar URL intuitivo|Definir o token de acesso|
    |---|---|---|
    |REST|[properties.vanityUrl](https://docs.microsoft.com/rest/api/media/liveevents/create#liveevent)|[LiveEventInput.accessToken](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventinput)|
    |CLI|[--vanity-url](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest#az-ams-live-event-create)|[--access-token](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest#optional-parameters)|
    |.NET|[LiveEvent.VanityUrl](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveevent.vanityurl?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_LiveEvent_VanityUrl)|[LiveEventInput.AccessToken](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveeventinput.accesstoken?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_LiveEventInput_AccessToken)|
    
### <a name="live-ingest-url-naming-rules"></a>Regras de nomenclatura de URL de ingestão dinâmica

* A cadeia *aleatória* abaixo é um número hexadecimal de 128 bits (que é composto por 32 carateres de 0-9 e a-f).
* *seu token de acesso* -a cadeia de caracteres GUID válida que você define ao usar o modo intuitivo. Por exemplo, `"1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"`.
* *nome do fluxo* – indica o nome do fluxo para uma conexão específica. O valor do nome do fluxo geralmente é adicionado pelo codificador ao vivo que você usa. Você pode configurar o codificador ao vivo para usar qualquer nome para descrever a conexão, por exemplo: "video1_audio1", "video2_audio1", "Stream".

#### <a name="non-vanity-url"></a>URL não intuitivo

##### <a name="rtmp"></a>RTMP

`rtmp://<random 128bit hex string>.channel.media.azure.net:1935/live/<auto-generated access token>/<stream name>`<br/>
`rtmp://<random 128bit hex string>.channel.media.azure.net:1936/live/<auto-generated access token>/<stream name>`<br/>
`rtmps://<random 128bit hex string>.channel.media.azure.net:2935/live/<auto-generated access token>/<stream name>`<br/>
`rtmps://<random 128bit hex string>.channel.media.azure.net:2936/live/<auto-generated access token>/<stream name>`<br/>

##### <a name="smooth-streaming"></a>Transmissão em Fluxo Uniforme

`http://<random 128bit hex string>.channel.media.azure.net/<auto-generated access token>/ingest.isml/streams(<stream name>)`<br/>
`https://<random 128bit hex string>.channel.media.azure.net/<auto-generated access token>/ingest.isml/streams(<stream name>)`<br/>

#### <a name="vanity-url"></a>URL do intuitivo

##### <a name="rtmp"></a>RTMP

`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1935/live/<your access token>/<stream name>`<br/>
`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1936/live/<your access token>/<stream name>`<br/>
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2935/live/<your access token>/<stream name>`<br/>
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2936/live/<your access token>/<stream name>`<br/>

##### <a name="smooth-streaming"></a>Transmissão em Fluxo Uniforme

`http://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<your access token>/ingest.isml/streams(<stream name>)`<br/>
`https://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<your access token>/ingest.isml/streams(<stream name>)`<br/>

## <a name="live-event-preview-url"></a>URL de visualização de eventos ao vivo

Depois que o evento ao vivo começar a receber o feed de contribuição, você poderá usar seu ponto de extremidade de visualização para visualizar e validar que você está recebendo a transmissão ao vivo antes da publicação adicional. Depois de verificar se o fluxo de visualização é bom, você pode usar o evento ao vivo para disponibilizar o Live Stream para entrega por meio de um ou mais pontos de extremidade de streaming (pré-criados). Para fazer isso, você cria uma nova [saída ao vivo](https://docs.microsoft.com/rest/api/media/liveoutputs) no evento ao vivo. 

> [!IMPORTANT]
> Verifique se o vídeo está fluindo para a URL de visualização antes de continuar!

## <a name="live-event-long-running-operations"></a>Operações de longa execução de evento ao vivo

Para obter detalhes, consulte [operações de execução longa](media-services-apis-overview.md#long-running-operations)

## <a name="live-outputs"></a>Saídas em Direto

Depois que o fluxo fluir para o evento ao vivo, você poderá iniciar o evento de streaming criando um [ativo](https://docs.microsoft.com/rest/api/media/assets), uma [saída ao vivo](https://docs.microsoft.com/rest/api/media/liveoutputs)e um [localizador de streaming](https://docs.microsoft.com/rest/api/media/streaminglocators). A saída ao vivo arquivará o fluxo e o tornará disponível para os visualizadores por meio do [ponto de extremidade de streaming](https://docs.microsoft.com/rest/api/media/streamingendpoints).  

Para obter informações detalhadas sobre as saídas ao vivo, consulte [usando um DVR de nuvem](live-event-cloud-dvr.md).

## <a name="ask-questions-give-feedback-get-updates"></a>Faça perguntas, envie comentários, obtenha atualizações

Confira o artigo [da Comunidade dos serviços de mídia do Azure](media-services-community.md) para ver diferentes maneiras que você pode fazer perguntas, fornecer comentários e obter atualizações sobre os serviços de mídia.

## <a name="next-steps"></a>Passos seguintes

[Tutorial de transmissão ao vivo](stream-live-tutorial-with-api.md)
