---
title: Altere o seu método e definições de verificação de dois fatores - Azure Ative Directory
description: Saiba como alterar o método de verificação de segurança e as definições para o seu trabalho ou conta escolar, a partir da página adicional de verificação de segurança.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: richagi
ms.assetid: d3372d9a-9ad1-4609-bdcf-2c4ca9679a3b
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 07/06/2020
ms.author: curtand
ms.openlocfilehash: e0a6c566e8e0dfb77b5899f735020d0f1facf3d1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "88798385"
---
# <a name="change-your-two-factor-verification-method-and-settings"></a>Altere o seu método e configurações de verificação de dois fatores

Depois de configurar os seus métodos de verificação de segurança para o seu trabalho ou conta escolar, pode atualizar qualquer um dos detalhes relacionados, incluindo:

- Método de verificação de segurança padrão

- Detalhes do método de verificação de segurança, como o seu número de telefone

- Configuração ou eliminação de aplicação autenticador da aplicação autenticadora

## <a name="using-the-additional-security-verification-page"></a>Utilização da página adicional de verificação de segurança

Se a sua organização lhe forneceu passos específicos sobre como ligar e gerir a sua verificação de dois fatores, deve seguir essas instruções primeiro. Caso contrário, pode chegar às definições do seu método de verificação de segurança a partir da página adicional de [verificação de segurança.](./multi-factor-authentication-end-user-first-time.md)

>[!Note]
>Se o que está a ver no seu ecrã não corresponder ao que está a ser coberto neste artigo, significa que ou o seu administrador ligou a experiência **de informações de Segurança (pré-visualização)** ou que a sua organização forneceu o seu próprio portal personalizado. Para obter mais informações sobre a nova experiência de informação de segurança, consulte [a informação de segurança (pré-visualização) visão geral](./security-info-setup-signin.md). Para mais informações sobre o portal personalizado da sua organização, deve contactar o balcão de ajuda da sua organização.

### <a name="to-get-to-the-additional-security-verification-page"></a>Para chegar à página adicional de verificação de segurança

Pode seguir este link para a [página adicional de verificação de segurança.](https://account.activedirectory.windowsazure.com/proofup.aspx?proofup=1)

![Página adicional de verificação de segurança, com os detalhes do método de verificação de segurança disponível](./media/multi-factor-authentication-end-user-manage-settings/mfa-security-verification-page.png)

Também pode chegar à página **de verificação de segurança adicional** seguindo estes passos:

1. Inicie sessão em [https://myapps.microsoft.com](https://myapps.microsoft.com).

1. Selecione o nome da sua conta no topo direito e, em seguida, selecione **o perfil**.

1. Selecione **verificação adicional de segurança.**  

    ![As minhas apps ligam-se à página adicional de verificação de segurança](./media/multi-factor-authentication-end-user-manage-settings/mfa-myapps-link.png)

>[!Note]
>Para obter informações sobre a utilização da secção de **palavras-passe** da App da página adicional de verificação de **segurança,** [consulte's Manage passwords da aplicação para verificação de dois fatores](multi-factor-authentication-end-user-app-passwords.md). As palavras-passe da aplicação devem ser usadas apenas para apps que não suportem a verificação de dois fatores.

## <a name="change-your-default-security-verification-method"></a>Altere o seu método de verificação de segurança padrão

Depois de iniciar sôm no seu trabalho ou na sua conta escolar com o seu nome de utilizador e senha, será automaticamente apresentado com o seu método de verificação de segurança escolhido. Dependendo dos requisitos da sua organização, este pode ser um código de notificação ou verificação através de uma aplicação autenticadora, uma mensagem de texto ou uma chamada telefónica.

Se decidir que pretende alterar o método de verificação de segurança predefinido que está a usar, pode fazê-lo a partir daqui.

### <a name="to-change-your-default-security-verification-method"></a>Para alterar o seu método de verificação de segurança padrão

1. A partir da página **de verificação de segurança adicional,** selecione o método a utilizar na lista **de opções preferida.** Você verá todas as opções, mas você pode selecionar apenas as que são disponibilizadas por sua organização.

    - **Notifique-me através da app**: Será notificado através da sua app autenticadora que tem uma solicitação de verificação de espera.

    - **Ligue para o meu telefone de autenticação:** receberá uma chamada telefónica no seu dispositivo móvel, pedindo-lhe que verifique as suas informações.

    - **Código de texto para o meu telefone de autenticação**: Você receberá um código de verificação como parte de uma mensagem de texto no seu dispositivo móvel. Deve introduzir este código na solicitação de verificação do seu trabalho ou conta escolar.

    - **Ligue para o telefone do meu escritório:** receberá uma ligação no telefone do seu escritório, pedindo-lhe que verifique as suas informações.

    - **Use o código de verificação a partir da aplicação**: Utilizará a sua aplicação autenticadora para obter um código de verificação que irá digitar na solicitação a partir do seu trabalho ou conta escolar.

2. Selecione **Guardar**.

## <a name="add-or-change-your-phone-number"></a>Adicione ou altere o seu número de telefone

Pode adicionar novos números de telefone, ou atualizar os números existentes, a partir da página adicional de **verificação de segurança.**

>[!Important]
>Recomendamos vivamente que adicione um número de telefone secundário para ajudar a evitar ser bloqueado fora da sua conta se o seu telefone principal for perdido ou roubado, ou se receber um telefone novo e deixar de ter o seu número de telefone primário original.

### <a name="to-change-your-phone-numbers"></a>Para alterar os seus números de telefone

1. A partir da secção Como pretende  **responder?**  

1. Selecione a caixa ao lado da opção **telefone de autenticação alternativa** e, em seguida, digite um número de telefone secundário onde pode receber chamadas telefónicas se não conseguir aceder ao seu dispositivo principal.

1. Selecione **Guardar**.

## <a name="add-a-new-account-to-the-microsoft-authenticator-app"></a>Adicione uma nova conta à aplicação autenticadora microsoft

Pode configurar a sua conta de trabalho ou escola na aplicação Microsoft Authenticator para [Android](https://play.google.com/store/apps/details?id=com.azure.authenticator) ou [iOS.](https://apps.apple.com/app/microsoft-authenticator/id983156458)

Se já configurar o seu trabalho ou conta escolar na aplicação Microsoft Authenticator, não precisa de o fazer novamente.

1. A partir da secção Como pretende  **responder?** 

    ![Configurar o seu trabalho ou conta escolar na aplicação Microsoft Authenticator](./media/multi-factor-authentication-end-user-manage-settings/mfa-security-verification-page-auth-app.png)

1. Siga as instruções no ecrã, incluindo a utilização do seu dispositivo móvel para digitalizar o código QR e, em seguida, selecione **Next**.

    Será solicitado que aprove uma notificação através da aplicação Microsoft Authenticator, para verificar as suas informações.

1. Selecione **Guardar**.

## <a name="delete-your-account-or-device-from-the-microsoft-authenticator-app"></a>Elimine a sua conta ou dispositivo da aplicação Microsoft Authenticator

Pode eliminar a sua conta a partir da aplicação Microsoft Authenticator e pode eliminar o seu dispositivo do seu trabalho ou da sua conta escolar. Normalmente, elimina o seu dispositivo para remover permanentemente um dispositivo perdido, roubado ou antigo da sua conta, e apaga a sua conta para tentar corrigir alguns problemas de ligação ou para resolver uma alteração de conta, como um novo nome de utilizador.

### <a name="to-delete-your-device-from-your-work-or-school-account"></a>Para eliminar o seu dispositivo do seu trabalho ou conta escolar

1. A partir da secção Como pretende  **responder?** 

1. Selecione **Guardar**.

### <a name="to-delete-your-account-from-the-microsoft-authenticator-app"></a>Para eliminar a sua conta a partir da aplicação Microsoft Authenticator

A partir da aplicação Microsoft Authenticator, selecione o botão **Eliminar** ao lado do dispositivo que pretende eliminar.

## <a name="turn-on-two-factor-verification-prompts-on-a-trusted-device"></a>Ligue as indicações de verificação de dois fatores num dispositivo de confiança

Dependendo das definições da sua organização, pode ver uma caixa de verificação que diz **Não pedir novamente x dias** quando efetuar a verificação de dois fatores no seu navegador. Se selecionou esta opção para parar as indicações de verificação de dois fatores e perder o seu dispositivo ou o seu dispositivo está potencialmente comprometido, deve voltar a ligar as indicações de verificação de dois fatores para ajudar a proteger a sua conta. Tem de ligar as indicações para todos os seus dispositivos ao mesmo tempo. Infelizmente, não se pode voltar a ligar as indicações para um dispositivo específico.

### <a name="to-turn-two-factor-verification-prompts-back-on-for-your-devices"></a>Para ligar as solicitações de verificação de dois fatores de volta para os seus dispositivos

A partir da página [ **de verificação de segurança adicional,**](#to-get-to-the-additional-security-verification-page)selecione **Restaurar a autenticação de vários fatores em dispositivos previamente fidedignos**. Da próxima vez que iniciar sedução em qualquer dispositivo, será solicitado para fazer uma verificação de dois fatores.

## <a name="next-steps"></a>Passos seguintes

Depois de adicionar ou atualizar as definições de verificação de dois fatores, pode gerir as suas palavras-passe da aplicação, iniciar sôs ou obter ajuda com alguns problemas comuns relacionados com a verificação de dois fatores.

- [Gerencie as palavras-passe de aplicações para verificação de dois fatores](multi-factor-authentication-end-user-app-passwords.md) para quaisquer aplicações que não suportem a verificação de dois fatores.

- [Como assinar usando a verificação de dois fatores](multi-factor-authentication-end-user-signin.md)

- [Resolver problemas comuns com verificação de dois fatores](multi-factor-authentication-end-user-troubleshoot.md)