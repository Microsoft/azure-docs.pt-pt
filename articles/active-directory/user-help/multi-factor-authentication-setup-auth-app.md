---
title: Configurar um aplicativo autenticador como seu método de verificação de dois fatores-Azure Active Directory | Microsoft Docs
description: Saiba como configurar o aplicativo Microsoft Authenticator como seu método de verificação de dois fatores.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: overview
ms.date: 08/12/2019
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: e3a09ccdc867cb6d0e84a6e0f85e532839604f5e
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69619440"
---
# <a name="set-up-an-authenticator-app-as-your-two-factor-verification-method"></a>Configurar um aplicativo autenticador como seu método de verificação de dois fatores

Você pode configurar um aplicativo autenticador para enviar uma notificação ao seu dispositivo móvel ou enviar um código de verificação como seu método de verificação de segurança. Não é necessário usar o aplicativo Microsoft Authenticator e você pode selecionar um aplicativo diferente durante o processo de configuração. No entanto, este artigo usa o aplicativo Microsoft Authenticator.

>[!Important]
>Para poder adicionar sua conta, você deve baixar e instalar o aplicativo Microsoft Authenticator. Se ainda não tiver feito isso, siga as etapas no artigo [baixar e instalar o aplicativo](user-help-auth-app-download-install.md) .

>[!Note]
> Se a opção aplicativo móvel estiver esmaecida, é possível que sua organização não permita que você use um aplicativo de autenticação para verificação. Nesse caso, você precisará selecionar outro método ou entrar em contato com o administrador para obter mais ajuda.

## <a name="set-up-the-microsoft-authenticator-app-to-send-notifications"></a>Configurar o aplicativo Microsoft Authenticator para enviar notificações

1. Na página **verificação de segurança adicional** , selecione **aplicativo móvel** na **etapa 1: Como devemos entrar em contato** com sua área.

2. Selecione **receber notificações para verificação** na área **como deseja usar o aplicativo móvel** e, em seguida, selecione **Configurar**.

    ![Página de verificação de segurança adicional, com o aplicativo móvel e a opção de notificações](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-notification.png)

    A página **Configurar aplicativo móvel** é exibida.

    ![Tela que fornece o código QR](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-barcode.png)

3. Abra o aplicativo Microsoft Authenticator, selecione **adicionar conta** no ícone **Personalizar e controlar** no canto superior direito e selecione **conta corporativa ou de estudante**.

    >[!Note]
    >Se esta for a primeira vez que você estiver configurando o aplicativo Microsoft Authenticator, você poderá receber um prompt perguntando se deseja permitir que o aplicativo acesse sua câmera (iOS) ou permitir que o aplicativo tire fotos e grave vídeo (Android). Você deve selecionar **permitir** para que o aplicativo autenticador possa acessar sua câmera para tirar uma imagem do código QR na próxima etapa. Se você não permitir a câmera, ainda poderá configurar o aplicativo autenticador, mas precisará adicionar as informações de código manualmente. Para obter informações sobre como adicionar o código manualmente, consulte [adicionar manualmente uma conta ao aplicativo](user-help-auth-app-add-account-manual.md).

4. Use a câmera do dispositivo para digitalizar o código QR na tela **Configurar aplicativo móvel** em seu computador e escolha **Avançar**.

5. Volte para o computador e a página de **verificação de segurança adicional** , certifique-se de receber a mensagem dizendo que sua configuração foi bem-sucedida e, em seguida, selecione **Avançar**.

    ![Página de verificação de segurança adicional, com mensagem de êxito](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-notification-confirm.png)

    O aplicativo autenticador enviará uma notificação ao seu dispositivo móvel como um teste.

6. Em seu dispositivo móvel, selecione **aprovar**.

7. Em seu computador, adicione o número de telefone do seu dispositivo **móvel à etapa 3: Caso você perca o acesso à área do aplicativo** móvel e selecione **Avançar**.

    É recomendável adicionar o número de telefone do seu dispositivo móvel para atuar como um backup se você não conseguir acessar ou usar o aplicativo móvel por qualquer motivo.

8. **Na etapa 4: Continue usando sua área de** aplicativos existentes, copie a senha de aplicativo fornecida e cole-a em um local seguro.

    ![Área de senhas de aplicativo da página de verificação de segurança adicional](media/multi-factor-authentication-verification-methods/multi-factor-authentication-app-passwords.png)

    >[!Note]
    >Para obter informações sobre como usar a senha de aplicativo com seus aplicativos mais antigos, consulte [gerenciar senhas de aplicativo](multi-factor-authentication-end-user-app-passwords.md). Você só precisa usar senhas de aplicativo se estiver continuando a usar aplicativos mais antigos que não dão suporte à verificação de dois fatores.

9. Selecione **Done** (Concluído).

## <a name="set-up-the-microsoft-authenticator-app-to-use-verification-codes"></a>Configurar o aplicativo Microsoft Authenticator para usar códigos de verificação

1. Na página **verificação de segurança adicional** , selecione **aplicativo móvel** na **etapa 1: Como devemos entrar em contato** com sua área.

2. Selecione **usar código de verificação** na área **como deseja usar o aplicativo móvel** e, em seguida, selecione **Configurar**.

    ![Página de verificação de segurança adicional, com o aplicativo móvel e a opção de notificações](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-verification-code.png)

    A página **Configurar aplicativo móvel** é exibida.

    ![Tela que fornece o código QR](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-barcode.png)

3. Abra o aplicativo Microsoft Authenticator, selecione **adicionar conta** no ícone **Personalizar e controlar** no canto superior direito e selecione **conta corporativa ou de estudante**.

    >[!Note]
    >Se esta for a primeira vez que você estiver configurando o aplicativo Microsoft Authenticator, você poderá receber um prompt perguntando se deseja permitir que o aplicativo acesse sua câmera (iOS) ou permitir que o aplicativo tire fotos e grave vídeo (Android). Você deve selecionar **permitir** para que o aplicativo autenticador possa acessar sua câmera para tirar uma imagem do código QR na próxima etapa. Se você não permitir a câmera, ainda poderá configurar o aplicativo autenticador, mas precisará adicionar as informações de código manualmente. Para obter informações sobre como adicionar o código manualmente, consulte [adicionar manualmente uma conta ao aplicativo](user-help-auth-app-add-account-manual.md).

4. Use a câmera do dispositivo para digitalizar o código QR na tela **Configurar aplicativo móvel** em seu computador e escolha **Avançar**.

5. Volte para o computador e a página de **verificação de segurança adicional** , certifique-se de receber a mensagem dizendo que sua configuração foi bem-sucedida e, em seguida, selecione **Avançar**.

    ![Página de verificação de segurança adicional, com mensagem de êxito](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-verification-confirm.png)

    O aplicativo autenticador solicitará um código de verificação como um teste.

6. No Microsoft Authenticator aplicativo, role para baixo até sua conta corporativa ou de estudante, copie e cole o código de seis dígitos do aplicativo na **etapa 2: Insira o código de verificação na caixa aplicativo** móvel no seu computador e, em seguida, selecione **verificar**.

    ![Página de verificação de segurança adicional, com teste de código de verificação](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-verification-test.png)

7. Em seu computador, adicione o número de telefone do seu dispositivo **móvel à etapa 3: Caso você perca o acesso à área do aplicativo** móvel e selecione **Avançar**.

    É recomendável adicionar o número de telefone do seu dispositivo móvel para atuar como um backup se você não conseguir acessar ou usar o aplicativo móvel por qualquer motivo.

8. **Na etapa 4: Continue usando sua área de** aplicativos existentes, copie a senha de aplicativo fornecida e cole-a em um local seguro.

    ![Área de senhas de aplicativo da página de verificação de segurança adicional](media/multi-factor-authentication-verification-methods/multi-factor-authentication-app-passwords.png)

    >[!Note]
    >Para obter informações sobre como usar a senha de aplicativo com seus aplicativos mais antigos, consulte [gerenciar senhas de aplicativo](multi-factor-authentication-end-user-app-passwords.md). Você só precisa usar senhas de aplicativo se estiver continuando a usar aplicativos mais antigos que não dão suporte à verificação de dois fatores.

9. Selecione **Done** (Concluído).

## <a name="next-steps"></a>Passos Seguintes

Depois de configurar o método de verificação de dois fatores, você pode adicionar outros métodos, gerenciar suas configurações e senhas de aplicativo, entrar ou obter ajuda com alguns problemas comuns relacionados à verificação de dois fatores.

- [Gerenciar as configurações do método de verificação de dois fatores](multi-factor-authentication-end-user-manage-settings.md)

- [Gerir as palavras-passe das aplicações](multi-factor-authentication-end-user-app-passwords.md)

- [Entrar usando a verificação de dois fatores](multi-factor-authentication-end-user-signin.md)

- [Obter ajuda com a verificação de dois fatores](multi-factor-authentication-end-user-troubleshoot.md)
