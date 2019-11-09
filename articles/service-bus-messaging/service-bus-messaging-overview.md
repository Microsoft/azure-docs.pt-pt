---
title: Descrição geral de mensagens do Service Bus do Azure | Microsoft Docs
description: Descrição de mensagens do Service Bus
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: overview
ms.date: 11/04/2019
ms.custom: mvc
ms.author: aschhab
ms.openlocfilehash: e2460ab760811a3db39058eac74d519ca09046c6
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73889821"
---
# <a name="what-is-azure-service-bus"></a>O que é o Azure Service Bus?

O Microsoft Azure Service Bus é um mediador de mensagens de integração empresarial totalmente gerido. O barramento de serviço pode desassociar aplicativos e serviços. O barramento de serviço oferece uma plataforma confiável e segura para dados assíncronos e transferência de estado.

Os dados são transferidos entre diferentes aplicações e serviços utilizando *mensagens*. Uma mensagem está em formato binário e pode conter JSON, XML ou apenas texto. Para obter mais informações, consulte [Integration Services](https://azure.com/integration).

Alguns cenários comuns de mensagens são:

* *Mensagens*. Transfira dados corporativos, como ordens de compra ou vendas, diários ou movimentações de estoque.
* *Dissociar aplicativos*. Melhore a confiabilidade e a escalabilidade de aplicativos e serviços. O cliente e o serviço não precisam estar online ao mesmo tempo.
* *Tópicos e assinaturas*. Habilite relações 1:*n* entre Publicadores e assinantes.
* *Sessões de mensagens*. Implemente fluxos de trabalho que exigem ordenação de mensagens ou adiamento de mensagens.

## <a name="namespaces"></a>Espaços de nomes

Um namespace é um contêiner para todos os componentes de mensagens. Várias filas e tópicos podem estar em um único namespace, e os namespaces geralmente servem como contêineres de aplicativos.

## <a name="queues"></a>Filas

As mensagens são enviadas e recebidas de *filas*. As filas armazenam mensagens até que o aplicativo receptor esteja disponível para receber e processá-las.

![Filas](./media/service-bus-messaging-overview/about-service-bus-queue.png)

As mensagens nas filas são ordenadas e com carimbo de data/hora na chegada. Depois de aceite, a mensagem é guardada em segurança no armazenamento redundante. As mensagens são entregues no modo de *pull* , apenas entregando mensagens quando solicitado.

## <a name="topics"></a>Tópicos

Também pode utilizar *tópicos* para enviar e receber mensagens. Embora uma fila seja utilizada frequentemente para comunicação ponto a ponto, os tópicos são úteis em cenários de publicação/subscrição.

![Tópico](./media/service-bus-messaging-overview/about-service-bus-topic.png)

Os tópicos podem ter várias subscrições independentes. Um subscritor de um tópico pode receber uma cópia de cada mensagem enviada para esse tópico. As assinaturas são nomeadas como entidades. As assinaturas persistem, mas podem expirar ou excluir a exclusão.

Talvez você não queira que assinaturas individuais recebam todas as mensagens enviadas a um tópico. Nesse caso, você pode usar *regras* e *filtros* para definir condições que disparam *ações*opcionais. Você pode filtrar as mensagens especificadas e definir ou modificar as propriedades da mensagem. Para obter mais informações, consulte [tópico Filters and actions](topic-filters.md).

## <a name="advanced-features"></a>Funcionalidades avançadas

O barramento de serviço inclui recursos avançados que permitem que você resolva problemas de mensagens mais complexos. As seções a seguir descrevem vários desses recursos.

### <a name="message-sessions"></a>Sessões de mensagens

Para criar uma garantia FIFO (primeiro a entrar, primeiro a sair) no barramento de serviço, use sessões. As sessões de mensagens permitem o tratamento em conjunto e ordenado de sequências não associadas de mensagens relacionadas. Para obter mais informações, consulte [sessões de mensagens: PEPS (primeiro a entrar, primeiro a sair)](message-sessions.md).

### <a name="autoforwarding"></a>Encaminhamento automático

O recurso de encaminhamento automático encadeia uma fila ou assinatura para outra fila ou tópico. Eles devem fazer parte do mesmo namespace. Com o encaminhamento automático, o barramento de serviço remove automaticamente as mensagens de uma fila ou assinatura e as coloca em uma fila ou tópico diferente. Para obter mais informações, consulte [encadeando entidades do barramento de serviço com encaminhamento](service-bus-auto-forwarding.md)automático.

### <a name="dead-letter-queue"></a>Fila de mensagens mortas

O barramento de serviço dá suporte a uma DLQ (fila de mensagens mortas). Um DLQ contém mensagens que não podem ser entregues a nenhum receptor. Ele contém mensagens que não podem ser processadas. O barramento de serviço permite remover mensagens do DLQ e inspecioná-las. Para obter mais informações, consulte [visão geral das filas de mensagens mortas do barramento de serviço](service-bus-dead-letter-queues.md).

### <a name="scheduled-delivery"></a>Entrega agendada

Você pode enviar mensagens para uma fila ou um tópico para processamento atrasado. Você pode agendar um trabalho para ser disponibilizado para processamento por um sistema em um determinado momento. Para obter mais informações, consulte [mensagens agendadas](message-sequencing.md#scheduled-messages).

### <a name="message-deferral"></a>Diferimento de mensagens

Um cliente de fila ou assinatura pode adiar a recuperação de uma mensagem até uma hora posterior. Esse adiamento pode ser devido a circunstâncias especiais no aplicativo. A mensagem permanece na fila ou assinatura, mas está reservada. Para obter mais informações, consulte [adiamento de mensagem](message-deferral.md).

### <a name="batching"></a>Lotes

O envio em lote do lado do cliente permite que um cliente de fila ou de tópico atrase enviando uma mensagem por um determinado período de tempo. Se o cliente enviar mensagens adicionais durante este período de tempo, transmitirá as mensagens num único lote. Para obter mais informações, consulte [envio em lote no lado do cliente](service-bus-performance-improvements.md#client-side-batching).

### <a name="transactions"></a>Transações

Uma transação agrupa duas ou mais operações em um *escopo de execução*. O barramento de serviço dá suporte a operações de agrupamento em uma única entidade de mensagens dentro do escopo de uma única transação. Uma entidade de mensagem pode ser uma fila, um tópico ou uma assinatura. Para obter mais informações, consulte [visão geral do processamento de transações do barramento de serviço](service-bus-transactions.md).

### <a name="filtering-and-actions"></a>Filtragem e ações

Os subscritores podem definir as mensagens que pretendem receber de um tópico. Essas mensagens são especificadas na forma de uma ou mais regras de assinatura nomeadas. Para cada condição de regra de correspondência, a assinatura produz uma cópia da mensagem, que pode ser anotada de forma diferente para cada regra de correspondência. Para obter mais informações, consulte [tópico Filters and actions](topic-filters.md).

### <a name="autodelete-on-idle"></a>Exclusão autoativa em ociosidade

A exclusão automática em ociosidade permite que você especifique um intervalo ocioso após o qual uma fila é excluída automaticamente. A duração mínima é 5 minutos. Para obter mais informações, consulte a [Propriedade QueueDescription. AutoDeleteOnIdle](/dotnet/api/microsoft.servicebus.messaging.queuedescription.autodeleteonidle).

### <a name="duplicate-detection"></a>Deteção de duplicados

Um erro pode fazer com que o cliente tenha uma dúvida sobre o resultado de uma operação de envio. A detecção de duplicidades permite que o remetente reenvie a mesma mensagem. Outra opção é para a fila ou o tópico para descartar cópias duplicadas. Para obter mais informações, consulte [detecção de duplicidades](duplicate-detection.md).

### <a name="security-protocols"></a>Protocolos de segurança
<a name="sas-rbac-and-managed-identities-for-azure-resources"></a>

O Service Bus suporta protocolos de segurança como [Assinaturas de Acesso Partilhado](service-bus-sas.md) (SAS), [Controlo de Acesso Baseado em Funções](authenticate-application.md) (RBAC) e [Identidades geridas para os recursos do Azure](service-bus-managed-service-identity.md).

### <a name="geo-disaster-recovery"></a>Recuperação após desastre geográfica

Quando as regiões ou os data centers do Azure têm experiência com tempo de inatividade, a recuperação de desastres geograficamente permite que o processamento de dados continue operando em uma região ou Datacenter diferente. Para obter mais informações, consulte [recuperação de desastre geográfico do barramento de serviço do Azure](service-bus-geo-dr.md).

### <a name="security"></a>Segurança

O Service Bus suporta os protocolos [AMQP 1.0](service-bus-amqp-overview.md) e [HTTP/REST](/rest/api/servicebus/) padrão.

## <a name="client-libraries"></a>Bibliotecas de cliente

O barramento de serviço dá suporte a bibliotecas de cliente para [.net](https://github.com/Azure/azure-service-bus-dotnet/tree/master), [Java](https://github.com/Azure/azure-service-bus-java/tree/master)e [JMS](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/qpid-jms-client).

## <a name="integration"></a>Integração

O Service Bus integra-se totalmente nos seguintes serviços do Azure:

* [Event Grid](https://azure.microsoft.com/services/event-grid/)
* [Aplicações Lógicas](https://azure.microsoft.com/services/logic-apps/)
* [Funções do Azure](https://azure.microsoft.com/services/functions/)
* [Dynamics 365](https://dynamics.microsoft.com)
* [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)

## <a name="next-steps"></a>Passos seguintes

Para começar a utilizar as mensagens do Service Bus, consulte os seguintes artigos:

* Para comparar os serviços de mensagens do Azure, consulte [comparação de serviços](../event-grid/compare-messaging-services.md?toc=%2fazure%2fservice-bus-messaging%2ftoc.json&bc=%2fazure%2fservice-bus-messaging%2fbreadcrumb%2ftoc.json).
* Experimente os guias de início rápido para [.net](service-bus-dotnet-get-started-with-queues.md), [Java](service-bus-java-how-to-use-queues.md)ou [JMS](service-bus-java-how-to-use-jms-api-amqp.md).
* Para gerenciar os recursos do barramento de serviço, consulte [Gerenciador do barramento de serviço](https://github.com/paolosalvatori/ServiceBusExplorer/releases).
* Para saber mais sobre as camadas Standard e Premium e seus preços, confira [preços do barramento de serviço](https://azure.microsoft.com/pricing/details/service-bus/).
* Para saber mais sobre o desempenho e a latência da camada Premium, consulte [mensagens Premium](https://techcommunity.microsoft.com/t5/Service-Bus-blog/Premium-Messaging-How-fast-is-it/ba-p/370722).
