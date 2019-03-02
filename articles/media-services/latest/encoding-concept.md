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
ms.date: 02/27/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: de2c60d4449762c4a8fcc3e2f486130f3df37c7c
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57243624"
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

- **EncoderNamedPreset.AdaptiveStreaming** (recomendado). Para obter mais informações, consulte [criação automática de uma escala de bits](autogen-bitrate-ladder.md).
- **EncoderNamedPreset.AACGoodQualityAudio** -produz um ficheiro MP4 único que contém apenas estéreo áudio codificado em 192 kbps.
- **EncoderNamedPreset.H264MultipleBitrate1080p** -produz um conjunto de ficheiros MP4 de alinhado GOP de 8, desde 6000 kbps até 400 kbps e áudio AAC estéreo. Resolução começa em 1080p e vai até 360p.
- **EncoderNamedPreset.H264MultipleBitrate720p** -produz um conjunto de 6 ficheiros MP4 GOP alinhados, desde 3400 kbps até 400 kbps e áudio AAC estéreo. Resolução é iniciada à 720p e vai até 360p.
- **EncoderNamedPreset.H264MultipleBitrateSD** -produz um conjunto de ficheiros MP4 de alinhado GOP de 5, desde 1600 kbps até 400 kbps e áudio AAC estéreo. Resolução é iniciada à 480p e vai até 360p.<br/><br/>Para obter mais informações, consulte [carregar, codificar e ficheiros de transmissão em fluxo](stream-files-tutorial-with-api.md).

### <a name="standardencoderpreset-preset"></a>Configuração predefinida de StandardEncoderPreset

[StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) descreve as definições para serem utilizadas quando a codificação de vídeo de entrada com o codificador Standard. Utilize esta configuração predefinida ao personalizar as configurações predefinidas de transformação. 

#### <a name="custom-presets"></a>Configurações predefinidas personalizadas

Serviços de multimédia suporta totalmente a personalizar todos os valores nas predefinições para atender às suas necessidades específicas de codificação e requisitos. Utilizar o **StandardEncoderPreset** predefinidos ao personalizar as configurações predefinidas de transformação. Uma detalhada para explicações e de exemplo, veja [como personalizar as configurações predefinidas de codificador](customize-encoder-presets-how-to.md).

## <a name="scaling-encoding-in-v3"></a>Dimensionar codificação da v3

Dimensionar processamento de multimédia, veja [dimensionamento com a CLI](media-reserved-units-cli-how-to.md).

## <a name="next-steps"></a>Passos Seguintes

* [Codificar a partir de um URL HTTPS com configurações predefinidas incorporadas](job-input-from-http-how-to.md)
* [Codificar um arquivo local usando as configurações predefinidas incorporadas](job-input-from-local-file-how-to.md)
* [Criar um personalizado predefinido para seus requisitos específicos de cenário ou dispositivo de destino](customize-encoder-presets-how-to.md)
* [Carregar, codificar e transmitir em fluxo através dos Media Services](stream-files-tutorial-with-api.md)
