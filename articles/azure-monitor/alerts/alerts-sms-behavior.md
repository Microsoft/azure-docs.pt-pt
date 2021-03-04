---
title: Comportamento de alerta de SMS em grupos de ação
description: Formato de mensagem SMS e resposta a mensagens SMS para cancelar a subscrição, resubscrever ou solicitar ajuda.
author: dkamstra
ms.author: dukek
services: monitoring
ms.topic: conceptual
ms.date: 02/16/2018
ms.openlocfilehash: e46517865ba01a0d4d113696fbadabf5ae7b0105
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102037936"
---
# <a name="sms-alert-behavior-in-action-groups"></a>Comportamento de alerta de SMS em grupos de ação

## <a name="overview"></a>Descrição Geral 
Os grupos de ação permitem-lhe configurar uma lista de ações. Estes grupos são utilizados na definição de alertas; assegurar que um determinado grupo de ação seja notificado quando o alerta é acionado. Uma das ações apoiadas é o SMS; As notificações SMS suportam a comunicação bidis. Um utilizador pode responder a um SMS para:

- **Cancelar a subscrição de alertas:** Um utilizador pode cancelar a subscrição de todos os alertas SMS para todos os grupos de ação ou de um único grupo de ação.
- **Reencamússo para alertas:** Um utilizador pode reenviar todos os alertas DE SMS para todos os grupos de ação ou um único grupo de ação.  
- **Pedido de ajuda:** Um utilizador pode pedir mais informações sobre o SMS. São redirecionados para este artigo.

Este artigo abrange o comportamento dos alertas SMS e as ações de resposta que o utilizador pode tomar com base no local do utilizador:

## <a name="receiving-an-sms-alert"></a>Receber um Alerta SMS
Um recetor SMS configurado como parte de um grupo de ação recebe um SMS quando um alerta é acionado. O SMS contém as seguintes informações:
* Nome curto do grupo de ação este alerta foi enviado para
* Título do alerta

| RESPONDER | Descrição |
| ----- | ----------- |
| DESATIVAR `<Action Group Short name>` | Desativa mais SMS do Grupo de Ação |
| ATIVAR `<Action Group Short name>` | Re-habilita SMS do Grupo de Ação |
| PARAR | Desativa mais SMS de todos os Grupos de Ação |
| INÍCIO | Reesibilizar SMS de TODOS os Grupos de Ação |
| AJUDA | É enviada uma resposta ao utilizador com um link para este artigo. |

>[!NOTE]
>Se um utilizador não tiver sido subscrito a partir de alertas SMS, mas for adicionado a um novo grupo de ação; receberão alertas SMS para o novo grupo de ação, mas continuam sem ser subscritos de todos os grupos de ação anteriores.

## <a name="next-steps"></a>Passos Seguintes
Obtenha uma [visão geral dos alertas de registo de atividade e](./alerts-overview.md) aprenda a ser alertado  
Saiba mais sobre [a limitação da taxa de SMS](alerts-rate-limiting.md)  
Saiba mais sobre [grupos de ação](./action-groups.md)