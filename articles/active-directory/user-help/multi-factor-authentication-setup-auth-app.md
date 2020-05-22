---
title: Configurar uma aplicação de autenticador como o seu método de verificação de dois fatores - Diretório Ativo Azure / Microsoft Docs
description: Aprenda a configurar a aplicação Microsoft Authenticator como o seu método de verificação de dois fatores.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 08/12/2019
ms.author: curtand
ms.openlocfilehash: 953f74a18ae7874c8772cdc8179e5e5bdd1a86d2
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83738647"
---
# <a name="set-up-an-authenticator-app-as-your-two-factor-verification-method"></a>Configurar uma aplicação de autenticador como o seu método de verificação de dois fatores

Pode configurar uma aplicação autenticadora para enviar uma notificação para o seu dispositivo móvel ou para lhe enviar um código de verificação como método de verificação de segurança. Não é obrigado a utilizar a aplicação Microsoft Authenticator e pode selecionar uma aplicação diferente durante o processo de configuração. No entanto, este artigo utiliza a aplicação Microsoft Authenticator.

>[!Important]
>Antes de poder adicionar a sua conta, tem de descarregar e instalar a aplicação Microsoft Authenticator. Se ainda não o fez, siga os passos no Download e instale o artigo [da aplicação.](user-help-auth-app-download-install.md)

>[!Note]
> Se a opção de aplicação Mobile estiver acinzentada, é possível que a sua organização não lhe permita utilizar uma aplicação de autenticação para verificação. Neste caso, terá de selecionar outro método ou contactar o seu administrador para obter mais ajuda.

## <a name="set-up-the-microsoft-authenticator-app-to-send-notifications"></a>Configurar a aplicação Microsoft Authenticator para enviar notificações

1. Na página adicional de verificação de [segurança,](https://account.activedirectory.windowsazure.com/proofup.aspx?proofup=1)selecione **aplicação Móvel** a partir do Passo **1: Como devemos contactá-lo** área.

2. Selecione **Receber notificações para verificação** a partir da área de **aplicação móvel** e, em seguida, selecione **Configurar**.

    ![Página adicional de verificação de segurança, com aplicação móvel e opção de notificações](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-notification.png)

    Aparece a página da **aplicação móvel Configure.**

    ![Tela que fornece o código QR](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-barcode.png)

3. Abra a aplicação Microsoft Authenticator, selecione **Adicionar conta** do ícone Personalizar **e controlar** no canto superior direito e, em seguida, selecione Trabalho ou **conta escolar**.

    >[!Note]
    >Se esta for a primeira vez que está a configurar a aplicação Microsoft Authenticator, poderá receber uma pergunta rápida sobre se permite que a aplicação aceda à sua câmara (iOS) ou para permitir que a aplicação tire fotografias e grave vídeos (Android). Deve selecionar **Permitir** para que a aplicação autenticadora possa aceder à sua câmara para tirar uma fotografia do código QR no próximo passo. Se não permitir a câmara, ainda pode configurar a aplicação autenticadora, mas terá de adicionar manualmente a informação do código. Para obter informações sobre como adicionar o código manualmente, consulte [Manualmente adicionar uma conta à aplicação](user-help-auth-app-add-account-manual.md).

4. Utilize a câmara do seu dispositivo para digitalizar o código QR a partir do ecrã de **aplicações móveis Configure** no seu computador e, em seguida, escolha **Next**.

5. Volte ao seu computador e à página adicional de verificação de **segurança,** certifique-se de que recebe a mensagem que diz que a sua configuração foi bem sucedida e, em seguida, selecione **Next**.

    ![Página adicional de verificação de segurança, com mensagem de sucesso](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-notification-confirm.png)

    A aplicação autenticadora enviará uma notificação para o seu dispositivo móvel como teste.

6. No seu dispositivo móvel, selecione **'Aprovar'.**

7. No seu computador, adicione o número de telefone do seu dispositivo móvel ao **Passo 3: Caso perca o acesso à** área da aplicação móvel e, em seguida, selecione **Next**.

    Sugerimos fortemente que adicione o número de telefone do seu dispositivo móvel para funcionar como uma cópia de segurança se não conseguir aceder ou utilizar a aplicação móvel por qualquer motivo.

8. A partir do Passo 4: Continue a utilizar a área de **aplicações existente,** copie a senha da aplicação fornecida e cole-a em algum lugar seguro.

    ![Área de senhas de aplicação da página adicional de verificação de segurança](media/multi-factor-authentication-verification-methods/multi-factor-authentication-app-passwords.png)

    >[!Note]
    >Para obter informações sobre como usar a palavra-passe da aplicação com as suas aplicações mais antigas, consulte [Gerir palavras-passe](multi-factor-authentication-end-user-app-passwords.md)da aplicação . Só precisa de usar palavras-passe de aplicações se continuar a utilizar aplicações mais antigas que não suportam a verificação de dois fatores.

9. Selecione **Done** (Concluído).

## <a name="set-up-the-microsoft-authenticator-app-to-use-verification-codes"></a>Configurar a aplicação Microsoft Authenticator para usar códigos de verificação

1. Na página adicional de verificação de **segurança,** selecione **aplicação Móvel** a partir do Passo **1: Como devemos contactá-lo** área.

2. Selecione Utilize o código de **verificação** da "Como pretende utilizar a área **da aplicação móvel"** e, em seguida, selecione **Configurar**.

    ![Página adicional de verificação de segurança, com aplicação móvel e opção de notificações](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-verification-code.png)

    Aparece a página da **aplicação móvel Configure.**

    ![Tela que fornece o código QR](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-barcode.png)

3. Abra a aplicação Microsoft Authenticator, selecione **Adicionar conta** do ícone Personalizar **e controlar** no canto superior direito e, em seguida, selecione Trabalho ou **conta escolar**.

    >[!Note]
    >Se esta for a primeira vez que está a configurar a aplicação Microsoft Authenticator, poderá receber uma pergunta rápida sobre se permite que a aplicação aceda à sua câmara (iOS) ou para permitir que a aplicação tire fotografias e grave vídeos (Android). Deve selecionar **Permitir** para que a aplicação autenticadora possa aceder à sua câmara para tirar uma fotografia do código QR no próximo passo. Se não permitir a câmara, ainda pode configurar a aplicação autenticadora, mas terá de adicionar manualmente a informação do código. Para obter informações sobre como adicionar o código manualmente, consulte [Manualmente adicionar uma conta à aplicação](user-help-auth-app-add-account-manual.md).

4. Utilize a câmara do seu dispositivo para digitalizar o código QR a partir do ecrã de **aplicações móveis Configure** no seu computador e, em seguida, escolha **Next**.

5. Volte ao seu computador e à página adicional de verificação de **segurança,** certifique-se de que recebe a mensagem que diz que a sua configuração foi bem sucedida e, em seguida, selecione **Next**.

    ![Página adicional de verificação de segurança, com mensagem de sucesso](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-verification-confirm.png)

    A aplicação autenticadora pedirá um código de verificação como teste.

6. A partir da aplicação Microsoft Authenticator, desloque-se até à sua conta de trabalho ou escola, copie e cole o código de 6 dígitos da aplicação para o **Passo 2: Introduza o código de verificação a partir da** caixa de aplicações móvel no seu computador e, em seguida, selecione **Verificar**.

    ![Página adicional de verificação de segurança, com teste de código de verificação](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-verification-test.png)

7. No seu computador, adicione o número de telefone do seu dispositivo móvel ao **Passo 3: Caso perca o acesso à** área da aplicação móvel e, em seguida, selecione **Next**.

    Sugerimos fortemente que adicione o número de telefone do seu dispositivo móvel para funcionar como uma cópia de segurança se não conseguir aceder ou utilizar a aplicação móvel por qualquer motivo.

8. A partir do Passo 4: Continue a utilizar a área de **aplicações existente,** copie a senha da aplicação fornecida e cole-a em algum lugar seguro.

    ![Área de senhas de aplicação da página adicional de verificação de segurança](media/multi-factor-authentication-verification-methods/multi-factor-authentication-app-passwords.png)

    >[!Note]
    >Para obter informações sobre como usar a palavra-passe da aplicação com as suas aplicações mais antigas, consulte [Gerir palavras-passe](multi-factor-authentication-end-user-app-passwords.md)da aplicação . Só precisa de usar palavras-passe de aplicações se continuar a utilizar aplicações mais antigas que não suportam a verificação de dois fatores.

9. Selecione **Done** (Concluído).

## <a name="next-steps"></a>Passos seguintes

Depois de configurar o seu método de verificação de dois fatores, pode adicionar métodos adicionais, gerir as suas definições e palavras-passe de aplicação, iniciar sessão ou obter ajuda com alguns problemas comuns relacionados com a verificação de dois fatores.

- [Gerencie as definições do método de verificação de dois fatores](multi-factor-authentication-end-user-manage-settings.md)

- [Gerir as palavras-passe de aplicação](multi-factor-authentication-end-user-app-passwords.md)

- [Iniciar sessão utilizando verificação de dois fatores](multi-factor-authentication-end-user-signin.md)

- [Obtenha ajuda com verificação de dois fatores](multi-factor-authentication-end-user-troubleshoot.md)
