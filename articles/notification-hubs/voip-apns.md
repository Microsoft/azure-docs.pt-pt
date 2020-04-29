---
title: Envie notificações VOIP APNS com Hubs de Notificação Azure
description: Saiba como enviar notificações DOBS APNS através de Hubs de Notificação Azure (não suportados oficialmente).
author: sethmanheim
ms.author: sethm
ms.date: 3/23/2020
ms.topic: how-to
ms.service: notification-hubs
ms.openlocfilehash: c99af881b8f93b75633741c2352dc5df17dd2963
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80146891"
---
# <a name="use-apns-voip-through-notification-hubs-not-officially-supported"></a>Utilize APNS VOIP através de Centros de Notificação (não suportados oficialmente)

É possível utilizar notificações APNS VOIP através de Centros de Notificação Azure; no entanto, não há apoio oficial para este cenário.

## <a name="considerations"></a>Considerações

Se ainda optar por enviar notificações DOP BP através de Centros de Notificação, esteja ciente das seguintes limitações:

- O envio de uma `apns-topic` notificação VOIP requer que o `.voip` cabeçalho seja definido para o pacote de aplicação ID + o sufixo. Por exemplo, para uma aplicação `com.microsoft.nhubsample`de `apns-topic` amostra com o ID do pacote, o cabeçalho deve ser definido para`com.microsoft.nhubsample.voip.`

   Este método não funciona bem com o Azure Notification Hubs, porque o ID da aplicação deve ser configurado como parte das credenciais APNS do hub, e o valor não pode ser alterado. Além disso, os Centros de `apns-topic` Notificação não permitem que o valor do cabeçalho seja ultrapassado no tempo de funcionar.

   Para enviar notificações VOIP, deve configurar um `.voip` centro de notificação separado com o pacote de aplicações ID.

- O envio de uma `apns-push-type` notificação VOIP requer `voip`que o cabeçalho seja definido ao valor .

   Para ajudar os clientes com a transição para o iOS 13, `apns-push-type` o Notification Hubs tenta inferir o valor correto para o cabeçalho. A lógica de inferência é intencionalmente simples, num esforço para evitar quebrar notificações padrão. Infelizmente, este método causa problemas com notificações VOIP, porque a Apple trata as notificações do VOIP como um caso especial que não segue as mesmas regras que as notificações padrão.

   Para enviar notificações VOIP, deve especificar `apns-push-type` um valor explícito para o cabeçalho.

- Os Centros de Notificação limitam as cargas apns a 4 KB, conforme documentado pela Apple. Para notificações VOIP, a Apple permite cargas até 5 KB. Os Centros de Notificação não diferenciam as notificações padrão e VOIP; portanto, todas as notificações estão limitadas a 4 KB.

   Para enviar notificações VOIP, não deve exceder o limite de tamanho de carga útil de 4 KB.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, veja as seguintes ligações:

- [Documentação `apns-topic` `apns-push-type` para e cabeçalhos e valores, incluindo os casos especiais para notificações VOIP](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/sending_notification_requests_to_apns).

- [Documentação para o limite](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)do tamanho da carga útil.

- [Notificações Hubs atualizações para iOS 13](push-notification-updates-ios-13.md#apns-push-type).
