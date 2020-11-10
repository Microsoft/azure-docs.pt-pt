---
title: incluir ficheiro
description: incluir ficheiro
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 11/05/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: bb6f2281cc115904db6babcf31f1af387b254d48
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94426573"
---
## <a name="trusted-microsoft-services"></a>Serviços confiáveis da Microsoft
Quando ativa os **serviços da Microsoft fidedignas para contornar esta** definição de firewall, os seguintes serviços têm acesso aos seus recursos de Service Bus.

| Serviço de confiança | Cenários de utilização apoiados | 
| --------------- | ------------------------- | 
| Azure Event Grid | Permite que a Azure Event Grid envie eventos para filas ou tópicos no seu espaço de nomes de Service Bus. Também precisa de fazer os seguintes passos: <ul><li>Ativar a identidade atribuída ao sistema para um tópico ou um domínio</li><li>Adicione a identidade ao papel de remetente de dados de autocarros do serviço Azure no espaço de nomes do Service Bus</li><li>Em seguida, configurar a subscrição do evento que usa uma fila de Serviço Bus ou tópico como ponto final para usar a identidade atribuída ao sistema.</li></ul> <p>Para mais informações, consulte [a entrega do Evento com uma identidade gerida](../articles/event-grid/managed-service-identity.md)</p>|
