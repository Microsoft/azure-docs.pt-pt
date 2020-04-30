---
title: Perguntas frequentes - Azure Event Hubs para Apache Kafka
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
ms.openlocfilehash: 0186b90e1d75c5dba6e1ca26e4ba079a3456cea4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81606746"
---
# <a name="frequently-asked-questions---event-hubs-for-apache-kafka"></a>Perguntas frequentes - Centros de Eventos para Apache Kafka 
Este artigo fornece respostas a algumas das perguntas frequentes sobre a migração para os Centros de Eventos para Apache Kafka.

## <a name="do-you-run-apache-kafka"></a>Dirige apache kafka?

Não.  Executamos operações da API kafka contra a infraestrutura do Event Hubs.  Porque há uma correlação estreita entre a funcionalidade Da Apache Kafka e os Hubs de Eventos AMQP (isto é, produzir, receber, gestão, assim por diante.), somos capazes de trazer a conhecida fiabilidade dos Hubs de Eventos para o espaço Kafka PaaS.

## <a name="event-hubs-consumer-group-vs-kafka-consumer-group"></a>Grupo de consumidores Event Hubs vs. Kafka consumer group
Qual é a diferença entre um grupo de consumidores do Event Hub e um grupo de consumidores Kafka em Event Hubs? Os grupos de consumidores kafka em Event Hubs são totalmente distintos dos grupos de consumidores standard Event Hubs.

**Grupos de consumidores de Hubs de Eventos**

- São geridos com modelos de criação, recuperação, atualização e eliminação (CRUD) através de modelos de portal, SDK ou Azure Resource Manager. Os grupos de consumidores do Event Hubs não podem ser autocriados.
- São entidades infantis de um centro de eventos. Significa que o mesmo nome de grupo de consumidores pode ser reutilizado entre centros de eventos no mesmo espaço de nome porque são entidades separadas.
- Não são usados para armazenar compensações. O consumo de AMQP orquestrado é feito com recurso ao armazenamento externo de compensação, por exemplo, o Armazenamento Azure.

**Grupos de consumidores kafka**

- São auto-criados.  Os grupos kafka podem ser geridos através do grupo de consumidores Kafka APIs.
- Podem armazenar compensações no serviço Event Hubs.
- São usados como chaves no que é efetivamente uma loja de valor-chave offset. Para um par `group.id` `topic-partition`único de e, armazenamos uma compensação no Armazenamento Azure (replicação 3x). Os utilizadores do Event Hubs não incorrem em custos de armazenamento extra ao armazenar em compensações kafka. As compensações são manipuladas através do grupo de consumidores Kafka APIs, mas as *contas* de armazenamento compensadas não são diretamente visíveis ou manipuladoras para os utilizadores do Event Hub.  
- Abrangem um espaço-nome. Usar o mesmo nome de grupo Kafka para múltiplas aplicações em vários tópicos significa que todas as aplicações e seus clientes Kafka serão reequilibrados sempre que apenas uma única aplicação precisa de ser reequilibrada.  Escolha os nomes do seu grupo sabiamente.
- Distinguem-se totalmente dos grupos de consumidores do Event Hubs. **Não** precisa de usar '$Default', nem precisa de se preocupar com clientes kafka que interferem com cargas de trabalho da AMQP.
- Não são vê-se no portal Azure. A informação do grupo de consumidores é acessível através de APIs kafka.

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre Centros de Eventos e Centros de Eventos para Kafka, consulte os seguintes artigos:  

- [Guia de desenvolvimento apache Kafka para Centros de Eventos](apache-kafka-developer-guide.md)
- [Guia de migração Apache Kafka para Centros de Eventos](apache-kafka-migration-guide.md)
- [Guia de resolução de problemas apache Kafka para Centros de Eventos](apache-kafka-troubleshooting-guide.md)
- [Configurações recomendadas](https://github.com/Azure/azure-event-hubs-for-kafka/blob/master/CONFIGURATION.md)

