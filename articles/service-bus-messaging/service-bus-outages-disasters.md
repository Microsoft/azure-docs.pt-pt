---
title: Isolar aplicativos do barramento de serviço do Azure contra interrupções e desastres
description: Técnicas para proteger aplicativos contra uma possível interrupção do barramento de serviço.
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 09/14/2018
ms.author: aschhab
ms.openlocfilehash: 9375ea4637db7727a40477177f180ab70bd4f417
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75462147"
---
# <a name="best-practices-for-insulating-applications-against-service-bus-outages-and-disasters"></a>Práticas recomendadas para isolar aplicativos contra interrupções e desastres do barramento de serviço

Os aplicativos de missão crítica devem operar continuamente, mesmo na presença de interrupções ou desastres não planejados. Este artigo descreve técnicas que você pode usar para proteger aplicativos do barramento de serviço contra uma potencial interrupção de serviço ou desastre.

Uma interrupção é definida como a indisponibilidade temporária do barramento de serviço do Azure. A interrupção pode afetar alguns componentes do barramento de serviço, como um repositório de mensagens ou até mesmo o datacenter inteiro. Depois que o problema for corrigido, o barramento de serviço ficará disponível novamente. Normalmente, uma interrupção não causa perda de mensagens ou outros dados. Um exemplo de uma falha de componente é a indisponibilidade de um repositório de mensagens específico. Um exemplo de uma interrupção em todo o datacenter é uma falha de energia do Datacenter ou um comutador de rede de datacenter com falha. Uma interrupção pode durar de alguns minutos a alguns dias.

Um desastre é definido como a perda permanente de uma unidade de escala ou de um datacenter do barramento de serviço. O datacenter pode ou não ficar disponível novamente. Normalmente, um desastre causa perda de algumas ou de todas as mensagens ou de outros dados. Exemplos de desastres são incêndios, inundações ou terremotos.

## <a name="protecting-against-outages-and-disasters---service-bus-premium"></a>Proteção contra interrupções e desastres – barramento de serviço Premium
Os conceitos de alta disponibilidade e recuperação de desastres são criados diretamente na camada Premium do barramento de serviço do Azure, na mesma região (via Zonas de Disponibilidade) e em regiões diferentes (por meio de recuperação de desastres geograficamente).

### <a name="geo-disaster-recovery"></a>Recuperação de desastres geograficamente

O barramento de serviço Premium dá suporte à recuperação de desastres geograficamente, no nível do namespace. Para obter mais informações, consulte [recuperação de desastre geográfico do barramento de serviço do Azure](service-bus-geo-dr.md). O recurso de recuperação de desastres, disponível somente para o [SKU Premium](service-bus-premium-messaging.md) , implementa a recuperação de desastre de metadados e se baseia em namespaces de recuperação de desastre primário e secundário.

### <a name="availability-zones"></a>Zonas de Disponibilidade

A SKU Premium do barramento de serviço dá suporte a [zonas de disponibilidade](../availability-zones/az-overview.md), fornecendo locais isolados com falhas na mesma região do Azure.

> [!NOTE]
> O suporte Zonas de Disponibilidade para o barramento de serviço Premium do Azure só está disponível nas [regiões do Azure](../availability-zones/az-overview.md#services-support-by-region) em que as zonas de disponibilidade estão presentes.

Pode ativar as zonas de disponibilidade nos novos espaços de nomes apenas, com o portal do Azure. O barramento de serviço não oferece suporte à migração de namespaces existentes. Não é possível desativar a redundância de zona após ativá-la no seu espaço de nomes.

![1][]


## <a name="protecting-against-outages-and-disasters---service-bus-standard"></a>Proteção contra interrupções e desastres-padrão do barramento de serviço
Para obter resiliência contra interrupções do datacenter ao usar o tipo de preço de sistema de mensagens Standard, o barramento de serviço dá suporte a duas abordagens: replicação *ativa* e *passiva* . Para cada abordagem, se uma determinada fila ou tópico precisar permanecer acessível na presença de uma interrupção do datacenter, você poderá criá-lo em ambos os namespaces. Ambas as entidades podem ter o mesmo nome. Por exemplo, uma fila primária pode ser acessada em **contosoPrimary.ServiceBus.Windows.net/MyQueue**, enquanto sua contraparte secundária pode ser acessada em **contosoSecondary.ServiceBus.Windows.net/MyQueue**.

>[!NOTE]
> A **replicação ativa** e a configuração de **replicação passiva** são soluções de uso geral e não recursos específicos do barramento de serviço. A lógica de replicação (envio para 2 namespaces diferentes) reside nos aplicativos do remetente e o receptor precisa ter uma lógica personalizada para detecção de duplicidades.

Se o aplicativo não exigir comunicação permanente de remetente para receptor, o aplicativo poderá implementar uma fila durável do lado do cliente para evitar a perda de mensagens e proteger o remetente de erros transitórios do barramento de serviço.

### <a name="active-replication"></a>Replicação ativa
A replicação ativa usa entidades em ambos os namespaces para cada operação. Qualquer cliente que envia uma mensagem envia duas cópias da mesma mensagem. A primeira cópia é enviada para a entidade primária (por exemplo, **contosoPrimary.ServiceBus.Windows.net/Sales**) e a segunda cópia da mensagem é enviada para a entidade secundária (por exemplo, **contosoSecondary.ServiceBus.Windows.net/Sales**).

Um cliente recebe mensagens de ambas as filas. O receptor processa a primeira cópia de uma mensagem e a segunda cópia é suprimida. Para suprimir mensagens duplicadas, o remetente deve marcar cada mensagem com um identificador exclusivo. As duas cópias da mensagem devem ser marcadas com o mesmo identificador. Você pode usar as propriedades [BrokeredMessage. MessageID][BrokeredMessage.MessageId] ou [BrokeredMessage. Label][BrokeredMessage.Label] ou uma propriedade personalizada para marcar a mensagem. O receptor deve manter uma lista de mensagens que já recebeu.

O exemplo de [replicação geográfica com camada padrão do barramento de serviço][Geo-replication with Service Bus Standard Tier] demonstra a replicação ativa de entidades de mensagens.

> [!NOTE]
> A abordagem de replicação ativa dobra o número de operações, portanto, essa abordagem pode levar a um custo mais alto.
> 
> 

### <a name="passive-replication"></a>Replicação passiva
No caso sem falhas, a replicação passiva usa apenas uma das duas entidades de mensagens. Um cliente envia a mensagem para a entidade ativa. Se a operação na entidade ativa falhar com um código de erro que indica que o datacenter que hospeda a entidade ativa pode estar indisponível, o cliente enviará uma cópia da mensagem para a entidade de backup. Nesse ponto, as entidades ativas e de backup alternam funções: o cliente de envio considera a entidade ativa antiga como a nova entidade de backup e a entidade de backup antiga é a nova entidade ativa. Se ambas as operações de envio falharem, as funções das duas entidades permanecerão inalteradas e um erro será retornado.

Um cliente recebe mensagens de ambas as filas. Como há uma chance de que o receptor receba duas cópias da mesma mensagem, o receptor deve suprimir mensagens duplicadas. Você pode suprimir duplicatas da mesma maneira como descrito para replicação ativa.

Em geral, a replicação passiva é mais econômica do que a replicação ativa porque, na maioria dos casos, apenas uma operação é executada. A latência, a taxa de transferência e o custo monetário são idênticos ao cenário não replicado.

Ao usar a replicação passiva, as mensagens de cenários a seguir podem ser perdidas ou recebidas duas vezes:

* **Atraso ou perda de mensagem**: Suponha que o remetente enviou com êxito uma mensagem M1 para a fila primária e, em seguida, a fila fica indisponível antes que o receptor receba M1. O remetente envia uma mensagem m2 subsequente para a fila secundária. Se a fila primária estiver temporariamente indisponível, o receptor receberá M1 depois que a fila ficar disponível novamente. No caso de um desastre, o receptor pode nunca receber M1.
* **Recepção duplicada**: Suponha que o remetente envie uma mensagem m para a fila primária. O barramento de serviço processa m com êxito, mas falha ao enviar uma resposta. Depois que a operação de envio atinge o tempo limite, o remetente envia uma cópia idêntica de m para a fila secundária. Se o receptor for capaz de receber a primeira cópia de m antes de a fila primária ficar indisponível, o receptor receberá ambas as cópias de m aproximadamente ao mesmo tempo. Se o receptor não puder receber a primeira cópia de m antes que a fila primária fique indisponível, o receptor receberá inicialmente apenas a segunda cópia de m, mas, em seguida, receberá uma segunda cópia de m quando a fila primária ficar disponível.

O exemplo de [replicação geográfica com camada padrão do barramento de serviço][Geo-replication with Service Bus Standard Tier] demonstra a replicação passiva de entidades de mensagens.

## <a name="protecting-relay-endpoints-against-datacenter-outages-or-disasters"></a>Protegendo pontos de extremidade de retransmissão contra interrupções ou desastres do datacenter
A replicação geográfica de pontos de extremidade de [retransmissão do Azure](../service-bus-relay/relay-what-is-it.md) permite que um serviço que expõe um ponto final de retransmissão seja acessível na presença de interrupções do barramento de serviço. Para obter a replicação geográfica, o serviço deve criar dois pontos de extremidade de retransmissão em namespaces diferentes. Os namespaces devem residir em data centers diferentes e os dois pontos de extremidade devem ter nomes diferentes. Por exemplo, um ponto de extremidade primário pode ser acessado em **contosoPrimary.ServiceBus.Windows.net/myPrimaryService**, enquanto sua contraparte secundária pode ser acessada em **contosoSecondary.ServiceBus.Windows.net/mySecondaryService**.

Em seguida, o serviço escuta os dois pontos de extremidade, e um cliente pode invocar o serviço por meio de qualquer um deles. Um aplicativo cliente escolhe aleatoriamente uma das retransmissões como o ponto de extremidade primário e envia sua solicitação ao ponto de extremidade ativo. Se a operação falhar com um código de erro, essa falha indicará que o ponto de extremidade de retransmissão não está disponível. O aplicativo abre um canal para o ponto de extremidade de backup e emite a solicitação novamente. Nesse ponto, o ativo e os pontos de extremidade de backup alternam as funções: o aplicativo cliente considera o ponto de extremidade ativo antigo como o novo ponto de extremidade de backup e o ponto de extremidade de backup antigo como o novo ponto de extremidade ativo. Se ambas as operações de envio falharem, as funções das duas entidades permanecerão inalteradas e um erro será retornado.

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre a recuperação de desastres, consulte estes artigos:

* [Recuperação de desastre geográfica do barramento de serviço do Azure](service-bus-geo-dr.md)
* [Continuidade dos negócios do banco de dados SQL do Azure][Azure SQL Database Business Continuity]
* [Conceber aplicações resilientes para o Azure][Azure resiliency technical guidance]

[Service Bus Authentication]: service-bus-authentication-and-authorization.md
[Partitioned messaging entities]: service-bus-partitioning.md
[Asynchronous messaging patterns and high availability]: service-bus-async-messaging.md#failure-of-service-bus-within-an-azure-datacenter
[BrokeredMessage.MessageId]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[BrokeredMessage.Label]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Geo-replication with Service Bus Standard Tier]: https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoReplication
[Azure SQL Database Business Continuity]: ../sql-database/sql-database-business-continuity.md
[Azure resiliency technical guidance]: /azure/architecture/resiliency

[1]: ./media/service-bus-outages-disasters/az.png
