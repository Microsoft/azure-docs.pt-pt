---
title: Migrar de uma política clássica que exija a autenticação multifator no portal do Azure | Documentos da Microsoft
description: Este artigo mostra como migrar uma política clássica que exija a autenticação multifator no portal do Azure.
services: active-directory
keywords: acesso condicional para aplicações, o acesso condicional com o Azure AD, o acesso seguro aos recursos da empresa, políticas de acesso condicional
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.component: protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/13/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: e3a4d751c99292c55da9af70998b6f07a0e7914f
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2018
ms.locfileid: "39523823"
---
# <a name="migrate-a-classic-policy-that-requires-multi-factor-authentication-in-the-azure-portal"></a>Migrar de uma política clássica que exija a autenticação multifator no portal do Azure 

Este artigo mostra como migrar uma política clássica, que requer **multi-factor authentication** para uma aplicação na cloud. Embora não seja um pré-requisito, recomendamos que leia [migrar políticas clássicas no portal do Azure](active-directory-conditional-access-migration.md) antes de começar a migrar as suas políticas clássicas.


 
## <a name="overview"></a>Descrição geral 

O cenário neste artigo mostra como migrar uma política clássica, que requer **multi-factor authentication** para uma aplicação na cloud. 

![Azure Active Directory](./media/active-directory-conditional-access-migration/33.png)


O processo de migração incluir os seguintes passos:

1. [Abra a política clássica](#open-a-classic-policy) para obter as definições de configuração.
2. Criar uma nova política de acesso condicional do Azure AD para substituir a sua política clássica. 
3. Desative a política clássica.



## <a name="open-a-classic-policy"></a>Abra uma política clássica

1. Na [portal do Azure](https://portal.azure.com), na barra de navegação esquerda, clique em **Azure Active Directory**.

    ![Azure Active Directory](./media/active-directory-conditional-access-migration-mfa/01.png)

2. Na **do Azure Active Directory** página, além do **gerir** secção, clique em **acesso condicional**.

    ![Acesso condicional](./media/active-directory-conditional-access-migration-mfa/02.png)

3. Na **Manage** secção, clique em **políticas de clássico (pré-visualização)**.

    ![Políticas clássicas](./media/active-directory-conditional-access-migration-mfa/12.png)

4. Na lista de políticas clássicas, clique na política que requer **multi-factor authentication** para uma aplicação na cloud.

    ![Políticas clássicas](./media/active-directory-conditional-access-migration-mfa/13.png)


## <a name="create-a-new-conditional-access-policy"></a>Criar uma nova política de acesso condicional


1. Na [portal do Azure](https://portal.azure.com), na barra de navegação esquerda, clique em **Azure Active Directory**.

    ![Azure Active Directory](./media/active-directory-conditional-access-migration/01.png)

2. Na **do Azure Active Directory** página, além do **gerir** secção, clique em **acesso condicional**.

    ![Acesso condicional](./media/active-directory-conditional-access-migration/02.png)



3. Sobre o **acesso condicional** página, para abrir o **New** página, na barra de ferramentas na parte superior, clique em **adicionar**.

    ![Acesso condicional](./media/active-directory-conditional-access-migration/03.png)

4. No **New** página, além do **nome** caixa de texto, escreva um nome para a sua política.

    ![Acesso condicional](./media/active-directory-conditional-access-migration/29.png)

5. Na **atribuições** secção, clique em **utilizadores e grupos**.

    ![Acesso condicional](./media/active-directory-conditional-access-migration/05.png)

    a. Se tiver selecionado na sua política clássica todos os utilizadores, clique em **todos os utilizadores**. 

    ![Acesso condicional](./media/active-directory-conditional-access-migration/35.png)

    b. Se tiver grupos selecionados na sua política clássica, clique em **selecionar utilizadores e grupos**e, em seguida, selecione os utilizadores necessários e grupos.

    ![Acesso condicional](./media/active-directory-conditional-access-migration/36.png)

    c. Se tiver os grupos excluídos, clique nas **excluir** separador e, em seguida, selecione os utilizadores necessários e grupos. 

    ![Acesso condicional](./media/active-directory-conditional-access-migration/37.png)

6. Na **New** página, para abrir o **aplicações na Cloud** página o **atribuição** secção, clique em **aplicações na Cloud**.

8. Sobre o **aplicações na Cloud** página, execute os seguintes passos:

    ![Acesso condicional](./media/active-directory-conditional-access-migration/08.png)

    a. Clique em **selecionar aplicações**.

    b. Clique em **Selecionar**.

    c. Sobre o **selecionar** página, selecione a sua aplicação na cloud e, em seguida, clique em **selecione**.

    d. Sobre o **aplicações na Cloud** página, clique em **feito**.



9. Se tiver **exigir autenticação multifator** selecionado:

    ![Acesso condicional](./media/active-directory-conditional-access-migration/26.png)

    a. Na **controlos de acesso** secção, clique em **concessão**.

    ![Acesso condicional](./media/active-directory-conditional-access-migration/27.png)

    b. Sobre o **concessão** página, clique em **conceder acesso**e, em seguida, clique em **exigir autenticação multifator**.

    c. Clique em **Selecionar**.


10. Clique em **no** para ativar a sua política.

    ![Acesso condicional](./media/active-directory-conditional-access-migration/30.png)



## <a name="disable-the-classic-policy"></a>Desativar a política clássica

Para desativar a sua política clássica, clique em **desativar** no **detalhes** vista.

![Políticas clássicas](./media/active-directory-conditional-access-migration-mfa/14.png)



## <a name="next-steps"></a>Passos Seguintes

- Para obter mais informações sobre a migração de política clássica, consulte [migrar políticas clássicas no portal do Azure](active-directory-conditional-access-migration.md).


- Se quiser saber como configurar uma política de acesso condicional, consulte [exigir a MFA para aplicações específicas com acesso condicional do Azure Active Directory](conditional-access/app-based-mfa.md).

- Se estiver pronto para configurar políticas de acesso condicional para o seu ambiente, veja a [melhores práticas para acesso condicional no Azure Active Directory](conditional-access/best-practices.md). 
