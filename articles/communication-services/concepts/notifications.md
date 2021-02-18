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
ms.openlocfilehash: 30cb023b8ca78f252dbf087a604a61b8aa5c6659
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100577394"
---
# <a name="communication-services-notifications"></a>Notificações dos Serviços de Comunicação

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

O chat dos Serviços de Comunicação Azure e as bibliotecas de clientes criam um canal de mensagens em tempo real que permite que as mensagens de sinalização sejam empurradas para clientes conectados de forma eficiente e fiável. Isto permite-lhe construir uma funcionalidade de comunicação rica e em tempo real nas suas aplicações sem a necessidade de implementar uma lógica de sondagens HTTP complicada. No entanto, em aplicações móveis, este canal de sinalização apenas permanece ligado quando a sua aplicação está ativa em primeiro plano. Se pretender que os seus utilizadores recebam chamadas recebidas ou mensagens de chat enquanto a sua aplicação estiver em segundo plano, deverá utilizar notificações push.

As notificações push permitem-lhe enviar informações da sua aplicação para dispositivos móveis dos utilizadores. Pode utilizar notificações push para mostrar um diálogo, reproduzir um som ou exibir uI de entrada. A Azure Communication Services fornece integrações com [Azure Event Grid](../../event-grid/overview.md) e [Azure Notification Hubs](../../notification-hubs/notification-hubs-push-notification-overview.md) que lhe permitem adicionar notificações push às suas apps.

## <a name="trigger-push-notifications-via-azure-event-grid"></a>Trigger notificações push via Azure Event Grid

A Azure Communication Services integra-se com [a Azure Event Grid](https://azure.microsoft.com/services/event-grid/) para fornecer notificações de eventos em tempo real de forma fiável, escalável e segura. Pode aproveitar esta integração para criar um serviço de notificação que fornece notificações de push móvel aos seus utilizadores, criando uma subscrição de rede de eventos que desencadeie uma [Função Azure](../../azure-functions/functions-overview.md) ou webhook.

:::image type="content" source="./media/notifications/acs-events-int.png" alt-text="Diagrama mostrando como os Serviços de Comunicação se integram com a Grade de Eventos.":::

Saiba mais sobre [o manuseamento de eventos nos Serviços de Comunicação Azure.](./event-handling.md)

## <a name="deliver-push-notifications-via-azure-notification-hubs"></a>Entregar notificações push através de Azure Notification Hubs

Pode ligar um Hub de Notificação Azure ao seu recurso de Serviços de Comunicação para enviar automaticamente notificações push para o dispositivo móvel de um utilizador quando recebem uma chamada recebida. Deverá utilizar estas notificações push para acordar a sua aplicação a partir do fundo e apresentar UI que permite ao utilizador aceitar ou recusar a chamada. 

:::image type="content" source="./media/notifications/acs-anh-int.png" alt-text="Diagrama mostrando como os serviços de comunicação se integram com os Hubs de Notificação Azure.":::

Os Serviços de Comunicação utilizam o Azure Notification Hub como um serviço de passagem para comunicar com os vários serviços de notificação push específicos da plataforma utilizando a API [de Envio Direto.](/rest/api/notificationhubs/direct-send) Isto permite-lhe reutilizar os recursos e configurações do Hub de Notificação Azure existentes para fornecer notificações de chamada fiáveis e de baixa latência às suas aplicações.

> [!NOTE]
> Atualmente apenas são suportadas notificações push push.

### <a name="notification-hub-provisioning"></a>Fornecimento de hub de notificação 

Para fornecer notificações push a dispositivos clientes usando Centros de Notificação, [crie um Centro de Notificação](../../notification-hubs/create-notification-hub-portal.md) dentro da mesma subscrição que o seu recurso de Serviços de Comunicação. Tem de configurar o Centro de Notificação Azure para o Sistema de Notificação da Plataforma que pretende utilizar. Para saber como obter notificações push na sua aplicação de clientes a partir de Centros de Notificação, consulte Começar com Os Centros de [Notificação](../../notification-hubs/notification-hubs-android-push-notification-google-fcm-get-started.md) e selecione a plataforma do cliente alvo a partir da lista de drop-down perto do topo da página.

> [!NOTE]
> Atualmente as plataformas APNs e FCM são suportadas.  
A plataforma APNs precisa de ser configurada com o modo de autenticação simbólica. O modo de autenticação de certificados não é suportado a partir de agora. 

Uma vez configurado o seu hub de Notificação, pode associá-lo ao seu recurso de Serviços de Comunicação, fornecendo uma cadeia de ligação para o hub utilizando o Cliente Gestor de Recursos Azure ou através do portal Azure. O fio de ligação deve conter `Send` permissões. Recomendamos a criação de outra política de acesso apenas com `Send` permissões especificamente para o seu hub. Saiba mais sobre as políticas de [segurança e acesso dos Hubs de Notificação](../../notification-hubs/notification-hubs-push-notification-security.md)

#### <a name="using-the-azure-resource-manager-client-to-link-your-notification-hub"></a>Utilizar o cliente Azure Resource Manager para ligar o seu Centro de Notificação

Para iniciar sessão no Azure Resource Manager, execute o seguinte e inscreva-se usando as suas credenciais.

```console
armclient login
```

 Uma vez iniciado com sucesso, execute o seguinte para provisionar o centro de notificação:

```console
armclient POST /subscriptions/<sub_id>/resourceGroups/<resource_group>/providers/Microsoft.Communication/CommunicationServices/<resource_id>/linkNotificationHub?api-version=2020-08-20-preview "{'connectionString': '<connection_string>','resourceId': '<resource_id>'}"
```

#### <a name="using-the-azure-portal-to-link-your-notification-hub"></a>Utilizar o portal Azure para ligar o seu Centro de Notificação

No portal, navegue para o seu recurso Azure Communication Services. Dentro do recurso Serviços de Comunicação, selecione Notificações push a partir do menu esquerdo da página serviços de comunicação e ligue o Centro de Notificação que aprovisionou anteriormente. Você precisará fornecer a sua cadeia de conexão e recursosId aqui:

:::image type="content" source="./media/notifications/acs-anh-portal-int.png" alt-text="Screenshot mostrando as definições de Notificações push dentro do portal Azure.":::

> [!NOTE]
> Se a cadeia de ligação Azure Notification Hub for atualizada, o recurso serviços de comunicação também tem de ser atualizado.  
Qualquer alteração na forma como o hub está ligado será refletida no plano de dados (isto é, ao enviar uma notificação) dentro de um período máximo de ``10`` minutos. Isto aplica-se também quando o hub está ligado pela primeira vez **se** houver notificações enviadas anteriormente.

### <a name="device-registration"></a>Registo de dispositivo 

Consulte o [quickstart da chamada de voz](../quickstarts/voice-video-calling/getting-started-with-calling.md) para aprender a registar o seu dispositivo com os Serviços de Comunicação.

### <a name="troubleshooting-guide-for-push-notifications"></a>Guia de resolução de problemas para notificações push

Quando não vê notificações push no seu dispositivo, existem três locais onde as notificações podem ter sido retiradas:

- A Azure Notification Hubs não aceitou a notificação dos Serviços de Comunicação da Azure
- O Sistema de Notificação da Plataforma (por exemplo, APNs e FCM) não aceitou a notificação dos Hubs de Notificação do Azure
- O Sistema de Notificação da Plataforma não entregou a notificação ao dispositivo.

O primeiro local onde uma notificação pode ser retirada (os Hubs de Notificação Azure não aceitaram as notificações dos Serviços de Comunicação Azure) é abordado abaixo. Para os outros dois locais, consulte [notificações de diagnóstico deixadas em Azure Notification Hubs](../../notification-hubs/notification-hubs-push-notification-fixer.md).

Uma maneira de ver se o seu recurso de Serviços de Comunicação envia notificações para os Hubs de Notificação do Azure é olhando para a `incoming messages` métrica das métricas do Hub de [Notificação Azure](../../azure-monitor/essentials/metrics-supported.md#microsoftnotificationhubsnamespacesnotificationhubs).

Seguem-se algumas configurações erradas comuns que podem ser a causa pela qual o Azure Notification Hub não aceita as notificações do seu recurso de Serviços de Comunicação.

#### <a name="azure-notification-hub-not-linked-to-the-communication-services-resource"></a>Hub de Notificação Azure não ligado ao recurso serviços de comunicação

Pode haver o caso de não ter ligado o seu Centro de Notificação Azure ao seu recurso de Serviços de Comunicação. Pode dar uma olhada na [secção de provisionamento do Centro de Notificações](#notification-hub-provisioning) para ver como ligá-los.

#### <a name="the-linked-azure-notification-hub-isnt-configured"></a>O centro de notificação Azure ligado não está configurado

Tem de configurar o Centro de Notificação ligado com as credenciais do Sistema de Notificação da Plataforma para a plataforma (por exemplo, iOS ou android) que gostaria de utilizar. Para mais detalhes sobre como isso pode ser feito, pode dar uma olhada nas [notificações push configurar num centro de notificações](../../notification-hubs/configure-notification-hub-portal-pns-settings.md).

#### <a name="the-linked-azure-notification-hub-doesnt-exist"></a>O centro de notificação Azure ligado não existe

O Centro de Notificação Azure ligado ao seu recurso de Serviços de Comunicação já não existe. Verifique se o Centro de Notificação ligado ainda existe.

#### <a name="the-azure-notification-hub-apns-platform-is-configured-with-certificate-authentication-mode"></a>A plataforma Azure Notification Hub APNs está configurada com o modo de autenticação de certificados

Caso pretenda utilizar a plataforma APNs com o modo de autenticação de certificados, não está suportada atualmente. Deverá configurar a plataforma APNs com o modo de autenticação simbólica, conforme especificado no [Conjunto de notificações push num centro de notificação](../../notification-hubs/configure-notification-hub-portal-pns-settings.md).

#### <a name="the-linked-connection-string-doesnt-have-send-permission"></a>A cadeia de ligação ligada não tem `Send` permissão

A cadeia de ligação que usou para ligar o seu Centro de Notificação ao seu recurso de Serviços de Comunicação precisa de ter a `Send` permissão. Para mais detalhes sobre como pode criar uma nova cadeia de ligação ou ver a cadeia de conexão atual a partir do seu Azure Notification Hub, pode dar uma olhada nas políticas de [segurança e acesso dos Centros de Notificação](../../notification-hubs/notification-hubs-push-notification-security.md)

#### <a name="the-linked-connection-string-or-azure-notification-hub-resourceid-arent-valid"></a>A cadeia de ligação ligada ou os recursos do Hub de Notificação AzureId não são válidos

Certifique-se de que configura o recurso dos Serviços de Comunicação com a cadeia de ligação correta e os recursos do Hub de Notificação Azure

#### <a name="the-linked-connection-string-is-regenerated"></a>A cadeia de ligação ligada é regenerada

Caso tenha regenerado a cadeia de ligação do seu Centro de Notificação Azure ligado, tem de atualizar a cadeia de ligação com a nova no recurso dos serviços de [comunicação, religando o Centro](#notification-hub-provisioning)de Notificação .

## <a name="next-steps"></a>Passos seguintes

* Para uma introdução à Grelha de Eventos Azure, veja [o que é a Grade de Eventos?](../../event-grid/overview.md)
* Para saber mais sobre os conceitos do Hub de Notificação Azure, consulte [a documentação do Azure Notification Hubs](../../notification-hubs/index.yml)