---
title: Configurar informações de segurança (pré-visualização) a partir de seu início de sessão linha - Azure Active Directory | Documentos da Microsoft
description: Como configurar as informações de segurança para a sua conta escolar ou profissional, se lhe for pedido da página de início de sessão da sua organização.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: overview
ms.date: 02/13/2019
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: f9c6faff10f68d720bc3c86a191e4cd1b1f9abdc
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58123611"
---
# <a name="set-up-your-security-info-preview-from-the-sign-in-page-prompt"></a>Configurar as suas informações de segurança (pré-visualização) no prompt de página de início de sessão de
Pode seguir estes passos, se lhe for pedido para configurar as informações de segurança imediatamente depois de iniciar sessão na sua conta escolar ou profissional.

Só verá esta linha de comandos se ainda não configurou as informações de segurança exigida pela sua organização. Se tive definido previamente suas informações de segurança, mas que pretende efetuar alterações, pode seguir os passos os vários artigos de instruções com base no método. Para obter mais informações, consulte [adicionar ou atualizar a sua visão geral de informações de segurança](security-info-add-update-methods-overview.md).

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

## <a name="sign-in-to-your-work-or-school-account"></a>Inicie sessão na sua conta escolar ou profissional
Depois de iniciar sessão sua conta escolar ou profissional, verá uma linha de comandos que lhe pede para fornecer mais informações antes de permite-lhe aceder à sua conta.

![Lhe for pedido para obter mais informações](media/security-info/securityinfo-prompt.png)

## <a name="set-up-your-security-info-using-the-wizard"></a>Configurar as suas informações de segurança utilizando o Assistente
Siga estes passos para configurar as suas informações de segurança para a sua conta escolar ou profissional da linha de comandos.

>[!Important]
>Este é apenas um exemplo do processo. Dependendo das necessidades da sua organização, o administrador poderá ter configurar métodos de verificação diferente que precisará configurar durante este processo. Neste exemplo, podemos estiver exigir dois métodos, a aplicação Microsoft Authenticator e um número de telefone celular para chamadas de verificação ou mensagens de texto.

1. Depois de selecionar **próxima** da linha de comandos, uma **manter o seu assistente de seguro de conta** aparece e mostra o primeiro método, o administrador e a organização tem de configurar. Neste exemplo, é a aplicação Microsoft Authenticator.

   > [!Note]
   > Se pretender utilizar uma aplicação de autenticação que não a aplicação Microsoft Authenticator, selecione o **desejo usar uma aplicação de autenticador diferente** ligação.
   > 
   > Se sua organização permite-lhe escolher um método diferente, além da aplicação authenticator, pode selecionar o **quero configurar uma ligação de outro método**.

    ![Manter o seu assistente de seguro de conta, que mostra a página de download da aplicação de autenticação](media/security-info/securityinfo-prompt-get-auth-app.png)

2. Selecione **baixe agora** para transferir e instalar a aplicação Microsoft Authenticator no seu dispositivo móvel e, em seguida, selecione **próxima**. Para obter mais informações sobre como transferir e instalar a aplicação, consulte [transferir e instalar a aplicação Microsoft Authenticator](user-help-auth-app-download-install.md).

    ![Manter o seu assistente de seguro de conta, que mostra o autenticador conjunto sua página de conta](media/security-info/securityinfo-prompt-auth-app-setup-acct.png)

3. Manter-se no **configurar a sua conta** página enquanto configurar a aplicação Microsoft Authenticator no seu dispositivo móvel.

4. Abra a aplicação Microsoft Authenticator, selecionar para permitir notificações (se lhe for pedido), selecione **adicionar conta** partir a **personalizar e controle** ícone no canto superior direito e, em seguida, selecione **trabalho ou conta escolar**.

5. Retorno para o **configurar a sua conta** página no seu computador e, em seguida, selecione **próxima**.

    O **digitalizar o código QR** é apresentada a página.

    ![Verifique o código QR com a aplicação de autenticador](media/security-info/securityinfo-prompt-auth-app-qrcode.png)

6. Digitalize o código fornecido com o leitor de código QR de aplicação Microsoft Authenticator, que apareceu no seu dispositivo móvel, depois que sua conta escolar ou profissional que criou no passo 5.

    A aplicação authenticator deve com êxito adicionar seu trabalho conta escolar ou profissional sem a necessidade de qualquer informação adicional sobre. No entanto, se o leitor de código QR não é possível ler o código, pode selecionar o **não é possível verificar a ligação do código de QR** e introduzir manualmente o código e o URL para a aplicação Microsoft Authenticator. Para obter mais informações sobre como adicionar manualmente um código, consulte [adicionar manualmente uma conta para a aplicação](user-help-auth-app-add-account-manual.md).

7. Selecione **próxima** sobre o **digitalizar o código QR** página no seu computador.

    É enviada uma notificação para a aplicação Microsoft Authenticator no seu dispositivo móvel, para testar a sua conta.

    ![Testar a sua conta com a aplicação de autenticador](media/security-info/securityinfo-prompt-test-app.png)

8. Aprove a notificação na aplicação Microsoft Authenticator e, em seguida, selecione **seguinte**.

    ![Notificação de êxito, ligar a aplicação e a sua conta](media/security-info/securityinfo-prompt-auth-app-success.png).

    Por predefinição, para verificar a sua identidade quando utilizar a reposição de palavra-passe ou a verificação em dois passos, suas informações de segurança é atualizada para utilizar a aplicação Microsoft Authenticator.

9. Sobre o **telefone** página de configuração, escolha se pretende receber uma mensagem de texto ou de uma chamada telefónica e, em seguida, selecione **próxima**. Para efeitos deste exemplo, estamos a utilizar as mensagens de texto, deve usar um número de telefone de um dispositivo que pode aceitar mensagens de texto.

    ![Começar a configurar o seu número de telefone para mensagens de texto](media/security-info/securityinfo-prompt-text-msg.png)

    É enviada uma mensagem de texto para o seu número de telefone. Se desejar mais receber uma chamada telefónica, o processo é o mesmo. No entanto, receberá uma chamada telefónica com instruções, em vez de uma mensagem de texto.

10. Introduza o código fornecido pela mensagem de texto enviada para o seu dispositivo móvel e, em seguida, selecione **seguinte**.

    ![Testar a sua conta com a mensagem de texto](media/security-info/securityinfo-prompt-text-msg-enter-code.png)

11. Reveja a notificação de êxito e, em seguida, selecione **feito**.

    ![Notificação de êxito](media/security-info/securityinfo-prompt-call-answered-success.png)

    As informações de segurança é atualizada para utilizar como um método de cópia de segurança de mensagens de texto para verificar a sua identidade quando utilizar a reposição de palavra-passe ou a verificação em dois passos.

12. Reveja os **sucesso** página para verificar que com êxito configurou a aplicação Microsoft Authenticator e um método de phone (mensagem de texto ou chamada telefónica) para as suas informações de segurança e, em seguida, selecione **feito** .

    ![Página do assistente concluída com êxito](media/security-info/securityinfo-prompt-setup-success.png)

## <a name="next-steps"></a>Passos Seguintes

- Para alterar, eliminar, ou métodos de informações de segurança padrão de atualização, consulte:

    - [Configurar informações de segurança para uma aplicação de autenticador](security-info-setup-auth-app.md).

    - [Configurar informações de segurança de mensagens de texto](security-info-setup-text-msg.md).

    - [Definir informações de segurança para utilizar chamadas telefónicas](security-info-setup-phone-number.md).

    - [Definir informações de segurança para utilizar o e-mail](security-info-setup-email.md).

    - [Definir informações de segurança para utilizar perguntas de segurança predefinidas](security-info-setup-questions.md).

- Para obter informações sobre como iniciar sessão com o seu método especificado, consulte [como iniciar sessão](user-help-sign-in.md).

- Repor a palavra-passe, se tiver perdido ou esquecidas, do [portal de reposição de palavra-passe](https://passwordreset.microsoftonline.com/) ou siga os passos a [repor a palavra-passe profissional ou escolar](user-help-reset-password.md) artigo.

- Obter solução de problemas dicas e ajuda para problemas de início de sessão no [não pode iniciar sessão na sua conta Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) artigo.