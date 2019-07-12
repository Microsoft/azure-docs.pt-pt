---
title: Live streaming conceitos nos serviços de multimédia do Azure - eventos ao vivo e saídas de Live | Documentos da Microsoft
description: Este artigo fornece uma descrição geral dos conceitos de transmissão em fluxo em direto em serviços de multimédia do Azure v3.
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
ms.date: 06/19/2019
ms.author: juliako
ms.openlocfilehash: a951ebd46335ad4639b8499283ddd30f13edd64e
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67605656"
---
# <a name="live-events-and-live-outputs"></a>Eventos em Direto e Saídas em Direto

Serviços de multimédia do Azure permite-lhe fornecer eventos em direto aos seus clientes na cloud do Azure. Para configurar seus eventos de transmissão em fluxo em direto dos serviços de multimédia v3, terá de compreender os conceitos discutidos neste artigo.

> [!TIP]
> Para os clientes a migrar a partir dos serviços de multimédia v2 APIs, o **evento em direto** entidade substitui **canal** no v2 e **Live saída** substitui **programa**.

## <a name="live-events"></a>Eventos em Direto

Os [Eventos em Direto](https://docs.microsoft.com/rest/api/media/liveevents) são responsáveis pela ingestão e o processamento dos feeds de vídeos em direto. Quando cria um evento em direto, é criado um ponto de final entrado primário e secundário que pode utilizar para enviar um sinal ao vivo a partir de um codificador remoto. O codificador em direto remoto envia a contribuição do feed que o ponto final através de entrada a [RTMP](https://www.adobe.com/devnet/rtmp.html) ou [Smooth Streaming](https://msdn.microsoft.com/library/ff469518.aspx) protocolo de entrada (MP4 fragmentado). Protocolo de ingestão de RTMP, o conteúdo pode ser enviado de forma (`rtmp://`) ou encriptado em segurança na conexão (`rtmps://`). Para a transmissão em fluxo uniforme de protocolo de ingestão, os esquemas de URL suportados são `http://` ou `https://`.  

## <a name="live-event-types"></a>Tipos de evento em direto

R [evento em direto](https://docs.microsoft.com/rest/api/media/liveevents) pode ser um dos dois tipos: codificação de pass-through e em direto. Os tipos são definidos durante a criação usando [LiveEventEncodingType](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventencodingtype):

* **LiveEventEncodingType.None** -um codificador em direto no local envia um fluxo de velocidade de transmissão múltiplas. As transmissões em fluxo passa por meio do evento em direto sem qualquer processamento adicional. 
* **LiveEventEncodingType.Standard** – um codificador em direto envia um fluxo de velocidade de transmissão única para o evento em direto e os serviços de multimédia cria vários fluxos de velocidade de transmissão no local. Se o feed de contribuição é da 720p ou resolução superior, o **Default720p** configuração predefinida será codificar um conjunto de pares de velocidades de transmissão/resolução 6.
* **LiveEventEncodingType.Premium1080p** – um codificador em direto envia um fluxo de velocidade de transmissão única para o evento em direto e os serviços de multimédia cria vários fluxos de velocidade de transmissão no local. A configuração predefinida de Default1080p Especifica o conjunto de saída de pares de resolução/velocidades de transmissão. 

### <a name="pass-through"></a>Pass-through

![pass-through](./media/live-streaming/pass-through.svg)

Quando utilizar o pass-through **Evento em Direto**, conta com o codificador em direto no local para gerar uma transmissão em fluxo de vídeo de velocidade múltipla e enviá-la como um feed de contribuição para o Evento em Direto (através do protocolo RTMP ou MP4 fragmentado). O Evento em Direto realiza as transmissões em fluxo de vídeo de entrada sem qualquer processamento adicional. Tal um pass-through evento em direto é otimizado para eventos em direto de longa execução ou transmissão em direto lineares 24 x 365. Quando criar este tipo de Evento em Direto, especifique None (LiveEventEncodingType.None).

Pode enviar o feed de contribuição em resoluções até 4 K e a uma velocidade de 60 fotogramas/segundo, com os codecs de vídeo H.264/AVC ou H.265/HEVC e o codec de áudio AAC (AAC-LC, HE-AACv1 ou HE-AACv2).  Veja o artigo [Live Event types comparison](live-event-types-comparison.md) (Comparação de tipos de Eventos em Direto) para obter mais detalhes.

> [!NOTE]
> A utilização de um método pass-through é a forma mais económica de realizar uma transmissão em fluxo em direto quando estiver a realizar vários eventos durante um longo período de tempo e já investiu em codificadores no local. Consulte os detalhes dos [preços](https://azure.microsoft.com/pricing/details/media-services/).
> 

Ver um exemplo de código do .NET no [MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126).

### <a name="live-encoding"></a>Live Encoding  

![codificação em direto](./media/live-streaming/live-encoding.svg)

Ao utilizar a codificação em direto com os Serviços de Multimédia, configuraria o codificador em direto no local para enviar um vídeo de velocidade de transmissão única como o feed de contribuição para o Evento em Direto (através dos protocolos RTMP ou Fragmented-Mp4.) Poderia, em seguida, configurar um evento em direto, de modo que ele codifica essa entrada velocidade de transmissão única transmitir para um [vários transmissão em fluxo vídeo](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)e disponibiliza a saída para entrega ao reproduzir os dispositivos através de protocolos como MPEG-DASH, HLS e a facilitar Transmissão em fluxo.

Quando usa a codificação em direto, pode enviar a contribuição feed apenas em resoluções à resolução de 1080p numa taxa de quadros de 30 quadros/segundo, com o codec de vídeo H.264/AVC e AAC (AAC-LC, HE-AACv1 ou HE-AACv2) codec de áudio. Tenha em atenção que o pass-through eventos em direto pode suportar as resoluções até 4K em 60 quadros por segundo. Veja o artigo [Live Event types comparison](live-event-types-comparison.md) (Comparação de tipos de Eventos em Direto) para obter mais detalhes.

As resoluções e velocidades de transmissão contidas na saída do codificador em direto é determinado pela configuração predefinida. Se utilizar um **padrão** live encoder (LiveEventEncodingType.Standard), em seguida, o *Default720p* configuração predefinida especifica um conjunto de pares de taxa de bits/resolução 6, do 720p em 3.5Mbps até 192 p em 200 kbps. Caso contrário, se utilizar um **Premium1080p** live encoder (LiveEventEncodingType.Premium1080p), em seguida, o *Default1080p* configuração predefinida especifica um conjunto de pares de taxa de bits/resolução 6, do 1080p em 3.5Mbps para baixo até 180 p em 200 kbps. Para obter informações, veja [System presets](live-event-types-comparison.md#system-presets) (Predefinições do sistema).

> [!NOTE]
> Se precisar de personalizar a predefinição de codificação em direto, abra um pedido de suporte através do portal do Azure. Deve especificar a tabela de resolução e velocidades de transmissão pretendida. Certifique-se de que existe apenas uma camada de 720p (se a pedir uma configuração predefinida de um codificador em direto Standard) ou em 1080p (se a pedir uma configuração predefinida de um codificador em direto Premium1080p) e no máximo, 6 camadas.

## <a name="live-event-creation-options"></a>Opções de criação de evento em direto

Ao criar um evento em direto, pode especificar as seguintes opções:

* O protocolo de transmissão em fluxo para o evento em direto (atualmente, são suportados os protocolos RTMP e Smooth Streaming).<br/>Não é possível alterar a opção de protocolo enquanto o evento em direto ou suas saídas associadas em direto estão em execução. Se necessitar de protocolos diferentes, deve criar evento Live separado para cada protocolo de transmissão em fluxo.  
* Restrições de IP na ingestão e na pré-visualização. Pode definir os endereços IP que estão autorizados a ingerir um vídeo para este evento em direto. Os endereços IP permitidos podem ser especificados como um endereço IP único (por exemplo "10.0.0.1"), um intervalo de IP com um endereço IP e uma máscara de sub-rede CIDR (por exemplo, ' 10.0.0.1/22') ou um intervalo de IP com um endereço IP e uma máscara de sub-rede de ponto decimal (por exemplo , ' 10.0.0.1(255.255.252.0)').<br/>Se não for especificado qualquer endereço IP e se não existir nenhuma definição de regra, não será permitido nenhum endereço IP. Para permitir um endereço IP, crie uma regra e defina 0.0.0.0/0.<br/>Os endereços IP tem de estar em um dos seguintes formatos: Endereço IpV4 com 4 números, o intervalo de endereços CIDR.
* Ao criar o evento, poderá especificar o início automático do mesmo. <br/>Quando o início automático está definido como true, o evento em direto será iniciado após a criação. A faturação tem início assim que o evento Live começa a ser executado. Tem de chamar explicitamente Stop do recurso de evento em direto para parar a faturação ainda mais. Em alternativa, pode iniciar o evento quando estiver pronto para começar a transmissão em fluxo. 

    Para obter mais informações, consulte [Estados de evento em direto e de faturação](live-event-states-billing.md).

## <a name="live-event-ingest-urls"></a>URLs de inserção de evento em direto

Quando o Evento em Direto estiver criado, pode obter os URLs de ingestão que vai fornecer ao codificador em direto no local. O codificador em direto utiliza esses URLs para introduzir uma transmissão um fluxo direto. Para obter mais informações, consulte [recomendado codificadores em direto no local](recommended-on-premises-live-encoders.md). 

Pode utilizar URLs intuitivos ou não intuitivos. 

> [!NOTE] 
> Para um URL de inserção ser preditiva, defina o modo de "personalizado".

* URL de não intuitivos

    URL personalizado não é o modo predefinido nos serviços de multimédia v3. Potencialmente, recebe o Evento em Direto mais depressa, embora o URL de ingestão só seja conhecido quando esse evento é iniciado. Se parar/iniciar o Evento em Direto, o URL mudará. <br/>Os URLs não intuitivos são úteis em cenários nos quais o utilizador final quer utilizar uma aplicação para transmitir em fluxo e essa aplicação pretende receber um evento em direto depressa e em que ter um URL de ingestão dinâmica não é problema.
    
    Se não precisar de uma aplicação de cliente para gerar previamente um URL de ingestão antes do evento Live é criado, apenas permitir que os serviços de multimédia para gerar automaticamente o Token de acesso para o evento em direto.
* URL personalizado

    O modo intuitivo é o preferido das grandes empresas de transmissão que utilizam codificadores de transmissão de hardware e não querem reconfigurar os codificadores quando iniciam o Evento em Direto. Essas empresas querem um URL de ingestão preditivo, que não mude ao longo do tempo.
    
    Para especificar neste modo, defina `vanityUrl` para `true` no momento de criação (a predefinição é `false`). Também tem de transmitir o seu token de acesso (`LiveEventInput.accessToken`) no momento da criação. Especifique o valor do token para evitar um token aleatório no URL. O token de acesso tem de ser uma cadeia GUID válida (com ou sem os hífenes). Assim que o modo é definido não pode ser atualizada.

    O token de acesso tem de ser exclusivo no seu centro de dados. Se a sua aplicação tem de utilizar um URL personalizado, recomenda-se sempre criar uma nova instância GUID para o token de acesso (em vez de reutilizar qualquer GUID existente). 

    Utilizar as APIs seguintes para ativar o URL de intuitivos e definir o token de acesso para um GUID válido (por exemplo `"accessToken": "1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"`).  
    
    |Idioma|Ativar o URL personalizado|Definir o token de acesso|
    |---|---|---|
    |REST|[properties.vanityUrl](https://docs.microsoft.com/rest/api/media/liveevents/create#liveevent)|[LiveEventInput.accessToken](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventinput)|
    |CLI|[--vanity-url](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest#az-ams-live-event-create)|[--access-token](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest#optional-parameters)|
    |.NET|[LiveEvent.VanityUrl](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveevent.vanityurl?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_LiveEvent_VanityUrl)|[LiveEventInput.AccessToken](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveeventinput.accesstoken?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_LiveEventInput_AccessToken)|
    
### <a name="live-ingest-url-naming-rules"></a>Regras de nomenclatura de URL de ingestão em direto

* A cadeia *aleatória* abaixo é um número hexadecimal de 128 bits (que é composto por 32 carateres de 0-9 e a-f).
* *o token de acesso* -a cadeia de caracteres do GUID válida definida ao usar o modo de intuitivos. Por exemplo, `"1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"`.
* *o nome do Stream* -indica o nome de fluxo para uma ligação específica. O valor de nome de fluxo, normalmente, é adicionado pelo codificador em direto que utilizar. Pode configurar o codificador em direto para utilizar qualquer nome para descrever a ligação, por exemplo: "video1_audio1", "video2_audio1", "stream".

#### <a name="non-vanity-url"></a>URL de não intuitivos

##### <a name="rtmp"></a>RTMP

`rtmp://<random 128bit hex string>.channel.media.azure.net:1935/live/<auto-generated access token>/<stream name>`<br/>
`rtmp://<random 128bit hex string>.channel.media.azure.net:1936/live/<auto-generated access token>/<stream name>`<br/>
`rtmps://<random 128bit hex string>.channel.media.azure.net:2935/live/<auto-generated access token>/<stream name>`<br/>
`rtmps://<random 128bit hex string>.channel.media.azure.net:2936/live/<auto-generated access token>/<stream name>`<br/>

##### <a name="smooth-streaming"></a>Transmissão em Fluxo Uniforme

`http://<random 128bit hex string>.channel.media.azure.net/<auto-generated access token>/ingest.isml/streams(<stream name>)`<br/>
`https://<random 128bit hex string>.channel.media.azure.net/<auto-generated access token>/ingest.isml/streams(<stream name>)`<br/>

#### <a name="vanity-url"></a>URL personalizado

##### <a name="rtmp"></a>RTMP

`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1935/live/<your access token>/<stream name>`<br/>
`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1936/live/<your access token>/<stream name>`<br/>
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2935/live/<your access token>/<stream name>`<br/>
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2936/live/<your access token>/<stream name>`<br/>

##### <a name="smooth-streaming"></a>Transmissão em Fluxo Uniforme

`http://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<your access token>/ingest.isml/streams(<stream name>)`<br/>
`https://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<your access token>/ingest.isml/streams(<stream name>)`<br/>

## <a name="live-event-preview-url"></a>URL de pré-visualização de eventos em direto

Depois do evento Live começa a receber a contribuição de feed, pode utilizar o seu ponto de extremidade de pré-visualização para pré-visualizar e validar que estão a receber a transmissão em direto antes de publicar ainda mais. Depois de ter verificado que o fluxo de pré-visualização é bom, pode utilizar o evento em direto para que a transmissão em direto disponíveis para entrega através de um ou mais (previamente criada) de transmissão em fluxo pontos finais. Para tal, crie um novo [Live saída](https://docs.microsoft.com/rest/api/media/liveoutputs) no evento em direto. 

> [!IMPORTANT]
> Certifique-se de que o vídeo está a ser encaminhados para o URL de pré-visualização antes de continuar!

## <a name="live-event-long-running-operations"></a>Operações de longa execução em direto do evento

Para obter detalhes, consulte [operações de longa execução](media-services-apis-overview.md#long-running-operations)

## <a name="live-outputs"></a>Saídas em Direto

Assim que tiver o fluxo a ser encaminhados para o evento em direto, pode começar o evento de transmissão em fluxo através da criação de um [Asset](https://docs.microsoft.com/rest/api/media/assets), [Live saída](https://docs.microsoft.com/rest/api/media/liveoutputs), e [localizador de transmissão em fluxo](https://docs.microsoft.com/rest/api/media/streaminglocators). Saída em direto irá arquivar a transmissão e disponibilizá-la para os espetadores através da [ponto final de transmissão em fluxo](https://docs.microsoft.com/rest/api/media/streamingendpoints).  

> [!NOTE]
> Início de saídas após a criação do Live e param quando eliminado. Ao eliminar a saída em direto, não serão eliminados os ativos e conteúdo no elemento subjacente. 

A relação entre um **evento em direto** e a respetiva **Live saídas** é semelhante a televisão tradicional de difusão, por meio das quais um canal (evento em direto) representa um fluxo constante de vídeo e uma gravação (Live Saída) é confinada para um segmento de tempo específico (por exemplo, noite notícias da 6 17:30, para as 19:00: 00). Pode gravar programas de televisão com um gravador de vídeo Digital (DVR) – a funcionalidade equivalente nos eventos em direto é gerenciada através da **archiveWindowLength** propriedade. É uma duração de período de tempo de ISO-8601 (por exemplo, PTHH:MM:SS), que especifica a capacidade do DVR e pode ser definida a partir de um mínimo de 3 minutos até um máximo de 25 horas.

O objeto de saída em direto é como um gravador de banda que detectará e o registo à transmissão em direto para um elemento na sua conta de Media Services. O conteúdo gravado será persistido para a conta de armazenamento do Azure anexada à sua conta, no contêiner definido pelo recurso do recurso. A saída do Live também permite-lhe controlar algumas propriedades do fluxo em direto saída, como muito da transmissão em fluxo é mantida na gravação do arquivo (por exemplo, a capacidade de DVR na cloud) e se é ou não podem iniciar os visualizadores assistem à transmissão em direto. O arquivo no disco é um arquivo circular "janela" que contém apenas a quantidade de conteúdo especificado na propriedade archiveWindowLength da saída em direto. Conteúdo que se encontre fora essa janela é descartado automaticamente do contentor de armazenamento e não é recuperável. Pode criar várias saídas em direto (até três máximo) num evento em direto com definições e comprimentos de arquivo diferente.  

Se tiver publicado a saída em direto **Asset** usando um **localizador de transmissão em fluxo**, o evento em direto (até o tamanho de janela DVR) irá continuar a ser visualizado até a expiração ou a eliminação, o localizador de transmissão em fluxo o que ocorrer primeiro.

Para obter mais informações, consulte [usando um DVR na cloud](live-event-cloud-dvr.md).

## <a name="ask-questions-give-feedback-get-updates"></a>Faça perguntas, comentários, obter atualizações

Veja a [Comunidade dos serviços de multimédia do Azure](media-services-community.md) artigo para ver formas diferentes, pode fazer perguntas, comentários e obter atualizações sobre os serviços de multimédia.

## <a name="next-steps"></a>Passos Seguintes

[Tutorial de transmissão em fluxo em direto](stream-live-tutorial-with-api.md)
