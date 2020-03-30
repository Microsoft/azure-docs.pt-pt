---
title: Criar uma revisão de acesso de um pacote de acesso na gestão de direitos da AD Azure
description: Saiba como criar uma política de revisão de acesso para pacotes de acesso à gestão de direitos em avaliações de acesso ao Diretório Ativo do Azure (Pré-visualização).
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 11/01/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: a862bbb1f574e4adab2f7d8e59a1abe8e5a5fa2a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73608842"
---
# <a name="create-an-access-review-of-an-access-package-in-azure-ad-entitlement-management"></a>Criar uma revisão de acesso de um pacote de acesso na gestão de direitos da AD Azure

Para reduzir o risco de acesso a estancar, deverá ativar revisões periódicas de utilizadores que possuam atribuições ativas a um pacote de acesso na gestão de direitos da AD Azure. Pode ativar avaliações quando criar um novo pacote de acesso ou editar um pacote de acesso existente. Este artigo descreve como permitir revisões de acesso de pacotes de acesso.

## <a name="prerequisites"></a>Pré-requisitos

Para permitir revisões dos pacotes de acesso, deve cumprir os pré-requisitos para a criação de um pacote de acesso:
- Azure AD Premium P2
- Administrador global, administrador de utilizador, proprietário do catálogo ou gestor de pacotes de acesso

Para mais informações, consulte [os requisitos da Licença.](entitlement-management-overview.md#license-requirements)


## <a name="create-an-access-review-of-an-access-package"></a>Criar uma revisão de acesso de um pacote de acesso

Pode ativar avaliações de acesso ao [criar um novo pacote](entitlement-management-access-package-create.md) de acesso ou editar uma política de pacotes de acesso [existente.](entitlement-management-access-package-lifecycle-policy.md) Siga estas medidas para permitir a revisão de acesso de um pacote de acesso:

1. Abra o separador **Lifecycle** para um pacote de acesso e desça até comentários de **acesso**.

1. Mova as **avaliações** de acesso Exigir para **Sim**.

    ![Adicione a revisão de acesso](./media/entitlement-management-access-reviews/access-reviews-pane.png)

1. Especifique a data em que as avaliações começarão ao lado de **Começar a iniciar**.

1. Em seguida, detete a **frequência de Revisão** para **Anualmente**, **Bi-anualmente,** **Trimestral** ou **Mensal**.
Esta definição determina a frequência com que as avaliações de acesso ocorrem.

1. Defina a **Duração** para definir quantos dias cada revisão da série recorrente estará aberta para entrada dos revisores. Por exemplo, pode agendar uma revisão anual que começa a 1 de janeiro e está aberta a revisão por 30 dias para que os revisores tenham até ao final do mês para responder.

1. Ao lado dos **Revisores**, selecione **Self-review** se pretender que os utilizadores realizem a sua própria revisão de acesso ou selecione **O(s) revisor específico** se pretender designar um revisor.

    ![Selecione Adicionar revisores](./media/entitlement-management-access-reviews/access-reviews-add-reviewer.png)

1. Se selecionou **o(s) revisor específico,** especifique quais os utilizadores que irão fazer a revisão de acesso:
    1. Selecione **Adicionar revisores**.
    1. No painel de **revisores Select,** procure e selecione o(s) utilizador(s) que pretende ser um revisor.
    1. Quando tiver selecionado o seu(s revisor), clique no botão **Select.**

    ![Especificar os revisores](./media/entitlement-management-access-reviews/access-reviews-select-reviewer.png)

1. Clique em **Rever + Crie** se estiver a criar um novo pacote de acesso ou **atualizar** se estiver a editar um pacote de acesso, na parte inferior da página.

## <a name="view-the-status-of-the-access-review"></a>Ver o estado da revisão de acesso

Após a data de início, uma revisão de acesso será listada na secção **de avaliações** de acesso. Siga estas medidas para ver o estado de uma revisão de acesso:

1. No **Identity Governance,** clique em **pacotes de acesso** e selecione o pacote de acesso com o estado de revisão de acesso que gostaria de verificar.   

1. Assim que estiver na visão geral do pacote de acesso, clique em comentários de **acesso** no menu esquerdo.
    
    ![Selecione avaliações de acesso](./media/entitlement-management-access-reviews/access-review-status-access-package-overview.png)

1. Aparecerá uma lista que contém todas as políticas que têm avaliações de acesso associadas a elas. Clique na avaliação para ver o seu relatório.

    ![Lista de avaliações de acesso](./media/entitlement-management-access-reviews/access-review-status-select-access-reviews.png)
   
1. Quando vê o relatório, mostra o número de utilizadores revistos e as ações tomadas pelo revisor sobre eles.

    ![Ver estado de revisão](./media/entitlement-management-access-reviews/access-review-status.png)
 

## <a name="access-reviews-email-notifications"></a>Notificações de e-mail de comentários de acesso
Pode designar revisores ou os utilizadores podem rever o seu acesso por si mesmos. Por padrão, o Azure AD enviará um e-mail para revisores ou auto-revisores logo após o início da revisão.

O e-mail incluirá instruções sobre como rever o acesso aos pacotes de acesso. Se a revisão for para os utilizadores reverem o seu acesso, mostre-lhes as instruções sobre como realizar uma auto-revisão dos seus pacotes de acesso.
  
Se atribuiu os utilizadores convidados como revisores e não aceitou o convite de hóspedes do Azure AD, não receberá e-mails de avaliações de acesso a Anúncio saqueadas. Primeiro devem aceitar o convite e criar uma conta com a Azure AD antes de poderem receber os e-mails. 

## <a name="next-steps"></a>Passos seguintes

- [Rever o acesso aos pacotes de acesso](entitlement-management-access-reviews-review-access.md)
- [Auto-revisão dos pacotes de acesso](entitlement-management-access-reviews-self-review.md)
