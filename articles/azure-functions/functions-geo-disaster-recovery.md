---
title: Azure Functions recuperação de desastres geograficamente e alta disponibilidade | Microsoft Docs
description: Como usar regiões geográficas para redundância e para fazer failover em Azure Functions.
services: functions
documentationcenter: na
author: wesmc7777
manager: jeconnoc
keywords: Azure functions, padrões, práticas recomendadas, funções
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.service: azure-functions
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: jehollan
ms.openlocfilehash: 1d75d58a6df622ffb1b277f75ceedc2c2a66369d
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73576266"
---
# <a name="azure-functions-geo-disaster-recovery"></a>Azure Functions recuperação de desastres geograficamente

Quando todas as regiões ou datacenters do Azure têm experiência com tempo de inatividade, é fundamental para a computação continuar o processamento em uma região diferente.  Este artigo explicará algumas das estratégias que você pode usar para implantar funções para permitir a recuperação de desastres.

## <a name="basic-concepts"></a>Conceitos básicos

Azure Functions executado em uma região específica.  Para obter maior disponibilidade, você pode implantar as mesmas funções em várias regiões.  Quando em várias regiões, você pode ter suas funções em execução no padrão *ativo/ativo* ou no padrão *ativo/passivo* .  

* Modo ativo/ativo. Ambas as regiões estão ativas e recebendo eventos (duplicados ou rotacionalmente). Ativo/ativo é recomendado para funções HTTPS em combinação com a porta frontal do Azure.
* Ativo/passivo. Uma região está ativa e recebendo eventos, enquanto um secundário está ocioso.  Quando o failover é necessário, a região secundária é ativada e assume o processamento.  Isso é recomendado para funções não HTTP, como barramento de serviço e hubs de eventos.

Leia como [executar aplicativos em várias regiões](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region) para obter mais informações sobre implantações de várias regiões.

## <a name="activeactive-for-https-functions"></a>Ativo/ativo para funções HTTPS

Para obter implantações ativas/ativas de funções, é necessário um componente que possa coordenar os eventos entre ambas as regiões.  Para funções HTTPS, essa coordenação é realizada usando a [porta frontal do Azure](../frontdoor/front-door-overview.md).  A porta frontal do Azure pode rotear e redirecionar solicitações HTTPS entre várias funções regionais.  Ele também verifica periodicamente a integridade de cada ponto de extremidade.  Se uma função regional parar de responder às verificações de integridade, a porta frontal do Azure a retirará da rotação e apenas encaminhará o tráfego para as funções íntegras.  

![Arquitetura para a porta e a função front do Azure](media/functions-geo-dr/front-door.png)  

## <a name="activeactive-for-non-https-functions"></a>Ativo/ativo para funções não-HTTPS

Você ainda pode obter implantações ativas/ativas para funções não-HTTPS.  No entanto, você precisa considerar como as duas regiões interagirão ou serão coordenadas umas com as outras.  Se você implantou o mesmo aplicativo de funções em duas regiões, cada um disparando na mesma fila do barramento de serviço, ele atuaria como consumidores concorrentes na remoção da fila dessa fila.  Embora isso signifique que cada mensagem está sendo processada apenas por uma das instâncias, isso também significa que ainda há um único ponto de falha no barramento de serviço único.  Se você implantar duas filas do barramento de serviço (uma em uma região primária, uma em uma região secundária) e os dois aplicativos de função apontados para sua fila de região, o desafio agora é o modo como as mensagens de fila são distribuídas entre as duas regiões.  Geralmente, isso significa que cada publicador tenta publicar uma mensagem em *ambas as* regiões e cada mensagem é processada por ambos os aplicativos de funções ativas.  Embora isso crie um padrão ativo/ativo, ele cria outros desafios em relação à duplicação de computação e quando ou como os dados são consolidados.  Por esses motivos, é recomendável que gatilhos não-HTTPS usem o padrão ativo/passivo.

## <a name="activepassive-for-non-https-functions"></a>Ativo/passivo para funções não HTTPS

Ativo/passivo fornece uma maneira para apenas uma única função processar cada mensagem, mas fornece um mecanismo para fazer failover para uma região secundária em caso de desastre.  Azure Functions funciona junto [com a recuperação geográfica do barramento de serviço do](../service-bus-messaging/service-bus-geo-dr.md) Azure e [com a recuperação geográfica dos hubs de eventos do Azure](../event-hubs/event-hubs-geo-dr.md).

Usando gatilhos de hubs de eventos do Azure como exemplo, o padrão ativo/passivo envolveria o seguinte:

* Hub de eventos do Azure implantado em uma região primária e secundária.
* Desastre geográfico habilitado para emparelhar o Hub de eventos primário e secundário.  Isso também cria um "alias" que você pode usar para se conectar aos hubs de eventos e alternar de primário para secundário sem alterar as informações de conexão.
* Aplicativos de funções implantados em uma região primária e secundária.
* Os aplicativos de funções estão sendo disparados na cadeia de conexão *direta* (sem alias) para seu respectivo Hub de eventos. 
* Os editores para o Hub de eventos devem publicar na cadeia de conexão do alias. 

![Arquitetura de exemplo ativo-passivo](media/functions-geo-dr/active-passive.png)

Antes do failover, os editores que enviarem para o alias compartilhado rotearão para o Hub de eventos primário.  O aplicativo de funções primário está ouvindo exclusivamente o Hub de eventos primário.  O aplicativo de funções secundário será passivo e ocioso.  Assim que o failover for iniciado, os Publicadores que enviarem para o alias compartilhado agora serão roteados para o Hub de eventos secundário.  O aplicativo de função secundário agora ficará ativo e começará a disparar automaticamente.  O failover efetivo para uma região secundária pode ser totalmente controlado do hub de eventos, com as funções se tornando ativas somente quando o respectivo Hub de eventos estiver ativo.

Leia mais sobre informações e considerações sobre failover com o [barramento de serviço](../service-bus-messaging/service-bus-geo-dr.md) e os hubs de [eventos](../event-hubs/event-hubs-geo-dr.md).

## <a name="next-steps"></a>Passos seguintes

* [Criar porta frontal do Azure](../frontdoor/quickstart-create-front-door.md)
* [Considerações sobre failover de hubs de eventos](../event-hubs/event-hubs-geo-dr.md#considerations)
