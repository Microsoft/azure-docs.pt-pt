---
title: Media Encoder Standard schema [ Microsoft Docs
description: Este artigo descreve alguns dos elementos e tipos do esquema XML em que se baseiam as predefinições padrão do Media Encoder.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 622f14beabb1f2f109dff5d28c1591ffdd5aa000
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74901442"
---
# <a name="media-encoder-standard-schema"></a>Esquema Media Encoder Standard
Este artigo descreve alguns dos elementos e tipos do esquema XML em que se baseiam as [predefinições padrão do Media Encoder.](media-services-mes-presets-overview.md) O artigo dá explicação aos elementos e aos seus valores válidos.  

## <a name="preset-root-element"></a><a name="Preset"></a>Predefinição (elemento raiz)
Define um predefinição de codificação.  

### <a name="elements"></a>Elementos

| Nome | Tipo | Descrição |
| --- | --- | --- |
| **Codificação** |[Codificação](media-services-mes-schema.md#Encoding) |Elemento raiz, indica que as fontes de entrada devem ser codificadas. |
| **Saídas** |[Saídas](media-services-mes-schema.md#Output) |Recolha de ficheiros de saída desejados. |
| **Alongamento**<br/>minOccurs="0"<br/>padrão="AutoSize|xs:corda|Controle o tamanho do quadro de vídeo de saída, acolchoamento, pixel ou relação de aspeto de exibição. **O StretchMode** pode ser um dos seguintes valores: **Nenhum,** **autosize** (predefinido) ou **AutoFit**.<br/><br/>**Nenhuma**: Siga rigorosamente a resolução de saída (por exemplo, a **Largura** e **a Altura** no predefinido) sem considerar a relação de aspeto de pixel ou a relação de aspeto de visualização do vídeo de entrada. Recomendado em cenários como [a colheita](media-services-crop-video.md), onde o vídeo de saída tem uma relação de aspeto diferente em comparação com a entrada. <br/><br/>**Tamanho automático**: A resolução de saída caberá dentro da janela (Largura * Altura) especificada por predefinição. No entanto, o codificador produz um vídeo de saída que tem uma relação de aspeto de pixel quadrado (1:1). Por conseguinte, quer a largura de saída quer a potência a altura podem ser ultrapassadas de modo a corresponder à relação de aspeto de visualização da entrada, sem estofamento. Por exemplo, se a entrada for 1920x1080 e o predefinição de codificação pedir 1280x1280, então o valor altura no predefinição é ultrapassado, e a saída será de 1280x720, o que mantém o rácio de aspeto de entrada de 16:9. <br/><br/>**AutoFit**: Se necessário, remare o vídeo de saída (com caixa de correio ou caixa de pilares) para honrar a resolução de saída desejada, garantindo ao mesmo tempo que a região de vídeo ativa na saída tem a mesma relação de aspeto que a entrada. Por exemplo, suponha que a entrada é 1920x1080 e o predefinição de codificação pede 1280x1280. Em seguida, o vídeo de saída será em 1280x1280, mas conterá um retângulo interior de 1280x720 de 'vídeo ativo' com relação de aspeto de 16:9, e regiões de caixa de letras 280 pixels de altura na parte superior e inferior. Por outro exemplo, se a entrada for de 1440x1080 e o predefinido de codificação pedir 1280x720, então a saída será de 1280x720, que contém um retângulo interno de 960x720 no rácio de aspeto de 4:3, e regiões de caixa de pilares 160 pixels de largura à esquerda e à direita. 

### <a name="attributes"></a>Atributos

| Nome | Tipo | Descrição |
| --- | --- | --- |
| **Versão**<br/><br/> Necessário |**xs: decimal** |A versão predefinida. Aplicam-se as seguintes restrições: xs:fraçãoDigits value="1" e xs:minInclusive value="1" Por exemplo, **versão="1.0".** |

## <a name="encoding"></a><a name="Encoding"></a>Codificação
Contém uma sequência dos seguintes elementos:  

### <a name="elements"></a>Elementos

| Nome | Tipo | Descrição |
| --- | --- | --- |
| **H264Video** |[H264Video](media-services-mes-schema.md#H264Video) |Definições para codificação de vídeo H.264. |
| **AACAudio** |[AACAudio](media-services-mes-schema.md#AACAudio) |Definições para codificação de áudio AAC. |
| **BmpImage** |[BmpImage](media-services-mes-schema.md#BmpImage) |Definições para imagem Bmp. |
| **PngImage** |[PngImage](media-services-mes-schema.md#PngImage) |Definições para imagem png. |
| **JpgImage** |[JpgImage](media-services-mes-schema.md#JpgImage) |Definições para imagem jpg. |

## <a name="h264video"></a><a name="H264Video"></a>H264Video
### <a name="elements"></a>Elementos

| Nome | Tipo | Descrição |
| --- | --- | --- |
| **Passagem Dupla**<br/><br/> minOccurs="0" |**xs:boolean** |Atualmente, apenas a codificação de um passe é suportada. |
| **Intervalo de quadro de teclado**<br/><br/> minOccurs="0"<br/><br/> **padrão="00:00:02"** |**xs:tempo** |Determina o espaçamento fixo entre os quadros de IDR em unidades de segundos. Também referida como a duração do GOP. Consulte o **SceneChangeDetection** para controlar se o codificador pode desviar-se deste valor. |
| **Deteção de Mudanças de Cena**<br/><br/> minOccurs="0"<br/><br/> padrão="falso" |**xs: boolean** |Se for definido como verdadeiro, o codificador tenta detetar a mudança de cena no vídeo e insere uma moldura IDR. |
| **Complexidade**<br/><br/> minOccurs="0"<br/><br/> padrão="Equilibrado" |**xs:corda** |Controla a compensação entre a velocidade de codificação e a qualidade do vídeo. Pode ser um dos seguintes valores: **Velocidade,** **Equilíbrio**ou **Qualidade**<br/><br/> Padrão: **Equilibrado** |
| **SyncMode**<br/><br/> minOccurs="0" | |A funcionalidade será exposta num futuro lançamento. |
| **H264Layers**<br/><br/> minOccurs="0" |[H264Layers](media-services-mes-schema.md#H264Layers) |Coleção de camadas de vídeo de saída. |

### <a name="attributes"></a>Atributos

| Nome | Tipo | Descrição |
| --- | --- | --- |
| **Condição** |**xs:corda** | Quando a entrada não tiver vídeo, é melhor forçar o codificador a inserir uma faixa de vídeo monocromática. Para isso, utilize o Condição="InsertBlackIfNoVideoBottomLayerOnly" (para inserir um vídeo apenas no menor bitrate) ou Condição="InsertBlackIfNoVideo" (para inserir um vídeo em todas as bitrates de saída). Para mais informações, consulte [este](media-services-advanced-encoding-with-mes.md#no_video) artigo.|

## <a name="h264layers"></a><a name="H264Layers"></a>H264Layers

Por predefinição, se enviar uma entrada para o codificador que contenha apenas áudio, e nenhum vídeo, o ativo de saída contém ficheiros apenas com dados áudio. Alguns jogadores podem não ser capazes de lidar com estes fluxos de saída. Pode utilizar a definição de atributo **Inserção Do** H264Video Para forçar o codificador a adicionar uma faixa de vídeo à saída nesse cenário. Para mais informações, consulte [este](media-services-advanced-encoding-with-mes.md#no_video) artigo.
              
### <a name="elements"></a>Elementos

| Nome | Tipo | Descrição |
| --- | --- | --- |
| **Camada H264**<br/><br/> minOccurs="0" maxOccurs="unbounded" |[Camada H264](media-services-mes-schema.md#H264Layer) |Uma coleção de camadas H264. |

## <a name="h264layer"></a><a name="H264Layer"></a>Camada H264
> [!NOTE]
> Os limites de vídeo baseiam-se nos valores descritos na tabela [Níveis H264.](https://en.wikipedia.org/wiki/H.264/MPEG-4_AVC#Levels)  
> 
> 

### <a name="elements"></a>Elementos

| Nome | Tipo | Descrição |
| --- | --- | --- |
| **Perfil**<br/><br/> minOccurs="0"<br/><br/> padrão="Auto" |**xs: corda** |Pode ser de um dos **seguintes xs: valores** de cordas: **Auto,** **Baseline**, **Main**, **High**. |
| **Nível**<br/><br/> minOccurs="0"<br/><br/> padrão="Auto" |**xs: corda** | |
| **Bitrate**<br/><br/> minOccurs="0" |**xs:int** |O bitrate utilizado para esta camada de vídeo, especificado em kbps. |
| **MaxBitrate**<br/><br/> minOccurs="0" |**xs: int** |A bitrate máxima utilizada para esta camada de vídeo, especificada em kbps. |
| **Janela tampão**<br/><br/> minOccurs="0"<br/><br/> padrão="00:00:05" |**xs: tempo** |Comprimento do tampão de vídeo. |
| **Width**<br/><br/> minOccurs="0" |**xs: int** |Largura da moldura de vídeo de saída, em pixels.<br/><br/> Atualmente, deve especificar a largura e a altura. A Largura e a Altura têm de ser números pares. |
| **Height**<br/><br/> minOccurs="0" |**xs:int** |Altura da moldura de vídeo de saída, em pixels.<br/><br/> Atualmente, deve especificar a largura e a altura. A Largura e a Altura têm de ser números pares.|
| **BFrames**<br/><br/> minOccurs="0" |**xs: int** |Número de quadros B entre quadros de referência. |
| **ReferenciaisFrames**<br/><br/> minOccurs="0"<br/><br/> padrão="3" |**xs:int** |Número de quadros de referência num GOP. |
| **EntropiaMode**<br/><br/> minOccurs="0"<br/><br/> padrão="Cabac" |**xs: corda** |Pode ser um dos seguintes valores: **Cabac** e **Cavlc.** |
| **FrameRate**<br/><br/> minOccurs="0" |número racional |Determina a taxa de fotogramas do vídeo de saída. Utilize o padrão de "0/1" para permitir que o codificador utilize a mesma taxa de fotogramas que o vídeo de entrada. Espera-se que os valores permitidos sejam taxas comuns de fotogramas de vídeo. No entanto, qualquer racional válido é permitido. Por exemplo, 1/1 seria 1 fps e é válido.<br/><br/> - 12/1 (12 fps)<br/><br/> - 15/1 (15 fps)<br/><br/> - 24/1 (24 fps)<br/><br/> - 24000/1001 (23.976 fps)<br/><br/> - 25/1 (25 fps)<br/><br/>  - 30/1 (30 fps)<br/><br/> - 30000/1001 (29,97 fps) <br/> <br/>**NOTA** Se estiver a criar um predefinição personalizado para codificação de vários bitrates, então todas as camadas do preset **devem** usar o mesmo valor de FrameRate.|
| **AdaptiveBFrame**<br/><br/> minOccurs="0" |**xs: boolean** |Cópia do codificador de mídia Azure |
| **Fatias**<br/><br/> minOccurs="0"<br/><br/> padrão="0" |**xs:int** |Determina quantas fatias uma moldura está dividida. Recomende a utilização de predefinição. |

## <a name="aacaudio"></a><a name="AACAudio"></a>AACAudio
 Contém uma sequência dos seguintes elementos e grupos.  

 Para mais informações sobre a AAC, consulte [a AAC.](https://en.wikipedia.org/wiki/Advanced_Audio_Coding)  

### <a name="elements"></a>Elementos

| Nome | Tipo | Descrição |
| --- | --- | --- |
| **Perfil**<br/><br/> minOccurs="0 "<br/><br/> padrão="AACLC" |**xs: corda** |Pode ser um dos seguintes valores: **AACLC,** **HEAACV1**ou **HEAACV2**. |

### <a name="attributes"></a>Atributos

| Nome | Tipo | Descrição |
| --- | --- | --- |
| **Condição** |**xs: corda** |Para forçar o codificador a produzir um ativo que contenha uma faixa de áudio silenciosa quando a entrada não tiver áudio, especifique o valor "InsertSilenceIfNoAudio".<br/><br/> Por predefinição, se enviar uma entrada para o codificador que contém apenas vídeo, e nenhum áudio, então o ativo de saída contém ficheiros que contêm apenas dados de vídeo. Alguns jogadores podem não ser capazes de lidar com estes fluxos de saída. Pode utilizar esta definição para forçar o codificador a adicionar uma faixa de áudio silenciosa à saída nesse cenário. |

### <a name="groups"></a>Grupos

| Referência | Descrição |
| --- | --- |
| [Grupo de Áudio](media-services-mes-schema.md#AudioGroup)<br/><br/> minOccurs="0" |Consulte a descrição do [AudioGroup](media-services-mes-schema.md#AudioGroup) para conhecer o número adequado de canais, taxa de amostragem e bit rate que poderia ser definido para cada perfil. |

## <a name="audiogroup"></a><a name="AudioGroup"></a>Grupo de Áudio
Para mais detalhes sobre quais os valores válidos para cada perfil, consulte a tabela "Detalhes do código áudio" que se segue.  

### <a name="elements"></a>Elementos

| Nome | Tipo | Descrição |
| --- | --- | --- |
| **Canais**<br/><br/> minOccurs="0" |**xs: int** |O número de canais de áudio codificados. Seguem-se as opções válidas: 1, 2, 5, 6, 8.<br/><br/> Padrão: 2. |
| **Taxa de amostragem**<br/><br/> minOccurs="0" |**xs: int** |A taxa de amostragem de áudio, especificada em Hz. |
| **Bitrate**<br/><br/> minOccurs="0" |**xs: int** |O bitrate utilizado ao codificar o áudio, especificado em kbps. |

### <a name="audio-codec-details"></a>Detalhes do código de áudio

Código de Áudio|Detalhes  
-----------------|---  
**AACLC** |1:<br/><br/> - 11025: &lt;8 &lt; = bitrate 16<br/><br/> - 12000: &lt;8 &lt; = bitrate 16<br/><br/> - 16000: &lt;8 &lt;= bitrate 32<br/><br/>- 22050: &lt;24 &lt; = bitrate 32<br/><br/> - 24000: &lt;24 &lt; = bitrate 32<br/><br/> - 32000: &lt;32 &lt;= bitrate = 192<br/><br/> - 44100: &lt;56 &lt;= bitrate = 288<br/><br/> - 48000: &lt;56 &lt;= bitrate = 288<br/><br/> - 88200: 128 &lt; &lt;= bitrate = 288<br/><br/> - 96000: 128 &lt; &lt;= bitrate = 288<br/><br/> 2:<br/><br/> - 11025: &lt;16 &lt; = bitrate 24<br/><br/> - 12000: &lt;16 &lt; = bitrate 24<br/><br/> - 16000: &lt;16 &lt; = bitrate 40<br/><br/> - 22050: &lt;32 &lt; = bitrate 40<br/><br/> - 24000: &lt;32 &lt; = bitrate 40<br/><br/> - 32000: &lt;40 &lt;= bitrate = 384<br/><br/> - 44100: &lt;96 &lt;= bitrate = 576<br/><br/> - 48000: &lt;96 &lt;= bitrate = 576<br/><br/> - 88200: 256 &lt; &lt;= bitrate = 576<br/><br/> - 96000: 256 &lt; &lt;= bitrate = 576<br/><br/> 5/6:<br/><br/> - 32000: 160 &lt; &lt;= bitrate = 896<br/><br/> - 44100: 240 &lt; &lt;= bitrate = 1024<br/><br/> - 48000: 240 &lt; &lt;= bitrate = 1024<br/><br/> - 88200: 640 &lt; &lt;= bitrate = 1024<br/><br/> - 96000: 640 &lt; &lt;= bitrate = 1024<br/><br/> 8:<br/><br/> - 32000: 224 &lt; &lt;= bitrate = 1024<br/><br/> - 44100: 384 &lt; &lt;= bitrate = 1024<br/><br/> - 48000: 384 &lt; &lt;= bitrate = 1024<br/><br/> - 88200: 896 &lt; &lt;= bitrate = 1024<br/><br/> - 96000: 896 &lt; &lt;= bitrate = 1024  
**HEAACV1** |1:<br/><br/> - 22050: bitrate = 8<br/><br/> - 24000: &lt;8 &lt;= bitrate = 10<br/><br/> - 32000: &lt;12 &lt;= bitrate = 64<br/><br/> - 44100: &lt;20 &lt;= bitrate = 64<br/><br/> - 48000: &lt;20 &lt;= bitrate = 64<br/><br/> - 88200: bitrate = 64<br/><br/> 2:<br/><br/> - 32000: &lt;16 &lt;= bitrate = 128<br/><br/> - 44100: &lt;16 &lt;= bitrate = 128<br/><br/> - 48000: &lt;16 &lt;= bitrate = 128<br/><br/> - 88200: &lt;96 &lt;= bitrate = 128<br/><br/> - 96000: &lt;96 &lt;= bitrate = 128<br/><br/> 5/6:<br/><br/> - 32000: &lt;64 &lt;= bitrate = 320<br/><br/> - 44100: &lt;64 &lt;= bitrate = 320<br/><br/> - 48000: &lt;64 &lt;= bitrate = 320<br/><br/> - 88200: 256 &lt; &lt;= bitrate = 320<br/><br/> - 96000: 256 &lt; &lt;= bitrate = 320<br/><br/> 8:<br/><br/> - 32000: &lt;96 &lt;= bitrate = 448<br/><br/> - 44100: &lt;96 &lt;= bitrate = 448<br/><br/> - 48000: &lt;96 &lt;= bitrate = 448<br/><br/> - 88200: 384 &lt; &lt;= bitrate = 448<br/><br/> - 96000: 384 &lt; &lt;= bitrate = 448  
**HEAACV2** |2:<br/><br/> - 22050: &lt;8 &lt;= bitrate = 10<br/><br/> - 24000: &lt;8 &lt;= bitrate = 10<br/><br/> - 32000: &lt;12 &lt;= bitrate = 64<br/><br/> - 44100: &lt;20 &lt;= bitrate = 64<br/><br/> - 48000: &lt;20 &lt;= bitrate = 64<br/><br/> - 88200: &lt;64 &lt;= bitrate = 64  
  
## <a name="clip"></a><a name="Clip"></a>Clip
### <a name="attributes"></a>Atributos

| Nome | Tipo | Descrição |
| --- | --- | --- |
| **Hora de Início** |**xs:duração** |Especifica o tempo de início de uma apresentação. O valor do StartTime precisa de corresponder aos selos de tempo absolutos do vídeo de entrada. Por exemplo, se o primeiro quadro do vídeo de entrada tiver um carimbo de tempo de 12:00:10.000, então o StartTime deve ser pelo menos 12:00:10.000 ou mais. |
| **Duração** |**xs:duração** |Especifica a duração de uma apresentação (por exemplo, o aparecimento de uma sobreposição no vídeo). |

## <a name="output"></a><a name="Output"></a>Saída
### <a name="attributes"></a>Atributos

| Nome | Tipo | Descrição |
| --- | --- | --- |
| **Nome de ficheiro** |**xs:corda** |O nome do ficheiro de saída.<br/><br/> Pode utilizar macros descritas na tabela seguinte para construir os nomes dos ficheiros de saída. Por exemplo:<br/><br/> **"Saídas": { "Nome de ficheiro": "{Basename}*{Resolução}*{Bitrate}.mp4", "Formato": { "Type": "MP4Format" } } ] ]** |

### <a name="macros"></a>Macros

| Macro | Descrição |
| --- | --- |
| **{Nome base}** |Se estiver a fazer codificação VoD, o {Basename} é o primeiro de 32 caracteres da propriedade AssetFile.Name do ficheiro principal no ativo de entrada.<br/><br/> Se o ativo de entrada for um arquivo ao vivo, então o {Nome base} é derivado dos atributos trackName no manifesto do servidor. Se estiver a submeter um trabalho de subclip usando o TopBitrate, como em: "<VideoStream\>TopBitrate</VideoStream\>", e o ficheiro de saída contém vídeo, então o {Basename} é o primeiro 32 caracteres do nome da faixa com o bitrate mais elevado.<br/><br/> Se em vez disso estiver a submeter um trabalho de subclip usando\>todos os bitrates de entrada, tais como "<VideoStream *</VideoStream",\>e o ficheiro de saída contém vídeo, então {Basename} é o primeiro 32 caracteres do nome da faixa da camada de vídeo correspondente. |
| **{Codec}** |Mapas para "H264" para vídeo e "AAC" para áudio. |
| **{Bitrate}** |O bitrate de vídeo alvo se o ficheiro de saída contiver vídeo e áudio, ou bitrate de áudio alvo se o ficheiro de saída contiver apenas áudio. O valor utilizado é o bitrate em kbps. |
| **{Canal}** |Contagem de canais áudio se o ficheiro contiver áudio. |
| **{Largura}** |Largura do vídeo, em pixels, no ficheiro de saída, se o ficheiro contiver vídeo. |
| **{Altura}** |Altura do vídeo, em pixels, no ficheiro de saída, se o ficheiro contiver vídeo. |
| **{Extensão}** |Herda da propriedade "Tipo" para o ficheiro de saída. O nome do ficheiro de saída tem uma extensão que é uma das: "mp4", "ts", "jpg", "png" ou "bmp". |
| **{Índice}** |Obrigatório para miniatura. Só deve estar presente uma vez. |

## <a name="video-complex-type-inherits-from-codec"></a><a name="Video"></a>Vídeo (tipo complexo herda do Codec)
### <a name="attributes"></a>Atributos

| Nome | Tipo | Descrição |
| --- | --- | --- |
| **Iniciar** |**xs:corda** | |
| **Passo** |**xs:corda** | |
| **Alcance** |**xs:corda** | |
| **PreservaçõesResolutosAfterRotation** |**xs:boolean** |Para obter uma explicação detalhada, consulte a seguinte secção: [PreserveResolutionAfterRotation](media-services-mes-schema.md#PreserveResolutionAfterRotation) |

### <a name="preserveresolutionafterrotation"></a><a name="PreserveResolutionAfterRotation"></a>PreservaçõesResolutosAfterRotation
Recomenda-se utilizar a bandeira **PreserveResolutionAfterRotation** em combinação com os valores de resolução expressos em termos percentuais (Largura="100%", Altura="100%").  

Por predefinição, as definições de resolução de código (Largura, Altura) nas predefinições media Encoder Standard (MES) são direcionadas para vídeos com rotação de 0 graus. Por exemplo, se o seu vídeo de entrada for de 1280x720 com rotação de zero graus, então as predefinições predefinidas predefinidas predefinidas garantem que a saída tem a mesma resolução.  

![MESRoation1](./media/media-services-shemas/media-services-mes-roation1.png) 

Se o vídeo de entrada tiver sido capturado com rotação não zero (por exemplo, um smartphone ou tablet mantido verticalmente), então o MES por padrão aplica as definições de resolução de código (Largura, Altura) ao vídeo de entrada e, em seguida, compensa a rotação. Por exemplo, veja a imagem que se segue. O predefinido utiliza largura = "100%", Altura = "100%", que o MES interpreta como exigindo que a saída tenha 1280 pixels de largura e 720 pixels de altura. Depois de rodar o vídeo, encolhe a imagem para caber naquela janela, levando a áreas de caixa de pilares à esquerda e à direita.  

![MESRoation2](./media/media-services-shemas/media-services-mes-roation2.png) 

Em alternativa, pode utilizar a bandeira **PreserveResolutionAfterRotation** e defini-la como "verdadeira" (o padrão é "falso"). Assim, se o seu preset tem Largura = "100%", Altura = "100%" e PreserveResolutionAfterRotation definido para "verdadeiro", um vídeo de entrada, que tem 1280 pixels de largura e 720 pixels de altura com rotação de 90 graus produz uma saída com rotação de zero graus, mas 720 pixels de largura e 1280 pixels de altura. Veja a seguinte imagem:  

![MESRoation3](./media/media-services-shemas/media-services-mes-roation3.png) 

## <a name="formatgroup-group"></a><a name="FormatGroup"></a>FormatoGroup (grupo)
### <a name="elements"></a>Elementos

| Nome | Tipo | Descrição |
| --- | --- | --- |
| **BmpFormat** |**BmpFormat** | |
| **Formato png** |**Formato png** | |
| **JpgFormat** |**JpgFormat** | |

## <a name="bmplayer"></a><a name="BmpLayer"></a>BmpLayer
### <a name="element"></a>Elemento

| Nome | Tipo | Descrição |
| --- | --- | --- |
| **Width**<br/><br/> minOccurs="0" |**xs:int** | |
| **Height**<br/><br/> minOccurs="0" |**xs:int** | |

### <a name="attributes"></a>Atributos

| Nome | Tipo | Descrição |
| --- | --- | --- |
| **Condição** |**xs:corda** | |

## <a name="pnglayer"></a><a name="PngLayer"></a>PngLayer
### <a name="element"></a>Elemento

| Nome | Tipo | Descrição |
| --- | --- | --- |
| **Width**<br/><br/> minOccurs="0" |**xs:int** | |
| **Height**<br/><br/> minOccurs="0" |**xs:int** | |

### <a name="attributes"></a>Atributos

| Nome | Tipo | Descrição |
| --- | --- | --- |
| **Condição** |**xs:corda** | |

## <a name="jpglayer"></a><a name="JpgLayer"></a>JpgLayer
### <a name="element"></a>Elemento

| Nome | Tipo | Descrição |
| --- | --- | --- |
| **Width**<br/><br/> minOccurs="0" |**xs:int** | |
| **Height**<br/><br/> minOccurs="0" |**xs:int** | |
| **Qualidade**<br/><br/> minOccurs="0" |**xs:int** |Valores válidos: 1(pior)-100 (melhor) |

### <a name="attributes"></a>Atributos

| Nome | Tipo | Descrição |
| --- | --- | --- |
| **Condição** |**xs:corda** | |

## <a name="pnglayers"></a><a name="PngLayers"></a>Pedreiros
### <a name="elements"></a>Elementos

| Nome | Tipo | Descrição |
| --- | --- | --- |
| **PngLayer**<br/><br/> minOccurs="0" maxOccurs="unbounded" |[PngLayer](media-services-mes-schema.md#PngLayer) | |

## <a name="bmplayers"></a><a name="BmpLayers"></a>BmpLayers
### <a name="elements"></a>Elementos

| Nome | Tipo | Descrição |
| --- | --- | --- |
| **BmpLayer**<br/><br/> minOccurs="0" maxOccurs="unbounded" |[BmpLayer](media-services-mes-schema.md#BmpLayer) | |

## <a name="jpglayers"></a><a name="JpgLayers"></a>JpgLayers
### <a name="elements"></a>Elementos

| Nome | Tipo | Descrição |
| --- | --- | --- |
| **JpgLayer**<br/><br/> minOccurs="0" maxOccurs="unbounded" |[JpgLayer](media-services-mes-schema.md#JpgLayer) | |

## <a name="bmpimage-complex-type-inherits-from-video"></a><a name="BmpImage"></a>BmpImage (tipo complexo herda de Vídeo)
### <a name="elements"></a>Elementos

| Nome | Tipo | Descrição |
| --- | --- | --- |
| **Pedreiros**<br/><br/> minOccurs="0" |[Pedreiros](media-services-mes-schema.md#PngLayers) |Camadas de PNG |

## <a name="jpgimage-complex-type-inherits-from-video"></a><a name="JpgImage"></a>JpgImage (tipo complexo herda do Vídeo)
### <a name="elements"></a>Elementos

| Nome | Tipo | Descrição |
| --- | --- | --- |
| **Pedreiros**<br/><br/> minOccurs="0" |[Pedreiros](media-services-mes-schema.md#PngLayers) |Camadas de PNG |

## <a name="pngimage-complex-type-inherits-from-video"></a><a name="PngImage"></a>PngImage (tipo complexo herda do Vídeo)
### <a name="elements"></a>Elementos

| Nome | Tipo | Descrição |
| --- | --- | --- |
| **Pedreiros**<br/><br/> minOccurs="0" |[Pedreiros](media-services-mes-schema.md#PngLayers) |Camadas de PNG |

## <a name="examples"></a>Exemplos
Consulte exemplos de predefinições XML que são construídas com base neste esquema, consulte [Predefinições de Tarefa para MES (Media Encoder Standard)](media-services-mes-presets-overview.md).

## <a name="next-steps"></a>Passos seguintes
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

