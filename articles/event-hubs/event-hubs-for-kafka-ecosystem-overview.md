---
title: Hub de eventos de utilização da aplicação do Apache Kafka - Event Hubs do Azure | Documentos da Microsoft
description: Este artigo fornece informações sobre o suporte do Apache Kafka ao Event Hubs do Azure.
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 02/12/2020
ms.author: shvija
ms.openlocfilehash: fc81226e754178ad0edfff96a494dd7522662261
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79264898"
---
# <a name="use-azure-event-hubs-from-apache-kafka-applications"></a>Utilizar os Hubs de eventos do Azure a partir de aplicações do Apache Kafka
Os Hubs de eventos fornece um ponto de extremidade do Kafka que pode ser utilizado por já existentes Kafka com base em aplicações como uma alternativa à execução de seu próprio cluster do Kafka. O Event Hubs suporta [o protocolo Apache Kafka 1.0 e mais tarde,](https://kafka.apache.org/documentation/)e trabalha com as suas aplicações kafka existentes, incluindo o MirrorMaker.  

## <a name="what-does-event-hubs-for-kafka-provide"></a>O que o Hubs de eventos para o Kafka fornece?

Os Hubs de eventos para a funcionalidade de Kafka fornece uma vantagem de protocolo na parte superior dos Hubs de eventos do Azure que é compatível com o binário com versões do Kafka 1.0 e, mais tarde para de leitura e gravação para tópicos do Kafka. Pode começar a utilizar o ponto de extremidade do Kafka de seus aplicativos com nenhuma alteração de código, mas uma alteração de configuração mínima. Atualizar a cadeia de ligação em configurações para apontar para o ponto de extremidade do Kafka exposto pelo seu hub de eventos em vez de apontar para o cluster do Kafka. Em seguida, pode começar a transmissão em fluxo de eventos a partir das suas aplicações que utilizam o protocolo de Kafka nos Hubs de eventos. Esta integração também suporta quadros como o [Kafka Connect,](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect)que está atualmente em pré-visualização. 

Conceptualmente, kafka e Centros de Eventos são quase idênticos: ambos são troncos divididos construídos para dados de streaming. A tabela seguinte mapeia conceitos entre Kafka e os Hubs de eventos.

### <a name="kafka-and-event-hub-conceptual-mapping"></a>Mapeamento de conceitual Kafka e o Hub de eventos

| Conceito de Kafka | Conceito de Hubs de eventos|
| --- | --- |
| Cluster | Espaço de nomes |
| Tópico | Hub de Eventos |
| Partição | Partição|
| Grupo de Consumidores | Grupo de Consumidores |
| Desvio | Desvio|

### <a name="key-differences-between-kafka-and-event-hubs"></a>Principais diferenças entre o Kafka e os Hubs de eventos

Enquanto [o Apache Kafka](https://kafka.apache.org/) é um software, que pode supor onde quiser, o Event Hubs é um serviço na nuvem semelhante ao Armazenamento Azure Blob. Existem não existem servidores ou redes para gerir e não mediadores para configurar. Criar um espaço de nomes, o que é um FQDN no qual seus tópicos em direto, e, em seguida, criar Hubs de eventos ou tópicos dentro desse namespace. Para mais informações sobre Centros de Eventos e espaços de nomes, consulte [as funcionalidades do Event Hubs.](event-hubs-features.md#namespace) Como um serviço na nuvem, o Event Hubs usa um único endereço IP virtual estável como ponto final, para que os clientes não precisem de saber sobre os corretores ou máquinas dentro de um cluster. 

Escala nos Hubs de eventos é controlada pelas unidades de débito quantos comprar, com cada unidade de débito entitling 1 MB por segundo ou 1000 eventos por segundo de entrada. Por predefinição, os Centros de Eventos escalam as unidades de produção quando atingiro limite com a função [Auto-Inflate;](event-hubs-auto-inflate.md) esta funcionalidade também trabalha com os Hubs de Eventos para a funcionalidade Kafka. 

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
Para um **tutorial** com instruções passo a passo para criar um hub de eventos ativado por Kafka e acessá-lo usando SAS ou OAuth, consulte [Quickstart: Data streaming with Event Hubs usando o protocolo Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md).

Para mais **amostras** que mostrem como usar OAuth com Hubs de Eventos para Kafka, veja [amostras no GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth).

## <a name="other-event-hubs-features-available-for-kafka"></a>Outros recursos de Hubs de eventos disponíveis para o Kafka

Os Hubs de eventos para a funcionalidade de Kafka permite-lhe escrever com um protocolo e ler com outra, para que os produtores de Kafka atuais podem continuar a publicar através do Kafka, e pode adicionar os leitores com os Hubs de eventos, como o Azure Stream Analytics ou as funções do Azure. Além disso, os Centros [](event-hubs-capture-overview.md) de [](event-hubs-geo-dr.md) Eventos também trabalham com os Hubs de Eventos para a funcionalidade Kafka.

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

## <a name="next-steps"></a>Passos seguintes

Este artigo fornecido uma introdução aos Hubs de eventos para o Kafka. Para obter mais informações, consulte as seguintes ligações:

- [How to create Kafka enabled Event Hubs](event-hubs-create-kafka-enabled.md) (Como criar os Hubs de Eventos ativados para Kafka)
- [Transmitir em fluxo para os Hubs de Eventos a partir das suas aplicações Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Mirror a Kafka broker in a Kafka-enabled event hub](event-hubs-kafka-mirror-maker-tutorial.md) (Espelhar um mediador de Kafka num hub de eventos com Kafka ativado)
- [Connect Apache Spark to a Kafka-enabled event hub](event-hubs-kafka-spark-tutorial.md) (Ligar o Apache Spark a um hub de eventos com Kafka ativado)
- [Connect Apache Spark to a Kafka-enabled event hub](event-hubs-kafka-flink-tutorial.md) (Ligar o Apache Flink a um hub de eventos com Kafka ativado)
- [Integrate Kafka Connect with a Kafka-enabled event hub](event-hubs-kafka-connect-tutorial.md) (Integrar o Kafka Connect num hub de eventos com Kafka ativado)
- [Connect Akka Streams to a Kafka-enabled event hub](event-hubs-kafka-akka-streams-tutorial.md) (Ligar o Akka Streams a um hub de eventos com Kafka ativado)
- [Explore samples on our GitHub](https://github.com/Azure/azure-event-hubs-for-kafka) (Explorar exemplos no nosso GitHub)


