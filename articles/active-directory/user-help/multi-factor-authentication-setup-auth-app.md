---
title: Configurar um aplicativo autenticador como seu método de verificação de dois fatores-Azure Active Directory | Microsoft Docs
description: Saiba como configurar o aplicativo Microsoft Authenticator como seu método de verificação de dois fatores.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: overview
ms.date: 08/12/2019
ms.author: curtand
ms.openlocfilehash: 6ab1f7c97173021cc112a5f117469abd74ac954d
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77062579"
---
# <a name="set-up-an-authenticator-app-as-your-two-factor-verification-method"></a>Configurar um aplicativo autenticador como seu método de verificação de dois fatores

Você pode configurar um aplicativo autenticador para enviar uma notificação ao seu dispositivo móvel ou enviar um código de verificação como seu método de verificação de segurança. Não é necessário usar o aplicativo Microsoft Authenticator e você pode selecionar um aplicativo diferente durante o processo de configuração. No entanto, este artigo usa o aplicativo Microsoft Authenticator.

>[!Important]
>Para poder adicionar sua conta, você deve baixar e instalar o aplicativo Microsoft Authenticator. Se ainda não o fez, siga os passos no Download e instale o artigo [da aplicação.](user-help-auth-app-download-install.md)

>[!Note]
> Se a opção aplicativo móvel estiver esmaecida, é possível que sua organização não permita que você use um aplicativo de autenticação para verificação. Nesse caso, você precisará selecionar outro método ou entrar em contato com o administrador para obter mais ajuda.

## <a name="set-up-the-microsoft-authenticator-app-to-send-notifications"></a>Configurar o aplicativo Microsoft Authenticator para enviar notificações

1. Na página adicional de verificação de **segurança,** selecione **aplicação Móvel** a partir do Passo **1: Como devemos contactá-lo** área.

2. Selecione **Receber notificações para verificação** a partir da área de **aplicação móvel** e, em seguida, selecione **Configurar**.

    ![Página de verificação de segurança adicional, com o aplicativo móvel e a opção de notificações](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-notification.png)

    Aparece a página da **aplicação móvel Configure.**

    ![Tela que fornece o código QR](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-barcode.png)

3. Abra a aplicação Microsoft Authenticator, selecione **Adicionar conta** do ícone Personalizar **e controlar** no canto superior direito e, em seguida, selecione Trabalho ou **conta escolar**.

    >[!Note]
    >Se esta for a primeira vez que você estiver configurando o aplicativo Microsoft Authenticator, você poderá receber um prompt perguntando se deseja permitir que o aplicativo acesse sua câmera (iOS) ou permitir que o aplicativo tire fotos e grave vídeo (Android). Deve selecionar **Permitir** para que a aplicação autenticadora possa aceder à sua câmara para tirar uma fotografia do código QR no próximo passo. Se você não permitir a câmera, ainda poderá configurar o aplicativo autenticador, mas precisará adicionar as informações de código manualmente. Para obter informações sobre como adicionar o código manualmente, consulte [Manualmente adicionar uma conta à aplicação](user-help-auth-app-add-account-manual.md).

4. Utilize a câmara do seu dispositivo para digitalizar o código QR a partir do ecrã de **aplicações móveis Configure** no seu computador e, em seguida, escolha **Next**.

5. Volte ao seu computador e à página adicional de verificação de **segurança,** certifique-se de que recebe a mensagem que diz que a sua configuração foi bem sucedida e, em seguida, selecione **Next**.

    ![Página de verificação de segurança adicional, com mensagem de êxito](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-notification-confirm.png)

    O aplicativo autenticador enviará uma notificação ao seu dispositivo móvel como um teste.

6. No seu dispositivo móvel, selecione **'Aprovar'.**

7. No seu computador, adicione o número de telefone do seu dispositivo móvel ao **Passo 3: Caso perca o acesso à** área da aplicação móvel e, em seguida, selecione **Next**.

    É recomendável adicionar o número de telefone do seu dispositivo móvel para atuar como um backup se você não conseguir acessar ou usar o aplicativo móvel por qualquer motivo.

8. A partir do Passo 4: Continue a utilizar a área de **aplicações existente,** copie a senha da aplicação fornecida e cole-a em algum lugar seguro.

    ![Área de senhas de aplicativo da página de verificação de segurança adicional](media/multi-factor-authentication-verification-methods/multi-factor-authentication-app-passwords.png)

    >[!Note]
    >Para obter informações sobre como usar a palavra-passe da aplicação com as suas aplicações mais antigas, consulte [Gerir palavras-passe](multi-factor-authentication-end-user-app-passwords.md)da aplicação . Você só precisa usar senhas de aplicativo se estiver continuando a usar aplicativos mais antigos que não dão suporte à verificação de dois fatores.

9. Selecione **Done** (Concluído).

## <a name="set-up-the-microsoft-authenticator-app-to-use-verification-codes"></a>Configurar o aplicativo Microsoft Authenticator para usar códigos de verificação

1. Na página adicional de verificação de **segurança,** selecione **aplicação Móvel** a partir do Passo **1: Como devemos contactá-lo** área.

2. Selecione Utilize o código de **verificação** da "Como pretende utilizar a área **da aplicação móvel"** e, em seguida, selecione **Configurar**.

    ![Página de verificação de segurança adicional, com o aplicativo móvel e a opção de notificações](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-verification-code.png)

    Aparece a página da **aplicação móvel Configure.**

    ![Tela que fornece o código QR](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-barcode.png)

3. Abra a aplicação Microsoft Authenticator, selecione **Adicionar conta** do ícone Personalizar **e controlar** no canto superior direito e, em seguida, selecione Trabalho ou **conta escolar**.

    >[!Note]
    >Se esta for a primeira vez que você estiver configurando o aplicativo Microsoft Authenticator, você poderá receber um prompt perguntando se deseja permitir que o aplicativo acesse sua câmera (iOS) ou permitir que o aplicativo tire fotos e grave vídeo (Android). Deve selecionar **Permitir** para que a aplicação autenticadora possa aceder à sua câmara para tirar uma fotografia do código QR no próximo passo. Se você não permitir a câmera, ainda poderá configurar o aplicativo autenticador, mas precisará adicionar as informações de código manualmente. Para obter informações sobre como adicionar o código manualmente, consulte [Manualmente adicionar uma conta à aplicação](user-help-auth-app-add-account-manual.md).

4. Utilize a câmara do seu dispositivo para digitalizar o código QR a partir do ecrã de **aplicações móveis Configure** no seu computador e, em seguida, escolha **Next**.

5. Volte ao seu computador e à página adicional de verificação de **segurança,** certifique-se de que recebe a mensagem que diz que a sua configuração foi bem sucedida e, em seguida, selecione **Next**.

    ![Página de verificação de segurança adicional, com mensagem de êxito](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-verification-confirm.png)

    O aplicativo autenticador solicitará um código de verificação como um teste.

6. A partir da aplicação Microsoft Authenticator, desloque-se até à sua conta de trabalho ou escola, copie e cole o código de 6 dígitos da aplicação para o **Passo 2: Introduza o código de verificação a partir da** caixa de aplicações móvel no seu computador e, em seguida, selecione **Verificar**.

    ![Página de verificação de segurança adicional, com teste de código de verificação](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-verification-test.png)

7. No seu computador, adicione o número de telefone do seu dispositivo móvel ao **Passo 3: Caso perca o acesso à** área da aplicação móvel e, em seguida, selecione **Next**.

    É recomendável adicionar o número de telefone do seu dispositivo móvel para atuar como um backup se você não conseguir acessar ou usar o aplicativo móvel por qualquer motivo.

8. A partir do Passo 4: Continue a utilizar a área de **aplicações existente,** copie a senha da aplicação fornecida e cole-a em algum lugar seguro.

    ![Área de senhas de aplicativo da página de verificação de segurança adicional](media/multi-factor-authentication-verification-methods/multi-factor-authentication-app-passwords.png)

    >[!Note]
    >Para obter informações sobre como usar a palavra-passe da aplicação com as suas aplicações mais antigas, consulte [Gerir palavras-passe](multi-factor-authentication-end-user-app-passwords.md)da aplicação . Você só precisa usar senhas de aplicativo se estiver continuando a usar aplicativos mais antigos que não dão suporte à verificação de dois fatores.

9. Selecione **Done** (Concluído).

## <a name="next-steps"></a>Passos seguintes

Depois de configurar o método de verificação de dois fatores, você pode adicionar outros métodos, gerenciar suas configurações e senhas de aplicativo, entrar ou obter ajuda com alguns problemas comuns relacionados à verificação de dois fatores.

- [Gerencie as definições do método de verificação de dois fatores](multi-factor-authentication-end-user-manage-settings.md)

- [Gerir as palavras-passe das aplicações](multi-factor-authentication-end-user-app-passwords.md)

- [Iniciar sessão utilizando verificação de dois fatores](multi-factor-authentication-end-user-signin.md)

- [Obtenha ajuda com verificação de dois fatores](multi-factor-authentication-end-user-troubleshoot.md)
