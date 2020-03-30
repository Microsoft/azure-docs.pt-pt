---
title: Use o hub de eventos da app Apache Kafka - Azure Event Hubs / Microsoft Docs
description: Este artigo fornece informações sobre o apoio de Apache Kafka por Azure Event Hubs.
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 02/12/2020
ms.author: shvija
ms.openlocfilehash: 6dcbf0ad0f6678d892c5c02446cac09b4325384c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80283655"
---
# <a name="use-azure-event-hubs-from-apache-kafka-applications"></a>Use hubs de eventos Azure a partir de aplicações Apache Kafka
O Event Hubs fornece um ponto final de Kafka que pode ser usado pelas suas aplicações baseadas em Kafka como alternativa para executar o seu próprio cluster Kafka. O Event Hubs suporta [o protocolo Apache Kafka 1.0 e mais tarde,](https://kafka.apache.org/documentation/)e trabalha com as suas aplicações kafka existentes, incluindo o MirrorMaker.  

## <a name="what-does-event-hubs-for-kafka-provide"></a>O que os Centros de Eventos para Kafka fornecem?

A funcionalidade Hubs de Eventos para Kafka fornece uma cabeça protocolar em cima de Azure Event Hubs que é binário compatível com as versões Kafka 1.0 e mais tarde para ler e escrever para tópicos kafka. Pode começar a utilizar o ponto final de Kafka a partir das suas aplicações sem alteração de código, mas com uma alteração mínima de configuração. Atualiza a cadeia de ligação em configurações para apontar para o ponto final de Kafka exposto pelo seu centro de eventos em vez de apontar para o seu cluster Kafka. Depois, pode começar a transmitir eventos a partir das suas aplicações que utilizam o protocolo Kafka em Centros de Eventos. Esta integração também suporta quadros como o [Kafka Connect,](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect)que está atualmente em pré-visualização. 

Conceptualmente, kafka e Centros de Eventos são quase idênticos: ambos são troncos divididos construídos para dados de streaming. A tabela seguinte mapeia conceitos entre Kafka e Event Hubs.

### <a name="kafka-and-event-hub-conceptual-mapping"></a>Mapeamento conceptual kafka e event hub

| Conceito de Kafka | Conceito de Hubs de Eventos|
| --- | --- |
| Cluster | Espaço de nomes |
| Tópico | Hub de Eventos |
| Partição | Partição|
| Grupo de Consumidores | Grupo de Consumidores |
| Desvio | Desvio|

### <a name="key-differences-between-kafka-and-event-hubs"></a>Principais diferenças entre Kafka e Centros de Eventos

Enquanto [o Apache Kafka](https://kafka.apache.org/) é um software, que pode supor onde quiser, o Event Hubs é um serviço na nuvem semelhante ao Armazenamento Azure Blob. Não existem servidores ou redes para gerir e não há corretores para configurar. Cria um espaço de nome, que é um FQDN no qual os seus tópicos vivem, e depois cria Hubs de Eventos ou tópicos dentro desse espaço de nome. Para mais informações sobre Centros de Eventos e espaços de nomes, consulte [as funcionalidades do Event Hubs.](event-hubs-features.md#namespace) Como um serviço na nuvem, o Event Hubs usa um único endereço IP virtual estável como ponto final, para que os clientes não precisem de saber sobre os corretores ou máquinas dentro de um cluster. 

A escala em Event Hubs é controlada por quantas unidades de produção você compra, com cada unidade de entrada que o cativa a 1 MB por segundo, ou 1000 eventos por segundo de entrada. Por predefinição, os Centros de Eventos escalam as unidades de produção quando atingiro limite com a função [Auto-Inflate;](event-hubs-auto-inflate.md) esta funcionalidade também trabalha com os Hubs de Eventos para a funcionalidade Kafka. 

### <a name="security-and-authentication"></a>Segurança e autenticação
Sempre que publica ou consome eventos de um Hubs de Eventos para kafka, o seu cliente está a tentar aceder aos recursos do Event Hubs. Pretende garantir que os recursos são acedidos através de uma entidade autorizada. Ao utilizar o protocolo Apache Kafka com os seus clientes, pode definir a sua configuração para autenticação e encriptação utilizando os mecanismos SASL. Ao utilizar os Hubs de Eventos para kafka requer a encriptação TLS (uma vez que todos os dados em trânsito com os Hubs de Eventos são encriptados por TLS). Pode ser feito especificando a opção SASL_SSL no seu ficheiro de configuração. 

O Azure Event Hubs oferece múltiplas opções para autorizar o acesso aos seus recursos seguros. 

- OAuth
- Assinatura de acesso partilhado (SAS)

#### <a name="oauth"></a>OAuth
O Event Hubs integra-se com o Azure Ative Directory (Azure AD), que fornece um servidor de autorização centralizado compatível com o **OAuth** 2.0. Com a Azure AD, pode utilizar o controlo de acesso baseado em papéis (RBAC) para conceder permissões finas de grãos às identidades do seu cliente. Pode utilizar esta funcionalidade com os seus clientes Kafka, especificando **SASL_SSL** para o protocolo e **OAUTHBEARER** para o mecanismo. Para mais detalhes sobre as funções rBAC e os níveis para o acesso à deteção, consulte [Autorizar o acesso com a AD Azure](authorize-access-azure-active-directory.md).

```xml
bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
security.protocol=SASL_SSL
sasl.mechanism=OAUTHBEARER
sasl.jaas.config=org.apache.kafka.common.security.oauthbearer.OAuthBearerLoginModule required;
sasl.login.callback.handler.class=CustomAuthenticateCallbackHandler;
```

#### <a name="shared-access-signature-sas"></a>Assinatura de acesso partilhado (SAS)
O Event Hubs também fornece as Assinaturas de **Acesso Partilhado (SAS)** para acesso delegado a Centros de Eventos para recursos Kafka. A autorização de acesso utilizando o mecanismo baseado em token OAuth 2.0 proporciona uma segurança e facilidade de utilização superiores ao longo do SAS. As funções incorporadas também podem eliminar a necessidade de autorização baseada em ACL, que deve ser mantida e gerida pelo utilizador. Pode utilizar esta funcionalidade com os seus clientes Kafka, especificando **SASL_SSL** para o protocolo e **PLAIN** para o mecanismo. 

```xml
bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

#### <a name="samples"></a>Amostras 
Para um **tutorial** com instruções passo a passo para criar um centro de eventos e acessá-lo usando SAS ou OAuth, consulte [Quickstart: Data streaming with Event Hubs usando o protocolo Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md).

Para mais **amostras** que mostrem como usar OAuth com Hubs de Eventos para Kafka, veja [amostras no GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth).

## <a name="other-event-hubs-features-available-for-kafka"></a>Outras funcionalidades do Event Hubs disponíveis para Kafka

A funcionalidade Hubs de Eventos para Kafka permite-lhe escrever com um protocolo e ler com outro, para que os seus atuais produtores de Kafka possam continuar a publicar via Kafka, e pode adicionar leitores com Hubs de Eventos, como Azure Stream Analytics ou Funções Azure. Além disso, os Centros [Capture](event-hubs-capture-overview.md) de [Geo Disaster-Recovery](event-hubs-geo-dr.md) Eventos também trabalham com os Hubs de Eventos para a funcionalidade Kafka.

## <a name="features-that-are-not-yet-supported"></a>Características que ainda não são suportadas 

Aqui está a lista de características kafka que ainda não são suportadas:

*   Produtor idempotente
*   Transação
*   Compressão
*   Retenção baseada em tamanho
*   Log compaction
*   Adicionar divisórias a um tópico existente
*   APOIO HTTP Kafka API
*   Riachos Kafka

## <a name="next-steps"></a>Passos seguintes

Este artigo forneceu uma introdução aos Centros de Eventos para Kafka. Para saber mais, consulte os seguintes links:

- [Como criar um centro de eventos](event-hubs-create.md)
- [Transmitir em fluxo para os Hubs de Eventos a partir das suas aplicações Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Espelhar um mediador de Kafka num hub de eventos](event-hubs-kafka-mirror-maker-tutorial.md)
- [Ligar o Apache Spark a um hub de eventos](event-hubs-kafka-spark-tutorial.md)
- [Ligar o Apache Flink a um hub de eventos](event-hubs-kafka-flink-tutorial.md)
- [Integrar kafka Connect com um hub de eventos](event-hubs-kafka-connect-tutorial.md)
- [Ligue a Akka Streams a um centro de eventos](event-hubs-kafka-akka-streams-tutorial.md)
- [Explore samples on our GitHub](https://github.com/Azure/azure-event-hubs-for-kafka) (Explorar exemplos no nosso GitHub)


