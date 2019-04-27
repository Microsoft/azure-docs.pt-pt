---
title: Hub de eventos de utilização da aplicação do Apache Kafka - Event Hubs do Azure | Documentos da Microsoft
description: Este artigo fornece informações sobre o suporte do Apache Kafka ao Event Hubs do Azure.
services: event-hubs
documentationcenter: .net
author: shvija
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: bahariri
ms.openlocfilehash: 8bf381e7c66e06bbaa140ed865f0f7c9b4f001af
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60821704"
---
# <a name="use-azure-event-hubs-from-apache-kafka-applications"></a>Utilizar os Hubs de eventos do Azure a partir de aplicações do Apache Kafka
Os Hubs de eventos fornece um ponto de extremidade do Kafka que pode ser utilizado por já existentes Kafka com base em aplicações como uma alternativa à execução de seu próprio cluster do Kafka. Os Hubs de eventos suportam [protocolo do Apache Kafka 1.0 e posterior](https://kafka.apache.org/documentation/)e funciona com seus aplicativos existentes do Kafka, incluindo o MirrorMaker.  

## <a name="what-does-event-hubs-for-kafka-provide"></a>O que o Hubs de eventos para o Kafka fornece?

Os Hubs de eventos para a funcionalidade de Kafka fornece uma vantagem de protocolo na parte superior dos Hubs de eventos do Azure que é compatível com o binário com versões do Kafka 1.0 e, mais tarde para de leitura e gravação para tópicos do Kafka. Pode começar a utilizar o ponto de extremidade do Kafka de seus aplicativos com nenhuma alteração de código, mas uma alteração de configuração mínima. Atualizar a cadeia de ligação em configurações para apontar para o ponto de extremidade do Kafka exposto pelo seu hub de eventos em vez de apontar para o cluster do Kafka. Em seguida, pode começar a transmissão em fluxo de eventos a partir das suas aplicações que utilizam o protocolo de Kafka nos Hubs de eventos. Esta integração também suporta estruturas como o [Kafka ligar](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect), que está atualmente em pré-visualização. 

Conceitualmente Kafka e os Hubs de eventos são quase idênticos: são os dois registos particionados criados para dados de transmissão em fluxo. A tabela seguinte mapeia conceitos entre Kafka e os Hubs de eventos.

### <a name="kafka-and-event-hub-conceptual-mapping"></a>Mapeamento de conceitual Kafka e o Hub de eventos

| Conceito de Kafka | Conceito de Hubs de eventos|
| --- | --- |
| Cluster | Espaço de Nomes |
| Tópico | Hub de Eventos |
| Partição | Partição|
| Grupo de Consumidores | Grupo de Consumidores |
| Desvio | Desvio|

### <a name="key-differences-between-kafka-and-event-hubs"></a>Principais diferenças entre o Kafka e os Hubs de eventos

Embora [Apache Kafka](https://kafka.apache.org/) é um software, que pode ser executada sempre que escolher, os Event Hubs são um serviço em nuvem semelhante ao armazenamento de Blobs do Azure. Existem não existem servidores ou redes para gerir e não mediadores para configurar. Criar um espaço de nomes, o que é um FQDN no qual seus tópicos em direto, e, em seguida, criar Hubs de eventos ou tópicos dentro desse namespace. Para obter mais informações sobre os Hubs de eventos e espaços de nomes, consulte [funcionalidades dos Hubs de eventos](event-hubs-features.md#namespace). Como um serviço cloud, os Event Hubs utilizam um único endereço IP virtual estável como o ponto final, para que os clientes não precisam saber sobre os corretores ou máquinas de um cluster. 

Escala nos Hubs de eventos é controlada pelas unidades de débito quantos comprar, com cada unidade de débito entitling 1 MB por segundo ou 1000 eventos por segundo de entrada. Por predefinição, os Hubs de eventos verticalmente as unidades de débito quando atingir o limite com o [ampliação automática](event-hubs-auto-inflate.md) funcionalidade; essa funcionalidade também funciona com os Hubs de eventos para a funcionalidade do Kafka. 

### <a name="security-and-authentication"></a>Segurança e autenticação

Os Hubs de eventos do Azure requer SSL ou TLS para todas as comunicações e utiliza assinaturas de acesso partilhado (SAS) para autenticação. Este requisito também é verdadeiro para um ponto de extremidade do Kafka dentro dos Hubs de eventos. Para compatibilidade com o Kafka, os Event Hubs utilizam simples do SASL para autenticação e SSL do SASL para a segurança de transporte. Para obter mais informações sobre a segurança nos Hubs de eventos, consulte [os Hubs de eventos de autenticação e segurança](event-hubs-authentication-and-security-model-overview.md).

## <a name="other-event-hubs-features-available-for-kafka"></a>Outros recursos de Hubs de eventos disponíveis para o Kafka

Os Hubs de eventos para a funcionalidade de Kafka permite-lhe escrever com um protocolo e ler com outra, para que os produtores de Kafka atuais podem continuar a publicar através do Kafka, e pode adicionar os leitores com os Hubs de eventos, como o Azure Stream Analytics ou as funções do Azure. Além disso, os recursos de Hubs de eventos, como [capturar](event-hubs-capture-overview.md) e [recuperação após desastre geográfico](event-hubs-geo-dr.md) também funcionam com os Hubs de eventos para a funcionalidade do Kafka.

## <a name="features-that-are-not-yet-supported"></a>Funcionalidades que ainda não são suportadas 

Eis a lista de recursos de Kafka que ainda não são suportadas:

*   Produtor de Idempotentes
*   Transação
*   Compressão
*   Retenção com base no tamanho
*   Compactação de registo
*   Adicionar partições para um tópico existente
*   Suporte de API do HTTP Kafka
*   Fluxos do Kafka

## <a name="next-steps"></a>Passos Seguintes

Este artigo fornecido uma introdução aos Hubs de eventos para o Kafka. Para obter mais informações, consulte as seguintes ligações:

- [How to create Kafka enabled Event Hubs](event-hubs-create-kafka-enabled.md) (Como criar os Hubs de Eventos ativados para Kafka)
- [Transmitir em fluxo para os Hubs de Eventos a partir das suas aplicações Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Mirror a Kafka broker in a Kafka-enabled event hub](event-hubs-kafka-mirror-maker-tutorial.md) (Espelhar um mediador de Kafka num hub de eventos com Kafka ativado)
- [Connect Apache Spark to a Kafka-enabled event hub](event-hubs-kafka-spark-tutorial.md) (Ligar o Apache Spark a um hub de eventos com Kafka ativado)
- [Connect Apache Spark to a Kafka-enabled event hub](event-hubs-kafka-flink-tutorial.md) (Ligar o Apache Flink a um hub de eventos com Kafka ativado)
- [Integrate Kafka Connect with a Kafka-enabled event hub](event-hubs-kafka-connect-tutorial.md) (Integrar o Kafka Connect num hub de eventos com Kafka ativado)
- [Connect Akka Streams to a Kafka-enabled event hub](event-hubs-kafka-akka-streams-tutorial.md) (Ligar o Akka Streams a um hub de eventos com Kafka ativado)
- [Explore samples on our GitHub](https://github.com/Azure/azure-event-hubs-for-kafka) (Explorar exemplos no nosso GitHub)


