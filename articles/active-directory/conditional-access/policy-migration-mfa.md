---
title: Migrar políticas de acesso condicional-Azure Active Directory
description: Este artigo mostra como migrar uma política clássica que requer autenticação multifator no portal do Azure.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: tutorial
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca00ae62ba114aecef48117fd8a54b7f2e962dfd
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74380315"
---
# <a name="migrate-a-classic-policy-that-requires-multi-factor-authentication-in-the-azure-portal"></a>Migre uma política clássica que exija autenticação multifator no portal do Azure

Este tutorial mostra como migrar uma política clássica que requer **autenticação multifator** para um aplicativo de nuvem. Embora não seja um pré-requisito, recomendamos que você leia [migrar políticas clássicas na portal do Azure antes de](policy-migration.md) começar a migrar suas políticas clássicas.

## <a name="overview"></a>Descrição geral

O cenário neste artigo mostra como migrar uma política clássica que requer **autenticação multifator** para um aplicativo de nuvem.

![Azure Active Directory](./media/policy-migration/33.png)

O processo de migração consiste nas seguintes etapas:

1. [Abra a política clássica](#open-a-classic-policy) para obter as definições de configuração.
1. Crie uma nova política de acesso condicional do Azure AD para substituir sua política clássica. 
1. Desabilite a política clássica.

## <a name="open-a-classic-policy"></a>Abrir uma política clássica

1. Na [portal do Azure](https://portal.azure.com), na barra de navegação à esquerda, clique em **Azure Active Directory**.

   ![Azure Active Directory](./media/policy-migration-mfa/01.png)

1. Na página **Azure Active Directory** , na seção **gerenciar** , clique em **acesso condicional**.

   ![Acesso Condicional](./media/policy-migration-mfa/02.png)

1. Na seção **gerenciar** , clique em **políticas clássicas (versão prévia)** .

   ![Políticas clássicas](./media/policy-migration-mfa/12.png)

1. Na lista de políticas clássicas, clique na política que requer **autenticação multifator** para um aplicativo de nuvem.

   ![Políticas clássicas](./media/policy-migration-mfa/13.png)

## <a name="create-a-new-conditional-access-policy"></a>Criar uma nova política de acesso condicional

1. Na [portal do Azure](https://portal.azure.com), na barra de navegação à esquerda, clique em **Azure Active Directory**.

   ![Azure Active Directory](./media/policy-migration/01.png)

1. Na página **Azure Active Directory** , na seção **gerenciar** , clique em **acesso condicional**.

   ![Acesso Condicional](./media/policy-migration/02.png)

1. Na página **acesso condicional** , para abrir a página **novo** , na barra de ferramentas na parte superior, clique em **Adicionar**.

   ![Acesso Condicional](./media/policy-migration/03.png)

1. Na página **novo** , na caixa de texto **nome** , digite um nome para a política.

   ![Acesso Condicional](./media/policy-migration/29.png)

1. Na seção **atribuições** , clique em **usuários e grupos**.

   ![Acesso Condicional](./media/policy-migration/05.png)

   1. Se você tiver todos os usuários selecionados em sua política clássica, clique em **todos os usuários**. 

      ![Acesso Condicional](./media/policy-migration/35.png)

   1. Se você tiver grupos selecionados em sua política clássica, clique em **Selecionar usuários e grupos**e selecione os usuários e grupos necessários.

      ![Acesso Condicional](./media/policy-migration/36.png)

   1. Se você tiver os grupos excluídos, clique na guia **excluir** e selecione os usuários e grupos necessários. 

      ![Acesso Condicional](./media/policy-migration/37.png)

1. Na página **novo** , para abrir a página **aplicativos de nuvem** , na seção **atribuição** , clique em **aplicativos de nuvem**.
1. Na página **aplicativos de nuvem** , execute as seguintes etapas:
   1. Clique em **selecionar aplicativos**.
   1. Clique em **Selecionar**.
   1. Na página **selecionar** , selecione seu aplicativo de nuvem e, em seguida, clique em **selecionar**.
   1. Na página **aplicativos de nuvem** , clique em **concluído**.
1. Se você **precisar de uma autenticação multifator** selecionada:

   ![Acesso Condicional](./media/policy-migration/26.png)

   1. Na seção **controles de acesso** , clique em **conceder**.

      ![Acesso Condicional](./media/policy-migration/27.png)

   1. Na página **conceder** , clique em **conceder acesso**e, em seguida, clique em **exigir autenticação multifator**.
   1. Clique em **Selecionar**.
1. Clique **em** ativar para habilitar sua política.

   ![Acesso Condicional](./media/policy-migration/30.png)

## <a name="disable-the-classic-policy"></a>Desabilitar a política clássica

Para desabilitar sua política clássica, clique em **desabilitar** na exibição de **detalhes** .

![Políticas clássicas](./media/policy-migration-mfa/14.png)

## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações sobre a migração de política clássica, consulte [migrar políticas clássicas no portal do Azure](policy-migration.md).
- Se você quiser saber como configurar uma política de acesso condicional, consulte [exigir MFA para aplicativos específicos com Azure Active Directory acesso condicional](app-based-mfa.md).
- Se você estiver pronto para configurar políticas de acesso condicional para seu ambiente, consulte as [práticas recomendadas para acesso condicional no Azure Active Directory](best-practices.md).
