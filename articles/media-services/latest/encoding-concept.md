---
title: Codificando vídeo e áudio com os serviços de mídia
titleSuffix: Azure Media Services
description: Este artigo explica sobre a codificação de vídeo e áudio com os serviços de mídia do Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 09/10/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 6a134d2bdfe7f370503b80703933ff646970d976
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75981111"
---
# <a name="encoding-video-and-audio-with-media-services"></a>Codificando vídeo e áudio com os serviços de mídia

O termo codificação nos serviços de mídia se aplica ao processo de conversão de arquivos que contêm vídeo digital e/ou áudio de um formato padrão para outro, com a finalidade de (a) reduzir o tamanho dos arquivos e/ou (b) produzir um formato compatível com uma ampla variedade de dispositivos e aplicativos. Esse processo também é conhecido como compactação de vídeo ou transcodificação. Consulte a [compactação de dados](https://en.wikipedia.org/wiki/Data_compression) e o [que é codificação e transcodificação?](https://www.streamingmedia.com/Articles/Editorial/What-Is-/What-Is-Encoding-and-Transcoding-75025.aspx) para obter mais informações sobre os conceitos.

Os vídeos normalmente são entregues a dispositivos e aplicativos por [download progressivo](https://en.wikipedia.org/wiki/Progressive_download) ou por meio de [streaming de taxa de bits adaptável](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming).

* Para entregar por download progressivo, você pode usar os serviços de mídia do Azure para converter um arquivo de mídia digital (mezanino) em um arquivo [MP4](https://en.wikipedia.org/wiki/MPEG-4_Part_14) , que contém vídeo que foi codificado com o codec [H. 264](https://en.wikipedia.org/wiki/H.264/MPEG-4_AVC) e áudio que foi codificado com o codec [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) . Esse arquivo MP4 é gravado em um ativo em sua conta de armazenamento. Você pode usar as APIs de armazenamento do Azure ou SDKs (por exemplo, [API REST de armazenamento](../../storage/common/storage-rest-api-auth.md) ou [SDK do .net](../../storage/blobs/storage-quickstart-blobs-dotnet.md)) para baixar o arquivo diretamente. Se você criou o ativo de saída com um nome de contêiner específico no armazenamento, use esse local. Caso contrário, você pode usar os serviços de mídia para [listar as URLs do contêiner de ativos](https://docs.microsoft.com/rest/api/media/assets/listcontainersas). 
* Para preparar o conteúdo para entrega por streaming de taxa de bits adaptável, o arquivo de mezanino precisa ser codificado em várias taxas de bits (alta para baixa). Para garantir a transição normal de qualidade, a resolução do vídeo é reduzida à medida que a taxa de bits é reduzida. Isso resulta em uma chamada de escada de codificação – uma tabela de resoluções e taxas de bits (consulte a [escada de taxa de bits adaptável gerada automaticamente](autogen-bitrate-ladder.md)). Você pode usar os serviços de mídia para codificar seus arquivos de mezanino em várias taxas de bits. Ao fazer isso, você obterá um conjunto de arquivos MP4 e arquivos de configuração de streaming associados gravados em um ativo em sua conta de armazenamento. Em seguida, você pode usar o recurso de [empacotamento dinâmico](dynamic-packaging-overview.md) nos serviços de mídia para entregar o vídeo por meio de protocolos de streaming como [MPEG-Dash](https://en.wikipedia.org/wiki/Dynamic_Adaptive_Streaming_over_HTTP) e [HLS](https://en.wikipedia.org/wiki/HTTP_Live_Streaming). Isso exige que você crie um [localizador de streaming](streaming-locators-concept.md) e crie URLs de streaming correspondentes aos protocolos com suporte, que podem ser entregues a dispositivos/aplicativos com base em seus recursos.

O diagrama a seguir mostra o fluxo de trabalho para a codificação sob demanda com o empacotamento dinâmico.

![Fluxo de trabalho para codificação sob demanda com empacotamento dinâmico](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

Este tópico fornece orientações sobre como codificar o conteúdo com serviços de multimédia v3.

## <a name="transforms-and-jobs"></a>Transformações e tarefas

Para codificar com os serviços de mídia v3, você precisa criar uma [transformação](https://docs.microsoft.com/rest/api/media/transforms) e um [trabalho](https://docs.microsoft.com/rest/api/media/jobs). A transformação define uma receita para suas configurações e saídas de codificação; o trabalho é uma instância da receita. Para obter mais informações, veja [Transforms and Jobs](transforms-jobs-concept.md) (Transformações e Trabalhos).

Quando o encoding com Media Services, vai utilizar configurações predefinidas para informar ao codificador, como os ficheiros de suporte de dados de entrada devem ser processados. Por exemplo, pode especificar a resolução de vídeo e/ou o número de canais de áudio que pretende no conteúdo codificado.

Pode começar a utilizar rapidamente com um dos pré-visualizando incorporada recomendado com base nas práticas recomendadas do setor ou pode optar por criar um personalizado predefinido para seus requisitos específicos de cenário ou dispositivo de destino. Para obter mais informações, consulte [codificar com uma transformação personalizada](customize-encoder-presets-how-to.md).

A partir de janeiro de 2019, ao codificar com Media Encoder Standard para produzir arquivos MP4, um novo arquivo. MPI é gerado e adicionado ao ativo de saída. Este arquivo MPI destina-se a melhorar o desempenho para cenários de streaming e [empacotamento dinâmico](dynamic-packaging-overview.md) .

> [!NOTE]
> Você não deve modificar ou remover o arquivo MPI ou assumir qualquer dependência em seu serviço na existência (ou não) desse arquivo.

### <a name="creating-job-input-from-an-https-url"></a>Criando entrada de trabalho de uma URL HTTPS

Ao enviar trabalhos para processar seus vídeos, você precisa informar aos serviços de mídia onde encontrar o vídeo de entrada. Uma das opções é especificar uma URL HTTPS como uma entrada de trabalho. Atualmente, os serviços de mídia v3 não dão suporte à codificação de transferência em partes sobre URLs HTTPS.

#### <a name="examples"></a>Exemplos

* [Codificar de uma URL HTTPS com .NET](stream-files-dotnet-quickstart.md)
* [Codificar de uma URL HTTPS com REST](stream-files-tutorial-with-rest.md)
* [Codificar de uma URL HTTPS com CLI](stream-files-cli-quickstart.md)
* [Codificar de uma URL HTTPS com node. js](stream-files-nodejs-quickstart.md)

### <a name="creating-job-input-from-a-local-file"></a>Criando entrada de trabalho de um arquivo local

O vídeo de entrada pode ser armazenado como um ativo de serviço de mídia. nesse caso, você cria um ativo de entrada com base em um arquivo (armazenado localmente ou no armazenamento de BLOBs do Azure).

#### <a name="examples"></a>Exemplos

[Codificar um arquivo local usando predefinições internas](job-input-from-local-file-how-to.md)

### <a name="creating-job-input-with-subclipping"></a>Criando entrada de trabalho com subcorte

Ao codificar um vídeo, você pode especificar também aparar ou recortar o arquivo de origem e produzir uma saída que tenha apenas uma parte desejada do vídeo de entrada. Essa funcionalidade funciona com qualquer [transformação](https://docs.microsoft.com/rest/api/media/transforms) criada usando as predefinições [BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) ou as predefinições de [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) .

Você pode especificar para criar um [trabalho](https://docs.microsoft.com/rest/api/media/jobs/create) com um único clipe de um arquivo de vídeo sob demanda ou dinâmico (um evento gravado). A entrada do trabalho pode ser um ativo ou uma URL HTTPS.

> [!TIP]
> Se você quiser transmitir uma sublip de seu vídeo sem recodificar o vídeo, considere o uso de [manifestos de filtragem prévia com o Dynamic Packager](filters-dynamic-manifest-overview.md).

#### <a name="examples"></a>Exemplos

Veja exemplos:

* [Subclipe um vídeo com o .NET](subclip-video-dotnet-howto.md)
* [Subclipe um vídeo com REST](subclip-video-rest-howto.md)

## <a name="built-in-presets"></a>Configurações predefinidas incorporadas

Os serviços de mídia oferecem suporte às seguintes predefinições de codificação internas:  

### <a name="builtinstandardencoderpreset"></a>BuiltInStandardEncoderPreset

[BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) é usado para definir uma predefinição interna para codificar o vídeo de entrada com o codificador padrão.

Atualmente são suportadas as seguintes predefinições:

- **EncoderNamedPreset. AACGoodQualityAudio**: produz um único arquivo MP4 contendo apenas áudio estéreo codificado a 192 kbps.
- **EncoderNamedPreset. AdaptiveStreaming** (recomendado): para obter mais informações, consulte [gerando automaticamente uma escada de taxa de bits](autogen-bitrate-ladder.md).
- **EncoderNamedPreset. ContentAwareEncodingExperimental**: expõe uma predefinição experimental para codificação com reconhecimento de conteúdo. Dado qualquer conteúdo de entrada, o serviço tenta determinar automaticamente o número ideal de camadas e as configurações apropriadas de taxa de bits e resolução para entrega por streaming adaptável. Os algoritmos subjacentes continuarão a evoluir ao longo do tempo. A saída conterá arquivos MP4 com vídeo e áudio intercalados. Para obter mais informações, consulte [predefinição experimental para codificação com reconhecimento de conteúdo](content-aware-encoding.md).
- **EncoderNamedPreset. H264MultipleBitrate1080p**: produz um conjunto de oito arquivos MP4 alinhados a GOP, variando de 6000 kbps a 400 Kbps e áudio AAC estéreo. Resolução começa em 1080p e vai até 360p.
- **EncoderNamedPreset. H264MultipleBitrate720p**: produz um conjunto de seis arquivos MP4 alinhados a GOP, variando de 3400 kbps a 400 Kbps e áudio AAC estéreo. Resolução é iniciada à 720p e vai até 360p.
- **EncoderNamedPreset. H264MultipleBitrateSD**: produz um conjunto de cinco arquivos MP4 alinhados a GOP, variando de 1600 kbps a 400 Kbps e áudio AAC estéreo. Resolução é iniciada à 480p e vai até 360p.
- **EncoderNamedPreset. H264SingleBitrate1080p**: produz um arquivo MP4 em que o vídeo é codificado com o codec H. 264 a 6750 kbps e uma altura de imagem de 1080 pixels, e o áudio estéreo é codificado com o codec AAC-LC em 64 Kbps.
- **EncoderNamedPreset. H264SingleBitrate720p**: produz um arquivo MP4 em que o vídeo é codificado com o codec H. 264 a 4500 kbps e uma altura de imagem de 720 pixels, e o áudio estéreo é codificado com o codec AAC-LC em 64 Kbps.
- **EncoderNamedPreset. H264SingleBitrateSD**: produz um arquivo MP4 em que o vídeo é codificado com o codec H. 264 a 2200 kbps e uma altura de imagem de 480 pixels, e o áudio estéreo é codificado com o codec AAC-LC em 64 Kbps.

Para ver a lista de predefinições mais atualizada, consulte [predefinições internas a serem usadas para codificar vídeos](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset).

Para ver como as predefinições são usadas, consulte [carregando, codificando e transmitindo arquivos](stream-files-tutorial-with-api.md).

### <a name="standardencoderpreset"></a>StandardEncoderPreset

[StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) descreve as configurações a serem usadas ao codificar o vídeo de entrada com o codificador padrão. Utilize esta configuração predefinida ao personalizar as configurações predefinidas de transformação.

#### <a name="considerations"></a>Considerações

Ao criar predefinições personalizadas, as seguintes considerações se aplicam:

- Todos os valores para altura e largura em conteúdo AVC devem ser um múltiplo de quatro.
- Nos serviços de mídia do Azure v3, todas as taxas de bits de codificação estão em bits por segundo. Isso é diferente das predefinições com nossas APIs v2, que usavam kilobits/segundo como a unidade. Por exemplo, se a taxa de bits em v2 tiver sido especificada como 128 (kilobits/segundo), em v3, ela seria definida como 128000 (bits/segundo).

### <a name="customizing-presets"></a>Personalizando predefinições

Serviços de multimédia suporta totalmente a personalizar todos os valores nas predefinições para atender às suas necessidades específicas de codificação e requisitos. Para obter exemplos que mostram como personalizar as predefinições do codificador, consulte a lista abaixo:

#### <a name="examples"></a>Exemplos

- [Personalizar as predefinições com o .NET](customize-encoder-presets-how-to.md)
- [Personalizar predefinições com a CLI](custom-preset-cli-howto.md)
- [Personalizar predefinições com REST](custom-preset-rest-howto.md)

## <a name="preset-schema"></a>Esquema predefinido

Nos serviços de mídia v3, as predefinições são entidades fortemente tipadas na própria API. Você pode encontrar a definição de "esquema" para esses objetos em [especificação de API aberta (ou Swagger)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01). Você também pode exibir as definições predefinidas (como **StandardEncoderPreset**) na [API REST](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset), no [SDK do .net](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.standardencoderpreset?view=azure-dotnet) (ou em outra documentação de referência do SDK dos serviços de mídia v3).

## <a name="scaling-encoding-in-v3"></a>Dimensionar codificação da v3

Para dimensionar o processamento de mídia, consulte [dimensionar com a CLI](media-reserved-units-cli-how-to.md).

## <a name="ask-questions-give-feedback-get-updates"></a>Faça perguntas, envie comentários, obtenha atualizações

Confira o artigo [da Comunidade dos serviços de mídia do Azure](media-services-community.md) para ver diferentes maneiras que você pode fazer perguntas, fornecer comentários e obter atualizações sobre os serviços de mídia.

## <a name="next-steps"></a>Passos seguintes

* [Carregue, codifique e transmita usando os serviços de mídia](stream-files-tutorial-with-api.md).
* [Codifique a partir de uma URL https usando predefinições internas](job-input-from-http-how-to.md).
* [Codifique um arquivo local usando predefinições internas](job-input-from-local-file-how-to.md).
* [Crie uma predefinição personalizada para direcionar seus requisitos específicos de cenário ou dispositivo](customize-encoder-presets-how-to.md).
