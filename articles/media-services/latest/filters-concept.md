---
title: Definição de filtros nos Serviços De Mídia Azure
description: Este tópico descreve como criar filtros para que o seu cliente possa usá-los para transmitir secções específicas de um fluxo. A Media Services cria manifestos dinâmicos para alcançar este streaming seletivo.
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
ms.date: 05/23/2019
ms.author: juliako
ms.openlocfilehash: fdf29924da31db0347938df89e698cb258c2336b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79251469"
---
# <a name="filters"></a>Filtros

Ao entregar o seu conteúdo aos clientes (eventos de Streaming ao Vivo ou Vídeo a Pedido) o seu cliente poderá necessitar de mais flexibilidade do que o descrito no ficheiro manifesto do ativo predefinido. O Azure Media Services oferece [Manifestos Dinâmicos baseados](filters-dynamic-manifest-overview.md) em filtros pré-definidos. 

Os filtros são regras do lado do servidor que permitem aos seus clientes fazer coisas como: 

- Retoque apenas uma secção de um vídeo (em vez de reproduzir todo o vídeo). Por exemplo:
  - Reduzir o manifesto para mostrar um subclip de um evento ao vivo ("filtragem de sub-clip"), ou
  - Aparar o início de um vídeo ("aparar um vídeo").
- Entregue apenas as interpretações especificadas e/ou faixas linguísticas especificadas que são suportadas pelo dispositivo que é usado para reproduzir o conteúdo ("filtragem de rendição"). 
- Ajuste a Janela de Apresentação (DVR) de forma a fornecer um comprimento limitado da janela DVR no leitor ("janela de apresentação de ajuste").

O Media Services permite-lhe criar **filtros de Conta** e **filtros de Ativos** para o seu conteúdo. Além disso, pode associar os seus filtros pré-criados a um Localizador de **Streaming**.

## <a name="defining-filters"></a>Definição de filtros

Existem dois tipos de filtros: 

* [Filtros](https://docs.microsoft.com/rest/api/media/accountfilters) de Conta (global) - podem ser aplicados a qualquer ativo na conta Azure Media Services, ter uma vida útil da conta.
* [Filtros](https://docs.microsoft.com/rest/api/media/assetfilters) de ativos (locais) - só podem ser aplicados a um ativo com o qual o filtro foi associado à criação, têm uma vida útil do ativo. 

**Os filtros de conta** e os tipos de **filtros** de ativos têm exatamente as mesmas propriedades para definir/descrever o filtro. Exceto na criação do Filtro de **Ativo,** é necessário especificar o nome do ativo com o qual pretende associar o filtro.

Dependendo do seu cenário, você decide que tipo de filtro é mais adequado (Filtro de Ativo ou Filtro de Conta). Os filtros de conta são adequados para perfis de dispositivo (filtragem de rendição) onde os Filtros de Ativos podem ser usados para aparar um ativo específico.

Utiliza as seguintes propriedades para descrever os filtros. 

|Nome|Descrição|
|---|---|
|primeira Qualidade|A primeira bitrate de qualidade do filtro.|
|apresentaçãoTimeRange|O intervalo de tempo de apresentação. Esta propriedade é usada para filtrar pontos de início/fim manifestos, comprimento da janela de apresentação e a posição de início ao vivo. <br/>Para mais informações, consulte [PresentationTimeRange](#presentationtimerange).|
|faixas|As condições de seleção dos trilhos. Para mais informações, consulte [faixas](#tracks)|

### <a name="presentationtimerange"></a>apresentaçãoTimeRange

Utilize esta propriedade com filtros de **ativos.** Não é aconselhável definir a propriedade com **filtros**de conta .

|Nome|Descrição|
|---|---|
|**fimTimestamp**|Aplica-se ao Vídeo a Pedido (VoD).<br/>Para a apresentação do Live Streaming, é silenciosamente ignorado e aplicado quando a apresentação termina e o fluxo torna-se VoD.<br/>Este é um longo valor que representa um ponto final absoluto da apresentação, arredondado para o próximo início do GOP mais próximo. A unidade é o calendário, por isso um carimbo de tempo final de 180000000000 seria por 3 minutos.<br/>Utilize o carimbo de tempo de partida e o carimbo final Timetime para aparar os fragmentos que estarão na lista de reprodução (manifesto).<br/>Por exemplo, o startTimestamp=40000000e e o endTimestamp=1000000000 utilizando a escala de tempo padrão gerarão uma lista de reprodução que contenha fragmentos entre 4 segundos e 10 segundos da apresentação do VoD. Se um fragmento se situar na fronteira, todo o fragmento será incluído no manifesto.|
|**forceEndTimestamp**|Aplica-se apenas ao Live Streaming.<br/>Indica se a propriedade endTimestamp deve estar presente. Se for verdade, o carimbo final deve ser especificado ou devolvido um código de pedido mau.<br/>Valores permitidos: falso, verdadeiro.|
|**liveBackoffDura**|Aplica-se apenas ao Live Streaming.<br/> Este valor define a mais recente posição ao vivo que um cliente pode procurar.<br/>Utilizando esta propriedade, pode atrasar a posição de reprodução ao vivo e criar um tampão do lado do servidor para os jogadores.<br/>A unidade para esta propriedade é de tempo (ver abaixo).<br/>A duração máxima de recompra é de 300 segundos (3000000000000).<br/>Por exemplo, um valor de 200000000000000000000000 000 significa que o conteúdo disponível mais recente está a 20 segundos atrasado da verdadeira borda ao vivo.|
|**apresentaçãoDuração janela**|Aplica-se apenas ao Live Streaming.<br/>Utilize a apresentaçãoJanelaDura para aplicar uma janela deslizante de fragmentos para incluir numa lista de reprodução.<br/>A unidade para esta propriedade é de tempo (ver abaixo).<br/>Por exemplo, definir ApresentaçãoJanelaDura=1200000000000para aplicar uma janela de deslizamento de dois minutos. Os meios de comunicação dentro de 2 minutos da borda ao vivo serão incluídos na lista de reprodução. Se um fragmento ultrapassar o limite, todo o fragmento será incluído na lista de reprodução. A duração mínima da janela de apresentação é de 60 segundos.|
|**inícioTimestamp**|Aplica-se a Vídeo a Pedido (VoD) ou Live Streaming.<br/>Este é um longo valor que representa um ponto de partida absoluto do fluxo. O valor é arredondado para o próximo início do GOP mais próximo. A unidade é o calendário, por isso um carimbo inicial de 150000000seria por 15 segundos.<br/>Utilize o carimbo de tempo de partida e o carimbo final Timetime para aparar os fragmentos que estarão na lista de reprodução (manifesto).<br/>Por exemplo, o startTimestamp=40000000e e o endTimestamp=1000000000 utilizando a escala de tempo padrão gerarão uma lista de reprodução que contenha fragmentos entre 4 segundos e 10 segundos da apresentação do VoD. Se um fragmento se situar na fronteira, todo o fragmento será incluído no manifesto.|
|**escala de tempo**|Aplica-se a todos os selos e durações de um intervalo de tempo de apresentação, especificado como o número de incrementos num segundo.<br/>O padrão é de 10000000 - dez milhões de incrementos num segundo, onde cada incremento teria 100 nanosegundos de comprimento.<br/>Por exemplo, se quiser definir um carimbo de tempo de partida em 30 segundos, utilizará um valor de 300000000 quando utilizar o calendário predefinido.|

### <a name="tracks"></a>Faixas

Especifica uma lista de condições de propriedade de filtragem (FilterTrackPropertyConditions) com base na qual as faixas do seu fluxo (Live Streaming ou Video on Demand) devem ser incluídas num manifesto criado dinamicamente. Os filtros são combinados utilizando uma operação lógica **e** **or.**

As condições de propriedade da via filtram descrevem tipos de faixas, valores (descritos na tabela seguinte) e operações (Igual, Não Igual). 

|Nome|Descrição|
|---|---|
|**Bitrate**|Utilize a bitrate da pista para filtrar.<br/><br/>O valor recomendado é uma gama de bitrates, em bits por segundo. Por exemplo, "0-2427000".<br/><br/>Nota: embora possa utilizar um valor bitrate específico, como 250000 (bits por segundo), esta abordagem não é recomendada, uma vez que os bitrates exatos podem oscilar de um Ativo para outro.|
|**FourCC**|Utilize o valor FourCC da pista para filtragem.<br/><br/>O valor é o primeiro elemento do formato codecs, conforme especificado no [RFC 6381](https://tools.ietf.org/html/rfc6381). Atualmente, os seguintes códigos são suportados: <br/>Para Vídeo: "avc1", "hev1", "hvc1"<br/>Para Áudio: "mp4a", "ec-3"<br/><br/>Para determinar os valores do FourCC para faixas num Ativo, obtenha e examine o ficheiro manifesto.|
|**Idioma**|Utilize a linguagem da pista para filtrar.<br/><br/>O valor é a etiqueta de uma língua que pretende incluir, conforme especificado no RFC 5646. Por exemplo, "en".|
|**Nome**|Utilize o nome da pista para filtrar.|
|**Tipo**|Utilize o tipo de via para filtrar.<br/><br/>São permitidos os seguintes valores: "vídeo", "áudio" ou "texto".|

### <a name="example"></a>Exemplo

O exemplo que se segue define um filtro live streaming: 

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

Pode especificar uma lista de [filtros](filters-concept.md) de ativos ou conta no seu Localizador de [Streaming](https://docs.microsoft.com/rest/api/media/streaminglocators/create#request-body). O [Pacote Dinâmico](dynamic-packaging-overview.md) aplica esta lista de filtros juntamente com os especificados pelo seu cliente no URL. Esta combinação gera um [Manifesto Dinâmico,](filters-dynamic-manifest-overview.md)que é baseado em filtros no URL + filtros que especifica no Localizador de Streaming. 

Veja os exemplos seguintes:

* [Filtros associados com localizador de streaming - .NET](filters-dynamic-manifest-dotnet-howto.md#associate-filters-with-streaming-locator)
* [Filtros associados com localizador de streaming - CLI](filters-dynamic-manifest-cli-howto.md#associate-filters-with-streaming-locator)

## <a name="updating-filters"></a>Atualizar filtros
 
**Os localizadores** de streaming não são atualizados enquanto os filtros podem ser atualizados. 

Não é aconselhável atualizar a definição de filtros associados a um localizador de **streaming**publicado ativamente, especialmente quando a CDN está ativada. Servidores de streaming e CDNs podem ter caches internos que podem resultar em dados em cache a serem devolvidos. 

Se a definição de filtro tiver de ser alterada, considere criar um novo filtro e adicioná-lo ao URL do Localizador de **Streaming** ou publicar um novo Localizador de **Streaming** que referencia o filtro diretamente.

## <a name="next-steps"></a>Passos seguintes

Os seguintes artigos mostram como criar filtros programáticamente.  

- [Criar filtros com APIs REST](filters-dynamic-manifest-rest-howto.md)
- [Criar filtros com .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Criar filtros com CLI](filters-dynamic-manifest-cli-howto.md)

