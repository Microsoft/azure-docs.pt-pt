---
title: Gerir o acesso ao utilizador com comentários de acesso - Azure AD
description: Saiba como gerir o acesso dos utilizadores, como a associação a um grupo ou a atribuição a uma aplicação, com as revisões de acesso do Azure Active Directory
services: active-directory
documentationcenter: ''
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 06/21/2018
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: cc12b4cb7e97a0808405baebc64ca83cdb742bf1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94696953"
---
# <a name="manage-user-access-with-azure-ad-access-reviews"></a>Gerir o acesso de utilizador com as revisões de acesso do Azure AD

Com o Azure Active Directory (Azure AD), pode facilmente garantir que os utilizadores têm acesso adequado. Pode pedir aos utilizadores ou a um decisor para participar na revisão de acesso e voltar a certificar (ou confirmar) o acesso dos utilizadores. Os revisores podem dar o seu parecer relativamente à necessidade de acesso contínuo de cada utilizador, com base nas sugestões do Azure AD. Quando a revisão de acesso estiver concluída, pode introduzir as alterações necessárias e remover o acesso dos utilizadores que já não precisam dele.

> [!NOTE]
> Se quiser rever apenas o acesso dos utilizadores convidados, sem abranger a revisão de acesso de todos os tipos de utilizador, veja [Gerir o acesso de utilizadores convidados com revisões de acesso](manage-guest-access-with-access-reviews.md). Se quiser rever a associação do utilizador a funções administrativas como a de administrador global, veja [Iniciar uma revisão de acesso no Azure AD Privileged Identity Management](../privileged-identity-management/pim-how-to-start-security-review.md).

## <a name="prerequisites"></a>Pré-requisitos

- Azure AD Premium P2

Para mais informações, consulte [os requisitos da Licença.](access-reviews-overview.md#license-requirements)

## <a name="create-and-perform-an-access-review"></a>Criar e realizar uma revisão de acesso

Uma revisão de acesso pode ter um ou mais utilizadores como revisores.  

1. Selecione um grupo no Azure AD que tenha um ou mais membros. Ou selecione uma aplicação ligada ao Azure AD que tenha um ou mais utilizadores atribuídos à mesma. 

2. Decida se quer que cada utilizador faça a revisão do seu próprio acesso ou se quer ter um ou mais utilizadores incumbidos de rever o acesso de todos os utilizadores.

3. Numa das seguintes funções: administrador global, administrador de utilizador ou (Preview) um M365 ou AAD Security Group proprietário do grupo a rever, vá à [página de Governança de Identidade](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).

4. Crie a revisão de acesso. Para obter mais informações, consulte [Criar uma revisão de acesso de grupos ou aplicações.](create-access-review.md)

5. Quando a revisão de acesso começar, peça aos revisores que dêem entrada. Por padrão, cada um recebe um e-mail da Azure AD com um link para o painel de acesso, onde analisam o [acesso a grupos ou aplicações.](perform-access-review.md)

6. Se os revisores não tiverem dado o seu parecer, pode pedir ao Azure AD para lhes enviar um lembrete. Por predefinição, o Azure AD envia automaticamente um lembrete aos revisores que ainda não responderam a meio do prazo para a data de fim.

7. Depois de os revisores darem o respetivo parecer, pare a revisão de acesso e aplique as alterações. Para mais informações, consulte [Complete uma revisão de acesso de grupos ou aplicações.](complete-access-review.md)


## <a name="next-steps"></a>Passos seguintes

[Criar uma revisão de acesso de grupos ou aplicações](create-access-review.md)




