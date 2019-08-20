---
title: Configurar um dispositivo móvel como seu método de verificação de dois fatores-Azure Active Directory | Microsoft Docs
description: Saiba como configurar um dispositivo móvel como seu método de verificação de dois fatores.
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
ms.openlocfilehash: 8f03e92c1bfd86d15bf582487e3e92a3713b63e2
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69619356"
---
# <a name="set-up-a-mobile-device-as-your-two-factor-verification-method"></a>Configurar um dispositivo móvel como seu método de verificação de dois fatores

Você pode configurar seu dispositivo móvel para atuar como seu método de verificação de dois fatores. Seu celular pode receber uma mensagem de texto com um código de verificação ou uma chamada telefônica.

>[!Note]
> Se a opção telefone de autenticação estiver esmaecida, é possível que sua organização não permita que você use um número de telefone ou mensagem de texto para verificação. Nesse caso, você precisará selecionar outro método ou entrar em contato com o administrador para obter mais ajuda.

## <a name="set-up-your-mobile-device-to-use-a-text-message-as-your-verification-method"></a>Configurar seu dispositivo móvel para usar uma mensagem de texto como seu método de verificação

1. Na página **verificação de segurança adicional** , selecione **telefone de autenticação** na **etapa 1: Como devemos entrar em contato** com a área, selecione seu país ou região na lista suspensa e digite o número de telefone do dispositivo móvel.

2. Selecione **Enviar para mim um código por mensagem de texto** da área **método** e, em seguida, selecione **Avançar**.

    ![Página de verificação de segurança adicional, com telefone de autenticação e mensagem de texto](media/multi-factor-authentication-verification-methods/multi-factor-authentication-text-message.png)

3. Digite o código de verificação da mensagem de texto enviada da Microsoft para **a etapa 2: Enviamos uma mensagem de texto para a área** de telefone e, em seguida, selecionamos **verificar**.

    ![Página de verificação de segurança adicional, com telefone de autenticação e mensagem de texto](media/multi-factor-authentication-verification-methods/multi-factor-authentication-text-message-test.png)

4. **Na etapa 3: Continue usando sua área de** aplicativos existentes, copie a senha de aplicativo fornecida e cole-a em um local seguro.

    ![Área de senhas de aplicativo da página de verificação de segurança adicional](media/multi-factor-authentication-verification-methods/multi-factor-authentication-app-passwords.png)

    >[!Note]
    >Para obter informações sobre como usar a senha de aplicativo com seus aplicativos mais antigos, consulte [gerenciar senhas de aplicativo](multi-factor-authentication-end-user-app-passwords.md). Você só precisa usar senhas de aplicativo se estiver continuando a usar aplicativos mais antigos que não dão suporte à verificação de dois fatores.

5. Selecione **Done** (Concluído).

## <a name="set-up-your-mobile-device-to-receive-a-phone-call"></a>Configurar seu dispositivo móvel para receber uma chamada telefônica

1. Na página **verificação de segurança adicional** , selecione **telefone de autenticação** na **etapa 1: Como devemos entrar em contato** com a área, selecione seu país ou região na lista suspensa e digite o número de telefone do dispositivo móvel.

2. Selecione **ligar para mim** na área **método** e, em seguida, selecione **Avançar**.

    ![Página de verificação de segurança adicional, com telefone de autenticação e chamada telefônica](media/multi-factor-authentication-verification-methods/multi-factor-authentication-phone-call.png)

3. Você receberá um telefonema da Microsoft, solicitando que você pressione o sinal de libra (#) em seu dispositivo móvel para verificar sua identidade.

    ![Testando o número de telefone especificado](media/multi-factor-authentication-verification-methods/multi-factor-authentication-phone-call-test.png)

4. **Na etapa 3: Continue usando sua área de** aplicativos existentes, copie a senha de aplicativo fornecida e cole-a em um local seguro.

    ![Área de senhas de aplicativo da página de verificação de segurança adicional](media/multi-factor-authentication-verification-methods/multi-factor-authentication-app-passwords.png)

    >[!Note]
    >Para obter informações sobre como usar a senha de aplicativo com seus aplicativos mais antigos, consulte [gerenciar senhas de aplicativo](multi-factor-authentication-end-user-app-passwords.md). Você só precisa usar senhas de aplicativo se estiver continuando a usar aplicativos mais antigos que não dão suporte à verificação de dois fatores.

5. Selecione **Done** (Concluído).

## <a name="next-steps"></a>Passos seguintes

Depois de configurar o método de verificação de dois fatores, você pode adicionar outros métodos, gerenciar suas configurações e senhas de aplicativo, entrar ou obter ajuda com alguns problemas comuns relacionados à verificação de dois fatores.

- [Gerenciar as configurações do método de verificação de dois fatores](multi-factor-authentication-end-user-manage-settings.md)

- [Gerir as palavras-passe das aplicações](multi-factor-authentication-end-user-app-passwords.md)

- [Entrar usando a verificação de dois fatores](multi-factor-authentication-end-user-signin.md)

- [Obter ajuda com a verificação de dois fatores](multi-factor-authentication-end-user-troubleshoot.md)
