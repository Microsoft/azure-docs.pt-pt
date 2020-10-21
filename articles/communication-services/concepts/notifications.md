---
title: Notificações nos Serviços de Comunicação da Azure
titleSuffix: An Azure Communication Services concept document
description: Envie notificações aos utilizadores de aplicações construídas nos Serviços de Comunicação Azure.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 3e68e65a5c2ed73a8fb6d8e5d01c645e05ca5157
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92320706"
---
# <a name="communication-services-notifications"></a>Notificações dos Serviços de Comunicação

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

O chat dos Serviços de Comunicação Azure e as bibliotecas de clientes criam um canal de mensagens em tempo real que permite que as mensagens de sinalização sejam empurradas para clientes conectados de forma eficiente e fiável. Isto permite-lhe construir uma funcionalidade de comunicação rica e em tempo real nas suas aplicações sem a necessidade de implementar uma lógica de sondagens HTTP complicada. No entanto, em aplicações móveis, este canal de sinalização apenas permanece ligado quando a sua aplicação está ativa em primeiro plano. Se pretender que os seus utilizadores recebam chamadas recebidas ou mensagens de chat enquanto a sua aplicação estiver em segundo plano, deverá utilizar notificações push.

As notificações push permitem-lhe enviar informações da sua aplicação para dispositivos móveis dos utilizadores. Pode utilizar notificações push para mostrar um diálogo, reproduzir um som ou exibir uI de entrada. A Azure Communication Services fornece integrações com [Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview) e [Azure Notification Hubs](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-overview) que lhe permitem adicionar notificações push às suas apps.

## <a name="trigger-push-notifications-via-azure-event-grid"></a>Trigger notificações push via Azure Event Grid

A Azure Communication Services integra-se com [a Azure Event Grid](https://azure.microsoft.com/services/event-grid/) para fornecer notificações de eventos em tempo real de forma fiável, escalável e segura. Pode aproveitar esta integração para criar um serviço de notificação que fornece notificações de push móvel aos seus utilizadores, criando uma subscrição de rede de eventos que desencadeie uma [Função Azure](https://docs.microsoft.com/azure/azure-functions/functions-overview) ou webhook.

:::image type="content" source="./media/notifications/acs-events-int.png" alt-text="Diagrama mostrando como os Serviços de Comunicação se integram com a Grade de Eventos.":::

Saiba mais sobre [o manuseamento de eventos nos Serviços de Comunicação Azure.](./event-handling.md)

## <a name="deliver-push-notifications-via-azure-notification-hubs"></a>Entregar notificações push através de Azure Notification Hubs

Pode ligar um Hub de Notificação Azure ao seu recurso de Serviços de Comunicação para enviar automaticamente notificações push para o dispositivo móvel de um utilizador quando recebem uma chamada recebida. Deverá utilizar estas notificações push para acordar a sua aplicação a partir do fundo e apresentar UI que permita ao utilizador aceitar ou recusar a chamada. 

:::image type="content" source="./media/notifications/acs-anh-int.png" alt-text="Diagrama mostrando como os Serviços de Comunicação se integram com a Grade de Eventos.":::

Os Serviços de Comunicação utilizam o Azure Notification Hub como um serviço de passagem para comunicar com os vários serviços de notificação push específicos da plataforma utilizando a API [de Envio Direto.](https://docs.microsoft.com/rest/api/notificationhubs/direct-send) Isto permite-lhe reutilizar os recursos e configurações do Hub de Notificação Azure existentes para fornecer notificações de chamada fiáveis e de baixa latência às suas aplicações.

> [!NOTE]
> Atualmente apenas são suportadas notificações push push.

### <a name="notification-hub-provisioning"></a>Fornecimento de hub de notificação 

Para fornecer notificações push a dispositivos clientes usando Centros de Notificação, [crie um Centro de Notificação](https://docs.microsoft.com/azure/notification-hubs/create-notification-hub-portal) dentro da mesma subscrição que o seu recurso de Serviços de Comunicação. Os Hubs de Notificação Azure devem ser configurados para o Serviço de Notificações da Plataforma que pretende utilizar. Para saber como obter notificações push na sua aplicação de clientes a partir de Centros de Notificação, consulte Começar com Os Centros de [Notificação](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-android-push-notification-google-fcm-get-started) e selecione a plataforma do cliente alvo a partir da lista de drop-down perto do topo da página.

> [!NOTE]
> Atualmente as plataformas APNs e FCM são suportadas.

Uma vez configurado o seu hub de Notificação, pode associá-lo ao seu recurso de Serviços de Comunicação, fornecendo uma cadeia de ligação para o hub utilizando o Cliente Gestor de Recursos Azure ou através do portal Azure. O fio de ligação deve conter permissões de "Enviar". Recomendamos a criação de outra política de acesso apenas com permissões "Enviar" especificamente para o seu hub. Saiba mais sobre as políticas de [segurança e acesso dos Hubs de Notificação](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-security)

> [!IMPORTANT]
> Para ativar as notificações do Serviço de Notificação Apple Push VOIP, tem de definir o nome do seu centro de notificações como iD do seu pacote de aplicações com o `.voip` sufixo. Ver [Utilização APNS VOIP através de Centros de Notificação](https://docs.microsoft.com/azure/notification-hubs/voip-apns).

#### <a name="using-the-azure-resource-manager-client-to-configure-the-notification-hub"></a>Utilizar o cliente Azure Resource Manager para configurar o Centro de Notificação

Para iniciar sessão no Azure Resource Manager, execute o seguinte e inscreva-se usando as suas credenciais.

```console
armclient login
```

 Uma vez iniciado com sucesso, execute o seguinte para provisionar o centro de notificação:

```console
armclient POST /subscriptions/<sub_id>/resourceGroups/<resource_group>/providers/Microsoft.Communication/CommunicationServices/<resource_id>/linkNotificationHub?api-version=2020-08-20-preview "{'connectionString': '<connection_string>','resourceId': '<resource_id>'}"
```

#### <a name="using-the-azure-portal-to-configure-the-notification-hub"></a>Utilizando o portal Azure para configurar o Centro de Notificação

No portal, navegue para o seu recurso Azure Communication Services. Dentro do recurso Serviços de Comunicação, selecione Notificações push a partir do menu esquerdo da página serviços de comunicação e ligue o Centro de Notificação que aprovisionou anteriormente. Você precisará fornecer a sua cadeia de ligação e iD de recursos aqui:

:::image type="content" source="./media/notifications/acs-anh-portal-int.png" alt-text="Diagrama mostrando como os Serviços de Comunicação se integram com a Grade de Eventos.":::

> [!NOTE]
> Se a cadeia de ligação Azure Notification Hub for atualizada, o recurso serviços de comunicação também tem de ser atualizado.

#### <a name="device-registration"></a>Registo de dispositivo 

Consulte o [quickstart da chamada de voz](../quickstarts/voice-video-calling/getting-started-with-calling.md) para aprender a registar o seu dispositivo com os Serviços de Comunicação.

## <a name="next-steps"></a>Passos seguintes

* Para uma introdução à Grelha de Eventos Azure, veja [o que é a Grade de Eventos?](https://docs.microsoft.com/azure/event-grid/overview)
* Para saber mais sobre os conceitos do Hub de Notificação Azure, consulte [a documentação do Azure Notification Hubs](https://docs.microsoft.com/azure/notification-hubs/)
