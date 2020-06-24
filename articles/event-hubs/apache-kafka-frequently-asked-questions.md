---
title: Perguntas frequentes - Azure Event Hubs para Apache Kafka
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
ms.openlocfilehash: cd4306fd4a3b6dd308b0d62945ae264dfb6ce8ff
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/24/2020
ms.locfileid: "85298332"
---
# <a name="frequently-asked-questions---event-hubs-for-apache-kafka"></a>Perguntas frequentes - Centros de Eventos para Apache Kafka 
Este artigo fornece respostas a algumas das perguntas frequentes sobre migração para Centros de Eventos para Apache Kafka.

## <a name="do-you-run-apache-kafka"></a>Dirige Apache Kafka?

Não.  Executamos operações da Kafka API contra a infraestrutura de Centros de Eventos.  Porque há uma correlação estreita entre a funcionalidade Apache Kafka e Event Hubs AMQP (isto é, produzir, receber, administrar, assim por diante.), somos capazes de trazer a fiabilidade conhecida dos Centros de Eventos para o espaço Kafka PaaS.

## <a name="event-hubs-consumer-group-vs-kafka-consumer-group"></a>Grupo de consumidores Event Hubs vs. Kafka consumer group
Qual é a diferença entre um grupo de consumidores event hub e um grupo de consumidores Kafka em Event Hubs? Os grupos de consumidores kafka em Event Hubs são totalmente distintos dos grupos de consumidores standard Event Hubs.

**Grupos de consumidores de Centros de Eventos**

- São Geridos com as operações de criação, recuperação, atualização e eliminação (CRUD) através de modelos de portal, SDK ou Azure Resource Manager. Os grupos de consumidores do Event Hubs não podem ser auto-criativos.
- São entidades infantis de um centro de eventos. Significa que o mesmo nome do grupo de consumidores pode ser reutilizado entre centros de eventos no mesmo espaço de nome porque são entidades separadas.
- Não são usados para armazenar compensações. O consumo de AMQP orquestrado é feito usando armazenamento externo offset, por exemplo, Azure Storage.

**Grupos de consumidores kafka**

- São auto-criados.  Os grupos Kafka podem ser geridos através do grupo de consumidores Kafka APIs.
- Podem armazenar compensações no serviço Event Hubs.
- São usados como chaves no que é efetivamente uma loja de valor-chave compensada. Para um par único de `group.id` `topic-partition` e, armazenamos um offset no Azure Storage (replicação 3x). Os utilizadores do Event Hubs não incorrem em custos de armazenamento extra ao armazenar compensações kafka. As compensações são manipuláveis através das APIs do grupo de *consumidores* Kafka, mas as contas de armazenamento compensadas não são diretamente visíveis ou manipuláveis para os utilizadores do Event Hub.  
- Abrangem um espaço de nome. A utilização do mesmo nome do grupo Kafka para múltiplas aplicações em vários tópicos significa que todas as aplicações e os seus clientes Kafka serão reequilibridos sempre que apenas uma aplicação precise de ser reequilibrada.  Escolha os nomes do seu grupo com sabedoria.
- São totalmente distintos dos grupos de consumidores Event Hubs. Não **don't** precisa de usar o "$Default", nem precisa de se preocupar com os clientes da Kafka que interferem com as cargas de trabalho da AMQP.
- Não são vistos no portal Azure. A informação do grupo de consumidores é acessível através de APIs kafka.

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre Os Centros de Eventos e Centros de Eventos para Kafka, consulte os seguintes artigos:  

- [Guia de desenvolvedores apache Kafka para centros de eventos](apache-kafka-developer-guide.md)
- [Guia de migração apache Kafka para centros de eventos](apache-kafka-migration-guide.md)
- [Guia de resolução de problemas de Apache Kafka para centros de eventos](apache-kafka-troubleshooting-guide.md)
- [Configurações recomendadas](https://github.com/Azure/azure-event-hubs-for-kafka/blob/master/CONFIGURATION.md)

