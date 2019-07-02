---
title: Migrar de uma política clássica que exija a autenticação multifator no portal do Azure
description: Este artigo mostra como migrar uma política clássica que exija a autenticação multifator no portal do Azure.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: tutorial
ms.date: 06/13/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4819c283a136057ad7c3ffd755fd9e157d99a1bf
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509448"
---
# <a name="migrate-a-classic-policy-that-requires-multi-factor-authentication-in-the-azure-portal"></a>Migrar de uma política clássica que exija a autenticação multifator no portal do Azure

Este tutorial mostra como migrar uma política clássica, que requer **multi-factor authentication** para uma aplicação na cloud. Embora não seja um pré-requisito, recomendamos que leia [migrar políticas clássicas no portal do Azure](policy-migration.md) antes de começar a migrar as suas políticas clássicas.

## <a name="overview"></a>Descrição geral

O cenário neste artigo mostra como migrar uma política clássica, que requer **multi-factor authentication** para uma aplicação na cloud.

![Azure Active Directory](./media/policy-migration/33.png)

O processo de migração inclui os seguintes passos:

1. [Abra a política clássica](#open-a-classic-policy) para obter as definições de configuração.
1. Crie uma nova política de acesso condicional do Azure AD para substituir a sua política clássica. 
1. Desative a política clássica.

## <a name="open-a-classic-policy"></a>Abra uma política clássica

1. Na [portal do Azure](https://portal.azure.com), na barra de navegação esquerda, clique em **Azure Active Directory**.

   ![Azure Active Directory](./media/policy-migration-mfa/01.png)

1. Na **do Azure Active Directory** página, além do **gerir** secção, clique em **acesso condicional**.

   ![Acesso Condicional](./media/policy-migration-mfa/02.png)

1. Na **Manage** secção, clique em **políticas de clássico (pré-visualização)** .

   ![Políticas clássicas](./media/policy-migration-mfa/12.png)

1. Na lista de políticas clássicas, clique na política que requer **multi-factor authentication** para uma aplicação na cloud.

   ![Políticas clássicas](./media/policy-migration-mfa/13.png)

## <a name="create-a-new-conditional-access-policy"></a>Criar uma nova política de acesso condicional

1. Na [portal do Azure](https://portal.azure.com), na barra de navegação esquerda, clique em **Azure Active Directory**.

   ![Azure Active Directory](./media/policy-migration/01.png)

1. Na **do Azure Active Directory** página, além do **gerir** secção, clique em **acesso condicional**.

   ![Acesso Condicional](./media/policy-migration/02.png)

1. Sobre o **acesso condicional** página, para abrir o **New** página, na barra de ferramentas na parte superior, clique em **adicionar**.

   ![Acesso Condicional](./media/policy-migration/03.png)

1. No **New** página, além do **nome** caixa de texto, escreva um nome para a sua política.

   ![Acesso Condicional](./media/policy-migration/29.png)

1. Na **atribuições** secção, clique em **utilizadores e grupos**.

   ![Acesso Condicional](./media/policy-migration/05.png)

   1. Se tiver selecionado na sua política clássica todos os utilizadores, clique em **todos os utilizadores**. 

      ![Acesso Condicional](./media/policy-migration/35.png)

   1. Se tiver grupos selecionados na sua política clássica, clique em **selecionar utilizadores e grupos**e, em seguida, selecione os utilizadores necessários e grupos.

      ![Acesso Condicional](./media/policy-migration/36.png)

   1. Se tiver os grupos excluídos, clique nas **excluir** separador e, em seguida, selecione os utilizadores necessários e grupos. 

      ![Acesso Condicional](./media/policy-migration/37.png)

1. Na **New** página, para abrir o **aplicações na Cloud** página o **atribuição** secção, clique em **aplicações na Cloud**.
1. Sobre o **aplicações na Cloud** página, execute os seguintes passos:
   1. Clique em **selecionar aplicações**.
   1. Clique em **Selecionar**.
   1. Sobre o **selecionar** página, selecione a sua aplicação na cloud e, em seguida, clique em **selecione**.
   1. Sobre o **aplicações na Cloud** página, clique em **feito**.
1. Se tiver **exigir autenticação multifator** selecionado:

   ![Acesso Condicional](./media/policy-migration/26.png)

   1. Na **controlos de acesso** secção, clique em **concessão**.

      ![Acesso Condicional](./media/policy-migration/27.png)

   1. Sobre o **concessão** página, clique em **conceder acesso**e, em seguida, clique em **exigir autenticação multifator**.
   1. Clique em **Selecionar**.
1. Clique em **no** para ativar a sua política.

   ![Acesso Condicional](./media/policy-migration/30.png)

## <a name="disable-the-classic-policy"></a>Desativar a política clássica

Para desativar a sua política clássica, clique em **desativar** no **detalhes** vista.

![Políticas clássicas](./media/policy-migration-mfa/14.png)

## <a name="next-steps"></a>Passos Seguintes

- Para obter mais informações sobre a migração de política clássica, consulte [migrar políticas clássicas no portal do Azure](policy-migration.md).
- Se quiser saber como configurar uma política de acesso condicional, consulte [exigir MFA para aplicações específicas com o Azure Active Directory condicional acesso](app-based-mfa.md).
- Se estiver pronto para configurar políticas de acesso condicional para o seu ambiente, veja a [melhores práticas para acesso condicional no Azure Active Directory](best-practices.md).
