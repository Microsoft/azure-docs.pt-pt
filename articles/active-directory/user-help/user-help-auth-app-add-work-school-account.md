---
title: Adicione uma conta de trabalho ou escola à app Microsoft Authenticator - Azure AD
description: Adicione o seu trabalho ou conta escolar à aplicação Microsoft Authenticator para verificar a sua identidade enquanto utiliza a verificação de dois fatores.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 11/15/2020
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: 04c9bc429d9663f7ac36b6ba8f40abf225eb71c6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97359120"
---
# <a name="add-your-work-or-school-account-to-the-microsoft-authenticator-app"></a>Adicione o seu trabalho ou conta escolar à aplicação Microsoft Authenticator

Se a sua organização utilizar a verificação de dois fatores, pode configurar a sua conta de trabalho ou escola para utilizar a aplicação Microsoft Authenticator como um dos métodos de verificação.

>[!Important]
>Antes de poder adicionar a sua conta, tem de descarregar e instalar a aplicação Microsoft Authenticator. Se ainda não o fez, siga os passos no Download e instale o artigo [da aplicação.](user-help-auth-app-download-install.md)

## <a name="add-your-work-or-school-account"></a>Adicione o seu trabalho ou conta escolar

Pode adicionar o seu trabalho ou conta escolar à aplicação Microsoft Authenticator, fazendo uma das seguintes:

- Faça o sômis com as credenciais de conta de trabalho ou escola (pré-visualização)
- Digitalize um código QR

### <a name="sign-in-with-your-credentials"></a>Inicie sessão com as suas credenciais

>[!Note]
>Esta funcionalidade é utilizável apenas por utilizadores cujos administradores tenham ativado o início de sôs-in do telefone utilizando a aplicação Authenticator para os mesmos.

Para adicionar uma conta, inscrevendo-se na sua conta de trabalho ou escola utilizando as suas credenciais:

1. Abra a aplicação Microsoft Authenticator e selecione para o **+** botão e toque **em Adicionar trabalho ou conta escolar**. Selecione **Iniciar sessão**.

1. Insira as credenciais de trabalho ou conta escolar. Se tiver um Passe de Acesso Temporário (TAP) pode usá-lo para iniciar sedutada. Neste ponto, pode potencialmente ser impedido de prosseguir por uma das seguintes condições:

   - Se não tiver métodos de autenticação suficientes na sua conta para obter um token de autenticação forte, não poderá proceder à adição de uma conta.

   - Se receber a `You might be signing in from a location that is restricted by your admin` mensagem, está bloqueado e precisa de um administrador para o desbloquear nas [informações de Segurança](https://mysignins.microsoft.com/security-info).

   - Se não estiver bloqueado para iniciar sing-in telefónico utilizando a aplicação Authenticator pelo seu administrador, poderá passar pelo registo do dispositivo para se configurar para iniciar senting de telefone sem palavra-passe e autenticação multi-factor Azure (MFA).

1. Neste momento, pode ser-lhe pedido que verifique um Código QR fornecido pela sua organização para criar uma conta de autenticação multi-factor no local na aplicação. Só é obrigado a fazê-lo se a sua organização utilizar o MFA Server no local.

1. No seu dispositivo, toque na conta e verifique na visão completa de que a sua conta está correta e que existe um código de verificação de seis dígitos associado. Para uma segurança adicional, o código de verificação muda a cada 30 segundos impedindo alguém de usar um código várias vezes.

## <a name="sign-in-with-a-qr-code"></a>Iniciar sins com um código QR

Para adicionar uma conta digitalizando um Código QR, faça o seguinte:

1. No seu computador, aceda à página **adicional de verificação de segurança.**

   >[!Note]
   >Se não vir a página **de verificação de segurança adicional,** é possível que o seu administrador tenha ligado a experiência de informação de segurança (pré-visualização). Se for esse o caso, deve seguir as instruções na informação de segurança Configurar para utilizar uma secção [de aplicação autenticadora.](security-info-setup-auth-app.md) Se não for esse o caso, terá de contactar o Help Desk da sua organização para obter assistência. Para obter mais informações sobre informações de segurança, consulte [Configurar as suas informações de Segurança a partir de um pedido de inscrição.](security-info-setup-signin.md)

1. Selecione a caixa de verificação ao lado da aplicação Authenticator e, em seguida, selecione **Configure**. Aparece a página **de aplicações móveis Configure.**

   ![Ecrã que fornece um código QR](./media/user-help-auth-app-add-work-school-account/auth-app-barcode.png)

1. Abra a aplicação Microsoft Authenticator, selecione o ícone mais ![ Selecione o ícone mais em dispositivos iOS ou Android ](media/user-help-auth-app-add-work-school-account/plus-icon.png) e selecione **adicionar a conta**, e, em seguida, selecione Trabalho ou conta **escolar,** seguido por **Digitalizar um Código QR**.
   Se não tiver uma conta configurada na aplicação Authenticator, verá um grande botão azul que diz **Adicionar conta**.

Se não for solicitado que utilize a sua câmara para digitalizar um Código QR, nas definições do seu telefone, certifique-se de que a aplicação Authenticator tem acesso à câmara do telefone.

## <a name="next-steps"></a>Passos seguintes

- Depois de adicionar as suas contas à aplicação, pode iniciar sôms usando a aplicação Authenticator no seu dispositivo. Para obter mais informações, consulte [Iniciar sção usando a aplicação.](user-help-auth-app-sign-in.md)

- Para dispositivos que executam o iOS, também pode fazer o back up das suas credenciais de conta e configurações de aplicações relacionadas, como a ordem das suas contas, para a nuvem. Para obter mais informações, consulte [Backup e recupere com a aplicação Microsoft Authenticator](user-help-auth-app-backup-recovery.md).
