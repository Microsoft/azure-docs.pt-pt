---
title: Limitação da taxa para SMS, e-mails, notificações push da App Azure e webhooks
description: Compreenda como o Azure limita o número de possíveis SMS, e-mail, push da App Azure ou notificações de webhook de um grupo de ação.
author: dkamstra
ms.author: dukek
ms.topic: conceptual
ms.date: 3/12/2018
ms.subservice: alerts
ms.openlocfilehash: 066fcac24571c8e982784a3845a010525ff9088a
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77665532"
---
# <a name="rate-limiting-for-voice-sms-emails-azure-app-push-notifications-and-webhook-posts"></a>Limitação de tarifas para voz, SMS, e-mails, notificações push da App Azure e posts webhook
A limitação da taxa é uma suspensão de notificações que ocorre quando muitas são enviadas para um determinado número de telefone, endereço de e-mail ou dispositivo. A limitação da taxa garante que os alertas são controláveis e exequíveis.

Os limiares-limite da taxa são:

- **SMS**: Não mais do que 1 SMS a cada 5 minutos.
- **Voz**: Não mais do que 1 Chamada de voz a cada 5 minutos.
- **E-mail**: Não mais de 100 e-mails numa hora.
 
  Outras ações não são limitadas.

## <a name="rate-limit-rules"></a>Regras de limite de taxas
- Um número de telefone ou e-mail específico é limitado quando recebe mais mensagens do que o limiar permite.
- Um número de telefone ou e-mail pode fazer parte de grupos de ação em muitas subscrições. A limitação da taxa aplica-se em todas as subscrições. Aplica-se logo que o limiar é atingido, mesmo que as mensagens sejam enviadas de várias subscrições.
- Quando um endereço de e-mail é limitado, uma notificação adicional é enviada para comunicar a limitação da taxa. O e-mail diz quando a taxa limite expirar.

## <a name="next-steps"></a>Passos seguintes ##
* Saiba mais sobre o comportamento de [alerta de SMS.](alerts-sms-behavior.md)
* Obtenha uma [visão geral dos alertas de registo de atividadee](alerts-overview.md)aprenda a receber alertas.  
* Saiba [configurar alertas sempre que é publicada uma notificação](../../azure-monitor/platform/alerts-activity-log-service-notifications.md)de saúde de serviço.

