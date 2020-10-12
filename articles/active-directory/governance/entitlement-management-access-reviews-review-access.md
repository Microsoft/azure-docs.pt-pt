---
title: Rever o acesso a um pacote de acesso na gestão de direitos Azure AD
description: Saiba como concluir uma revisão de acesso aos pacotes de acesso à gestão de direitos em avaliações de acesso ao Azure Ative Directory (Preview).
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
ms.openlocfilehash: d1115c08214db19227b4b8d7be671ce4da1cf2b1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87798602"
---
# <a name="review-access-of-an-access-package-in-azure-ad-entitlement-management"></a>Rever o acesso a um pacote de acesso na gestão de direitos Azure AD

A gestão de direitos AZure AD simplifica a forma como as empresas gerem o acesso a grupos, aplicações e sites SharePoint. Este artigo descreve como realizar avaliações de acesso a outros utilizadores que são atribuídos a um pacote de acesso como um revisor designado.

## <a name="prerequisites"></a>Pré-requisitos

Para rever as atribuições de pacotes de acesso ativo dos utilizadores, tem de cumprir os requisitos para fazer uma revisão de acesso:
- Azure AD Premium P2
- Administrador global
- Administrador designado do utilizador, proprietário do catálogo ou gestor de pacotes access

Para mais informações, consulte [os requisitos da Licença.](entitlement-management-overview.md#license-requirements)


## <a name="open-the-access-review"></a>Abra a revisão de acesso

Utilize os seguintes passos para encontrar e abrir a revisão de acesso:

1. Pode receber um e-mail da Microsoft que lhe pede para rever o acesso. Localize o e-mail para abrir a revisão de acesso. Aqui está um e-mail de exemplo para rever o acesso:
    
    ![E-mail do revisor de comentários de acesso](./media/entitlement-management-access-reviews-review-access/review-access-reviewer-email.png)

1. Clique no link de acesso ao **utilizador do Review** para abrir a revisão de acesso. 

1. Se não tiver o e-mail, pode encontrar os seus comentários de acesso pendentes navegando diretamente para https://myaccess.microsoft.com .  (Para o Governo dos EUA, use `https://myaccess.microsoft.us` em vez disso.)

1. Clique em **comentários de Acesso** na barra de navegação esquerda para ver uma lista de avaliações de acesso pendentes atribuídas a si.
    
    ![Selecione comentários de acesso no My Access](./media/entitlement-management-access-reviews-review-access/review-access-myaccess-select-access-review.png)

1. Clique na análise que gostaria de iniciar.
    
    ![Selecione a revisão de acesso](./media/entitlement-management-access-reviews-review-access/review-access-select-access-review.png)

## <a name="perform-the-access-review"></a>Realizar a revisão de acesso

Assim que abrir a revisão de acesso, verá os nomes dos utilizadores para os quais precisa de rever. Há duas formas de aprovar ou negar o acesso:
- Pode aprovar manualmente ou negar o acesso a um ou mais utilizadores
- Pode aceitar as recomendações do sistema

### <a name="manually-approve-or-deny-access-for-one-or-more-users"></a>Aprovar ou negar manualmente o acesso a um ou mais utilizadores
1. Reveja a lista de utilizadores e determine quais os utilizadores que precisam de continuar a ter acesso.

    ![Lista de utilizadores a rever](./media/entitlement-management-access-reviews-review-access/review-access-list-of-users.png)

1. Para aprovar ou negar o acesso, selecione o botão de rádio à esquerda do nome do utilizador.

1. Selecione **Aprovar** ou **Negar** na barra acima dos nomes de utilizador.

    ![Selecione o utilizador](./media/entitlement-management-access-reviews-review-access/review-access-select-users.png)

1. Se não tiver a certeza, pode clicar no botão **Não Saber.**

    Se fizer esta seleção, o utilizador mantém o acesso e esta seleção vai para os registos de auditoria. O registo mostra quaisquer outros revisores que ainda tenha concluído a revisão.

1. Pode ser-lhe exigido que forneça uma razão para a sua decisão. Digite uma razão e clique em **Enviar por isso.**

    ![Aprovar ou negar acesso](./media/entitlement-management-access-reviews-review-access/review-access-decision-approve.png)

1. Pode alterar a sua decisão a qualquer momento antes do final da revisão. Para tal, selecione o utilizador da lista e altere a decisão. Por exemplo, pode aprovar o acesso a um utilizador que já negou.

Se houver vários revisores, a última resposta submetida é registada. Considere um exemplo onde um administrador designa dois revisores - Alice e Bob. Alice abre a revisão primeiro e aprova o acesso. Antes que a revisão termine, Bob abre a revisão e nega o acesso. Neste caso, a última decisão de acesso de negação é registada.

>[!NOTE]
>Se um utilizador não tiver acesso, não é imediatamente removido do pacote de acesso. O utilizador será removido do pacote de acesso quando a revisão terminar, ou um administrador termina a revisão.

### <a name="approve-or-deny-access-using-the-system-generated-recommendations"></a>Aprovar ou negar o acesso usando as recomendações geradas pelo sistema

Para rever o acesso a vários utilizadores mais rapidamente, pode utilizar as recomendações geradas pelo sistema, aceitando as recomendações com um simples clique. As recomendações são geradas com base na atividade de inscrição do utilizador.

1.  Na barra no topo da página, clique em **Aceitar recomendações**.
    
    ![Selecione Recomendações de aceitação](./media/entitlement-management-access-reviews-review-access/review-access-use-recommendations.png)
    
    Verá um resumo das ações recomendadas.

1.  Clique **em Submeter-se** para aceitar as recomendações.

## <a name="next-steps"></a>Passos seguintes

- [Auto-revisão dos pacotes de acesso](entitlement-management-access-reviews-self-review.md)