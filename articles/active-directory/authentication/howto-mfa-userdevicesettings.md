---
title: Gerenciar usuários e dispositivos Azure MFA-Azure Active Directory
description: Como os administradores podem alterar as configurações do usuário, como forçar os usuários a fazer o processo de verificação novamente.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5fd03ea807e48f6f0e287bb4497e4d20268995db
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74404169"
---
# <a name="manage-user-settings-with-azure-multi-factor-authentication-in-the-cloud"></a>Gerenciar configurações de usuário com a autenticação multifator do Azure na nuvem

Como administrador, você pode gerenciar as seguintes configurações de usuário e dispositivo:

* Exigir que os usuários forneçam métodos de contato novamente
* Excluir senhas de aplicativo
* Exigir MFA em todos os dispositivos confiáveis

## <a name="manage-authentication-methods"></a>Gerenciar métodos de autenticação

Como um administrador atribuiu a função de administrador de autenticação, você pode exigir que os usuários redefinam sua senha, registrem-se novamente para MFA ou revogar sessões de MFA existentes de seu objeto de usuário.

![Gerenciar métodos de autenticação do portal do Azure](./media/howto-mfa-userdevicesettings/manage-authentication-methods.png)

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
1. À esquerda, selecione **Azure Active Directory** > **usuários** > **todos os usuários**.
1. Escolha o usuário no qual você deseja executar uma ação e selecione **métodos de autenticação**.
   - **Redefinir senha** redefinirá a senha do usuário e atribuirá uma senha temporária que deve ser alterada na próxima entrada.
   - **Exigir o novo registro da MFA** fará com que, quando o usuário entrar na próxima vez, será solicitado a configurar um novo método de autenticação de MFA.
   - **Revogar sessões de MFA** limpa as sessões do MFA lembradas do usuário e exige que elas executem o MFA na próxima vez que ela for exigida pela política no dispositivo.

## <a name="delete-users-existing-app-passwords"></a>Excluir senhas de aplicativo de usuários existentes

Essa configuração exclui todas as senhas de aplicativo que um usuário criou. Aplicativos sem navegador que estavam associados a essas senhas de aplicativo param de funcionar até que uma nova senha de aplicativo seja criada. São necessárias permissões de administrador global para executar esta ação.

### <a name="how-to-delete-users-existing-app-passwords"></a>Como excluir senhas de aplicativo de usuários existentes

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. À esquerda, selecione **Azure Active Directory** > **usuários** > **todos os usuários**.
3. À direita, selecione **autenticação multifator** na barra de ferramentas. A página autenticação multifator é aberta.
4. Marque a caixa ao lado do usuário ou usuários que você deseja gerenciar. Uma lista de opções de etapa rápida é exibida à direita.
5. Selecione **gerenciar configurações do usuário**.
6. Marque a caixa **excluir todas as senhas de aplicativo existentes geradas pelos usuários selecionados**.
   ![excluir todas as senhas de aplicativo existentes](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
7. Clique em **Guardar**.
8. Clique em **fechar**.

## <a name="next-steps"></a>Passos seguintes

- Obter mais informações sobre como [definir as configurações de autenticação multifator do Azure](howto-mfa-mfasettings.md)
- Se os usuários precisarem de ajuda, aponte [para o guia do usuário para verificação em duas etapas](../user-help/multi-factor-authentication-end-user.md)
