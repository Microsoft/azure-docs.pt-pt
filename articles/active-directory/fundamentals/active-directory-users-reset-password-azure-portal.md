---
title: Redefinir a palavra-passe de um utilizador - Azure Ative Directory Microsoft Docs
description: Instruções sobre como redefinir a palavra-passe de um utilizador utilizando o Azure Ative Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.assetid: fad5624b-2f13-4abc-b3d4-b347903a8f16
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
ms.date: 09/05/2018
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 397c74203aae2f52ce81844695266cc36fdf3042
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92370904"
---
# <a name="reset-a-users-password-using-azure-active-directory"></a>Repor a palavra-passe do utilizador com o Azure Active Directory

Como administrador, pode redefinir a palavra-passe de um utilizador se a palavra-passe for esquecida, se o utilizador for bloqueado fora de um dispositivo ou se o utilizador nunca tiver recebido uma palavra-passe.

>[!Note]
>A menos que o seu inquilino Azure AD seja o diretório de casa para um utilizador, você não poderá redefinir a sua senha. Isto significa que se o seu utilizador estiver a iniciar sessão na sua organização utilizando uma conta de outra organização, uma conta Microsoft ou uma conta google, não poderá redefinir a sua palavra-passe.<br><br>Se o seu utilizador tiver uma fonte de autoridade como Diretoria Ative do Windows Server, só poderá redefinir a palavra-passe se tiver ativado a gravação da palavra-passe.<br><br>Se o seu utilizador tiver uma fonte de autoridade como Ad Azure Externo, não poderá redefinir a palavra-passe. Apenas o utilizador, ou um administrador em Ad Azure Externo, pode redefinir a palavra-passe.

>[!Note]
>Se não for administrador e estiver, em vez disso, à procura de instruções sobre como redefinir o seu próprio trabalho ou senha escolar, consulte [redefinir o seu trabalho ou a palavra-passe da escola](../user-help/active-directory-passwords-update-your-own-password.md).

## <a name="to-reset-a-password"></a>Para redefinir uma palavra-passe

1. Inscreva-se no [portal Azure](https://portal.azure.com/) como administrador de utilizador ou administrador de password. Para obter mais informações sobre as funções disponíveis, consulte [atribuindo funções de administrador no Azure Ative Directory](../roles/permissions-reference.md#available-roles)

2. Selecione **Azure Ative Directory**, selecione **Utilizadores**, procure e selecione o utilizador que necessita do reset e, em seguida, **selecione Reset Password**.

    A página **Alain Charon - Perfil** aparece com a opção **de palavra-passe Reset.**

    ![Página de perfil do utilizador, com a opção de palavra-passe reset realçada](media/active-directory-users-reset-password-azure-portal/user-profile-reset-password-link.png)

3. Na página de **palavra-passe 'Redefinir' 'Redefinir',** **selecione Redefinir a palavra-passe**.

    > [!Note]
    > Ao utilizar o Azure Ative Directory, uma palavra-passe temporária é gerada automaticamente para o utilizador. Ao utilizar o Ative Directory no local, cria a palavra-passe para o utilizador.

4. Copie a palavra-passe e entregue-a ao utilizador. O utilizador será obrigado a alterar a palavra-passe durante o próximo processo de inscrição.

    >[!Note]
    >A senha temporária nunca expira. Da próxima vez que o utilizador entrar, a palavra-passe continuará a funcionar, independentemente do tempo que passou desde que a senha temporária foi gerada.

## <a name="next-steps"></a>Passos seguintes

Depois de ter reposto a palavra-passe do utilizador, pode efetuar os seguintes processos básicos:

- [Adicionar ou eliminar utilizadores](add-users-azure-active-directory.md)

- [Atribuir funções a utilizadores](active-directory-users-assign-role-azure-portal.md)

- [Adicionar ou alterar informações de perfil](active-directory-users-profile-azure-portal.md)

- [Criar um grupo básico e adicionar membros](active-directory-groups-create-azure-portal.md)

Ou pode realizar cenários de utilizador mais complexos, como atribuir delegados, usar políticas e partilhar contas de utilizadores. Para obter mais informações sobre outras ações disponíveis, consulte [a documentação de gestão de utilizadores do Azure Ative Directory](../enterprise-users/index.yml).
