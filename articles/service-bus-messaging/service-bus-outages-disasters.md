---
title: Isolar aplicações de autocarros da Azure contra interrupções e desastres
description: Estes artigos fornecem técnicas para proteger aplicações contra uma possível interrupção do ônibus de serviço Azure.
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 01/27/2020
ms.author: aschhab
ms.openlocfilehash: 2a7f5d5eacb2d03e64ae95d34e1cf0bd37bbc7f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259256"
---
# <a name="best-practices-for-insulating-applications-against-service-bus-outages-and-disasters"></a>Melhores práticas para proteger aplicações de indisponibilidades e de desastres do Service Bus

As aplicações críticas à missão devem funcionar continuamente, mesmo na presença de interrupções não planeadas ou catástrofes. Este artigo descreve técnicas que pode usar para proteger aplicações de ônibus de serviço contra uma possível interrupção de serviço ou desastre.

Uma paralisação é definida como a indisponibilidade temporária do Azure Service Bus. A paralisação pode afetar alguns componentes do Service Bus, como uma loja de mensagens, ou mesmo todo o datacenter. Depois de o problema ter sido corrigido, o Service Bus volta a estar disponível. Normalmente, uma interrupção não causa perda de mensagens ou outros dados. Um exemplo de falha de componentes é a indisponibilidade de uma determinada loja de mensagens. Um exemplo de uma falha em todo o datacenter é uma falha de energia do datacenter, ou um interruptor de rede de datacenter defeituoso. Uma paragem pode durar de alguns minutos a alguns dias.

Um desastre é definido como a perda permanente de uma unidade de escala de ônibus de serviço ou datacenter. O datacenter pode ou não voltar a estar disponível. Normalmente, um desastre causa a perda de algumas ou todas as mensagens ou outros dados. Exemplos de desastres são incêndios, inundações ou terramotos.

## <a name="protecting-against-outages-and-disasters---service-bus-premium"></a>Proteção contra interrupções e desastres - Service Bus Premium
Os conceitos de alta disponibilidade e recuperação de desastres são construídos diretamente no nível Azure Service Bus Premium, tanto dentro da mesma região (via Zonas de Disponibilidade) como em diferentes regiões (via Recuperação geo-desastre).

### <a name="geo-disaster-recovery"></a>Recuperação geo-desastre

Service Bus Premium suporta a recuperação geo-desastre, ao nível do espaço de nome. Para mais informações, consulte [a recuperação geo-desastre do Azure Service Bus.](service-bus-geo-dr.md) A funcionalidade de recuperação de desastres, disponível apenas para o [Premium SKU,](service-bus-premium-messaging.md) implementa a recuperação de desastres de metadados e baseia-se em espaços de recuperação de desastres primários e secundários.

### <a name="availability-zones"></a>Zonas de Disponibilidade

O Service Bus Premium SKU suporta zonas de [disponibilidade,](../availability-zones/az-overview.md)fornecendo localizações isoladas de falhas dentro da mesma região azure.

> [!NOTE]
> O suporte das Zonas de Disponibilidade para o Azure Service Bus Premium só está disponível nas [regiões do Azure](../availability-zones/az-overview.md#services-support-by-region) onde estão presentes zonas de disponibilidade.

Só pode ativar Zonas de Disponibilidade em novos espaços de nome, utilizando o portal Azure. A Service Bus não suporta a migração de espaços de nome existentes. Não é possível desativar a redundância da zona depois de a ter habilitado para o seu espaço de nome.

![1][]


## <a name="protecting-against-outages-and-disasters---service-bus-standard"></a>Proteção contra interrupções e desastres - Padrão de autocarrode serviço
Para obter resiliência contra falhas no datacenter ao utilizar o nível padrão de preços de mensagens, o Service Bus suporta duas abordagens: replicação *ativa* e *passiva.* Para cada abordagem, se uma determinada fila ou tópico deve permanecer acessível na presença de uma paragem de datacenter, pode criá-la em ambos os espaços de nome. Ambas as entidades podem ter o mesmo nome. Por exemplo, uma fila primária pode ser alcançada em **contosoPrimary.servicebus.windows.net/myQueue**, enquanto a sua contrapartida secundária pode ser alcançada sob **contosoSecondary.servicebus.windows.net/myQueue**.

>[!NOTE]
> A configuração de **Replicação Ativa** e **Replicação Passiva** são soluções de propósito geral e características não específicas do Bus de Serviço. A lógica de replicação (envio para 2 espaços de nome diferentes) vive nas aplicações do remetente e o recetor tem de ter uma lógica personalizada para a deteção duplicada.

Se a aplicação não necessitar de uma comunicação permanente de remetente para recetor, a aplicação pode implementar uma fila duradoura do lado do cliente para evitar a perda de mensagens e proteger o remetente de quaisquer erros transitórios do Autocarro de Serviço.

### <a name="active-replication"></a>Replicação ativa
A replicação ativa utiliza entidades em ambos os espaços de nome para cada operação. Qualquer cliente que envie uma mensagem envia duas cópias da mesma mensagem. A primeira cópia é enviada à entidade primária (por exemplo, **contosoPrimary.servicebus.windows.net/sales),** e a segunda cópia da mensagem é enviada à entidade secundária (por exemplo, **contosoSecondary.servicebus.windows.net/sales).**

Um cliente recebe mensagens de ambas as filas. O recetor processa a primeira cópia de uma mensagem e a segunda cópia é suprimida. Para suprimir mensagens duplicadas, o remetente deve marcar cada mensagem com um identificador único. Ambas as cópias da mensagem devem ser marcadas com o mesmo identificador. Pode utilizar as propriedades [BrokeredMessage.MessageId][BrokeredMessage.MessageId] ou [BrokeredMessage.Label,][BrokeredMessage.Label] ou uma propriedade personalizada para marcar a mensagem. O recetor deve manter uma lista de mensagens que já recebeu.

A [geo-replicação com][Geo-replication with Service Bus Standard Tier] a amostra Service Bus Standard Tier demonstra a replicação ativa de entidades de mensagens.

> [!NOTE]
> A abordagem de replicação ativa duplica o número de operações, pelo que esta abordagem pode conduzir a um custo mais elevado.
> 
> 

### <a name="passive-replication"></a>Replicação passiva
No caso sem falhas, a replicação passiva utiliza apenas uma das duas entidades de mensagens. Um cliente envia a mensagem para a entidade ativa. Se a operação na entidade ativa falhar com um código de erro que indique que o datacenter que acolhe a entidade ativa pode não estar disponível, o cliente envia uma cópia da mensagem para a entidade de backup. Nessa altura, as entidades ativas e de backup mudam de funções: o cliente que envia considera a antiga entidade ativa como a nova entidade de backup, e a antiga entidade de backup é a nova entidade ativa. Se ambas as operações de envio falharem, as funções das duas entidades permanecem inalteradas e um erro é devolvido.

Um cliente recebe mensagens de ambas as filas. Como existe a possibilidade de o recetor receber duas cópias da mesma mensagem, o recetor deve suprimir mensagens duplicadas. Pode suprimir duplicados da mesma forma que descrito para a replicação ativa.

Em geral, a replicação passiva é mais económica do que a replicação ativa, porque na maioria dos casos apenas uma operação é realizada. A latência, o custo e o custo monetário são idênticos ao cenário não replicado.

Ao utilizar a replicação passiva, nos seguintes cenários as mensagens podem ser perdidas ou recebidas duas vezes:

* **Atraso ou perda**de mensagem : Assuma que o remetente enviou com sucesso uma mensagem m1 para a fila primária e, em seguida, a fila fica indisponível antes de o recetor receber m1. O remetente envia uma mensagem subsequente m2 para a fila secundária. Se a fila primária estiver temporariamente indisponível, o recetor recebe m1 depois de a fila voltar a estar disponível. Em caso de desastre, o recetor nunca poderá receber m1.
* **Receção duplicada**: Suponha que o remetente envie uma mensagem m para a fila primária. O Service Bus processa m com sucesso, mas não envia uma resposta. Após o tempo de funcionamento do envio, o remetente envia uma cópia idêntica de m para a fila secundária. Se o recetor puder receber a primeira cópia de m antes da indisponibilidade da fila primária, o recetor recebe ambas as cópias de m aproximadamente ao mesmo tempo. Se o recetor não conseguir receber a primeira cópia de m antes da indisponibilidade da fila primária, o recetor recebe inicialmente apenas a segunda cópia de m, mas recebe uma segunda cópia de m quando a fila primária estiver disponível.

A [geo-replicação com][Geo-replication with Service Bus Standard Tier] a amostra Service Bus Standard Tier demonstra replicação passiva de entidades de mensagens.

## <a name="protecting-relay-endpoints-against-datacenter-outages-or-disasters"></a>Proteger pontos finais de retransmissão contra interrupções de datacenters ou desastres
A geo-replicação dos pontos finais [do Azure Relay](../service-bus-relay/relay-what-is-it.md) permite que um serviço que exponha um ponto final de retransmissão seja acessível na presença de interrupções de ônibus de serviço. Para conseguir a geo-replicação, o serviço deve criar dois pontos finais de retransmissão em diferentes espaços de nome. Os espaços de nome devem residir em diferentes datacenters e os dois pontos finais devem ter nomes diferentes. Por exemplo, um ponto final primário pode ser alcançado sob **contosoPrimary.servicebus.windows.net/myPrimaryService**, enquanto a sua contrapartida secundária pode ser alcançada sob **contosoSecondary.servicebus.windows.net/mySecondaryService**.

O serviço então ouve em ambos os pontos finais, e um cliente pode invocar o serviço através de qualquer ponto final. Uma aplicação de cliente escolhe aleatoriamente um dos retransmissores como o ponto final primário, e envia o seu pedido para o ponto final ativo. Se a operação falhar com um código de erro, esta falha indica que o ponto final do retransmissor não está disponível. A aplicação abre um canal para o ponto final de reserva e reedita o pedido. Nessa altura, as funções de switch ativa e de backup: a aplicação do cliente considera que o antigo ponto final ativo é o novo ponto final de backup, e o antigo ponto final de backup é o novo ponto final ativo. Se ambas as operações de envio falharem, as funções das duas entidades permanecem inalteradas e um erro é devolvido.

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre a recuperação de desastres, consulte estes artigos:

* [Recuperação geo-desastre do azure service bus](service-bus-geo-dr.md)
* [Continuidade do negócio da base de dados Azure SQL][Azure SQL Database Business Continuity]
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
