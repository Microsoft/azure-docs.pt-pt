---
title: Definir as configurações de função do Azure AD no Privileged Identity Management-Azure Active Directory | Microsoft Docs
description: Saiba como definir as configurações de função do Azure AD no Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 10/22/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: cff298e24ac185767e6290e396818ccece7b9b55
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72809146"
---
# <a name="configure-azure-ad-role-settings-in-privileged-identity-management"></a>Definir as configurações de função do Azure AD no Privileged Identity Management

Um administrador de função com privilégios pode personalizar Privileged Identity Management (PIM) em sua organização do Azure Active Directory (Azure AD), incluindo a alteração da experiência de um usuário que está ativando uma atribuição de função qualificada.

## <a name="open-role-settings"></a>Abrir configurações de função

Siga estas etapas para abrir as configurações de uma função do Azure AD.

1. Abra **Azure ad Privileged Identity Management**.

1. Clique em **funções do Azure ad**.

1. Clique em **configurações**.

    ![Funções do Azure AD – configurações](./media/pim-how-to-change-default-settings/pim-directory-roles-settings.png)

1. Clique em **funções**.

1. Clique na função cujas configurações você deseja configurar.

    ![Funções do Azure AD – funções de configurações](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-role.png)

    Na página Configurações de cada função, há várias configurações que você pode configurar. Essas configurações afetam apenas os usuários que são atribuições **qualificadas** , não atribuições **permanentes** .

## <a name="activations"></a>Ativações

Use o controle deslizante de **ativações** para definir o tempo máximo, em horas, que uma função permanece ativa antes de expirar. Esse valor pode ser entre 1 e 72 horas.

## <a name="notifications"></a>Notificações

Use a opção de **notificações** para especificar se os administradores receberão notificações por email quando as funções forem ativadas. Essa notificação pode ser útil para detectar ativações não autorizadas ou ilegítimos.

Quando definido como **habilitar**, as notificações são enviadas para:

- Administrador de função com privilégios
- Administrador de segurança
- Administrador global

Para obter mais informações, consulte [notificações por email em Privileged Identity Management](pim-email-notifications.md).

## <a name="incidentrequest-ticket"></a>Tíquete de solicitação/incidente

Use a opção de **tíquete de incidente/solicitação** para exigir que os administradores qualificados incluam um número de tíquete quando ativarem sua função. Essa prática pode tornar as auditorias de acesso de função mais eficientes.

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Use a opção **autenticação multifator** para especificar se deseja exigir que os usuários verifiquem sua identidade com o MFA antes que possam ativar suas funções. Eles só precisam verificar sua identidade uma vez por sessão, nem sempre que ativam uma função. Há duas dicas para ter em mente ao habilitar a MFA:

- Os usuários que têm contas da Microsoft para seus endereços de email (normalmente @outlook.com, mas nem sempre) não podem se registrar para a autenticação multifator do Azure. Se você quiser atribuir funções a usuários com contas da Microsoft, você deve torná-los administradores permanentes ou desabilitar a autenticação multifator para essa função.
- Não é possível desabilitar a autenticação multifator do Azure para funções altamente privilegiadas para o Azure AD e o Office 365. Esse recurso de segurança ajuda a proteger as seguintes funções:  
  
  - Administrador da proteção de informações do Azure
  - Administrador de faturação
  - Administrador de aplicativos de nuvem
  - Administrador de conformidade
  - Administrador de acesso condicional
  - Administrador do Dynamics 365
  - Aprovador de acesso de LockBox do cliente
  - Gravadores de diretório
  - Administrador do Exchange
  - Administrador global
  - Administrador do Intune
  - Administrador de Power BI
  - Administrador de função com privilégios
  - Administrador de segurança
  - Administrador do SharePoint
  - Administrador do Skype for Business
  - Administrador do usuário

Para obter mais informações, consulte [autenticação multifator e Privileged Identity Management](pim-how-to-require-mfa.md).

## <a name="require-approval"></a>Exigir aprovação

Se você quiser delegar a aprovação necessária para ativar uma função, siga estas etapas.

1. Defina a opção **exigir aprovação** como **habilitada**. O painel se expande com opções para selecionar aprovadores.

    ![Funções do Azure AD – configurações-exigir aprovação](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval.png)

    Se você não especificar nenhum aprovador, o administrador da função com privilégios se tornará o aprovador padrão e, em seguida, será necessário para aprovar todas as solicitações de ativação para essa função.

1. Para especificar os aprovadores, clique em **selecionar aprovadores**.

    ![Funções do Azure AD – configurações-exigir aprovação](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval-select-approvers.png)

1. Selecione um ou mais Aprovadores, além do administrador de função com privilégios e, em seguida, clique em **selecionar**. Você pode selecionar usuários ou grupos. Recomendamos pelo menos dois aprovadores. Mesmo que você se adicione como um aprovador, não é possível aprovar automaticamente uma ativação de função. Suas seleções aparecerão na lista de aprovadores selecionados.

1. Depois de especificar todas as suas configurações de função, selecione **salvar** para salvar as alterações.

<!--PLACEHOLDER: Need an explanation of what the temporary Global Administrator setting is for.-->

## <a name="next-steps"></a>Passos seguintes

- [Atribuir funções do Azure AD no Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Configurar alertas de segurança para funções do Azure AD no Privileged Identity Management](pim-how-to-configure-security-alerts.md)
