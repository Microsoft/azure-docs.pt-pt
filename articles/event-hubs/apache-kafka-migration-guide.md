---
title: Migrar para hubs de eventos azure para Apache Kafka
description: Este artigo mostra como os consumidores e produtores que utilizam diferentes protocolos (AMQP, Apache Kafka e HTTPS) podem trocar eventos ao utilizar em Azure Event Hubs.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/01/2020
ms.author: shvija
ms.openlocfilehash: 806a1f16327ad72a7f3527c813b355e1ba807dda
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606759"
---
# <a name="migrate-to-azure-event-hubs-for-apache-kafka-ecosystems"></a>Migrar para hubs de eventos azure para ecossistemas Apache Kafka
O Azure Event Hubs expõe um ponto final apache Kafka, que lhe permite ligar-se aos Centros de Eventos utilizando o protocolo Kafka. Ao fazer alterações mínimas na sua aplicação kafka existente, pode ligar-se aos Hubs de Eventos Azure e colher os benefícios do ecossistema Azure. Os Hubs de Eventos para Kafka suportam [a versão 1.0 de Apache Kafka](https://kafka.apache.org/10/documentation.html) e mais tarde.

## <a name="pre-migration"></a>Pré-migração 

### <a name="create-an-azure-account"></a>Criar uma conta do Azure
Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.

### <a name="create-an-event-hubs-namespace"></a>Criar um espaço de nomes dos Hubs de Eventos
Siga instruções passo a passo no artigo [Create a event hub](event-hubs-create.md) para criar um espaço de nome do Event Hubs e um hub de eventos. 

### <a name="connection-string"></a>Cadeia de ligação
Siga os passos da [cadeia de ligação Get a partir do](event-hubs-get-connection-string.md#get-connection-string-from-the-portal) artigo do portal. E, note a corda de ligação para posterior utilização. 

### <a name="fully-qualified-domain-name-fqdn"></a>Nome de domínio totalmente qualificado (FQDN)
Também pode necessitar do FQDN que aponta para o seu espaço de nome sem nome do Event Hub. O FQDN pode ser encontrado dentro da sua cadeia de ligação da seguinte forma:

`Endpoint=sb://`**`mynamespace.servicebus.windows.net`**`/;SharedAccessKeyName=XXXXXX;SharedAccessKey=XXXXXX`

Se o espaço de nome sem nome do Event Hubs for implantado \*numa nuvem \*não pública, o seu nome de domínio pode diferir (por exemplo, .servicebus.chinacloudapi.cn, .servicebus.usgovcloudapi.net ou \*.servicebus.cloudapi.de).

## <a name="migration"></a>Migração 

### <a name="update-your-kafka-client-configuration"></a>Atualize a configuração do seu cliente Kafka

Para se ligar a um Hub de Eventos ativado por Kafka, terá de atualizar as configurações do cliente Kafka. Se está com dificuldades em encontrar o `bootstrap.servers` seu, tente procurar onde está definido na sua candidatura.

Insira os seguintes configs onde quer que faça sentido na sua aplicação. Certifique-se de `bootstrap.servers` `sasl.jaas.config` atualizar os valores e valores para direcionar o cliente para o seu ponto final do Event Hubs Kafka com a autenticação correta. 

```
bootstrap.servers={MYNAMESPACE}.servicebus.windows.net:9093
request.timeout.ms=60000
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{CONNECTION STRING TO YOUR NAMESPACE}";
``` 

Se `sasl.jaas.config` não for uma configuração suportada no seu quadro, encontre as configurações que são usadas para definir o nome de utilizador e a palavra-passe SASL e utilizá-las. Detete o `$ConnectionString` nome de utilizador e a palavra-passe para a sua linha de ligação Event Hubs.

## <a name="post-migration"></a>Pós-migração
Execute a sua aplicação Kafka que envia eventos para o centro do evento. Em seguida, verifique se o centro do evento recebe os eventos usando o portal Azure. Na página **de visão geral** do espaço de nome sinuoso do seu Event Hubs, altere para a vista **Mensagens** na secção **Métricas.** Refresque a página para atualizar o gráfico. Pode levar alguns segundos para mostrar que as mensagens foram recebidas. 

[![Verifique se o centro do evento recebeu as mensagens](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)


## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre Centros de Eventos e Centros de Eventos para Kafka, consulte os seguintes artigos:  

- [Guia de resolução de problemas apache Kafka para Centros de Eventos](apache-kafka-troubleshooting-guide.md)
- [Perguntas frequentes - Centros de Eventos para Apache Kafka](apache-kafka-frequently-asked-questions.md)
- [Guia de desenvolvimento apache Kafka para Hubs de Eventos Azure](apache-kafka-developer-guide.md)
- [Configurações recomendadas](https://github.com/Azure/azure-event-hubs-for-kafka/blob/master/CONFIGURATION.md)
- 