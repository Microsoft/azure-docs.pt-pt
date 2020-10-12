---
title: Adicione uma conta de trabalho ou escola à app Microsoft Authenticator - Azure AD
description: Adicione o seu trabalho ou conta escolar à aplicação Microsoft Authenticator para verificar a sua identidade enquanto utiliza a verificação de dois fatores.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 01/24/2019
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: e003c45aa1e7d75b709b7fbf99532fb1302fcbb9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88797654"
---
# <a name="add-your-work-or-school-account-to-the-microsoft-authenticator-app"></a>Adicione o seu trabalho ou conta escolar à aplicação Microsoft Authenticator

Se a sua organização utilizar a verificação de dois fatores, pode configurar a sua conta de trabalho ou escola para utilizar a aplicação Microsoft Authenticator como um dos métodos de verificação.

>[!Important]
>Antes de poder adicionar a sua conta, tem de descarregar e instalar a aplicação Microsoft Authenticator. Se ainda não o fez, siga os passos no Download e instale o artigo [da aplicação.](user-help-auth-app-download-install.md)

## <a name="add-your-work-or-school-account"></a>Adicione o seu trabalho ou conta escolar

1. No seu computador, aceda à página [adicional de verificação de segurança.](https://account.activedirectory.windowsazure.com/proofup.aspx?proofup=1)

    >[!Note]
    >Se não vir a página **de verificação de segurança adicional,** é possível que o seu administrador tenha ligado a experiência de informação de segurança (pré-visualização). Se for esse o caso, deve seguir as instruções na informação de segurança Configurar para utilizar uma secção [de aplicação autenticadora.](security-info-setup-auth-app.md) Se não for esse o caso, terá de contactar o Help Desk da sua organização para obter assistência. Para obter mais informações sobre informações de segurança, consulte [a informação de segurança (pré-visualização) visão geral](./security-info-setup-signin.md).

2. Verifique a caixa ao lado **da aplicação Authenticator**e, em seguida, selecione **Configure**.

    Aparece a página **de aplicações móveis Configure.**

    ![Ecrã que fornece o código QR](./media/user-help-auth-app-download-install/auth-app-barcode.png)

3. Abra a aplicação Microsoft Authenticator, **selecione Adicionar a conta** a partir do ícone De **personalizar e controlar** no canto superior direito e, em seguida, selecione Trabalho ou conta **escolar**.

    >[!Note]
    >Se esta for a primeira vez que está a configurar a aplicação Microsoft Authenticator, poderá receber uma solicitação a perguntar se permite que a app aceda à sua câmara (iOS) ou se permite que a app tire fotografias e grave vídeos (Android). Tem de selecionar **Permitir** que a aplicação autenticadora possa aceder à sua câmara para tirar uma fotografia do código QR no passo seguinte. Se não permitir a câmara, ainda pode configurar a aplicação autenticadora, mas terá de adicionar manualmente a informação de código. Para obter informações sobre como adicionar o código manualmente, consulte [Manualmente adicione uma conta à aplicação.](user-help-auth-app-add-account-manual.md)

4. Utilize a câmara do seu dispositivo para digitalizar o código QR a partir do ecrã de **aplicações móveis Configure** no seu computador e, em seguida, escolha **Feito**.

    >[!Note]
    >Se a sua câmara não conseguir capturar o código QR, pode adicionar manualmente as informações da sua conta à aplicação Microsoft Authenticator para verificação de dois fatores. Para mais informações e como fazê-lo, consulte [Manualmente adicionar a sua conta.](user-help-auth-app-add-account-manual.md)

5. Reveja o ecrã de **Contas** da aplicação no seu dispositivo, para se certificar de que a sua conta está correta e que existe um código de verificação de seis dígitos associado. Para uma segurança adicional, o código de verificação muda a cada 30 segundos impedindo alguém de usar um código várias vezes.

    ![Tela de contas](./media/user-help-auth-app-download-install/auth-app-accounts.png)

## <a name="next-steps"></a>Passos seguintes

- Depois de adicionar as suas contas à aplicação, pode iniciar sôms usando a aplicação Authenticator no seu dispositivo. Para obter mais informações, consulte [Iniciar sção usando a aplicação.](user-help-auth-app-sign-in.md)

- Para dispositivos que executam o iOS, também pode fazer o back up das suas credenciais de conta e configurações de aplicações relacionadas, como a ordem das suas contas, para a nuvem. Para obter mais informações, consulte [Backup e recupere com a aplicação Microsoft Authenticator](user-help-auth-app-backup-recovery.md).