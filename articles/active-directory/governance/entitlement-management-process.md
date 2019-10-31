---
title: Solicitar notificações de processo e email no gerenciamento de direitos do Azure AD-Azure Active Directory
description: Saiba mais sobre o processo de solicitação de um pacote do Access e quando as notificações por email são enviadas no gerenciamento de direitos de Azure Active Directory.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/30/2019
ms.author: ajburnle
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: e34d2c69cc808552a3b0c604804f3cd2597b379b
ms.sourcegitcommit: fa5ce8924930f56bcac17f6c2a359c1a5b9660c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2019
ms.locfileid: "73200020"
---
# <a name="request-process-and-email-notifications-in-azure-ad-entitlement-management"></a>Solicitar notificações de processo e email no gerenciamento de direitos do Azure AD

Quando um usuário envia uma solicitação para um pacote do Access, um processo começa a entregar essa solicitação de acesso. O gerenciamento de direitos do Azure AD envia notificações por email a Aprovadores e solicitantes quando ocorrem eventos de chave durante o processo. Este artigo descreve o processo de solicitação e as notificações de email que são enviadas.

## <a name="request-process"></a>Processo de solicitação

Um usuário que precisa acessar um pacote do Access pode enviar uma solicitação de acesso. Dependendo da configuração da política, a solicitação pode exigir uma aprovação. Quando uma solicitação é aprovada, um processo começa a atribuir o acesso do usuário a cada recurso no pacote do Access. O diagrama a seguir mostra uma visão geral do processo e os diferentes Estados:

![Diagrama de processo de aprovação](./media/entitlement-management-process/request-process.png)

| Estado | Descrição |
| --- | --- |
| Enviada | O usuário envia uma solicitação. |
| Aprovação pendente | Se a política para um pacote de acesso exigir aprovação, uma solicitação será movida para aprovação pendente. |
| Fora do prazo | Se nenhum Aprovador aprovar uma solicitação dentro do tempo limite da solicitação de aprovação, a solicitação expirará. Para tentar novamente, o usuário precisará reenviar a solicitação. |
| Nega | O aprovador nega uma solicitação. |
| Aprovado | O aprovador aprova uma solicitação. |
| Lucrativ | O usuário **não** recebeu acesso a todos os recursos no pacote de acesso. Se esse for um usuário externo, talvez o usuário ainda não tenha acessado o diretório de recursos e aceitou o prompt de consentimento. |
| Entregue | O usuário recebeu acesso a todos os recursos no pacote de acesso. |
| Acesso estendido | Se as extensões forem permitidas na política, o usuário estendeu a atribuição. |
| Acesso expirado | O acesso do usuário ao pacote de acesso expirou. Para obter acesso novamente, o usuário precisará enviar uma solicitação. |

## <a name="email-notifications"></a>Notificações por e-mail

Se você for um aprovador, receberá notificações por email quando precisar aprovar uma solicitação de acesso e quando uma solicitação de acesso tiver sido concluída. Se você for um solicitante, receberá notificações por email que indicam o status de sua solicitação.

Os diagramas a seguir mostram quando essas notificações de email são enviadas para os aprovadores ou para o solicitante. Faça referência à [tabela de notificações de email](entitlement-management-process.md#email-notifications-table) para localizar o número correspondente às notificações de email exibidas nos diagramas.

### <a name="primary-approvers-and-alternate-approvers"></a>Aprovadores primários e aprovadores alternativos
O diagrama a seguir mostra a experiência de aprovadores primários e aprovadores alternativos e as notificações por email que eles recebem durante o processo de solicitação:

![Fluxo de processo de aprovadores primários e alternativos](./media/entitlement-management-process/primary-approvers-and-alternate-with-escalation-flow.png)

### <a name="requestors"></a>Requerentes
O diagrama a seguir mostra a experiência de solicitantes e as notificações por email recebidas durante o processo de solicitação:

![Fluxo do processo do solicitante](./media/entitlement-management-process/requestor-approval-and-expiration-request-flow.png)

### <a name="email-notifications-table"></a>Tabela de notificações de email
A tabela a seguir fornece mais detalhes sobre cada uma dessas notificações por email. Para gerenciar esses emails, você pode usar regras. Por exemplo, no Outlook, você pode criar regras para mover os emails para uma pasta se o assunto contiver palavras desta tabela:

| # | Assunto do email | Quando enviado | Enviado para |
| --- | --- | --- | --- |
| 1 | Ação necessária: aprovar ou negar a solicitação encaminhada por *[data]* | Este email será enviado aos aprovadores alternativos do estágio 1 (depois que a solicitação for escalonada) para executar uma ação. | Aprovação alternativa do estágio 1 |
| 2 | Ação necessária: aprovar ou negar solicitação por *[data]* | Este email será enviado para os aprovadores primários do estágio 1, se o escalonamento estiver desabilitado, para executar uma ação. | Principal Aprovador de estágio 1 |
| 3 | Lembrete: aprovar ou negar a solicitação em *[data]* para *[solicitante]* | Este email de lembrete será enviado para os aprovadores primários do estágio 1, se o escalonamento estiver desabilitado, para tomar uma ação, somente quando ainda não tiverem feito uma ação. | Principal Aprovador de estágio 1 |
| 4 | Aprovar ou negar a solicitação em *[time]* em *[data]* | Este email será enviado para os aprovadores primários do estágio 1 (se o escalonamento estiver habilitado) para executar uma ação. | Principal Aprovador de estágio 1 |
| 5 | Lembrete necessário da ação: aprovar ou negar a solicitação por *[data]* para *[solicitante]* | Este email de lembrete será enviado para os aprovadores primários do estágio 1, se o escalonamento estiver habilitado, para tomar uma ação, somente quando ainda não tiverem feito a ação. | Principal Aprovador de estágio 1 |
| 6 | A solicitação expirou para *[access_package]* | Este email será enviado para os aprovadores primários do estágio 1 e/ou para os aprovadores alternativos do estágio 1, de uma solicitação de estágio único ou de vários estágios, depois que a solicitação tiver expirado. | Principal aprovador da fase 1, Aprovador alternativo do estágio 1 |
| 7 | Solicitação aprovada para *[solicitante]* para *[access_package]* | Este email será enviado para os aprovadores primários do estágio 1 e/ou para os aprovadores alternativos do estágio 1, após a conclusão de uma solicitação. | Principal aprovador da fase 1, Aprovador alternativo do estágio 1 |
| 8 | Solicitação aprovada para *[solicitante]* para *[access_package]* | Este email será enviado para os aprovadores primários do estágio 1 e/ou para os aprovadores alternativos do estágio 1, de uma solicitação de dois estágios, somente quando o estágio-1 for aprovado. | Principal aprovador da fase 1, Aprovador alternativo do estágio 1 |
| 9 | Solicitação negada a *[access_package]* | Este email será enviado para o solicitante somente quando sua solicitação for negada | Solicitante |
| 10 | Sua solicitação expirou para *[access_package]* | Este email será enviado para o solicitante no final do estágio-1, de uma solicitação de estágio único ou de vários estágios, depois que a solicitação tiver expirado. | Solicitante |
| 18 | Agora você tem acesso ao *[access_package]* | Este email será enviado aos usuários finais para começar a usar seu acesso. | Solicitante |
| 19 | Estender o acesso para *[access_package]* por *[data]* | Este email será enviado aos usuários finais antes que seu acesso expire. | Solicitante |
| 20 | O acesso foi encerrado para *[access_package]* | Este email será enviado aos usuários finais depois que o acesso expirar. | Solicitante |

### <a name="access-request-emails"></a>Emails de solicitação de acesso

Quando um solicitante envia uma solicitação de acesso para um pacote de acesso configurado para exigir aprovação, todos os aprovadores adicionados à política receberão uma notificação por email com detalhes da solicitação. Os detalhes incluem o nome do solicitante, organização, data de início e de término do acesso (se fornecido), justificativa de negócios, quando a solicitação foi enviada e quando a solicitação expirará.

O email inclui um link que os aprovadores podem clicar para acessar o myaccess para aprovar ou negar a solicitação de acesso. Aqui está um exemplo de notificação por email que é enviada para um Aprovador quando um solicitante envia uma solicitação de acesso:

![Aprovar solicitação para acessar email de pacote](./media/entitlement-management-shared/approver-request-email.png)

Os aprovadores primários também enviam uma notificação por email com um lembrete para tomar medidas e tomar uma decisão para a solicitação. Aqui está um email de exemplo dos aprovadores primários de notificação que recebem para lembrá-los de agir:

![Email de solicitação de acesso ao lembrete](./media/entitlement-management-process/approver-access-request-reminder-email.png)

### <a name="alternate-approver-request-emails"></a>Emails de solicitação de aprovador alternativos

Se o encaminhamento para aprovadores alternativos estiver habilitado, de acordo com a política de encaminhamento, se a solicitação ainda estiver pendente, a solicitação será encaminhada. O aprovador alternativo receberá um email de notificação para aprovar ou negar a solicitação. Aqui está um email de exemplo da notificação que os aprovadores alternativos recebem:

![Email de solicitação de aprovador alternativo](./media/entitlement-management-process/alternate-approver-email-fwd-request.png)

Ambos, os aprovadores primários e os aprovadores alternativos podem aprovar ou negar a solicitação.

### <a name="approved-or-denied-emails"></a>Emails aprovados ou negados

Os solicitantes são notificados quando sua solicitação de acesso é aprovada e está disponível para acesso ou quando sua solicitação de acesso é negada. Quando um Aprovador recebe uma solicitação de acesso enviada por um solicitante, ele pode aprovar ou negar a solicitação de acesso. O aprovador precisa adicionar uma justificativa de negócios para sua decisão. Aqui está um email de exemplo enviado aos aprovadores primários ou alternativos depois que uma solicitação é aprovada:

![Examinar o email de solicitação de acesso](./media/entitlement-management-process/approver-request-email-approved.png)

Quando uma solicitação de acesso é aprovada e seu acesso é provisionado, uma notificação por email é enviada ao solicitante que agora tem acesso ao pacote de acesso. Aqui está um exemplo de notificação por email que é enviada para um solicitante quando recebe acesso a um pacote de acesso:

![Email de solicitação de acesso expirado](./media/entitlement-management-process/requestor-email-approved.png)

Quando uma solicitação de acesso é negada, uma notificação por email é enviada ao solicitante. Aqui está um exemplo de notificação por email que é enviada para um solicitante quando sua solicitação de acesso é negada:

![Email de solicitação negada do solicitante](./media/entitlement-management-process/requestor-email-denied.png)

### <a name="expired-access-request-emails"></a>Emails de solicitação de acesso expirados

As solicitações de acesso poderão expirar se nenhum aprovador tiver aprovado ou negado a solicitação. 

Quando a solicitação atingir sua data de expiração configurada e expirar, ela não poderá mais ser aprovada ou negada pelos aprovadores. Aqui está um email de exemplo da notificação enviada para todos os aprovadores primários e alternativos:

 ![Email de solicitação de acesso de aprovadores expirados](./media/entitlement-management-process/approver-request-email-expired.png)

 Uma notificação por email também é enviada ao solicitante, notificando que a solicitação de acesso expirou e que eles precisam reenviar a solicitação de acesso. Aqui está um exemplo de notificação por email que é enviada para um solicitante quando sua solicitação de acesso expirou:

![Email de solicitação de acesso expirado do solicitante](./media/entitlement-management-process/requestor-email-request-expired.png)

## <a name="next-steps"></a>Passos seguintes

- [Solicitar acesso a um pacote de acesso](entitlement-management-request-access.md)
- [Aprovar ou negar solicitações de acesso](entitlement-management-request-approve.md)
