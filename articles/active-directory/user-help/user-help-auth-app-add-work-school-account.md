---
title: Adicionar sua conta corporativa ou de estudante ao Microsoft Authenticator app-Azure Active Directory | Microsoft Docs
description: Como adicionar sua conta corporativa ou de estudante ao aplicativo Microsoft Authenticator para verificação de dois fatores.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: lizross
ms.reviewer: olhaun
ms.collection: M365-identity-device-management
ms.openlocfilehash: a73500d17a0dd5d55e60700f7c0b6dbe92a3f96b
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/22/2019
ms.locfileid: "68382525"
---
# <a name="add-your-work-or-school-account"></a>Adicionar sua conta corporativa ou de estudante

Se sua organização usa a verificação de dois fatores, você pode configurar sua conta corporativa ou de estudante para usar o aplicativo Microsoft Authenticator como um dos métodos de verificação.

>[!Important]
>Para poder adicionar sua conta, você deve baixar e instalar o aplicativo Microsoft Authenticator. Se ainda não tiver feito isso, siga as etapas no artigo [baixar e instalar o aplicativo](user-help-auth-app-download-install.md) .

## <a name="add-your-work-or-school-account"></a>Adicionar sua conta corporativa ou de estudante

1. Em seu computador, vá para a página de [verificação de segurança adicional](https://aka.ms/mfasetup) .

    >[!Note]
    >Se você não vir a página de **verificação de segurança adicional** , é possível que o administrador tenha ativado a experiência de informações de segurança (versão prévia). Se esse for o caso, você deve seguir as instruções na seção [configurar informações de segurança para usar um aplicativo autenticador](security-info-setup-auth-app.md) . Se esse não for o caso, você precisará entrar em contato com o suporte técnico da sua organização para obter ajuda. Para obter mais informações sobre informações de segurança, consulte [visão geral de informações de segurança (versão prévia)](user-help-security-info-overview.md).

2. Marque a caixa ao lado de **aplicativo autenticador**e, em seguida, selecione **Configurar**.

    A página **Configurar aplicativo móvel** é exibida.

    ![Tela que fornece o código QR](./media/user-help-auth-app-download-install/auth-app-barcode.png)

3. Abra o aplicativo Microsoft Authenticator, selecione **adicionar conta** no ícone **Personalizar e controlar** no canto superior direito e selecione **conta corporativa ou de estudante**.

4. Use a câmera do dispositivo para digitalizar o código QR na tela **Configurar aplicativo móvel** no computador e, em seguida, escolha **concluído**.

    >[!Note]
    >Se a câmera não conseguir capturar o código QR, você poderá adicionar manualmente as informações da conta ao aplicativo Microsoft Authenticator para verificação de dois fatores. Para obter mais informações e como fazer isso, consulte [adicionar manualmente sua conta](user-help-auth-app-add-account-manual.md).

5. Examine a tela de **contas** do aplicativo em seu dispositivo para verificar se sua conta está correta e se há um código de verificação de seis dígitos associado. Para segurança adicional, o código de verificação é alterado a cada 30 segundos, impedindo que alguém use um código várias vezes.

    ![Tela de contas](./media/user-help-auth-app-download-install/auth-app-accounts.png)

## <a name="next-steps"></a>Passos Seguintes

- Depois de adicionar suas contas ao aplicativo, você pode entrar usando o aplicativo autenticador em seu dispositivo. Para obter mais informações, consulte [entrar usando o aplicativo](user-help-auth-app-sign-in.md).

- Para dispositivos que executam o iOS, você também pode fazer backup de suas credenciais de conta e configurações de aplicativo relacionadas, como a ordem de suas contas, para a nuvem. Para obter mais informações, consulte [fazer backup e recuperar com Microsoft Authenticator aplicativo](user-help-auth-app-backup-recovery.md).
