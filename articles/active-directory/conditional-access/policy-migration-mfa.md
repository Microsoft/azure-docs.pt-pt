---
title: Políticas de acesso condicional migrador - Diretório Ativo Azure
description: Este artigo mostra como migrar uma política clássica que requer a autenticação multifactor no portal Azure.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 05/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: d1dc7b7227dbaee488726a75fe7120bd26614dbf
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83993733"
---
# <a name="migrate-a-classic-policy-in-the-azure-portal"></a>Migrar uma política clássica no portal Azure

Este artigo mostra como migrar uma política clássica que requer **a autenticação de vários fatores** para uma aplicação na nuvem. Embora não seja um pré-requisito, recomendamos que leia [políticas clássicas de migrar no portal Azure](policy-migration.md) antes de começar a migrar as suas políticas clássicas.

![Detalhes de política clássicos que requerem MFA para app Salesforce](./media/policy-migration/33.png)

O processo de migração consiste nos seguintes passos:

1. [Abra a política clássica](#open-a-classic-policy) para obter as definições de configuração.
1. Crie uma nova política de Acesso Condicional Azure AD para substituir a sua política clássica. 
1. Desativar a política clássica.

## <a name="open-a-classic-policy"></a>Abra uma política clássica

1. No [portal Azure,](https://portal.azure.com)navegue até ao Acesso Condicional de Segurança **do Diretório Ativo**  >  **Security**  >  **Conditional Access**Azure.
1. Selecione, **políticas clássicas.**

   ![Visão de políticas clássicas](./media/policy-migration-mfa/12.png)

1. Na lista de políticas clássicas, selecione a política que pretende migrar. Documente as definições de configuração para que possa recriar com uma nova política de Acesso Condicional.

## <a name="create-a-new-conditional-access-policy"></a>Criar uma nova política de acesso condicional

1. No [portal Azure,](https://portal.azure.com)navegue até ao Acesso Condicional de Segurança **do Diretório Ativo**  >  **Security**  >  **Conditional Access**Azure.
1. Para criar uma nova política de Acesso Condicional, selecione **Nova política.**
1. Na página **Nova,** na caixa de texto **Name,** digite um nome para a sua apólice.
1. Na secção **Demissões,** clique em **Utilizadores e grupos**.
   1. Se tiver todos os utilizadores selecionados na sua política clássica, clique em **Todos os utilizadores**. 
   1. Se tiver grupos selecionados na sua política clássica, clique em **Selecionar utilizadores e grupos**e, em seguida, selecione os utilizadores e grupos necessários.
   1. Se tiver os grupos excluídos, clique no separador **Excluir** e, em seguida, selecione os utilizadores e grupos necessários. 
   1. Selecione **Feito**
1. Na secção **Atribuição,** clique em **aplicações ou ações cloud**.
1. Na página de **aplicações ou ações da Cloud,** execute os seguintes passos:
   1. Clique em **selecionar aplicações**.
   1. Clique em **Selecionar**.
   1. Na página **Select,** selecione a sua aplicação cloud e, em seguida, clique em **Selecionar**.
   1. Na página de **aplicações Cloud,** clique **em Done**.
1. Se tiver **exigido a autenticação de vários fatores** selecionada:
   1. Na secção **de controlos de acesso,** clique em **Grant**.
   1. Na página **Grant,** clique no **acesso ao Grant**, e clique em Exigir **autenticação de vários fatores**.
   1. Clique em **Selecionar**.
1. Clique **em On** para ativar a sua política e, em seguida, selecione **Guardar**.

   ![Criação de política de acesso condicional](./media/policy-migration-mfa/conditional-access-policy-migration.png)

## <a name="disable-the-classic-policy"></a>Desativar a política clássica

Para desativar a sua política clássica, clique em **Desativar** na vista **Detalhes.**

![Desativar políticas clássicas](./media/policy-migration-mfa/14.png)

## <a name="next-steps"></a>Próximos passos

- Para obter mais informações sobre a migração política clássica, consulte [as políticas clássicas de migrar no portal Azure.](policy-migration.md)
- [Utilize o modo de acesso condicional apenas para o relatório para determinar o impacto das novas decisões políticas.](concept-conditional-access-report-only.md)