---
title: Usar o Hub de eventos de Apache Kafka aplicativo – hubs de eventos do Azure | Microsoft Docs
description: Este artigo fornece informações sobre suporte Apache Kafka pelos hubs de eventos do Azure.
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: d3271d6e8cb7d423e1dccd235b244688e7ab5683
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555784"
---
# <a name="use-azure-event-hubs-from-apache-kafka-applications"></a>Usar os hubs de eventos do Azure de aplicativos Apache Kafka
Os hubs de eventos fornecem um ponto de extremidade Kafka que pode ser usado por seus aplicativos existentes baseados em Kafka como uma alternativa para executar seu próprio cluster Kafka. Os hubs de eventos dão suporte ao [protocolo Apache Kafka 1,0 e posterior](https://kafka.apache.org/documentation/)e funcionam com seus aplicativos Kafka existentes, incluindo MirrorMaker.  

## <a name="what-does-event-hubs-for-kafka-provide"></a>O que os hubs de eventos para Kafka fornecem?

O recurso hubs de eventos para Kafka fornece um cabeçalho de protocolo sobre os hubs de eventos do Azure que é compatível binário com as versões 1,0 e posteriores para ler e gravar em tópicos do Kafka. Você pode começar a usar o ponto de extremidade Kafka de seus aplicativos sem alteração de código, mas uma alteração mínima de configuração. Você atualiza a cadeia de conexão em configurações para apontar para o ponto de extremidade Kafka exposto pelo hub de eventos em vez de apontar para o cluster Kafka. Em seguida, você pode iniciar os eventos de streaming de seus aplicativos que usam o protocolo Kafka nos hubs de eventos. Essa integração também oferece suporte a estruturas como o [Kafka Connect](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect), que está atualmente em versão prévia. 

Os hubs de eventos e Kafka conceitualmente são quase idênticos: são ambos logs particionados criados para streaming de dados. A tabela a seguir mapeia os conceitos entre Kafka e hubs de eventos.

### <a name="kafka-and-event-hub-conceptual-mapping"></a>Kafka e mapeamento conceitual do hub de eventos

| Conceito de Kafka | Conceito de hubs de eventos|
| --- | --- |
| Cluster | Namespace |
| Tópico | Hub de Eventos |
| Partição | Partição|
| Grupo de consumidores | Grupo de consumidores |
| Desvio | Desvio|

### <a name="key-differences-between-kafka-and-event-hubs"></a>Principais diferenças entre Kafka e hubs de eventos

Embora [Apache Kafka](https://kafka.apache.org/) seja um software, que pode ser executado onde quer que você escolha, os hubs de eventos são um serviço de nuvem semelhante ao armazenamento de BLOBs do Azure. Não há servidores ou redes para gerenciar e não há agentes para configurar. Você cria um namespace, que é um FQDN no qual seus tópicos residem e, em seguida, cria hubs de eventos ou tópicos dentro desse namespace. Para obter mais informações sobre os namespaces e hubs de eventos, consulte [recursos de hubs de eventos](event-hubs-features.md#namespace). Como um serviço de nuvem, os hubs de eventos usam um único endereço IP virtual estável como o ponto de extremidade, para que os clientes não precisem saber sobre os agentes ou computadores em um cluster. 

A escala nos hubs de eventos é controlada por quantas unidades de taxa de transferência você compra, com cada unidade de produtividade entitling a 1 MB por segundo ou 1000 eventos por segundo de entrada. Por padrão, os hubs de eventos dimensionam as unidades de produtividade quando você atinge seu limite com o recurso de [inflar automaticamente](event-hubs-auto-inflate.md) ; Esse recurso também funciona com os hubs de eventos para o recurso Kafka. 

### <a name="security-and-authentication"></a>Segurança e autenticação

Os hubs de eventos do Azure exigem SSL ou TLS para toda a comunicação e usam SAS (assinaturas de acesso compartilhado) para autenticação. Esse requisito também é verdadeiro para um ponto de extremidade Kafka nos hubs de eventos. Para compatibilidade com o Kafka, os hubs de eventos usam SASL PLAIN para autenticação e o SSL SASL para segurança de transporte. Para obter mais informações sobre segurança em hubs de eventos, consulte [autenticação e segurança de hubs de eventos](event-hubs-authentication-and-security-model-overview.md).

## <a name="other-event-hubs-features-available-for-kafka"></a>Outros recursos dos hubs de eventos disponíveis para Kafka

O recurso hubs de eventos para Kafka permite que você grave com um protocolo e leia com outro, para que seus produtores Kafka atuais possam continuar publicando via Kafka e você pode adicionar leitores com hubs de eventos, como Azure Stream Analytics ou Azure Functions. Além disso, os recursos dos hubs de eventos, como [captura](event-hubs-capture-overview.md) e [recuperação de desastres geográficos](event-hubs-geo-dr.md) , também funcionam com os hubs de eventos para o recurso Kafka.

## <a name="features-that-are-not-yet-supported"></a>Recursos que ainda não têm suporte 

Aqui está a lista de recursos do Kafka que ainda não têm suporte:

*   Produtor idempotente
*   aciona
*   Compressão
*   Retenção baseada em tamanho
*   Compactação de log
*   Adicionando partições a um tópico existente
*   Suporte à API HTTP Kafka
*   Fluxos de Kafka

## <a name="next-steps"></a>Passos seguintes

Este artigo forneceu uma introdução aos hubs de eventos para Kafka. Para saber mais, confira os links a seguir:

- [How to create Kafka enabled Event Hubs](event-hubs-create-kafka-enabled.md) (Como criar os Hubs de Eventos ativados para Kafka)
- [Transmitir em fluxo para os Hubs de Eventos a partir das suas aplicações Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Mirror a Kafka broker in a Kafka-enabled event hub](event-hubs-kafka-mirror-maker-tutorial.md) (Espelhar um mediador de Kafka num hub de eventos com Kafka ativado)
- [Connect Apache Spark to a Kafka-enabled event hub](event-hubs-kafka-spark-tutorial.md) (Ligar o Apache Spark a um hub de eventos com Kafka ativado)
- [Connect Apache Spark to a Kafka-enabled event hub](event-hubs-kafka-flink-tutorial.md) (Ligar o Apache Flink a um hub de eventos com Kafka ativado)
- [Integrate Kafka Connect with a Kafka-enabled event hub](event-hubs-kafka-connect-tutorial.md) (Integrar o Kafka Connect num hub de eventos com Kafka ativado)
- [Connect Akka Streams to a Kafka-enabled event hub](event-hubs-kafka-akka-streams-tutorial.md) (Ligar o Akka Streams a um hub de eventos com Kafka ativado)
- [Explore samples on our GitHub](https://github.com/Azure/azure-event-hubs-for-kafka) (Explorar exemplos no nosso GitHub)


