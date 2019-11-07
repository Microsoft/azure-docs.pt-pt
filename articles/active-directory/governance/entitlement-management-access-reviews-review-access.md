---
title: Examinar o acesso de um pacote de acesso no gerenciamento de direitos do Azure AD
description: Saiba como concluir uma revisão de acesso de pacotes de acesso de gerenciamento de direitos em revisões de Azure Active Directory acesso (versão prévia).
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
ms.openlocfilehash: a78a69301ab2ae637531f8643d9d57a8b44b563f
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73608933"
---
# <a name="review-access-of-an-access-package-in-azure-ad-entitlement-management"></a>Examinar o acesso de um pacote de acesso no gerenciamento de direitos do Azure AD

O gerenciamento de direitos do Azure AD simplifica o modo como as empresas gerenciam o acesso a grupos, aplicativos e sites do SharePoint. Este artigo descreve como executar revisões de acesso para outros usuários atribuídos a um pacote do Access como um revisor designado.

## <a name="prerequisites"></a>Pré-requisitos

Para examinar as atribuições de pacote do acesso ativo dos usuários, você deve atender aos pré-requisitos para fazer uma revisão de acesso:
- Azure AD Premium P2
- Administrador global
- Administrador de usuário designado, proprietário do catálogo ou Gerenciador de pacotes de acesso

Para obter mais informações, consulte [requisitos de licença](entitlement-management-overview.md#license-requirements).


## <a name="open-the-access-review"></a>Abrir a revisão de acesso

Use as seguintes etapas para localizar e abrir a revisão de acesso:

1. Você pode receber um email da Microsoft que solicita que você examine o acesso. Localize o email para abrir a revisão de acesso. Aqui está um email de exemplo para revisar o acesso:
    
    ![Email do revisor de revisão de acesso](./media/entitlement-management-access-reviews-review-access/review-access-reviewer-email.png)

1. Clique no link **examinar acesso do usuário** para abrir a revisão de acesso. 

1. Se você não tiver o email, poderá encontrar suas revisões de acesso pendentes navegando diretamente para https://myaccess.microsoft.com.

1. Clique em **revisões de acesso** na barra de navegação à esquerda para ver uma lista de revisões de acesso pendentes atribuídas a você.
    
    ![Selecionar revisões de acesso no meu acesso](./media/entitlement-management-access-reviews-review-access/review-access-myaccess-select-access-review.png)

1. Clique na revisão que você gostaria de começar.
    
    ![Selecione a revisão de acesso](./media/entitlement-management-access-reviews-review-access/review-access-select-access-review.png)

## <a name="perform-the-access-review"></a>Executar a revisão de acesso

Depois de abrir a revisão de acesso, você verá os nomes dos usuários para os quais você precisa examinar. Há duas maneiras de aprovar ou negar o acesso:
- Você pode aprovar ou negar manualmente o acesso para um ou mais usuários
- Você pode aceitar as recomendações do sistema

### <a name="manually-approve-or-deny-access-for-one-or-more-users"></a>Aprovar ou negar manualmente o acesso para um ou mais usuários
1. Examine a lista de usuários e determine quais usuários precisam continuar a ter acesso.

    ![Lista de usuários a serem examinados](./media/entitlement-management-access-reviews-review-access/review-access-list-of-users.png)

1. Para aprovar ou negar o acesso, selecione o botão de opção à esquerda do nome do usuário.

1. Selecione **aprovar** ou **negar** na barra acima dos nomes de usuário.

    ![Selecionar o usuário](./media/entitlement-management-access-reviews-review-access/review-access-select-users.png)

1. Se você não tiver certeza, poderá clicar no botão **não saber** .

    Se você fizer essa seleção, o usuário manterá o acesso e essa seleção entrará nos logs de auditoria. O log mostra todos os outros revisores que você ainda concluiu a revisão.

1. Talvez seja necessário fornecer um motivo para sua decisão. Digite um motivo e clique em **Enviar**.

    ![Aprovar ou negar acesso](./media/entitlement-management-access-reviews-review-access/review-access-decision-approve.png)

1. Você pode alterar sua decisão a qualquer momento antes do final da revisão. Para fazer isso, selecione o usuário na lista e altere a decisão. Por exemplo, você pode aprovar o acesso para um usuário que você negou anteriormente.

Se houver vários revisores, a última resposta enviada será registrada. Considere um exemplo em que um administrador designa dois revisores – Alice e Bob. Alice abre a revisão primeiro e aprova o acesso. Antes de a revisão terminar, Bob abre a revisão e nega o acesso. Nesse caso, a última decisão de acesso de negação é registrada.

>[!NOTE]
>Se um usuário tiver acesso negado, ele não será removido do pacote de acesso imediatamente. O usuário será removido do pacote de acesso quando a revisão terminar ou um administrador terminar a revisão.

### <a name="approve-or-deny-access-using-the-system-generated-recommendations"></a>Aprovar ou negar o acesso usando as recomendações geradas pelo sistema

Para revisar o acesso a vários usuários mais rapidamente, você pode usar as recomendações geradas pelo sistema, aceitando as recomendações com um único clique. As recomendações são geradas com base na atividade de entrada do usuário.

1.  Na barra na parte superior da página, clique em **aceitar recomendações**.
    
    ![Selecionar aceitar recomendações](./media/entitlement-management-access-reviews-review-access/review-access-use-recommendations.png)
    
    Você verá um resumo das ações recomendadas.

1.  Clique em **Enviar** para aceitar as recomendações.

## <a name="next-steps"></a>Passos seguintes

- [Revisão automática de pacotes do Access](entitlement-management-access-reviews-self-review.md)