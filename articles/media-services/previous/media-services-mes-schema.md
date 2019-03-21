---
title: Esquema Media Encoder Standard | Documentos da Microsoft
description: O artigo apresenta uma visão geral do esquema Media Encoder Standard.
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
ms.openlocfilehash: 837235e04ce190a4481e1f19789d8e9ff9cb7578
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58259247"
---
# <a name="media-encoder-standard-schema"></a>Esquema Media Encoder Standard
Este artigo descreve alguns dos elementos e tipos do esquema XML no qual [predefine Media Encoder Standard](media-services-mes-presets-overview.md) baseiam-se. O artigo apresenta a explicação de elementos e os respetivos valores válidos.  

## <a name="Preset"></a> Configuração predefinida (elemento de raiz)
Define uma predefinição de codificação.  

### <a name="elements"></a>Elementos

| Name | Tipo | Descrição |
| --- | --- | --- |
| **Codificação** |[Codificação](media-services-mes-schema.md#Encoding) |Elemento de raiz, indica que as origens de entrada estão a ser codificada. |
| **Saídas** |[Saídas](media-services-mes-schema.md#Output) |Coleção de ficheiros de saída desejado. |
| **StretchMode**<br/>minOccurs="0"<br/>default="AutoSize|xs:string|Controlar o tamanho de quadro de vídeo de saída, o preenchimento, o pixel ou visualizar a proporção de aspecto. **StretchMode** poderia ser um dos seguintes valores: **NONE**, **AutoSize** (predefinição), ou **AutoFit**.<br/><br/>**Nenhum**: Siga estritamente a resolução de saída (por exemplo, o **largura** e **altura** na configuração predefinida) sem considerar a taxa de proporção de pixel ou a taxa de proporção de exibição de vídeo de entrada. Recomendado em cenários como [corte](media-services-crop-video.md), em que o vídeo de saída tem uma taxa de proporção diferente em comparação com a entrada. <br/><br/>**AutoSize**: A resolução de saída poderão caber dentro da janela (largura * altura) especificado pela configuração predefinida. No entanto, o codificador produz um vídeo de saída que tem a proporção de pixel do quadrado (1:1). Por conseguinte, a saída largura ou altura de saída pode ser substituído para corresponderem à proporção de apresentação da entrada, sem preenchimento. Por exemplo, se a entrada é de 1920 x 1080, e a predefinição de codificação pede-lhe 1280 x 1280, em seguida, o valor de altura na configuração predefinida é substituído e o resultado será em 1280 x 720, que mantém a entrada proporção de 16:9. <br/><br/>**AutoFit**: Se for necessário, preenche o vídeo de saída (com letterbox ou pillarbox) que respeite a resolução de saída desejada, garantindo que a região de vídeo Active Directory na saída tem a mesma proporção como entrada. Por exemplo, suponha que a entrada é de 1920 x 1080 e a predefinição de codificação pede-lhe 1280 x 1280. Em seguida, a saída de vídeo será 1280 x 1280, mas ele conterá um retângulo de 1280 x 720 interna de "vídeo Active Directory' com a taxa de proporção de 16:9 e letterbox regiões 280 pixels de altura na parte superior e inferior. Por outro exemplo, se a entrada é 1440 x 1080 e a predefinição de codificação pede-lhe 1280 x 720, em seguida, o resultado será em 1280 x 720, que contém um retângulo interno de 960 x 720 na taxa de proporção de 4:3 e pilar caixa regiões 160 pixels grande no lado esquerdo e direito. 

### <a name="attributes"></a>Atributos

| Name | Tipo | Descrição |
| --- | --- | --- |
| **Versão**<br/><br/> Necessário |**xs: decimal** |A versão predefinida. As seguintes restrições aplicam-se: valor de xs:fractionDigits = valor "1" e xs:minInclusive = "1", por exemplo, **versão = "1.0"**. |

## <a name="Encoding"></a> Codificação
Contém uma seqüência dos seguintes elementos:  

### <a name="elements"></a>Elementos

| Name | Tipo | Descrição |
| --- | --- | --- |
| **H264Video** |[H264Video](media-services-mes-schema.md#H264Video) |Definições para H.264 codificação de vídeo. |
| **AACAudio** |[AACAudio](media-services-mes-schema.md#AACAudio) |Definições para AAC codificação de áudio. |
| **BmpImage** |[BmpImage](media-services-mes-schema.md#BmpImage) |Definições para a imagem do Bmp. |
| **PngImage** |[PngImage](media-services-mes-schema.md#PngImage) |Definições de imagem Png. |
| **JpgImage** |[JpgImage](media-services-mes-schema.md#JpgImage) |Definições de imagem Jpg. |

## <a name="H264Video"></a> H264Video
### <a name="elements"></a>Elementos

| Name | Tipo | Descrição |
| --- | --- | --- |
| **TwoPass**<br/><br/> minOccurs="0" |**xs:boolean** |Atualmente, é suportada a codificação de apenas de uma passagem. |
| **KeyFrameInterval**<br/><br/> minOccurs="0"<br/><br/> **default="00:00:02"** |**xs:time** |Determina o espaçamento fixo entre quadros IDR em unidades de segundos. Também referida como a duração de GOP. Ver **SceneChangeDetection** para controlar se o codificador pode se desviar este valor. |
| **SceneChangeDetection**<br/><br/> minOccurs="0"<br/><br/> predefinição = "false" |**xs: boolean** |Se definido como true, codificador tenta detetar alterações de cena o vídeo e insere um quadro IDR. |
| **Complexidade**<br/><br/> minOccurs="0"<br/><br/> default="Balanced" |**xs:string** |Controla o equilíbrio entre velocidade e vídeo de qualidade de codificar. Poderia ser um dos seguintes valores: **Velocidade**, **balanceada**, ou **qualidade**<br/><br/> Predefinição: **Com balanceamento de** |
| **SyncMode**<br/><br/> minOccurs="0" | |Funcionalidade será exposta numa versão futura. |
| **H264Layers**<br/><br/> minOccurs="0" |[H264Layers](media-services-mes-schema.md#H264Layers) |Coleção de camadas de vídeo de saída. |

### <a name="attributes"></a>Atributos

| Name | Tipo | Descrição |
| --- | --- | --- |
| **condição** |**xs:string** | Quando a entrada não tem nenhum vídeo, pode querer forçar o codificador para inserir um Roteiro de vídeo monocromático. Para tal, utilize condição = "InsertBlackIfNoVideoBottomLayerOnly" (para inserir um vídeo em apenas a velocidade de transmissão mais baixa) ou condição = "InsertBlackIfNoVideo" (para inserir um vídeo de todo o resultado de velocidades de transmissão). Para obter mais informações, veja [este](media-services-advanced-encoding-with-mes.md#no_video) artigo.|

## <a name="H264Layers"></a> H264Layers

Por predefinição, se tiver de enviar uma entrada ao codificador que contenha apenas áudio e nenhum vídeo, o elemento de saída contém ficheiros com dados de áudio apenas. Alguns leitores podem não ser capazes de lidar com esses fluxos de saída. Pode usar o H264Video **InsertBlackIfNoVideo** atributo definição para forçar o codificador para adicionar uma faixa de vídeo para o resultado desse cenário. Para obter mais informações, veja [este](media-services-advanced-encoding-with-mes.md#no_video) artigo.
              
### <a name="elements"></a>Elementos

| Name | Tipo | Descrição |
| --- | --- | --- |
| **H264Layer**<br/><br/> minOccurs="0" maxOccurs="unbounded" |[H264Layer](media-services-mes-schema.md#H264Layer) |Uma coleção de camadas de h264 taxa de bits. |

## <a name="H264Layer"></a> H264Layer
> [!NOTE]
> Limites do vídeo baseiam-se nos valores descritos a [H264 níveis](https://en.wikipedia.org/wiki/H.264/MPEG-4_AVC#Levels) tabela.  
> 
> 

### <a name="elements"></a>Elementos

| Name | Tipo | Descrição |
| --- | --- | --- |
| **Perfil**<br/><br/> minOccurs="0"<br/><br/> predefinição = "Auto" |**xs: string** |Pode ser de um dos seguintes procedimentos **xs: string** valores: **Auto**, **linha de base**, **Main**, **elevada**. |
| **Nível**<br/><br/> minOccurs="0"<br/><br/> predefinição = "Auto" |**xs: string** | |
| **Velocidade de transmissão**<br/><br/> minOccurs="0" |**xs:int** |A velocidade de transmissão utilizada para esta camada de vídeo, especificada em kbps. |
| **MaxBitrate**<br/><br/> minOccurs="0" |**xs: int** |A velocidade de transmissão máxima utilizada para esta camada de vídeo, especificada em kbps. |
| **BufferWindow**<br/><br/> minOccurs="0"<br/><br/> default="00:00:05" |**xs: hora** |Comprimento da memória intermédia de vídeo. |
| **Width**<br/><br/> minOccurs="0" |**xs: int** |Largura do quadro de vídeo de saída, em pixéis.<br/><br/> Atualmente, tem de especificar a largura e altura. A largura e altura tem de ser números pares. |
| **Height**<br/><br/> minOccurs="0" |**xs:int** |Altura do quadro de vídeo de saída, em pixéis.<br/><br/> Atualmente, tem de especificar a largura e altura. A largura e altura tem de ser números pares.|
| **BFrames**<br/><br/> minOccurs="0" |**xs: int** |Número de quadros de B entre quadros de referência. |
| **ReferenceFrames**<br/><br/> minOccurs="0"<br/><br/> predefinição = "3" |**xs:int** |Número de quadros de referência num GOP. |
| **EntropyMode**<br/><br/> minOccurs="0"<br/><br/> predefinição = "Cabac" |**xs: string** |Poderia ser um dos seguintes valores: **Cabac** e **Cavlc**. |
| **FrameRate**<br/><br/> minOccurs="0" |Número racional |Determina a velocidade de fotogramas do vídeo de saída. Utilize predefinição "0/1" para que o codificador de utilizar a mesma taxa de quadros como o vídeo de entrada. Valores permitidos devem ser as taxas de quadro de vídeo comum. No entanto, qualquer válido racional é permitido. Por exemplo, 1/1 seria 1 fps e é válido.<br/><br/> -12/1 (12 fps)<br/><br/> -15/1 (15 fps)<br/><br/> -24/1 (24 fps)<br/><br/> 24000/1001 (23.976 fps)<br/><br/> -25/1 (25 fps)<br/><br/>  - 30/1 (30 fps)<br/><br/> 30000/1001 (29.97 fps) <br/> <br/>**Tenha em atenção** se estiver a criar uma configuração predefinida para a codificação de bits múltipla, em seguida, todas as camadas da configuração predefinida **tem** utilize o mesmo valor de FrameRate.|
| **AdaptiveBFrame**<br/><br/> minOccurs="0" |**xs: boolean** |Copiar do codificador de multimédia do Azure |
| **Setores**<br/><br/> minOccurs="0"<br/><br/> default="0" |**xs:int** |Determina quantas setores de um quadro é dividido em. Recomendamos utilizar a predefinição. |

## <a name="AACAudio"></a> AACAudio
 Contém uma sequência os seguintes elementos e grupos.  

 Para obter mais informações sobre AAC, consulte [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding).  

### <a name="elements"></a>Elementos

| Name | Tipo | Descrição |
| --- | --- | --- |
| **Perfil**<br/><br/> minOccurs="0 "<br/><br/> default="AACLC" |**xs: string** |Poderia ser um dos seguintes valores: **AACLC**, **HEAACV1**, ou **HEAACV2**. |

### <a name="attributes"></a>Atributos

| Name | Tipo | Descrição |
| --- | --- | --- |
| **condição** |**xs: string** |Para forçar o codificador para produzir um elemento que contenha uma faixa de áudio silenciosa quando a entrada tem sem áudio, especifique o valor de "InsertSilenceIfNoAudio".<br/><br/> Por predefinição, se tiver de enviar uma entrada ao codificador que contém apenas vídeo e áudio de nenhum, em seguida, o elemento de saída contém ficheiros que contêm dados apenas de vídeos. Alguns leitores podem não ser capazes de lidar com esses fluxos de saída. Pode utilizar esta definição para forçar o codificador para adicionar uma faixa de áudio automática para a saída desse cenário. |

### <a name="groups"></a>Grupos

| Referência | Descrição |
| --- | --- |
| [AudioGroup](media-services-mes-schema.md#AudioGroup)<br/><br/> minOccurs="0" |Ver Descrição [AudioGroup](media-services-mes-schema.md#AudioGroup) saber o número apropriado de canais, a taxa de amostragem e a taxa de bits que poderia ser definida para cada perfil. |

## <a name="AudioGroup"></a> AudioGroup
Para obter detalhes sobre quais os valores são válidos para cada perfil, consulte a tabela "Detalhes do codec de áudio" que se segue.  

### <a name="elements"></a>Elementos

| Name | Tipo | Descrição |
| --- | --- | --- |
| **canais**<br/><br/> minOccurs="0" |**xs: int** |O número de canais de áudio codificado. Seguem-se as opções válidas: 1, 2, 5, 6, 8.<br/><br/> Predefinição: 2. |
| **SamplingRate**<br/><br/> minOccurs="0" |**xs: int** |A taxa de amostragem de áudio, especificada no Hz. |
| **Velocidade de transmissão**<br/><br/> minOccurs="0" |**xs: int** |A velocidade de transmissão utilizada quando a codificação de áudio, especificado em kbps. |

### <a name="audio-codec-details"></a>Detalhes de codec de áudio

Codec de áudio|Detalhes  
-----------------|---  
**AACLC** |1:<br/><br/> - 11025: 8 &lt;= a velocidade de transmissão &lt; 16<br/><br/> - 12000: 8 &lt;= a velocidade de transmissão &lt; 16<br/><br/> - 16000: 8 &lt;= a velocidade de transmissão &lt;32<br/><br/>- 22050: 24 &lt;= a velocidade de transmissão &lt; 32<br/><br/> - 24000: 24 &lt;= a velocidade de transmissão &lt; 32<br/><br/> - 32000: 32 &lt;= a velocidade de transmissão &lt;= 192<br/><br/> - 44100: 56 &lt;= a velocidade de transmissão &lt;= 288<br/><br/> - 48000: 56 &lt;= a velocidade de transmissão &lt;= 288<br/><br/> - 88200 : 128 &lt;= a velocidade de transmissão &lt;= 288<br/><br/> - 96000 : 128 &lt;= a velocidade de transmissão &lt;= 288<br/><br/> 2:<br/><br/> - 11025: 16 &lt;= a velocidade de transmissão &lt; 24<br/><br/> - 12000: 16 &lt;= a velocidade de transmissão &lt; 24<br/><br/> - 16000: 16 &lt;= a velocidade de transmissão &lt; 40<br/><br/> - 22050: 32 &lt;= a velocidade de transmissão &lt; 40<br/><br/> - 24000 : 32 &lt;= a velocidade de transmissão &lt; 40<br/><br/> - 32000:  40 &lt;= a velocidade de transmissão &lt;= 384<br/><br/> - 44100: 96 &lt;= a velocidade de transmissão &lt;= 576<br/><br/> - 48000 : 96 &lt;= a velocidade de transmissão &lt;= 576<br/><br/> - 88200: 256 &lt;= a velocidade de transmissão &lt;= 576<br/><br/> - 96000: 256 &lt;= a velocidade de transmissão &lt;= 576<br/><br/> 5/6:<br/><br/> - 32000: 160 &lt;= a velocidade de transmissão &lt;= 896<br/><br/> - 44100: 240 &lt;= a velocidade de transmissão &lt;= 1024<br/><br/> - 48000: 240 &lt;= a velocidade de transmissão &lt;= 1024<br/><br/> - 88200: 640 &lt;= a velocidade de transmissão &lt;= 1024<br/><br/> - 96000: 640 &lt;= a velocidade de transmissão &lt;= 1024<br/><br/> 8:<br/><br/> - 32000 : 224 &lt;= a velocidade de transmissão &lt;= 1024<br/><br/> - 44100 : 384 &lt;= a velocidade de transmissão &lt;= 1024<br/><br/> - 48000: 384 &lt;= a velocidade de transmissão &lt;= 1024<br/><br/> - 88200: 896 &lt;= a velocidade de transmissão &lt;= 1024<br/><br/> - 96000: 896 &lt;= a velocidade de transmissão &lt;= 1024  
**HEAACV1** |1:<br/><br/> - 22050: bitrate = 8<br/><br/> - 24000: 8 &lt;= a velocidade de transmissão &lt;= 10<br/><br/> - 32000: 12 &lt;= a velocidade de transmissão &lt;= 64<br/><br/> - 44100: 20 &lt;= a velocidade de transmissão &lt;= 64<br/><br/> - 48000: 20 &lt;= a velocidade de transmissão &lt;= 64<br/><br/> - 88200: bitrate = 64<br/><br/> 2:<br/><br/> - 32000: 16 &lt;= a velocidade de transmissão &lt;= 128<br/><br/> - 44100: 16 &lt;= a velocidade de transmissão &lt;= 128<br/><br/> - 48000: 16 &lt;= a velocidade de transmissão &lt;= 128<br/><br/> - 88200 : 96 &lt;= a velocidade de transmissão &lt;= 128<br/><br/> - 96000: 96 &lt;= a velocidade de transmissão &lt;= 128<br/><br/> 5/6:<br/><br/> - 32000 : 64 &lt;= a velocidade de transmissão &lt;= 320<br/><br/> - 44100: 64 &lt;= a velocidade de transmissão &lt;= 320<br/><br/> - 48000: 64 &lt;= a velocidade de transmissão &lt;= 320<br/><br/> - 88200 : 256 &lt;= a velocidade de transmissão &lt;= 320<br/><br/> - 96000: 256 &lt;= a velocidade de transmissão &lt;= 320<br/><br/> 8:<br/><br/> - 32000: 96 &lt;= a velocidade de transmissão &lt;= 448<br/><br/> - 44100: 96 &lt;= a velocidade de transmissão &lt;= 448<br/><br/> - 48000: 96 &lt;= a velocidade de transmissão &lt;= 448<br/><br/> - 88200: 384 &lt;= a velocidade de transmissão &lt;= 448<br/><br/> - 96000: 384 &lt;= a velocidade de transmissão &lt;= 448  
**HEAACV2** |2:<br/><br/> - 22050: 8 &lt;= a velocidade de transmissão &lt;= 10<br/><br/> - 24000: 8 &lt;= a velocidade de transmissão &lt;= 10<br/><br/> - 32000: 12 &lt;= a velocidade de transmissão &lt;= 64<br/><br/> - 44100: 20 &lt;= a velocidade de transmissão &lt;= 64<br/><br/> - 48000: 20 &lt;= a velocidade de transmissão &lt;= 64<br/><br/> - 88200: 64 &lt;= a velocidade de transmissão &lt;= 64  
  
## <a name="Clip"></a> Clip
### <a name="attributes"></a>Atributos

| Name | Tipo | Descrição |
| --- | --- | --- |
| **StartTime** |**xs:duration** |Especifica a hora de início de uma apresentação. O valor de StartTime tem de corresponder os carimbos de data / absoluto de vídeo de entrada. Por exemplo, se o primeiro quadro de vídeo de entrada tem um carimbo de 12:00:10.000, em seguida, StartTime deve ser, pelo menos, 12:00:10.000 ou superior. |
| **Duração** |**xs:duration** |Especifica a duração de uma apresentação (por exemplo, a aparência de uma sobreposição no vídeo). |

## <a name="Output"></a> Saída
### <a name="attributes"></a>Atributos

| Name | Tipo | Descrição |
| --- | --- | --- |
| **FileName** |**xs:string** |O nome do ficheiro de saída.<br/><br/> Pode usar macros descritas na tabela seguinte para criar os nomes de ficheiro de saída. Por exemplo:<br/><br/> **"Outputs": [      {       "FileName": "{Basename}*{Resolution}*{Bitrate}.mp4",       "Format": {         "Type": "MP4Format"       }     }   ]** |

### <a name="macros"></a>Macros

| Macro | Descrição |
| --- | --- |
| **{Basename}** |Se estiver fazendo a codificação VoD, {Nome_de_base} é os primeiro 32 carateres da propriedade AssetFile.Name do ficheiro principal no recurso de entrada.<br/><br/> Se o elemento de entrada for um arquivo em direto, em seguida, {Nome_de_base} é derivado dos atributos de trackName no manifesto do servidor. Se estiver a enviar uma tarefa do subclip com TopBitrate, como em: "< VideoStream\>TopBitrate < / VideoStream\>" e o ficheiro de saída contém vídeo, em seguida, {Nome_de_base} é os primeiro 32 carateres de trackName da camada de vídeo com a velocidade de transmissão mais alta.<br/><br/> Se em vez disso, que está a enviar uma tarefa de subclip com todas as velocidades de transmissão de entrada, tais como "< VideoStream\>* < / VideoStream\>" e o ficheiro de saída contém vídeo, em seguida, {Nome_de_base} é os primeiro 32 carateres de trackName das camada de vídeo correspondente. |
| **{Codec}** |Para vídeo é mapeado para "H264" e "AAC" para áudio. |
| **{Bitrate}** |A destino vídeo velocidade de transmissão se o ficheiro de saída contém vídeo e áudio ou a velocidade de transmissão de áudio de destino, se o ficheiro de saída contém apenas áudio. O valor utilizado é a velocidade de transmissão em kbps. |
| **{Channel}** |Contagem de canal de áudio se o ficheiro contiver áudio. |
| **{Width}** |Largura do vídeo, em pixels, no arquivo de saída, se o ficheiro contiver o vídeo. |
| **{Height}** |Altura do vídeo, em pixels, no arquivo de saída, se o ficheiro contiver o vídeo. |
| **{Extension}** |Herda a propriedade "Type" para o ficheiro de saída. O nome de ficheiro de saída tem uma extensão que é um de: "mp4", "ts", "jpg", "png" ou "bmp". |
| **{Index}** |Obrigatório para a miniatura. Só devem estar presente uma vez. |

## <a name="Video"></a> Vídeo (tipo complexo herda de Codec)
### <a name="attributes"></a>Atributos

| Name | Tipo | Descrição |
| --- | --- | --- |
| **Começar** |**xs:string** | |
| **Passo** |**xs:string** | |
| **Range** |**xs:string** | |
| **PreserveResolutionAfterRotation** |**xs:boolean** |Para obter explicações detalhadas, consulte a secção seguinte: [PreserveResolutionAfterRotation](media-services-mes-schema.md#PreserveResolutionAfterRotation) |

### <a name="PreserveResolutionAfterRotation"></a> PreserveResolutionAfterRotation
É recomendado que utilize o **PreserveResolutionAfterRotation** sinalizador em combinação com os valores de resolução expressado em termos de percentagem (Width = "100%", altura = "100%").  

Por predefinição, as definições de resolução de codificar (largura, altura) na pré-visualizando Media Encoder Standard (MES) são destinadas a vídeos com rotação de 0 graus. Por exemplo, se o seu vídeo de entrada é 1280 x 720, com rotação de zero graus, em seguida, pré-visualizando o padrão Certifique-se de que a saída tem a mesma resolução.  

![MESRoation1](./media/media-services-shemas/media-services-mes-roation1.png) 

Se o vídeo de entrada tem sido capturado com rotação de diferente de zero (por exemplo, um smartphone ou tablet mantidos verticalmente), em seguida, MES por predefinição aplica-se as definições de resolução de codificar (largura, altura) para o vídeo de entrada e compensar, em seguida, a rotação. Por exemplo, veja a imagem que se segue. A predefinição utiliza largura = "100%", altura = "100%", o que interpreta o MES como exigir que a saída seja 1280 pixels de largura e 720 pixels de altura. Depois de efetuar a rotação de vídeo, em seguida, diminui a foto para se encaixa nessa janela, levando a áreas de pillar-box à esquerda e direita.  

![MESRoation2](./media/media-services-shemas/media-services-mes-roation2.png) 

Em alternativa, pode fazer uso do **PreserveResolutionAfterRotation** sinalizar e defini-lo como "true" (a predefinição é "false"). Portanto, se a sua configuração predefinida tem largura "100%", altura = = "100%" e PreserveResolutionAfterRotation definido como "true", um vídeo de entrada, que é 1280 pixels de largura e 720 pixels de altura, com rotação de 90 graus produz uma saída com rotação de zero graus, mas 720 pixels de largura e 1280 pixels de altura. Veja a imagem seguinte:  

![MESRoation3](./media/media-services-shemas/media-services-mes-roation3.png) 

## <a name="FormatGroup"></a> FormatGroup (grupo)
### <a name="elements"></a>Elementos

| Name | Tipo | Descrição |
| --- | --- | --- |
| **BmpFormat** |**BmpFormat** | |
| **PngFormat** |**PngFormat** | |
| **JpgFormat** |**JpgFormat** | |

## <a name="BmpLayer"></a> BmpLayer
### <a name="element"></a>Elemento

| Name | Tipo | Descrição |
| --- | --- | --- |
| **Width**<br/><br/> minOccurs="0" |**xs:int** | |
| **Height**<br/><br/> minOccurs="0" |**xs:int** | |

### <a name="attributes"></a>Atributos

| Name | Tipo | Descrição |
| --- | --- | --- |
| **condição** |**xs:string** | |

## <a name="PngLayer"></a> PngLayer
### <a name="element"></a>Elemento

| Name | Tipo | Descrição |
| --- | --- | --- |
| **Width**<br/><br/> minOccurs="0" |**xs:int** | |
| **Height**<br/><br/> minOccurs="0" |**xs:int** | |

### <a name="attributes"></a>Atributos

| Name | Tipo | Descrição |
| --- | --- | --- |
| **condição** |**xs:string** | |

## <a name="JpgLayer"></a> JpgLayer
### <a name="element"></a>Elemento

| Name | Tipo | Descrição |
| --- | --- | --- |
| **Width**<br/><br/> minOccurs="0" |**xs:int** | |
| **Height**<br/><br/> minOccurs="0" |**xs:int** | |
| **Qualidade**<br/><br/> minOccurs="0" |**xs:int** |Valores válidos: 1(worst)-100(Best) |

### <a name="attributes"></a>Atributos

| Name | Tipo | Descrição |
| --- | --- | --- |
| **condição** |**xs:string** | |

## <a name="PngLayers"></a> PngLayers
### <a name="elements"></a>Elementos

| Name | Tipo | Descrição |
| --- | --- | --- |
| **PngLayer**<br/><br/> minOccurs="0" maxOccurs="unbounded" |[PngLayer](media-services-mes-schema.md#PngLayer) | |

## <a name="BmpLayers"></a> BmpLayers
### <a name="elements"></a>Elementos

| Name | Tipo | Descrição |
| --- | --- | --- |
| **BmpLayer**<br/><br/> minOccurs="0" maxOccurs="unbounded" |[BmpLayer](media-services-mes-schema.md#BmpLayer) | |

## <a name="JpgLayers"></a> JpgLayers
### <a name="elements"></a>Elementos

| Name | Tipo | Descrição |
| --- | --- | --- |
| **JpgLayer**<br/><br/> minOccurs="0" maxOccurs="unbounded" |[JpgLayer](media-services-mes-schema.md#JpgLayer) | |

## <a name="BmpImage"></a> BmpImage (tipo complexo herda de vídeo)
### <a name="elements"></a>Elementos

| Name | Tipo | Descrição |
| --- | --- | --- |
| **PngLayers**<br/><br/> minOccurs="0" |[PngLayers](media-services-mes-schema.md#PngLayers) |Camadas de PNG |

## <a name="JpgImage"></a> JpgImage (tipo complexo herda de vídeo)
### <a name="elements"></a>Elementos

| Name | Tipo | Descrição |
| --- | --- | --- |
| **PngLayers**<br/><br/> minOccurs="0" |[PngLayers](media-services-mes-schema.md#PngLayers) |Camadas de PNG |

## <a name="PngImage"></a> PngImage (tipo complexo herda de vídeo)
### <a name="elements"></a>Elementos

| Name | Tipo | Descrição |
| --- | --- | --- |
| **PngLayers**<br/><br/> minOccurs="0" |[PngLayers](media-services-mes-schema.md#PngLayers) |Camadas de PNG |

## <a name="examples"></a>Exemplos
Veja exemplos configurações predefinidas de XML que são criadas com base neste esquema, consulte [predefinições de tarefas para MES (Media Encoder Standard)](media-services-mes-presets-overview.md).

## <a name="next-steps"></a>Passos Seguintes
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

