---
title: Descrição geral de mensagens do Service Bus do Azure | Microsoft Docs
description: Este artigo fornece uma visão geral de alto nível da Azure Service Bus, um corretor de mensagens de integração empresarial totalmente gerido.
ms.topic: overview
ms.date: 11/20/2020
ms.openlocfilehash: febb25474f84819b0afc9ab1f9af96e93489ab54
ms.sourcegitcommit: 1d366d72357db47feaea20c54004dc4467391364
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2020
ms.locfileid: "95415291"
---
# <a name="what-is-azure-service-bus"></a>O que é o Azure Service Bus?

O Microsoft Azure Service Bus é um mediador de mensagens de integração empresarial totalmente gerido. A Service Bus pode dissociar aplicações e serviços. A Service Bus oferece uma plataforma fiável e segura para transferência assíncronea de dados e estado.

Os dados são transferidos entre diferentes aplicações e serviços utilizando *mensagens*. Uma mensagem está em formato binário e pode conter JSON, XML ou apenas texto. Para mais informações, consulte [os Serviços de Integração.](https://azure.com/integration)

Alguns cenários comuns de mensagens são:

* *Mensagens.* Transfira dados empresariais, tais como encomendas de vendas ou compras, revistas ou movimentos de inventário.
* *Desaconsição de candidaturas.* Melhorar a fiabilidade e escalabilidade das aplicações e serviços. O cliente e o serviço não têm de estar online ao mesmo tempo.
* *Tópicos e subscrições.* Ativar 1:*n* relações entre editores e assinantes.
* *Sessões de mensagens*. Implementar fluxos de trabalho que requerem pedido de mensagem ou adiamento de mensagens.

## <a name="namespaces"></a>Espaços de nomes

Um espaço de nome é um recipiente para todos os componentes de mensagens. Várias filas e tópicos podem estar num espaço com um único nome, e os espaços de nomes muitas vezes servem como recipientes de aplicação.

## <a name="queues"></a>Filas

As mensagens são enviadas e recebidas de *filas*. As filas armazenam mensagens até que a aplicação recetora esteja disponível para as receber e processar.

![Fila](./media/service-bus-messaging-overview/about-service-bus-queue.png)

As mensagens nas filas são encomendadas e anotados à chegada. Depois de aceite, a mensagem é guardada em segurança no armazenamento redundante. As mensagens são entregues no modo *de puxar,* apenas entregando mensagens quando solicitadas.

## <a name="topics"></a>Tópicos

Também pode utilizar *tópicos* para enviar e receber mensagens. Embora uma fila seja utilizada frequentemente para comunicação ponto a ponto, os tópicos são úteis em cenários de publicação/subscrição.

![Tópico](./media/service-bus-messaging-overview/about-service-bus-topic.png)

Os tópicos podem ter várias subscrições independentes. Um subscritor de um tópico pode receber uma cópia de cada mensagem enviada para esse tópico. As assinaturas são entidades nomeadas. As subscrições persistem, mas podem expirar ou autodelete.

Pode não querer que as subscrições individuais recebam todas as mensagens enviadas para um tópico. Em caso afirmativo, pode utilizar *regras* e *filtros* para definir condições que desencadeiem *ações* opcionais . Pode filtrar mensagens especificadas e definir ou modificar as propriedades da mensagem. Para mais informações, consulte [filtros e ações tópicos.](topic-filters.md)

## <a name="advanced-features"></a>Funcionalidades avançadas

O Service Bus inclui funcionalidades avançadas que lhe permitem resolver problemas de mensagens mais complexos. As seguintes secções descrevem várias destas características.

### <a name="message-sessions"></a>Sessões de mensagens

Para criar uma garantia de primeira e primeira saída (FIFO) no Service Bus, utilize sessões. As sessões de mensagens permitem o manuseamento conjunto e ordenado de sequências não vinculadas de mensagens relacionadas. Para obter mais informações, consulte [as sessões de mensagem: primeiro a entrar, a sair primeiro (FIFO)](message-sessions.md).

### <a name="autoforwarding"></a>Autoforwarding

A funcionalidade de auto-ordenação acordam uma fila ou subscrição de outra fila ou tópico. Devem fazer parte do mesmo espaço de identificação. Com a auto-ordenação, o Service Bus remove automaticamente as mensagens de uma fila ou subscrição e coloca-as numa fila ou tópico diferente. Para mais informações, consulte [as entidades chaining Service Bus com autoforwarding.](service-bus-auto-forwarding.md)

### <a name="dead-letter-queue"></a>Fila de cartas mortas

A Service Bus suporta uma fila de letras mortas (DLQ). Um DLQ contém mensagens que não podem ser entregues a nenhum recetor. Contém mensagens que não podem ser processadas. O Service Bus permite-lhe remover mensagens do DLQ e inspecioná-las. Para mais informações, consulte [as filas de cartas mortas do Service Bus.](service-bus-dead-letter-queues.md)

### <a name="scheduled-delivery"></a>Entrega agendada

Pode enviar mensagens para uma fila ou tópico para processamento atrasado. Pode agendar um trabalho para se tornar disponível para processamento por um sistema a uma determinada hora. Para mais informações, consulte [mensagens agendadas.](message-sequencing.md#scheduled-messages)

### <a name="message-deferral"></a>Diferimento de mensagens

Uma fila ou cliente por subscrição pode adiar a recuperação de uma mensagem até uma hora posterior. Este adiamento pode ser devido a circunstâncias especiais no pedido. A mensagem permanece na fila ou subscrição, mas está reservada. Para mais informações, consulte [o adiamento da mensagem.](message-deferral.md)

### <a name="batching"></a>Lotes

A criação de lotes do lado do cliente permite que um cliente de fila ou tópico adie o envio de uma mensagem por determinado período de tempo. Se o cliente enviar mensagens adicionais durante este período de tempo, transmitirá as mensagens num único lote. Para obter mais informações, consulte [o lote do lado do Cliente.](service-bus-performance-improvements.md#client-side-batching)

### <a name="transactions"></a>Transações

Uma transação agrupo duas ou mais operações juntas num *âmbito de execução.* A Service Bus suporta operações de agrupamento contra uma única entidade de mensagens no âmbito de uma única transação. Uma entidade de mensagens pode ser uma fila, tópico ou subscrição. Para mais informações, consulte [a visão geral do processamento de transações de service bus.](service-bus-transactions.md)

### <a name="filtering-and-actions"></a>Filtragem e ações

Os subscritores podem definir as mensagens que pretendem receber de um tópico. Estas mensagens são especificadas na forma de uma ou mais regras de subscrição denominadas. Para cada condição de regra de correspondência, a subscrição produz uma cópia da mensagem, que pode ser anotada de forma diferente para cada regra correspondente. Para mais informações, consulte [filtros e ações tópicos.](topic-filters.md)

### <a name="autodelete-on-idle"></a>Autodelete em marcha lenta

A autodelete em marcha lenta permite especificar um intervalo de marcha lenta após o qual uma fila é automaticamente eliminada. A duração mínima é 5 minutos. Para mais informações, consulte a [Propriedade QueueDescription.AutoDeleteOnIdle](/dotnet/api/microsoft.servicebus.messaging.queuedescription.autodeleteonidle).

### <a name="duplicate-detection"></a>Deteção de duplicados

Um erro pode levar o cliente a ter dúvidas sobre o resultado de uma operação de envio. A deteção duplicada permite ao remetente reensi para reenso a mesma mensagem. Outra opção é que a fila ou o tópico deitam fora quaisquer cópias duplicadas. Para obter mais informações, consulte [a deteção de Duplicado.](duplicate-detection.md)

### <a name="geo-disaster-recovery"></a>Recuperação após desastre geográfica

Quando as regiões do Azure ou centros de dados experimentam tempo de inatividade, a funcionalidade de recuperação de desastres geo-distribuída permite que o processamento de dados continue a operar numa região ou centro de dados diferente. Para mais informações, consulte [a recuperação de geo-desastres de autocarros da Azure Service.](service-bus-geo-dr.md)

### <a name="security"></a>Segurança

A Service Bus suporta os protocolos [padrão AMQP 1.0](service-bus-amqp-overview.md) e [HTTP/REST](/rest/api/servicebus/) e respetivas instalações de segurança, incluindo a segurança do nível de transporte (TLS). Os clientes podem ser autorizados a aceder através do modelo de [Assinatura de Acesso Partilhado de](service-bus-sas.md) Service Bus ou com segurança baseada em funções do [Azure Ative Directory,](service-bus-authentication-and-authorization.md) utilizando contas de serviço regulares ou identidades geridas pela Azure. 

Para proteção contra tráfego indesejado, o Service Bus fornece uma gama de [funcionalidades](network-security.md)de segurança de rede , incluindo uma firewall de filtragem IP e integração com a Azure e redes virtuais no local.

## <a name="client-libraries"></a>Bibliotecas de cliente

As bibliotecas de clientes do Service Bus totalmente suportadas estão disponíveis através do Azure SDK.

- [Ônibus de serviço Azure para .NET](https://docs.microsoft.com/dotnet/api/overview/azure/service-bus?view=azure-dotnet&preserve-view=true)
- [Bibliotecas de ônibus de serviço Azure para Java](https://docs.microsoft.com/java/api/overview/azure/servicebus?view=azure-java-stable&preserve-view=true)
- [Fornecedor de ônibus de serviço Azure para Java JMS 2.0](how-to-use-java-message-service-20.md)
- [Módulos de ônibus de serviço Azure para JavaScript e TypeScript](https://docs.microsoft.com/javascript/api/overview/azure/service-bus?view=azure-node-latest&preserve-view=true)
- [Bibliotecas de ônibus de serviço Azure para Python](https://docs.microsoft.com/python/api/overview/azure/servicebus?view=azure-python&preserve-view=true)

[O protocolo principal da Azure Service Bus é AMQP 1.0](service-bus-amqp-overview.md) e pode ser usado a partir de qualquer cliente protocolo compatível com AMQP 1.0. Vários clientes amqp de código aberto têm amostras que demonstram explicitamente a interoperabilidade do Service Bus. Por favor, reveja o [guia de protocolo AMQP 1.0](service-bus-amqp-protocol-guide.md) para entender como usar as funcionalidades do Service Bus com clientes AMQP 1.0 diretamente.

| Idioma | Biblioteca |
| --- | --- |
| Java | [Apache Qpid Protão-J](https://qpid.apache.org/proton/index.html) |
| C/C++ |[Azure uAMQP C](https://github.com/azure/azure-uamqp-c/), [Apache Qpid Proton-C](https://qpid.apache.org/proton/index.html) |
| Python |[Azure uAMQP para Python](https://github.com/azure/azure-uamqp-python/), [Apache Qpid Proton Python](https://qpid.apache.org/releases/qpid-proton-0.32.0/proton/python/docs/overview.html) |
| PHP | [Azure uAMQP para PHP](https://github.com/vsouz4/azure-uamqp-php/) |
| Ruby | [Apache Qpid Proton Ruby](https://github.com/apache/qpid-proton/tree/master/ruby) |
| Ir | [Azure Go AMQP](https://github.com/Azure/go-amqp), [Apache Qpid Proton Go](https://github.com/apache/qpid-proton/tree/master/go/examples)
| C#/F#/VB | [AMQP .NET Lite,](https://github.com/Azure/amqpnetlite) [Apache NMS AMQP](https://github.com/apache/activemq-nms-amqp)|
| JavaScript/Nó | [Rio Rhea](https://github.com/grs/rhea) |

## <a name="integration"></a>Integração

A Service Bus integra-se totalmente com muitos serviços microsoft e Azure, por exemplo:

* [Event Grid](https://azure.microsoft.com/services/event-grid/)
* [Logic Apps](https://azure.microsoft.com/services/logic-apps/)
* [Funções do Azure](https://azure.microsoft.com/services/functions/)
* [Power Platform](https://powerplatform.microsoft.com/)
* [Dynamics 365](https://dynamics.microsoft.com)
* [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)

## <a name="next-steps"></a>Próximos passos

Para começar a utilizar as mensagens do Service Bus, consulte os seguintes artigos:

* Para comparar os serviços de mensagens Azure, consulte [Comparação de serviços.](../event-grid/compare-messaging-services.md?toc=%2fazure%2fservice-bus-messaging%2ftoc.json&bc=%2fazure%2fservice-bus-messaging%2fbreadcrumb%2ftoc.json)
* Experimente os quickstarts para [.NET,](service-bus-dotnet-get-started-with-queues.md) [Java](service-bus-java-how-to-use-queues.md)ou [JMS](service-bus-java-how-to-use-jms-api-amqp.md).
* Para gerir os recursos do Service Bus, consulte [o Service Bus Explorer.](https://github.com/paolosalvatori/ServiceBusExplorer/releases)
* Para saber mais sobre os níveis Standard e Premium e seus preços, consulte [os preços do Service Bus.](https://azure.microsoft.com/pricing/details/service-bus/)
* Para saber sobre desempenho e latência para o nível Premium, consulte [Mensagens Premium](https://techcommunity.microsoft.com/t5/Service-Bus-blog/Premium-Messaging-How-fast-is-it/ba-p/370722).
