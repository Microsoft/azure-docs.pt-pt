---
title: Codificação de vídeo e áudio com serviços de mídia
titleSuffix: Azure Media Services
description: Este artigo explica sobre codificação de vídeo e áudio com a Azure Media Services.
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
ms.openlocfilehash: 6e1c9aa5c2e049d5fc1ebd8bf745417f56d232ec
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80366583"
---
# <a name="encoding-video-and-audio-with-media-services"></a>Codificação de vídeo e áudio com serviços de mídia

O termo codificação nos Serviços de Media aplica-se ao processo de conversão de ficheiros que contenham vídeo digital e/ou áudio de um formato padrão para outro, com o objetivo de (a) reduzir o tamanho dos ficheiros, e/ou (b) produzir um formato compatível com uma ampla gama de dispositivos e aplicações. Este processo também é referido como compressão de vídeo, ou transcodificação. Consulte a [compressão de Dados](https://en.wikipedia.org/wiki/Data_compression) e o [Que É A Codificação e Transcodificação?](https://www.streamingmedia.com/Articles/Editorial/What-Is-/What-Is-Encoding-and-Transcoding-75025.aspx)

Os vídeos são normalmente entregues a dispositivos e aplicações por [download progressivo](https://en.wikipedia.org/wiki/Progressive_download) ou através de streaming [de bitrate adaptativo](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming).

> [!IMPORTANT]
> A Media Services não cobra empregos cancelados ou erros. Por exemplo, um trabalho que atingiu 50% de progresso e é cancelado não é cobrado em 50% das atas de trabalho. Só é cobrado por trabalhos acabados.

* Para entregar por download progressivo, pode utilizar o Azure Media Services para converter um ficheiro de mídia digital (mezanino) num ficheiro [MP4,](https://en.wikipedia.org/wiki/MPEG-4_Part_14) que contém vídeo que foi codificado com o código [H.264,](https://en.wikipedia.org/wiki/H.264/MPEG-4_AVC) e áudio que foi codificado com o codec [AAC.](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) Este ficheiro MP4 está escrito a um Ativo na sua conta de armazenamento. Pode utilizar as APIs ou SDKs de Armazenamento Azure (por exemplo, [Storage REST API](../../storage/common/storage-rest-api-auth.md) ou [.NET SDK)](../../storage/blobs/storage-quickstart-blobs-dotnet.md)para descarregar o ficheiro diretamente. Se criou o Ativo de saída com um nome de contentor específico no armazenamento, utilize essa localização. Caso contrário, pode utilizar os Serviços de Media para [listar os URLs](https://docs.microsoft.com/rest/api/media/assets/listcontainersas)de contentores de ativos . 
* Para preparar o conteúdo para entrega através de streaming de bitrate adaptativo, o ficheiro mezanino precisa de ser codificado a várias bitrates (alto a baixo). Para garantir uma transição graciosa de qualidade, a resolução do vídeo é reduzida à medida que o bitrate é reduzido. Isto resulta numa chamada escada de codificação- uma tabela de resoluções e bitrates (ver escada de [bitrate adaptável gerada](autogen-bitrate-ladder.md)automaticamente). Pode utilizar os Serviços de Media para codificar os seus ficheiros de mezanino a várias bitrates. Ao fazê-lo, obterá um conjunto de ficheiros MP4 e ficheiros de configuração de streaming associados escritos a um Ativo na sua conta de armazenamento. Em seguida, pode utilizar a capacidade de [Embalagem Dinâmica](dynamic-packaging-overview.md) nos Serviços de Media para entregar o vídeo através de protocolos de streaming como [MPEG-DASH](https://en.wikipedia.org/wiki/Dynamic_Adaptive_Streaming_over_HTTP) e [HLS](https://en.wikipedia.org/wiki/HTTP_Live_Streaming). Isto requer que crie um Localizador de [Streaming](streaming-locators-concept.md) e construa URLs de streaming correspondentes aos protocolos suportados, que podem ser depois entregues a dispositivos/apps com base nas suas capacidades.

O diagrama seguinte mostra o fluxo de trabalho para codificação a pedido com embalagem dinâmica.

![Fluxo de trabalho para codificação a pedido com embalagem dinâmica](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

Este tópico dá-lhe orientação sobre como codificar o seu conteúdo com o Media Services v3.

## <a name="transforms-and-jobs"></a>Transformações e tarefas

Para codificar com os Media Services v3, é necessário criar um [Transform](https://docs.microsoft.com/rest/api/media/transforms) and [a Job.](https://docs.microsoft.com/rest/api/media/jobs) A transformação define uma receita para as suas definições e saídas de codificação; o trabalho é um exemplo da receita. Para obter mais informações, veja [Transforms and Jobs](transforms-jobs-concept.md) (Transformações e Trabalhos).

Ao codificar com os Serviços de Media, utilize predefinições para dizer ao codificador como os ficheiros de mídia de entrada devem ser processados. Nos Serviços de Media v3, utiliza o Standard Encoder para codificar os seus ficheiros. Por exemplo, pode especificar a resolução de vídeo e/ou o número de canais de áudio que deseja no conteúdo codificado.

Pode começar rapidamente com uma das predefinições incorporadas recomendadas com base nas melhores práticas da indústria ou pode optar por construir um preset personalizado para direcionar os seus requisitos específicos para o seu cenário ou dispositivo. Para mais informações, consulte [o Encode com um Transform personalizado](customize-encoder-presets-how-to.md).

A partir de janeiro de 2019, quando codificar com o Standard Encoder para produzir ficheiros MP4(s), é gerado e adicionado ao Ativo de saída um novo ficheiro .mpi. Este ficheiro MPI destina-se a melhorar o desempenho das [embalagens dinâmicas](dynamic-packaging-overview.md) e cenários de streaming.

> [!NOTE]
> Não deve modificar ou remover o ficheiro MPI, nem tomar qualquer dependência do seu serviço sobre a existência (ou não) de tal ficheiro.

### <a name="creating-job-input-from-an-https-url"></a>Criação de entrada de emprego a partir de um URL HTTPS

Quando submete jobs para processar os seus vídeos, tem de dizer aos Media Services onde encontrar o vídeo de entrada. Uma das opções é especificar um URL HTTPS como entrada de trabalho. Atualmente, o Media Services v3 não suporta a codificação de transferências em pedaços sobre URLs HTTPS.

#### <a name="examples"></a>Exemplos

* [Codificar a partir de um URL HTTPS com .NET](stream-files-dotnet-quickstart.md)
* [Codificar a partir de um URL HTTPS com REST](stream-files-tutorial-with-rest.md)
* [Codificar a partir de um URL HTTPS com CLI](stream-files-cli-quickstart.md)
* [Codificar a partir de um URL HTTPS com Node.js](stream-files-nodejs-quickstart.md)

### <a name="creating-job-input-from-a-local-file"></a>Criação de entrada de emprego a partir de um arquivo local

O vídeo de entrada pode ser armazenado como um Media Service Asset, caso em que cria um ativo de entrada com base num ficheiro (armazenado localmente ou no armazenamento do Azure Blob).

#### <a name="examples"></a>Exemplos

[Codificar um ficheiro local utilizando presets incorporados](job-input-from-local-file-how-to.md)

### <a name="creating-job-input-with-subclipping"></a>Criação de entrada de emprego com subclipping

Ao codificar um vídeo, pode especificar também aparar ou cortar o ficheiro fonte e produzir uma saída que tenha apenas uma parte desejada do vídeo de entrada. Esta funcionalidade funciona com qualquer [Transform](https://docs.microsoft.com/rest/api/media/transforms) que seja construído utilizando os predefinições [BuiltInStandardEncoderPreset,](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) ou os predefinições [StandardEncoderPreset.](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset)

Pode especificar criar um [Trabalho](https://docs.microsoft.com/rest/api/media/jobs/create) com um único clip de um vídeo a pedido ou arquivo ao vivo (um evento gravado). A entrada de trabalho pode ser um Ativo ou um URL HTTPS.

> [!TIP]
> Se quiser transmitir um sublip do seu vídeo sem codificar novamente o vídeo, considere utilizar [manifestos de pré-filtragem com pacote dinâmico](filters-dynamic-manifest-overview.md).

#### <a name="examples"></a>Exemplos

Ver exemplos:

* [Subclipe um vídeo com .NET](subclip-video-dotnet-howto.md)
* [Subclipe um vídeo com REST](subclip-video-rest-howto.md)

## <a name="built-in-presets"></a>Presets incorporados

A Media Services suporta as seguintes predefinições de codificação incorporadas:  

### <a name="builtinstandardencoderpreset"></a>IncorporadoStandardEncoderPreset

[BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) é utilizado para definir um preset incorporado para codificar o vídeo de entrada com o Codificador Standard.

As seguintes predefinições são atualmente suportadas:

- **EncoderNamedPreset.AACGoodQualityAudio**: produz um único ficheiro MP4 contendo apenas áudio estéreo codificado a 192 kbps.
- **EncoderNamedPreset.AdaptiveStreaming** (recomendado): Para obter mais informações, consulte a [geração automática de uma escada bitrate](autogen-bitrate-ladder.md).
- **EncoderNamedPreset.ContentAwareEncodingExperimental**: expõe um preconjunto experimental para codificação consciente do conteúdo. Dado qualquer conteúdo de entrada, o serviço tenta determinar automaticamente o número ideal de camadas e configurações adequadas de bitrate e resolução para entrega através de streaming adaptativo. Os algoritmos subjacentes continuarão a evoluir ao longo do tempo. A saída conterá ficheiros MP4 com vídeo e áudio interleaved. Para mais informações, consulte [o predefinido experimental para codificação consciente do conteúdo](content-aware-encoding.md).
- **EncoderNamedPreset.H264MultipleBitrate1080p**: produz um conjunto de oito ficheiros MP4 alinhados com GOP, que variam entre 6000 kbps e 400 kbps e áudio AAC estéreo. A resolução começa a 1080p e desce para 360p.
- **EncoderNamedPreset.H264MultipleBitrate720p**: produz um conjunto de seis ficheiros MP4 alinhados com GOP, que variam entre 3400 kbps e 400 kbps e áudio AAC estéreo. A resolução começa a 720p e desce para 360p.
- **EncoderNamedPreset.H264MultipleBitrateSD**: produz um conjunto de cinco ficheiros MP4 alinhados com GOP, que variam entre 1600 kbps e 400 kbps e áudio AAC estéreo. A resolução começa a 480p e desce para 360p.
- **EncoderNamedPreset.H264SingleBitrate1080p**: produz um ficheiro MP4 onde o vídeo é codificado com código H.264 a 6750 kbps e uma altura de imagem de 1080 pixels, e o áudio estéreo é codificado com codec AAC-LC a 64 kbps.
- **EncoderNamedPreset.H264SingleBitrate720p**: produz um ficheiro MP4 onde o vídeo é codificado com código H.264 a 4500 kbps e uma altura de imagem de 720 pixels, e o áudio estéreo é codificado com codec AAC-LC a 64 kbps.
- **EncoderNamedPreset.H264SingleBitrateSD**: produz um ficheiro MP4 onde o vídeo é codificado com código H.264 a 2200 kbps e uma altura de imagem de 480 pixels, e o áudio estéreo é codificado com codec AAC-LC a 64 kbps.

Para ver a lista de predefinições mais atualizadas, consulte [predefinições incorporadas a utilizar para codificar vídeos](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset).

Para ver como as predefinições são utilizadas, consulte [ficheiros de upload, codificação e streaming](stream-files-tutorial-with-api.md).

### <a name="standardencoderpreset"></a>StandardEncoderPreset

[StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) descreve definições a utilizar ao codificar o vídeo de entrada com o Codificador Standard. Utilize este predefinido ao personalizar as predefinições Transform.

#### <a name="considerations"></a>Considerações

Ao criar predefinições personalizadas, aplicam-se as seguintes considerações:

- Todos os valores para a altura e largura no teor de AVC devem ser múltiplos de quatro.
- No Azure Media Services v3, todos os bitrates de codificação estão em bits por segundo. Isto é diferente dos presets com as nossas V2 APIs, que usavam quilobits/segundo como unidade. Por exemplo, se o bitrate em v2 fosse especificado como 128 (quilobits/segundo), em v3 seria fixado em 128000 (bits/segundo).

### <a name="customizing-presets"></a>Personalizando predefinições

A Media Services suporta totalmente a personalização de todos os valores em predefinições para satisfazer as suas necessidades e requisitos específicos de codificação. Por exemplo, que mostram como personalizar predefinições codificadoras, consulte a lista abaixo:

#### <a name="examples"></a>Exemplos

- [Personalize predefinições com .NET](customize-encoder-presets-how-to.md)
- [Personalize predefinições com CLI](custom-preset-cli-howto.md)
- [Personalize predefinições com REST](custom-preset-rest-howto.md)

## <a name="preset-schema"></a>Esquema pré-definido

Nos Serviços de Media v3, as predefinições são entidades fortemente digitadas na própria API. Pode encontrar a definição "schema" para estes objetos em [Especificação API Aberta (ou Swagger)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01). Também pode visualizar as definições predefinidas (como **StandardEncoderPreset)** no [REST API,](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.standardencoderpreset?view=azure-dotnet) (ou outra documentação de referência de Media Services v3 SDK).

## <a name="scaling-encoding-in-v3"></a>Codificação de escalonamento em v3

Para escalar o processamento dos meios de comunicação, consulte [Escala com CLI](media-reserved-units-cli-how-to.md).

## <a name="billing"></a>Faturação

A Media Services não cobra empregos cancelados ou erros. Por exemplo, um trabalho que atingiu 50% de progresso e é cancelado não é cobrado em 50% das atas de trabalho. Só é cobrado por trabalhos acabados.

Para obter mais informações, veja os [preços](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="ask-questions-give-feedback-get-updates"></a>Faça perguntas, dê feedback, obtenha atualizações

Confira o artigo da [comunidade Azure Media Services](media-services-community.md) para ver diferentes formas de fazer perguntas, dar feedback e obter atualizações sobre os Serviços de Media.

## <a name="next-steps"></a>Passos seguintes

* [Carregar, codificar e transmitir através dos Serviços de Media](stream-files-tutorial-with-api.md).
* [Codificar a partir de um URL HTTPS utilizando predefinições incorporadas](job-input-from-http-how-to.md).
* [Codificar um ficheiro local utilizando predefinições incorporadas](job-input-from-local-file-how-to.md).
* [Construa um predefinido personalizado para direcionar o seu cenário ou requisitos específicos do dispositivo](customize-encoder-presets-how-to.md).
