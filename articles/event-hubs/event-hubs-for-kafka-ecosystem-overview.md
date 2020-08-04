---
title: Use o centro de eventos da aplicação Apache Kafka - Azure Event Hubs ! Microsoft Docs
description: Este artigo fornece informações sobre o suporte da Apache Kafka pela Azure Event Hubs.
ms.topic: article
ms.date: 07/20/2020
ms.openlocfilehash: ab29c9c4270514e95752ab2bbd085ffe1b0a2fb0
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/03/2020
ms.locfileid: "87534877"
---
# <a name="use-azure-event-hubs-from-apache-kafka-applications"></a>Use hubs de eventos Azure a partir de aplicações Apache Kafka
O Event Hubs fornece um ponto final kafka que pode ser usado pelas suas aplicações existentes baseadas em Kafka como uma alternativa para executar o seu próprio cluster Kafka. O Event Hubs suporta [o protocolo Apache Kafka 1.0 e mais tarde,](https://kafka.apache.org/documentation/)e trabalha com as aplicações kafka existentes, incluindo o MirrorMaker.  

> [!VIDEO https://www.youtube.com/embed/UE1WgB96_fc]

## <a name="what-does-event-hubs-for-kafka-provide"></a>O que os Centros de Eventos de Kafka fornecem?

O event hubs para kafka fornece uma cabeça de protocolo em cima de Azure Event Hubs que é binária compatível com as versões Kafka 1.0 e mais tarde para leitura e escrita para tópicos kafka. Pode começar a utilizar o ponto final kafka a partir das suas aplicações sem alterações de código, mas com uma alteração mínima de configuração. Atualiza a cadeia de ligação nas configurações para apontar para o ponto final kafka exposto pelo seu centro de eventos em vez de apontar para o seu cluster Kafka. Em seguida, pode começar a transmitir eventos a partir das suas aplicações que usam o protocolo Kafka em Event Hubs. Esta integração também suporta quadros como [o Kafka Connect,](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect)que está atualmente em pré-visualização. 

Conceptualmente, kafka e Centros de Eventos são quase idênticos: ambos são registos divididos construídos para dados de streaming. Os seguintes conceitos de mapas de mesa entre Kafka e Event Hubs.

### <a name="kafka-and-event-hub-conceptual-mapping"></a>Mapeamento conceptual kafka e Event Hub

| Conceito kafka | Conceito de hubs de eventos|
| --- | --- |
| Cluster | Espaço de Nomes |
| Tópico | Hub de Eventos |
| Partição | Partição|
| Grupo de Consumidores | Grupo de Consumidores |
| Desvio | Desvio|

### <a name="key-differences-between-kafka-and-event-hubs"></a>Principais diferenças entre Kafka e Centros de Eventos

Enquanto [o Apache Kafka](https://kafka.apache.org/) é um software, que pode correr onde quiser, o Event Hubs é um serviço de nuvem semelhante ao Azure Blob Storage. Não existem servidores ou redes para gerir e não há corretores para configurar. Você cria um espaço de nome, que é um FQDN no qual os seus tópicos vivem, e depois cria Centros de Eventos ou tópicos dentro desse espaço de nome. Para obter mais informações sobre os Centros de Eventos e espaços de nome, consulte [as funcionalidades do Event Hubs](event-hubs-features.md#namespace). Como serviço na nuvem, o Event Hubs utiliza um único endereço IP virtual estável como ponto final, para que os clientes não precisem de saber sobre os corretores ou máquinas dentro de um cluster. 

A escala em Event Hubs é controlada por quantas unidades de produção compra, com cada unidade de produção a permitir-lhe 1 MB por segundo, ou 1000 eventos por segundo de entrada. Por predefinição, os Centros de Eventos escalam as unidades de produção quando atinge o seu limite com a função [Auto-Insuflado;](event-hubs-auto-inflate.md) esta funcionalidade também funciona com os Centros de Eventos para recurso Kafka. 

### <a name="security-and-authentication"></a>Segurança e autenticação
Sempre que publica ou consome eventos a partir de um Centro de Eventos para a Kafka, o seu cliente está a tentar aceder aos recursos do Event Hubs. Pretende assegurar que os recursos são acedidos através de uma entidade autorizada. Ao utilizar o protocolo Apache Kafka com os seus clientes, pode definir a sua configuração para autenticação e encriptação utilizando os mecanismos SASL. Ao utilizar os Centros de Eventos para Kafka requer a encriptação TLS (uma vez que todos os dados em trânsito com os Centros de Eventos são encriptados por TLS). Pode ser feito especificando a opção SASL_SSL no seu ficheiro de configuração. 

O Azure Event Hubs oferece múltiplas opções para autorizar o acesso aos seus recursos seguros. 

- Oauth
- Assinatura de acesso partilhado (SAS)

#### <a name="oauth"></a>Oauth
O Event Hubs integra-se com o Azure Ative Directory (Azure AD), que fornece um servidor de autorização centralizado compatível com **OAuth** 2.0. Com a Azure AD, pode utilizar o controlo de acesso baseado em funções (RBAC) para conceder permissões de grão fino às identidades do seu cliente. Pode utilizar esta funcionalidade com os seus clientes Kafka especificando **SASL_SSL** para o protocolo e **OAUTHBEARER** para o mecanismo. Para obter mais informações sobre as funções e níveis de Azure para o acesso à deteção, consulte [o Acesso autorizado com a Azure AD](authorize-access-azure-active-directory.md).

```xml
bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
security.protocol=SASL_SSL
sasl.mechanism=OAUTHBEARER
sasl.jaas.config=org.apache.kafka.common.security.oauthbearer.OAuthBearerLoginModule required;
sasl.login.callback.handler.class=CustomAuthenticateCallbackHandler;
```

#### <a name="shared-access-signature-sas"></a>Assinatura de acesso compartilhado (SAS)
O Event Hubs também fornece as **Assinaturas de Acesso Compartilhado (SAS)** para acesso delegado aos Centros de Eventos para recursos kafka. Autorizar o acesso utilizando o mecanismo baseado em fichas OAuth 2.0 proporciona uma segurança superior e facilidade de utilização sobre o SAS. As funções incorporadas também podem eliminar a necessidade de autorização baseada em ACL, que tem de ser mantida e gerida pelo utilizador. Pode utilizar esta funcionalidade com os seus clientes Kafka especificando **SASL_SSL** para o protocolo e **a PLAIN** para o mecanismo. 

```xml
bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

> [!NOTE]
> Ao utilizar a autenticação SAS com clientes Kafka, as ligações estabelecidas não são desligadas quando a tecla SAS é regenerada. 

#### <a name="samples"></a>Amostras 
Para um **tutorial** com instruções passo a passo para criar um centro de eventos e acessá-lo usando SAS ou OAuth, consulte [Quickstart: Streaming de dados com Centros de Eventos utilizando o protocolo Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md).

Para mais **amostras** que mostrem como usar o OAuth com Os Centros de Eventos para Kafka, consulte [as amostras no GitHub.](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth)

## <a name="other-event-hubs-features-available-for-kafka"></a>Outras funcionalidades do Event Hubs disponíveis para Kafka

O evento Hubs for Kafka permite-lhe escrever com um protocolo e ler com outro, para que os seus atuais produtores kafka possam continuar a publicar via Kafka, e pode adicionar leitores com Centros de Eventos, como Azure Stream Analytics ou Azure Functions. Além disso, funcionalidades de Event Hubs como [Capture](event-hubs-capture-overview.md) e [Geo Disaster-Recovery](event-hubs-geo-dr.md) também trabalham com os Centros de Eventos para recurso Kafka.

## <a name="features-that-are-not-yet-supported"></a>Características que ainda não estão suportadas 

Aqui está a lista de funcionalidades de Kafka que ainda não estão suportadas:

*   Transação
*   Compressão
*   Retenção baseada em tamanho
*   Compactação de registo
*   APOIO HTTP KAFKA API
*   Riachos Kafka

## <a name="next-steps"></a>Passos seguintes
Este artigo forneceu uma introdução aos Centros de Eventos para Kafka. Para saber mais, consulte [o guia de desenvolvimento apache Kafka para o Azure Event Hubs.](apache-kafka-developer-guide.md)


