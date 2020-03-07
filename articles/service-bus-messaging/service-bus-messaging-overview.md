---
title: Descrição geral de mensagens do Service Bus do Azure | Microsoft Docs
description: Este artigo fornece uma visão geral de alto nível do Azure Service Bus, um corretor de mensagens de integração empresarial totalmente gerido.
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
ms.openlocfilehash: 49a54491c36ef29209d1a53094cc5baf57057557
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78396275"
---
# <a name="what-is-azure-service-bus"></a>O que é o Azure Service Bus?

O Microsoft Azure Service Bus é um mediador de mensagens de integração empresarial totalmente gerido. O Service Bus pode dissociar aplicações e serviços. A Service Bus oferece uma plataforma fiável e segura para transferência assíncrona de dados e estado.

Os dados são transferidos entre diferentes aplicações e serviços utilizando *mensagens*. Uma mensagem está em formato binário e pode conter JSON, XML ou apenas texto. Para mais informações, consulte Serviços de [Integração.](https://azure.com/integration)

Alguns cenários comuns de mensagens são:

* *Mensagens*. Transfira dados empresariais, tais como vendas ou compras, revistas ou movimentos de inventário.
* *Desagregar aplicações*. Melhorar a fiabilidade e a escalabilidade das aplicações e serviços. O cliente e o serviço não têm de estar online ao mesmo tempo.
* *Tópicos e subscrições.* Habilitar 1:*n* relações entre editores e assinantes.
* *Sessões de mensagens.* Implementar fluxos de trabalho que exijam pedido de mensagens ou diferimento de mensagem.

## <a name="namespaces"></a>Espaços de nomes

Um espaço de nome é um recipiente para todos os componentes de mensagens. Várias filas e tópicos podem estar num único espaço de nome, e os espaços de nome muitas vezes servem como recipientes de aplicação.

## <a name="queues"></a>Filas

As mensagens são enviadas e recebidas de *filas*. As filas armazenam mensagens até que a aplicação recetora esteja disponível para recebê-las e processá-las.

![Filas](./media/service-bus-messaging-overview/about-service-bus-queue.png)

As mensagens nas filas são encomendadas e marcadas à chegada. Depois de aceite, a mensagem é guardada em segurança no armazenamento redundante. As mensagens são entregues no modo *pull,* apenas entregando mensagens quando solicitadas.

## <a name="topics"></a>Tópicos

Também pode utilizar *tópicos* para enviar e receber mensagens. Embora uma fila seja utilizada frequentemente para comunicação ponto a ponto, os tópicos são úteis em cenários de publicação/subscrição.

![Tópico](./media/service-bus-messaging-overview/about-service-bus-topic.png)

Os tópicos podem ter várias subscrições independentes. Um subscritor de um tópico pode receber uma cópia de cada mensagem enviada para esse tópico. As assinaturas são entidades nomeadas. As subscrições persistem, mas podem expirar ou apagar automaticamente.

Pode não querer que as subscrições individuais recebam todas as mensagens enviadas para um tópico. Em caso afirmativo, pode utilizar *regras* e *filtros* para definir condições que desencadeiem *ações*opcionais . Pode filtrar mensagens especificadas e definir ou modificar propriedades de mensagem. Para mais informações, consulte [os filtros e ações](topic-filters.md)do Tópico.

## <a name="advanced-features"></a>Funcionalidades avançadas

O Service Bus inclui funcionalidades avançadas que lhe permitem resolver problemas de mensagens mais complexos. As seguintes secções descrevem várias destas funcionalidades.

### <a name="message-sessions"></a>Sessões de mensagens

Para criar uma garantia de primeira in,first-out (FIFO) no Service Bus, utilize sessões. As sessões de mensagens permitem o manuseamento conjunto e ordenado de sequências não limitadas de mensagens relacionadas. Para mais informações, consulte [as sessões de mensagem: primeiro a entrar, primeiro a sair (FIFO)](message-sessions.md).

### <a name="autoforwarding"></a>Auto-encaminhamento

O auto-reencaminhamento de recursos acorrenta uma fila ou subscrição para outra fila ou tópico. Devem fazer parte do mesmo espaço de nome. Com o auto-reencaminhamento, o Service Bus remove automaticamente as mensagens de uma fila ou subscrição e coloca-as numa fila ou tópico diferente. Para mais informações, consulte as entidades de Ônibus de Serviço de [Cadeia com reencaminhamento automático](service-bus-auto-forwarding.md).

### <a name="dead-letter-queue"></a>Fila de letras mortas

O Ônibus de Serviço suporta uma fila de cartas mortas (DLQ). Um DLQ contém mensagens que não podem ser entregues a nenhum recetor. Contém mensagens que não podem ser processadas. O Service Bus permite-lhe remover mensagens do DLQ e inspecioná-las. Para mais informações, consulte a [visão geral das filas de cartas mortas do Autocarro de Serviço.](service-bus-dead-letter-queues.md)

### <a name="scheduled-delivery"></a>Entrega agendada

Pode enviar mensagens para uma fila ou tópico para processamento atrasado. Pode agendar um trabalho para se tornar disponível para processamento por um sistema a certa hora. Para mais informações, consulte [as mensagens agendadas](message-sequencing.md#scheduled-messages).

### <a name="message-deferral"></a>Diferimento de mensagens

Um cliente de fila ou subscrição pode adiar a recuperação de uma mensagem até uma hora posterior. Este adiamento pode ser devido a circunstâncias especiais no pedido. A mensagem permanece na fila ou subscrição, mas está reservada. Para mais informações, consulte o [adiamento da mensagem.](message-deferral.md)

### <a name="batching"></a>Lotes

O lote do lado do cliente permite que um cliente de fila ou tópico atrase o envio de uma mensagem por um determinado período de tempo. Se o cliente enviar mensagens adicionais durante este período de tempo, transmitirá as mensagens num único lote. Para mais informações, consulte o [lote do lado do Cliente](service-bus-performance-improvements.md#client-side-batching).

### <a name="transactions"></a>Transações

Uma transação agrupa duas ou mais operações em conjunto num âmbito de *execução.* A Service Bus apoia operações de agrupamento contra uma única entidade de mensagens no âmbito de uma única transação. Uma entidade de mensagem pode ser uma fila, tópico ou subscrição. Para mais informações, consulte [a visão geral do processamento de transações de ônibus de serviço.](service-bus-transactions.md)

### <a name="filtering-and-actions"></a>Filtragem e ações

Os subscritores podem definir as mensagens que pretendem receber de um tópico. Estas mensagens são especificadas sob a forma de uma ou mais regras de subscrição nomeadas. Para cada condição de regra correspondente, a subscrição produz uma cópia da mensagem, que pode ser anotada de forma diferente para cada regra correspondente. Para mais informações, consulte [os filtros e ações](topic-filters.md)do Tópico.

### <a name="autodelete-on-idle"></a>Autodelete em ocioso

A eliminação automática no ocioso permite especificar um intervalo de marcha lenta após o qual uma fila é automaticamente eliminada. A duração mínima é 5 minutos. Para mais informações, consulte a [propriedade QueueDescription.AutoDeleteOnIdle](/dotnet/api/microsoft.servicebus.messaging.queuedescription.autodeleteonidle).

### <a name="duplicate-detection"></a>Deteção de duplicados

Um erro pode levar o cliente a ter dúvidas sobre o resultado de uma operação de envio. A deteção duplicada permite ao remetente reenviar a mesma mensagem. Outra opção é que a fila ou tópico elimine quaisquer cópias duplicadas. Para mais informações, consulte [a deteção do Duplicate](duplicate-detection.md).

### <a name="security-protocols"></a>Protocolos de segurança
<a name="sas-rbac-and-managed-identities-for-azure-resources"></a>

O Service Bus suporta protocolos de segurança como [Assinaturas de Acesso Partilhado](service-bus-sas.md) (SAS), [Controlo de Acesso Baseado em Funções](authenticate-application.md) (RBAC) e [Identidades geridas para os recursos do Azure](service-bus-managed-service-identity.md).

### <a name="geo-disaster-recovery"></a>Recuperação após desastre geográfica

Quando as regiões do Azure ou datacenters experimentam o tempo de inatividade, a recuperação de geo-desastres permite que o processamento de dados continue a operar numa região ou datacenter diferente. Para mais informações, consulte [a recuperação geo-desastre do Azure Service Bus.](service-bus-geo-dr.md)

### <a name="security"></a>Segurança

O Service Bus suporta os protocolos [AMQP 1.0](service-bus-amqp-overview.md) e [HTTP/REST](/rest/api/servicebus/) padrão.

## <a name="client-libraries"></a>Bibliotecas de cliente

Service Bus suporta bibliotecas de clientes para [.NET,](https://github.com/Azure/azure-service-bus-dotnet/tree/master) [Java](https://github.com/Azure/azure-service-bus-java/tree/master)e [JMS](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/qpid-jms-client).

## <a name="integration"></a>Integração

O Service Bus integra-se totalmente nos seguintes serviços do Azure:

* [Event Grid](https://azure.microsoft.com/services/event-grid/)
* [Aplicações Lógicas](https://azure.microsoft.com/services/logic-apps/)
* [Funções do Azure](https://azure.microsoft.com/services/functions/)
* [Dynamics 365](https://dynamics.microsoft.com)
* [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)

## <a name="next-steps"></a>Passos seguintes

Para começar a utilizar as mensagens do Service Bus, consulte os seguintes artigos:

* Para comparar os serviços de mensagens Azure, consulte [A Comparação de serviços.](../event-grid/compare-messaging-services.md?toc=%2fazure%2fservice-bus-messaging%2ftoc.json&bc=%2fazure%2fservice-bus-messaging%2fbreadcrumb%2ftoc.json)
* Experimente as quickstarts para [.NET,](service-bus-dotnet-get-started-with-queues.md) [Java](service-bus-java-how-to-use-queues.md)ou [JMS](service-bus-java-how-to-use-jms-api-amqp.md).
* Para gerir os recursos do Ônibus de serviço, consulte [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/releases).
* Para saber mais sobre os níveis Standard e Premium e seus preços, consulte [os preços dos Autocarros de Serviço.](https://azure.microsoft.com/pricing/details/service-bus/)
* Para aprender sobre desempenho e latência para o nível Premium, consulte [A Mensagem Premium](https://techcommunity.microsoft.com/t5/Service-Bus-blog/Premium-Messaging-How-fast-is-it/ba-p/370722).
