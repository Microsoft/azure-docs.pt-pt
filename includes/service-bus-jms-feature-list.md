---
title: incluir ficheiro
description: incluir ficheiro
services: service-bus-messaging
author: axisc
ms.service: service-bus-messaging
ms.topic: include
ms.date: 6/9/2020
ms.author: aschhab
ms.custom: include file
ms.openlocfilehash: 574507fcc6a3c05919c441bd6d0ec9c573d4b6ae
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "100652615"
---
A tabela que se segue lista as funcionalidades do Java Message Service (JMS) que a Azure Service Bus suporta atualmente. Também mostra características que não são apoiadas.


| Funcionalidade | API |Estado |
|---|---|---|
| Filas   | <ul> <li> JMSContext.createQueue (Nome da fila de cordas) </li> </ul>| **Suportado** |
| Tópicos   | <ul> <li> JMSContext.createTopic ( Tema de cordaName) </li> </ul>| **Suportado** |
| Filas temporárias |<ul> <li> JMSContext.createTemporaryQueue() </li> </ul>| **Suportado** |
| Tópicos temporários |<ul> <li> JMSContext.createTemporaryTopic() </li> </ul>| **Suportado** |
| Produtor de mensagens /<br/> JMSProducer |<ul> <li> JMSContext.createProducer() </li> </ul>| **Suportado** |
| Navegadores de fila |<ul> <li> JMSContext.createBrowser (fila de fila) </li> <li> JMSContext.createBrowser (fila de fila, mensagens de cordaSelector) </li> </ul> | **Suportado** |
| Mensagem Consumidor/ <br/> JMSConsumer | <ul> <li> JMSContext.createConsumer (Destino de destino) </li> <li> JMSContext.createConsumer (destino de destino, mensagem de cordaSelector) </li> <li> JMSContext.createConsumer (Destino destino, mensagem de cordasSelector, boolean noLocal)</li> </ul>  <br/> noLocal não é atualmente suportado | **Suportado** |
| Assinaturas duradouras partilhadas | <ul> <li> JMSContext.createSharedDurableConsumer (tópico, nome de corda) </li> <li> JMSContext.createPartambleConsumer (tópico, nome de corda, mensagem de cordaSelector) </li> </ul>| **Suportado**|
| Assinaturas duradouras não partilhadas | <ul> <li> JMSContext.createDurableConsumer (tópico, nome de corda) </li> <li> createDurableConsumer (tópico, nome de corda, mensagem de cordaSelector, boolean noLocal) </li> </ul> <br/> noLocal não é atualmente suportado e deve ser definido para falso | **Suportado** |
| Assinaturas partilhadas não duráveis |<ul> <li> JMSContext.createPartilhadConsumer (tópico, String sharedSubscriptionName) </li> <li> JMSContext.createPartilhadConsumer (tópico, String sharedSubscriptionName, String messageSelector) </li> </ul> | **Suportado** |
| Assinaturas não partilhadas não duráveis |<ul> <li> JMSContext.createConsumer (Destino de destino) </li> <li> JMSContext.createConsumer (destino de destino, mensagem de cordaSelector) </li> <li> JMSContext.createConsumer (Destino destino, mensagem de cordasSelector, boolean noLocal) </li> </ul> <br/> noLocal não é atualmente suportado e deve ser definido para falso | **Suportado** |
| Seletores de mensagens | depende do consumidor criado | **Suportado** |
| Atraso de entrega (mensagens programadas) | <ul> <li> JMSProducer.setDeliveryDelay(longa entregaDelay) </li> </ul>|**Suportado**|
| Mensagem criada |<ul> <li> JMSContext.createMessage() </li> <li> JMSContext.createBytesMessage() </li> <li> JMSContext.createMapMessage() </li> <li> JMSContext.createObjectMessage (objeto serializável) </li> <li> JMSContext.createStreamMessage() </li> <li> JMSContext.createTextMessage() </li> <li> JMSContext.createTextMessage (Texto de cordas) </li> </ul>| **Suportado** |
| Transações de entidades cruzadas |<ul> <li> Connection.createSession (verdadeiro, Session.SESSION_TRANSACTED) </li> </ul> | **Suportado** |
| Transações distribuídas || Não suportado |
