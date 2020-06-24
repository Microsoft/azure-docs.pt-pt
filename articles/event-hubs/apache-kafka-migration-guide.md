---
title: Migrar para Azure Event Hubs para Apache Kafka
description: Este artigo mostra como os consumidores e produtores que usam diferentes protocolos (AMQP, Apache Kafka e HTTPS) podem trocar eventos ao utilizarem os Azure Event Hubs.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/23/2020
ms.author: shvija
ms.openlocfilehash: f3f5e7ab5ca0d47d18c802dadbcac902ed12e147
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/24/2020
ms.locfileid: "85299403"
---
# <a name="migrate-to-azure-event-hubs-for-apache-kafka-ecosystems"></a>Migrar para centros de eventos de Azure para ecossistemas Apache Kafka
O Azure Event Hubs expõe um ponto final Apache Kafka, que lhe permite ligar-se aos Centros de Eventos usando o protocolo Kafka. Ao fazer alterações mínimas na sua aplicação Kafka existente, pode ligar-se aos Azure Event Hubs e colher os benefícios do ecossistema Azure. Os Centros de Eventos para Kafka suportam [a versão 1.0](https://kafka.apache.org/10/documentation.html) e posterior da Apache Kafka.

## <a name="pre-migration"></a>Pré-migração 

### <a name="create-an-azure-account"></a>Criar uma conta do Azure
Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.

### <a name="create-an-event-hubs-namespace"></a>Criar um espaço de nomes dos Hubs de Eventos
Siga as instruções passo a passo no artigo do Centro de [Eventos](event-hubs-create.md) para criar um espaço de nomes de Event Hubs e um centro de eventos. 

### <a name="connection-string"></a>Cadeia de ligação
Siga os passos da [cadeia de ligação Get a partir do](event-hubs-get-connection-string.md#get-connection-string-from-the-portal) artigo do portal. E, anote a cadeia de ligação para posterior utilização. 

### <a name="fully-qualified-domain-name-fqdn"></a>Nome de domínio totalmente qualificado (FQDN)
Você também pode precisar do FQDN que aponta para o seu espaço de nomes Event Hub. O FQDN pode ser encontrado dentro da sua cadeia de ligação da seguinte forma:

`Endpoint=sb://`**`mynamespace.servicebus.windows.net`**`/;SharedAccessKeyName=XXXXXX;SharedAccessKey=XXXXXX`

Se o seu espaço de nomes 'Event Hubs' for implantado numa nuvem não pública, o nome do seu domínio pode diferir (por exemplo, \* .servicebus.chinacloudapi.cn, \* .servicebus.usgovcloudapi.net ou \* .servicebus.cloudapi.de).

## <a name="migration"></a>Migração 

### <a name="update-your-kafka-client-configuration"></a>Atualize a configuração do seu cliente Kafka

Para se ligar a um Centro de Eventos ativado por Kafka, terá de atualizar as configurações do cliente kafka. Se está com dificuldades em encontrar o seu, tente procurar onde `bootstrap.servers` está definido na sua aplicação.

Insira os seguintes configs onde quer que faça sentido na sua aplicação. Certifique-se de atualizar os `bootstrap.servers` valores e `sasl.jaas.config` valores para direcionar o cliente para o seu ponto final de Eventos Hubs Kafka com a autenticação correta. 

```
bootstrap.servers={MYNAMESPACE}.servicebus.windows.net:9093
request.timeout.ms=60000
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{CONNECTION STRING TO YOUR NAMESPACE}";
``` 

Se `sasl.jaas.config` não for uma configuração suportada no seu quadro, encontre as configurações que são usadas para definir o nome de utilizador e palavra-passe SASL e usá-las em vez disso. Desa estama de utilizador `$ConnectionString` e a palavra-passe para a sua cadeia de ligação 'Centros de Eventos'.

## <a name="post-migration"></a>Pós-migração
Execute a sua aplicação Kafka que envia eventos para o centro de eventos. Em seguida, verifique se o centro de eventos recebe os eventos usando o portal Azure. Na página **geral** do espaço de nomes dos Centros de Eventos, mude para a vista **mensagens** na secção **Métricas.** Refresque a página para atualizar o gráfico. Pode levar alguns segundos para mostrar que as mensagens foram recebidas. 

[![Verifique se o centro de eventos recebeu as mensagens](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)


## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre Os Centros de Eventos e Centros de Eventos para Kafka, consulte os seguintes artigos:  

- [Guia de resolução de problemas de Apache Kafka para centros de eventos](apache-kafka-troubleshooting-guide.md)
- [Perguntas frequentes - Centros de Eventos para Apache Kafka](apache-kafka-frequently-asked-questions.md)
- [Guia de desenvolvimento apache Kafka para hubs de eventos Azure](apache-kafka-developer-guide.md)
- [Configurações recomendadas](https://github.com/Azure/azure-event-hubs-for-kafka/blob/master/CONFIGURATION.md)