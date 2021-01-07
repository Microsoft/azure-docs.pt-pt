---
title: incluir ficheiro
description: incluir ficheiro
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 01/05/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 5c0585182fcd6899bdd123607f3b7d46a97253e9
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/06/2021
ms.locfileid: "97956512"
---
## <a name="trusted-microsoft-services"></a>Serviços confiáveis da Microsoft
Quando ativa os **serviços da Microsoft fidedignos para contornar esta** definição de firewall, os seguintes serviços têm acesso aos recursos do Seu Centro de Eventos.

| Serviço de confiança | Cenários de utilização apoiados | 
| --------------- | ------------------------- | 
| Azure Event Grid | Permite que a Azure Event Grid envie eventos para centros de eventos no seu espaço de nomes de Event Hubs. Também precisa de fazer os seguintes passos: <ul><li>Ativar a identidade atribuída ao sistema para um tópico ou um domínio</li><li>Adicione a identidade ao papel de remetente de dados do Azure Event Hubs no espaço de nomes do Event Hubs</li><li>Em seguida, configurar a subscrição do evento que usa um centro de eventos como ponto final para usar a identidade atribuída ao sistema.</li></ul> <p>Para mais informações, consulte [a entrega do Evento com uma identidade gerida](../articles/event-grid/managed-service-identity.md)</p>|
| Monitor Azure (Definições de diagnóstico) | Permite que o Azure Monitor envie informações de diagnóstico para centros de eventos no seu espaço de nomes De Event Hubs. |
| Azure Stream Analytics | Permite que um trabalho do Azure Stream Analytics leia dados a partir de[(entrada)](../articles/stream-analytics/stream-analytics-add-inputs.md)ou escreva dados para[(saída)](../articles/stream-analytics/event-hubs-output.md)centros de eventos no seu espaço de nomes De Event Hubs. <p>**Importante**: O trabalho stream Analytics deve ser configurado para usar uma **identidade gerida** para aceder ao centro de eventos. Para obter mais informações, consulte [utilize identidades geridas para aceder ao Event Hub a partir de um trabalho Azure Stream Analytics (Preview)](../articles/stream-analytics/event-hubs-managed-identity.md). </p>|
| Hub IoT do Azure | Permite que o IoT Hub envie mensagens para centros de eventos no seu espaço de nomes Event Hub. Também precisa de fazer os seguintes passos: <ul><li>Ativar a identidade atribuída ao sistema para o seu hub IoT</li><li>Adicione a identidade ao papel de remetente de dados do Azure Event Hubs no espaço de nomes do Event Hubs.</li><li>Em seguida, configurar o IoT Hub que usa um centro de eventos como um ponto final personalizado para usar a autenticação baseada na identidade.</li></ul>
