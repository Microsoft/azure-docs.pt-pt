---
title: Limite de taxa para SMS, e-mails, notificações push
description: Compreenda como o Azure limita o número de possíveis notificações de SMS, e-mail, azure app push ou webhook de um grupo de ação.
author: dkamstra
ms.author: dukek
ms.topic: conceptual
ms.date: 3/12/2018
ms.subservice: alerts
ms.openlocfilehash: 39b9fd8396601d777aa802a147bee3acc4a22cd0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87045292"
---
# <a name="rate-limiting-for-voice-sms-emails-azure-app-push-notifications-and-webhook-posts"></a>Limite de tarifas para Voz, SMS, e-mails, notificações push da App Azure e posts webhook
A limitação da taxa é uma suspensão de notificações que ocorre quando muitas são enviadas para um determinado número de telefone, endereço de e-mail ou dispositivo. A limitação da taxa garante que os alertas são geríveis e exequíveis.

Os limiares de limite de taxa são:

- **SMS**: Não mais de 1 SMS a cada 5 minutos.
- **Voz**: Não mais do que 1 Chamada de voz a cada 5 minutos.
- **Email**: Não mais de 100 e-mails em uma hora.
 
  Outras ações não são limitadas.

## <a name="rate-limit-rules"></a>Regras limite de taxa
- Um número de telefone ou e-mail em particular é limitado quando recebe mais mensagens do que o limiar permite.
- Um número de telefone ou e-mail pode fazer parte de grupos de ação em muitas subscrições. A limitação da taxa aplica-se em todas as subscrições. Aplica-se assim que o limiar é atingido, mesmo que as mensagens sejam enviadas a partir de várias subscrições.
- Quando um endereço de e-mail é limitado, uma notificação adicional é enviada para comunicar a taxa limite. O e-mail indica quando a taxa limite expira.

## <a name="next-steps"></a>Passos seguintes ##
* Saiba mais sobre [o comportamento do alerta por SMS.](alerts-sms-behavior.md)
* Obtenha uma [visão geral dos alertas de registo de atividades](alerts-overview.md)e aprenda a receber alertas.  
* Saiba como [configurar alertas sempre que uma notificação de saúde do serviço for publicada](../../service-health/alerts-activity-log-service-notifications-portal.md).
