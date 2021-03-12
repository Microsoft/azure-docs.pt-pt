---
title: Media Encoder Esquema Padrão | Microsoft Docs
description: Este artigo descreve alguns dos elementos e tipos do esquema XML em que se baseiam as predefinições Standard media Encoder.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: f82e0c3f76dba05c3404b11e07c7130119ce0b9d
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103015663"
---
# <a name="media-encoder-standard-schema"></a>Esquema Media Encoder Standard

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Este artigo descreve alguns dos elementos e tipos do esquema XML em que se baseiam [as predefinições Standard media Encoder.](media-services-mes-presets-overview.md) O artigo dá explicação dos elementos e dos seus valores válidos.  

## <a name="preset-root-element"></a><a name="Preset"></a> Predefinição (elemento de raiz)
Define uma predefinição codificante.  

### <a name="elements"></a>Elementos

| Nome | Tipo | Description |
| --- | --- | --- |
| **Encoding** (Codificação) |[Encoding](media-services-mes-schema.md#Encoding) (Codificação) |O elemento raiz indica que as fontes de entrada devem ser codificadas. |
| **Saídas** |[Saídas](media-services-mes-schema.md#Output) |Recolha de ficheiros de saída desejados. |
| **StretchMode**<br/>minOccurs="0"<br/>default="AutoSize|xs:cadeia|Controle o tamanho do quadro de vídeo de saída, o estofamento, o pixel ou a relação de aspeto do ecrã. **O StretchMode** pode ser um dos seguintes valores: **Nenhum,** **AutoSize** (padrão) ou **AutoFit**.<br/><br/>**Nenhum**: Siga rigorosamente a resolução de saída (por exemplo, a **Largura** e **a Altura** na predefinição) sem considerar a relação de aspeto do pixel ou a relação de aspeto do ecrã do vídeo de entrada. Recomendado em cenários como [a colheita,](media-services-crop-video.md)onde o vídeo de saída tem uma relação de aspeto diferente em comparação com a entrada. <br/><br/>**AutoSize**: A resolução de saída caberá no interior da janela (Largura * Altura) especificada por predefinição. No entanto, o codificader produz um vídeo de saída que tem uma relação de aspeto de pixel quadrada (1:1). Portanto, quer a largura de saída quer a altura de saída podem ser ultrapassadas de modo a corresponder à relação de aspeto do visor da entrada, sem estofos. Por exemplo, se a entrada for 1920x1080 e a predefinição codificante pedir 1280x1280, então o valor de Altura na predefinição é ultrapassado, e a saída será de 1280x720, que mantém o rácio de aspeto de entrada de 16:9. <br/><br/>**AutoFit**: Se necessário, apadrinha o vídeo de saída (com caixa de correio ou caixa de pilares) para honrar a resolução de saída desejada, garantindo ao mesmo tempo que a região de vídeo ativa na saída tem o mesmo rácio de aspeto que a entrada. Por exemplo, suponha que a entrada é 1920x1080 e a predefinição de codificação pede 1280x1280. Em seguida, o vídeo de saída será em 1280x1280, mas conterá um retângulo interior de 1280x720 de 'vídeo ativo' com relação aspeto de 16:9, e regiões de letterbox 280 pixels de altura na parte superior e inferior. Por outro exemplo, se a entrada for 1440x1080 e a predefinição codificante pedir 1280x720, então a saída será de 1280x720, que contém um retângulo interno de 960x720 na relação aspeto de 4:3, e regiões de caixa de pilares 160 pixels de largura à esquerda e à direita. 

### <a name="attributes"></a>Atributos

| Nome | Tipo | Description |
| --- | --- | --- |
| **Versão**<br/><br/> Necessário |**xs: decimal** |A versão predefinida. Aplicam-se as seguintes restrições: xs:fraçõesDigits value="1" e xs:minInclusive value="1" Por exemplo, **versão="1.0".** |

## <a name="encoding"></a><a name="Encoding"></a> Codificação
Contém uma sequência dos seguintes elementos:  

### <a name="elements"></a>Elementos

| Nome | Tipo | Description |
| --- | --- | --- |
| **H264Video** |[H264Video](media-services-mes-schema.md#H264Video) |Definições para codificação de vídeo H.264. |
| **AACAudio** |[AACAudio](media-services-mes-schema.md#AACAudio) |Definições para codificação de áudio AAC. |
| **BmpImage** |[BmpImage](media-services-mes-schema.md#BmpImage) |Definições para imagem Bmp. |
| **PngImage** |[PngImage](media-services-mes-schema.md#PngImage) |Definições para imagem png. |
| **JpgImage** |[JpgImage](media-services-mes-schema.md#JpgImage) |Definições para a imagem jpg. |

## <a name="h264video"></a><a name="H264Video"></a> H264Video
### <a name="elements"></a>Elementos

| Nome | Tipo | Description |
| --- | --- | --- |
| **TwoPass**<br/><br/> minOccurs="0" |**xs:boolean** |Atualmente, apenas uma codificação de um passe é suportada. |
| **KeyFrameInterval**<br/><br/> minOccurs="0"<br/><br/> **default="00:00:02"** |**xs:tempo** |Determina o espaçamento fixo entre os quadros do IDR em unidades de segundos. Também referida como a duração do GOP. Consulte **SceneChangeDetection** para controlar se o codificador pode desviar-se deste valor. |
| **CenaChangeDetection**<br/><br/> minOccurs="0"<br/><br/> default="falso" |**xs: boolean** |Se for definido como verdadeiro, o codificador tenta detetar a mudança de cena no vídeo e insere uma moldura IDR. |
| **Complexidade**<br/><br/> minOccurs="0"<br/><br/> default="Equilibrado" |**xs:cadeia** |Controla a troca entre a velocidade de codificação e a qualidade do vídeo. Pode ser um dos seguintes valores: **Velocidade,** **Equilíbrio** ou **Qualidade**<br/><br/> Padrão: **Equilibrado** |
| **SyncMode**<br/><br/> minOccurs="0" | |A funcionalidade será exposta num lançamento futuro. |
| **H264Layers**<br/><br/> minOccurs="0" |[H264Layers](media-services-mes-schema.md#H264Layers) |Coleção de camadas de vídeo de saída. |

### <a name="attributes"></a>Atributos

| Nome | Tipo | Description |
| --- | --- | --- |
| **Condition** |**xs:cadeia** | Quando a entrada não tiver vídeo, é melhor forçar o codificador a inserir uma faixa de vídeo monocromática. Para isso, utilize Condition="InsertBlackIfNoVideoBottomLayerOnly" (para inserir um vídeo apenas no bitrate mais baixo) ou Condition="InsertBlackIfNoVideo" (para inserir um vídeo em todos os bitrates de saída). Para mais informações, consulte [este](media-services-advanced-encoding-with-mes.md#no_video) artigo.|

## <a name="h264layers"></a><a name="H264Layers"></a> H264Layers

Por predefinição, se enviar uma entrada para o codificar que contém apenas áudio, e nenhum vídeo, o ativo de saída contém ficheiros apenas com dados áudio. Alguns jogadores podem não ser capazes de lidar com tais fluxos de saída. Pode utilizar a definição de atributo **InsertBlackIfNoVideo** do H264Video para forçar o codificador a adicionar uma faixa de vídeo à saída nesse cenário. Para mais informações, consulte [este](media-services-advanced-encoding-with-mes.md#no_video) artigo.
              
### <a name="elements"></a>Elementos

| Nome | Tipo | Description |
| --- | --- | --- |
| **H264Layer**<br/><br/> minOccurs="0" maxOccurs="unbounded" |[H264Layer](media-services-mes-schema.md#H264Layer) |Uma coleção de camadas H264. |

## <a name="h264layer"></a><a name="H264Layer"></a> H264Layer
> [!NOTE]
> Os limites de vídeo baseiam-se nos valores descritos na tabela [Níveis H264.](https://en.wikipedia.org/wiki/H.264/MPEG-4_AVC#Levels)  
> 
> 

### <a name="elements"></a>Elementos

| Nome | Tipo | Description |
| --- | --- | --- |
| **Perfil**<br/><br/> minOccurs="0"<br/><br/> default="Auto" |**xs: corda** |Pode ser de um dos **seguintes xs: valores** de cordas: **Auto,** **Baseline,** **Main,** **High**. |
| **Nível**<br/><br/> minOccurs="0"<br/><br/> default="Auto" |**xs: corda** | |
| **Bitrate**<br/><br/> minOccurs="0" |**xs:int** |O bitrate usado para esta camada de vídeo, especificado em kbps. |
| **MaxBitrate**<br/><br/> minOccurs="0" |**xs: int** |O bitrate máximo utilizado para esta camada de vídeo, especificado em kbps. |
| **BufferWindow**<br/><br/> minOccurs="0"<br/><br/> default="00:00:05" |**xs: tempo** |Comprimento do tampão de vídeo. |
| **Width**<br/><br/> minOccurs="0" |**xs: int** |Largura da moldura de vídeo de saída, em pixels.<br/><br/> Atualmente, deve especificar tanto a largura como a altura. A Largura e Altura têm de ser números pares. |
| **Height**<br/><br/> minOccurs="0" |**xs:int** |Altura da estrutura de vídeo de saída, em pixels.<br/><br/> Atualmente, deve especificar tanto a largura como a altura. A Largura e Altura têm de ser números pares.|
| **BFrames**<br/><br/> minOccurs="0" |**xs: int** |Número de quadros B entre quadros de referência. |
| **Quadros de Referência**<br/><br/> minOccurs="0"<br/><br/> default="3" |**xs:int** |Número de quadros de referência num GOP. |
| **EntropiaMode**<br/><br/> minOccurs="0"<br/><br/> default="Cabac" |**xs: corda** |Pode ser um dos seguintes valores: **Cabac** e **Cavlc**. |
| **FrameRate**<br/><br/> minOccurs="0" |número racional |Determina a taxa de fotogramas do vídeo de saída. Utilize o padrão de "0/1" para permitir que o codificar utilize a mesma taxa de fotogramas que o vídeo de entrada. Espera-se que os valores permitidos sejam taxas comuns de fotogramas de vídeo. No entanto, qualquer racionalidade válida é permitida. Por exemplo, 1/1 seria 1 fps e é válido.<br/><br/> - 12/1 (12 fps)<br/><br/> - 15/1 (15 fps)<br/><br/> - 24/1 (24 fps)<br/><br/> - 24000/1001 (23.976 fps)<br/><br/> - 25/1 (25 fps)<br/><br/>  - 30/1 (30 fps)<br/><br/> - 30000/1001 (29,97 fps) <br/> <br/>**NOTA** Se estiver a criar uma predefinição personalizada para codificação de bitramentos múltiplos, então todas as camadas da predefinição **devem** utilizar o mesmo valor de FrameRate.|
| **AdaptiveBFrame**<br/><br/> minOccurs="0" |**xs: boolean** |Cópia do codificador de meios de comunicação Azure |
| **Segmentações**<br/><br/> minOccurs="0"<br/><br/> padrão="0" |**xs:int** |Determina quantas fatias uma moldura é dividida. Recomendo a utilização por defeito. |

## <a name="aacaudio"></a><a name="AACAudio"></a> AACAudio
 Contém uma sequência dos seguintes elementos e grupos.  

 Para mais informações sobre a AAC, consulte [a AAC.](https://en.wikipedia.org/wiki/Advanced_Audio_Coding)  

### <a name="elements"></a>Elementos

| Nome | Tipo | Description |
| --- | --- | --- |
| **Perfil**<br/><br/> minOccurs="0"<br/><br/> default="AACLC" |**xs: corda** |Pode ser um dos seguintes valores: **AACLC,** **HEAACV1,** ou **HEAACV2**. |

### <a name="attributes"></a>Atributos

| Nome | Tipo | Description |
| --- | --- | --- |
| **Condition** |**xs: corda** |Para forçar o codificador a produzir um ativo que contenha uma faixa de áudio silenciosa quando a entrada não tiver áudio, especifique o valor "InsertSilenceIfNoAudio".<br/><br/> Por predefinição, se enviar uma entrada para o codificar que contém apenas vídeo e nenhum áudio, então o ativo de saída contém ficheiros que contêm apenas dados de vídeo. Alguns jogadores podem não ser capazes de lidar com tais fluxos de saída. Pode utilizar esta definição para forçar o codificar a adicionar uma faixa de áudio silenciosa à saída nesse cenário. |

### <a name="groups"></a>Grupos

| Referência | Description |
| --- | --- |
| [AudioGroup](media-services-mes-schema.md#AudioGroup)<br/><br/> minOccurs="0" |Consulte a descrição do [AudioGroup](media-services-mes-schema.md#AudioGroup) para saber o número adequado de canais, taxa de amostragem e taxa de bits que poderiam ser definidos para cada perfil. |

## <a name="audiogroup"></a><a name="AudioGroup"></a> AudioGroup
Para obter mais informações sobre os valores válidos para cada perfil, consulte a tabela "Detalhes do código áudio" que se segue.  

### <a name="elements"></a>Elementos

| Nome | Tipo | Description |
| --- | --- | --- |
| **Channels** (Canais)<br/><br/> minOccurs="0" |**xs: int** |O número de canais de áudio codificados. Seguem-se opções válidas: 1, 2, 5, 6, 8.<br/><br/> Predefinição: 2. |
| **AmostragemRate**<br/><br/> minOccurs="0" |**xs: int** |A taxa de amostragem de áudio, especificada em Hz. |
| **Bitrate**<br/><br/> minOccurs="0" |**xs: int** |O bitrate utilizado ao codificar o áudio, especificado em kbps. |

### <a name="audio-codec-details"></a>Detalhes do codec áudio

Codec áudio|Detalhes  
-----------------|---  
**AACLC** |1:<br/><br/> - 11025: 8 &lt; = bitrate &lt; 16<br/><br/> - 12000: 8 &lt; = bitrate &lt; 16<br/><br/> - 16000: 8 &lt; = bitrate &lt; 32<br/><br/>- 22050: 24 &lt; = bitrate &lt; 32<br/><br/> - 24000: 24 &lt; = bitrate &lt; 32<br/><br/> - 32000: 32 &lt; = bitrate &lt; = 192<br/><br/> - 44100: 56 &lt; = bitrate &lt; = 288<br/><br/> - 48000: 56 &lt; = bitrate &lt; = 288<br/><br/> - 88200 : 128 &lt; = bitrate &lt; = 288<br/><br/> - 96000 : 128 &lt; = bitrate &lt; = 288<br/><br/> 2:<br/><br/> - 11025: 16 &lt; = bitrate &lt; 24<br/><br/> - 12000: 16 &lt; = bitrate &lt; 24<br/><br/> - 16000: 16 &lt; = bitrate &lt; 40<br/><br/> - 22050: 32 &lt; = bitrate &lt; 40<br/><br/> - 24000: 32 &lt; = bitrate &lt; 40<br/><br/> - 32000: 40 &lt; = bitrate &lt; = 384<br/><br/> - 44100: 96 &lt; = bitrate &lt; = 576<br/><br/> - 48000: 96 &lt; = bitrate &lt; = 576<br/><br/> - 88200: 256 &lt; = bitrate &lt; = 576<br/><br/> - 96000: 256 &lt; = bitrate &lt; = 576<br/><br/> 5/6:<br/><br/> - 32000: 160 &lt; = bitrate &lt; = 896<br/><br/> - 44100: 240 &lt; = bitrate &lt; = 1024<br/><br/> - 48000: 240 &lt; = bitrate &lt; = 1024<br/><br/> - 88200: 640 &lt; = bitrate &lt; = 1024<br/><br/> - 96000: 640 &lt; = bitrate &lt; = 1024<br/><br/> 8:<br/><br/> - 32000 : 224 &lt; = bitrate &lt; = 1024<br/><br/> - 44100 : 384 &lt; = bitrate &lt; = 1024<br/><br/> - 48000: 384 &lt; = bitrate &lt; = 1024<br/><br/> - 88200: 896 &lt; = bitrate &lt; = 1024<br/><br/> - 96000: 896 &lt; = bitrate &lt; = 1024  
**HEAACV1** |1:<br/><br/> - 22050: bitrate = 8<br/><br/> - 24000: 8 &lt; = bitrate &lt; = 10<br/><br/> - 32000: 12 &lt; = bitrate &lt; = 64<br/><br/> - 44100: 20 &lt; = bitrate &lt; = 64<br/><br/> - 48000: 20 &lt; = bitrate &lt; = 64<br/><br/> - 88200: bitrate = 64<br/><br/> 2:<br/><br/> - 32000: 16 &lt; = bitrate &lt; = 128<br/><br/> - 44100: 16 &lt; = bitrate &lt; = 128<br/><br/> - 48000: 16 &lt; = bitrate &lt; = 128<br/><br/> - 88200: 96 &lt; = bitrate &lt; = 128<br/><br/> - 96000: 96 &lt; = bitrate &lt; = 128<br/><br/> 5/6:<br/><br/> - 32000 : 64 &lt; = bitrate &lt; = 320<br/><br/> - 44100: 64 &lt; = bitrate &lt; = 320<br/><br/> - 48000: 64 &lt; = bitrate &lt; = 320<br/><br/> - 88200: 256 &lt; = bitrate &lt; = 320<br/><br/> - 96000: 256 &lt; = bitrate &lt; = 320<br/><br/> 8:<br/><br/> - 32000: 96 &lt; = bitrate &lt; = 448<br/><br/> - 44100: 96 &lt; = bitrate &lt; = 448<br/><br/> - 48000: 96 &lt; = bitrate &lt; = 448<br/><br/> - 88200: 384 &lt; = bitrate &lt; = 448<br/><br/> - 96000: 384 &lt; = bitrate &lt; = 448  
**HEAACV2** |2:<br/><br/> - 22050: 8 &lt; = bitrate &lt; = 10<br/><br/> - 24000: 8 &lt; = bitrate &lt; = 10<br/><br/> - 32000: 12 &lt; = bitrate &lt; = 64<br/><br/> - 44100: 20 &lt; = bitrate &lt; = 64<br/><br/> - 48000: 20 &lt; = bitrate &lt; = 64<br/><br/> - 88200: 64 &lt; = bitrate &lt; = 64  
  
## <a name="clip"></a><a name="Clip"></a> Clip
### <a name="attributes"></a>Atributos

| Nome | Tipo | Description |
| --- | --- | --- |
| **Horário de início** |**xs:duração** |Especifica a hora de início de uma apresentação. O valor do StartTime tem de corresponder aos intervalos de tempo absolutos do vídeo de entrada. Por exemplo, se o primeiro quadro do vídeo de entrada tiver um tempotampido de 12:00:10.000, então o StartTime deve ser pelo menos 12:00:10.000 ou maior. |
| **Duration** |**xs:duração** |Especifica a duração de uma apresentação (por exemplo, aparecimento de uma sobreposição no vídeo). |

## <a name="output"></a><a name="Output"></a> Saída
### <a name="attributes"></a>Atributos

| Nome | Tipo | Description |
| --- | --- | --- |
| **Nome de arquivo** |**xs:cadeia** |O nome do ficheiro de saída.<br/><br/> Pode utilizar macros descritas na tabela seguinte para construir os nomes dos ficheiros de saída. Por exemplo:<br/><br/> **"Saídas": [ {"FileName": "{Basename}*{Resolução}*{Bitrate}.mp4", "Formato": { "Tipo": "MP4Format" } }** |

### <a name="macros"></a>Macros

| Macro | Description |
| --- | --- |
| **{Nome base}** |Se estiver a fazer a codificação VoD, o {Basename} é o primeiro AssetFile.Name propriedade do ficheiro primário no ativo de entrada.<br/><br/> Se o ativo de entrada for um arquivo ao vivo, então o {Basename} é derivado dos atributos trackName no manifesto do servidor. Se estiver a submeter um trabalho de subclip usando o TopBitrate, como em: "<VideoStream \> TopBitrate</VideoStream \> ", e o ficheiro de saída contém vídeo, então o {Basename} é o primeiro 32 caracteres da faixaName da camada de vídeo com o bitrate mais elevado.<br/><br/> Se em vez disso estiver a submeter um trabalho de subclip utilizando todos os bitrates de entrada, tais como "<VideoStream \> *</VideoStream \> ", e o ficheiro de saída contiver vídeo, então {Basename} são os primeiros 32 caracteres da faixaName da camada de vídeo correspondente. |
| **{Codec}** |Mapas para "H264" para vídeo e "AAC" para áudio. |
| **{Bitrate}** |O bitrate de vídeo-alvo se o ficheiro de saída contiver vídeo e áudio, ou o bitrate de áudio alvo se o ficheiro de saída contiver apenas áudio. O valor utilizado é o bitrate em kbps. |
| **{Canal}** |Contagem de canais de áudio se o ficheiro contiver áudio. |
| **{Largura}** |Largura do vídeo, em pixels, no ficheiro de saída, se o ficheiro contiver vídeo. |
| **{Altura}** |Altura do vídeo, em pixels, no ficheiro de saída, se o ficheiro contiver vídeo. |
| **{Extensão}** |Herda da propriedade "Tipo" para o ficheiro de saída. O nome do ficheiro de saída tem uma extensão que é uma de: "mp4", "ts", "jpg", "png" ou "bmp". |
| **{Índice}** |Obrigatório para miniaturas. Só deve estar presente uma vez. |

## <a name="video-complex-type-inherits-from-codec"></a><a name="Video"></a> Vídeo (tipo complexo herdado do Codec)
### <a name="attributes"></a>Atributos

| Nome | Tipo | Description |
| --- | --- | --- |
| **Iniciar** |**xs:cadeia** | |
| **Passo** |**xs:cadeia** | |
| **Intervalo** |**xs:cadeia** | |
| **PreservarResolutionAfterRotation** |**xs:boolean** |Para obter uma explicação detalhada, consulte a seguinte secção: [PreserveResolutionAfterRotation](media-services-mes-schema.md#PreserveResolutionAfterRotation) |

### <a name="preserveresolutionafterrotation"></a><a name="PreserveResolutionAfterRotation"></a> PreservarResolutionAfterRotation
Recomenda-se a utilização da bandeira **PreserveResolutionAfterRotation** em combinação com os valores de resolução expressos em termos percentuais (Largura="100%" , Altura="100%").  

Por predefinição, as definições de resolução de codificação (Largura, Altura) nas predefinições media Encoder Standard (MES) são direcionadas para vídeos com rotação de 0 graus. Por exemplo, se o seu vídeo de entrada for de 1280x720 com rotação de zero graus, então as predefinições predefinidas asseguram que a saída tem a mesma resolução.  

![MESRoation1](./media/media-services-shemas/media-services-mes-roation1.png) 

Se o vídeo de entrada tiver sido capturado com rotação não-zero (por exemplo, um smartphone ou tablet mantido verticalmente), então o MES por defeito aplica as definições de resolução de codificação (Largura, Altura) ao vídeo de entrada e, em seguida, compensa a rotação. Por exemplo, veja a imagem que se segue. O predefinitivo usa largura = "100%", altura = "100%", que a MES interpreta como exigindo que a saída tenha 1280 pixels de largura e 720 pixels de altura. Depois de rodar o vídeo, encolhe a imagem para caber naquela janela, levando a áreas de caixa de pilares à esquerda e à direita.  

![MESRoation2](./media/media-services-shemas/media-services-mes-roation2.png) 

Em alternativa, pode utilizar a bandeira **de PreservaResolutionAfterRotation** e defini-la como "verdadeira" (o padrão é "falso"). Assim, se a sua predefinição tem Largura = "100%", Altura = "100%" e PreserveResolutionAfterRotation definido para "verdadeiro", um vídeo de entrada, que tem 1280 pixels de largura e 720 pixels de altura com rotação de 90 graus produz uma saída com rotação de zero graus, mas 720 pixels de largura e 1280 pixels de altura. Veja a seguinte imagem:  

![MESRoation3](./media/media-services-shemas/media-services-mes-roation3.png) 

## <a name="formatgroup-group"></a><a name="FormatGroup"></a> Grupo de Formato (grupo)
### <a name="elements"></a>Elementos

| Nome | Tipo | Description |
| --- | --- | --- |
| **BmpFormat** |**BmpFormat** | |
| **PngFormat** |**PngFormat** | |
| **JpgFormat** |**JpgFormat** | |

## <a name="bmplayer"></a><a name="BmpLayer"></a> BmpLayer
### <a name="element"></a>Elemento

| Nome | Tipo | Description |
| --- | --- | --- |
| **Width**<br/><br/> minOccurs="0" |**xs:int** | |
| **Height**<br/><br/> minOccurs="0" |**xs:int** | |

### <a name="attributes"></a>Atributos

| Nome | Tipo | Description |
| --- | --- | --- |
| **Condition** |**xs:cadeia** | |

## <a name="pnglayer"></a><a name="PngLayer"></a> PngLayer
### <a name="element"></a>Elemento

| Nome | Tipo | Description |
| --- | --- | --- |
| **Width**<br/><br/> minOccurs="0" |**xs:int** | |
| **Height**<br/><br/> minOccurs="0" |**xs:int** | |

### <a name="attributes"></a>Atributos

| Nome | Tipo | Description |
| --- | --- | --- |
| **Condition** |**xs:cadeia** | |

## <a name="jpglayer"></a><a name="JpgLayer"></a> JpgLayer
### <a name="element"></a>Elemento

| Nome | Tipo | Description |
| --- | --- | --- |
| **Width**<br/><br/> minOccurs="0" |**xs:int** | |
| **Height**<br/><br/> minOccurs="0" |**xs:int** | |
| **Qualidade**<br/><br/> minOccurs="0" |**xs:int** |Valores válidos: 1(pior)-100 (melhor) |

### <a name="attributes"></a>Atributos

| Nome | Tipo | Description |
| --- | --- | --- |
| **Condition** |**xs:cadeia** | |

## <a name="pnglayers"></a><a name="PngLayers"></a> PngLayers
### <a name="elements"></a>Elementos

| Nome | Tipo | Description |
| --- | --- | --- |
| **PngLayer**<br/><br/> minOccurs="0" maxOccurs="unbounded" |[PngLayer](media-services-mes-schema.md#PngLayer) | |

## <a name="bmplayers"></a><a name="BmpLayers"></a> BmpLayers
### <a name="elements"></a>Elementos

| Nome | Tipo | Description |
| --- | --- | --- |
| **BmpLayer**<br/><br/> minOccurs="0" maxOccurs="unbounded" |[BmpLayer](media-services-mes-schema.md#BmpLayer) | |

## <a name="jpglayers"></a><a name="JpgLayers"></a> JpgLayers
### <a name="elements"></a>Elementos

| Nome | Tipo | Description |
| --- | --- | --- |
| **JpgLayer**<br/><br/> minOccurs="0" maxOccurs="unbounded" |[JpgLayer](media-services-mes-schema.md#JpgLayer) | |

## <a name="bmpimage-complex-type-inherits-from-video"></a><a name="BmpImage"></a> BmpImage (tipo complexo herdado de Vídeo)
### <a name="elements"></a>Elementos

| Nome | Tipo | Description |
| --- | --- | --- |
| **PngLayers**<br/><br/> minOccurs="0" |[PngLayers](media-services-mes-schema.md#PngLayers) |Camadas de png |

## <a name="jpgimage-complex-type-inherits-from-video"></a><a name="JpgImage"></a> JpgImage (tipo complexo herdado de Vídeo)
### <a name="elements"></a>Elementos

| Nome | Tipo | Description |
| --- | --- | --- |
| **PngLayers**<br/><br/> minOccurs="0" |[PngLayers](media-services-mes-schema.md#PngLayers) |Camadas de png |

## <a name="pngimage-complex-type-inherits-from-video"></a><a name="PngImage"></a> PngImage (tipo complexo herdado de Vídeo)
### <a name="elements"></a>Elementos

| Nome | Tipo | Description |
| --- | --- | --- |
| **PngLayers**<br/><br/> minOccurs="0" |[PngLayers](media-services-mes-schema.md#PngLayers) |Camadas de png |

## <a name="examples"></a>Exemplos
Consulte exemplos de predefinições XML que são construídas com base neste esquema, consulte [Predefinições de Tarefa para MES (Media Encoder Standard)](media-services-mes-presets-overview.md).

## <a name="next-steps"></a>Passos seguintes
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

