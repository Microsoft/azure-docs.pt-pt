---
title: Migrar políticas de acesso condicional-Azure Active Directory
description: Este artigo mostra como migrar uma política clássica que requer autenticação multifator no portal do Azure.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/04/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: d637ac464b689a25ce5d5a79cf47da0c85d38d0f
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74846041"
---
# <a name="migrate-a-classic-policy-in-the-azure-portal"></a>Migrar uma política clássica no portal do Azure

Este artigo mostra como migrar uma política clássica que requer **autenticação multifator** para um aplicativo de nuvem. Embora não seja um pré-requisito, recomendamos que você leia [migrar políticas clássicas na portal do Azure antes de](policy-migration.md) começar a migrar suas políticas clássicas.

![Detalhes da política clássica que exigem MFA para o aplicativo Salesforce](./media/policy-migration/33.png)

O processo de migração consiste nas seguintes etapas:

1. [Abra a política clássica](#open-a-classic-policy) para obter as definições de configuração.
1. Crie uma nova política de acesso condicional do Azure AD para substituir sua política clássica. 
1. Desabilite a política clássica.

## <a name="open-a-classic-policy"></a>Abrir uma política clássica

1. Na [portal do Azure](https://portal.azure.com), navegue até **Azure Active Directory** > **segurança** > **acesso condicional**.
1. Selecione **as políticas clássicas**.

   ![Exibição de políticas clássicas](./media/policy-migration-mfa/12.png)

1. Na lista de políticas clássicas, selecione a política que você deseja migrar. Documente as definições de configuração para que você possa recriar com uma nova política de acesso condicional.

## <a name="create-a-new-conditional-access-policy"></a>Criar uma nova política de acesso condicional

1. Na [portal do Azure](https://portal.azure.com), navegue até **Azure Active Directory** > **segurança** > **acesso condicional**.
1. Para criar uma nova política de acesso condicional, selecione **nova política**.
1. Na página **novo** , na caixa de texto **nome** , digite um nome para a política.
1. Na seção **atribuições** , clique em **usuários e grupos**.
   1. Se você tiver todos os usuários selecionados em sua política clássica, clique em **todos os usuários**. 
   1. Se você tiver grupos selecionados em sua política clássica, clique em **Selecionar usuários e grupos**e selecione os usuários e grupos necessários.
   1. Se você tiver os grupos excluídos, clique na guia **excluir** e selecione os usuários e grupos necessários. 
   1. Selecionar **concluído**
1. Na seção **atribuição** , clique em **aplicativos de nuvem ou ações**.
1. Na página **aplicativos de nuvem ou ações** , execute as seguintes etapas:
   1. Clique em **selecionar aplicativos**.
   1. Clique em **Selecionar**.
   1. Na página **selecionar** , selecione seu aplicativo de nuvem e, em seguida, clique em **selecionar**.
   1. Na página **aplicativos de nuvem** , clique em **concluído**.
1. Se você **precisar de uma autenticação multifator** selecionada:
   1. Na seção **controles de acesso** , clique em **conceder**.
   1. Na página **conceder** , clique em **conceder acesso**e, em seguida, clique em **exigir autenticação multifator**.
   1. Clique em **Selecionar**.
1. Clique **em** ativar para habilitar a política e selecione **salvar**.

   ![Criação de política de acesso condicional](./media/policy-migration-mfa/conditional-access-policy-migration.png)

## <a name="disable-the-classic-policy"></a>Desabilitar a política clássica

Para desabilitar sua política clássica, clique em **desabilitar** na exibição de **detalhes** .

![Desabilitar políticas clássicas](./media/policy-migration-mfa/14.png)

## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações sobre a migração de política clássica, consulte [migrar políticas clássicas no portal do Azure](policy-migration.md).
- [Use o modo somente de relatório para acesso condicional para determinar o impacto das novas decisões de política.](concept-conditional-access-report-only.md)