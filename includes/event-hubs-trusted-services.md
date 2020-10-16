---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 08/07/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 7cef6252a99430f0d62d8f976510f0a723badc1f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91654402"
---
## <a name="trusted-microsoft-services"></a>Serviços confiáveis da Microsoft
Quando ativa os **serviços da Microsoft fidedignos para contornar esta** definição de firewall, os seguintes serviços têm acesso aos recursos do Seu Centro de Eventos.

| Serviço de confiança | Cenários de utilização apoiados | 
| --------------- | ------------------------- | 
| Azure Event Grid | Permite que a Azure Event Grid envie eventos para centros de eventos no seu espaço de nomes de Event Hubs. Também precisa de fazer os seguintes passos: <ul><li>Ativar a identidade atribuída ao sistema para um tópico ou um domínio</li><li>Adicione a identidade ao papel de remetente de dados do Azure Event Hubs no espaço de nomes do Event Hubs</li><li>Em seguida, configurar a subscrição do evento que usa um centro de eventos como ponto final para usar a identidade atribuída ao sistema.</li></ul> <p>Para mais informações, consulte [a entrega do Evento com uma identidade gerida](../articles/event-grid/managed-service-identity.md)</p>|
| Monitor Azure (Definições de diagnóstico) | Permite que o Azure Monitor envie informações de diagnóstico para centros de eventos no seu espaço de nomes De Event Hubs. |