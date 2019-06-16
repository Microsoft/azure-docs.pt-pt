---
title: Concluir uma revisão de acesso de grupos ou aplicações - Azure Active Directory | Documentos da Microsoft
description: Aprenda como concluir uma revisão de acesso de membros do grupo ou o acesso de aplicação nas revisões de acesso do Azure Active Directory.
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
ms.date: 05/22/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: ec3909ffbb624284f999360140b7454098643062
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66473365"
---
# <a name="complete-an-access-review-of-groups-or-applications-in-azure-ad-access-reviews"></a>Concluir uma revisão de acesso de grupos ou as revisões de acesso a aplicações no Azure AD

Como administrador, [criar uma revisão de acesso de grupos ou aplicativos](create-access-review.md) e revisores [realizar a revisão de acesso](perform-access-review.md). Este artigo descreve como ver os resultados da revisão de acesso e aplicar os resultados.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Azure AD Premium P2
- Administrador global, administrador de utilizadores, administrador de segurança ou leitor de segurança

Para obter mais informações, consulte [os utilizadores que têm de ter licenças?](access-reviews-overview.md#which-users-must-have-licenses).

## <a name="view-an-access-review"></a>Ver uma revisão de acesso

Pode monitorizar o progresso conforme os revisores de concluir as revisões.

1. Inicie sessão no portal do Azure e abra o [página de governação de identidade](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).

1. No menu à esquerda, clique em **as revisões de acesso**.

1. Na lista, clique numa revisão de acesso.

    Para ver uma série de acesso revisões, navegue para a revisão de acesso e encontrará futuras ocorrências nas revisões de agendada.

    Sobre o **descrição geral** página, pode ver o progresso. Nenhum direito de acesso forem alterado no diretório até a revisão é concluída.

    ![Progresso de revisões de acesso](./media/complete-access-review/overview-progress.png)

1. Se pretender parar uma revisão de acesso antes que ela chegou a data de fim agendada, clique nas **parar** botão.

    Quando parar uma revisão, os revisores já não será capazes de dar respostas. Não é possível reiniciar uma revisão depois está parado.

1. Se já não estiver interessado na revisão de acesso, pode eliminá-la clicando a **eliminar** botão.

## <a name="apply-the-changes"></a>Aplicar as alterações

Se **automaticamente aplicar resultados ao recurso** foi ativada e com base nas suas seleções **após as definições de conclusão**, auto-aplicam-se irá ser executado após a data de fim a revisão ou quando manualmente para a revisão.

Se **automaticamente aplicar resultados ao recurso** não foi ativada para a revisão, clique em **aplicar** para aplicar as alterações manualmente. Se o acesso de um utilizador foi negado da revisão, quando clica **aplicar**, do Azure AD remove a respetiva atribuição de associação ou aplicação.

![Aplicar alterações de revisão de acesso](./media/complete-access-review/apply-changes.png)

O estado da revisão será alterado de **concluído** através de Estados intermediários como **aplicar** e, finalmente, para o estado **resultado aplicado**. Deve esperar para ver utilizadores sem permissão, caso haja algum, que está a ser removido a atribuição de aplicação ou associação de grupo dentro de alguns minutos.

Um configurado automaticamente aplicar revisão ou por selecionar **aplicar** não tem um efeito num grupo que tem origem num diretório no local ou um grupo dinâmico. Se pretender alterar um grupo que tem origem no local, transferir os resultados e aplicar essas alterações para a representação do grupo nesse diretório.

## <a name="retrieve-the-results"></a>Obter os resultados

Para ver os resultados para uma revisão de acesso monouso, clique nas **resultados** página. Para ver apenas acesso de um utilizador, na caixa de pesquisa, escreva o nome a apresentar ou o nome principal de utilizador de um utilizador cujo acesso foi avaliado.

![Obter resultados para uma revisão de acesso](./media/complete-access-review/retrieve-results.png)

Para ver o progresso de uma revisão de acesso de Active Directory que seja recorrente, clique nas **resultados** página.

Para ver os resultados de uma instância de conclusão de uma revisão de acesso que seja recorrente, clique em **rever histórico**, em seguida, selecione a instância específica da lista de instâncias de revisão de acesso concluída, com base no início da instância e data de fim. Os resultados desta instância podem ser obtidos a partir da **resultados** página.

Para obter todos os resultados de uma revisão de acesso, clique nas **transferir** botão. O ficheiro CSV resultante pode ser visualizado no Excel ou em outros programas que abrem o UTF-8 codificado ficheiros CSV.

## <a name="remove-users-from-an-access-review"></a>Remover utilizadores de uma revisão de acesso

 Por predefinição, um utilizador eliminado permanecerá eliminado no Azure AD para 30 dias, durante o qual pode ser restaurado por um administrador, se necessário.  Após 30 dias, esse utilizador é eliminado permanentemente.  Além disso, através do portal do Azure Active Directory, um Administrador Global pode explicitamente [eliminar permanentemente um utilizador recentemente eliminado](../fundamentals/active-directory-users-restore.md) antes desse período de tempo ser atingido.  Depois de um utilizador ser eliminado de maneira permanente, subsequentemente os dados sobre esse utilizador serão removidos das revisões de acesso ativas.  As informações de auditoria sobre os utilizadores eliminados permanecem no registo de auditoria.

## <a name="next-steps"></a>Passos Seguintes

- [Gerir o acesso de utilizador com as revisões de acesso do Azure AD](manage-user-access-with-access-reviews.md)
- [Gerir o acesso de convidado com as revisões de acesso do Azure AD](manage-guest-access-with-access-reviews.md)
- [Criar uma revisão de acesso de grupos ou aplicações](create-access-review.md)
- [Criar uma revisão de acesso de utilizadores numa função administrativa do Azure AD](../privileged-identity-management/pim-how-to-start-security-review.md)
