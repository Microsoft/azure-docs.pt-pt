---
title: Complete uma revisão de acesso de grupos & aplicações - Azure AD
description: Saiba como completar uma revisão de acesso aos membros do grupo ou ao acesso à aplicação nas avaliações de acesso ao Diretório Ativo do Azure.
services: active-directory
documentationcenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 07/23/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 266234f2872cfe99509d564c9460bfba4a0e2bf2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75932550"
---
# <a name="complete-an-access-review-of-groups-and-applications-in-azure-ad-access-reviews"></a>Complete uma revisão de acesso de grupos e aplicações em avaliações de acesso a AD Azure

Como administrador, [cria uma revisão de acesso de grupos ou aplicações](create-access-review.md) e revisores [realizam a revisão de acesso](perform-access-review.md). Este artigo descreve como ver os resultados da revisão de acesso e aplicar os resultados.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Azure AD Premium P2
- Administrador global, administrador de utilizador, administrador de segurança ou leitor de segurança

Para mais informações, consulte [os requisitos da Licença.](access-reviews-overview.md#license-requirements)

## <a name="view-an-access-review"></a>Ver uma revisão de acesso

Pode acompanhar o progresso à medida que os revisores completam as suas avaliações.

1. Inscreva-se no portal Azure e abra a [página de Governança de Identidade.](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)

1. No menu esquerdo, clique em comentários de **acesso.**

1. Na lista, clique numa revisão de acesso.

    Para ver uma série de avaliações de acesso, navegue para a revisão de acesso e encontrará as próximas ocorrências em comentários agendados.

    Na página **de Visão Geral,** pode ver o progresso. Nenhum direito de acesso é alterado no diretório até que a revisão esteja concluída.

    ![Acesso avalia progresso](./media/complete-access-review/overview-progress.png)

1. Se pretender parar uma revisão de acesso antes de atingir a data de fim agendada, clique no botão **Stop.**

    Quando parar uma revisão, os revisores deixarão de poder dar respostas. Não pode relançar uma revisão depois de parar.

1. Se já não estiver interessado na revisão de acesso, pode eliminá-la clicando no botão **Eliminar.**

## <a name="apply-the-changes"></a>Aplicar as alterações

Se a **Aplicação automática dos resultados ao recurso** estiver ativada e com base nas suas seleções nas **definições de conclusão,** a aplicação automática será executada após a data final da revisão ou quando parar manualmente a revisão.

Se a aplicação automática **de resultados ao recurso** não estiver ativada para a revisão, clique em **Aplicar** manualmente as alterações. Se o acesso de um utilizador foi negado na revisão, quando clica em **Apply**, o Azure AD remove a sua atribuição de adesão ou aplicação.

![Aplicar alterações na revisão do acesso](./media/complete-access-review/apply-changes.png)

O estado da revisão passará de **Concluído** através de estados intermédios, como **a aplicação** e, finalmente, o resultado do estado **aplicado.** Deverá esperar ver os utilizadores negados, caso exista, a serem removidos da atribuição de pedidos de membros ou de aplicações em poucos minutos.

Uma revisão de aplicação automática configurada ou seleção **apply** não tem um efeito num grupo que se origina num diretório no local ou num grupo dinâmico. Se quiser alterar um grupo originário do local, faça o download dos resultados e aplique essas alterações na representação do grupo nesse diretório.

## <a name="retrieve-the-results"></a>Obter os resultados

Para ver os resultados para uma revisão de acesso única, clique na página **resultados.** Para visualizar apenas o acesso de um utilizador, na caixa de Pesquisa, digite o nome do ecrã ou o nome principal do utilizador de um utilizador cujo acesso foi revisto.

![Recuperar resultados para uma revisão de acesso](./media/complete-access-review/retrieve-results.png)

Para ver o progresso de uma revisão de acesso ativo que é recorrente, clique na página **resultados.**

Para visualizar os resultados de uma instância completa de uma revisão de acesso recorrente, clique no histórico de **revisão,** em seguida, selecione a instância específica da lista de instâncias de revisão de acesso completas, com base na data de início e fim da instância. Os resultados desta ocorrência podem ser obtidos a partir da página **Resultados.**

Para recuperar todos os resultados de uma revisão de acesso, clique no botão **Download.** O ficheiro CSV resultante pode ser visto no Excel ou noutros programas que abrem ficheiros CSV codificados uTF-8.

## <a name="remove-users-from-an-access-review"></a>Remova os utilizadores de uma revisão de acesso

 Por predefinição, um utilizador eliminado permanecerá eliminado no Azure AD para 30 dias, durante o qual pode ser restaurado por um administrador, se necessário.  Após 30 dias, esse utilizador é eliminado permanentemente.  Além disso, através do portal do Azure Active Directory, um Administrador Global pode explicitamente [eliminar permanentemente um utilizador recentemente eliminado](../fundamentals/active-directory-users-restore.md) antes desse período de tempo ser atingido.  Depois de um utilizador ser eliminado de maneira permanente, subsequentemente os dados sobre esse utilizador serão removidos das revisões de acesso ativas.  As informações de auditoria sobre os utilizadores eliminados permanecem no registo de auditoria.

## <a name="next-steps"></a>Passos seguintes

- [Gerir o acesso de utilizador com as revisões de acesso do Azure AD](manage-user-access-with-access-reviews.md)
- [Gerir o acesso de convidado com as revisões de acesso do Azure AD](manage-guest-access-with-access-reviews.md)
- [Criar uma revisão de acesso de grupos ou aplicações](create-access-review.md)
- [Criar uma revisão de acesso de utilizadores numa função administrativa do Azure AD](../privileged-identity-management/pim-how-to-start-security-review.md)
