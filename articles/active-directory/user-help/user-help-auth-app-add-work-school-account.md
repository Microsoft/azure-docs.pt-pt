---
title: Adicionar uma conta corporativa ou de estudante ao aplicativo Microsoft Authenticator-Azure AD
description: Adicione sua conta corporativa ou de estudante ao aplicativo Microsoft Authenticator para verificar sua identidade ao usar a verificação de dois fatores.
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
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77063922"
---
# <a name="add-your-work-or-school-account-to-the-microsoft-authenticator-app"></a>Adicionar sua conta corporativa ou de estudante ao aplicativo Microsoft Authenticator

Se sua organização usa a verificação de dois fatores, você pode configurar sua conta corporativa ou de estudante para usar o aplicativo Microsoft Authenticator como um dos métodos de verificação.

>[!Important]
>Para poder adicionar sua conta, você deve baixar e instalar o aplicativo Microsoft Authenticator. Se ainda não o fez, siga os passos no Download e instale o artigo [da aplicação.](user-help-auth-app-download-install.md)

## <a name="add-your-work-or-school-account"></a>Adicionar sua conta corporativa ou de estudante

1. No seu computador, vá à página adicional de verificação de [segurança.](https://account.activedirectory.windowsazure.com/proofup.aspx?proofup=1)

    >[!Note]
    >Se não vir a página adicional de verificação de **segurança,** é possível que o seu administrador tenha ligado a experiência de informação de segurança (pré-visualização). Se for esse o caso, deve seguir as instruções na Configuração de informações de segurança para utilizar uma secção de [aplicação autenticadora.](security-info-setup-auth-app.md) Se esse não for o caso, você precisará entrar em contato com o suporte técnico da sua organização para obter ajuda. Para mais informações sobre informações de segurança, consulte a visão geral da informação de [segurança (pré-visualização).](user-help-security-info-overview.md)

2. Verifique a caixa ao lado da **aplicação Authenticator**e, em seguida, selecione **Configurar**.

    Aparece a página da **aplicação móvel Configure.**

    ![Tela que fornece o código QR](./media/user-help-auth-app-download-install/auth-app-barcode.png)

3. Abra a aplicação Microsoft Authenticator, selecione **Adicionar conta** do ícone Personalizar **e controlar** no canto superior direito e, em seguida, selecione Trabalho ou **conta escolar**.

    >[!Note]
    >Se esta for a primeira vez que você estiver configurando o aplicativo Microsoft Authenticator, você poderá receber um prompt perguntando se deseja permitir que o aplicativo acesse sua câmera (iOS) ou permitir que o aplicativo tire fotos e grave vídeo (Android). Deve selecionar **Permitir** para que a aplicação autenticadora possa aceder à sua câmara para tirar uma fotografia do código QR no próximo passo. Se você não permitir a câmera, ainda poderá configurar o aplicativo autenticador, mas precisará adicionar as informações de código manualmente. Para obter informações sobre como adicionar o código manualmente, consulte [Manualmente adicionar uma conta à aplicação](user-help-auth-app-add-account-manual.md).

4. Utilize a câmara do seu dispositivo para digitalizar o código QR a partir do ecrã de **aplicações móveis Configure** no seu computador e, em seguida, escolha **Done**.

    >[!Note]
    >Se a câmera não conseguir capturar o código QR, você poderá adicionar manualmente as informações da conta ao aplicativo Microsoft Authenticator para verificação de dois fatores. Para mais informações e como fazê-lo, consulte [Manualmente adicionar a sua conta](user-help-auth-app-add-account-manual.md).

5. Reveja o ecrã de Contas da aplicação no seu dispositivo, para se certificar de que a sua conta está certa e que existe um código de verificação de seis **dígitos** associado. Para segurança adicional, o código de verificação é alterado a cada 30 segundos, impedindo que alguém use um código várias vezes.

    ![Tela de contas](./media/user-help-auth-app-download-install/auth-app-accounts.png)

## <a name="next-steps"></a>Passos seguintes

- Depois de adicionar suas contas ao aplicativo, você pode entrar usando o aplicativo autenticador em seu dispositivo. Para mais informações, consulte [O Sign in usando a aplicação](user-help-auth-app-sign-in.md).

- Para dispositivos que executam o iOS, você também pode fazer backup de suas credenciais de conta e configurações de aplicativo relacionadas, como a ordem de suas contas, para a nuvem. Para mais informações, consulte [Backup e recupere com a aplicação Microsoft Authenticator](user-help-auth-app-backup-recovery.md).
