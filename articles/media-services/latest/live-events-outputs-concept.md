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
ms.date: 03/01/2019
ms.author: juliako
ms.openlocfilehash: c446a71a363a9a81eeb7d0dddcdbd90ccee08b7d
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58189364"
---
# <a name="live-events-and-live-outputs"></a>Eventos em Direto e Saídas em Direto

Serviços de multimédia do Azure permite-lhe fornecer eventos em direto aos seus clientes na cloud do Azure. Para configurar seus eventos de transmissão em fluxo em direto dos serviços de multimédia v3, precisa entender os conceitos discutidos neste artigo:

* [Eventos em direto](#live-events)
* Tipos de evento em direto
* Comparação de tipos de evento em direto
* [Opções de criação de evento em direto](#live-event-creation-options)
* [URLs de inserção de evento em direto](#live-event-ingest-urls)
* [URL de pré-visualização de eventos em direto](#live-event-preview-url)
* [Live saídas](#live-outputs).

## <a name="live-events"></a>Eventos em Direto

[Eventos em direto](https://docs.microsoft.com/rest/api/media/liveevents) são responsáveis por ingerir e processar os feeds de vídeo em direto. Quando cria um evento em direto, um ponto de final de entrada é criado a que pode utilizar para enviar um sinal ao vivo a partir de um codificador remoto. O codificador em direto remoto envia a contribuição do feed que o ponto final através de entrada a [RTMP](https://www.adobe.com/devnet/rtmp.html) ou [Smooth Streaming](https://msdn.microsoft.com/library/ff469518.aspx) protocol (MP4 fragmentado). Para a transmissão em fluxo uniforme de protocolo de ingestão, os esquemas de URL suportados são `http://` ou `https://`. Para o RTMP de protocolo de ingestão, os esquemas de URL suportados são `rtmp://` ou `rtmps://`. 

## <a name="live-event-types"></a>Tipos de evento em direto

R [evento em direto](https://docs.microsoft.com/rest/api/media/liveevents) pode ser um dos dois tipos: codificação de pass-through e em direto. 

### <a name="pass-through"></a>Pass-through

![pass-through](./media/live-streaming/pass-through.svg)

Ao utilizar o pass-through **evento em direto**, contar com o codificador em direto de locais para gerar um fluxo de vídeo de velocidade de transmissão múltiplas e enviar como a contribuição feed para o evento em direto (usando o protocolo RTMP ou MP4 fragmentado). O evento em direto, em seguida, continua até os fluxos de vídeo de entrada sem qualquer processamento adicional. Tal um LiveEvent pass-through está otimizado para eventos em direto de longa execução ou transmissão em direto lineares 24 x 365. Ao criar este tipo de evento em direto, especifique nenhum (LiveEventEncodingType.None).

Pode enviar a contribuição de feed em resoluções até 4 K e num quadro de taxa de 60 quadros por segundo, com H.264/AVC ou H.265/HEVC codecs de vídeo e AAC (AAC-LC, HE-AACv1 ou HE-AACv2) codec de áudio.  Consulte a [comparação de tipos de evento em direto](live-event-types-comparison.md) artigo para obter mais detalhes.

> [!NOTE]
> A utilização de um método pass-through é a forma mais económica de realizar uma transmissão em fluxo em direto quando estiver a realizar vários eventos durante um longo período de tempo e já investiu em codificadores no local. Consulte os detalhes dos [preços](https://azure.microsoft.com/pricing/details/media-services/).
> 

Ver um exemplo de código do .NET no [MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126).

### <a name="live-encoding"></a>Live Encoding  

![codificação do Live](./media/live-streaming/live-encoding.svg)

Ao utilizar o live encoding com Media Services, poderia configurar a seu codificador em direto de locais para enviar uma velocidade de transmissão única vídeo como a contribuição de feed para o evento em direto (usando o protocolo RTMP ou Mp4 fragmentado). O evento Live codifica essa entrada velocidade de transmissão única transmitir para um [vários transmissão em fluxo vídeo](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming), torna-o disponível para entrega ao reproduzir os dispositivos através de protocolos como MPEG-DASH, HLS e Smooth Streaming. Ao criar este tipo de evento em direto, especifique o tipo de codificação **padrão** (LiveEventEncodingType.Standard).

Pode enviar a contribuição de feed de cada até 1080 resolução numa taxa de quadros de 30 quadros/segundo, com o codec de vídeo H.264/AVC e AAC (AAC-LC, HE-AACv1 ou HE-AACv2) codec de áudio. Consulte a [comparação de tipos de evento em direto](live-event-types-comparison.md) artigo para obter mais detalhes.

Ao usar a codificação em tempo real (evento em direto é definido como **padrão**), a predefinição de codificação define como o fluxo de entrada é codificado em várias velocidades de transmissão ou camadas. Para obter informações, consulte [predefinições do sistema](live-event-types-comparison.md#system-presets).

> [!NOTE]
> Atualmente, o único permitido valor predefinido para o tipo de padrão de evento em direto é *Default720p*. Se precisar de utilizar uma predefinição de codificação em direto personalizada, entre em contato com amshelp@microsoft.com. Deve especificar a tabela desejada de resolução e velocidades de transmissão. Certifique-se de que existe apenas uma camada em 720p e no máximo, 6 camadas.

## <a name="live-event-creation-options"></a>Opções de criação de evento em direto

Ao criar um evento em direto, pode especificar as seguintes opções:

* O protocolo de transmissão em fluxo para o evento em direto (atualmente, são suportados os protocolos RTMP e Smooth Streaming).<br/>Não é possível alterar a opção de protocolo enquanto o evento em direto ou suas saídas associadas em direto estão em execução. Se necessitar de protocolos diferentes, deve criar evento Live separado para cada protocolo de transmissão em fluxo.  
* Restrições de IP na ingestão e na pré-visualização. Pode definir os endereços IP que estão autorizados a ingerir um vídeo para este evento em direto. Os endereços IP permitidos podem ser especificados como um endereço IP único (por exemplo "10.0.0.1"), um intervalo de IP com um endereço IP e uma máscara de sub-rede CIDR (por exemplo, ' 10.0.0.1/22') ou um intervalo de IP com um endereço IP e uma máscara de sub-rede de ponto decimal (por exemplo , ' 10.0.0.1(255.255.252.0)').<br/>Se não for especificado qualquer endereço IP e se não existir nenhuma definição de regra, não será permitido nenhum endereço IP. Para permitir um endereço IP, crie uma regra e defina 0.0.0.0/0.<br/>Os endereços IP tem de estar em um dos seguintes formatos: Endereço IpV4 com 4 números, o intervalo de endereços CIDR.
* Ao criar o evento, poderá especificar o início automático do mesmo. <br/>Quando o início automático está definido como true, o evento em direto será iniciado após a criação. A faturação tem início assim que o evento Live começa a ser executado. Tem de chamar explicitamente Stop do recurso de evento em direto para parar a faturação ainda mais. Em alternativa, pode iniciar o evento quando estiver pronto para começar a transmissão em fluxo. 

    Para obter mais informações, consulte [Estados de evento em direto e de faturação](live-event-states-billing.md).

## <a name="live-event-ingest-urls"></a>URLs de inserção de evento em direto

Depois de criar o evento em direto, pode obter URLs de inserção que fornecerá ao codificador em direto no local. O codificador em direto utiliza estes URLs para uma transmissão em direto de entrada. Para obter mais informações, consulte [recomendado codificadores em direto no local](recommended-on-premises-live-encoders.md). 

Pode utilizar URLs não intuitivos ou URLs intuitivos. 

* URL de não intuitivos

    URL personalizado não é o modo predefinido da v3 de AMS. Obter o evento Live rapidamente mas URL de ingestão potencialmente é conhecida apenas quando o evento em direto for iniciado. O URL será alterado se iniciar/parar o evento em direto. <br/>Não intuitivos é útil em cenários, quando um usuário final quer transmitir a utilizar uma aplicação, onde quer que a aplicação obter um evento ao vivo urgente e ter um URL de ingestão de dinâmico não é um problema.
* URL personalizado

    Modo de intuitivos é preferível pelo suporte de dados grandes emissores que utilizam o hardware codificadores de difusão e não quiser configurar novamente os seus codificadores durante o arranque do evento em direto. Eles querem uma previsão ingerir URL, que não se altera ao longo do tempo.

> [!NOTE] 
> Para um URL de inserção ser preditiva, terá de utilizar o modo de "personalizado" e transmitir o seu token de acesso (para evitar um token aleatório no URL).

### <a name="live-ingest-url-naming-rules"></a>Regras de nomenclatura de URL de ingestão em direto

O *aleatório* abaixo da cadeia de caracteres é um número hexadecimal de 128 bits (que é composto de 32 carateres de 0 a 9-f).<br/>
O *token de acesso* segue-se o que precisa especificar para URL fixo. Também é número hexadecimal de 128 bits.

#### <a name="non-vanity-url"></a>URL de não intuitivos

##### <a name="rtmp"></a>RTMP

`rtmp://<random 128bit hex string>.channel.media.azure.net:1935/<access token>`
`rtmp://<random 128bit hex string>.channel.media.azure.net:1936/<access token>`
`rtmps://<random 128bit hex string>.channel.media.azure.net:2935/<access token>`
`rtmps://<random 128bit hex string>.channel.media.azure.net:2936/<access token>`

##### <a name="smooth-streaming"></a>Transmissão em Fluxo Uniforme

`http://<random 128bit hex string>.channel.media.azure.net/<access token>/ingest.isml`
`https://<random 128bit hex string>.channel.media.azure.net/<access token>/ingest.isml`

#### <a name="vanity-url"></a>URL personalizado

##### <a name="rtmp"></a>RTMP

`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1935/<access token>`
`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1936/<access token>`
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2935/<access token>`
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2936/<access token>`

##### <a name="smooth-streaming"></a>Transmissão em Fluxo Uniforme

`http://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<access token>/ingest.isml`
`https://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<access token>/ingest.isml`

## <a name="live-event-preview-url"></a>URL de pré-visualização de eventos em direto

Uma vez a **evento em direto** começa a receber a contribuição de feed, pode utilizar o seu ponto de extremidade de pré-visualização para pré-visualizar e validar que estão a receber a transmissão em direto antes de publicar ainda mais. Depois de ter verificado que o fluxo de pré-visualização é bom, pode utilizar o LiveEvent para disponibilizar a transmissão em direto para uma entrega por meio de um ou mais (previamente criado) **pontos finais de transmissão em fluxo**. Para tal, crie um novo [saída Live](https://docs.microsoft.com/rest/api/media/liveoutputs) sobre o **evento em direto**. 

> [!IMPORTANT]
> Certifique-se de que o vídeo está a ser encaminhados para o URL de pré-visualização antes de continuar!

## <a name="live-outputs"></a>Saídas em Direto

Assim que tiver o fluxo a ser encaminhados para o evento em direto, pode começar o evento de transmissão em fluxo através da criação de um [Asset](https://docs.microsoft.com/rest/api/media/assets), [Live saída](https://docs.microsoft.com/rest/api/media/liveoutputs), e [localizador de transmissão em fluxo](https://docs.microsoft.com/rest/api/media/streaminglocators). Saída em direto irá arquivar a transmissão e disponibilizá-la para os espetadores através da [ponto final de transmissão em fluxo](https://docs.microsoft.com/rest/api/media/streamingendpoints).  

> [!NOTE]
> Início de saídas após a criação do Live e param quando eliminado. Ao eliminar a saída em direto, não serão eliminados os ativos e conteúdo no elemento subjacente. 

A relação entre um **evento em direto** e a respetiva **Live saídas** é semelhante a tradicional de televisão de difusão, no qual um canal (**evento em direto**) representa uma constante fluxo de vídeo e uma gravação (**Live saída**) tem um âmbito para um segmento de tempo específico (por exemplo, noite notícias da 6 17:30, para as 19:00: 00). Pode gravar programas de televisão com um gravador de vídeo Digital (DVR) – a funcionalidade equivalente nos eventos em direto é gerenciada através da **ArchiveWindowLength** propriedade. É uma duração de período de tempo de ISO-8601 (por exemplo, PTHH:MM:SS), que especifica a capacidade do DVR e pode ser definida a partir de um mínimo de 3 minutos até um máximo de 25 horas.

O **Live saída** objeto é como um gravador de banda que irá capturar e registrar a transmissão em direto para um elemento na sua conta de Media Services. O conteúdo gravado será persistido para a conta de armazenamento do Azure anexada à sua conta, no contêiner definido pelo recurso do recurso. O **Live saída** também permite-lhe controlar algumas propriedades do fluxo em direto saída, como o quanto da transmissão em fluxo é mantido na gravação do arquivo (por exemplo, a capacidade de DVR na cloud) e, se é ou não podem começar a visualizadores assistir à transmissão em direto. O arquivo no disco é um arquivo circular "janela" que contém apenas a quantidade de conteúdo especificado na **archiveWindowLength** propriedade da **Live saída**. Conteúdo que se encontre fora essa janela é descartado automaticamente do contentor de armazenamento e não é recuperável. Pode criar várias **saídas Live** (até três máximo) num **evento em direto** com definições e comprimentos de arquivo diferente.  

Se tiver publicado a **saída Live**da **Asset** usando um **localizador de transmissão em fluxo**, o **evento em direto** (até o tamanho de janela DVR) será continuar a ser visualizado até a expiração ou a eliminação do localizador de transmissão em fluxo, o que ocorrer primeiro.

Para obter mais informações, consulte [usando um DVR na cloud](live-event-cloud-dvr.md).

## <a name="next-steps"></a>Passos Seguintes

[Tutorial de transmissão em fluxo em direto](stream-live-tutorial-with-api.md)
