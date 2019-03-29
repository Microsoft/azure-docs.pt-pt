---
title: Configurar definições de função do Azure AD no PIM - Azure Active Directory | Documentos da Microsoft
description: Saiba como configurar as definições de função do Azure AD no Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 11/30/2018
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: b14cf76ec7b8b905c41792f4dc9a56e97dc0f7ba
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2019
ms.locfileid: "58576867"
---
# <a name="configure-azure-ad-role-settings-in-pim"></a>Configurar definições de função do Azure AD no PIM

Um administrador com função privilegiada pode personalizar na sua organização, incluindo a alteração da experiência de para um utilizador que consiste em ativar uma atribuição de função elegível, a Azure Active Directory (Azure AD) Privileged Identity Management (PIM).

## <a name="open-role-settings"></a>Abra as definições de função

Siga estes passos para abrir as definições para uma função do Azure AD.

1. Open **do Azure AD Privileged Identity Management**.

1. Clique em **funções do Azure AD**.

1. Clique em **definições**.

    ![Funções do Azure AD - definições](./media/pim-how-to-change-default-settings/pim-directory-roles-settings.png)

1. Clique em **funções**.

1. Clique na função cujas definições pretende configurar.

    ![Funções do Azure do AD - definições de funções](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-role.png)

    Na página de definições para cada função, existem várias definições que pode configurar. Estas definições afetam apenas os utilizadores que estão **elegíveis** atribuições, não **permanente** atribuições.

## <a name="activations"></a>Ativações

Utilize o **ativações** controlo de deslize para definir o tempo máximo, em horas, que uma função fica ativa antes de expirar. Este valor pode ser entre 1 e 72 horas.

## <a name="notifications"></a>Notificações

Utilize o **notificações** comutador para especificar se os administradores receberá notificações por e-mail quando funções são ativadas. Isso pode ser útil para detetar ativações não autorizadas ou ilegítimo.

Quando definido como **ativar**, as notificações são enviadas para:

- Administrador com Função Privilegiada
- Administrador de Segurança
- Administrador Global

Para obter mais informações, consulte [notificações no PIM por E-Mail](pim-email-notifications.md).

## <a name="incidentrequest-ticket"></a>Pedido de suporte de incidente/pedido 

Utilize o **pedido de incidente/pedido** comutador para especificar se exigir que os administradores elegíveis incluir um número de pedido de suporte quando ativar a respetiva função. Isso pode ser útil ao realizar auditorias de acesso de função.

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Utilize o **multi-factor Authentication** comutador para especificar se pretende exigir que os utilizadores verifiquem a respetiva identidade com a MFA para poderem ativar as suas funções. Eles só precisam verificar este vez por sessão, não toda vez que os utilizadores ativar uma função. Há duas dicas para ter em mente ao ativar a MFA:

* Os utilizadores com contas Microsoft para os respetivos endereços de e-mail (normalmente @outlook.com, mas não sempre) não é possível registar para MFA do Azure. Se pretender atribuir funções a utilizadores com contas Microsoft, deve torná-los a administradores permanentes ou desativar a MFA para essa função.
* Não é possível desativar o MFA para funções com privilégios elevados para o Azure AD e Office 365. Esse é um recurso de segurança, uma vez que estas funções devem ser protegidas com cuidado:  
  
  * Administrador de Faturação
  * Administrador da Aplicação Cloud
  * Administrador de Conformidade
  * Administrador de Acesso Condicional
  * Administrador de Serviço CRM
  * Aprovador de Acesso ao Cofre de Cliente
  * Gravadores de Diretórios
  * Administrador do Exchange
  * Administrador Global
  * Administrador do Information Protection
  * Administrador de Serviços do Intune
  * Administrador do Power BI
  * Administrador com Função Privilegiada
  * Administrador de Segurança
  * Administrador do Serviço SharePoint
  * Administrador do Skype para Empresas
  * Administrador de Utilizadores

Para obter mais informações, consulte [multi-factor authentication (MFA) e o PIM](pim-how-to-require-mfa.md).

## <a name="require-approval"></a>Exigir aprovação

Se quiser exigir aprovação para ativar uma função, siga estes passos.

1. Definir o **exigem a aprovação** mudar para **ativado**. O painel expande-se com as opções para selecionar os aprovadores.

    ![Funções do Azure AD, - Settings - exigir a aprovação](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval.png)

    Se **fazer não** especificar qualquer aprovadores, os administradores de função com privilégios se tornarem aprovadores padrão. Os administradores de função com privilégios seriam necessários para aprovar **todos os** solicitações de ativação para esta função.

1. Para especificar os aprovadores, clique em **selecionar aprovadores**.

    ![Funções do Azure AD, - Settings - exigir a aprovação](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval-select-approvers.png)

1. Selecione um ou mais aprovadores e, em seguida, clique em **selecione**. Pode selecionar utilizadores ou grupos. Recomenda-se, pelo menos, 2 aprovadores. Aprovação automática não é permitida.

    As suas seleções aparecerá na lista de aprovadores selecionados.

1. Depois de especificar as todas as suas definições de função, clique em **guardar** para guardar as alterações.


<!--PLACEHOLDER: Need an explanation of what the temporary Global Administrator setting is for.-->

## <a name="next-steps"></a>Passos Seguintes

- [Atribuir funções do Azure AD no PIM](pim-how-to-add-role-to-user.md)
- [Configurar alertas de segurança para funções do Azure AD no PIM](pim-how-to-configure-security-alerts.md)
