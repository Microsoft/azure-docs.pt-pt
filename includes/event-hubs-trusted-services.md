---
title: incluir ficheiro
description: incluir ficheiro
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 03/08/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 0e487b3ab3663c765c052f2064201865508ef57f
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102510858"
---
## <a name="trusted-microsoft-services"></a>Serviços confiáveis da Microsoft
Quando ativa os **serviços da Microsoft fidedignos para contornar esta** definição de firewall, os seguintes serviços têm acesso aos recursos do Seu Centro de Eventos.

| Serviço de confiança | Cenários de utilização apoiados | 
| --------------- | ------------------------- | 
| Azure Event Grid | Permite que a Azure Event Grid envie eventos para centros de eventos no seu espaço de nomes de Event Hubs. Também precisa de fazer os seguintes passos: <ul><li>Ativar a identidade atribuída ao sistema para um tópico ou um domínio</li><li>Adicione a identidade ao papel de remetente de dados do Azure Event Hubs no espaço de nomes do Event Hubs</li><li>Em seguida, configurar a subscrição do evento que usa um centro de eventos como ponto final para usar a identidade atribuída ao sistema.</li></ul> <p>Para mais informações, consulte [a entrega do Evento com uma identidade gerida](../articles/event-grid/managed-service-identity.md)</p>|
| Monitor Azure (Definições de diagnóstico) | Permite que o Azure Monitor envie informações de diagnóstico para centros de eventos no seu espaço de nomes De Event Hubs. O Azure Monitor pode ler a partir do centro de eventos e também escrever dados para o centro de eventos. |
| Azure Stream Analytics | Permite que um trabalho do Azure Stream Analytics leia dados a partir de[(entrada)](../articles/stream-analytics/stream-analytics-add-inputs.md)ou escreva dados para[(saída)](../articles/stream-analytics/event-hubs-output.md)centros de eventos no seu espaço de nomes De Event Hubs. <p>**Importante**: O trabalho stream Analytics deve ser configurado para usar uma **identidade gerida** para aceder ao centro de eventos. Para obter mais informações, consulte [utilize identidades geridas para aceder ao Event Hub a partir de um trabalho Azure Stream Analytics (Preview)](../articles/stream-analytics/event-hubs-managed-identity.md). </p>|
| Hub IoT do Azure | Permite que o IoT Hub envie mensagens para centros de eventos no seu espaço de nomes Event Hub. Também precisa de fazer os seguintes passos: <ul><li>Ativar a identidade atribuída ao sistema para o seu hub IoT</li><li>Adicione a identidade ao papel de remetente de dados do Azure Event Hubs no espaço de nomes do Event Hubs.</li><li>Em seguida, configurar o IoT Hub que usa um centro de eventos como um ponto final personalizado para usar a autenticação baseada na identidade.</li></ul>
| API Management do Azure | <p>O serviço de Gestão API permite-lhe enviar eventos para um centro de eventos no seu espaço de nomes De Event Hubs.</p> <ul><li>Pode desencadear fluxos de trabalho personalizados enviando eventos para o seu centro de eventos quando uma API é invocada utilizando a [política de pedido de envio.](../articles/api-management/api-management-sample-send-request.md)</li><li>Você também pode tratar um centro de eventos como seu backend em uma API. Para obter uma política de amostragem, consulte [Authenticate utilizando uma identidade gerida para aceder a um centro de eventos](https://github.com/Azure/api-management-policy-snippets/blob/master/examples/Authenticate%20using%20Managed%20Identity%20to%20access%20Event%20Hub.xml). Também precisa de fazer os seguintes passos:<ol><li>Ativar a identidade atribuída ao sistema na instância de Gestão da API. Para obter instruções, consulte [utilização de identidades geridas na Azure API Management](../articles/api-management/api-management-howto-use-managed-service-identity.md).</li><li>Adicione a identidade ao papel de **remetente de dados do Azure Event Hubs** no espaço de nomes do Event Hubs</li></ol></li></ul> | 
