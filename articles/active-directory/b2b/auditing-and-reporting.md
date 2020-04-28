---
title: Auditoria e reportagem de um utilizador de colaboração B2B - Azure AD
description: As propriedades dos utilizadores convidados são configuráveis na colaboração Azure Ative Directory B2B
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74273299"
---
# <a name="auditing-and-reporting-a-b2b-collaboration-user"></a>Auditoria e comunicação de um utilizador de colaboração B2B
Com os utilizadores convidados, tem capacidades de auditoria semelhantes às dos utilizadores membros. 

## <a name="access-reviews"></a>Revisões de acesso
Pode utilizar avaliações de acesso para verificar periodicamente se os utilizadores ainda precisam de acesso aos seus recursos. A funcionalidade **de avaliações** de acesso está disponível no **Diretório Ativo Azure** no âmbito **da Manage** > **Organizational Relationships**. (Também pode pesquisar "avaliações de acesso" de **todos os serviços** do portal Azure.) Para aprender a usar as avaliações de acesso, consulte Gerir o acesso dos [hóspedes com avaliações de acesso a AD Azure](../governance/manage-guest-access-with-access-reviews.md).

## <a name="audit-logs"></a>Registos de auditoria

Os registos de auditoria da AD Azure fornecem registos de sistemas e atividades de utilizadores, incluindo atividades iniciadas pelos utilizadores convidados. Para aceder a registos de auditoria, no **Diretório Ativo Azure,** sob **monitorização,** selecione registos de **auditoria.** Aqui está um exemplo da história do convite e da redenção do convite Sam Oogle:

![Screenshot mostrando e exemplo de saída de registo de auditoria](./media/auditing-and-reporting/audit-log.png)

Você pode mergulhar em cada um destes eventos para obter os detalhes. Por exemplo, vamos ver os detalhes de aceitação.

![Screenshot mostrando e exemplo de saída de detalhes da atividade](./media/auditing-and-reporting/activity-details.png)

Também pode exportar estes registos a partir de Azure AD e usar a ferramenta de reporte à sua escolha para obter relatórios personalizados.

### <a name="next-steps"></a>Passos seguintes

- [Propriedades do utilizador de colaboração B2B](user-properties.md)

