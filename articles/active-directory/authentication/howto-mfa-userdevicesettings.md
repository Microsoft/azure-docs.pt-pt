---
title: Gerir utilizadores e dispositivos Azure MFA - Diretório Ativo Azure
description: Como é que os administradores podem alterar as definições do utilizador, tais como forçar os utilizadores a fazer o processo de comprovação novamente.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 07845bb5b742b1bcfbb22d260457e9a8e16edab6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263715"
---
# <a name="manage-user-settings-with-azure-multi-factor-authentication-in-the-cloud"></a>Manage user settings with Azure Multi-Factor Authentication in the cloud (Gerir definições de utilizador com o Multi-Factor Authentication do Azure na cloud)

Como administrador, pode gerir as seguintes definições de utilizador e dispositivo:

* Exigir que os utilizadores forneçam novamente métodos de contacto
* Eliminar senhas de aplicativo
* Exigir MFA em todos os dispositivos fidedignos

## <a name="manage-authentication-methods"></a>Gerir métodos de autenticação

Como administrador designado pela função de Administrador de Autenticação, pode exigir que os utilizadores redefinirem a sua palavra-passe, re-registem-se para o MFA ou revoguem as sessões de MFA existentes a partir do seu objeto de utilizador.

![Gerir métodos de autenticação a partir do portal Azure](./media/howto-mfa-userdevicesettings/manage-authentication-methods-in-azure.png)

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
1. À esquerda, selecione **Utilizadores de Diretório** > **Users** > Ativo Azure**Todos os utilizadores**.
1. Escolha o utilizador que deseja realizar uma ação e **selecione métodos**de autenticação .
   - **Redefinir a palavra-passe redefinirá** a palavra-passe do utilizador e atribuirá uma senha temporária que deve ser alterada no próximo início de sessão.
   - **Exigir o re-registo do MFA** fará com que, quando o utilizador assinar na próxima vez, seja solicitado que instale um novo método de autenticação MFA.
   - **A Revogação das Sessões de MFA** iliba as sessões de MFA lembradas do utilizador e exige que realizem MFA da próxima vez que for exigido pela apólice no dispositivo.

## <a name="delete-users-existing-app-passwords"></a>Eliminar as senhas de aplicação existentes dos utilizadores

Esta definição elimina todas as palavras-passe da aplicação que um utilizador criou. As aplicações não-navegadoras que estavam associadas a estas palavras-passe da aplicação deixam de funcionar até que seja criada uma nova senha de aplicação. São necessárias permissões globais de administradores para realizar esta ação.

### <a name="how-to-delete-users-existing-app-passwords"></a>Como eliminar as senhas de aplicação existentes dos utilizadores

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. À esquerda, selecione **Utilizadores de Diretório** > **Users** > Ativo Azure**Todos os utilizadores**.
3. À direita, selecione **A autenticação multi-factor** na barra de ferramentas. A página de autenticação de vários fatores abre.
4. Verifique a caixa ao lado do utilizador ou utilizadores que deseja gerir. Uma lista de opções rápidas aparece à direita.
5. Selecione **Gerir as definições do utilizador**.
6. Verifique a caixa para **Eliminar todas as palavras-passe de aplicação existentes geradas pelos utilizadores selecionados**.
   ![Eliminar todas as senhas de aplicação existentes](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
7. Clique em **guardar**.
8. Clique **perto**.

## <a name="next-steps"></a>Passos seguintes

- Obtenha mais informações sobre como [configurar as definições de autenticação multi-factor do Azure](howto-mfa-mfasettings.md)
- Se os seus utilizadores precisarem de ajuda, aponte-os para o guia do [Utilizador para verificação em duas etapas](../user-help/multi-factor-authentication-end-user.md)
