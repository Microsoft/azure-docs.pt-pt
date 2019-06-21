---
title: Isolando as aplicações do Azure Service Bus contra interrupções e desastres | Documentos da Microsoft
description: Técnicas para proteger as aplicações em caso de falha possível do Service Bus.
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 09/14/2018
ms.author: aschhab
ms.openlocfilehash: 24fba1961c8fd95f1b9489716d690dd6eaa97b62
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67274842"
---
# <a name="best-practices-for-insulating-applications-against-service-bus-outages-and-disasters"></a>Melhores práticas para separando aplicativos em relação do Service Bus interrupções e desastres

Aplicativos de missão crítica devem operar continuamente, mesmo na ocorrência de falhas não planeadas ou desastres. Este artigo descreve as técnicas que pode ser usadas para proteger as aplicações do Service Bus em relação a uma potencial falha de serviço ou um desastre.

Um período de indisponibilidade é definido como a indisponibilidade temporária do Azure Service Bus. A falha pode afetar alguns componentes do Service Bus, como um arquivo de mensagens, ou até mesmo o todo o datacenter. Depois do problema foi resolvido, do Service Bus ficar novamente disponível. Normalmente, uma indisponibilidade não irá causar perda de mensagens ou outros dados. Um exemplo de uma falha de componente é a indisponibilidade de um arquivo de mensagens específico. Um exemplo de uma falha de todo o datacenter é uma falha de energia do Centro de dados ou um comutador de rede do Centro de dados com falhas. Pode sobreviver a uma falha de alguns minutos alguns dias.

Após um desastre é definido como a perda permanente de uma unidade de escala do Service Bus ou datacenter. O Centro de dados poderá ou poderá não fique disponível novamente. Normalmente, um desastre faz com que a perda de algumas ou todas as mensagens ou outros dados. Exemplos de desastres são fire, sobrecarregar ou sismo.

## <a name="protecting-against-outages-and-disasters---service-bus-premium"></a>Proteger contra interrupções e desastres - Premium do Service Bus
Conceitos de disponibilidade e recuperação após desastre alta são criados diretamente no escalão Premium do Azure Service Bus, tanto de dentro da mesma região (através de zonas de disponibilidade) e em diferentes regiões (por meio de recuperação após desastre geográfico).

### <a name="geo-disaster-recovery"></a>Recuperação após desastre geográfico

Premium do Service Bus suporta a recuperação após desastre geográfico, ao nível do espaço de nomes. Para obter mais informações, consulte [recuperação de desastre geográfico de Azure Service Bus](service-bus-geo-dr.md). O recurso de recuperação após desastre, disponível para o [Premium SKU](service-bus-premium-messaging.md) apenas, implementa a recuperação após desastre de metadados e se baseia nos espaços de nomes de recuperação de desastres primário e secundário.

### <a name="availability-zones"></a>Zonas de Disponibilidade

O SKU do Service Bus Premium suporta [zonas de disponibilidade](../availability-zones/az-overview.md), fornecer localizações isoladas de falhas na mesma região do Azure.

> [!NOTE]
> O suporte de zonas de disponibilidade para o Azure Service Bus Premium só está disponível no [regiões do Azure](../availability-zones/az-overview.md#services-support-by-region) onde as zonas de disponibilidade estão presentes.

Pode ativar as zonas de disponibilidade nos novos espaços de nomes apenas, com o portal do Azure. Barramento de serviço não suporta a migração de espaços de nomes existentes. Não é possível desativar a redundância de zona após ativá-la no seu espaço de nomes.

![1][]


## <a name="protecting-against-outages-and-disasters---service-bus-standard"></a>Proteger contra interrupções e desastres - padrão de barramento de serviço
Para obter resiliência contra falhas de datacenter ao utilizar o escalão de preço de mensagens padrão, o Service Bus suporta duas abordagens: *Active Directory* e *passivo* replicação. Para cada abordagem, se uma determinada fila ou tópico tem de permanecer acessível na presença de uma falha do datacenter, pode criá-la em ambos os espaços de nomes. As duas entidades podem ter o mesmo nome. Por exemplo, uma fila primária pode ser contatada em **contosoPrimary.servicebus.windows.net/myQueue**, enquanto sua contraparte secundário pode ser contatado em **contosoSecondary.servicebus.windows.net/myQueue**.

>[!NOTE]
> O **replicação de Active Directory** e **replicação passivo** configuração são soluções de fins gerais e os recursos não específicos do Service Bus. A lógica de replicação (enviar a namespaces diferentes 2) reside nos aplicativos remetente e o destinatário tem de ter uma lógica personalizada para deteção de duplicados.

Se a aplicação não necessitar de comunicação de remetente para o recetor permanente, a aplicação pode implementar uma fila durável do lado do cliente para evitar a perda de mensagens e para proteger o remetente quaisquer erros transitórios do Service Bus.

### <a name="active-replication"></a>Replicação do Active Directory
Replicação de Active Directory utiliza entidades em ambos os espaços de nomes para cada operação. Qualquer cliente que envia uma mensagem envia duas cópias da mesma mensagem. A primeira cópia é enviada para a entidade principal (por exemplo, **contosoPrimary.servicebus.windows.net/sales**), e a segunda cópia da mensagem é enviada para a entidade secundária (por exemplo,  **contosoSecondary.servicebus.windows.net/sales**).

Um cliente recebe mensagens de ambas as filas. O recetor processar a primeira cópia de uma mensagem e a segunda cópia suprimida. Para suprimir mensagens duplicadas, o remetente deve etiquetar cada mensagem com um identificador exclusivo. As duas cópias da mensagem devem ser etiquetadas com o mesmo identificador. Pode utilizar o [Brokeredmessage][BrokeredMessage.MessageId] ou [BrokeredMessage.Label][BrokeredMessage.Label] propriedades ou uma propriedade personalizada a mensagem. O destinatário tem de manter uma lista de mensagens que já recebidos.

O [georreplicação com o escalão Standard do Service Bus][Geo-replication with Service Bus Standard Tier] exemplo demonstra os replicação de Active Directory de entidades de mensagens.

> [!NOTE]
> A abordagem de replicação do Active Directory duplica o número de operações, essa abordagem pode levar à custo mais elevado.
> 
> 

### <a name="passive-replication"></a>Replicação passiva
No caso de falhas gratuitos, replicação passiva usa apenas uma das duas entidades de mensagens. Um cliente envia a mensagem para a entidade de Active Directory. Se a operação na entidade active falhar com um código de erro que indica o datacenter que aloja a entidade de Active Directory pode não estar disponível, o cliente envia uma cópia da mensagem para a entidade de cópia de segurança. Nesse momento o Active Directory e as entidades de cópia de segurança mudar funções: o cliente de envio considera a entidade de Active Directory antiga a ser a nova entidade de cópia de segurança e a entidade de cópia de segurança antiga é a nova entidade de Active Directory. Se ambos enviar operações falhar, as funções de duas entidades permanecem inalteradas e é devolvido um erro.

Um cliente recebe mensagens de ambas as filas. Uma vez que é provável que o destinatário recebe duas cópias da mesma mensagem, o recetor deve suprimir mensagens duplicadas. É possível suprimir duplicados da mesma forma, conforme descrito para a replicação do Active Directory.

Em geral, os replicação passivo é mais económico do que a replicação do Active Directory, porque na maioria dos casos é executada apenas uma operação. Latência, débito e o custo de monetário são idênticos para o cenário de não-replicados.

Ao usar os replicação passivo, nos seguintes cenários de mensagens são perdidas ou recebidas duas vezes:

* **Atraso de mensagem ou perda**: Partem do princípio de que o remetente enviada com êxito um m1 de mensagem para a fila primária e, em seguida, a fila fica indisponível antes do recetor recebe m1. O remetente envia uma mensagem subseqüente m2 para a fila secundária. Se a fila primária está temporariamente indisponível, o destinatário recebe m1 depois da fila fica disponível novamente. Em caso de desastre, o recetor nunca poderá receber m1.
* **Duplicar receção**: Partem do princípio de que o remetente envia uma mensagem m para a fila principal. Do Service Bus com êxito processa m, mas não conseguir enviar uma resposta. Depois da operação de envio exceder o tempo limite, o remetente envia uma cópia idêntica do m para a fila secundária. Se o destinatário é consegue receber a primeira cópia de m antes da fila primária fica indisponível, o destinatário recebe ambas as cópias de m, aproximadamente ao mesmo tempo. Se o recetor não for capaz de receber a primeira cópia de m antes da fila primária fica indisponível, o destinatário recebe inicialmente apenas a segunda cópia da m, mas, em seguida, recebe uma segunda cópia da m quando a fila primária fica disponível.

O [georreplicação com o escalão Standard do Service Bus][Geo-replication with Service Bus Standard Tier] exemplo demonstra os replicação passiva de entidades de mensagens.

## <a name="protecting-relay-endpoints-against-datacenter-outages-or-disasters"></a>Proteger pontos finais de reencaminhamento contra falhas de datacenter ou desastres
Georreplicação de [reencaminhamento do Azure](../service-bus-relay/relay-what-is-it.md) pontos finais permite que um serviço que expõe um ponto de extremidade de reencaminhamento estar acessível na presença de falhas do Service Bus. Para alcançar os replicação geográfica, o serviço tem de criar dois pontos de extremidade de reencaminhamento num espaço de nomes diferentes. Os espaços de nomes tem de residir em datacenters diferentes e dois pontos de extremidade tem de ter nomes diferentes. Por exemplo, um ponto final primário pode ser contatado em **contosoPrimary.servicebus.windows.net/myPrimaryService**, enquanto sua contraparte secundário pode ser contatado em **contosoSecondary.servicebus.windows.net /mySecondaryService**.

O serviço de escuta, em seguida, em ambos os pontos de extremidade e um cliente pode chamar o serviço através de qualquer ponto de extremidade. Uma aplicação cliente escolhe um dos relés como o ponto final primário aleatoriamente e envia a solicitação para o ponto final do Active Directory. Se a operação falhar com um código de erro, esta falha indica que o ponto de extremidade de reencaminhamento não está disponível. A aplicação abrirá um canal para o ponto final de cópia de segurança e emite novamente o pedido. Nesse momento o Active Directory e os pontos finais de cópia de segurança mudar funções: a aplicação cliente considera o antigo ponto final do Active Directory para ser o novo ponto de final de cópia de segurança e o ponto de final de cópia de segurança antigo para o novo ponto final do Active Directory. Se ambos enviar operações falhar, as funções de duas entidades permanecem inalteradas e é devolvido um erro.

## <a name="next-steps"></a>Passos Seguintes
Para saber mais sobre a recuperação após desastre, veja estes artigos:

* [Recuperação após desastre do Service Bus Geo do Azure](service-bus-geo-dr.md)
* [Continuidade de negócio de base de dados SQL do Azure][Azure SQL Database Business Continuity]
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
