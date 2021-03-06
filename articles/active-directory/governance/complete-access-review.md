---
title: Complete uma revisão de acesso de grupos & aplicações - Azure AD
description: Saiba como completar uma revisão de acesso dos membros do grupo ou acesso a aplicações em avaliações de acesso ao Azure Ative.
services: active-directory
documentationcenter: ''
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 02/08/2021
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: e31b9ce115ebe0723a9d082aaddd8056e486ed27
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102176982"
---
# <a name="complete-an-access-review-of-groups-and-applications-in-azure-ad-access-reviews"></a>Complete uma revisão de acesso de grupos e aplicações em avaliações de acesso Azure AD

Como administrador, [cria uma revisão de acesso de grupos ou aplicações](create-access-review.md) e revisores [efetuam a revisão de acesso](perform-access-review.md). Este artigo descreve como ver os resultados da revisão de acesso e aplicar os resultados.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Azure AD Premium P2
- Administrador global, administrador de utilizador, administrador de segurança ou leitor de segurança

Para mais informações, consulte [os requisitos da Licença.](access-reviews-overview.md#license-requirements)

## <a name="view-an-access-review"></a>Ver uma análise de acesso

Pode acompanhar o progresso à medida que os revisores completarem as suas avaliações.

1. Inscreva-se no portal Azure e abra a [página de Governação da Identidade](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).

1. No menu esquerdo, clique nas **avaliações de Acesso**.

1. Na lista, clique numa análise de acesso.

    Para ver futuras instâncias de uma análise de acesso, navegue para a revisão de acesso e selecione avaliações agendadas.

    Na página **'Visão Geral',** pode ver o progresso da instância atual. Nenhum direito de acesso é alterado no diretório até que a revisão esteja concluída.

     ![Revisão de todos os grupos da empresa](./media/complete-access-review/all-company-group.png)

    Todas as lâminas em Corrente só são visualizais durante a duração de cada instância de revisão. 

    A página Resultados fornece mais informações sobre cada utilizador em análise no caso, incluindo a capacidade de parar, redefinir e descarregar resultados.

    ![Reveja o acesso dos hóspedes em todos os grupos Microsoft 365](./media/complete-access-review/all-company-group-results.png)


    Se estiver a ver uma análise de acesso que revê o acesso dos hóspedes em todos os grupos microsoft 365, a lâmina de visão geral lista cada grupo na revisão. 
   
    ![rever acesso de hóspedes em todos os grupos Microsoft 365](./media/complete-access-review/review-guest-access-across-365-groups.png)

    Clique num grupo para ver o progresso da revisão nesse grupo, bem como para Parar, Redefinir, Aplicar e Eliminar.

   ![rever o acesso dos hóspedes em todos os grupos microsoft 365 em detalhe](./media/complete-access-review/progress-group-review.png)

1. Se pretender interromper uma revisão de acesso antes de atingir a data de fim prevista, clique no botão **Stop.**

    Quando parar uma revisão, os revisores deixarão de poder dar respostas. Não pode reiniciar uma revisão depois de parar.

1. Se já não estiver interessado na revisão de acesso, pode eliminá-la clicando no botão **Eliminar.**

## <a name="apply-the-changes"></a>Aplicar as alterações

Se **o Auto aplicar resultados ao recurso** foi ativado com base nas suas seleções nas **definições de conclusão,** a aplicação automática será executada após a data de fim da revisão ou quando parar manualmente a revisão.

Se **o Auto aplicar resultados a recursos** não foi habilitado para a revisão, navegue para Rever **Histórico** em **Série** após o fim da duração da revisão ou a revisão foi interrompida mais cedo, e clique na instância da revisão que gostaria de aplicar.

![Aplicar alterações na revisão de acesso](./media/complete-access-review/apply-changes.png)

Clique **em Aplicar** para aplicar manualmente as alterações. Se o acesso de um utilizador foi negado na revisão, quando clica em **Aplicar,** o Azure AD remove a sua adesão ou atribuição de candidaturas.

![Aplicar botão de alteração de revisão de acesso](./media/complete-access-review/apply-changes-button.png)


O estado da revisão passará de **Concluído** através de estados intermédios como **a Aplicação** e, finalmente, o Resultado do Estado **aplicado.** Deverá esperar ver utilizadores negados, caso existam, a serem removidos da subscrição do grupo ou da atribuição de candidaturas em poucos minutos.

A aplicação manual ou automática dos resultados não tem efeito num grupo originário de um diretório no local ou de um grupo dinâmico. Se quiser alterar um grupo que tenha origem no local, faça o download dos resultados e aplique essas alterações à representação do grupo nesse diretório.

## <a name="retrieve-the-results"></a>Obter os resultados

Para ver os resultados de uma revisão de acesso único, clique na página **Resultados.** Para visualizar apenas o acesso de um utilizador, na caixa De Busca, digite o nome de exibição ou o nome principal do utilizador de um utilizador cujo acesso foi revisto.

![Recuperar resultados para uma revisão de acesso](./media/complete-access-review/retrieve-results.png) 

Para ver o progresso de uma revisão de acesso ativa que é recorrente, clique na página **Resultados.**

Para visualizar os resultados de uma instância completa de uma revisão de acesso que é recorrente, clique no **histórico de Revisão,** em seguida, selecione a instância específica da lista de instâncias de revisão de acesso concluída, com base na data de início e fim da instância. Os resultados deste caso podem ser obtidos na página **Resultados.**

Para obter todos os resultados de uma revisão de acesso, clique no botão **Descarregar.** O ficheiro CSV resultante pode ser visualizado no Excel ou em outros programas que abram ficheiros CSV codificados UTF-8.

## <a name="remove-users-from-an-access-review"></a>Remover os utilizadores de uma revisão de acesso

 Por predefinição, um utilizador eliminado permanecerá eliminado no Azure AD para 30 dias, durante o qual pode ser restaurado por um administrador, se necessário.  Após 30 dias, esse utilizador é eliminado permanentemente.  Além disso, através do portal do Azure Active Directory, um Administrador Global pode explicitamente [eliminar permanentemente um utilizador recentemente eliminado](../fundamentals/active-directory-users-restore.md) antes desse período de tempo ser atingido.  Depois de um utilizador ser eliminado de maneira permanente, subsequentemente os dados sobre esse utilizador serão removidos das revisões de acesso ativas.  As informações de auditoria sobre os utilizadores eliminados permanecem no registo de auditoria.

## <a name="next-steps"></a>Passos seguintes

- [Gerir o acesso de utilizador com as revisões de acesso do Azure AD](manage-user-access-with-access-reviews.md)
- [Gerir o acesso de convidado com as revisões de acesso do Azure AD](manage-guest-access-with-access-reviews.md)
- [Criar uma revisão de acesso de grupos ou aplicações](create-access-review.md)
- [Criar uma revisão de acesso de utilizadores numa função administrativa do Azure AD](../privileged-identity-management/pim-how-to-start-security-review.md)
