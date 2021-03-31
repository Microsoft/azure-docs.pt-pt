---
title: Processo de pedido & notificações - Gestão de direitos Azure AD
description: Conheça o processo de pedido de um pacote de acesso e quando as notificações de email são enviadas na gestão de direitos do Azure Ative Directory.
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
ms.date: 12/23/2020
ms.author: barclayn
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb84eae2db01180865db54250598f45c7b2ddca0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97746240"
---
# <a name="request-process-and-email-notifications-in-azure-ad-entitlement-management"></a>Processo de pedido e notificações de email na gestão de direitos da AD Azure

Quando um utilizador submete um pedido a um pacote de acesso, um processo começa a entregar esse pedido de acesso. A gestão de direitos AD da Azure envia notificações de e-mail a aprovadores e solicitadores quando eventos-chave ocorrem durante o processo. Este artigo descreve o processo de pedido e as notificações por e-mail que são enviadas.

## <a name="request-process"></a>Processo de pedido

Um utilizador que precise de acesso a um pacote de acesso pode submeter um pedido de acesso. Dependendo da configuração da apólice, o pedido pode requerer uma aprovação. Quando um pedido é aprovado, um processo começa a atribuir o acesso do utilizador a cada recurso no pacote de acesso. O diagrama a seguir mostra uma visão geral do processo e dos diferentes estados:

![Diagrama do processo de aprovação](./media/entitlement-management-process/request-process.png)

| Estado | Descrição |
| --- | --- |
| Submetido | O utilizador submete um pedido. |
| Aprovação pendente | Se a política de um pacote de acesso necessitar de aprovação, um pedido passa para a aprovação pendente. |
| Fora do prazo | Se nenhum aprovador aprovar um pedido dentro do prazo limite do pedido de aprovação, o pedido expira. Para tentar novamente, o utilizador terá de reenviar o seu pedido. |
| Negado | O aprovador nega um pedido. |
| Aprovado | O aprovador aprova um pedido. |
| Entrega | O utilizador **não** foi atribuído ao acesso a todos os recursos do pacote de acesso. Se este for um utilizador externo, o utilizador pode ainda não ter acedido ao diretório de recursos. Também podem não ter aceitado o pedido de consentimento. |
| Entregue | Foi-lhe atribuído acesso a todos os recursos do pacote de acesso. |
| Acesso alargado | Se forem permitidas extensões na apólice, o utilizador alargou a atribuição. |
| Acesso caducado | O acesso do utilizador ao pacote de acesso expirou. Para ter acesso novamente, o utilizador terá de submeter um pedido. |

## <a name="email-notifications"></a>Notificações por e-mail

Se for um aprovador, é-lhe enviadas notificações por e-mail quando precisa de aprovar um pedido de acesso. Também recebe notificações quando um pedido de acesso foi concluído. Também é enviada notificações por e-mail que indicam o estado do seu pedido se for solicitador.

Os seguintes diagramas mostram quando estas notificações de e-mail são enviadas para os aprovadores ou para o solicitador. Consulte a [tabela de notificações por e-mail](entitlement-management-process.md#email-notifications-table) para encontrar o número correspondente às notificações de email apresentadas nos diagramas.

### <a name="first-approvers-and-alternate-approvers"></a>Primeiros aprovadores e aprovadores alternativos
O diagrama que se segue mostra a experiência dos primeiros aprovadores e aprovadores alternativos, bem como as notificações por e-mail que recebem durante o processo de pedido:

![Fluxo de processo de aprovação primeiro e alternativo](./media/entitlement-management-process/first-approvers-and-alternate-with-escalation-flow.png)

### <a name="requestors"></a>Requerentes
O seguinte diagrama mostra a experiência dos solicitadores e as notificações de e-mail que recebem durante o processo de pedido:

![Fluxo de processo do solicitador](./media/entitlement-management-process/requestor-approval-request-flow.png)

### <a name="2-stage-approval"></a>Aprovação em 2 estágios
O diagrama que se segue mostra a experiência dos aprovadores de fase 1 e fase 2 e as notificações de e-mail que recebem durante o processo de pedido:

![Fluxo de processo de aprovação em 2 fases](./media/entitlement-management-process/2stage-approval-with-request-timeout-flow.png)

### <a name="email-notifications-table"></a>Tabela de notificações por e-mail
A tabela seguinte fornece mais detalhes sobre cada uma destas notificações de e-mail. Para gerir estes e-mails, pode usar regras. Por exemplo, no Outlook, pode criar regras para mover os e-mails para uma pasta se o assunto contiver palavras a partir desta tabela:

| # | Assunto de e-mail | Quando enviado | Enviado para |
| --- | --- | --- | --- |
| 1 | Ação necessária: Aprovar ou negar pedido reencaminhado *até [data]* | Este e-mail será enviado aos aprovadores alternativos da Fase 1 (após a escalada do pedido) para tomar medidas. | Aprovadores alternativos de fase 1 |
| 2 | Ação necessária: Aprovar ou negar pedido por *[data]* | Este e-mail será enviado ao primeiro aprovador, se a escalada for desativada, para tomar medidas. | Primeiro aprovador |
| 3 | Lembrete: Aprovar ou negar o pedido por *[data]* para *[solicitador]* | Este e-mail de lembrete será enviado para o primeiro aprovador, se a escalada for desativada. O e-mail pede-lhes que tomem medidas se não o fizerem. | Primeiro aprovador |
| 4 | Aprovar ou negar o pedido por *[hora]* em *[data]* | Este e-mail será enviado ao primeiro aprovador (se a escalada estiver habilitada) a tomar medidas. | Primeiro aprovador |
| 5 | Ação necessária para lembrar: Aprovar ou negar o pedido até *[data]* para *[solicitador]* | Este e-mail de lembrete será enviado para o primeiro aprovador, se a escalada estiver ativada. O e-mail pede-lhes que tomem medidas se não o fizerem. | Primeiro aprovador |
| 6 | O pedido expirou para *[access_package]* | Este e-mail será enviado para os primeiros aprovadores e aprovadores alternativos da fase 1 após o termo do pedido. | Primeiro aprovador, aprovadores alternativos de fase 1 |
| 7 | Pedido aprovado para *[solicitador]* para *[access_package]* | Este e-mail será enviado para os primeiros aprovadores e aprovadores alternativos de fase 1 após a conclusão do pedido. | Primeiro aprovador, aprovadores alternativos de fase 1 |
| 8 | Pedido aprovado para *[solicitador]* para *[access_package]* | Este e-mail será enviado aos primeiros aprovadores e aprovadores suplentes da fase 1 de um pedido de 2 fases quando o pedido de fase 1 for aprovado. | Primeiro aprovador, aprovadores alternativos de fase 1 |
| 9 | Pedido negado a *[access_package]* | Este e-mail será enviado ao solicitador quando o seu pedido for negado | Requerente |
| 10 | O seu pedido expirou para *[access_package]* | Este e-mail será enviado ao solicitador no final de um pedido único ou de 2 etapas. O e-mail notifica o solicitador de que o pedido expirou. | Requerente |
| 11 | Ação necessária: Aprovar ou negar pedido por *[data]* | Este e-mail será enviado ao segundo aprovador, se a escalada for desativada, para tomar medidas. | Segundo aprovador |
| 12 | Ação necessária para lembrar: Aprovar ou negar o pedido até *[data]* | Este e-mail de lembrete será enviado para o segundo aprovador, se a escalada for desativada. A notificação pede-lhes que tomem medidas se ainda não o fizeram. | Segundo aprovador |
| 13 | Ação necessária: Aprovar ou negar o pedido até *[data]* para *[solicitador]* | Este e-mail será enviado para o segundo aprovador, se a escalada estiver ativada, para tomar medidas. | Segundo aprovador |
| 14 | Ação necessária para lembrar: Aprovar ou negar o pedido até *[data]* para *[solicitador]* | Este e-mail de lembrete será enviado para o segundo aprovador, se a escalada estiver ativada. A notificação pede-lhes que tomem medidas se ainda não o fizeram. | Segundo aprovador |
| 15 | Ação necessária: Aprovar ou negar pedido reencaminhado *até [data]* | Este e-mail será enviado para os aprovadores alternativos da fase 2, se a escalada estiver ativada, para tomar medidas. | Aprovadores alternativos de fase 2 |
| 16 | Pedido aprovado para *[solicitador]* para *[access_package]* | Este e-mail será enviado ao segundo aprovante e aos aprovadores alternativos da fase 2 ao aprovar o pedido. | Segundo aprovador, aprovadores alternativos fase 2 |
| 17 | Um pedido expirou para *[access_package]* | Este e-mail será enviado para o segundo aprovante ou aprovadores alternativos, após o termo do pedido. | Segundo aprovador, aprovadores alternativos de fase 2 |
| 18 | Agora tem acesso a *[access_package]* | Este e-mail será enviado para os utilizadores finais para começarem a utilizar o seu acesso. | Requerente |
| 19 | Prolongar o acesso a *[access_package]* até *[data]* | Este e-mail será enviado aos utilizadores finais antes do seu acesso expirar. | Requerente |
| 20 | O acesso terminou para *[access_package]* | Este e-mail será enviado aos utilizadores finais após o seu acesso expirar. | Requerente |

### <a name="access-request-emails"></a>E-mails de pedido de acesso

Quando um solicitador submeter um pedido de acesso a um pacote de acesso configurado para exigir aprovação, todos os aprovadores adicionados à apólice receberão uma notificação de e-mail com detalhes do pedido. Os detalhes no e-mail incluem: organização de nome do solicitador, e justificação do negócio; e a data de início e fim de acesso solicitada (se fornecida). Os detalhes também incluirão quando o pedido foi apresentado e quando o pedido expirará.

O e-mail inclui um link que os aprovadores podem clicar para ir ao Meu Acesso para aprovar ou negar o pedido de acesso. Aqui está uma notificação por e-mail de amostra que é enviada para o primeiro aprovador ou segundo aprovador (se a aprovação em 2 fases estiver habilitada) para completar um pedido de acesso:

![Aprovar pedido de acesso ao pacote de e-mail](./media/entitlement-management-shared/approver-request-email.png)

Os aprovadores também podem receber um e-mail de lembrete. O e-mail pede ao aprovador que tome uma decisão sobre o pedido. Aqui está uma amostra de notificação de e-mail que o aprovador recebe para lembrá-los de tomar medidas:

![Enviar por email o pedido de acesso ao lembrete](./media/entitlement-management-process/approver-access-request-reminder-email.png)

### <a name="alternate-approvers-request-emails"></a>Aprovadores alternativos solicitam e-mails

Se a definição de aprovação alternativa estiver ativada e o pedido ainda estiver pendente, será reencaminhado. Os aprovadores alternativos receberão um e-mail para aprovar ou negar o pedido. Pode ativar os aprovadores alternativos na fase 1 e na fase 2. Aqui está uma amostra de e-mail da notificação que os aprovadores alternativos recebem:

![Aprovadores alternativos solicitam e-mail](./media/entitlement-management-process/alternate-approver-email-fwd-request.png)

Tanto o aprovador como os aprovadores alternativos podem aprovar ou negar o pedido.

### <a name="approved-or-denied-emails"></a>E-mails aprovados ou negados

 Quando um aprovador recebe um pedido de acesso submetido por um solicitador, pode aprovar ou negar o pedido de acesso. O aprovador precisa de acrescentar uma justificação comercial para a sua decisão. Aqui está uma amostra de e-mail enviada aos aprovadores e aprovadores alternativos após a aprovação de um pedido:

![Pedido aprovado para aceder ao pacote de e-mail](./media/entitlement-management-process/approver-request-email-approved.png)

Quando um pedido de acesso é aprovado, e o seu acesso é aprovisionado, é enviada uma notificação por e-mail ao solicitador de que agora têm acesso ao pacote de acesso. Aqui está uma notificação por e-mail de amostra que é enviada a um solicitador quando lhes é concedido acesso a um pacote de acesso:

![E-mail de pedido aprovado ou de acesso](./media/entitlement-management-process/requestor-email-approved.png)

Quando um pedido de acesso é negado, uma notificação por e-mail é enviada ao solicitador. Aqui está uma notificação por e-mail de amostra que é enviada a um solicitador quando o seu pedido de acesso é negado:

![Pedido de pedido negado e-mail](./media/entitlement-management-process/requestor-email-denied.png)

### <a name="2-stage-approval-access-request-emails"></a>E-mails de pedido de acesso à aprovação em 2 fases

Se estiver ativada a aprovação em duas fases, pelo menos dois aprovadores devem aprovar o pedido, um de cada fase, antes de o solicitador poder ter acesso.

Durante a fase 1, o primeiro aprovador receberá o e-mail do pedido de acesso e tomará uma decisão. Se aprovarem o pedido, todos os primeiros aprovadores e aprovadores suplentes na fase 1 (se a escalada for ativada) receberão a notificação de que a fase 1 está completa. Aqui está uma amostra de e-mail da notificação que é enviada quando a fase 1 está completa:

![E-mail de pedido de acesso em 2 estágios](./media/entitlement-management-process/approver-request-email-2stage.png)

Após o primeiro ou suplente aprovar o pedido na fase 1, começa a fase 2. Durante a fase 2, o segundo aprovador receberá o e-mail de notificação do pedido de acesso. Após o segundo aprovante ou os aprovadores suplentes na fase 2 (se for ativada a escalada) decidirem aprovar ou negar o pedido, os e-mails de notificação são enviados para os primeiros e segundos aprovadores, bem como todos os aprovadores suplentes na fase 1 e 2, bem como o solicitador.

### <a name="expired-access-request-emails"></a>E-mails de pedido de acesso expirados

Os pedidos de acesso podem expirar se nenhum aprovador tiver aprovado ou negado o pedido. 

Quando o pedido atingir a data de validade configurada e expirar, deixará de poder ser aprovado ou negado pelos aprovadores. Aqui está uma amostra de e-mail da notificação enviada a todos os primeiros, segundo (se a aprovação em 2 fases estiver ativada) e os aprovadores alternativos:

![Os aprovadores expiraram o pedido de acesso e-mail](./media/entitlement-management-process/approver-request-email-expired.png)

É também enviada uma notificação por e-mail ao solicitador, notificando-os de que o seu pedido de acesso expirou e que necessita de reenviar o pedido de acesso. O seguinte diagrama mostra a experiência do solicitador e as notificações por e-mail que recebem quando solicitam o alargamento do acesso:

![Solicitador prolongar o fluxo do processo de acesso](./media/entitlement-management-process/requestor-expiration-request-flow.png) 

Aqui está uma notificação por e-mail de amostra que é enviada a um solicitador quando o seu pedido de acesso expirou:

![Pedido de pedido expirou e-mail de pedido de acesso](./media/entitlement-management-process/requestor-email-request-expired.png)

## <a name="next-steps"></a>Passos seguintes

- [Solicitar acesso a um pacote de acesso](entitlement-management-request-access.md)
- [Aprovar ou negar pedidos de acesso](entitlement-management-request-approve.md)
