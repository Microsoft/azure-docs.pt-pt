---
title: Crie um dispositivo móvel como o seu método de verificação de dois fatores - Azure Ative Directory | Microsoft Docs
description: Saiba como configurar um dispositivo móvel como o seu método de verificação de dois fatores.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 08/12/2019
ms.author: curtand
ms.openlocfilehash: 40af9ac692a226b12f984db447db9635cba655e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91530260"
---
# <a name="set-up-a-mobile-device-as-your-two-factor-verification-method"></a>Crie um dispositivo móvel como o seu método de verificação de dois fatores

Pode configurar o seu dispositivo móvel para funcionar como o seu método de verificação de dois fatores. O seu telemóvel pode receber uma mensagem de texto com um código de verificação ou uma chamada telefónica.

>[!Note]
> Se a opção de telefone de autenticação estiver acinzentado, é possível que a sua organização não permita que utilize um número de telefone ou mensagem de texto para verificação. Neste caso, terá de selecionar outro método ou contactar o seu administrador para obter mais ajuda.

## <a name="set-up-your-mobile-device-to-use-a-text-message-as-your-verification-method"></a>Configurar o seu dispositivo móvel para utilizar uma mensagem de texto como método de verificação

1. Na página **de verificação de segurança adicional,** selecione **o telefone autenticação** a partir do **Passo 1: Como entrar em contato com a** área, selecione o seu país ou região a partir da lista de drop-down e, em seguida, digite o número de telefone do seu dispositivo móvel.

2. Selecione **Enviar-me um código por mensagem** de texto a partir da área **Método** e, em seguida, selecione **Seguinte**.

    ![Screenshot que mostra a página "Verificação adicional de segurança", com "Telefone de autenticação" e "Enviar-me um código por mensagem de texto" selecionado.](media/multi-factor-authentication-verification-methods/multi-factor-authentication-text-message.png)

3. Digite o código de verificação da mensagem de texto enviada pela Microsoft para o **Passo 2: Enviámos uma mensagem de texto para a sua** área de telefone e, em seguida, selecione **Verificar**.

    ![Página adicional de verificação de segurança, com telefone de autenticação e mensagem de texto](media/multi-factor-authentication-verification-methods/multi-factor-authentication-text-message-test.png)

4. A partir do **Passo 3: Continue a utilizar a área de aplicações existente,** copie a senha da aplicação fornecida e cole-a num local seguro.

    ![Área de passwords da aplicação da página adicional de verificação de segurança](media/multi-factor-authentication-verification-methods/multi-factor-authentication-app-passwords.png)

    >[!Note]
    >Para obter informações sobre como usar a palavra-passe da aplicação com as suas aplicações mais antigas, consulte [Gerir as palavras-passe da aplicação.](multi-factor-authentication-end-user-app-passwords.md) Só precisa de usar palavras-passe de aplicações se continuar a utilizar aplicações mais antigas que não suportam a verificação de dois fatores.

5. Selecione **Concluído**.

## <a name="set-up-your-mobile-device-to-receive-a-phone-call"></a>Configurar o seu dispositivo móvel para receber uma chamada telefónica

1. Na página **de verificação de segurança adicional,** selecione **o telefone autenticação** a partir do **Passo 1: Como entrar em contato com a** área, selecione o seu país ou região a partir da lista de drop-down e, em seguida, digite o número de telefone do seu dispositivo móvel.

2. Selecione **Ligue-me** da área **método** e, em seguida, selecione **Seguinte**.

    ![Página adicional de verificação de segurança, com telefone de autenticação e chamada telefónica](media/multi-factor-authentication-verification-methods/multi-factor-authentication-phone-call.png)

3. Receberá uma chamada da Microsoft, pedindo que pressione a libra (#) no seu dispositivo móvel para verificar a sua identidade.

    ![Testar o número de telefone especificado](media/multi-factor-authentication-verification-methods/multi-factor-authentication-phone-call-test.png)

4. A partir do **Passo 3: Continue a utilizar a área de aplicações existente,** copie a senha da aplicação fornecida e cole-a num local seguro.

    ![Área de passwords da aplicação da página adicional de verificação de segurança](media/multi-factor-authentication-verification-methods/multi-factor-authentication-app-passwords.png)

    >[!Note]
    >Para obter informações sobre como usar a palavra-passe da aplicação com as suas aplicações mais antigas, consulte [Gerir as palavras-passe da aplicação.](multi-factor-authentication-end-user-app-passwords.md) Só precisa de usar palavras-passe de aplicações se continuar a utilizar aplicações mais antigas que não suportam a verificação de dois fatores.

5. Selecione **Concluído**.

## <a name="next-steps"></a>Passos seguintes

Depois de configurar o seu método de verificação de dois fatores, pode adicionar métodos adicionais, gerir as suas definições e senhas de aplicação, iniciar sposição ou obter ajuda com alguns problemas comuns relacionados com a verificação de dois fatores.

- [Gerir as definições do método de verificação de dois fatores](multi-factor-authentication-end-user-manage-settings.md)

- [Gerir as palavras-passe de aplicação](multi-factor-authentication-end-user-app-passwords.md)

- [Iniciar s-in utilizando a verificação de dois fatores](multi-factor-authentication-end-user-signin.md)

- [Obtenha ajuda com a verificação de dois fatores](multi-factor-authentication-end-user-troubleshoot.md)
