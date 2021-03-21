---
title: Codificação de vídeo e áudio com Serviços de Media
description: Este artigo explica sobre a codificação de vídeo e áudio com a Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: 6a486057a265b02ce30059940c8c98837ec43f8e
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102617646"
---
# <a name="encoding-video-and-audio-with-media-services"></a>Codificação de vídeo e áudio com Serviços de Media

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

O termo codificação nos Serviços de Media aplica-se ao processo de conversão de ficheiros que contenham vídeo e/ou áudio digital de um formato padrão para outro, com o objetivo de (a) reduzir o tamanho dos ficheiros, e/ou (b) produzir um formato compatível com uma ampla gama de dispositivos e aplicações. Este processo também é referido como compressão de vídeo, ou transcoding. Veja a [compressão de dados](https://en.wikipedia.org/wiki/Data_compression) e o [Que É Codificação e Transcoding?](https://www.streamingmedia.com/Articles/Editorial/What-Is-/What-Is-Encoding-and-Transcoding-75025.aspx)

Os vídeos são normalmente entregues em dispositivos e apps através [de download progressivo](https://en.wikipedia.org/wiki/Progressive_download) ou através do [streaming de bitrate adaptativo.](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)

> [!IMPORTANT]
> Os Serviços de Comunicação Social não cobram por empregos cancelados ou erros. Por exemplo, um trabalho que tenha atingido 50% de progresso e seja cancelado não é cobrado em 50% das atas de trabalho. Só é cobrado por trabalhos acabados.

* Para entregar através de download progressivo, pode utilizar o Azure Media Services para converter um ficheiro de mídia digital (mezanino) num ficheiro [MP4,](https://en.wikipedia.org/wiki/MPEG-4_Part_14) que contém vídeo que foi codificado com o codec [H.264](https://en.wikipedia.org/wiki/H.264/MPEG-4_AVC) e áudio que foi codificado com o codec [AAC.](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) Este ficheiro MP4 está escrito para um Ativo na sua conta de armazenamento. Pode utilizar as APIs ou SDKs de armazenamento Azure (por exemplo, [API de Armazenamento OU](../../storage/common/storage-rest-api-auth.md) [.NET SDK)](../../storage/blobs/storage-quickstart-blobs-dotnet.md)para descarregar o ficheiro diretamente. Se criou o Ativo de saída com um nome de recipiente específico armazenado, utilize essa localização. Caso contrário, pode utilizar os Serviços de Comunicação Social para [listar os URLs do contentor de ativos.](/rest/api/media/assets/listcontainersas) 
* Para preparar o conteúdo para a entrega através do streaming de bitrate adaptativo, o ficheiro mezanino precisa de ser codificado a vários bitrates (de alto a baixo). Para garantir uma transição graciosa de qualidade, a resolução do vídeo é reduzida à medida que o bitrate é reduzido. Isto resulta numa chamada escada codificadora - uma tabela de resoluções e bitrates (ver [escada de bitrate adaptativa gerada automaticamente).](autogen-bitrate-ladder.md) Pode utilizar os Serviços de Comunicação Social para codificar os seus ficheiros mezaninos em vários bitrates. Ao fazê-lo, obterá um conjunto de ficheiros MP4 e ficheiros de configuração de streaming associados escritos num Ativo na sua conta de armazenamento. Em seguida, pode utilizar a capacidade [de Embalagem Dinâmica](dynamic-packaging-overview.md) nos Serviços de Media para entregar o vídeo através de protocolos de streaming como [MPEG-DASH](https://en.wikipedia.org/wiki/Dynamic_Adaptive_Streaming_over_HTTP) e [HLS](https://en.wikipedia.org/wiki/HTTP_Live_Streaming). Isto requer que crie um [Localizador de Streaming](streaming-locators-concept.md) e construa URLs de streaming correspondentes aos protocolos suportados, que podem ser depois entregues a dispositivos/apps com base nas suas capacidades.

O diagrama seguinte mostra o fluxo de trabalho para codificação a pedido com embalagem dinâmica.

![Fluxo de trabalho para codificação a pedido com embalagem dinâmica](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

Este tópico dá-lhe orientação sobre como codificar o seu conteúdo com o Media Services v3.

## <a name="transforms-and-jobs"></a>Transformações e tarefas

Para codificar com os Media Services v3, é necessário criar uma [Transformação](/rest/api/media/transforms) e um [Trabalho.](/rest/api/media/jobs) A transformação define uma receita para as suas definições e saídas codificantes; o trabalho é um exemplo da receita. Para obter mais informações, veja [Transforms and Jobs](transforms-jobs-concept.md) (Transformações e Trabalhos).

Ao codificar com os Serviços de Comunicação, utiliza predefinições para dizer ao codificadora como os ficheiros de meios de entrada devem ser processados. Nos Serviços de Comunicação Social v3, utiliza o Standard Encoder para codificar os seus ficheiros. Por exemplo, pode especificar a resolução de vídeo e/ou o número de canais de áudio que deseja no conteúdo codificado.

Pode começar rapidamente com uma das predefinições recomendadas com base nas melhores práticas da indústria ou pode optar por construir uma predefinição personalizada para direcionar o seu cenário específico ou os requisitos do dispositivo. Para obter mais informações, consulte [Codificar com uma transformação personalizada.](customize-encoder-presets-how-to.md)

A partir de janeiro de 2019, quando codificado com o Standard Encoder para produzir ficheiros MP4, é gerado um novo ficheiro .mpi e adicionado à produção Ativo. Este ficheiro MPI destina-se a melhorar o desempenho para cenários [dinâmicos de embalagem](dynamic-packaging-overview.md) e streaming.

> [!NOTE]
> Não deve modificar ou remover o ficheiro MPI, nem assumir qualquer dependência no seu serviço sobre a existência (ou não) de tal ficheiro.

### <a name="creating-job-input-from-an-https-url"></a>Criação de entrada de emprego a partir de um URL HTTPS

Quando submete Jobs para processar os seus vídeos, tem de dizer aos Media Services onde encontrar o vídeo de entrada. Uma das opções é especificar um URL HTTPS como uma entrada de trabalho. Atualmente, a Media Services v3 não suporta a codificação de transferências em fatias sobre URLs HTTPS.

#### <a name="examples"></a>Exemplos

* [Codificar a partir de um URL HTTPS com .NET](stream-files-dotnet-quickstart.md)
* [Codificar a partir de um URL HTTPS com REST](stream-files-tutorial-with-rest.md)
* [Codificar a partir de um URL HTTPS com CLI](stream-files-cli-quickstart.md)
* [Codificar a partir de um URL HTTPS com Node.js](stream-files-nodejs-quickstart.md)

### <a name="creating-job-input-from-a-local-file"></a>Criando entrada de emprego a partir de um arquivo local

O vídeo de entrada pode ser armazenado como Um Ativo de Serviço de Mídia, caso em que cria um ativo de entrada baseado num ficheiro (armazenado localmente ou no armazenamento Azure Blob).

#### <a name="examples"></a>Exemplos

[Codificar um ficheiro local utilizando predefinições incorporadas](job-input-from-local-file-how-to.md)

### <a name="creating-job-input-with-subclipping"></a>Criação de entrada de emprego com subclipping

Ao codificar um vídeo, pode especificar também para aparar ou cortar o ficheiro de origem e produzir uma saída que tenha apenas uma parte desejada do vídeo de entrada. Esta funcionalidade funciona com qualquer [Transform](/rest/api/media/transforms) que seja construído utilizando as predefinições Predefinidas [BuiltInStandardEncoderPreset](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) ou as predefinições [StandardEncoderPreset.](/rest/api/media/transforms/createorupdate#standardencoderpreset)

Pode especificar para criar um [Jó](/rest/api/media/jobs/create) com um único clip de um vídeo a pedido ou um arquivo ao vivo (um evento gravado). A entrada de trabalho pode ser um Ativo ou um URL HTTPS.

> [!TIP]
> Se pretender transmitir um sublip do seu vídeo sem voltar a codificar o vídeo, considere utilizar [manifestos de pré-filtragem com o Dynamic Packager](filters-dynamic-manifest-overview.md).

#### <a name="examples"></a>Exemplos

Ver exemplos:

* [Subclip um vídeo com .NET](subclip-video-dotnet-howto.md)
* [Subclip um vídeo com REST](subclip-video-rest-howto.md)

## <a name="built-in-presets"></a>Predefinições embutidas

Os Serviços de Comunicação Social suportam as seguintes predefinições codificantes incorporadas:  

### <a name="builtinstandardencoderpreset"></a>Presidente do Condomínio BuiltInStandardEncoderPreset

[O BuiltInStandardEncoderPreset](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) é utilizado para definir uma predefinição incorporada para codificar o vídeo de entrada com o Standard Encoder.

As seguintes predefinições incorporadas são atualmente suportadas:

- **EncoderNamedPreset.AACGoodQualityAudio:** produz um único ficheiro MP4 contendo apenas áudio estéreo codificado a 192 kbps.
- **EncoderNamedPreset.AdaptiveStreaming** (recomendado): Isto suporta a codificação de bitramento adaptativo H.264. Para obter mais informações, consulte [uma escada de bitrate que gera automaticamente.](autogen-bitrate-ladder.md)
- **EncoderNamerPreset.H265AdaptiveStreaming** : Semelhante à pré-sintonia de AdaptiveStreaming, mas utiliza o codec HEVC (H.265). Produz um conjunto de ficheiros MP4 alinhados com vídeo H.265 e áudio AAC estéreo. Gera automaticamente uma escada de bitrate com base na resolução de entrada, bitrate e taxa de fotogramas. A predefinição gerada automaticamente nunca excederá a resolução de entrada. Por exemplo, se a entrada for de 720p, a saída permanecerá 720p na melhor das hipóteses.
- **EncoderNamedPreset.ContentAwareEncoding:** expõe uma predefinição para codificação consciente do conteúdo H.264. Dado qualquer conteúdo de entrada, o serviço tenta determinar automaticamente o número ideal de camadas e definições de bitrate e resolução apropriadas para entrega através do streaming adaptativo. Os algoritmos subjacentes continuarão a evoluir ao longo do tempo. A saída conterá ficheiros MP4 com vídeo e áudio intercalados. Para obter mais informações, consulte [a codificação consciente do conteúdo.](content-aware-encoding.md)
- **EncoderNamedPreset.H265ContentAwareEncoding:** expõe uma predefinição para codificação consciente do conteúdo HEVC (H.265). Produz um conjunto de MP4s alinhados com GOP utilizando codificação consciente do conteúdo. Dado qualquer conteúdo de entrada, o serviço realiza uma análise leve inicial do conteúdo de entrada, e utiliza os resultados para determinar o número ideal de camadas, definições de bitrate e resolução apropriadas para entrega através do streaming adaptativo. Esta predefinição é particularmente eficaz para vídeos de baixa e média complexidade, onde os ficheiros de saída estarão em bitrates mais baixos, mas com uma qualidade que ainda oferece uma boa experiência aos espectadores. A saída conterá ficheiros MP4 com vídeo e áudio intercalados.
  > [!NOTE]
  > Certifique-se de usar **ContentAwareEncoding** não ContentAwareEncodingExperimental que agora é depreciado

- **EncoderNamedPreset.H264MultipleBitrate1080p**: produz um conjunto de oito ficheiros MP4 alinhados com GOP, que variam de 6000 kbps a 400 kbps, e áudio Estéreo AAC. A resolução começa às 1080p e desce para 360p.
- **EncoderNamedPreset.H264MultipleBitrate720p:** produz um conjunto de seis ficheiros MP4 alinhados com GOP, que variam de 3400 kbps a 400 kbps, e áudio Estéreo AAC. A resolução começa em 720p e desce para 360p.
- **EncoderNamedPreset.H264MultipleBitrateSD:** produz um conjunto de cinco ficheiros MP4 alinhados com GOP, que variam entre 1600 kbps e 400 kbps, e áudio AAC estéreo. A resolução começa a 480p e desce para 360p.
- **EncoderNamedPreset.H264SingleBitrate1080p**: produz um ficheiro MP4 onde o vídeo é codificado com código H.264 a 6750 kbps e uma altura de imagem de 1080 pixels, e o áudio estéreo é codificado com código AAC-LC a 64 kbps.
- **EncoderNamedPreset.H264SingleBitrate720p**: produz um ficheiro MP4 onde o vídeo é codificado com código H.264 a 4500 kbps e uma altura de imagem de 720 pixels, e o áudio estéreo é codificado com codec AAC-LC a 64 kbps.
- **EncoderNamedPreset.H264SingleBitrateSD:** produz um ficheiro MP4 onde o vídeo é codificado com código H.264 a 2200 kbps e uma altura de imagem de 480 pixels, e o áudio estéreo é codificado com código AAC-LCc a 64 kbps.
- **EncoderNamedPreset.H265SingleBitrate720P:** produz um ficheiro MP4 onde o vídeo é codificado com código HEVC (H.265) a 1800 kbps e uma altura de imagem de 720 pixels, e o áudio estéreo é codificado com codec AAC-LC a 128 kbps.
- **EncoderNamedPreset.H265SingleBitrate1080p**: produz um ficheiro MP4 onde o vídeo é codificado com código HEVC (H.265) a 3500 kbps e uma altura de imagem de 1080 pixels, e o áudio estéreo é codificado com código AAC-LCC a 128 kbps.
- **EncoderNamedPreset.H265SingleBitrate4K**: produz um ficheiro MP4 onde o vídeo é codificado com código HEVC (H.265) a 9500 kbps e uma altura de imagem de 2160 pixels, e o áudio estéreo é codificado com codec AAC-LC a 128 kbps.

Para ver a lista de predefinições mais atualizadas, consulte [predefinições incorporadas para serem usadas para codificar vídeos](/rest/api/media/transforms/createorupdate#encodernamedpreset).

Para ver como as predefinições são utilizadas, consulte [upload, codificação e ficheiros de streaming](stream-files-tutorial-with-api.md).

### <a name="standardencoderpreset"></a>StandardEncoderPreset

[O StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset) descreve as definições a utilizar ao codificar o vídeo de entrada com o Codificador Standard. Utilize esta predefinição ao personalizar as predefinições transforme.

#### <a name="considerations"></a>Considerações

Ao criar predefinições personalizadas, aplicam-se as seguintes considerações:

- Todos os valores para altura e largura no conteúdo de AVC devem ser múltiplos de quatro.
- No Azure Media Services v3, todos os bitrates codificadores estão em bits por segundo. Isto é diferente das predefinições com as nossas APIs v2, que usavam quilobits/segundo como unidade. Por exemplo, se o bitrate em v2 fosse especificado como 128 (quilobits/segundo), em v3 seria definido para 128000 (bits/segundo).

### <a name="customizing-presets"></a>Personalizar predefinições

Os Serviços de Comunicação Social suportam totalmente a personalização de todos os valores em predefinições para satisfazer as suas necessidades e requisitos específicos de codificação. Para exemplos que mostram como personalizar predefinições codificantes, consulte a lista abaixo:

#### <a name="examples"></a>Exemplos

- [Personalize predefinições com .NET](customize-encoder-presets-how-to.md)
- [Personalize predefinições com CLI](custom-preset-cli-howto.md)
- [Personalize predefinições com REST](custom-preset-rest-howto.md)


## <a name="preset-schema"></a>Esquema predefinido

Nos Serviços de Comunicação Social v3, as predefinições são entidades fortemente dactilografada na própria API. Pode encontrar a definição de "esquema" para estes objetos em [Especificação API Aberta (ou Swagger)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01). Também pode ver as definições predefinidas (como **StandardEncoderPreset)** na [API REST](/rest/api/media/transforms/createorupdate#standardencoderpreset), [.NET SDK](/dotnet/api/microsoft.azure.management.media.models.standardencoderpreset) (ou outros Serviços de Mídia v3 SDK documentação de referência).

## <a name="scaling-encoding-in-v3"></a>Codificação de escalonamento em v3

Para escalar o processamento dos meios de comunicação, consulte [Scale com CLI](media-reserved-units-cli-how-to.md).
Para contas criadas com a versão **2020-05-01** da API ou através do portal Azure, já não são necessárias unidades reservadas para o escalonamento e media. O dimensionamento será automático e manuseado pelo serviço internamente.

## <a name="billing"></a>Faturação

Os Serviços de Comunicação Social não cobram por empregos cancelados ou erros. Por exemplo, um trabalho que tenha atingido 50% de progresso e seja cancelado não é cobrado em 50% das atas de trabalho. Só é cobrado por trabalhos acabados.

Para obter mais informações, veja os [preços](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="ask-questions-give-feedback-get-updates"></a>Faça perguntas, dê feedback, obtenha atualizações

Consulte o artigo da [comunidade Azure Media Services](media-services-community.md) para ver diferentes formas de fazer perguntas, dar feedback e obter atualizações sobre os Media Services.

## <a name="next-steps"></a>Passos seguintes

* [Faça upload, codificação e transmissão utilizando os Serviços de Mídia](stream-files-tutorial-with-api.md).
* [Codificar a partir de um URL HTTPS utilizando predefinições incorporadas](job-input-from-http-how-to.md).
* [Codificar um ficheiro local utilizando predefinições incorporadas](job-input-from-local-file-how-to.md).
* [Construa uma predefinição personalizada para direcionar o seu cenário específico ou requisitos do dispositivo](customize-encoder-presets-how-to.md).
