---
title: Cenários de preços para chamada (Voz/Vídeo) e Chat
titleSuffix: An Azure Communication Services concept document
description: Conheça o Modelo de Preços dos Serviços de Comunicação.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: ad88a7a6c91128bb863eeb51cc7f26c8d71b9eed
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2021
ms.locfileid: "105107912"
---
# <a name="pricing-scenarios"></a>Cenários de preços

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]


Os preços dos Serviços de Comunicação Azure baseiam-se geralmente num modelo de pagamento. Os preços dos seguintes exemplos são para fins ilustrativos e podem não refletir os preços mais recentes do Azure.

## <a name="voicevideo-calling-and-screen-sharing"></a>Chamada de voz/vídeo e partilha de ecrã

Os Serviços de Comunicação Azure permitem adicionar chamadas de voz/vídeo e partilha de ecrãs às suas aplicações. Pode incorporar a experiência nas suas aplicações utilizando JavaScript, Objective-C (Apple), Java (Android) ou .NET SDKs. Consulte a nossa [lista completa de SDKs disponíveis.](./sdk-options.md)

### <a name="pricing"></a>Preços

Os serviços de chamada e partilha de ecrãs são cobrados por minuto por minuto por participante a $0,004 por participante por minuto para chamadas de grupo. Para compreender os vários fluxos de chamadas que são possíveis, consulte [esta página](./call-flows.md).

Cada participante da chamada contará na faturação por cada minuto que estiver ligado à chamada. Isto é verdade independentemente de o utilizador estar a chamar por vídeo, a chamar voz ou a partilhar ecrãs.

### <a name="pricing-example-group-audiovideo-call-using-js-and-ios-sdks"></a>Exemplo de preços: Chamada de áudio/vídeo em grupo utilizando SDKs JS e iOS

A Alice fez uma chamada em grupo com os colegas, Bob e Charlie. Alice e Bob usaram os JS SDKs, Charlie iOS SDKs.

- A chamada dura um total de 60 minutos.
- Alice e Bob participaram durante toda a ligação. Alice ligou o vídeo durante cinco minutos e partilhou o ecrã durante 23 minutos. Bob tinha o vídeo ligado durante toda a chamada (60 minutos) e partilhou o ecrã durante 12 minutos.
- Charlie deixou a ligação após 43 minutos. Charlie usou áudio e vídeo durante o tempo em que participou (43 minutos).

**Cálculos de custos**

- 2 participantes x 60 minutos x $0,004 por participante por minuto = $0,48 [tanto o vídeo como o áudio são cobrados à mesma taxa]
- 1 participante x 43 minutos x $0,004 por participante por minuto = $0,172 [tanto o vídeo como o áudio são cobrados à mesma taxa]

**Custo total da chamada do grupo**: $0,48 + $0,172 = $0,652

### <a name="pricing-example-a-user-of-the-communication-services-javascript-sdk-joins-a-scheduled-microsoft-teams-meeting"></a>Exemplo de preços: Um utilizador dos Serviços de Comunicação JavaScript SDK junta-se a uma reunião agendada da Microsoft Teams

A Alice está a encontrar-se com o paciente, Bob. Alice juntar-se-á à visita da aplicação Teams Desktop. Bob receberá um link para se juntar ao site do prestador de cuidados de saúde, que se conecta à reunião usando os Serviços de Comunicação JavaScript SDK. Bob usará o seu telemóvel para entrar na reunião usando um navegador web (iPhone com Safari). O chat estará disponível durante a visita virtual.

- A chamada dura um total de 30 minutos.
- Alice e Bob participam de toda a ligação. Alice liga o vídeo cinco minutos depois da chamada começar e partilha o ecrã durante 13 minutos. O Bob tem o vídeo dele ligado para toda a chamada.
- Alice envia cinco mensagens, Bob responde com três mensagens.


**Cálculos de custos**

- 1 participante (Bob) x 30 minutos x $0,004 por participante por minuto = $0,12 [tanto o vídeo como o áudio são cobrados à mesma taxa]
- 1 participante (Alice) x 30 minutos x $0,000 por participante por minuto = $0,0*.
- 1 participante (Bob) x 3 mensagens de chat x $0,0008 = $0,0024.
- 1 participante (Alice) x 5 mensagens de chat x $0,000 = $0,0*.

*A participação da Alice está coberta pela licença das equipas. A sua fatura Azure mostrará as minutos e mensagens de chat que os utilizadores das Equipas tinham com os Utilizadores dos Serviços de Comunicação para sua conveniência, mas esses minutos e mensagens originárias do cliente das Equipas não custarão.

**Custo total da visita:**
- Unição do utilizador através dos Serviços de Comunicação JavaScript SDK: $0.12 + $0.0024 = $0.1224
- Adesão do utilizador à aplicação de ambiente de trabalho teams: $0 (coberto pela licença das equipas)


## <a name="chat"></a>Chat

Com os Serviços de Comunicação pode melhorar a sua aplicação com a capacidade de enviar e receber mensagens de chat entre dois ou mais utilizadores. Os Chat SDKs estão disponíveis para JavaScript, .NET, Python e Java. Consulte [esta página para saber sobre SDKs](./sdk-options.md)

### <a name="price"></a>Preço

Cobram-lhe $0.0008 por cada mensagem de chat enviada.

### <a name="pricing-example-chat-between-two-users"></a>Exemplo de preços: Conversa entre dois utilizadores

Geeta inicia um chat thread com Emily para partilhar uma atualização e envia 5 mensagens. A conversa dura 10 minutos. Geeta e Emily enviam mais 15 mensagens cada.

**Cálculos de custos**
- Número de mensagens enviadas (5 + 15 + 15) x $0,0008 = $0,028

### <a name="pricing-example-group-chat-with-multiple-users"></a>Exemplo de preços: Chat de grupo com vários utilizadores

Charlie começa uma conversa com os amigos Casey & Jasmine para planear umas férias. Conversam durante algum tempo onde Charlie, Casey & Jasmine enviam 20, 30 e 18 mensagens, respectivamente. Eles percebem que a sua amiga Rose também pode estar interessada em juntar-se à viagem, por isso acrescentam-na ao chat thread e partilham toda a história da mensagem com ela.

Rose vê as mensagens e começa a conversar. Entretanto, o Casey recebe uma chamada e decide pôr a conversa em dia mais tarde. Charlie, Jasmine & Rose decidem as datas de viagem e enviam mais 30, 25, 35 mensagens, respectivamente.

**Cálculos de custos**

- Número de mensagens enviadas (20 + 30 + 18 + 30 + 25 + 35) x $0,0008 = $0,1264


## <a name="telephony-and-sms"></a>Telefonia e SMS

## <a name="price"></a>Preço

Os serviços de telefonia têm preços por minuto, enquanto os SMS têm preços por mensagem. O preço é determinado pelo tipo e localização do número que está a utilizar, bem como pelo destino das suas chamadas e mensagens SMS.

### <a name="telephone-number-leasing"></a>Locação de números de telefone

As taxas de locação de números de telefone são cobradas antecipadamente e, em seguida, repetem-se mensalmente:

|Tipo de número   |Taxa mensal   |
|--------------|-----------|
|Local (Estados Unidos)     |$1/mo        |
|Portagem gratuita (Estados Unidos) |$2/mo |


### <a name="telephone-calling"></a>Chamada telefónica

As chamadas telefónicas tradicionais (chamadas que ocorrem através da rede telefónica comusada pública) estão disponíveis com preços de pagamento para números de telefone baseados nos Estados Unidos. O preço é uma taxa por minuto com base no tipo de número utilizado e no destino da chamada. Os detalhes dos preços dos destinos de chamada mais populares estão incluídos na tabela abaixo. Consulte a [lista de preços detalhadas](https://github.com/Azure/Communication/blob/master/pricing/communication-services-pstn-rates.csv) para obter uma lista completa de destinos.


#### <a name="united-states-calling-prices"></a>Estados Unidos chamam os preços

Os seguintes preços incluem taxas e taxas de comunicação obrigatórias até 30 de junho de 2021:

|Tipo de número   |Para fazer chamadas   |Para receber chamadas|
|--------------|-----------|------------|
|Local     |A partir de $0.013/min       |$0.0085/min        |
|Sem portagens |$0.013/min   |$0.0220/min |

#### <a name="other-calling-destinations"></a>Outros destinos de chamada

Os seguintes preços incluem taxas e taxas de comunicação obrigatórias até 30 de junho de 2021:

|Fazer chamadas para   |Preço por minuto|
|-----------|------------|
|Canadá     |A partir de $0.013/min   |
|Reino Unido     |A partir de $0.015/min   |
|Alemanha     |A partir de $0.015/min   |
|França     |A partir de $0.016/min   |


### <a name="sms"></a>SMS

A SMS oferece preços de pagamento à medida. O preço é uma taxa por mensagem baseada no destino da mensagem. As mensagens podem ser enviadas por números de telefone gratuitos para números de telefone localizados nos Estados Unidos. Note que os números de telefone locais (geográficos) não podem ser usados para enviar mensagens SMS.

Os seguintes preços incluem taxas e taxas de comunicação obrigatórias até 30 de junho de 2021:

|País   |Enviar mensagens|Receber mensagens|
|-----------|------------|------------|
|EUA (Portagem gratuita)    |$0.0075/msg   | $0.0075/msg |
