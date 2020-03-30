---
title: Processo de pedido & notificações - Gestão de direitos da Azure AD
description: Conheça o processo de pedido de um pacote de acesso e quando as notificações de e-mail são enviadas na gestão de direitos do Diretório Ativo do Azure.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4ff270977449bb80f97073342dc0c726a3f2316
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128530"
---
# <a name="request-process-and-email-notifications-in-azure-ad-entitlement-management"></a>Processo de pedido e notificações de e-mail na gestão de direitos da AD Azure

Quando um utilizador submete um pedido a um pacote de acesso, um processo começa a entregar esse pedido de acesso. A administração de direitos da Azure AD envia notificações por e-mail a aprovadores e solicitadores quando ocorrerem eventos-chave durante o processo. Este artigo descreve o processo de pedido e as notificações de e-mail que são enviadas.

## <a name="request-process"></a>Processo de pedido

Um utilizador que necessite de acesso a um pacote de acesso pode enviar um pedido de acesso. Dependendo da configuração da apólice, o pedido pode requerer uma aprovação. Quando um pedido é aprovado, um processo começa a atribuir ao utilizador o acesso a cada recurso no pacote de acesso. O diagrama seguinte mostra uma visão geral do processo e dos diferentes estados:

![Diagrama do processo de aprovação](./media/entitlement-management-process/request-process.png)

| Estado | Descrição |
| --- | --- |
| Submetido | O utilizador submete um pedido. |
| Aprovação pendente | Se a política de um pacote de acesso necessitar de aprovação, um pedido passa para aprovação pendente. |
| Fora do prazo | Se nenhum aprovador aprovar um pedido dentro do prazo de aprovação, o pedido expira. Para tentar novamente, o utilizador terá de reenviar o seu pedido. |
| Negado | O aprovador nega um pedido. |
| Aprovado | O aprovador aprova um pedido. |
| Entrega | O utilizador **não** foi atribuído acesso a todos os recursos do pacote de acesso. Se este for um utilizador externo, o utilizador pode ainda não ter acedido ao diretório de recursos. Também podem não ter aceitado o pedido de consentimento. |
| Entregue | Foi atribuído ao utilizador acesso a todos os recursos do pacote de acesso. |
| Acesso alargado | Se forem permitidas extensões na apólice, o utilizador alargou a atribuição. |
| Acesso caducado | O acesso do utilizador ao pacote de acesso expirou. Para ter acesso novamente, o utilizador terá de submeter um pedido. |

## <a name="email-notifications"></a>Notificações por e-mail

Se for um aprovador, são enviadas notificações de e-mail quando precisa de aprovar um pedido de acesso. Também recebe notificações quando um pedido de acesso tiver sido concluído. Também são enviadas notificações de e-mail que indicam o estado do seu pedido se for um solicitador.

Os seguintes diagramas mostram quando estas notificações de e-mail são enviadas para os aprovadores ou para o solicitador. Consulte a tabela de notificações por [e-mail](entitlement-management-process.md#email-notifications-table) para encontrar o número correspondente às notificações de e-mail apresentadas nos diagramas.

### <a name="first-approvers-and-alternate-approvers"></a>Primeiros aprovadores e aprovadores alternativos
O diagrama seguinte mostra a experiência dos primeiros aprovadores e aprovadores alternativos, e as notificações de e-mail que recebem durante o processo de pedido:

![Fluxo de processo de aprovadores de primeiro e suplente](./media/entitlement-management-process/first-approvers-and-alternate-with-escalation-flow.png)

### <a name="requestors"></a>Requerentes
O diagrama seguinte mostra a experiência dos solicitadores e as notificações de e-mail que recebem durante o processo de pedido:

![Fluxo de processo de solicitação](./media/entitlement-management-process/requestor-approval-request-flow.png)

### <a name="2-stage-approval"></a>Aprovação em 2 fases
O diagrama seguinte mostra a experiência dos aprovadores do estágio 1 e do estágio 2 e as notificações de e-mail que recebem durante o processo de pedido:

![Fluxo de processo de aprovação de 2 fases](./media/entitlement-management-process/2stage-approval-with-request-timeout-flow.png)

### <a name="email-notifications-table"></a>Tabela de notificações por e-mail
A tabela seguinte fornece mais detalhes sobre cada uma destas notificações de e-mail. Para gerir estes e-mails, pode usar regras. Por exemplo, no Outlook, pode criar regras para mover os e-mails para uma pasta se o assunto contiver palavras desta tabela:

| # | Assunto de e-mail | Quando enviado | Enviado para |
| --- | --- | --- | --- |
| 1 | Ação necessária: Aprovar ou negar pedido reencaminhado até *[data]* | Este e-mail será enviado aos aprovadores alternativos da Fase 1 (após a escalada do pedido) para tomar medidas. | Aprovadores alternativos estágio 1 |
| 2 | Ação necessária: Aprovar ou negar pedido até *[data]* | Este e-mail será enviado ao primeiro aprovador, se a escalada for desativada, para tomar medidas. | Primeiro aprovador |
| 3 | Lembrete: Aprovar ou negar o pedido até *[data]* para *[requestor]* | Este e-mail de lembrete será enviado ao primeiro aprovador, se a escalada for desativada. O e-mail pede-lhes que tomem medidas se não o fizeram. | Primeiro aprovador |
| 4 | Aprovar ou negar o pedido por *[data]* em *[data]* | Este e-mail será enviado ao primeiro aprovador (se a escalada estiver ativada) para tomar medidas. | Primeiro aprovador |
| 5 | Ação requerida lembrete: Aprovar ou negar o pedido até *[data]* para *[solicitação]* | Este e-mail de lembrete será enviado ao primeiro aprovador, se a escalada estiver ativada. O e-mail pede-lhes que tomem medidas se não o fizeram. | Primeiro aprovador |
| 6 | O pedido expirou para *[access_package]* | Este e-mail será enviado aos primeiros aprovadores e aprovadores alternativos da fase 1 após o termo do pedido. | Primeiro aprovador, aprovadores alternativos da fase 1 |
| 7 | Pedido aprovado para *[requestor]* para *[access_package]* | Este e-mail será enviado aos primeiros aprovadores e aprovadores alternativos da fase 1 após a conclusão do pedido. | Primeiro aprovador, aprovadores alternativos da fase 1 |
| 8 | Pedido aprovado para *[requestor]* para *[access_package]* | Este e-mail será enviado aos primeiros aprovadores e aprovadores alternativos da fase 1 de um pedido de 2 fases quando o pedido da fase 1 for aprovado. | Primeiro aprovador, aprovadores alternativos da fase 1 |
| 9 | Pedido negado a *[access_package]* | Este e-mail será enviado ao solicitado quando o seu pedido for negado | Requerente |
| 10 | O seu pedido expirou para *[access_package]* | Este e-mail será enviado ao solicitado no final de um pedido de uma única ou 2 fases. O e-mail informa o solicitador de que o pedido expirou. | Requerente |
| 11 | Ação necessária: Aprovar ou negar pedido até *[data]* | Este e-mail será enviado ao segundo aprovador, se a escalada for desativada, para tomar medidas. | Segundo aprovador |
| 12 | Ação necessária lembrete: Aprovar ou negar o pedido até *[data]* | Este e-mail de lembrete será enviado para o segundo aprovador, se a escalada for desativada. A notificação pede-lhes que tomem medidas se ainda não o fizeram. | Segundo aprovador |
| 13 | Ação necessária: Aprovar ou negar o pedido até *[data]* para *[solicitação]* | Este e-mail será enviado para o segundo aprovador, se a escalada estiver ativada, para tomar medidas. | Segundo aprovador |
| 14 | Ação requerida lembrete: Aprovar ou negar o pedido até *[data]* para *[solicitação]* | Este e-mail de lembrete será enviado para o segundo aprovador, se a escalada estiver ativada. A notificação pede-lhes que tomem medidas se ainda não o fizeram. | Segundo aprovador |
| 15 | Ação necessária: Aprovar ou negar pedido reencaminhado até *[data]* | Este e-mail será enviado para os aprovadores alternativos do estágio 2, se a escalada estiver ativada, para tomar medidas. | Aprovadores alternativos estágio 2 |
| 16 | Pedido aprovado para *[requestor]* para *[access_package]* | Este e-mail será enviado para o segundo aprovador e aprovadores alternativos da fase 2 após a aprovação do pedido. | Segundo aprovador, aprovadores alternativos estágio-2 |
| 17 | Um pedido expirou para *[access_package]* | Este e-mail será enviado para o segundo aprovador ou aprovadores alternativos, após o termo do pedido. | Segundo aprovador, aprovadores alternativos estágio 2 |
| 18 | Agora tem acesso a *[access_package]* | Este email será enviado aos utilizadores finais para começar a utilizar o seu acesso. | Requerente |
| 19 | Alargar o acesso para *[access_package]* até *[data]* | Este e-mail será enviado aos utilizadores finais antes de expirar o seu acesso. | Requerente |
| 20 | O acesso terminou para *[access_package]* | Este e-mail será enviado aos utilizadores finais após o seu acesso expirar. | Requerente |

### <a name="access-request-emails"></a>Emails de pedido de acesso

Quando um solicitador submeter um pedido de acesso a um pacote de acesso configurado para exigir aprovação, todos os aprovadores adicionados à apólice receberão uma notificação por e-mail com detalhes do pedido. Os detalhes no e-mail incluem: organização de nome do solicitado réeia e justificação do negócio; e a data de início e fim de acesso solicitada (se fornecido). Os detalhes incluirão também quando o pedido foi apresentado e quando o pedido expirar.

O e-mail inclui um link que os aprovadores podem clicar para ir ao My Access para aprovar ou negar o pedido de acesso. Aqui está uma notificação de e-mail de amostra que é enviada ao primeiro aprovador ou segundo aprovador (se a aprovação de 2 fases estiver ativada) para completar um pedido de acesso:

![Aprovar pedido de acesso a email saque](./media/entitlement-management-shared/approver-request-email.png)

Os aprovadores também podem receber um e-mail de lembrete. O e-mail pede ao aprovador que tome uma decisão sobre o pedido. Aqui está uma notificação de e-mail de amostra que o aprovador recebe para lembrá-los de tomar medidas:

![Email de pedido de acesso de lembrete](./media/entitlement-management-process/approver-access-request-reminder-email.png)

### <a name="alternate-approvers-request-emails"></a>Os aprovadores alternativos solicitam e-mails

Se a definição de aprovadores alternativos estiver ativada e o pedido ainda estiver pendente, será reencaminhado. Os aprovadores alternativos receberão um e-mail para aprovar ou negar o pedido. Pode ativar os aprovadores alternativos na fase 1 e na fase 2. Aqui está uma amostra de e-mail da notificação que os aprovadores alternativos recebem:

![E-mail de pedido de aprovação alternativo](./media/entitlement-management-process/alternate-approver-email-fwd-request.png)

Tanto o aprovador como os aprovadores alternativos podem aprovar ou negar o pedido.

### <a name="approved-or-denied-emails"></a>E-mails aprovados ou negados

 Quando um executor recebe um pedido de acesso apresentado por um solicitador, pode aprovar ou negar o pedido de acesso. O aprovador precisa de acrescentar uma justificação empresarial para a sua decisão. Aqui está um e-mail de amostra enviado aos aprovadores e aprovadores alternativos após a aprovação de um pedido:

![Pedido aprovado para aceder a email saque](./media/entitlement-management-process/approver-request-email-approved.png)

Quando um pedido de acesso é aprovado, e o seu acesso é aprovisionado, uma notificação por e-mail é enviada ao solicitor que agora tem acesso ao pacote de acesso. Aqui está uma notificação de e-mail de amostra que é enviada a um solicitador quando lhes é concedido acesso a um pacote de acesso:

![E-mail de pedido de pedido de pedido de pedido de solicitação de solicitado aprovado](./media/entitlement-management-process/requestor-email-approved.png)

Quando um pedido de acesso é negado, uma notificação de e-mail é enviada ao solicitador. Aqui está uma notificação de e-mail de amostra que é enviada a um solicitador quando o seu pedido de acesso é negado:

![Pedido de pedido de pedido negado e-mail](./media/entitlement-management-process/requestor-email-denied.png)

### <a name="2-stage-approval-access-request-emails"></a>E-mails de pedido de acesso de aprovação de 2 fases

Se a aprovação de duas fases estiver ativada, pelo menos dois aprovadores devem aprovar o pedido, um de cada fase, antes de o solicitador poder ter acesso.

Durante a fase 1, o primeiro aprovador receberá o e-mail de pedido de acesso e tomará uma decisão. Se aprovarem o pedido, todos os primeiros aprovadores e aprovadores alternativos na fase 1 (se a escalada estiver ativada) receberão a notificação de que a fase 1 está completa. Aqui está uma amostra de e-mail da notificação que é enviada quando a fase 1 está completa:

![Email de pedido de acesso de 2 fases](./media/entitlement-management-process/approver-request-email-2stage.png)

Depois de os primeiros ou suplentes aprovarem o pedido na fase 1, começa a fase 2. Durante o estágio 2, o segundo aprovador receberá o e-mail de notificação de pedido de acesso. Após o segundo aprovador ou aprovadores alternativos na fase 2 (se a escalada estiver ativada) decidir aprovar ou negar o pedido, os e-mails de notificação são enviados para os primeiros e segundo supor, e todos os aprovadores alternativos na fase 1 e fase 2, bem como o solicitador.

### <a name="expired-access-request-emails"></a>E-mails de pedido de acesso expirado

Os pedidos de acesso podem caducar se nenhum aprovador tiver aprovado ou negado o pedido. 

Quando o pedido atinge a data de validade configurada e expira, este já não pode ser aprovado ou negado pelos aprovadores. Aqui está uma amostra de e-mail da notificação enviada para todos os primeiros, segundo (se a aprovação de 2 fases estiver ativada), e aprovadores alternativos:

![Aprovadores expiram pedido de acesso enviar e-mail](./media/entitlement-management-process/approver-request-email-expired.png)

É também enviada uma notificação por e-mail ao solicitado, notificando-os de que o seu pedido de acesso expirou e que necessita de reenviar o pedido de acesso. O diagrama seguinte mostra a experiência do solicitador e as notificações de e-mail que recebem quando pedem para alargar o acesso:

![Requestor prolongar fluxo de processo de acesso](./media/entitlement-management-process/requestor-expiration-request-flow.png) 

Aqui está uma notificação de e-mail de amostra que é enviada a um solicitador quando o seu pedido de acesso expirou:

![Pedido de acesso caducado pedido de e-mail](./media/entitlement-management-process/requestor-email-request-expired.png)

## <a name="next-steps"></a>Passos seguintes

- [Solicitar acesso a um pacote de acesso](entitlement-management-request-access.md)
- [Aprovar ou negar pedidos de acesso](entitlement-management-request-approve.md)
