---
title: Rever o acesso a um pacote de acesso na gestão de direitos da AD Azure
description: Saiba como concluir uma revisão de acesso aos pacotes de acesso à gestão de direitos em avaliações de acesso ao Diretório Ativo do Azure (Pré-visualização).
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
ms.openlocfilehash: 99de022b7259b33baab3aa825673a8f85e932bff
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2020
ms.locfileid: "78968735"
---
# <a name="review-access-of-an-access-package-in-azure-ad-entitlement-management"></a>Rever o acesso a um pacote de acesso na gestão de direitos da AD Azure

A gestão de direitos da Azure AD simplifica a forma como as empresas gerem o acesso a grupos, aplicações e sites SharePoint. Este artigo descreve como realizar avaliações de acesso para outros utilizadores que são atribuídos a um pacote de acesso como revisor designado.

## <a name="prerequisites"></a>Pré-requisitos

Para rever as atribuições de pacotes de acesso ativo dos utilizadores, deve cumprir os pré-requisitos para então uma revisão de acesso:
- Azure AD Premium P2
- Administrador global
- Administrador de utilizador designado, proprietário do catálogo ou gestor de pacotes de acesso

Para mais informações, consulte [os requisitos da Licença.](entitlement-management-overview.md#license-requirements)


## <a name="open-the-access-review"></a>Abra a revisão de acesso

Utilize os seguintes passos para encontrar e abrir a revisão de acesso:

1. Pode receber um e-mail da Microsoft que lhe pede para rever o acesso. Localize o e-mail para abrir a revisão de acesso. Aqui está um e-mail de exemplo para rever o acesso:
    
    ![Access review reviewer email](./media/entitlement-management-access-reviews-review-access/review-access-reviewer-email.png)

1. Clique no link de acesso ao **utilizador da Rever** para abrir a revisão de acesso. 

1. Se não tiver o e-mail, pode encontrar as suas avaliações de acesso pendentes navegando diretamente para https://myaccess.microsoft.com.  (Para o Governo dos EUA, use `https://myaccess.microsoft.us` em vez disso.)

1. Clique em comentários de **Acesso** na barra de navegação esquerda para ver uma lista de avaliações de acesso pendentes atribuídas a si.
    
    ![Selecione avaliações de acesso no My Access](./media/entitlement-management-access-reviews-review-access/review-access-myaccess-select-access-review.png)

1. Clique na avaliação que gostaria de começar.
    
    ![Selecione a revisão de acesso](./media/entitlement-management-access-reviews-review-access/review-access-select-access-review.png)

## <a name="perform-the-access-review"></a>Realizar a revisão de acesso

Assim que abrir a revisão de acesso, verá os nomes dos utilizadores para os quais precisa de rever. Há duas formas de aprovar ou negar o acesso:
- Pode aprovar manualmente ou negar o acesso a um ou mais utilizadores
- Pode aceitar as recomendações do sistema

### <a name="manually-approve-or-deny-access-for-one-or-more-users"></a>Aprovar manualmente ou negar o acesso a um ou mais utilizadores
1. Reveja a lista de utilizadores e determine quais os utilizadores que precisam de continuar a ter acesso.

    ![Lista de utilizadores a rever](./media/entitlement-management-access-reviews-review-access/review-access-list-of-users.png)

1. Para aprovar ou negar o acesso, selecione o botão de rádio à esquerda do nome do utilizador.

1. Selecione **Aprovar** ou **Negar** na barra acima dos nomes dos utilizadores.

    ![Selecione o utilizador](./media/entitlement-management-access-reviews-review-access/review-access-select-users.png)

1. Se não tiver a certeza, pode clicar no botão **"Não Sei".**

    Se fizer esta seleção, o utilizador mantém o acesso, e esta seleção vai nos registos de auditoria. O registo mostra quaisquer outros revisores que ainda tenha concluído a revisão.

1. Pode ser obrigado a fornecer uma razão para a sua decisão. Digite uma razão e clique em **Submeter**.

    ![Aprovar ou negar o acesso](./media/entitlement-management-access-reviews-review-access/review-access-decision-approve.png)

1. Pode alterar a sua decisão a qualquer momento antes do final da revisão. Para isso, selecione o utilizador da lista e altere a decisão. Por exemplo, pode aprovar o acesso a um utilizador que negou anteriormente.

Se houver vários revisores, a última resposta submetida é gravada. Considere um exemplo onde um administrador designa dois revisores - Alice e Bob. Alice abre a revisão primeiro e aprova o acesso. Antes que a revisão termine, Bob abre a revisão e nega o acesso. Neste caso, a última decisão de acesso é registada.

>[!NOTE]
>Se um utilizador não tiver acesso, não é removido do pacote de acesso imediatamente. O utilizador será removido do pacote de acesso quando a revisão terminar, ou um administrador termina a revisão.

### <a name="approve-or-deny-access-using-the-system-generated-recommendations"></a>Aprovar ou negar o acesso utilizando as recomendações geradas pelo sistema

Para rever o acesso a vários utilizadores mais rapidamente, pode utilizar as recomendações geradas pelo sistema, aceitando as recomendações com um único clique. As recomendações são geradas com base na atividade de inscrição do utilizador.

1.  No bar no topo da página, clique em **Aceitar recomendações.**
    
    ![Selecione Aceitar recomendações](./media/entitlement-management-access-reviews-review-access/review-access-use-recommendations.png)
    
    Verá um resumo das ações recomendadas.

1.  Clique em **Submeter** para aceitar as recomendações.

## <a name="next-steps"></a>Passos seguintes

- [Auto-revisão dos pacotes de acesso](entitlement-management-access-reviews-self-review.md)