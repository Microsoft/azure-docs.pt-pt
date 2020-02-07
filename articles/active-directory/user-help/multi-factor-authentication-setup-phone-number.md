---
title: Configurar um dispositivo móvel como seu método de verificação de dois fatores-Azure Active Directory | Microsoft Docs
description: Saiba como configurar um dispositivo móvel como seu método de verificação de dois fatores.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: overview
ms.date: 08/12/2019
ms.author: curtand
ms.openlocfilehash: b0bfaa87c77ba9fff9f6605c1989e48ffbc3fb35
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77062511"
---
# <a name="set-up-a-mobile-device-as-your-two-factor-verification-method"></a>Configurar um dispositivo móvel como seu método de verificação de dois fatores

Você pode configurar seu dispositivo móvel para atuar como seu método de verificação de dois fatores. Seu celular pode receber uma mensagem de texto com um código de verificação ou uma chamada telefônica.

>[!Note]
> Se a opção telefone de autenticação estiver esmaecida, é possível que sua organização não permita que você use um número de telefone ou mensagem de texto para verificação. Nesse caso, você precisará selecionar outro método ou entrar em contato com o administrador para obter mais ajuda.

## <a name="set-up-your-mobile-device-to-use-a-text-message-as-your-verification-method"></a>Configurar seu dispositivo móvel para usar uma mensagem de texto como seu método de verificação

1. Na página adicional de verificação de **segurança,** selecione telefone de autenticação a partir do Passo **1: Como devemos contactar a** sua área, selecionar o seu país ou região a partir da lista de drop-down e, em seguida, **digitar** o seu número de telefone do dispositivo móvel.

2. Selecione **Enviar-me um código por mensagem** de texto a partir da área **método** e, em seguida, selecione **Next**.

    ![Página de verificação de segurança adicional, com telefone de autenticação e mensagem de texto](media/multi-factor-authentication-verification-methods/multi-factor-authentication-text-message.png)

3. Digite o código de verificação a partir da mensagem de texto enviada da Microsoft para o **Passo 2: Enviámos uma mensagem** de texto para a sua área de telefone e, em seguida, selecione **Verificar**.

    ![Página de verificação de segurança adicional, com telefone de autenticação e mensagem de texto](media/multi-factor-authentication-verification-methods/multi-factor-authentication-text-message-test.png)

4. A partir do Passo 3: Continue a utilizar a área de **aplicações existente,** copie a senha da aplicação fornecida e cole-a em algum lugar seguro.

    ![Área de senhas de aplicativo da página de verificação de segurança adicional](media/multi-factor-authentication-verification-methods/multi-factor-authentication-app-passwords.png)

    >[!Note]
    >Para obter informações sobre como usar a palavra-passe da aplicação com as suas aplicações mais antigas, consulte [Gerir palavras-passe](multi-factor-authentication-end-user-app-passwords.md)da aplicação . Você só precisa usar senhas de aplicativo se estiver continuando a usar aplicativos mais antigos que não dão suporte à verificação de dois fatores.

5. Selecione **Done** (Concluído).

## <a name="set-up-your-mobile-device-to-receive-a-phone-call"></a>Configurar seu dispositivo móvel para receber uma chamada telefônica

1. Na página adicional de verificação de **segurança,** selecione telefone de autenticação a partir do Passo **1: Como devemos contactar a** sua área, selecionar o seu país ou região a partir da lista de drop-down e, em seguida, **digitar** o seu número de telefone do dispositivo móvel.

2. Selecione **Ligue-me** da área **método** e, em seguida, selecione **Next**.

    ![Página de verificação de segurança adicional, com telefone de autenticação e chamada telefônica](media/multi-factor-authentication-verification-methods/multi-factor-authentication-phone-call.png)

3. Você receberá um telefonema da Microsoft, solicitando que você pressione o sinal de libra (#) em seu dispositivo móvel para verificar sua identidade.

    ![Testando o número de telefone especificado](media/multi-factor-authentication-verification-methods/multi-factor-authentication-phone-call-test.png)

4. A partir do Passo 3: Continue a utilizar a área de **aplicações existente,** copie a senha da aplicação fornecida e cole-a em algum lugar seguro.

    ![Área de senhas de aplicativo da página de verificação de segurança adicional](media/multi-factor-authentication-verification-methods/multi-factor-authentication-app-passwords.png)

    >[!Note]
    >Para obter informações sobre como usar a palavra-passe da aplicação com as suas aplicações mais antigas, consulte [Gerir palavras-passe](multi-factor-authentication-end-user-app-passwords.md)da aplicação . Você só precisa usar senhas de aplicativo se estiver continuando a usar aplicativos mais antigos que não dão suporte à verificação de dois fatores.

5. Selecione **Done** (Concluído).

## <a name="next-steps"></a>Passos seguintes

Depois de configurar o método de verificação de dois fatores, você pode adicionar outros métodos, gerenciar suas configurações e senhas de aplicativo, entrar ou obter ajuda com alguns problemas comuns relacionados à verificação de dois fatores.

- [Gerencie as definições do método de verificação de dois fatores](multi-factor-authentication-end-user-manage-settings.md)

- [Gerir as palavras-passe das aplicações](multi-factor-authentication-end-user-app-passwords.md)

- [Iniciar sessão utilizando verificação de dois fatores](multi-factor-authentication-end-user-signin.md)

- [Obtenha ajuda com verificação de dois fatores](multi-factor-authentication-end-user-troubleshoot.md)
