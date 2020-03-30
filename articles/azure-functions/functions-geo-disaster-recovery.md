---
title: Azure Functions recuperação geo-desastre e elevada disponibilidade
description: Como utilizar as regiões geográficas para o despedimento e para falhar nas Funções Azure.
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.topic: conceptual
ms.date: 08/29/2019
ms.openlocfilehash: 481a716bd6ced5c304da41c70fdcfc687b76661d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79080224"
---
# <a name="azure-functions-geo-disaster-recovery"></a>Recuperação geo-desastre das Funções Azure

Quando regiões ou datacenters inteiros do Azure experimentam tempo de inatividade, é fundamental que a computação continue a processar numa região diferente.  Este artigo irá explicar algumas das estratégias que pode usar para implementar funções para permitir a recuperação de desastres.

## <a name="basic-concepts"></a>Conceitos básicos

As Funções Azure funcionam numa região específica.  Para obter uma maior disponibilidade, pode implementar as mesmas funções em várias regiões.  Quando em várias regiões pode ter as suas funções em execução no padrão *ativo/ativo* ou no padrão *ativo/passivo.*  

* Modo ativo/ativo. Ambas as regiões estão ativas e recebem eventos (duplicados ou rotativamente). Ativa/ativa é recomendada para funções HTTPS em combinação com a Porta Frontal Azure.
* Ativo/passivo. Uma região está ativa e recebe eventos, enquanto uma secundária está inativa.  Quando é necessária uma falha, a região secundária é ativada e assume o processamento.  Isto é recomendado para funções não HTTP como Service Bus e Event Hubs.

Leia como [executar aplicações em várias regiões](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region) para obter mais informações sobre implementações multi-regiões.

## <a name="activeactive-for-https-functions"></a>Ativo/ativo para funções HTTPS

Para conseguir implantações ativas/ativas de funções, requer algum componente que possa coordenar os eventos entre ambas as regiões.  Para funções HTTPS, esta coordenação é realizada utilizando a [Porta frontal Azure](../frontdoor/front-door-overview.md).  A Porta Frontal Azure pode encaminhar e fazer pedidos de round-robin HTTPS entre várias funções regionais.  Também verifica periodicamente a saúde de cada ponto final.  Se uma função regional deixar de responder aos controlos de saúde, a Porta frontal azure irá retirá-la da rotação e apenas encaminhar o tráfego para funções saudáveis.  

![Arquitetura para porta e função azure](media/functions-geo-dr/front-door.png)  

## <a name="activeactive-for-non-https-functions"></a>Ativo/ativo para funções não HTTPS

Ainda pode obter implementações ativas/ativas para funções não HTTPS.  No entanto, é necessário considerar como as duas regiões irão interagir ou coordenar-se entre si.  Se implementasse a mesma aplicação de funções em duas regiões, cada uma desencadeando na mesma fila de Autocarros de Serviço, eles agiriam como consumidores concorrentes na desfilação dessa fila.  Embora isto signifique que cada mensagem está a ser processada apenas por uma das instâncias, também significa que ainda há um único ponto de falha no autocarro de serviço único.  Se implementar duas filas de ônibus de serviço (uma numa região primária, uma numa região secundária), e as duas aplicações de funções apontadas para a fila da região, o desafio agora vem na forma como as mensagens de fila são distribuídas entre as duas regiões.  Muitas vezes isto significa que cada editor tenta publicar uma mensagem para *ambas as* regiões, e cada mensagem é processada por ambas as aplicações de função ativa.  Embora isto crie um padrão ativo/ativo, cria outros desafios em torno da duplicação de cálculo e quando ou como os dados são consolidados.  Por estas razões, recomenda-se que os gatilhos não HTTPS utilizem o padrão ativo/passivo.

## <a name="activepassive-for-non-https-functions"></a>Ativa/passiva para funções não HTTPS

Ativo/passivo fornece uma forma de apenas uma única função processar cada mensagem, mas fornece um mecanismo para falhar numa região secundária em caso de catástrofe.  A Azure Functions trabalha ao lado da [geo-recuperação](../service-bus-messaging/service-bus-geo-dr.md) do Azure Service Bus e da [geo-recuperação do Azure Event Hubs.](../event-hubs/event-hubs-geo-dr.md)

Utilizando os hubs de eventos Azure como exemplo, o padrão ativo/passivo envolveria o seguinte:

* O Azure Event Hub deslocou-se para uma região primária e secundária.
* Geo-desastre permitiu emparelhar o Centro de Eventos Primário e Secundário.  Isto também cria um "pseudónimo" que pode usar para ligar aos centros de eventos e mudar de primário para secundário sem alterar a informação de ligação.
* Aplicações de funções implementadas para uma região primária e secundária.
* As aplicações de função estão a ativar a cadeia de ligação *direta* (não-alias) para o seu respetivo centro de eventos. 
* As editoras do centro de eventos devem publicar na cadeia de ligação do pseudónimo. 

![Arquitetura de exemplo passivo ativo](media/functions-geo-dr/active-passive.png)

Antes do fracasso, os editores que enviam para o pseudónimo partilhado irão encaminhar-se para o centro de eventos primários.  A aplicação de função primária está a ouvir exclusivamente o centro do evento primário.  A aplicação de função secundária será passiva e inativa.  Assim que a falha for iniciada, as editoras que enviam para o pseudónimo partilhado irão agora encaminhar-se para o centro de eventos secundários.  A aplicação de função secundária irá agora tornar-se ativa e começar a disparar automaticamente.  A falha efetiva numa região secundária pode ser totalmente impulsionada a partir do centro de eventos, com as funções a tornarem-se ativas apenas quando o respetivo centro de eventos estiver ativo.

Leia mais sobre informações e considerações para falha com [service bus](../service-bus-messaging/service-bus-geo-dr.md) e centros [de eventos](../event-hubs/event-hubs-geo-dr.md).

## <a name="next-steps"></a>Passos seguintes

* [Criar porta da frente azure](../frontdoor/quickstart-create-front-door.md)
* [Evento Hubs falha considerações](../event-hubs/event-hubs-geo-dr.md#considerations)
