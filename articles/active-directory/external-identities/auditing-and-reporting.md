---
title: Auditoria e reporte de um utilizador de colaboração B2B - Azure AD
description: As propriedades dos utilizadores dos hóspedes são configuráveis na colaboração B2B do Azure Ative Directory
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 05/11/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: a805f643e5f93270fe4721f527073baa9a1e4c8d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "87909695"
---
# <a name="auditing-and-reporting-a-b2b-collaboration-user"></a>Auditoria e reporte de um utilizador de colaboração B2B
Com os utilizadores convidados, tem capacidades de auditoria semelhantes às dos utilizadores dos membros. 

## <a name="access-reviews"></a>Revisões de acesso
Pode utilizar comentários de acesso para verificar periodicamente se os utilizadores de hóspedes ainda precisam de acesso aos seus recursos. A funcionalidade **de comentários de Acesso** está disponível no **Azure Ative Directy** no âmbito de avaliações de acesso **de identidades**  >  **externas.** Também pode pesquisar por "comentários de acesso" a partir de **todos os serviços** do portal Azure. Para aprender a usar comentários de acesso, consulte [gerir o acesso dos hóspedes com comentários de acesso AZure.](../governance/manage-guest-access-with-access-reviews.md)

## <a name="audit-logs"></a>Registos de auditoria

Os registos de auditoria da AZure AD fornecem registos de atividades do sistema e do utilizador, incluindo atividades iniciadas pelos utilizadores convidados. Para aceder aos registos de auditoria, no **Azure Ative Directory,** em **Monitorização,** selecione **registos de auditoria**. Aqui está um exemplo da história do convite e redenção do convidado Sam Oogle:

![Screenshot mostrando e exemplo da saída de registo de auditoria](./media/auditing-and-reporting/audit-log.png)

Você pode mergulhar em cada um destes eventos para obter os detalhes. Por exemplo, vamos ver os detalhes da aceitação.

![Screenshot mostrando e exemplo de saída de detalhes de atividade](./media/auditing-and-reporting/activity-details.png)

Também pode exportar estes registos a partir da Azure AD e utilizar a ferramenta de reporte à sua escolha para obter relatórios personalizados.

### <a name="next-steps"></a>Passos seguintes

- [Propriedades do utilizador de colaboração B2B](user-properties.md)

