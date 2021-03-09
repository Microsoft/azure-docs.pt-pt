---
title: incluir ficheiro
description: incluir ficheiro
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 03/08/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: cd4d5de5d93e4aea862aaabd10fb5d3c6d45cb1c
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102510837"
---
## <a name="trusted-microsoft-services"></a>Serviços confiáveis da Microsoft
Quando ativa os **serviços da Microsoft fidedignas para contornar esta** definição de firewall, os seguintes serviços têm acesso aos seus recursos de Service Bus.

| Serviço de confiança | Cenários de utilização apoiados | 
| --------------- | ------------------------- | 
| Azure Event Grid | Permite que a Azure Event Grid envie eventos para filas ou tópicos no seu espaço de nomes de Service Bus. Também precisa de fazer os seguintes passos: <ul><li>Ativar a identidade atribuída ao sistema para um tópico ou um domínio</li><li>Adicione a identidade ao papel de remetente de dados de autocarros do serviço Azure no espaço de nomes do Service Bus</li><li>Em seguida, configurar a subscrição do evento que usa uma fila de Serviço Bus ou tópico como ponto final para usar a identidade atribuída ao sistema.</li></ul> <p>Para mais informações, consulte [a entrega do Evento com uma identidade gerida](../articles/event-grid/managed-service-identity.md)</p>|
| API Management do Azure | <p>O serviço de Gestão API permite-lhe enviar mensagens para uma fila/tópico de Serviço Bus no seu espaço de nomes de autocarros de serviço.</p><ul><li>Pode desencadear fluxos de trabalho personalizados enviando mensagens para a sua fila/tópico do Service Bus quando uma API é invocada utilizando a [política de pedido de envio](../articles/api-management/api-management-sample-send-request.md).</li><li>Também pode tratar uma fila/tópico de Service Bus como o seu backend numa API. Para obter uma política de amostragem, consulte [Authenticate utilizando uma identidade gerida para aceder a uma fila ou tópico de Serviço.](https://github.com/Azure/api-management-policy-snippets/blob/master/examples/Authenticate%20using%20Managed%20Identity%20to%20access%20Service%20Bus.xml) Também precisa de fazer os seguintes passos:<ol><li>Ativar a identidade atribuída ao sistema na instância de Gestão da API. Para obter instruções, consulte [utilização de identidades geridas na Azure API Management](../articles/api-management/api-management-howto-use-managed-service-identity.md).</li><li>Adicione a identidade ao papel de **remetente de dados de autocarros do serviço Azure** no espaço de nomes do Service Bus</li></ol></li></ul> | 