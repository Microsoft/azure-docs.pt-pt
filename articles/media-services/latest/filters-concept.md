---
title: Definição de filtros nos Serviços de Media Azure
description: Este tópico descreve como criar filtros para que o seu cliente possa usá-los para transmitir secções específicas de um fluxo. Os Media Services criam manifestos dinâmicos para alcançar este streaming seletivo.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: bb5561ced93c3f5a899c6e48fdab0f14e52914bb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "89291556"
---
# <a name="filters"></a>Filtros

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Ao entregar o seu conteúdo aos clientes (eventos de Streaming ao Vivo ou Vídeo a Pedido) o seu cliente poderá necessitar de mais flexibilidade do que o descrito no ficheiro manifesto do ativo padrão. A Azure Media Services oferece [Manifestos Dinâmicos baseados](filters-dynamic-manifest-overview.md) em filtros pré-definidos. 

Os filtros são regras do lado do servidor que permitem aos seus clientes fazer coisas como: 

- Reproduzir apenas uma secção de um vídeo (em vez de reproduzir todo o vídeo). Por exemplo:
  - Reduza o manifesto para mostrar um sub-clip de um evento ao vivo ("filtragem sub-clip"), ou
  - Corte o início de um vídeo ("aparar um vídeo").
- Entregue apenas as representações especificadas e/ou faixas linguísticas especificadas que são suportadas pelo dispositivo que é utilizado para reproduzir o conteúdo ("filtragem de rendição"). 
- Ajuste a Janela de Apresentação (DVR) de modo a proporcionar um comprimento limitado da janela DVR no leitor ("janela de apresentação de ajuste").

Os Serviços de Mídia permitem criar **filtros de conta** e **filtros de ativos** para o seu conteúdo. Além disso, pode associar os filtros pré-criados a um **Localizador de Streaming.**

## <a name="defining-filters"></a>Filtragem de definição

Existem dois tipos de filtros: 

* [Filtros de Conta](/rest/api/media/accountfilters) (global) - pode ser aplicado a qualquer ativo na conta Azure Media Services, ter uma vida inteira da conta.
* [Filtros de Ativos](/rest/api/media/assetfilters) (locais) - só pode ser aplicado a um ativo com o qual o filtro foi associado aquando da criação, ter uma vida útil do ativo. 

**Os tipos de Filtros de Conta** e **Filtros de Ativos** têm exatamente as mesmas propriedades para definir/descrever o filtro. Exceto ao criar o **Filtro de Ativos,** é necessário especificar o nome do ativo com o qual pretende associar o filtro.

Dependendo do seu cenário, decide que tipo de filtro é mais adequado (Filtro de Ativos ou Filtro de Conta). Os filtros de conta são adequados para perfis de dispositivos (filtragem de rendição) onde os filtros de ativos podem ser usados para aparar um ativo específico.

Utiliza as seguintes propriedades para descrever os filtros. 

|Nome|Descrição|
|---|---|
|primeira Igualdade|O primeiro bitrate de qualidade do filtro.|
|apresentaçãoTimeRange|O intervalo de tempo de apresentação. Esta propriedade é utilizada para filtrar pontos de partida/fim manifestos, comprimento da janela de apresentação e posição de arranque ao vivo. <br/>Para mais informações, consulte [PresentationTimeRange](#presentationtimerange).|
|faixas|As condições de seleção dos trilhos. Para mais informações, consulte [as faixas](#tracks)|

### <a name="presentationtimerange"></a>apresentaçãoTimeRange

Utilize esta propriedade com **filtros de ativos.** Não é aconselhável definir o imóvel com **Filtros de Conta.**

|Nome|Descrição|
|---|---|
|**endTimestamp**|Aplica-se a Vídeo a Pedido (VoD).<br/>Para a apresentação live streaming, é silenciosamente ignorado e aplicado quando a apresentação termina e o fluxo torna-se VoD.<br/>Este é um valor longo que representa um ponto final absoluto da apresentação, arredondado para o próximo gop arranque mais próximo. A unidade é o calendário, por isso um fimtimestamp de 180000000000 seria por 3 minutos.<br/>Utilize o startTimestamp e endTimestamp para aparar os fragmentos que estarão na lista de reprodução (manifesto).<br/>Por exemplo, startTimestamp=400000000 e endTimestamp=10000000000, utilizando o calendário padrão, gerará uma lista de reprodução que contém fragmentos entre 4 segundos e 10 segundos da apresentação do VoD. Se um fragmento se desbardar no limite, todo o fragmento será incluído no manifesto.|
|**forceEndTimestamp**|Aplica-se apenas ao Live Streaming.<br/>Indica se a propriedade endTimestamp deve estar presente. Se for verdade, o fimtimestamp deve ser especificado ou um código de pedido mau é devolvido.<br/>Valores permitidos: falsos, verdadeiros.|
|**liveBackoffDuration**|Aplica-se apenas ao Live Streaming.<br/> Este valor define a mais recente posição ao vivo que um cliente pode procurar.<br/>Utilizando esta propriedade, pode atrasar a posição de reprodução ao vivo e criar um tampão lateral para os jogadores.<br/>A unidade para esta propriedade é um calendário (ver abaixo).<br/>A duração máxima de volta ao vivo é de 300 segundos (3000000000).<br/>Por exemplo, um valor de 2000000000000000000000000000000000000000000000000000000000000000000|
|**apresentaçãoWindowDuration**|Aplica-se apenas ao Live Streaming.<br/>Utilize a apresentaçãoWindowDuration para aplicar uma janela deslizante de fragmentos para incluir numa lista de reprodução.<br/>A unidade para esta propriedade é um calendário (ver abaixo).<br/>Por exemplo, desemboe a apresentaçãoWindowDuration=120000000000 para aplicar uma janela deslizante de dois minutos. Os meios de comunicação dentro de 2 minutos da borda ao vivo serão incluídos na lista de reprodução. Se um fragmento contornar o limite, todo o fragmento será incluído na lista de reprodução. A duração mínima da janela de apresentação é de 60 segundos.|
|**startTimestamp**|Aplica-se a Vídeo a Pedido (VoD) ou Live Streaming.<br/>Este é um valor longo que representa um ponto de partida absoluto do fluxo. O valor é arredondado para o próximo gop mais próximo. A unidade é o calendário, por isso um carregamento de 1500000000 seria por 15 segundos.<br/>Utilize o startTimestamp e endTimestampp para aparar os fragmentos que estarão na lista de reprodução (manifesto).<br/>Por exemplo, startTimestamp=400000000 e endTimestamp=10000000000, utilizando o calendário padrão, gerará uma lista de reprodução que contém fragmentos entre 4 segundos e 10 segundos da apresentação do VoD. Se um fragmento se desbardar no limite, todo o fragmento será incluído no manifesto.|
|**escala de tempo**|Aplica-se a todos os tempos e durações num intervalo de tempo de apresentação, especificado como o número de incrementos num segundo.<br/>O padrão é de 10000000 - dez milhões de incrementos num segundo, onde cada incremento teria 100 nanosegundos de comprimento.<br/>Por exemplo, se pretender definir um relógio startTimestamp a 30 segundos, utilizará um valor de 3000000000000 ao utilizar o prazo padrão.|

### <a name="tracks"></a>Faixas

Especifica uma lista de condições de propriedade da faixa de filtro (FilterTrackPropertyConditions) com base na qual as faixas do seu stream (Live Streaming ou Video on Demand) devem ser incluídas num manifesto criado dinamicamente. Os filtros são combinados utilizando uma operação **lógica e** **de OR.**

As condições de propriedade da faixa de filtro descrevem tipos de faixas, valores (descritos na tabela seguinte) e operações (Equal, NotEqual). 

|Nome|Descrição|
|---|---|
|**Bitrate**|Utilize o bitrate da pista para filtrar.<br/><br/>O valor recomendado é uma gama de bitrates, em bits por segundo. Por exemplo, "0-2427000".<br/><br/>Nota: embora possa utilizar um valor bitrate específico, como 250000 (bits por segundo), esta abordagem não é recomendada, uma vez que os bitrates exatos podem oscilar de um Ativo para outro.|
|**FourCC**|Utilize o valor FourCC da faixa para filtragem.<br/><br/>O valor é o primeiro elemento do formato codecs, conforme especificado no [RFC 6381](https://tools.ietf.org/html/rfc6381). Atualmente, os seguintes códigos são suportados: <br/>Para Vídeo: "avc1", "hev1", "hvc1"<br/>Para Áudio: "mp4a", "ec-3"<br/><br/>Para determinar os valores de FourCC para faixas num Ativo, obtenha e examine o ficheiro manifesto.|
|**Linguagem**|Utilize o idioma da faixa para filtrar.<br/><br/>O valor é a etiqueta de um idioma que pretende incluir, conforme especificado no RFC 5646. Por exemplo, "en".|
|**Nome**|Utilize o nome da faixa para filtragem.|
|**Tipo**|Utilize o tipo de faixa para filtragem.<br/><br/>São permitidos os seguintes valores: "vídeo", "áudio" ou "texto".|

### <a name="example"></a>Exemplo

O exemplo a seguir define um filtro live streaming: 

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

## <a name="associating-filters-with-streaming-locator"></a>Associar filtros com localizador de streaming

Pode especificar uma lista de [filtros](filters-concept.md) de ativos ou de conta no seu [Localizador de Streaming.](/rest/api/media/streaminglocators/create#request-body) O [Dynamic Packager](dynamic-packaging-overview.md) aplica esta lista de filtros juntamente com os que o seu cliente especifica no URL. Esta combinação gera um [Manifesto Dinâmico,](filters-dynamic-manifest-overview.md)que é baseado em filtros nos filtros URL + que especifica no Localizador de Streaming. 

Veja os exemplos seguintes:

* [Associar filtros com localizador de streaming - .NET](filters-dynamic-manifest-dotnet-howto.md#associate-filters-with-streaming-locator)
* [Associar filtros com localizador de streaming - CLI](filters-dynamic-manifest-cli-howto.md#associate-filters-with-streaming-locator)

## <a name="updating-filters"></a>Atualizar filtros
 
**Os localizadores de streaming** não são updatable enquanto os filtros podem ser atualizados. 

Não é aconselhável atualizar a definição de filtros associados a um **localizador de streaming** ativamente publicado , especialmente quando o CDN está ativado. Os servidores de streaming e os CDNs podem ter caches internos que podem resultar na devolvição de dados em cache. 

Se a definição do filtro tiver de ser alterada, considere criar um novo filtro e adicioná-lo ao URL **do localizador de streaming** ou publicar um novo **Localizador de Streaming** que faz referência direta ao filtro.

## <a name="next-steps"></a>Passos seguintes

Os seguintes artigos mostram como criar filtros programáticamente.  

- [Criar filtros com APIs REST](filters-dynamic-manifest-rest-howto.md)
- [Criar filtros com .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Criar filtros com CLI](filters-dynamic-manifest-cli-howto.md)
