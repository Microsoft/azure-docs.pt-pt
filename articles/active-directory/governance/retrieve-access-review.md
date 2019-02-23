---
title: Obter resultados de revisão de acesso para grupos ou aplicações nas revisões de acesso do Azure AD | Documentos da Microsoft
description: Saiba como obter resultados de revisão de acesso para membros do grupo ou de acesso da aplicação nas revisões de acesso do Azure AD.
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
ms.openlocfilehash: f5a422f619c4ef63da59516ff2748683eb0f7f70
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56726694"
---
# <a name="retrieve-access-review-results-for-groups-or-applications-in-azure-ad-access-reviews"></a>Obter resultados de revisão de acesso para grupos ou aplicações nas revisões de acesso do Azure AD

Os administradores podem utilizar o Azure Active Directory (Azure AD) para [criar uma revisão de acesso](create-access-review.md) para membros do grupo ou os utilizadores atribuídos a uma aplicação.  Um utilizador que tenha a função de **Administrador Global**, **Administrador de Conta de Segurança**, **Administrador de Segurança** ou **Leitor de Segurança** também pode ler os resultados de uma revisão de acesso.  Para atribuir utilizadores a uma dessas funções, um Administrador com Função Privilegiada pode utilizar o Azure AD PIM para tornar um utilizador elegível para ativar a função ou um Administrador Global pode permanentemente [atribuir um utilizador à função](../fundamentals/active-directory-users-assign-role-azure-portal.md).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="locating-an-access-review"></a>Localizar uma revisão de acesso

Se souber que programa contém a revisão de acesso, vá para a página de revisão de acesso, selecione **Programas**e selecione o programa que contém o controlo de revisão de acesso.  Em seguida, selecione **Controlos** e escolha o controlo de revisão de acesso. Se existirem vários controlos no programa, pode filtrar os controlos de um tipo específico e ordená-los pelo respetivo estado. Também pode procurar pelo nome do controlo de revisão de acesso ou pelo nome a apresentar do proprietário que a criou. 

Se não souber que programa contém a revisão de acesso, vá para a página de revisão de acesso e selecione **Controlos**.  Pode filtrar por controlos de um tipo específico e ordenar pelo respetivo estado, e também pode procurar pelo nome do controlo de revisão ou pelo nome a apresentar do proprietário que a criou. 

## <a name="retrieving-the-results-for-a-one-time-access-review"></a>Obter os resultados de uma revisão de acesso único

Se o tipo de periodicidade de revisão for uma vez, o progresso de uma revisão de acesso ativa e os resultados de uma revisão de acesso concluída podem ser obtidos a partir da secção **Resultados**.  Pode escrever o nome a apresentar ou o nome principal de um utilizador cujo acesso está a ser revisto para ver apenas o acesso desse utilizador.  Para obter todos os resultados de uma revisão de acesso concluída, clique no botão **Transferir**.

## <a name="retrieving-the-results-for-multiple-instances-of-a-recurring-access-review"></a>Obter os resultados de múltiplas instâncias de uma revisão periódica de acesso

Para ver o progresso de uma revisão de acesso ativa que seja recorrente, clique na secção **Resultados**.  Pode escrever o nome a apresentar ou o nome principal de um utilizador cujo acesso esteja a ser revisto.

Para ver os resultados de uma instância concluída de uma revisão de acesso que seja recorrente, selecione **Rever histórico** e, em seguida, selecione a instância específica na lista de instâncias de revisão de acesso concluídas, com base na data de início e de fim da instância.   Os resultados desta instância podem ser obtidos a partir da secção **Resultados**.  Pode escrever o nome a apresentar ou o nome principal de um utilizador cujo acesso está a ser revisto para ver apenas o acesso desse utilizador.  Para obter todos os resultados da instância concluída de uma revisão de acesso concluída, clique no botão **Transferir**.


## <a name="removing-users-from-an-access-review"></a>Remover utilizadores de uma revisão de acesso

Por predefinição, um utilizador eliminado permanecerá eliminado no Azure AD para 30 dias, durante o qual pode ser restaurado por um administrador, se necessário.  Após 30 dias, esse utilizador é eliminado permanentemente.  Além disso, através do portal do Azure Active Directory, um Administrador Global pode explicitamente [eliminar permanentemente um utilizador recentemente eliminado](../fundamentals/active-directory-users-restore.md) antes desse período de tempo ser atingido.  Depois de um utilizador ser eliminado de maneira permanente, subsequentemente os dados sobre esse utilizador serão removidos das revisões de acesso ativas.  As informações de auditoria sobre os utilizadores eliminados permanecem no registo de auditoria.

## <a name="next-steps"></a>Passos Seguintes

- [Gerir o acesso de utilizador com as revisões de acesso do Azure AD](manage-user-access-with-access-reviews.md)
- [Gerir o acesso de convidado com as revisões de acesso do Azure AD](manage-guest-access-with-access-reviews.md)
- [Gerir programas e controlos de revisões de acesso do Azure AD](manage-programs-controls.md)
- [Criar uma revisão de acesso de grupos ou aplicações](create-access-review.md)
- [Criar uma revisão de acesso de utilizadores numa função administrativa do Azure AD](../privileged-identity-management/pim-how-to-start-security-review.md)


