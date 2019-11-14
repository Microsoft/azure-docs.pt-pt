---
title: Entrar usando suas informações de autenticação de identidade-Azure AD
description: Saiba mais sobre como entrar usando os vários métodos de verificação de identidade em informações de segurança.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: librown
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: lizross
ms.custom: user-help
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1c79e82680ae81137ee4164e88e62235f934565a
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74028516"
---
# <a name="sign-in-using-two-step-verification-or-security-info"></a>Entrar usando a verificação em duas etapas ou informações de segurança

Depois de configurar a verificação em duas etapas ou as informações de segurança, você poderá entrar em sua conta usando o método de autenticação especificado.

> [!Note]
> Se você ainda estiver usando a experiência de verificação em duas etapas, precisará configurar seus métodos de autenticação seguindo as instruções no artigo [configurar minha conta para a verificação em duas etapas](multi-factor-authentication-end-user-first-time.md) .
>
> Se o administrador tiver ativado a experiência de informações de segurança, você precisará definir seus métodos de autenticação usando estes artigos passo a passo:<ul><li>[Configurar informações de segurança para usar um aplicativo de autenticação](security-info-setup-auth-app.md)</li><li>[Configurar as informações de segurança para utilizar mensagens de texto](security-info-setup-text-msg.md)</li><li>[Configurar informações de segurança para usar uma chamada telefônica](security-info-setup-phone-number.md)</li><li>[Configurar informações de segurança para usar uma chave de segurança](security-info-setup-security-key.md)</li></ul>

## <a name="sign-in-using-an-authenticator-app-notification-on-your-mobile-device"></a>Entrar usando uma notificação de aplicativo autenticador em seu dispositivo móvel

1. Entre em sua conta com seu nome de usuário e senha.

2. Selecione **aprovar** na notificação de aprovação enviada ao seu dispositivo móvel.

## <a name="sign-in-using-an-authenticator-app-code-on-your-mobile-device"></a>Entre usando um código de aplicativo autenticador em seu dispositivo móvel

1. Entre em sua conta com seu nome de usuário e senha.

2. Abra seu aplicativo autenticador e digite o código gerado aleatoriamente para sua conta na caixa de **código inserir** .

## <a name="sign-in-using-your-phone-number"></a>Entre usando seu número de telefone

1. Entre em sua conta com seu nome de usuário e senha.

2. Responda ao seu telefone e siga as instruções.

## <a name="sign-in-using-a-text-message"></a>Entrar usando uma mensagem de texto

1. Entre em sua conta com seu nome de usuário e senha.

2. Abra a mensagem de texto e digite o código da mensagem de texto na caixa de **código Enter** .

## <a name="sign-in-using-a-security-key-at-the-lock-screen"></a>Entrar usando uma chave de segurança na tela de bloqueio

1. Depois de registrar sua chave de segurança, selecione a imagem da chave de segurança na tela de bloqueio do Windows 10.

2. Insira sua chave de segurança na porta USB do dispositivo e entre no Windows usando o PIN de chave de segurança.

    ![Entrada de chave de segurança na tela de bloqueio do Windows 10](./media/security-info/security-info-windows-10-lock-screen-security-key.png)

## <a name="sign-in-using-a-security-key-and-the-microsoft-edge-browser"></a>Entre usando uma chave de segurança e o navegador Microsoft Edge

1. Depois de registrar sua chave de segurança, abra o navegador Microsoft Edge.

2. Quando solicitado a entrar, insira sua chave de segurança na porta USB do dispositivo e entre no Windows usando o PIN de chave de segurança.

    ![Entrada de chave de segurança usando o navegador Microsoft Edge](./media/security-info/security-info-edge-security-key.png)

    >[!NOTE]
    >Para obter informações sobre como entrar usando o aplicativo Microsoft Authenticator, consulte o artigo, [entrar em suas contas usando o aplicativo Microsoft Authenticator](user-help-auth-app-sign-in.md).

## <a name="sign-in-using-another-verification-method"></a>Entrar usando outro método de verificação

Se, por alguma razão, você não conseguir usar seu método de entrada primário, poderá usar outro método de verificação configurado anteriormente.

1. Entre em sua conta normalmente e, em seguida, escolha o link **entrar de outra maneira** na página de **verificação em duas etapas** .

    ![Alterar método de verificação de entrada](media/security-info/two-factor-auth-signin-another-way.png)

    >[!Note]
    >Se você não vir o link **entrar de outra maneira** , isso significa que você não configurou nenhum outro método de verificação e que precisará entrar em contato com o administrador para obter ajuda para entrar em sua conta. Depois que o administrador ajuda você a entrar, certifique-se de adicionar métodos de verificação adicionais. Para obter mais informações sobre como adicionar métodos de verificação, consulte o artigo [gerenciar suas configurações para a verificação em duas etapas](multi-factor-authentication-end-user-manage-settings.md) .
    >
    >Se você vir o link **entrar de outra maneira** , mas ainda não vir nenhum outro método de verificação, será necessário entrar em contato com o administrador para obter ajuda para entrar na sua conta.

2. Escolha o método de verificação alternativo e continue com o processo de verificação em duas etapas.

3. Depois de voltar à sua conta, você pode atualizar seus métodos de verificação (se necessário). Para obter mais informações sobre como adicionar ou alterar seus métodos, consulte o artigo [gerenciar suas configurações para a verificação em duas etapas](multi-factor-authentication-end-user-manage-settings.md) .

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre informações de segurança no artigo [visão geral de informações de segurança (versão prévia)](user-help-security-info-overview.md) .

- Saiba mais sobre a verificação em duas etapas no artigo [visão geral da verificação em duas etapas](user-help-two-step-verification-overview.md) .

- Redefinir sua senha se você a tiver perdido ou esquecido, no [portal de redefinição de senha](https://passwordreset.microsoftonline.com/)

- Obtenha dicas de solução de problemas e ajuda para problemas de conexão no artigo [não é possível entrar no seu conta Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) .
