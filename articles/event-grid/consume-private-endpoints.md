---
title: Entregar eventos usando o serviço de link privado
description: Este artigo descreve como trabalhar em torno da limitação de não ser capaz de entregar eventos usando o serviço de ligação privada.
ms.topic: how-to
ms.date: 02/12/2021
ms.openlocfilehash: 9df78e1cc7734ba9e455ed686286658006f9445e
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2021
ms.locfileid: "105629296"
---
# <a name="deliver-events-using-private-link-service"></a>Entregar eventos usando o serviço de link privado
Atualmente, não é possível entregar eventos usando [pontos finais privados.](../private-link/private-endpoint-overview.md) Ou seja, não há suporte se tiver requisitos rigorosos de isolamento de rede onde o tráfego de eventos entregues não deve sair do espaço IP privado. 

## <a name="use-managed-identity"></a>Utilizar a identidade gerida
No entanto, se os seus requisitos exigirem uma forma segura de enviar eventos usando um canal encriptado e uma identidade conhecida do remetente (neste caso, Grade de Evento) usando espaço IP público, poderá entregar eventos a Event Hubs, Service Bus ou Azure Storage service usando um tópico personalizado de rede de eventos Azure ou um domínio com identidade gerida pelo sistema. Para obter detalhes sobre a entrega de eventos utilizando identidade gerida, consulte [a entrega do Evento utilizando uma identidade gerida.](managed-service-identity.md) 

Em seguida, pode utilizar um link privado configurado em Funções Azure ou o seu webhook implantado na sua rede virtual para puxar eventos. Consulte a amostra: [Ligue-se a pontos finais privados com funções Azure](/samples/azure-samples/azure-functions-private-endpoints/connect-to-private-endpoints-with-azure-functions/).


:::image type="content" source="./media/consume-private-endpoints/deliver-private-link-service.svg" alt-text="Entregar através do serviço de link privado":::


Nesta configuração, o tráfego passa pelo IP/internet público, desde a Grade de Eventos até Centros de Eventos, Service Bus ou Azure Storage, mas o canal pode ser encriptado e é utilizada uma identidade gerida da Grade de Eventos. Se configurar as suas Funções Azure ou webhook implantados na sua rede virtual para utilizar um Event Hubs, Service Bus ou Azure Storage via link privado, essa secção do tráfego permanecerá, evidentemente, dentro do Azure.

## <a name="deliver-events-to-event-hubs-using-managed-identity"></a>Entregar eventos a Centros de Eventos usando identidade gerida
Para entregar eventos a centros de eventos no seu espaço de nomes Event Hubs utilizando identidade gerida, siga estes passos:

1. Ativar a identidade atribuída ao sistema: [tópicos do sistema,](enable-identity-system-topics.md) [tópicos personalizados e domínios](enable-identity-custom-topics-domains.md).  
1. [Adicione a identidade ao papel **de remetente de dados do Azure Event Hubs** no espaço de nomes dos Centros de Eventos](../event-hubs/authenticate-managed-identity.md#to-assign-azure-roles-using-the-azure-portal).
1. [Ativar os **serviços da Microsoft fidedignos para contornar esta** definição de firewall no seu espaço de nomes Desempacotadores do Event Hubs](../event-hubs/event-hubs-service-endpoints.md#trusted-microsoft-services). 
1. [Configure a subscrição do evento](managed-service-identity.md#create-event-subscriptions-that-use-an-identity) que usa um centro de eventos como ponto final para usar a identidade atribuída ao sistema.

## <a name="deliver-events-to-service-bus-using-managed-identity"></a>Entregar eventos ao Service Bus usando identidade gerida
Para entregar eventos às filas ou tópicos do Service Bus no seu espaço de nomes de Service Bus utilizando a identidade gerida, siga estes passos:

1. Ativar a identidade atribuída ao sistema: [tópicos do sistema,](enable-identity-system-topics.md) [tópicos personalizados e domínios](enable-identity-custom-topics-domains.md). 
1. Adicione a identidade ao papel [de **remetente de dados de autocarros do serviço Azure**](/service-bus-messaging/service-bus-managed-service-identity#azure-built-in-roles-for-azure-service-bus) no espaço de nomes do Service Bus
1. [Ativar os **serviços da Microsoft fidedignos para contornar esta** definição de firewall no seu espaço de nomes do Service Bus](../service-bus-messaging/service-bus-service-endpoints.md#trusted-microsoft-services). 
1. [Configure a subscrição do evento](managed-service-identity.md) que utiliza uma fila de Service Bus ou tópico como ponto final para utilizar a identidade atribuída ao sistema.

## <a name="deliver-events-to-storage"></a>Entregar eventos ao Armazenamento 
Para entregar eventos às filas de armazenamento utilizando a identidade gerida, siga estes passos:

1. Ativar a identidade atribuída ao sistema: [tópicos do sistema,](enable-identity-system-topics.md) [tópicos personalizados e domínios](enable-identity-custom-topics-domains.md). 
1. Adicione a identidade ao papel [ **de remetente de mensagens de fila de armazenamento**](../storage/common/storage-auth-aad-rbac-portal.md) na fila de armazenamento do Azure.
1. [Configure a subscrição do evento](managed-service-identity.md#create-event-subscriptions-that-use-an-identity) que utiliza uma fila de Service Bus ou tópico como ponto final para utilizar a identidade atribuída ao sistema.


## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre a entrega de eventos utilizando uma identidade gerida, consulte [a entrega do Evento utilizando uma identidade gerida.](managed-service-identity.md) 
