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
ms.date: 05/08/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 937a032bffbad4e8a7d737360aa140e59760f8e2
ms.sourcegitcommit: 399db0671f58c879c1a729230254f12bc4ebff59
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/09/2019
ms.locfileid: "65472444"
---
# <a name="encoding-with-media-services"></a>Encoding com Media Services

Serviços de multimédia do Azure permite-lhe a codificar seus arquivos de mídia digital de alta qualidade em ficheiros MP4 de velocidade de transmissão adaptável, para que seu conteúdo pode ser jogado numa grande variedade de navegadores e dispositivos. Uma tarefa de codificação de serviços de multimédia com êxito cria uma saída ativo com um conjunto de MP4s de velocidade de transmissão adaptável e ficheiros de configuração de transmissão em fluxo. Os ficheiros de configuração incluem. ISM, .ismc, .mpi e outros arquivos que não deve modificar. Depois de fazer o trabalho de codificação, pode aproveitar [empacotamento dinâmico](dynamic-packaging-overview.md) e iniciar a transmissão em fluxo.

Para fazer vídeos na saída Asset disponível para os clientes para a reprodução, tem de criar uma **localizador de transmissão em fluxo** e criar URLs de transmissão em fluxo. Em seguida, com base no formato especificado no manifesto, seus clientes recebem o fluxo no protocolo que escolheu.

O diagrama seguinte mostra o streaming sob demanda com o fluxo de trabalho de empacotamento dinâmico.

![Empacotamento dinâmico](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

Este tópico fornece orientações sobre como codificar o conteúdo com serviços de multimédia v3.

## <a name="transforms-and-jobs"></a>Transformações e tarefas

Codificar com serviços de multimédia v3, tem de criar uma [transformar](https://docs.microsoft.com/rest/api/media/transforms) e uma [tarefa](https://docs.microsoft.com/rest/api/media/jobs). Uma transformação define a receita para as suas definições e saídas de codificação e a tarefa é uma instância da receita. Para obter mais informações, consulte [transforma e tarefas](transforms-jobs-concept.md)

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
