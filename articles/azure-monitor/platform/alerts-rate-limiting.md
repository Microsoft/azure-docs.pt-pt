---
title: Taxa de limitação para mensagens de e-mail, SMS e webhooks e notificações push da aplicação do Azure
description: Compreenda como o Azure limita o número de possíveis SMS, e-mail e as notificações de push ou webhook de aplicação do Azure a partir de um grupo de ação.
author: dkamstra
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 3/12/2018
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: 11fd6a2c58671cc5d0bcf0593239eb9e62aca834
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58109496"
---
# <a name="rate-limiting-for-voice-sms-emails-azure-app-push-notifications-and-webhook-posts"></a>Taxa de limitação para voz, SMS, mensagens de correio eletrónico, notificações push da aplicação do Azure e webhook de mensagens
Limitação de velocidade é uma suspensão de notificações que ocorre quando demasiados são enviados para um número de telefone específico, o endereço de e-mail ou o dispositivo. Limitação de velocidade garante que os alertas são gerenciáveis e passíveis de ação.

Os limiares de limite de taxa são:

- **SMS**: SMS não mais de 1 a cada 5 minutos.
- **Voz**: Chamada de voz não mais de 1 a cada 5 minutos.
- **e-mail**: Não mais do que 100 mensagens de e-mail numa hora.
 
  Outras ações não são o limite de taxa.

## <a name="rate-limit-rules"></a>Regras de limite de taxa
- Um número de telefone específico ou o e-mail é limitada ao receber mais mensagens do que permite o limiar de taxa.
- Um número de telefone ou e-mail pode ser parte de grupos de ação em várias subscrições. Limitação de velocidade aplica-se em todas as subscrições. Aplica-se assim que o limiar for atingido, mesmo que as mensagens são enviadas a partir de várias subscrições.
- Quando um endereço de e-mail é taxa limitada, é enviada uma notificação adicional para comunicar a limitação de velocidade. Os Estados de e-mail quando a limitação de velocidade expira.

## <a name="next-steps"></a>Passos Seguintes ##
* Saiba mais sobre [comportamento de alertas do SMS](alerts-sms-behavior.md).
* Obter um [descrição geral dos alertas de registo de atividade](alerts-overview.md)e saiba como receber alertas.  
* Saiba como [configurar alertas sempre que uma notificação de estado de funcionamento do serviço é lançada](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).

