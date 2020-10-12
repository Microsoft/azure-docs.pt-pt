---
title: Iniciar sação utilizando a informação de autenticação de identidade - Azure AD
description: Saiba como iniciar sing-in utilizando os vários métodos de verificação de identidade em informações de segurança.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: librown
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 07/18/2019
ms.author: curtand
ms.custom: user-help
ms.openlocfilehash: 42ced6632ebfa56af8fe13a02f531b9835e13df3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88799388"
---
# <a name="sign-in-using-two-step-verification-or-security-info"></a>Inscreva-se usando informações de verificação ou segurança em duas etapas

Depois de configurar uma verificação em duas etapas ou informações de segurança, poderá iniciar scontabilidade na sua conta utilizando o seu método de verificação especificado.

> [!Note]
> Se ainda estiver a utilizar a experiência de verificação em duas etapas, terá de configurar os seus métodos de verificação seguindo as instruções da minha conta Configurar para o artigo de verificação em [duas etapas.](multi-factor-authentication-end-user-first-time.md)
>
> Se o seu administrador tiver ligado a experiência de informação de segurança, terá de definir os seus métodos de verificação utilizando estes artigos passo a passo:<ul><li>[Configurar informações de segurança para usar uma app de autenticação](security-info-setup-auth-app.md)</li><li>[Configurar as informações de segurança para utilizar mensagens de texto](security-info-setup-text-msg.md)</li><li>[Configurar informações de segurança para usar uma chamada telefónica](security-info-setup-phone-number.md)</li><li>[Configurar informações de segurança para usar uma chave de segurança](security-info-setup-security-key.md)</li></ul>

## <a name="sign-in-using-an-authenticator-app-notification-on-your-mobile-device"></a>Inscreva-se usando uma notificação de aplicação autenticadora no seu dispositivo móvel

1. Inscreva-se na sua conta com o seu nome de utilizador e senha.

2. Selecione **Aprovar** a notificação de aprovação enviada para o seu dispositivo móvel.

## <a name="sign-in-using-an-authenticator-app-code-on-your-mobile-device"></a>Inscreva-se usando um código de aplicação autenticador no seu dispositivo móvel

1. Inscreva-se na sua conta com o seu nome de utilizador e senha.

2. Abra a sua aplicação autenticadora e digite o código gerado aleatoriamente para a sua conta na caixa **de código Enter.**

## <a name="sign-in-using-your-phone-number"></a>Inscreva-se usando o seu número de telefone

1. Inscreva-se na sua conta com o seu nome de utilizador e senha.

2. Atende o telemóvel e segue as instruções.

## <a name="sign-in-using-a-text-message"></a>Inscreva-se usando uma mensagem de texto

1. Inscreva-se na sua conta com o seu nome de utilizador e senha.

2. Abra a mensagem de texto e digite o código da sua mensagem de texto na caixa **de código 'Introduzir'.**

## <a name="sign-in-using-a-security-key-at-the-lock-screen"></a>Inscreva-se usando uma chave de segurança no ecrã de bloqueio

1. Depois de ter registado a sua chave de segurança, selecione a imagem da chave de segurança a partir do ecrã de bloqueio do Windows 10.

2. Insira a chave de segurança na porta USB do seu dispositivo e inscreva-se no Windows utilizando o PIN da sua chave de segurança.

    ![Insusou-se na chave de segurança no ecrã de bloqueio do Windows 10](./media/security-info/security-info-windows-10-lock-screen-security-key.png)

## <a name="sign-in-using-a-security-key-and-the-microsoft-edge-browser"></a>Inscreva-se usando uma chave de segurança e o navegador Microsoft Edge

1. Depois de registar a sua chave de segurança, abra o navegador Microsoft Edge.

2. Quando for solicitado para iniciar sôm, insira a sua chave de segurança na porta USB do seu dispositivo e inscreva-se no Windows utilizando o pin da sua chave de segurança.

    ![Entrada de chave de segurança utilizando o navegador Microsoft Edge](./media/security-info/security-info-edge-security-key.png)

    >[!NOTE]
    >Para obter informações sobre a utilização da aplicação Microsoft Authenticator consulte o artigo, [Inscreva-se nas suas contas utilizando a aplicação Microsoft Authenticator](user-help-auth-app-sign-in.md).

## <a name="sign-in-using-another-verification-method"></a>Inscreva-se usando outro método de verificação

Se, por alguma razão, não conseguir utilizar o seu método de insicio primário, pode utilizar outro método de verificação previamente configurado.

1. Inscreva-se normalmente na sua conta e, em seguida, escolha o Sinal de **outra forma** na página de **verificação em duas etapas.**

    ![Alterar sinal no método de verificação](media/security-info/two-factor-auth-signin-another-way.png)

    >[!Note]
    >Se não vir o **Sinal de outra forma,** significa que não estabeleceu quaisquer outros métodos de verificação e que terá de contactar o seu administrador para ajudar a iniciar sessão na sua conta. Depois de o seu administrador o ajudar a iniciar sôm, certifique-se de adicionar métodos de verificação adicionais. Para obter mais informações sobre a adição de métodos de verificação, consulte as [definições de Gerir as suas definições para artigo de verificação em duas etapas.](multi-factor-authentication-end-user-manage-settings.md)
    >
    >Se vir o **Sinal de outra forma,** mas ainda não vir outros métodos de verificação, terá de contactar o seu administrador para ajudar a iniciar sessão na sua conta.

2. Escolha o seu método de verificação alternativo e continue com o processo de verificação em duas etapas.

3. Depois de voltar à sua conta, pode atualizar os seus métodos de verificação (se necessário). Para obter mais informações sobre adicionar ou alterar os seus métodos, consulte as [definições de Gerir as suas definições para artigo de verificação em duas etapas.](multi-factor-authentication-end-user-manage-settings.md)

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre informações de segurança no artigo de visão geral da [informação de segurança (pré-visualização).](./security-info-setup-signin.md)

- Saiba mais sobre a verificação em duas etapas no artigo [de verificação de duas etapas.](./multi-factor-authentication-end-user-first-time.md)

- Reinicie a sua palavra-passe se a tiver perdido ou esquecido, a partir do [portal de reset password](https://passwordreset.microsoftonline.com/)

- Obtenha dicas de resolução de problemas e ajude a iniciar sessão no [Artigo da Conta Da Microsoft.](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)