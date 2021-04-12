---
title: Isolar as aplicações do Azure Service Bus contra interrupções e desastres
description: Estes artigos fornecem técnicas para proteger as aplicações contra uma possível paragem do autocarro da Azure Service.
ms.topic: article
ms.date: 02/10/2021
ms.openlocfilehash: b9090a54cd58788dbd13f528af4dda4aa96005b7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100374597"
---
# <a name="best-practices-for-insulating-applications-against-service-bus-outages-and-disasters"></a>Melhores práticas para proteger aplicações de indisponibilidades e de desastres do Service Bus

As aplicações críticas à missão devem funcionar continuamente, mesmo na presença de interrupções ou catástrofes não planeadas. Este artigo descreve técnicas que pode usar para proteger aplicações de Service Bus contra uma possível falha de serviço ou desastre.

Uma paralisação é definida como a indisponibilidade temporária da Azure Service Bus. A paralisação pode afetar alguns componentes do Service Bus, como uma loja de mensagens, ou mesmo todo o datacenter. Depois de resolvido o problema, o Service Bus volta a estar disponível. Normalmente, uma paragem não causa perda de mensagens ou outros dados. Um exemplo de falha de um componente é a indisponibilidade de uma determinada loja de mensagens. Um exemplo de uma paragem em todo o datacenter é uma falha de energia do datacenter, ou um interruptor de rede de datacenter defeituoso. Uma paralisação pode durar de alguns minutos a alguns dias.

Um desastre é definido como a perda permanente de uma unidade de escala de autocarro de serviço ou datacenter. O datacenter pode ou não voltar a estar disponível. Normalmente, um desastre causa a perda de algumas ou todas as mensagens ou outros dados. Exemplos de desastres são incêndios, inundações ou terramotos.

## <a name="protecting-against-outages-and-disasters---service-bus-premium"></a>Proteção contra interrupções e desastres - Service Bus Premium
Os conceitos de Alta Disponibilidade e Recuperação de Desastres são integrados no nível Azure Service Bus Premium, tanto na mesma região (via Zonas de Disponibilidade) como em diferentes regiões (via Geo-Disaster Recovery).

### <a name="geo-disaster-recovery"></a>Recuperação Geo-Disaster

Service Bus Premium suporta a recuperação de geo-desastres, ao nível do espaço de nome. Para mais informações, consulte [a recuperação de geo-desastres de autocarros da Azure Service.](service-bus-geo-dr.md) A funcionalidade de recuperação de desastres, disponível apenas para o [Premium SKU,](service-bus-premium-messaging.md) implementa a recuperação de desastres de metadados e baseia-se em espaços de nomes de recuperação de desastres primários e secundários. Com Geo-Disaster Recuperação, apenas os metadados para entidades são replicados entre espaços de nome primário e secundário.  

### <a name="availability-zones"></a>Zonas de Disponibilidade

O Service Bus Premium SKU suporta [Zonas de Disponibilidade,](../availability-zones/az-overview.md)fornecendo localizações isoladas de falhas na mesma região de Azure. A Service Bus gere três cópias da loja de mensagens (1 primária e 2 secundária). A Service Bus mantém as três cópias sincronizadas para operações de dados e gestão. Se a cópia primária falhar, uma das cópias secundárias é promovida para primária sem tempo de inatividade percebido. Se as aplicações virem desconexões transitórias do Service Bus, a lógica de repetição no SDK reconectará-se automaticamente ao Service Bus. 

Quando utiliza zonas de disponibilidade, tanto os metadados como os dados (mensagens) são replicados em centros de dados na zona de disponibilidade. 

> [!NOTE]
> O suporte das Zonas disponibilidade para o Azure Service Bus Premium só está disponível nas [regiões de Azure](../availability-zones/az-region.md) onde existem zonas de disponibilidade.

Pode ativar zonas de disponibilidade apenas em novos espaços de nome, utilizando o portal Azure. A Service Bus não apoia a migração de espaços de nome existentes. Não é possível desativar a redundância da zona depois de a permitir no seu espaço de nome.

![1][]


## <a name="protecting-against-outages-and-disasters---service-bus-standard"></a>Proteção contra interrupções e desastres - Service Bus Standard
Para obter resiliência contra falhas no datacenter ao utilizar o nível de preços padrão de mensagens, o Service Bus suporta duas abordagens: replicação *ativa* e *passiva.* Para cada abordagem, se uma determinada fila ou tópico deve permanecer acessível na presença de uma interrupção do datacenter, pode criá-la em ambos os espaços de nome. Ambas as entidades podem ter o mesmo nome. Por exemplo, uma fila primária pode ser alcançada sob **contosoPrimary.servicebus.windows.net/myQueue**, enquanto a sua contraparte secundária pode ser alcançada sob **contosoSecondary.servicebus.windows.net/myQueue**.

>[!NOTE]
> A **configuração de replicação ativa** e **replicação passiva** são soluções de finalidade geral e não características específicas do Service Bus. A lógica de replicação (envio para 2 espaços de nome diferentes) vive nas aplicações do remetente e o recetor tem de ter lógica personalizada para a deteção duplicada.

Se a aplicação não necessitar de uma comunicação permanente entre remetente e recetor, a aplicação pode implementar uma fila duradoura do lado do cliente para evitar a perda de mensagens e para proteger o remetente de quaisquer erros transitórios do Service Bus.

### <a name="active-replication"></a>Replicação ativa
A replicação ativa utiliza entidades em ambos os espaços de nome para cada operação. Qualquer cliente que envie uma mensagem envia duas cópias da mesma mensagem. A primeira cópia é enviada para a entidade primária (por exemplo, **contosoPrimary.servicebus.windows.net/sales),** e a segunda cópia da mensagem é enviada para a entidade secundária (por exemplo, **contosoSecondary.servicebus.windows.net/sales).**

Um cliente recebe mensagens de ambas as filas. O recetor processa a primeira cópia de uma mensagem e a segunda cópia é suprimida. Para suprimir mensagens duplicadas, o remetente deve marcar cada mensagem com um identificador único. Ambas as cópias da mensagem devem ser marcadas com o mesmo identificador. Pode utilizar as propriedades [BrokeredMessage.MessageId][BrokeredMessage.MessageId] ou [BrokeredMessage.Label][BrokeredMessage.Label] ou uma propriedade personalizada para marcar a mensagem. O recetor deve manter uma lista de mensagens que já recebeu.

A [geo-replicação com][Geo-replication with Service Bus Standard Tier] a amostra Service Bus Standard Tier demonstra a replicação ativa de entidades de mensagens.

> [!NOTE]
> A abordagem ativa de replicação duplica o número de operações, pelo que esta abordagem pode conduzir a custos mais elevados.
> 
> 

### <a name="passive-replication"></a>Replicação passiva
No caso de livre falhas, a replicação passiva utiliza apenas uma das duas entidades de mensagens. Um cliente envia a mensagem para a entidade ativa. Se a operação na entidade ativa falhar com um código de erro que indique que o datacenter que acolhe a entidade ativa pode estar indisponível, o cliente envia uma cópia da mensagem para a entidade de backup. Nessa altura, as entidades ativas e de backup mudam de funções: o cliente que envia considera a antiga entidade ativa como a nova entidade de backup, e a antiga entidade de backup é a nova entidade ativa. Se ambas as operações de envio falharem, as funções das duas entidades permanecem inalteradas e um erro é devolvido.

Um cliente recebe mensagens de ambas as filas. Uma vez que existe a possibilidade de o recetor receber duas cópias da mesma mensagem, o recetor deve suprimir mensagens duplicadas. Pode suprimir duplicações da mesma forma que descrito para a replicação ativa.

Em geral, a replicação passiva é mais económica do que a replicação ativa, porque na maioria dos casos apenas uma operação é realizada. A latência, o rendimento e o custo monetário são idênticos ao cenário não replicado.

Ao utilizar a replicação passiva, nos seguintes cenários as mensagens podem ser perdidas ou recebidas duas vezes:

* **Atraso ou perda** de mensagem : Assuma que o remetente enviou com sucesso uma mensagem m1 para a fila primária e, em seguida, a fila fica indisponível antes de o recetor receber m1. O remetente envia uma mensagem m2 subsequente para a fila secundária. Se a fila primária estiver temporariamente indisponível, o recetor recebe m1 depois de a fila voltar a estar disponível. Em caso de catástrofe, o recetor nunca poderá receber m1.
* **Receção duplicada**: Assuma que o remetente envia uma mensagem m para a fila primária. A Service Bus processa m com sucesso mas não envia uma resposta. Após o envio dos tempos de funcionamento, o remetente envia uma cópia idêntica de m para a fila secundária. Se o recetor conseguir receber a primeira cópia de m antes da fila primária ficar indisponível, o recetor recebe ambas as cópias de m aproximadamente ao mesmo tempo. Se o recetor não conseguir receber a primeira cópia de m antes da fila primária ficar indisponível, o recetor recebe inicialmente apenas a segunda cópia de m, mas recebe uma segunda cópia de m quando a fila primária fica disponível.

A [geo-replicação com][Geo-replication with Service Bus Standard Tier] a amostra Service Bus Standard Tier demonstra a replicação passiva de entidades de mensagens.

## <a name="protecting-relay-endpoints-against-datacenter-outages-or-disasters"></a>Proteção dos pontos finais de retransmissão contra interrupções ou desastres do datacenter
A geo-replicação dos pontos finais da [Azure Relay](../azure-relay/relay-what-is-it.md) permite que um serviço que expõe um ponto final de retransmissão seja alcançável na presença de paragens de Service Bus. Para obter a geo-replicação, o serviço deve criar dois pontos finais de retransmissão em diferentes espaços de nome. Os espaços de nome devem residir em diferentes datacenters e os dois pontos finais devem ter nomes diferentes. Por exemplo, um ponto final primário pode ser alcançado sob **contosoPrimary.servicebus.windows.net/myPrimaryService**, enquanto a sua contraparte secundária pode ser alcançada sob **contosoSecondary.servicebus.windows.net/mySecondaryService**.

O serviço então ouve em ambos os pontos finais, e um cliente pode invocar o serviço através de qualquer ponto final. Uma aplicação de cliente escolhe aleatoriamente um dos retransmissores como ponto final primário, e envia o seu pedido para o ponto final ativo. Se a operação falhar com um código de erro, esta falha indica que o ponto final do relé não está disponível. A aplicação abre um canal para o ponto final de reserva e reedita o pedido. Nessa altura, o ponto final ativo e o backup trocam as funções: a aplicação do cliente considera o antigo ponto final ativo como o novo ponto final de backup, e o antigo ponto final de backup é o novo ponto final ativo. Se ambas as operações de envio falharem, as funções das duas entidades permanecem inalteradas e um erro é devolvido.

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre a recuperação de desastres, consulte estes artigos:

* [Recuperação de geo-desastre de autocarro de serviço Azure](service-bus-geo-dr.md)
* [Continuidade do negócio da base de dados Azure SQL][Azure SQL Database Business Continuity]
* [Conceber aplicações resilientes para o Azure][Azure resiliency technical guidance]

[Service Bus Authentication]: service-bus-authentication-and-authorization.md
[Partitioned messaging entities]: service-bus-partitioning.md
[Asynchronous messaging patterns and high availability]: service-bus-async-messaging.md#failure-of-service-bus-within-an-azure-datacenter
[BrokeredMessage.MessageId]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[BrokeredMessage.Label]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Geo-replication with Service Bus Standard Tier]: https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoReplication
[Azure SQL Database Business Continuity]:../azure-sql/database/business-continuity-high-availability-disaster-recover-hadr-overview.md
[Azure resiliency technical guidance]: /azure/architecture/resiliency

[1]: ./media/service-bus-outages-disasters/az.png