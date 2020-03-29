---
title: Redefinir a palavra-passe de um utilizador - Diretório Ativo Azure / Microsoft Docs
description: Instruções sobre como redefinir a palavra-passe de um utilizador utilizando o Diretório Ativo Azure.
services: active-directory
author: msaburnley
manager: daveba
ms.assetid: fad5624b-2f13-4abc-b3d4-b347903a8f16
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 09/05/2018
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: b4fdbbd4d71a9c97259678413cd9e59ee8aeae6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "69032665"
---
# <a name="reset-a-users-password-using-azure-active-directory"></a>Redefinir a palavra-passe de um utilizador utilizando o Diretório Ativo do Azure

Como administrador, pode redefinir a palavra-passe de um utilizador se a palavra-passe for esquecida, se o utilizador for bloqueado de um dispositivo ou se o utilizador nunca recebeu uma palavra-passe.

>[!Note]
>A menos que o seu inquilino Azure AD seja o diretório de casa para um utilizador, não poderá redefinir a sua palavra-passe. Isto significa que se o utilizador estiver a iniciar sessão na sua organização utilizando uma conta de outra organização, uma conta Microsoft ou uma conta Google, não poderá redefinir a sua palavra-passe.<br><br>Se o utilizador tiver uma fonte de autoridade como Diretório Ativo do Windows Server, só poderá redefinir a palavra-passe se tiver ativado a reescrita da palavra-passe.<br><br>Se o utilizador tiver uma fonte de autoridade como AD Azure Externo, não poderá redefinir a palavra-passe. Apenas o utilizador, ou um administrador em AD Azure Externo, pode redefinir a palavra-passe.

>[!Note]
>Se não é administrador e, em vez disso, procura instruções sobre como redefinir o seu próprio trabalho ou senha escolar, consulte [Redefinir o seu trabalho ou senha escolar](../user-help/active-directory-passwords-update-your-own-password.md).

## <a name="to-reset-a-password"></a>Para redefinir uma palavra-passe

1. Inscreva-se no [portal Azure](https://portal.azure.com/) como administrador de utilizador ou administrador de passwords. Para obter mais informações sobre as funções disponíveis, consulte as funções de [administrador de atribuição no Diretório Ativo azure](../users-groups-roles/directory-assign-admin-roles.md#available-roles)

2. Selecione **Diretório Ativo Azure**, selecione **Utilizadores,** procure e selecione o utilizador que necessita do reset e, em seguida, selecione **Reset Password**.

    A página **Alain Charon - Profile** aparece com a opção **de palavra-passe Reset.**

    ![Página de perfil do utilizador, com a opção de palavra-passe Redefinida destacada](media/active-directory-users-reset-password-azure-portal/user-profile-reset-password-link.png)

3. Na página de **palavra-passe Redefinir,** selecione **redefinir a palavra-passe**.

    > [!Note]
    > Ao utilizar o Diretório Ativo Azure, uma palavra-passe temporária é gerada automaticamente para o utilizador. Ao utilizar o Ative Directory no local, cria a palavra-passe para o utilizador.

4. Copie a palavra-passe e dê-a ao utilizador. O utilizador será obrigado a alterar a palavra-passe durante o próximo processo de iniciar sessão.

    >[!Note]
    >A senha temporária nunca expira. Da próxima vez que o utilizador entrar, a senha continuará a funcionar, independentemente do tempo que passou desde que a senha temporária foi gerada.

## <a name="next-steps"></a>Passos seguintes

Depois de redefinir a palavra-passe do utilizador, pode executar os seguintes processos básicos:

- [Adicionar ou eliminar utilizadores](add-users-azure-active-directory.md)

- [Atribuir funções a utilizadores](active-directory-users-assign-role-azure-portal.md)

- [Adicionar ou alterar informações sobre o perfil](active-directory-users-profile-azure-portal.md)

- [Criar um grupo básico e adicionar membros](active-directory-groups-create-azure-portal.md)

Ou pode realizar cenários de utilizador mais complexos, tais como atribuir delegados, usar políticas e partilhar contas de utilizadores. Para obter mais informações sobre outras ações disponíveis, consulte a documentação de [gestão de utilizadores do Azure Ative Directory.](../users-groups-roles/index.yml)
