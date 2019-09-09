---
title: Definir as configurações de função do Azure AD no PIM-Azure Active Directory | Microsoft Docs
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
ms.date: 05/31/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e9252e3bb8ccddb810074b485f6f073f1bda3f05
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804432"
---
# <a name="configure-azure-ad-role-settings-in-pim"></a>Definir as configurações de função do Azure AD no PIM

Um administrador de função com privilégios pode personalizar o Azure Active Directory (Azure AD) Privileged Identity Management (PIM) em sua organização, incluindo a alteração da experiência de um usuário que está ativando uma atribuição de função qualificada.

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

Use a opção de **notificações** para especificar se os administradores receberão notificações por email quando as funções forem ativadas. Isso pode ser útil para detectar ativações não autorizadas ou ilegítimos.

Quando definido como **habilitar**, as notificações são enviadas para:

- Administrador com Função Privilegiada
- Administrador de Segurança
- Administrador Global

Para obter mais informações, consulte [notificações por email no PIM](pim-email-notifications.md).

## <a name="incidentrequest-ticket"></a>Tíquete de solicitação/incidente

Use a opção de **tíquete de incidente/solicitação** para especificar se é necessário que os administradores qualificados incluam um número de tíquete quando ativarem sua função. Isso pode ser útil quando você executa auditorias de acesso de função.

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Use a opção **autenticação multifator** para especificar se deseja exigir que os usuários verifiquem sua identidade com o MFA antes que possam ativar suas funções. Eles só precisam verificar isso uma vez por sessão, nem sempre que ativam uma função. Há duas dicas para ter em mente ao habilitar a MFA:

* Os usuários que têm contas da Microsoft para seus endereços de @outlook.comemail (normalmente, mas nem sempre) não podem se registrar para o Azure MFA. Se você quiser atribuir funções a usuários com contas da Microsoft, deverá torná-las administradores permanentes ou desabilitar a MFA para essa função.
* Não é possível desabilitar o MFA para funções altamente privilegiadas para o Azure AD e o office365. Esse é um recurso de segurança porque essas funções devem ser cuidadosamente protegidas:  
  
  * Administrador da proteção de informações do Azure
  * Administrador de Faturação
  * Administrador de Aplicações de Cloud
  * Administrador de Conformidade
  * Administrador de Acesso Condicional
  * Administrador de Serviço de CRM
  * Aprovador de Acesso ao Sistema de Proteção de Dados do Cliente
  * Gravadores de Diretórios
  * Administrador do Exchange
  * Administrador Global
  * Administrador de Serviços do Intune
  * Administrador de Serviço do Power BI
  * Administrador com Função Privilegiada
  * Administrador de Segurança
  * Administrador do Serviço SharePoint
  * Administrador do Skype para Empresas
  * Administrador de Utilizadores

Para obter mais informações, consulte [autenticação multifator (MFA) e PIM](pim-how-to-require-mfa.md).

## <a name="require-approval"></a>Exigir aprovação

Se você quiser exigir aprovação para ativar uma função, siga estas etapas.

1. Defina a opção **exigir aprovação** como **habilitada**. O painel se expande com opções para selecionar aprovadores.

    ![Funções do Azure AD – configurações-exigir aprovação](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval.png)

    Se você **não** especificar nenhum aprovador, os administradores de função com privilégios se tornarão os aprovadores padrão. Administradores de função com privilégios seriam solicitados a aprovar **todas as** solicitações de ativação para essa função.

1. Para especificar os aprovadores, clique em **selecionar aprovadores**.

    ![Funções do Azure AD – configurações-exigir aprovação](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval-select-approvers.png)

1. Selecione um ou mais Aprovadores e clique em **selecionar**. Você pode selecionar usuários ou grupos. É recomendável pelo menos dois aprovadores. A autoaprovação não é permitida.

    Suas seleções aparecerão na lista de aprovadores selecionados.

1. Depois de especificar todas as suas configurações de função, clique em **salvar** para salvar suas alterações.


<!--PLACEHOLDER: Need an explanation of what the temporary Global Administrator setting is for.-->

## <a name="next-steps"></a>Passos Seguintes

- [Atribuir funções do Azure AD no PIM](pim-how-to-add-role-to-user.md)
- [Configurar alertas de segurança para funções do Azure AD no PIM](pim-how-to-configure-security-alerts.md)
