---
title: Pedir o processo e notificações de e-mail na gestão de direitos do Azure AD (pré-visualização) - Azure Active Directory
description: Saiba mais sobre o processo de pedido para um pacote de acesso e quando são enviadas notificações de e-mail na gestão de direitos do Azure Active Directory (pré-visualização).
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 05/30/2019
ms.author: rolyon
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: aede5e315141251026867f7028ebf989d44da4d5
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66473042"
---
# <a name="request-process-and-email-notifications-in-azure-ad-entitlement-management-preview"></a>Pedir o processo e notificações de e-mail na gestão de direitos do Azure AD (pré-visualização)

> [!IMPORTANT]
> Gestão de direitos do Active Directory (Azure AD) do Azure está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Quando um utilizador submete um pedido para um pacote de acesso, um processo é iniciado para fornecer essa solicitação. A gestão de direitos do Azure AD também envia notificações por e-mail para os aprovadores e requerentes quando ocorrem eventos de chaves durante o processo.

Este artigo descreve o processo de pedido e as notificações de e-mail que são enviadas.

## <a name="request-process"></a>Processo de pedido

Um utilizador que precisa de acesso a um pacote de acesso pode submeter um pedido de acesso. Dependendo da configuração da política, o pedido pode exigir uma aprovação. Quando um pedido for aprovado, um processo é iniciado atribuir o acesso de utilizador para cada recurso do pacote de acesso. O diagrama seguinte mostra uma descrição geral do processo e os Estados diferentes.

![Diagrama do processo de aprovação](./media/entitlement-management-process/request-process.png)

| Estado | Descrição |
| --- | --- |
| Submetido | Utilizador submete um pedido. |
| Aprovação pendente | Se a política para um pacote de acesso necessita de aprovação, move de um pedido para aguardar a aprovação. |
| Fora do prazo | Se não os aprovadores aprovar um pedido dentro do tempo de limite do pedido de aprovação, o pedido expira. Para tentar novamente, o utilizador terá de voltar a submeter o pedido. |
| Negado | Aprovador nega um pedido. |
| Aprovado | Um pedido de aprovação do aprovador. |
| Entrega | O utilizador tem **não** foi atribuído acesso a todos os recursos do pacote de acesso. Se se tratar de um utilizador externo, o utilizador ainda não tem acedidos no diretório de recursos e aceite o pedido de permissões. |
| Entregue | Foi atribuído acesso a todos os recursos no pacote de acesso do utilizador. |
| Acesso estendido | Se as extensões são permitidas na política, o utilizador estendido a atribuição. |
| Acesso expirado | Acesso de utilizador para o pacote de acesso a expirou. Para obter acesso mais uma vez, o utilizador terá que submeter um pedido. |

## <a name="email-notifications"></a>Notificações por e-mail

Se for um aprovador, são enviadas notificações por e-mail quando tem de aprovar um pedido de acesso e quando um pedido de acesso foi concluído. Se for um requerente, são enviadas notificações de e-mail que indicam o estado do seu pedido. O diagrama seguinte mostra quando estas notificações por e-mail é enviado.

![Processo de correio eletrónico de gestão de direitos](./media/entitlement-management-process/email-notifications.png)

A tabela seguinte fornece mais detalhes sobre cada uma dessas notificações de e-mail.

| # | Assunto do e-mail | Quando enviados | Enviado para |
| --- | --- | --- | --- |
| 1 | Ação necessária: Analisar pedido de acesso de *[requerente]* ao *[pacote acesso]* por *[date]* | Quando um solicitante envia uma solicitação para um pacote de acesso | Todos os aprovadores |
| 2 | Ação necessária: Analisar pedido de acesso de *[requerente]* ao *[pacote acesso]* por *[date]* | Tempo limite do pedido de X dias antes da aprovação | Todos os aprovadores |
| 3 | Notificação de estado: *[requerente]* do pedido de acesso ao *[pacote acesso]* expirou | Quando os aprovadores não aprovar ou negar um pedido de acesso dentro da duração do pedido | Requerente |
| 4 | Notificação de estado: *[requerente]* pedido de acesso ao *[pacote acesso]* foi concluída | Quando o primeiro aprovador aprova ou nega um pedido de acesso | Todos os aprovadores |
| 5 | Foi negado acesso a *[pacote acesso]* | Quando um requerente foi negado acesso ao pacote de acesso | Requerente |
| 6 | Agora tem acesso a *[pacote acesso]*  | Quando um solicitante tenha sido concedido acesso a todos os recursos do pacote de acesso | Requerente |
| 7 | O acesso ao *[pacote acesso]* expira em X dia (s) | X dias antes de acesso do requerente para o pacote de acesso expira | Requerente |
| 8 | O acesso ao *[pacote acesso]* expirou | Quando o acesso do requerente a um pacote de acesso expira | Requerente |

### <a name="access-request-emails"></a>Mensagens de e-mail de pedido de acesso

Quando um solicitante envia um pedido de acesso para um pacote de acesso que está configurado para exigir a aprovação, todos os aprovadores configurados na política de recebem uma notificação por e-mail com detalhes do pedido. Detalhes incluem o nome do requerente, organização, aceder a data de início e fim se for fornecido, justificação de negócio, quando o pedido foi submetido, e quando o pedido irá expirar. O e-mail inclui uma ligação em que os aprovadores podem aprovar ou negar o pedido de acesso. Aqui está uma notificação de e-mail de exemplo que é enviada para um aprovador quando um solicitante envia um pedido de acesso.

![E-mail de pedido de acesso de revisão](./media/entitlement-management-shared/email-approve-request.png)

### <a name="approved-or-denied-emails"></a>Mensagens de correio eletrónico aprovadas ou negadas

Requerentes são notificados quando o pedido de acesso é aprovado e disponíveis para acesso, ou quando o pedido de acesso é negado. Quando um aprovador recebe um pedido de acesso enviado por um requerente, pode aprovar ou negar o pedido de acesso. O aprovador tem de adicionar uma justificativa comercial para a sua decisão.

Quando for aprovado um pedido de acesso, gestão de direitos inicia o processo de conceder o acesso de requerente para cada um dos recursos do pacote de acesso. Depois do requerente tem sido concedido acesso a todos os recursos do pacote de acesso, é enviada uma notificação por e-mail para o requerente do que o seu pedido de acesso foi aprovado e se têm agora acesso ao pacote de acesso. Aqui está uma notificação de e-mail de exemplo que é enviada para um requerente quando eles recebem acesso a um pacote de acesso.

Quando um pedido de acesso é negado, é enviada uma notificação de e-mail para o requerente. Aqui está uma notificação de e-mail de exemplo que é enviada para um requerente quando o pedido de acesso é negado.

### <a name="expired-access-request-emails"></a>Expirado mensagens de e-mail de pedido de acesso

Requerentes são notificados quando o pedido de acesso tiver expirado. Quando um solicitante envia um pedido de acesso, o pedido tem uma duração de pedido após a qual expira. Se não houver nenhum aprovadores que submeter uma decisão de aprovar/negar, continua a solicitação permaneça num Estado de aprovação pendentes. Quando o pedido atinge sua duração de expiração configurada, o pedido expirar e já não ser aprovado ou negado por aprovadores. Neste caso, o pedido entra num estado expirado. Já não pode ser aprovado ou negado um pedido expirado. Uma notificação por e-mail é enviada para o requerente do pedido de acesso tiver expirado, e que precisam para submeter novamente o pedido de acesso. Aqui está uma notificação de e-mail de exemplo que é enviada para um requerente quando o pedido de acesso tiver expirado.

![E-mail de pedido de acesso de expiração](./media/entitlement-management-process/email-expired-access-request.png)

## <a name="next-steps"></a>Passos Seguintes

- [Pedir acesso a um pacote de acesso](entitlement-management-request-access.md)
- [Aprovar ou negar pedidos de acesso](entitlement-management-request-approve.md)
