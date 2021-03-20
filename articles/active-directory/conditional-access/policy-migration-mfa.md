---
title: Migrar políticas de acesso condicional com autenticação multi-factor - Diretório Ativo Azure
description: Este artigo mostra como migrar uma política clássica que requer autenticação multi-factor no portal Azure.
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
ms.openlocfilehash: 5cc99241f5fa7c09c69db13dcb3f6c3aaa75e376
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "87027398"
---
# <a name="migrate-a-classic-policy-in-the-azure-portal"></a>Migrar uma política clássica no portal Azure

Este artigo mostra como migrar uma política clássica que requer **autenticação multi-factor** para uma aplicação em nuvem. Embora não seja um pré-requisito, recomendamos que leia [as políticas clássicas da Migração no portal Azure](policy-migration.md) antes de começar a migrar as suas políticas clássicas.

![Detalhes clássicos da política que requerem MFA para app Salesforce](./media/policy-migration/33.png)

O processo de migração consiste nas seguintes etapas:

1. [Abra a política clássica](#open-a-classic-policy) para obter as definições de configuração.
1. Crie uma nova política de acesso condicionado Azure AD para substituir a sua política clássica. 
1. Desative a política clássica.

## <a name="open-a-classic-policy"></a>Abrir uma política clássica

1. No [portal Azure,](https://portal.azure.com)navegue para acesso condicional de segurança **do Diretório Ativo Azure**  >    >  .
1. Selecione, **Políticas clássicas**.

   ![Vista de políticas clássicas](./media/policy-migration-mfa/12.png)

1. Na lista de políticas clássicas, selecione a política que deseja migrar. Documente as definições de configuração para que possa recriar com uma nova política de Acesso Condicional.

## <a name="create-a-new-conditional-access-policy"></a>Criar uma nova política de acesso condicional

1. No [portal Azure,](https://portal.azure.com)navegue para acesso condicional de segurança **do Diretório Ativo Azure**  >    >  .
1. Para criar uma nova política de acesso condicional, selecione **Nova política**.
1. Na página **Novo,** na caixa de texto **'Nome',** escreva um nome para a sua política.
1. Na secção **Atribuições,** clique em **Utilizadores e grupos**.
   1. Se tiver todos os utilizadores selecionados na sua política clássica, clique em **Todos os utilizadores**. 
   1. Se tiver grupos selecionados na sua política clássica, clique em **Selecionar utilizadores e grupos** e, em seguida, selecione os utilizadores e grupos necessários.
   1. Se tiver os grupos excluídos, clique no separador **Excluir** e, em seguida, selecione os utilizadores e grupos necessários. 
   1. Selecione **Feito**
1. Na secção **Atribuição,** clique em **aplicativos cloud ou ações**.
1. Na página de **aplicativos ou ações** cloud, execute os seguintes passos:
   1. Clique **em Selecionar apps**.
   1. Clique em **Selecionar**.
   1. Na página **'Selecionar',** selecione a sua aplicação cloud e, em seguida, clique em **Selecionar**.
   1. Na página de **aplicativos Cloud,** clique em **'Fazer'.**
1. Se tiver **requerer a autenticação multi-factor** selecionada:
   1. Na secção **controlos de acesso,** clique em **Grant**.
   1. Na página **Grant,** clique em **'Grant' e,** em seguida, clique em **Exigir autenticação multi-factor**.
   1. Clique em **Selecionar**.
1. Clique **em** para ativar a sua política e, em seguida, selecione **Guardar**.

   ![Criação de política de acesso condicional](./media/policy-migration-mfa/conditional-access-policy-migration.png)

## <a name="disable-the-classic-policy"></a>Desativar a política clássica

Para desativar a sua política clássica, clique em **Desativar** na vista **Detalhes.**

![Desativar as políticas clássicas](./media/policy-migration-mfa/14.png)

## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações sobre a migração política clássica, consulte [as políticas clássicas da Migração no portal Azure.](policy-migration.md)
- [Utilize o modo apenas de relatório para acesso condicional para determinar o impacto de novas decisões políticas.](concept-conditional-access-report-only.md)