---
title: Codificação na cloud com serviços de multimédia - Azure | Documentos da Microsoft
description: Este tópico descreve o processo de codificação ao utilizar os serviços de multimédia do Azure
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/10/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 25b3209bed98ea217db9e414caa6f08cee6d8c89
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65761889"
---
# <a name="encoding-with-media-services"></a>Encoding com Media Services

A codificação de termo nos serviços de multimédia aplica-se para o processo de conversão de ficheiros que contêm vídeo digital e/ou áudio de um formato padrão para outro, com o propósito de (a) a reduzir o tamanho dos ficheiros de e/ou (b) produzir um formato compatível com um ampla gama de dispositivos e aplicações. Este processo também é referido como compressão de vídeo, ou a transcodificação. Consulte a [compressão de dados](https://en.wikipedia.org/wiki/Data_compression) e o [o que é a codificação e transcodificação?](https://www.streamingmedia.com/Articles/Editorial/What-Is-/What-Is-Encoding-and-Transcoding-75025.aspx) para ainda mais discussão sobre os conceitos.

Vídeos são normalmente entregues aos dispositivos e aplicações ao [transferência progressiva](https://en.wikipedia.org/wiki/Progressive_download) ou através de [transmissão em fluxo de velocidade de transmissão adaptável](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming). 

* Para entregar por transferência progressiva, pode utilizar os serviços de multimédia do Azure para converter um o ficheiro de multimédia digital (mezanino) para um [MP4](https://en.wikipedia.org/wiki/MPEG-4_Part_14) ficheiro que contém o vídeo que tem sido codificado com o [H.264](https://en.wikipedia.org/wiki/H.264/MPEG-4_AVC) codec, e áudio que tenha sido codificado com o [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) codec. Este ficheiro MP4 é escrito para um recurso na sua conta de armazenamento. Pode utilizar as APIs de armazenamento do Azure ou SDKs (por exemplo, [API do REST de armazenamento](../../storage/common/storage-rest-api-auth.md), [SDK do JAVA](../../storage/blobs/storage-quickstart-blobs-java-v10.md), ou [SDK de .NET](../../storage/blobs/storage-quickstart-blobs-dotnet.md)) para transferir o ficheiro diretamente. Se tiver criado a saída ativo com um nome de contentor específicas no armazenamento, utilize essa localização. Caso contrário, pode utilizar serviços de multimédia para [lista os URLs do contentor de elemento](https://docs.microsoft.com/rest/api/media/assets/listcontainersas). 
* Para preparar o conteúdo para o fornecimento por transmissão em fluxo de velocidade de transmissão adaptável, o ficheiro de mezanino tem de ser codificados em múltiplas velocidades de transmissão (alta a baixa). Para garantir uma transição anulações normal de qualidade, como a velocidade de transmissão é reduzida, portanto, é a resolução do vídeo. Isso resulta numa escada codificação chamada – uma tabela de resoluções e velocidades de transmissão (consulte [gerado automaticamente velocidade de transmissão adaptável escada](autogen-bitrate-ladder.md)). Pode utilizar os serviços de multimédia para codificar seus arquivos de mezanino em múltiplas velocidades de transmissão – ao fazer isso, obterá um conjunto de ficheiros MP4 e transmissão em fluxo configuração ficheiros associados, escritos para um recurso na sua conta de armazenamento. Em seguida, pode utilizar o [empacotamento dinâmico](dynamic-packaging-overview.md) capacidade nos serviços de multimédia para entregar o vídeo através de protocolos, como de transmissão em fluxo [MPEG-DASH](https://en.wikipedia.org/wiki/Dynamic_Adaptive_Streaming_over_HTTP) e [HLS](https://en.wikipedia.org/wiki/HTTP_Live_Streaming). Isso exige que crie uma [localizador de transmissão em fluxo](streaming-locators-concept.md) e crie URLs correspondentes aos protocolos suportados, que, em seguida, podem ser entregue aos dispositivos/aplicativos com base em seus recursos de transmissão em fluxo.

O diagrama seguinte mostra o fluxo de trabalho para a codificação de demanda com o empacotamento dinâmico.

![Empacotamento dinâmico](./media/dynamic-packaging-overview/media-services-dynamic-packaging.png)

Este tópico fornece orientações sobre como codificar o conteúdo com serviços de multimédia v3.

## <a name="transforms-and-jobs"></a>Transformações e tarefas

Codificar com serviços de multimédia v3, tem de criar uma [transformar](https://docs.microsoft.com/rest/api/media/transforms) e uma [tarefa](https://docs.microsoft.com/rest/api/media/jobs). A transformação define uma receita para suas configurações de codificação e saídas; a tarefa é uma instância da receita. Para obter mais informações, consulte [transforma e tarefas](transforms-jobs-concept.md)

Quando o encoding com Media Services, vai utilizar configurações predefinidas para informar ao codificador, como os ficheiros de suporte de dados de entrada devem ser processados. Por exemplo, pode especificar a resolução de vídeo e/ou o número de canais de áudio que pretende no conteúdo codificado. 

Pode começar a utilizar rapidamente com um dos pré-visualizando incorporada recomendado com base nas práticas recomendadas do setor ou pode optar por criar um personalizado predefinido para seus requisitos específicos de cenário ou dispositivo de destino. Para obter mais informações, consulte [codificar com uma transformação personalizada](customize-encoder-presets-how-to.md). 

A partir de 2019 Janeiro, quando o encoding com Media Encoder Standard para produzir ficheiros MP4, um novo ficheiro de .mpi é gerado e adicionado à saída de ativo. Este ficheiro MPI destina-se para melhorar o desempenho para [empacotamento dinâmico](dynamic-packaging-overview.md) e cenários de transmissão em fluxo.

> [!NOTE]
> Não deve modificar ou remover o arquivo MPI ou fazer qualquer dependência no seu serviço na existência (ou não) de um arquivo desse tipo.

## <a name="built-in-presets"></a>Configurações predefinidas incorporadas

Atualmente, os serviços de multimédia suporta as seguintes predefinições de codificação incorporadas:  

### <a name="builtinstandardencoderpreset-preset"></a>Configuração predefinida de BuiltInStandardEncoderPreset

[BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) é usado para definir um incorporado na configuração predefinido para a codificação de vídeo de entrada com o codificador Standard. 

Atualmente são suportadas as seguintes predefinições:

- **EncoderNamedPreset.AACGoodQualityAudio** -produz um ficheiro MP4 único que contém apenas estéreo áudio codificado em 192 kbps.
- **EncoderNamedPreset.AdaptiveStreaming** (recomendado). Para obter mais informações, consulte [criação automática de uma escala de bits](autogen-bitrate-ladder.md).
- **EncoderNamedPreset.ContentAwareEncodingExperimental** -expõe uma configuração experimental predefinida para a codificação com reconhecimento de conteúdo. Devido a qualquer conteúdo de entrada, o serviço tenta determinar automaticamente o número ideal de camadas, velocidade de transmissão adequada e definições de resolução para o fornecimento por transmissão em fluxo adaptável. Os algoritmos subjacentes continuará a evoluir ao longo do tempo. O resultado irá conter ficheiros MP4 com vídeo e áudio intercalado. Para obter mais informações, consulte [Experimental configuração predefinida para a codificação de conteúdo com suporte para](cae-experimental.md).
- **EncoderNamedPreset.H264MultipleBitrate1080p** -produz um conjunto de ficheiros MP4 de alinhado GOP de 8, desde 6000 kbps até 400 kbps e áudio AAC estéreo. Resolução começa em 1080p e vai até 360p.
- **EncoderNamedPreset.H264MultipleBitrate720p** -produz um conjunto de 6 ficheiros MP4 GOP alinhados, desde 3400 kbps até 400 kbps e áudio AAC estéreo. Resolução é iniciada à 720p e vai até 360p.
- **EncoderNamedPreset.H264MultipleBitrateSD** -produz um conjunto de ficheiros MP4 de alinhado GOP de 5, desde 1600 kbps até 400 kbps e áudio AAC estéreo. Resolução é iniciada à 480p e vai até 360p.
- **EncoderNamedPreset.H264SingleBitrate1080p** -produz um ficheiro MP4, em que o vídeo está codificado com H.264 codec em 6750 kbps e uma altura de imagem de 1080 pixels, e o som estéreo está codificado com codec AAC-LC em 64 kbps.
- **EncoderNamedPreset.H264SingleBitrate720p** -produz um ficheiro MP4, em que o vídeo está codificado com H.264 codec em 4500 kbps e uma altura de imagem de 720 pixels e o som estéreo está codificado com codec AAC-LC em 64 kbps.
- **EncoderNamedPreset.H264SingleBitrateSD** -produz um ficheiro MP4, em que o vídeo está codificado com H.264 codec em 2200 kbps e uma altura de imagem de 480 pixels e o som estéreo está codificado com codec AAC-LC em 64 kbps.

Para ver a lista de predefinições mais atualizada, consulte [suas configurações predefinidas incorporadas a ser utilizado para a codificação de vídeos](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset).

Para ver como são utilizadas as predefinições, veja [carregar, codificar e ficheiros de transmissão em fluxo](stream-files-tutorial-with-api.md).

### <a name="standardencoderpreset-preset"></a>Configuração predefinida de StandardEncoderPreset

[StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) descreve as definições para serem utilizadas quando a codificação de vídeo de entrada com o codificador Standard. Utilize esta configuração predefinida ao personalizar as configurações predefinidas de transformação. 

#### <a name="considerations"></a>Considerações

Ao criar configurações predefinidas personalizadas, aplicam-se as seguintes considerações:

- Todos os valores para a altura e largura no conteúdo de AVC tem de ser um múltiplo de 4.
- Em serviços de multimédia do Azure v3, todas as velocidades de transmissão de codificação são em bits por segundo. Isso é diferente de predefinições com nossas APIs v2, que utilizado kilobits por segundo, como a unidade. Por exemplo, se a velocidade de transmissão no v2 foi especificada como 128 (kilobits por segundo), na v3-la seria definido como 128000 (bits por segundo).

#### <a name="examples"></a>Exemplos

Serviços de multimédia suporta totalmente a personalizar todos os valores nas predefinições para atender às suas necessidades específicas de codificação e requisitos. Para obter exemplos que mostram como personalizar as configurações predefinidas de codificador, consulte:

- [Personalizar suas configurações predefinidas com .NET](customize-encoder-presets-how-to.md)
- [Personalizar suas configurações predefinidas, com a CLI](custom-preset-cli-howto.md)
- [Personalizar suas configurações predefinidas com REST](custom-preset-rest-howto.md)

## <a name="preset-schema"></a>Esquema predefinida

Em serviços de multimédia v3, suas configurações predefinidas são com rigidez de tipos de entidades na API propriamente dita. Pode encontrar a definição de "schema" para esses objetos no [especificação de API aberta (ou Swagger)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01). Também pode ver as definições predefinidas (como **StandardEncoderPreset**) na [REST API](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset), [SDK de .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.standardencoderpreset?view=azure-dotnet) (ou outra documentação de referência do serviços de multimédia v3 SDK).

## <a name="scaling-encoding-in-v3"></a>Dimensionar codificação da v3

Dimensionar processamento de multimédia, veja [dimensionamento com a CLI](media-reserved-units-cli-how-to.md).

## <a name="ask-questions-give-feedback-get-updates"></a>Faça perguntas, comentários, obter atualizações

Veja a [Comunidade dos serviços de multimédia do Azure](media-services-community.md) artigo para ver formas diferentes, pode fazer perguntas, comentários e obter atualizações sobre os serviços de multimédia.

## <a name="next-steps"></a>Passos Seguintes

* [Codificar a partir de um URL HTTPS com configurações predefinidas incorporadas](job-input-from-http-how-to.md)
* [Codificar um arquivo local usando as configurações predefinidas incorporadas](job-input-from-local-file-how-to.md)
* [Criar um personalizado predefinido para seus requisitos específicos de cenário ou dispositivo de destino](customize-encoder-presets-how-to.md)
* [Carregar, codificar e transmitir em fluxo através dos Media Services](stream-files-tutorial-with-api.md)
