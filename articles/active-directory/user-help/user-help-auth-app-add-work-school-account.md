---
title: Adicione uma conta de trabalho ou escola à aplicação Microsoft Authenticator - Azure AD
description: Adicione o seu trabalho ou conta escolar à aplicação Microsoft Authenticator para verificar a sua identidade enquanto utiliza a verificação de dois fatores.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: f0cc14a53f7ead7f0a496728d477d7d30857a0fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063922"
---
# <a name="add-your-work-or-school-account-to-the-microsoft-authenticator-app"></a>Adicione o seu trabalho ou conta escolar à aplicação Microsoft Authenticator

Se a sua organização utilizar a verificação de dois fatores, pode configurar o seu trabalho ou conta escolar para utilizar a aplicação Microsoft Authenticator como um dos métodos de verificação.

>[!Important]
>Antes de poder adicionar a sua conta, tem de descarregar e instalar a aplicação Microsoft Authenticator. Se ainda não o fez, siga os passos no Download e instale o artigo [da aplicação.](user-help-auth-app-download-install.md)

## <a name="add-your-work-or-school-account"></a>Adicione o seu trabalho ou conta escolar

1. No seu computador, vá à página adicional de verificação de [segurança.](https://account.activedirectory.windowsazure.com/proofup.aspx?proofup=1)

    >[!Note]
    >Se não vir a página adicional de verificação de **segurança,** é possível que o seu administrador tenha ligado a experiência de informação de segurança (pré-visualização). Se for esse o caso, deve seguir as instruções na Configuração de informações de segurança para utilizar uma secção de [aplicação autenticadora.](security-info-setup-auth-app.md) Se não for esse o caso, terá de contactar o Balcão de Ajuda da sua organização para obter assistência. Para mais informações sobre informações de segurança, consulte a visão geral da informação de [segurança (pré-visualização).](user-help-security-info-overview.md)

2. Verifique a caixa ao lado da **aplicação Authenticator**e, em seguida, selecione **Configurar**.

    Aparece a página da **aplicação móvel Configure.**

    ![Tela que fornece o código QR](./media/user-help-auth-app-download-install/auth-app-barcode.png)

3. Abra a aplicação Microsoft Authenticator, selecione **Adicionar conta** do ícone Personalizar **e controlar** no canto superior direito e, em seguida, selecione Trabalho ou **conta escolar**.

    >[!Note]
    >Se esta for a primeira vez que está a configurar a aplicação Microsoft Authenticator, poderá receber uma pergunta rápida sobre se permite que a aplicação aceda à sua câmara (iOS) ou para permitir que a aplicação tire fotografias e grave vídeos (Android). Deve selecionar **Permitir** para que a aplicação autenticadora possa aceder à sua câmara para tirar uma fotografia do código QR no próximo passo. Se não permitir a câmara, ainda pode configurar a aplicação autenticadora, mas terá de adicionar manualmente a informação do código. Para obter informações sobre como adicionar o código manualmente, consulte [Manualmente adicionar uma conta à aplicação](user-help-auth-app-add-account-manual.md).

4. Utilize a câmara do seu dispositivo para digitalizar o código QR a partir do ecrã de **aplicações móveis Configure** no seu computador e, em seguida, escolha **Done**.

    >[!Note]
    >Se a sua câmara não conseguir capturar o código QR, pode adicionar manualmente as informações da sua conta à aplicação Microsoft Authenticator para verificação de dois fatores. Para mais informações e como fazê-lo, consulte [Manualmente adicionar a sua conta](user-help-auth-app-add-account-manual.md).

5. Reveja o ecrã de Contas da aplicação no seu dispositivo, para se certificar de que a sua conta está certa e que existe um código de verificação de seis **dígitos** associado. Para obter segurança adicional, o código de verificação muda a cada 30 segundos impedindo alguém de usar um código várias vezes.

    ![Tela de contas](./media/user-help-auth-app-download-install/auth-app-accounts.png)

## <a name="next-steps"></a>Passos seguintes

- Depois de adicionar as suas contas à aplicação, pode iniciar sessão utilizando a aplicação Autenticadora no seu dispositivo. Para mais informações, consulte [O Sign in usando a aplicação](user-help-auth-app-sign-in.md).

- Para dispositivos que executam o iOS, também pode fazer o back up as credenciais da sua conta e configurações de aplicações relacionadas, como a ordem das suas contas, para a nuvem. Para mais informações, consulte [Backup e recupere com a aplicação Microsoft Authenticator](user-help-auth-app-backup-recovery.md).
