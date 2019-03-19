---
title: Comportamento do alerta por SMS nos grupos de ação
description: Formato de mensagem SMS e a responder às mensagens SMS para cancelar sua assinatura, subscrever ou pedir ajuda.
author: dkamstra
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 02/16/2018
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: 225c86ee1a7f764f60b2da0b8e3be02aa5dd22e7
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58123305"
---
# <a name="sms-alert-behavior-in-action-groups"></a>Comportamento em grupos de ação de alerta de SMS
## <a name="overview"></a>Descrição geral ##
Grupos de ação permitem-lhe configurar uma lista de ações. Estes grupos são utilizados quando definir alertas; garantir que um grupo de ação específica é notificado quando o alerta é acionado. Uma das ações suportadas é SMS; Notificações por SMS suportam comunicação bidirecional. Um utilizador pode responder a um SMS para:

- **Anular a subscrição de alertas:** Um utilizador pode cancelar a inscrição de todos os alertas SMS para todos os grupos de ação ou um grupo de ação única.
- **Subscrever a alertas:** Um utilizador pode subscrever a todos os alertas SMS para todos os grupos de ação ou um grupo de ação única.  
- **Ajuda do pedido:** Um utilizador pode pedir para obter mais informações sobre o SMS. Eles são redirecionados para este artigo.

Este artigo aborda o comportamento dos alertas SMS e as ações de resposta o utilizador pode efetuar com base na localidade do usuário:

## <a name="receiving-an-sms-alert"></a>Receber um alerta SMS
Um receptor SMS configurado como parte de um grupo de ação recebe uma SMS quando for acionado um alerta. O SMS contém as seguintes informações:
* Nome abreviado do grupo de ação que este alerta foi enviado para
* Título do alerta

| RESPOSTA | Descrição |
| ----- | ----------- |
| DESATIVAR <Action Group Short name> | Desativa o SMS adicional do grupo de ação |
| ATIVAR <Action Group Short name> | Reativa o SMS. o grupo de ação |
| PARAR | Desativa o SMS adicional de todos os grupos de ação |
| INICIAR | Reativa SMS de todos os grupos de ação |
| OBTER AJUDA | Uma resposta é enviada para o utilizador com uma ligação para este artigo. |

>[!NOTE]
>Se um utilizador anulou a subscrição de SMS de alertas, mas, em seguida, é adicionado a um novo grupo de ação; eles irão receber alertas SMS para esse novo grupo de ação, mas permanecem anulados todos os grupos de ação anterior.

## <a name="next-steps"></a>Próximos Passos
Obter um [descrição geral dos alertas de registo de atividade](alerts-overview.md) e saiba como receber alertas  
Saiba mais sobre [limitação de velocidade SMS](alerts-rate-limiting.md)  
Saiba mais sobre [grupos de ação](../../azure-monitor/platform/action-groups.md)

