---
title: Comportamento de alerta de SMS em grupos de ação
description: Formato de mensagem SMS e resposta às mensagens SMS para cancelar a subscrição, reinscrever ou solicitar ajuda.
author: dkamstra
ms.author: dukek
services: monitoring
ms.topic: conceptual
ms.date: 02/16/2018
ms.subservice: alerts
ms.openlocfilehash: b75bda626f887f1224c1b0f18a80887983a2367d
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77665311"
---
# <a name="sms-alert-behavior-in-action-groups"></a>Comportamento de alerta sms em grupos de ação

## <a name="overview"></a>Descrição geral 
Os grupos de ação permitem-lhe configurar uma lista de ações. Estes grupos são utilizados na definição de alertas; assegurar que um determinado grupo de ação seja notificado quando o alerta for acionado. Uma das ações apoiadas é a SMS; As notificações sMS suportam a comunicação bidirecional. Um utilizador pode responder a um SMS a:

- **Cancelar a subscrição de alertas:** Um utilizador pode cancelar a subscrição de todos os alertas SMS para todos os grupos de ação ou um único grupo de ação.
- **Reinscreva-se para alertas:** Um utilizador pode voltar a subscrever todos os alertas SMS para todos os grupos de ação ou um único grupo de ação.  
- **Pedido de ajuda:** Um utilizador pode solicitar mais informações sobre o SMS. São redirecionados para este artigo.

Este artigo cobre o comportamento dos alertas sms e as ações de resposta que o utilizador pode tomar com base no local do utilizador:

## <a name="receiving-an-sms-alert"></a>Recebendo um alerta de SMS
Um recetor SMS configurado como parte de um grupo de ação recebe um SMS quando um alerta é desencadeado. O SMS contém as seguintes informações:
* Shortname do grupo de ação este alerta foi enviado para
* Título do alerta

| RESPOSTA | Descrição |
| ----- | ----------- |
| DESATIVAção `<Action Group Short name>` | Desativa mais SMS do Grupo de Ação |
| ATIVAR `<Action Group Short name>` | Reativa SMS do Grupo de Ação |
| PARAR | Desativa mais SMS de todos os grupos de ação |
| INÍCIO | Reativa SMS de TODOS os Grupos de Ação |
| Ajuda | Uma resposta é enviada ao utilizador com um link para este artigo. |

>[!NOTE]
>Se um utilizador tiver dessubscrito dos alertas SMS, mas for adicionado a um novo grupo de ação; receberão alertas de SMS para o novo grupo de ação, mas permanecem sem subscrição de todos os grupos de ação anteriores.

## <a name="next-steps"></a>Passos Seguintes
Obtenha uma [visão geral dos alertas de registo de atividade](alerts-overview.md) e aprenda a ser alertado  
Saiba mais sobre [a limitação da taxa de SMS](alerts-rate-limiting.md)  
Saiba mais sobre [grupos de ação](../../azure-monitor/platform/action-groups.md)

