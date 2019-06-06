---
title: Gerir o acesso de utilizador com as revisões de acesso - Azure Active Directory | Documentos da Microsoft
description: Saiba como gerir o acesso dos utilizadores, como a associação a um grupo ou a atribuição a uma aplicação, com as revisões de acesso do Azure Active Directory
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 06/21/2018
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9d873f1dace3e3be6a8767e77d57db5ac554afd7
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66473801"
---
# <a name="manage-user-access-with-azure-ad-access-reviews"></a>Gerir o acesso de utilizador com as revisões de acesso do Azure AD

Com o Azure Active Directory (Azure AD), pode facilmente garantir que os utilizadores têm acesso adequado. Pode pedir aos utilizadores ou a um decisor para participar na revisão de acesso e voltar a certificar (ou confirmar) o acesso dos utilizadores. Os revisores podem dar o seu parecer relativamente à necessidade de acesso contínuo de cada utilizador, com base nas sugestões do Azure AD. Quando a revisão de acesso estiver concluída, pode introduzir as alterações necessárias e remover o acesso dos utilizadores que já não precisam dele.

> [!NOTE]
> Se quiser rever apenas o acesso dos utilizadores convidados, sem abranger a revisão de acesso de todos os tipos de utilizador, veja [Gerir o acesso de utilizadores convidados com revisões de acesso](manage-guest-access-with-access-reviews.md). Se quiser rever a associação do utilizador a funções administrativas como a de administrador global, veja [Iniciar uma revisão de acesso no Azure AD Privileged Identity Management](../privileged-identity-management/pim-how-to-start-security-review.md).

## <a name="prerequisites"></a>Pré-requisitos

- Azure AD Premium P2

Para obter mais informações, consulte [os utilizadores que têm de ter licenças?](access-reviews-overview.md#which-users-must-have-licenses).

## <a name="create-and-perform-an-access-review"></a>Criar e realizar uma revisão de acesso

Uma revisão de acesso pode ter um ou mais utilizadores como revisores.  

1. Selecione um grupo no Azure AD que tenha um ou mais membros. Ou selecione uma aplicação ligada ao Azure AD que tenha um ou mais utilizadores atribuídos à mesma. 

2. Decida se quer que cada utilizador faça a revisão do seu próprio acesso ou se quer ter um ou mais utilizadores incumbidos de rever o acesso de todos os utilizadores.

3. Como um administrador global ou administrador de utilizadores, vá para o [página de governação de identidade](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).

4. Crie a revisão de acesso. Para obter mais informações, consulte [criar uma revisão de acesso de grupos ou aplicações](create-access-review.md).

5. Quando a revisão de acesso é iniciado, pedir os revisores para dar entrada. Por predefinição, cada um deles recebe um e-mail do Azure AD com uma ligação para o painel de acesso, onde eles [rever o acesso a grupos ou aplicações](perform-access-review.md).

6. Se os revisores não tiverem dado o seu parecer, pode pedir ao Azure AD para lhes enviar um lembrete. Por predefinição, o Azure AD envia automaticamente um lembrete aos revisores que ainda não responderam a meio do prazo para a data de fim.

7. Depois de os revisores darem o respetivo parecer, pare a revisão de acesso e aplique as alterações. Para obter mais informações, consulte [concluir uma revisão de acesso de grupos ou aplicações](complete-access-review.md).


## <a name="next-steps"></a>Passos Seguintes

[Criar uma revisão de acesso de grupos ou aplicações](create-access-review.md)




