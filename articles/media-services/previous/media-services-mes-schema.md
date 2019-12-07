---
title: Esquema de Media Encoder Standard | Microsoft Docs
description: Este artigo descreve alguns dos elementos e tipos do esquema XML no qual Media Encoder Standard predefinições são baseadas.
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
ms.sourcegitcommit: 375b70d5f12fffbe7b6422512de445bad380fe1e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74901442"
---
# <a name="media-encoder-standard-schema"></a>Esquema Media Encoder Standard
Este artigo descreve alguns dos elementos e tipos do esquema XML no qual [Media Encoder Standard predefinições](media-services-mes-presets-overview.md) são baseadas. O artigo fornece uma explicação dos elementos e seus valores válidos.  

## <a name="Preset"></a>Predefinição (elemento raiz)
Define uma predefinição de codificação.  

### <a name="elements"></a>Elementos

| Nome | Tipo | Descrição |
| --- | --- | --- |
| **Codificação** |[Codificação](media-services-mes-schema.md#Encoding) |Elemento raiz, indica que as fontes de entrada devem ser codificadas. |
| **Saídas** |[Saídas](media-services-mes-schema.md#Output) |Coleção de arquivos de saída desejados. |
| **StretchMode**<br/>minOccurs="0"<br/>padrão = "AutoSize|xs: String|Controle o tamanho do quadro de vídeo de saída, o preenchimento, o pixel ou a taxa de proporção de exibição. **Stretchmode** pode ser um dos seguintes valores: **nenhum**, **AutoSize** (padrão) ou **AutoAjuste**.<br/><br/>**Nenhum**: segue estritamente a resolução de saída (por exemplo, a **largura** e a **altura** na predefinição) sem considerar a taxa de proporção de pixel ou exibir a taxa de proporção do vídeo de entrada. Recomendado em cenários como [cortar](media-services-crop-video.md), em que o vídeo de saída tem uma taxa de proporção diferente em comparação com a entrada. <br/><br/>**AutoSize**: a resolução de saída será ajustada dentro da janela (largura * altura) especificada por predefinição. No entanto, o codificador produz um vídeo de saída com taxa de proporção de pixel quadrado (1:1). Portanto, a largura de saída ou a altura de saída pode ser substituída para corresponder à taxa de proporção de exibição da entrada, sem preenchimento. Por exemplo, se a entrada for 1920 x 1080 e a predefinição de codificação solicitar 1280x1280, o valor de altura na predefinição será substituído e a saída será em 1280x720, que mantém a taxa de proporção de entrada de 16:9. <br/><br/>**AutoAjuste**: se necessário, preencha o vídeo de saída (com Letterbox ou pillarbox) para honrar a resolução de saída desejada, garantindo que a região de vídeo ativa na saída tenha a mesma taxa de proporção que a entrada. Por exemplo, suponha que a entrada seja 1920 x 1080 e a predefinição de codificação solicite 1280x1280. Em seguida, o vídeo de saída estará em 1280x1280, mas ele conterá um retângulo 1280x720 interno de ' vídeo ativo ' com a taxa de proporção de 16:9 e as regiões de Letterbox 280 pixels de altura na parte superior e inferior. Para outro exemplo, se a entrada for 1440 x 1080 e a predefinição de codificação solicitar 1280x720, a saída será em 1280x720, que contém um retângulo interno de 960x720 na taxa de proporção de 4:3, e regiões de caixa de pilar 160 pixels de largura à esquerda e à direita. 

### <a name="attributes"></a>Atributos

| Nome | Tipo | Descrição |
| --- | --- | --- |
| **Versão**<br/><br/> Obrigatório |**xs: decimal** |A versão predefinida. As seguintes restrições se aplicam: xs: fractionDigits Value = "1" e xs: minInclusive Value = "1", por exemplo, **version = "1.0"** . |

## <a name="Encoding"></a>Mecanismo
Contém uma sequência dos seguintes elementos:  

### <a name="elements"></a>Elementos

| Nome | Tipo | Descrição |
| --- | --- | --- |
| **H264Video** |[H264Video](media-services-mes-schema.md#H264Video) |Configurações da codificação H. 264 de vídeo. |
| **AACAudio** |[AACAudio](media-services-mes-schema.md#AACAudio) |Configurações para codificação AAC de áudio. |
| **BmpImage** |[BmpImage](media-services-mes-schema.md#BmpImage) |Configurações da imagem BMP. |
| **PngImage** |[PngImage](media-services-mes-schema.md#PngImage) |Configurações para imagem png. |
| **JpgImage** |[JpgImage](media-services-mes-schema.md#JpgImage) |Configurações da imagem jpg. |

## <a name="H264Video"></a>H264Video
### <a name="elements"></a>Elementos

| Nome | Tipo | Descrição |
| --- | --- | --- |
| **TwoPass**<br/><br/> minOccurs="0" |**xs:boolean** |Atualmente, há suporte apenas para codificação de uma passagem. |
| **KeyFrameInterval**<br/><br/> minOccurs="0"<br/><br/> **default="00:00:02"** |**xs:time** |Determina o espaçamento fixo entre os quadros de IDR em unidades de segundos. Também conhecida como a duração de GOP. Consulte **SceneChangeDetection** para controlar se o codificador pode se desviar desse valor. |
| **SceneChangeDetection**<br/><br/> minOccurs="0"<br/><br/> padrão = "falso" |**xs: Boolean** |Se definido como true, o codificador tentará detectar a alteração de cena no vídeo e inserirá um quadro de IDR. |
| **Complexidade**<br/><br/> minOccurs="0"<br/><br/> default="Balanced" |**xs:string** |Controla a compensação entre a velocidade de codificação e a qualidade do vídeo. Pode ser um dos seguintes valores: **velocidade**, **equilibrado**ou **qualidade**<br/><br/> Padrão: **equilibrado** |
| **SyncMode**<br/><br/> minOccurs="0" | |O recurso será exposto em uma versão futura. |
| **H264Layers**<br/><br/> minOccurs="0" |[H264Layers](media-services-mes-schema.md#H264Layers) |Coleção de camadas de vídeo de saída. |

### <a name="attributes"></a>Atributos

| Nome | Tipo | Descrição |
| --- | --- | --- |
| **Problema** |**xs:string** | Quando a entrada não tiver vídeo, talvez você queira forçar o codificador a inserir uma faixa de vídeo monocromática. Para fazer isso, use Condition = "InsertBlackIfNoVideoBottomLayerOnly" (para inserir um vídeo somente na taxa de bits mais baixa) ou Condition = "InsertBlackIfNoVideo" (para inserir um vídeo em todas as taxas de bits de saída). Para obter mais informações, veja [este](media-services-advanced-encoding-with-mes.md#no_video) artigo.|

## <a name="H264Layers"></a>H264Layers

Por padrão, se você enviar uma entrada para o codificador que contém apenas áudio e sem vídeo, o ativo de saída conterá arquivos somente com dados de áudio. Alguns jogadores podem não ser capazes de lidar com esses fluxos de saída. Você pode usar a configuração de atributo H264Video's **InsertBlackIfNoVideo** para forçar o codificador a adicionar uma faixa de vídeo à saída nesse cenário. Para obter mais informações, veja [este](media-services-advanced-encoding-with-mes.md#no_video) artigo.
              
### <a name="elements"></a>Elementos

| Nome | Tipo | Descrição |
| --- | --- | --- |
| **H264Layer**<br/><br/> minOccurs="0" maxOccurs="unbounded" |[H264Layer](media-services-mes-schema.md#H264Layer) |Uma coleção de camadas de H264. |

## <a name="H264Layer"></a>H264Layer
> [!NOTE]
> Os limites de vídeo são baseados nos valores descritos na tabela [níveis de H264](https://en.wikipedia.org/wiki/H.264/MPEG-4_AVC#Levels) .  
> 
> 

### <a name="elements"></a>Elementos

| Nome | Tipo | Descrição |
| --- | --- | --- |
| **Perfil**<br/><br/> minOccurs="0"<br/><br/> padrão = "auto" |**xs: String** |Pode ser de um dos seguintes valores **xs: String** : **auto**, **linha de base**, **principal**, **alta**. |
| **Nível**<br/><br/> minOccurs="0"<br/><br/> padrão = "auto" |**xs: String** | |
| **720p**<br/><br/> minOccurs="0" |**xs:int** |A taxa de bits usada para esta camada de vídeo, especificada em Kbps. |
| **MaxBitrate**<br/><br/> minOccurs="0" |**xs: int** |A taxa de bits máxima usada para esta camada de vídeo, especificada em Kbps. |
| **BufferWindow**<br/><br/> minOccurs="0"<br/><br/> default="00:00:05" |**xs: time** |Comprimento do buffer de vídeo. |
| **Width**<br/><br/> minOccurs="0" |**xs: int** |Largura do quadro de vídeo de saída, em pixels.<br/><br/> No momento, você deve especificar a largura e a altura. A largura e a altura precisam ser pares de números. |
| **Tamanho**<br/><br/> minOccurs="0" |**xs:int** |Altura do quadro de vídeo de saída, em pixels.<br/><br/> No momento, você deve especificar a largura e a altura. A largura e a altura precisam ser pares de números.|
| **BFrames**<br/><br/> minOccurs="0" |**xs: int** |Número de quadros B entre quadros de referência. |
| **ReferenceFrames**<br/><br/> minOccurs="0"<br/><br/> padrão = "3" |**xs:int** |Número de quadros de referência em um GOP. |
| **EntropyMode**<br/><br/> minOccurs="0"<br/><br/> padrão = "CABAC" |**xs: String** |Pode ser um dos seguintes valores: **CABAC** e **CAVLC**. |
| **FrameRate**<br/><br/> minOccurs="0" |número racional |Determina a taxa de quadros do vídeo de saída. Use o padrão de "0/1" para permitir que o codificador use a mesma taxa de quadros que o vídeo de entrada. Espera-se que os valores permitidos sejam taxas de quadros de vídeo comuns. No entanto, qualquer racional válido é permitido. Por exemplo, 1/1 seria 1 fps e é válido.<br/><br/> -12/1 (12 fps)<br/><br/> -15/1 (15 fps)<br/><br/> -24/1 (24 fps)<br/><br/> -24000/1001 (23,976 fps)<br/><br/> -25/1 (25 fps)<br/><br/>  -30/1 (30 fps)<br/><br/> -30000/1001 (29,97 fps) <br/> <br/>**Observação** Se você estiver criando uma predefinição personalizada para a codificação de múltiplas taxas de bits, todas as camadas da predefinição **deverão** usar o mesmo valor de taxa de quadros.|
| **AdaptiveBFrame**<br/><br/> minOccurs="0" |**xs: Boolean** |Copiar do codificador de mídia do Azure |
| **Fatia**<br/><br/> minOccurs="0"<br/><br/> default="0" |**xs:int** |Determina a quantidade de fatias em que um quadro é dividido. Recomendável usar o padrão. |

## <a name="AACAudio"></a>AACAudio
 Contém uma sequência dos seguintes elementos e grupos.  

 Para obter mais informações sobre o AAC, consulte [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding).  

### <a name="elements"></a>Elementos

| Nome | Tipo | Descrição |
| --- | --- | --- |
| **Perfil**<br/><br/> minOccurs="0 "<br/><br/> default="AACLC" |**xs: String** |Pode ser um dos seguintes valores: **AACLC**, **HEAACV1**ou **HEAACV2**. |

### <a name="attributes"></a>Atributos

| Nome | Tipo | Descrição |
| --- | --- | --- |
| **Problema** |**xs: String** |Para forçar o codificador a produzir um ativo que contenha uma faixa de áudio silenciosa quando a entrada não tiver áudio, especifique o valor "InsertSilenceIfNoAudio".<br/><br/> Por padrão, se você enviar uma entrada para o codificador que contém apenas vídeo e sem áudio, o ativo de saída conterá arquivos que contêm apenas dados de vídeo. Alguns jogadores podem não ser capazes de lidar com esses fluxos de saída. Você pode usar essa configuração para forçar o codificador a adicionar uma faixa de áudio silenciosa à saída nesse cenário. |

### <a name="groups"></a>Grupos

| Referência | Descrição |
| --- | --- |
| [AudioGroup](media-services-mes-schema.md#AudioGroup)<br/><br/> minOccurs="0" |Consulte Descrição do grupo de [áudio](media-services-mes-schema.md#AudioGroup) para saber o número apropriado de canais, taxa de amostragem e taxa de bits que podem ser definidos para cada perfil. |

## <a name="AudioGroup"></a>MyAudio
Para obter detalhes sobre quais valores são válidos para cada perfil, consulte a tabela "detalhes do codec de áudio" a seguir.  

### <a name="elements"></a>Elementos

| Nome | Tipo | Descrição |
| --- | --- | --- |
| **Channels** (Canais)<br/><br/> minOccurs="0" |**xs: int** |O número de canais de áudio codificados. As opções a seguir são válidas: 1, 2, 5, 6, 8.<br/><br/> Padrão: 2. |
| **SamplingRate**<br/><br/> minOccurs="0" |**xs: int** |A taxa de amostragem de áudio, especificada em Hz. |
| **720p**<br/><br/> minOccurs="0" |**xs: int** |A taxa de bits usada ao codificar o áudio, especificada em Kbps. |

### <a name="audio-codec-details"></a>Detalhes do codec de áudio

Codec de áudio|Detalhes  
-----------------|---  
**AACLC** |1:<br/><br/> -11025:8 &lt;= taxa de bits &lt; 16<br/><br/> -12000:8 &lt;= taxa de bits &lt; 16<br/><br/> -16000:8 &lt;= taxa de bits &lt;32<br/><br/>-22050:24 &lt;= taxa de bits &lt; 32<br/><br/> -24000:24 &lt;= taxa de bits &lt; 32<br/><br/> -32000:32 &lt;= taxa de bits &lt;= 192<br/><br/> -44100:56 &lt;= taxa de bits &lt;= 288<br/><br/> -48000:56 &lt;= taxa de bits &lt;= 288<br/><br/> -88200:128 &lt;= taxa de bits &lt;= 288<br/><br/> -96000:128 &lt;= taxa de bits &lt;= 288<br/><br/> 2:<br/><br/> -11025:16 &lt;= taxa de bits &lt; 24<br/><br/> -12000:16 &lt;= taxa de bits &lt; 24<br/><br/> -16000:16 &lt;= taxa de bits &lt; 40<br/><br/> -22050:32 &lt;= taxa de bits &lt; 40<br/><br/> -24000:32 &lt;= taxa de bits &lt; 40<br/><br/> -32000:40 &lt;= taxa de bits &lt;= 384<br/><br/> -44100:96 &lt;= taxa de bits &lt;= 576<br/><br/> -48000:96 &lt;= taxa de bits &lt;= 576<br/><br/> -88200:256 &lt;= taxa de bits &lt;= 576<br/><br/> -96000:256 &lt;= taxa de bits &lt;= 576<br/><br/> 5/6:<br/><br/> -32000:160 &lt;= taxa de bits &lt;= 896<br/><br/> -44100:240 &lt;= taxa de bits &lt;= 1024<br/><br/> -48000:240 &lt;= taxa de bits &lt;= 1024<br/><br/> -88200:640 &lt;= taxa de bits &lt;= 1024<br/><br/> -96000:640 &lt;= taxa de bits &lt;= 1024<br/><br/> 8:<br/><br/> -32000:224 &lt;= taxa de bits &lt;= 1024<br/><br/> -44100:384 &lt;= taxa de bits &lt;= 1024<br/><br/> -48000:384 &lt;= taxa de bits &lt;= 1024<br/><br/> -88200:896 &lt;= taxa de bits &lt;= 1024<br/><br/> -96000:896 &lt;= taxa de bits &lt;= 1024  
**HEAACV1** |1:<br/><br/> -22050: taxa de bits = 8<br/><br/> -24000:8 &lt;= taxa de bits &lt;= 10<br/><br/> -32000:12 &lt;= taxa de bits &lt;= 64<br/><br/> -44100:20 &lt;= taxa de bits &lt;= 64<br/><br/> -48000:20 &lt;= taxa de bits &lt;= 64<br/><br/> -88200: taxa de bits = 64<br/><br/> 2:<br/><br/> -32000:16 &lt;= taxa de bits &lt;= 128<br/><br/> -44100:16 &lt;= taxa de bits &lt;= 128<br/><br/> -48000:16 &lt;= taxa de bits &lt;= 128<br/><br/> -88200:96 &lt;= taxa de bits &lt;= 128<br/><br/> -96000:96 &lt;= taxa de bits &lt;= 128<br/><br/> 5/6:<br/><br/> -32000:64 &lt;= taxa de bits &lt;= 320<br/><br/> -44100:64 &lt;= taxa de bits &lt;= 320<br/><br/> -48000:64 &lt;= taxa de bits &lt;= 320<br/><br/> -88200:256 &lt;= taxa de bits &lt;= 320<br/><br/> -96000:256 &lt;= taxa de bits &lt;= 320<br/><br/> 8:<br/><br/> -32000:96 &lt;= taxa de bits &lt;= 448<br/><br/> -44100:96 &lt;= taxa de bits &lt;= 448<br/><br/> -48000:96 &lt;= taxa de bits &lt;= 448<br/><br/> -88200:384 &lt;= taxa de bits &lt;= 448<br/><br/> -96000:384 &lt;= taxa de bits &lt;= 448  
**HEAACV2** |2:<br/><br/> -22050:8 &lt;= taxa de bits &lt;= 10<br/><br/> -24000:8 &lt;= taxa de bits &lt;= 10<br/><br/> -32000:12 &lt;= taxa de bits &lt;= 64<br/><br/> -44100:20 &lt;= taxa de bits &lt;= 64<br/><br/> -48000:20 &lt;= taxa de bits &lt;= 64<br/><br/> -88200:64 &lt;= taxa de bits &lt;= 64  
  
## <a name="Clip"></a>Clipe
### <a name="attributes"></a>Atributos

| Nome | Tipo | Descrição |
| --- | --- | --- |
| **StartTime** |**xs:duration** |Especifica a hora de início de uma apresentação. O valor de StartTime precisa corresponder aos carimbos de data/hora absolutos do vídeo de entrada. Por exemplo, se o primeiro quadro do vídeo de entrada tiver um carimbo de data/hora de 12:00:10.000, o StartTime deverá ser pelo menos 12:00:10.000 ou superior. |
| **Permanência** |**xs:duration** |Especifica a duração de uma apresentação (por exemplo, a aparência de uma sobreposição no vídeo). |

## <a name="Output"></a>Der
### <a name="attributes"></a>Atributos

| Nome | Tipo | Descrição |
| --- | --- | --- |
| **FileName** |**xs:string** |O nome do arquivo de saída.<br/><br/> Você pode usar as macros descritas na tabela a seguir para criar os nomes de arquivo de saída. Por exemplo:<br/><br/> **"Outputs": [      {       "FileName": "{Basename} *{Resolution}* {Bitrate}.mp4",       "Format": {         "Type": "MP4Format"       }     }   ]** |

### <a name="macros"></a>Macros

| Macro | Descrição |
| --- | --- |
| **{Basename}** |Se você estiver fazendo a codificação VoD, o {basename} será o primeiro 32 caracteres da propriedade AssetFile.Name do arquivo primário no ativo de entrada.<br/><br/> Se o ativo de entrada for um arquivo ao vivo, o {basename} será derivado dos atributos trackname no manifesto do servidor. Se você estiver enviando um trabalho de subclipe usando o TopBitrate, como em: "< VideoStream\>TopBitrate </VideoStream\>" e o arquivo de saída contiver vídeo, então {basename} será o primeiro 32 caracteres do trackname da camada de vídeo com a taxa de bits mais alta.<br/><br/> Se, em vez disso, você estiver enviando um trabalho de subclipe usando todas as taxas de bits de entrada, como "< VideoStream\>* </VideoStream\>" e o arquivo de saída contiver vídeo, {basename} será o primeiro 32 caracteres do trackname da camada de vídeo correspondente. |
| **{Codec}** |Mapeia para "H264" para vídeo e "AAC" para áudio. |
| **{Bitrate}** |A taxa de bits do vídeo de destino se o arquivo de saída contiver vídeo e áudio, ou taxa de bits de áudio de destino se o arquivo de saída contiver apenas áudio. O valor usado é a taxa de bits em Kbps. |
| **{Channel}** |Contagem de canais de áudio se o arquivo contiver áudio. |
| **{Width}** |Largura do vídeo, em pixels, no arquivo de saída, se o arquivo contiver vídeo. |
| **{Height}** |Altura do vídeo, em pixels, no arquivo de saída, se o arquivo contiver vídeo. |
| **{Extension}** |Herda da propriedade "Type" para o arquivo de saída. O nome do arquivo de saída tem uma extensão, que é um dos: "MP4", "TS", "jpg", "png" ou "bmp". |
| **{Index}** |Obrigatório para miniatura. Só deve estar presente uma vez. |

## <a name="Video"></a>Vídeo (o tipo complexo herda do codec)
### <a name="attributes"></a>Atributos

| Nome | Tipo | Descrição |
| --- | --- | --- |
| **Começar** |**xs:string** | |
| **Etapa** |**xs:string** | |
| **Intervalo** |**xs:string** | |
| **PreserveResolutionAfterRotation** |**xs:boolean** |Para obter uma explicação detalhada, consulte a seguinte seção: [PreserveResolutionAfterRotation](media-services-mes-schema.md#PreserveResolutionAfterRotation) |

### <a name="PreserveResolutionAfterRotation"></a> PreserveResolutionAfterRotation
É recomendável usar o sinalizador **PreserveResolutionAfterRotation** em combinação com valores de resolução expressos em termos percentuais (largura = "100%", altura = "100%").  

Por padrão, as configurações de resolução de codificação (largura, altura) nas predefinições de Media Encoder Standard (MES) são direcionadas a vídeos com rotação de 0 graus. Por exemplo, se o vídeo de entrada for 1280x720 com rotação de grau zero, as predefinições padrão garantirão que a saída tenha a mesma resolução.  

![MESRoation1](./media/media-services-shemas/media-services-mes-roation1.png) 

Se o vídeo de entrada tiver sido capturado com uma rotação diferente de zero (por exemplo, um smartphone ou Tablet mantido verticalmente), o MES, por padrão, aplicará as configurações de resolução de codificação (largura, altura) ao vídeo de entrada e compensará a rotação. Por exemplo, consulte a imagem a seguir. A predefinição usa largura = "100%", altura = "100%", que o MES interpreta como exigir que a saída tenha de 1280 pixels de largura e de 720 pixels de altura. Depois de girar o vídeo, ele reduz a imagem para caber nessa janela, levando a áreas da caixa pilar à esquerda e à direita.  

![MESRoation2](./media/media-services-shemas/media-services-mes-roation2.png) 

Como alternativa, você pode fazer uso do sinalizador **PreserveResolutionAfterRotation** e defini-lo como "true" (o padrão é "false"). Portanto, se sua predefinição tiver width = "100%", Height = "100%" e PreserveResolutionAfterRotation definida como "true", um vídeo de entrada, que tem 1280 pixels de largura e 720 pixels de altura com a rotação de nível de 90 produz uma saída com rotação de zero graus, mas que 720 pixels de largura e 1280 pixels de altura. Consulte a figura a seguir:  

![MESRoation3](./media/media-services-shemas/media-services-mes-roation3.png) 

## <a name="FormatGroup"></a>Grupo de formatos (Group)
### <a name="elements"></a>Elementos

| Nome | Tipo | Descrição |
| --- | --- | --- |
| **BmpFormat** |**BmpFormat** | |
| **PngFormat** |**PngFormat** | |
| **JpgFormat** |**JpgFormat** | |

## <a name="BmpLayer"></a>BmpLayer
### <a name="element"></a>Elemento

| Nome | Tipo | Descrição |
| --- | --- | --- |
| **Width**<br/><br/> minOccurs="0" |**xs:int** | |
| **Tamanho**<br/><br/> minOccurs="0" |**xs:int** | |

### <a name="attributes"></a>Atributos

| Nome | Tipo | Descrição |
| --- | --- | --- |
| **Problema** |**xs:string** | |

## <a name="PngLayer"></a>PngLayer
### <a name="element"></a>Elemento

| Nome | Tipo | Descrição |
| --- | --- | --- |
| **Width**<br/><br/> minOccurs="0" |**xs:int** | |
| **Tamanho**<br/><br/> minOccurs="0" |**xs:int** | |

### <a name="attributes"></a>Atributos

| Nome | Tipo | Descrição |
| --- | --- | --- |
| **Problema** |**xs:string** | |

## <a name="JpgLayer"></a> JpgLayer
### <a name="element"></a>Elemento

| Nome | Tipo | Descrição |
| --- | --- | --- |
| **Width**<br/><br/> minOccurs="0" |**xs:int** | |
| **Tamanho**<br/><br/> minOccurs="0" |**xs:int** | |
| **PrintQuality**<br/><br/> minOccurs="0" |**xs:int** |Valores válidos: 1 (pior)-100 (melhor) |

### <a name="attributes"></a>Atributos

| Nome | Tipo | Descrição |
| --- | --- | --- |
| **Problema** |**xs:string** | |

## <a name="PngLayers"></a>PngLayers
### <a name="elements"></a>Elementos

| Nome | Tipo | Descrição |
| --- | --- | --- |
| **PngLayer**<br/><br/> minOccurs="0" maxOccurs="unbounded" |[PngLayer](media-services-mes-schema.md#PngLayer) | |

## <a name="BmpLayers"></a>BmpLayers
### <a name="elements"></a>Elementos

| Nome | Tipo | Descrição |
| --- | --- | --- |
| **BmpLayer**<br/><br/> minOccurs="0" maxOccurs="unbounded" |[BmpLayer](media-services-mes-schema.md#BmpLayer) | |

## <a name="JpgLayers"></a> JpgLayers
### <a name="elements"></a>Elementos

| Nome | Tipo | Descrição |
| --- | --- | --- |
| **JpgLayer**<br/><br/> minOccurs="0" maxOccurs="unbounded" |[JpgLayer](media-services-mes-schema.md#JpgLayer) | |

## <a name="BmpImage"></a>BmpImage (o tipo complexo herda do vídeo)
### <a name="elements"></a>Elementos

| Nome | Tipo | Descrição |
| --- | --- | --- |
| **PngLayers**<br/><br/> minOccurs="0" |[PngLayers](media-services-mes-schema.md#PngLayers) |Camadas png |

## <a name="JpgImage"></a>JpgImage (o tipo complexo herda do vídeo)
### <a name="elements"></a>Elementos

| Nome | Tipo | Descrição |
| --- | --- | --- |
| **PngLayers**<br/><br/> minOccurs="0" |[PngLayers](media-services-mes-schema.md#PngLayers) |Camadas png |

## <a name="PngImage"></a>PngImage (o tipo complexo herda do vídeo)
### <a name="elements"></a>Elementos

| Nome | Tipo | Descrição |
| --- | --- | --- |
| **PngLayers**<br/><br/> minOccurs="0" |[PngLayers](media-services-mes-schema.md#PngLayers) |Camadas png |

## <a name="examples"></a>Exemplos
Consulte exemplos de predefinições de XML que são criadas com base nesse esquema, consulte [predefinições de tarefa para mes (Media Encoder Standard)](media-services-mes-presets-overview.md).

## <a name="next-steps"></a>Passos seguintes
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

