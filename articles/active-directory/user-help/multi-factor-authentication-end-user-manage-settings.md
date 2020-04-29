---
title: Altere o seu método e definições de verificação de dois fatores - Diretório Ativo Azure
description: Aprenda a alterar o método de verificação de segurança e as configurações para o seu trabalho ou conta escolar, a partir da página adicional de verificação de segurança.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: richagi
ms.assetid: d3372d9a-9ad1-4609-bdcf-2c4ca9679a3b
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: curtand
ms.openlocfilehash: 5949f04ecc28a88e340a9c2de530031793f193a0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79253250"
---
# <a name="change-your-two-factor-verification-method-and-settings"></a>Altere o seu método de verificação de dois fatores e as definições

Depois de configurar os seus métodos de verificação de segurança para o seu trabalho ou conta escolar, pode atualizar qualquer um dos detalhes relacionados, incluindo:

- Método de verificação de segurança padrão

- Detalhes do método de verificação de segurança, como o seu número de telefone

- Configuração ou apreensão de um dispositivo da aplicação autenticadora

## <a name="using-the-additional-security-verification-page"></a>Utilização da página adicional de verificação de segurança

Se a sua organização lhe forneceu passos específicos sobre como ligar e gerir a sua verificação de dois fatores, deve seguir essas instruções primeiro. Caso contrário, pode chegar às definições do seu método de verificação de segurança a partir da página adicional de verificação de [segurança.](https://docs.microsoft.com/azure/active-directory/user-help/multi-factor-authentication-end-user-first-time)

>[!Note]
>Se o que está a ver no seu ecrã não corresponder ao que está a ser coberto neste artigo, significa que ou o seu administrador ligou a experiência de informação de **Segurança (pré-visualização)** ou que a sua organização forneceu o seu próprio portal personalizado. Para mais informações sobre a nova experiência de informação de segurança, consulte a visão geral da informação de [segurança (pré-visualização).](user-help-security-info-overview.md) Para mais informações sobre o portal personalizado da sua organização, deve contactar o balcão de Ajuda da sua organização.

### <a name="to-get-to-the-additional-security-verification-page"></a>Para chegar à página adicional de verificação de segurança

Pode seguir este link para a página adicional de [verificação](https://account.activedirectory.windowsazure.com/proofup.aspx?proofup=1)de segurança .

![Página adicional de verificação de segurança, com os detalhes do método de verificação de segurança disponíveis](./media/multi-factor-authentication-end-user-manage-settings/mfa-security-verification-page.png)

Também pode chegar à página adicional de **verificação** de segurança seguindo estes passos:

1. Inicie sessão em [https://myapps.microsoft.com](https://myapps.microsoft.com).

1. Selecione o nome da sua conta no direito superior e, em seguida, selecione **o perfil**.

1. Selecione **verificação adicional de segurança**.  

    ![As minhas apps ligam-se à página adicional de verificação de segurança](./media/multi-factor-authentication-end-user-manage-settings/mfa-myapps-link.png)

>[!Note]
>Para obter informações sobre a utilização da secção de **passwords** da App da página adicional de **verificação** de segurança, consulte [Gerir as palavras-passe da aplicação para verificação de dois fatores](multi-factor-authentication-end-user-app-passwords.md). As palavras-passe das aplicações devem ser utilizadas apenas para aplicações que não suportem a verificação de dois fatores.

## <a name="change-your-default-security-verification-method"></a>Altere o seu método de verificação de segurança padrão

Depois de iniciar sessão na sua conta de trabalho ou escola com o seu nome de utilizador e senha, será automaticamente apresentado com o seu método de verificação de segurança escolhido. Dependendo dos requisitos da sua organização, este pode ser um código de notificação ou verificação através de uma aplicação autenticadora, uma mensagem de texto ou uma chamada telefónica.

Se decidir que quer alterar o método de verificação de segurança padrão que está a usar, pode fazê-lo a partir daqui.

### <a name="to-change-your-default-security-verification-method"></a>Para alterar o seu método de verificação de segurança padrão

1. A partir da página adicional de **verificação** de segurança, selecione o método a utilizar a partir da lista de **opções preferida.** Você verá todas as opções, mas você pode selecionar apenas as que são disponibilizadas para você pela sua organização.

    - **Notifique-me através da aplicação**: Será notificado através da sua aplicação autenticadora de que tem um pedido de verificação de espera.

    - **Ligue para o meu telefone de autenticação:** receberá uma chamada telefónica no seu dispositivo móvel, pedindo-lhe que verifique as suas informações.

    - Código de **texto para o meu telefone de autenticação**: Receberá um código de verificação como parte de uma mensagem de texto no seu dispositivo móvel. Deve inserir este código no pedido de verificação do seu trabalho ou conta escolar.

    - **Ligue para o meu telefone**do escritório: receberá uma ligação no seu telefone do escritório, pedindo-lhe para verificar a sua informação.

    - Utilize o **código de verificação da aplicação**: Utilizará a sua aplicação autenticadora para obter um código de verificação que irá digitar no pedido a partir do seu trabalho ou conta escolar.

2. Selecione **Guardar**.

## <a name="add-or-change-your-phone-number"></a>Adicione ou altere o seu número de telefone

Pode adicionar novos números de telefone, ou atualizar os números existentes, a partir da página adicional de verificação de **segurança.**

>[!Important]
>Recomendamos vivamente que adicione um número de telefone secundário para ajudar a evitar ser bloqueado fora da sua conta se o seu telefone principal estiver perdido ou roubado, ou se tiver um telefone novo e deixar de ter o seu número de telefone original, primário.

### <a name="to-change-your-phone-numbers"></a>Para alterar os seus números de telefone

1. A partir da secção como pretende **Additional security verification** **responder?** **Authentication phone** **Office phone**

1. Selecione a caixa ao lado da opção de telefone de **autenticação alternativa** e, em seguida, digite um número de telefone secundário onde possa receber mensagens de texto ou chamadas telefónicas se não conseguir aceder ao seu dispositivo principal.

1. Selecione **Guardar**.

## <a name="add-a-new-account-to-the-microsoft-authenticator-app"></a>Adicione uma nova conta à aplicação autenticadora da Microsoft

Pode configurar o seu trabalho ou conta escolar na aplicação Microsoft Authenticator para [Android](https://play.google.com/store/apps/details?id=com.azure.authenticator) ou [iOS.](https://apps.apple.com/app/microsoft-authenticator/id983156458)

Se já configurar o seu trabalho ou conta escolar na aplicação Microsoft Authenticator, não precisa de o fazer novamente.

1. A partir da secção Como gostaria **Additional security verification** de **responder?** **Set up Authenticator app**

    ![Configurar o seu trabalho ou conta escolar na aplicação Microsoft Authenticator](./media/multi-factor-authentication-end-user-manage-settings/mfa-security-verification-page-auth-app.png)

1. Siga as instruções no ecrã, incluindo a utilização do seu dispositivo móvel para digitalizar o código QR e, em seguida, selecione **Next**.

    Será-lhe pedido que aprove uma notificação através da aplicação Microsoft Authenticator, para verificar as suas informações.

1. Selecione **Guardar**.

## <a name="delete-your-account-or-device-from-the-microsoft-authenticator-app"></a>Elimine a sua conta ou dispositivo da aplicação Microsoft Authenticator

Pode eliminar a sua conta da aplicação Microsoft Authenticator e pode eliminar o seu dispositivo a partir do seu trabalho ou conta escolar. Normalmente, elimina o seu dispositivo para remover permanentemente um dispositivo perdido, roubado ou antigo da sua conta, e elimina a sua conta para tentar corrigir alguns problemas de ligação ou para resolver uma alteração de conta, como um novo nome de utilizador.

### <a name="to-delete-your-device-from-your-work-or-school-account"></a>Para eliminar o seu dispositivo do seu trabalho ou conta escolar

1. A partir da secção Como gostaria **Additional security verification** de **responder?** **Set up Authenticator app**

1. Selecione **Guardar**.

### <a name="to-delete-your-account-from-the-microsoft-authenticator-app"></a>Para eliminar a sua conta da aplicação Microsoft Authenticator

A partir da aplicação Microsoft Authenticator, selecione o botão **Eliminar** ao lado do dispositivo que pretende eliminar.

## <a name="turn-on-two-factor-verification-prompts-on-a-trusted-device"></a>Ligue os pedidos de verificação de dois fatores num dispositivo de confiança

Dependendo das definições da sua organização, poderá ver uma caixa de verificação que diz Não **peça novamente x dias** quando realizar verificação de dois fatores no seu navegador. Se selecionou esta opção para parar as solicitações de verificação de dois fatores e, em seguida, perde o seu dispositivo ou o seu dispositivo está potencialmente comprometido, deve voltar a ligar as solicitações de verificação de dois fatores para ajudar a proteger a sua conta. Deve ligar as instruções para todos os seus dispositivos ao mesmo tempo. Infelizmente, não pode ligar as instruções para um dispositivo específico.

### <a name="to-turn-two-factor-verification-prompts-back-on-for-your-devices"></a>Para ligar as solicitações de verificação de dois fatores para os seus dispositivos

A partir da página adicional de verificação de [ **segurança,** ](#to-get-to-the-additional-security-verification-page)selecione Restaurar a **autenticação de vários fatores em dispositivos previamente fidedignos**. Da próxima vez que iniciar sessão em qualquer dispositivo, será solicitado a realização de verificação de dois fatores.

## <a name="next-steps"></a>Passos seguintes

Depois de adicionar ou atualizar as definições de verificação de dois fatores, pode gerir as suas palavras-passe da aplicação, iniciar sessão ou obter ajuda com alguns problemas comuns de verificação de dois fatores.

- [Gerencie as palavras-passe da aplicação para verificação de dois fatores](multi-factor-authentication-end-user-app-passwords.md) para quaisquer aplicações que não suportem a verificação de dois fatores.

- [Como assinar usando verificação de dois fatores](multi-factor-authentication-end-user-signin.md)

- [Resolver problemas comuns com verificação de dois fatores](multi-factor-authentication-end-user-troubleshoot.md)
