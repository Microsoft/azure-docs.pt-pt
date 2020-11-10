---
title: incluir ficheiro
description: incluir ficheiro
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 11/09/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: d828a0c3eb2582a833ee8ad07bdf4f18002c9dca
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94427200"
---
## <a name="trusted-microsoft-services"></a>Serviços confiáveis da Microsoft
Quando ativa os **serviços da Microsoft fidedignos para contornar esta** definição de firewall, os seguintes serviços têm acesso aos recursos do Seu Centro de Eventos.

| Serviço de confiança | Cenários de utilização apoiados | 
| --------------- | ------------------------- | 
| Azure Event Grid | Permite que a Azure Event Grid envie eventos para centros de eventos no seu espaço de nomes de Event Hubs. Também precisa de fazer os seguintes passos: <ul><li>Ativar a identidade atribuída ao sistema para um tópico ou um domínio</li><li>Adicione a identidade ao papel de remetente de dados do Azure Event Hubs no espaço de nomes do Event Hubs</li><li>Em seguida, configurar a subscrição do evento que usa um centro de eventos como ponto final para usar a identidade atribuída ao sistema.</li></ul> <p>Para mais informações, consulte [a entrega do Evento com uma identidade gerida](../articles/event-grid/managed-service-identity.md)</p>|
| Monitor Azure (Definições de diagnóstico) | Permite que o Azure Monitor envie informações de diagnóstico para centros de eventos no seu espaço de nomes De Event Hubs. |
| Azure Stream Analytics | Permite que um trabalho do Azure Stream Analytics leia dados a partir de[(entrada)](../articles/stream-analytics/stream-analytics-add-inputs.md)ou escreva dados para[(saída)](../articles/stream-analytics/event-hubs-output.md)centros de eventos no seu espaço de nomes De Event Hubs. |
| Hub IoT do Azure | Permite que o IoT Hub envie mensagens para centros de eventos no seu espaço de nomes Event Hub. Também precisa de fazer os seguintes passos: <ul><li>Ativar a identidade atribuída ao sistema para o seu hub IoT</li><li>Adicione a identidade ao papel de remetente de dados do Azure Event Hubs no espaço de nomes do Event Hubs.</li><li>Em seguida, configurar o IoT Hub que usa um centro de eventos como um ponto final personalizado para usar a autenticação baseada na identidade.</li></ul>
