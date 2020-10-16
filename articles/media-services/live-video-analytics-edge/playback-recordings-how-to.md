---
title: Reprodução de gravações - Azure
description: Pode utilizar o Live Video Analytics no IoT Edge para gravação contínua de vídeo, através do qual pode gravar vídeos na nuvem durante semanas ou meses. Também pode limitar a sua gravação a clips que sejam de interesse, através de gravações baseadas em eventos. Este artigo fala sobre como reproduzir gravações.
ms.topic: how-to
ms.date: 04/27/2020
ms.openlocfilehash: 6222d2c05b2fe05945d4bcbef6dbb0d64bd4726a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84261080"
---
# <a name="playback-of-recordings"></a>Reprodução de gravações 

## <a name="pre-read"></a>Pré-leitura  

* [Reproduzir vídeo](video-playback-concept.md)
* [Gravação de vídeo contínua](continuous-video-recording-concept.md)
* [Gravação de vídeo baseada em eventos](event-based-video-recording-concept.md)

## <a name="background"></a>Fundo  

Pode utilizar o Live Video Analytics no IoT Edge para [gravação contínua](continuous-video-recording-concept.md) de vídeo (CVR), através do qual pode gravar vídeo na nuvem durante semanas ou meses. Também pode limitar a sua gravação a clips que sejam de interesse, através de [gravação de vídeo baseada em eventos](event-based-video-recording-concept.md) (EVR). 

A sua conta de Media Service está ligada a uma conta de Armazenamento Azure e, quando grava o vídeo na nuvem, o conteúdo é escrito para um [ativo do Media Service](terminology.md#asset). Os Serviços de Comunicação Social permitem-lhe [transmitir esse conteúdo](terminology.md#streaming), quer após a gravação, quer enquanto a gravação estiver em curso. Os Serviços de Comunicação Social fornecem-lhe as capacidades necessárias para entregar streams através de protocolos HLS ou MPEG-DASH a dispositivos de reprodução (clientes). Veja o artigo [de reprodução de vídeo](video-playback-concept.md) para mais detalhes. Utilizaria APIs de Serviço de Mídia para gerar os URLs de streaming necessários – utilizados pelos clientes para reproduzir o vídeo & áudio. Para construir o URL de streaming para um ativo, consulte [criar um localizador de streaming e construir URLs](../latest/create-streaming-locator-build-url.md). Uma vez criado o URL de streaming para um ativo, pode e deve armazenar a associação do URL com a fonte de vídeo (isto é, câmara) no seu sistema de gestão de conteúdos.

Por exemplo, ao transmitir via HLS, o URL de streaming seria como `https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl).m3u8` . Para MPEG-DASH, `https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=mpd-time-cmaf).mpd` pareceria.

Isto devolve um ficheiro manifesto que, entre outras coisas, descreve a duração global do fluxo que está a ser entregue, e se representa conteúdo pré-gravado, ou o feed 'live' em curso.

### <a name="live-vs-vod"></a>Ao vivo vs. VoD  

Protocolos de streaming como HLS e MPEG-DASH foram autores para lidar com cenários como o streaming de vídeos ao vivo, bem como o streaming de conteúdos on-demand/pré-gravados, como séries de TV e filmes. Para vídeos ao vivo, os clientes HLS e MPEG-DASH são projetados para começar a jogar a partir do tempo mais recente. Com os filmes, no entanto, os espectadores esperam ser capazes de começar do início e saltar ao redor se quiserem. Os manifestos HLS e MPEG-DASH têm bandeiras que indicam aos clientes se o vídeo representa um live stream ou se é conteúdo pré-gravado.
Este conceito também se aplica aos streams HLS e MPEG-DASH de Ativos que contêm vídeo gravado usando Live Video Analytics em IoT Edge.

## <a name="browsing-and-selective-playback-of-recordings"></a>Navegação e reprodução seletiva de gravações  

Considere o cenário em que utilizou o Live Video Analytics no IoT Edge para gravar o vídeo apenas das 8h às 10h nos dias em que uma escola estava aberta, durante todo o ano letivo. Ou talvez esteja a gravar vídeos apenas das 7h às 19h nos feriados nacionais. Em qualquer um destes dois cenários, ao tentar navegar e ver a sua gravação de vídeo, você precisaria de:

* Uma forma de determinar que datas/horas de vídeo tem numa gravação.
* Uma forma de selecionar uma porção (por exemplo, das 9h às 11h no Dia de Ano Novo) dessa gravação para reproduzir.

Os Serviços de Media fornecem-lhe uma consulta API (availableMedia) para resolver a primeira emissão, e filtros de intervalo de tempo (startTime, endTime) para abordar o segundo.

## <a name="query-api"></a>API de Consulta 

Ao utilizar o CVR, os dispositivos de reprodução (clientes) não podem solicitar uma reprodução manifesta de cobertura de toda a gravação.  Uma gravação pluásia produziria um ficheiro manifesto demasiado grande para a reprodução e seria inflexível analisar porções utilizáveis do lado do cliente.  O cliente precisa de saber que intervalos de data têm dados na gravação para que possa fazer pedidos válidos sem muito trabalho de adivinhação. É aqui que entra a nova API de Consulta – os clientes podem agora utilizar esta API do lado do servidor para descobrir conteúdo.

Onde o valor de precisão pode ser de: ano, mês, dia ou cheio (como mostrado abaixo). 

|Precisão|ano|month|day|cheio|
|---|---|---|---|---|
|Consulta|`/availableMedia?precision=year&startTime=2018&endTime=2019`|`/availableMedia?precision=month& startTime=2018-01& endTime=2019-02`|`/availableMedia?precision=day& startTime=2018-01-15& endTime=2019-02-02`|`/availableMedia?precision=full& startTime=2018-01-15T10:08:11.123& endTime=2019-01-015T12:00:01.123`|
|Resposta|`{  "timeRanges":[{ "start":"2018", "end":"2019" }]}`|`{  "timeRanges":[{ "start":"2018-03", "end":"2019-01" }]}`|`{  "timeRanges":[    { "start":"2018-03-01", "end":"2018-03-07" },    { "start":"2018-03-09", "end":"2018-03-31" }  ]}`|Resposta de fidelidade completa. Se não houvesse lacunas, o início seria startTime, e o fim seria fim de tempo.|
|Constrangimentos|&#x2022;startTime <= endTime<br/>&#x2022;Ambos devem estar no formato YYYY, caso contrário, devolva o erro de devolução.<br/>&#x2022;Valores podem ter vários anos de diferença.<br/>&#x2022;Valores são inclusivos.|&#x2022;startTime <= endTime<br/>&#x2022;Ambos devem estar no formato YYYY-MM, caso contrário, erro de devolução.<br/>&#x2022;Valores podem ter no máximo 12 meses de intervalo.<br/>&#x2022;Valores são inclusivos.|&#x2022;startTime <= endTime<br/>&#x2022;Ambos devem estar no formato YYYY-MM-DD, caso contrário, erro de devolução.<br/>&#x2022;Valores podem ter no máximo 31 dias de intervalo.<br/>Os valores são inclusivos.|&#x2022;startTime < endTime<br/>&#x2022;Valores podem estar no máximo com 25 horas de intervalo.<br/>&#x2022;Valores são inclusivos.|

#### <a name="additional-request-format-considerations"></a>Considerações adicionais do formato de pedido

* Todos os tempos estão na UTC
* É necessário o parâmetro de cadeia de consulta de precisão.  
* Os parâmetros de cadeia de consulta startTime e endTime são necessários para o mês, dia e valores de precisão completos.  
* Os parâmetros de cadeia de consulta startTime e endTime são opcionais para o valor de precisão do ano (nenhum, ou ambos são suportados).  

    * Se o startTime for omitido, presume-se que seja o primeiro ano da gravação.
    * Se o fim doTime for omitido, presume-se que seja o último ano na gravação.
    * Por exemplo, se a sua gravação começou em 2011, e continuou até 2020, então:

        * /availableMedia?precision=year is the same as /availableMedia?precision=year&startTime=2011&endTime=2020
        * /availableMedia?precision=ano&startTime=2015 é o mesmo que /availableMedia?precision=ano&startTime=2015&endTime=2020
        * /availableMedia?precision=ano&endTime=2018 é o mesmo que /availableMedia?precision=ano&startTime=2011&endTime=2018

Se não houver dados de mídia disponíveis para os intervalos de tempo, então uma lista vazia é devolvida.

Se o ativo não contiver uma gravação a partir de um gráfico de mídia, então uma resposta HTTP 400 será devolvida com uma mensagem de erro explicando que esta funcionalidade só está disponível para conteúdos gravados através de um gráfico de mídia.

Se a duração do tempo especificado pelos parâmetros for maior do que o permitido pelo intervalo de tempo máximo para um determinado tipo de consulta, então um HTTP 400 será devolvido com uma mensagem de erro explicando o intervalo de tempo máximo permitido para o tipo de consulta solicitado.

Assumindo que o intervalo de tempo é válido para os parâmetros indicados, não serão devolvidos erros para consultas que estejam fora do período de tempo dos dados na gravação.  Ou seja, se a gravação começou há 7 horas, mas o cliente pede meios disponíveis de {UtcNow – 24 horas} para o UtcNow, então apenas devolvemos as horas de dados {UtcNow – 7} .

### <a name="response-format"></a>Formato de resposta  

A chamada AvailableMedia devolve um conjunto de valores de tempo.

A resposta será um corpo JSON, com os valores timeRanges sendo um conjunto de datas ISO 8601 UTC para o ano (em formato YYYY), mês (em formato YYYY-MM) ou dia (YYYY-MM-DD) dependendo da precisão solicitada.  O tempo integralRanges conterá um valor inicial e final formatado como uma data DE SO 8601 UTC (em formato YYYYY-MM-DDThh:mm:ss.sssZ).

Para a consulta de Media disponível, a API irá sair da linha temporal de vídeo. Quaisquer descontinuidades na linha do tempo aparecerão como lacunas na resposta.

#### <a name="response-example-for-availablemedia"></a>Exemplo de resposta para Os Media disponíveis

##### <a name="example-1-live-recording-with-no-gaps"></a>Exemplo 1: Gravação ao vivo sem lacunas

Eis uma resposta que mostra todos os meios de comunicação disponíveis para 21 de dezembro de 2019, onde a gravação foi 100% completa. A resposta tem apenas um par de partida/extremidade.

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=full&startTime=2019-12-21T00:00:00Z&endTime=2019-12-22T00:00:00Z
{
   "timeRanges":[
   {
         "start":"2019-12-21T00:00:00.000Z", 
         "end":"2019-12-22T00:00:00.000Z"
    }
   ]
}
```

##### <a name="example-2-live-recording-with-a-2-second-gap"></a>Exemplo 2: Gravação ao vivo com uma lacuna de 2 segundos

Suponha que, por alguma razão, a câmara não enviou um vídeo válido para um intervalo de 2 segundos num dia. Aqui está uma resposta mostrando todos os meios de comunicação disponíveis para 21 de dezembro de 2019:

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=full&startTime=2019-12-21T00:00:00Z&endTime=2019-12-22T00:00:00Z
{
   "timeRanges":[
    {
         "start":"2019-12-21T00:00:00Z", 
         "end":"2019-12-21T04:01:08Z"
    },
    {
         "start":"2019-12-21T04:01:10Z", 
         "end":"2019-12-22T00:00:00Z"
    }
   ]
}
```

##### <a name="example-3-live-recording-with-an-8-hour-gap"></a>Exemplo 3: Gravação ao vivo com uma diferença de 8 horas

Suponha que a câmara e/ou instalação no local perdeu energia durante um período de 8 horas durante o dia, das 4:00 utc ao meio-dia UTC, e não havia fonte de energia de reserva. Aqui está uma resposta mostrando todos os meios de comunicação disponíveis para tal dia

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=full&startTime=2019-12-21T00:00:00Z&endTime=2019-12-22T00:00:00Z
{
   "timeRanges":[
    {
         "start":"2019-12-21T00:00:00Z", 
         "end":"2019-12-21T04:00:00Z"
    },
    {
         "start":"2019-12-21T12:00:00Z", 
         "end":"2019-12-22T00:00:00Z"
    }
   ]
}
```

#### <a name="example-4-live-recording-where-no-video-is-recorded-over-summer-holidays"></a>Exemplo 4: Gravação ao vivo onde nenhum vídeo é gravado durante as férias de verão

Suponha que só gravou o vídeo quando a escola estava em sessão, e a gravação foi interrompida de 17 de junho a 6 de setembro. Uma consulta para os meses disponíveis seria como:

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=month&startTime=2019-01&endTime=2019-12
{
   "timeRanges":[
    {
         "start":"2019-01", 
         "end":"2019-06"
    },
    {
         "start":"2019-09", 
         "end":"2019-12"
    }
   ]
}
```

Se então pedisse os dias disponíveis em junho, veria:

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=day&startTime=2019-06-01&endTime=2019-06-30
{
   "timeRanges":[
    {
         "start":"2019-06-01", 
         "end":"2019-06-17"
    }
   ]
}
```

##### <a name="example-5-live-recording-where-no-video-is-recorded-over-weekends-or-holidays"></a>Exemplo 5: Gravação ao vivo onde nenhum vídeo é gravado nos fins de semana ou feriados

Suponha que tenha gravado o vídeo apenas durante o horário comercial. Uma consulta para os dias disponíveis seria como

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=day&startTime=2020-02-01&endTime=2020-02-29
{
   "timeRanges":[
    {
         "start":"2020-02-03", 
         "end":"2020-02-07"
    },
    {
         "start":"2020-02-10", 
         "end":"2020-02-14"
    },
    {
         "start":"2020-02-18", // Monday Feb 17th was a holiday
         "end":"2020-02-21"
    },
    {
         "start":"2020-02-24", 
         "end":"2020-02-28"
    }
   ]
}
```

## <a name="time-range-filters"></a>Filtros de intervalo de tempo

Como mencionado acima, estes filtros ajudam-no a selecionar partes da sua gravação (por exemplo, das 9h às 11h no Dia de Ano Novo) para reprodução. Ao transmitir via HLS, o URL de streaming seria como `https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl).m3u8` . Para selecionar uma parte da sua gravação, adicionaria um parâmetro startTime e um fimtime, tais como: `https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2019-12-21T08:00:00Z,endTime=2019-12-21T10:00:00Z).m3u8` . Assim, os filtros de intervalo de tempo são modificadores DE URL utilizados para descrever a parte da linha temporal da gravação que está incluída no manifesto de streaming:

* `starttime` é um carimbo ISO 8601 DateTime que descreve a hora de início desejada da linha do tempo de vídeo no manifesto devolvido.
* `endtime` é um carimbo ISO 8601 DateTime que descreve o tempo de fim desejado da linha de tempo de vídeo devolvido no manifesto.

O comprimento máximo (a tempo) de tal manifesto não pode exceder 24 horas.

Aqui estão os constrangimentos nos filtros.

|startTime| endTime |Resultado|
|---|---|---|
|Ausente |Ausente |Devolve a parte mais recente do vídeo no Ativo, até um comprimento máximo de 4 horas.<br/><br/>Se o Ativo não tiver sido escrito (nenhum novo dado de vídeo a chegar) recentemente, um manifesto on-demand (VoD) é devolvido. Caso contrário, um manifesto vivo é devolvido.|
|Presente|Ausente|    Devolva um manifesto com qualquer vídeo disponível que seja mais recente do que o startTime, se tal manifesto for inferior a 24 horas.<br/>Se o comprimento do manifesto exceder 24 horas, então devolva um erro.<br/>Se o Ativo não tiver sido escrito (nenhum novo dado de vídeo a chegar) recentemente, um manifesto on-demand (VoD) é devolvido. Caso contrário, um manifesto vivo é devolvido.
|Ausente|Presente |Erro – se o startTime estiver presente, então o fimtime é obrigatório.|
|Presente|Presente|Devolva um manifesto VoD com qualquer vídeo disponível entre startTime e endTime.<br/>O intervalo (startTime, endTime) não pode exceder 24 horas.|

### <a name="response-examples"></a>Exemplos de resposta  

#### <a name="example-1-live-recording-with-no-gaps"></a>Exemplo 1: Gravação ao vivo sem lacunas

Eis uma resposta que mostra todos os meios de comunicação disponíveis para 21 de dezembro de 2019, onde a gravação foi 100% completa. A resposta tem apenas um par de partida/extremidade.

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=full&startTime=2019-12-21T00:00:00Z&endTime=2019-12-22T00:00:00Z
{
   "timeRanges":[
   {
         "start":"2019-12-21T00:00:00Z", 
         "end":"2019-12-22T00:00:00Z"
    }
   ]
}
```

Quando a gravação for contínua, pode solicitar manifestos HLS ou DASH por qualquer parte do tempo dentro do par Start/End – desde que o intervalo seja de 24 horas ou menos. Um exemplo para um pedido manifesto hls de 4 horas para o acima referido seria:

`GET https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2019-12-21T14:00:00.000Z,endTime=2019-12-21T18:00:00.000Z).m3u8`

#### <a name="example-2-live-recording-with-a-2-second-gap"></a>Exemplo 2: Gravação ao vivo com uma lacuna de 2 segundos

Suponha que, por alguma razão, a câmara não enviou um vídeo válido para um intervalo de 2 segundos num dia. Aqui está uma resposta mostrando os meios de comunicação disponíveis para 21 de dezembro de 2019:

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=full&startTime=2019-12-21T00:00:00Z&endTime=2019-12-22T00:00:00Z
{
   "timeRanges":[
    {
         "start":"2019-12-21T00:00:00Z", 
         "end":"2019-12-21T04:01:08Z"
    },
    {
         "start":"2019-12-21T04:01:10Z", 
         "end":"2019-12-22T00:00:00Z"
    }
   ]
}
```

Com uma gravação como a acima, mais uma vez, você poderia solicitar manifestos HLS e DASH com quaisquer pares startTime/endTime, desde que o intervalo fosse inferior a 24 horas. Se estes valores se desconsessem na diferença às 04:01:08AM UTC, então o manifesto devolvido sinalizaria a descontinuidade na linha temporal dos meios de comunicação, de acordo com as especificações relevantes para esses protocolos.

#### <a name="example-3-live-recording-with-an-8-hour-gap"></a>Exemplo 3: Gravação ao vivo com uma lacuna de 8 horas

Suponha que a câmara e/ou instalação no local perdeu energia durante um período de 8 horas durante o dia, das 4:00 utc ao meio-dia UTC, e não havia fonte de energia de reserva. Aqui está uma resposta mostrando todos os meios de comunicação disponíveis para tal dia.

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=full&startTime=2019-12-21T00:00:00Z&endTime=2019-12-22T00:00:00Z
{
   "timeRanges":[
    {
         "start":"2019-12-21T00:00:00Z", 
         "end":"2019-12-21T04:00:00Z"
    },
    {
         "start":"2019-12-21T12:00:00Z", 
         "end":"2019-12-22T00:00:00Z"
    }
   ]
}
```

Com tal gravação:

* Se solicitar um manifesto onde ambos os filtros de gama startTime/endTime estavam dentro das partes 'disponíveis' da linha do tempo, nomeadamente da meia-noite às 4h, ou do meio-dia à meia-noite, então o serviço devolveria um manifesto que cobre o tempo do início do Tempo até ao fim do Tempo, tais como:

    `GET https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2019-12-21T14:01:00.000Z,endTime=2019-12-21T03:00:00.000Z).m3u8`
* Se solicitar um manifesto onde o startTime e o endTime estavam dentro do 'buraco' no meio – digamos das 8h às 10h UTC, então o serviço comportar-se-ia da mesma forma que se um Filtro de Ativos resultasse num resultado vazio.

    [Este é um pedido que recebe uma resposta vazia] `GET https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2019-12-21T08:00:00.000Z,endTime=2019-12-21T10:00:00.000Z).m3u8`
* Se solicitar um manifesto onde apenas um dos tempos de arranque ou fim do Tempo esteja dentro do 'buraco', então o manifesto devolvido apenas incluirá uma parte desse período de tempo. Encaixaria o valor startTime ou endTime para o limite válido mais próximo. Por exemplo, se pedisse um fluxo de 3 horas das 10:00 às 13:00, a resposta conteria 1 hora de meios de comunicação entre as 12:00 e as 13:00.

    `GET https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2019-12-21T10:00:00.000Z,endTime=2019-12-21T13:00:00.000Z).m3u8`
    
    O manifesto devolvido começaria em 2019-12-21T12:00:00.000Z, apesar de o pedido ter pedido o início das 10h. Ao construir um sistema de gestão de vídeo com um plugin do jogador, deve-se ter cuidado para sinalizar isto ao espectador. Um espectador inconsciente ficaria confuso sobre o porquê da linha de tempo de reprodução começar ao meio-dia, e não 10am como solicitado

## <a name="recording-and-playback-latencies"></a>Latências de gravação e reprodução

Ao utilizar o Live Video Analytics no IoT Edge para gravar um Ativo, irá especificar uma propriedade segmentLength que diz ao módulo para agregar uma duração mínima de vídeo (em segundos) antes de ser gravado na nuvem. Por exemplo, se o comprimento de segmentoLength estiver definido para 300, então o módulo acumulará 5 minutos de vídeo antes de carregar um "pedaço" de 5 minutos, em seguida, entrar em modo de acumulação durante os próximos 5 minutos, e carregar novamente. Aumentar o segmentoLength tem o benefício de baixar os custos de transação de Armazenamento Azure, uma vez que o número de leituras e escritos não será mais frequente do que uma vez por segmentoSLength segundos.

Consequentemente, o streaming do vídeo dos Media Services será adiado pelo menos por muito tempo. 

Outro fator que determina a latência da reprodução (o atraso entre o momento em que um evento ocorre em frente à câmara, até ao momento em que pode ser visto num dispositivo de reprodução) é a duração [do GOP](https://en.wikipedia.org/wiki/Group_of_pictures) em grupo de imagens. Como [reduzir o atraso dos fluxos ao vivo usando 3 técnicas simples](https://medium.com/vrt-digital-studio/reducing-the-delay-of-live-streams-by-using-3-simple-techniques-e8e028b0a641) explica, mais longa duração do GOP, mais longa a latência. É comum ter câmaras IP usadas em cenários de vigilância e segurança configurados para usar GOPs por mais de 30 segundos. Isto tem um grande impacto na latência global.

## <a name="next-steps"></a>Passos seguintes

[Tutorial de gravação contínua de vídeo](continuous-video-recording-tutorial.md)
