---
title: Enviar notificações APNS VOIP com centros de notificação Azure
description: Saiba como enviar notificações APNS VOIP através de Azure Notification Hubs (não suportados oficialmente).
author: sethmanheim
ms.author: sethm
ms.date: 3/23/2020
ms.topic: how-to
ms.service: notification-hubs
ms.openlocfilehash: c99af881b8f93b75633741c2352dc5df17dd2963
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "80146891"
---
# <a name="use-apns-voip-through-notification-hubs-not-officially-supported"></a>Utilize o APNS VOIP através de Centros de Notificação (não suportados oficialmente)

É possível utilizar notificações APNS VOIP através de Azure Notification Hubs; no entanto, não há apoio oficial para este cenário.

## <a name="considerations"></a>Considerações

Se ainda optar por enviar notificações APNS VOIP através de Centros de Notificação, esteja ciente das seguintes limitações:

- O envio de uma notificação VOIP requer que o `apns-topic` cabeçalho seja definido para o pacote de aplicações ID + o `.voip` sufixo. Por exemplo, para uma aplicação de amostra com o iD do `com.microsoft.nhubsample` pacote, o `apns-topic` cabeçalho deve ser definido para `com.microsoft.nhubsample.voip.`

   Este método não funciona bem com os Hubs de Notificação Azure, porque o ID do pacote da aplicação deve ser configurado como parte das credenciais apns do hub, e o valor não pode ser alterado. Além disso, os Centros de Notificação não permitem que o valor do `apns-topic` cabeçalho seja ultrapassado no tempo de execução.

   Para enviar notificações VOIP, tem de configurar um centro de notificação separado com o `.voip` ID do pacote de aplicações.

- O envio de uma notificação VOIP requer que o `apns-push-type` cabeçalho seja definido para o valor `voip` .

   Para ajudar os clientes com a transição para o iOS 13, os Centros de Notificação tentam inferir o valor correto para o `apns-push-type` cabeçalho. A lógica da inferência é intencionalmente simples, num esforço para evitar quebrar notificações padrão. Infelizmente, este método causa problemas com notificações VOIP, porque a Apple trata as notificações VOIP como um caso especial que não segue as mesmas regras que as notificações padrão.

   Para enviar notificações VOIP, deve especificar um valor explícito para o `apns-push-type` cabeçalho.

- Os Centros de Notificação limitam as cargas apns a 4 KB, conforme documentado pela Apple. Para notificações VOIP, a Apple permite cargas até 5 KB. Os Centros de Notificação não diferenciam entre notificações standard e VOIP; por conseguinte, todas as notificações estão limitadas a 4 KB.

   Para enviar notificações VOIP, não deve exceder o limite de tamanho de carga útil de 4 KB.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, veja as seguintes ligações:

- [Documentação `apns-topic` para `apns-push-type` cabeçalhos e valores, incluindo os casos especiais para notificações VOIP](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/sending_notification_requests_to_apns).

- [Documentação para o limite do tamanho da carga útil.](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)

- [Notificações Hubs atualizações para iOS 13](push-notification-updates-ios-13.md#apns-push-type).
