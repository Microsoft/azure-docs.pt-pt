---
title: Definir filtros nos serviços de multimédia do Azure
description: Este tópico descreve como criar filtros para que o cliente possa utilizar secções específicas do fluxo de um fluxo. Serviços de multimédia cria os manifestos dinâmicos para alcançar este seletiva de transmissão em fluxo.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 02/12/2019
ms.author: juliako
ms.openlocfilehash: 09de372ffdb48c00fde9a43c07f8f8b574462d1f
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2019
ms.locfileid: "56405728"
---
# <a name="define-account-filters-and-asset-filters"></a>Definir filtros de conta e filtros de elemento  

Quando a entrega de conteúdo aos clientes (transmissão em fluxo eventos em direto ou de vídeo a pedido) o cliente poderá ter mais flexibilidade do que o que é descrito no arquivo de manifesto do recurso padrão. Serviços de multimédia do Azure permite-lhe definir os filtros de conta e filtros de recurso para o seu conteúdo. 

Os filtros são regras do lado do servidor que permitem que os clientes podem fazer coisas como: 

- Reproduza apenas uma secção de um vídeo (em vez de reproduzir o vídeo todo). Por exemplo:
  - Reduzir o manifesto para mostrar um subclips de um evento em direto ("subclips filtragem"), ou
  - Corte o início de um vídeo ("recortar um vídeo").
- Entrega apenas as representações especificadas e/ou faixas de idioma especificado que são suportadas pelo dispositivo que é utilizado para reproduzir o conteúdo ("filtragem de representação"). 
- Ajuste a janela de apresentação (DVR) a fim de fornecer uma duração limitada da janela DVR no player de ("Ajuste janela apresentação").

Serviços de multimédia oferecem [manifestos dinâmica](filters-dynamic-manifest-overview.md) com base nos filtros predefinidos. Depois de definir filtros, os clientes podem utilizá-los o URL de transmissão em fluxo. Filtros podem ser aplicados a velocidade de transmissão adaptável, protocolos de transmissão em fluxo: Apple HTTP Live Streaming (HLS), MPEG-DASH e Smooth Streaming.

A tabela seguinte mostra alguns exemplos de URLs com filtros:

|Protocolo|Exemplo|
|---|---|
|HLS V4|`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|HLS V3|`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3,filter=myAccountFilter)`|
|MPEG DASH|`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Transmissão em Fluxo Uniforme|`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=myAssetFilter)`|

## <a name="define-filters"></a>Definir filtros

Existem dois tipos de filtros ativos: 

* [Filtros de conta](https://docs.microsoft.com/rest/api/media/accountfilters) (global) - podem ser aplicados a qualquer recurso na conta de Media Services do Azure, têm uma duração da conta.
* [Filtros ativos](https://docs.microsoft.com/rest/api/media/assetfilters) (local) - apenas pode ser aplicado a um recurso com a qual o filtro foi associado após a criação, ter um tempo de vida do ativo. 

[Filtro de contas](https://docs.microsoft.com/rest/api/media/accountfilters) e [Asset filtro](https://docs.microsoft.com/rest/api/media/assetfilters) tipos têm exatamente as mesmas propriedades para definir/descrever o filtro. Exceto quando criar o **filtro de elemento**, tem de especificar o nome do elemento com a qual pretende associar o filtro.

Dependendo do seu cenário, decidir que tipo de um filtro é mais adequada (filtro de elemento ou filtro de conta). Filtros de conta são adequados para perfis de dispositivo (filtragem de representação) onde os filtros ativos poderia ser usados para cortar um recurso específico.

Utilize as seguintes propriedades para descrever os filtros. 

|Name|Descrição|
|---|---|
|firstQuality|A primeira qualidade velocidade de transmissão do filtro.|
|presentationTimeRange|O intervalo de tempo de apresentação. Esta propriedade é utilizada para filtragem de pontos de início/fim manifesto, duração da janela de apresentação e a posição inicial em direto. <br/>Para obter mais informações, consulte [PresentationTimeRange](#PresentationTimeRange).|
|roteiros|As condições de seleção de faixas. Para obter mais informações, consulte [roteiros](#tracks)|

### <a name="presentationtimerange"></a>PresentationTimeRange

Use essa propriedade com o **Asset filtros**. Não é recomendado para definir a propriedade com o **filtros de conta**.

|Name|Descrição|
|---|---|
|**endTimestamp**|O limite de tempo absoluto final. Aplica-se para vídeo a pedido (VoD). Para a apresentação em direto, é ignorada e aplicada automaticamente quando as extremidades de apresentação e o fluxo de se tornar VoD.<br/><br/>O valor representa um ponto final absoluto da transmissão em fluxo. -É arredondado para o próximo início de GOP mais próximo.<br/><br/>Utilize StartTimestamp e EndTimestamp para cortar a lista de reprodução (manifesto). Por exemplo, StartTimestamp = 40000000 e EndTimestamp = 100000000 irá gerar uma lista de reprodução que contém o suporte de dados entre StartTimestamp e EndTimestamp. Se um fragmento faz a ponte do limite, o fragmento todo será incluído no manifesto.<br/><br/>Além disso, veja a **forceEndTimestamp** definição que se segue.|
|**forceEndTimestamp**|Aplica-se aos filtros em direto.<br/><br/>**forceEndTimestamp** é um valor booleano que indica se é ou não **endTimestamp** foi definido para um valor válido. <br/><br/>Se o valor for **true**, o **endTimestamp** deve ser especificado qualquer valor. Se não for especificada, em seguida, é devolvido um pedido incorreto.<br/><br/>Se, por exemplo, pretende definir um filtro que começa em 5 minutos para o vídeo de entrada e dura até ao final da transmissão em fluxo, definiria **forceEndTimestamp** como falso e omitir a definição **endTimestamp**.|
|**liveBackoffDuration**|Aplica-se em direto apenas. A propriedade é usada para definir a posição de reprodução em direto. Com esta regra, pode atrasar a posição de reprodução em direto e criar uma memória intermédia do lado do servidor para jogadores. LiveBackoffDuration é relativo a posição em direto. A duração máxima de término em direto é de 300 segundos.|
|**presentationWindowDuration**|Aplica-se em direto. Uso **presentationWindowDuration** para aplicar uma janela deslizante para a lista de reprodução. Por exemplo, definir presentationWindowDuration = 1200000000 para aplicar uma janela deslizante de dois minutos. Suporte de dados em dois minutos de borda em direto serão incluídos na playlist. Se um fragmento faz a ponte do limite, o fragmento todo será incluído na playlist. Duração da janela de apresentação mínimo é de 60 segundos.|
|**startTimestamp**|Aplica-se aos fluxos VoD ou em direto. O valor representa um ponto de início absoluto da transmissão em fluxo. O valor é arredondado para o próximo início de GOP mais próximo.<br/><br/>Uso **startTimestamp** e **endTimestamp** para cortar a lista de reprodução (manifesto). Por exemplo, startTimestamp = 40000000 e endTimestamp = 100000000 irá gerar uma lista de reprodução que contém o suporte de dados entre StartTimestamp e EndTimestamp. Se um fragmento faz a ponte do limite, o fragmento todo será incluído no manifesto.|
|**timescale**|Aplica-se aos fluxos VoD ou em direto. A escala temporal utilizado pelos carimbos e durações especificadas acima. A escala temporal padrão é 10000000. Pode ser utilizada uma escala temporal alternativo. A predefinição é 10000000 HNS (nanossegundos centenas).|

### <a name="tracks"></a>roteiros

Especifique uma lista de condições de propriedade de controle de filtro (FilterTrackPropertyConditions) com base no qual as faixas de sua transmissão em fluxo (Live ou vídeo a pedido) devem ser incluídas no manifesto criado dinamicamente. Os filtros são combinados com uma lógica **AND** e **ou** operação.

Condições de propriedade de controle de filtro descrevem os tipos de controlo, valores (descritos na tabela a seguir) e operações (igual, NotEqual). 

|Name|Descrição|
|---|---|
|**Velocidade de transmissão**|Utilize a velocidade de transmissão da faixa para filtragem.<br/><br/>O valor recomendado é um intervalo de velocidades de transmissão em bits por segundo. Por exemplo, "0-2427000".<br/><br/>Nota: Embora seja possível usar um valor de velocidade de transmissão específica, como 250000 (bits por segundo), essa abordagem não é recomendada, como as velocidades de transmissão exatas podem variar de um recurso para outro.|
|**FourCC**|Utilize o valor de FourCC da faixa de filtragem.<br/><br/>O valor é o primeiro elemento do formato de codecs, conforme especificado nas [RFC 6381](https://tools.ietf.org/html/rfc6381). Atualmente, são suportados os codecs seguintes: <br/>Vídeo: "Avc1", "hev1", "hvc1"<br/>Para áudio: "Mp4a", da "ec-3"<br/><br/>Para determinar os valores de FourCC roteiros num elemento [obter e examinar o arquivo de manifesto](#get-and-examine-manifest-files).|
|**Language** (Idioma)|Utilize a linguagem da faixa para filtragem.<br/><br/>O valor é a marca de um idioma que pretende incluir, como especificado no RFC 5646. Por exemplo, "pt".|
|**Nome**|Utilize o nome da faixa para filtragem.|
|**Tipo**|Utilize o tipo da faixa para filtragem.<br/><br/>São permitidos os seguintes valores: "vídeo", "áudio" ou "text".|

## <a name="example"></a>Exemplo

```json
{
  "properties": {
    "presentationTimeRange": {
      "startTimestamp": 0,
      "endTimestamp": 170000000,
      "presentationWindowDuration": 9223372036854776000,
      "liveBackoffDuration": 0,
      "timescale": 10000000,
      "forceEndTimestamp": false
    },
    "firstQuality": {
      "bitrate": 128000
    },
    "tracks": [
      {
        "trackSelections": [
          {
            "property": "Type",
            "operation": "Equal",
            "value": "Audio"
          },
          {
            "property": "Language",
            "operation": "NotEqual",
            "value": "en"
          },
          {
            "property": "FourCC",
            "operation": "NotEqual",
            "value": "EC-3"
          }
        ]
      },
      {
        "trackSelections": [
          {
            "property": "Type",
            "operation": "Equal",
            "value": "Video"
          },
          {
            "property": "Bitrate",
            "operation": "Equal",
            "value": "3000000-5000000"
          }
        ]
      }
    ]
  }
}
```

## <a name="next-steps"></a>Passos Seguintes

Os artigos seguintes mostram como criar filtros de forma programática.  

- [Criar filtros com as APIs REST](filters-dynamic-manifest-rest-howto.md)
- [Criar filtros com .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Criar filtros com a CLI](filters-dynamic-manifest-cli-howto.md)

