---
title: Auditando e relatando um usuário de colaboração B2B-Azure AD
description: As propriedades do usuário convidado são configuráveis em Azure Active Directory colaboração B2B
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 74d2473d37dcf0d435ab61763e72a64111845956
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74273299"
---
# <a name="auditing-and-reporting-a-b2b-collaboration-user"></a>Auditando e relatando um usuário de colaboração B2B
Com os usuários convidados, você tem recursos de auditoria semelhantes aos usuários membros. 

## <a name="access-reviews"></a>Revisões de acesso
Você pode usar as revisões de acesso para verificar periodicamente se os usuários convidados ainda precisam de acesso aos seus recursos. O recurso de **revisões de acesso** está disponível em **Azure Active Directory** em **gerenciar** > **relações organizacionais**. (Você também pode Pesquisar "revisões de acesso" de **todos os serviços** no portal do Azure.) Para saber como usar as revisões de acesso, consulte [gerenciar o acesso de convidado com as revisões de acesso do Azure ad](../governance/manage-guest-access-with-access-reviews.md).

## <a name="audit-logs"></a>Registos de auditoria

Os logs de auditoria do Azure AD fornecem registros de atividades do sistema e do usuário, incluindo atividades iniciadas por usuários convidados. Para acessar os logs de auditoria, em **Azure Active Directory**, em **monitoramento**, selecione **logs de auditoria**. Aqui está um exemplo do histórico de convite e resgate de convide o Sam oogle:

![Captura de tela mostrando e exemplo de saída do log de auditoria](./media/auditing-and-reporting/audit-log.png)

Você pode se aprofundar em cada um desses eventos para obter os detalhes. Por exemplo, vamos examinar os detalhes da aceitação.

![Exibição da captura de tela e exemplo de saída de detalhes da atividade](./media/auditing-and-reporting/activity-details.png)

Você também pode exportar esses logs do Azure AD e usar a ferramenta de relatório de sua escolha para obter relatórios personalizados.

### <a name="next-steps"></a>Passos seguintes

- [Propriedades do utilizador de colaboração B2B](user-properties.md)

