---
title: Criar uma revisão de acesso de um pacote de acesso na gestão de direitos Azure AD
description: Saiba como criar uma política de revisão de acesso para pacotes de acesso à gestão de direitos em avaliações de acesso ao Azure Ative Directory (Preview).
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 012eefe9140703a62d7bb1074ab763191a0976cb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87798516"
---
# <a name="create-an-access-review-of-an-access-package-in-azure-ad-entitlement-management"></a>Criar uma revisão de acesso de um pacote de acesso na gestão de direitos Azure AD

Para reduzir o risco de acesso à estagnação, deve permitir revisões periódicas de utilizadores que tenham atribuições ativas a um pacote de acesso na gestão de direitos AZure AD. Pode ativar comentários quando criar um novo pacote de acesso ou editar um pacote de acesso existente. Este artigo descreve como permitir comentários de acesso a pacotes de acesso.

## <a name="prerequisites"></a>Pré-requisitos

Para permitir revisões de pacotes de acesso, deve cumprir os requisitos para criar um pacote de acesso:
- Azure AD Premium P2
- Administrador global, administrador de utilizador, proprietário de catálogo ou gestor de pacotes access

Para mais informações, consulte [os requisitos da Licença.](entitlement-management-overview.md#license-requirements)


## <a name="create-an-access-review-of-an-access-package"></a>Criar uma revisão de acesso de um pacote de acesso

Pode ativar comentários de acesso ao [criar um novo pacote de acesso](entitlement-management-access-package-create.md) ou editar uma política de [pacotes de acesso existente.](entitlement-management-access-package-lifecycle-policy.md) Siga estas etapas para permitir a revisão de acesso de um pacote de acesso:

1. Abra o **separador Lifecycle** para um pacote de acesso e desloque-se até ao **Access Reviews**.

1. Mover as **avaliações de acesso requere** para **alternar**para Sim .

    ![Adicione a revisão de acesso](./media/entitlement-management-access-reviews/access-reviews-pane.png)

1. Especificar a data em que as avaliações começarão ao lado **de Iniciar**.

1. Em seguida, deslove a **frequência de revisão** para **anualmente,** **bianualmente,** **trimestral** ou **mensal.**
Esta definição determina a frequência com que ocorrem comentários de acesso.

1. Defina a **Duração** para definir quantos dias cada revisão da série recorrente estará aberta para a entrada dos revisores. Por exemplo, pode agendar uma revisão anual que começa a 1 de janeiro e está aberta a revisão por 30 dias para que os revisores tenham até ao final do mês para responder.

1. Ao lado **dos Revisores**, selecione **Self-review** se pretender que os utilizadores realizem a sua própria revisão de acesso ou selecione **revisores específicos** se quiser designar um revisor.

    ![Selecione Adicionar revisores](./media/entitlement-management-access-reviews/access-reviews-add-reviewer.png)

1. Se selecionou **revisores específicos,** especifique quais os utilizadores que irão fazer a revisão de acesso:
    1. Selecione **Adicionar revisores**.
    1. No painel **de revisores Select,** procure e selecione o(s) utilizador(s) que pretende ser um revisor.
    1. Quando tiver selecionado o(s) revisor(s), clique no botão **Seleção.**

    ![Especificar os revisores](./media/entitlement-management-access-reviews/access-reviews-select-reviewer.png)

1. Clique **em 'Rever + Criar'** se estiver a criar um novo pacote de acesso ou **atualização** se estiver a editar um pacote de acesso, na parte inferior da página.

## <a name="view-the-status-of-the-access-review"></a>Ver o estado da revisão de acesso

Após a data de início, uma revisão de acesso será listada na secção **de comentários de Acesso.** Siga estes passos para ver o estado de uma revisão de acesso:

1. Na **Governação de Identidade,** clique em **pacotes de Acesso** e, em seguida, selecione o pacote de acesso com o estado de revisão de acesso que pretende verificar.   

1. Assim que estiver na visão geral do pacote de acesso, clique em **comentários de Acesso** no menu esquerdo.
    
    ![Selecione comentários de acesso](./media/entitlement-management-access-reviews/access-review-status-access-package-overview.png)

1. Aparecerá uma lista que contém todas as políticas que têm revisões de acesso associadas a elas. Clique na análise para ver o seu relatório.

    ![Lista de avaliações de acesso](./media/entitlement-management-access-reviews/access-review-status-select-access-reviews.png)
   
1. Quando vê o relatório, mostra o número de utilizadores revistos e as ações tomadas pelo revisor sobre os mesmos.

    ![Ver estado de revisão](./media/entitlement-management-access-reviews/access-review-status.png)
 

## <a name="access-reviews-email-notifications"></a>Acesso avalia notificações de email
Pode designar revisores ou os utilizadores podem rever o seu próprio acesso. Por padrão, o Azure AD enviará um e-mail para revisores ou auto-revisores logo após o início da revisão.

O e-mail incluirá instruções sobre como rever o acesso aos pacotes de acesso. Se a revisão for para que os utilizadores revejam o seu acesso, mostrem-lhes as instruções sobre como realizar uma auto-revisão dos seus pacotes de acesso.
  
Se você atribuiu utilizadores convidados como revisores, e eles não aceitaram o seu convite para convidados Azure AD, eles não receberão e-mails de comentários de acesso AD Azure. Primeiro devem aceitar o convite e criar uma conta com a Azure AD antes de poderem receber os e-mails. 

## <a name="next-steps"></a>Passos seguintes

- [Rever o acesso aos pacotes de acesso](entitlement-management-access-reviews-review-access.md)
- [Auto-revisão dos pacotes de acesso](entitlement-management-access-reviews-self-review.md)
