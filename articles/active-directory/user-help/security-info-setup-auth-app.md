---
title: Definir informações de segurança (pré-visualização) para utilizar uma aplicação de autenticação - Azure Active Directory | Documentos da Microsoft
description: Como configurar as informações de segurança para verificar a sua identidade com a aplicação Microsoft Authenticator.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: 336b30a8e8687b3a2bdd05a17e558f44905f135e
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57338846"
---
# <a name="set-up-security-info-preview-to-use-an-authenticator-app"></a>Definir informações de segurança (pré-visualização) para utilizar uma aplicação de autenticação
Pode seguir estes passos para adicionar a verificação de dois fatores e métodos de reposição de palavra-passe. Depois de ter configurado isso pela primeira vez, pode retornar para o **informações de segurança** página para adicionar, atualizar ou eliminar as suas informações de segurança.

Se lhe for pedido para configurá-la imediatamente depois de iniciar sessão sua conta escolar ou profissional, consulte os passos detalhados no [configurar as suas informações de segurança no prompt de página de início de sessão de](security-info-setup-signin.md) artigo.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Note]
>Se não vir uma opção de aplicação de autenticador, é possível que a sua organização não permite a utilização de uma aplicação de autenticação para a verificação. Neste caso, precisará escolher outro método ou contacte o administrador para obter mais ajuda.

## <a name="set-up-the-microsoft-authenticator-app-from-the-security-info-page"></a>Configurar a aplicação Microsoft Authenticator a partir da página de informações de segurança
Dependendo das definições da sua organização, poderá conseguir utilizar uma aplicação de autenticação como um dos métodos de informações de segurança. Não são necessários para utilizar a aplicação Microsoft Authenticator e pode escolher uma aplicação diferente durante o processo de configuração. No entanto, este artigo utiliza a aplicação Microsoft Authenticator. 

### <a name="to-set-up-the-microsoft-authenticator-app"></a>Para configurar a aplicação Microsoft Authenticator

1. Inicie sessão na sua conta escolar ou profissional e, em seguida, aceda ao seu https://myprofile.microsoft.com/ página.

    ![Minha página de perfil, que apresenta ligações de informações de segurança realçadas](media/security-info/securityinfo-myprofile-page.png)

2. Selecione **informações de segurança** no painel de navegação esquerdo ou na ligação presente o **informações de segurança** bloquear e, em seguida, selecione **Adicionar método** do **informações de segurança**  página.

    ![Página de informações de segurança com a opção de método adicionar realçada](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Sobre o **adicione um método** página, selecione **aplicação de autenticador** na lista pendente e, em seguida, selecione **adicionar**.

    ![Adicionar a caixa de método, com a aplicação de autenticação selecionada](media/security-info/securityinfo-myprofile-addauthapp.png)

4. Sobre o **começar por colocar a aplicação** página, selecione **Transferir agora** para transferir e instalar a aplicação Microsoft Authenticator no seu dispositivo móvel e, em seguida, selecione **seguinte**.

    Para obter mais informações sobre como transferir e instalar a aplicação, consulte [transferir e instalar a aplicação Microsoft Authenticator](user-help-auth-app-download-install.md).

    ![Comece obtendo a página da aplicação](media/security-info/securityinfo-myprofile-getauthapp.png)

    >[!Note]
    >Se pretender utilizar uma aplicação de autenticação que não a aplicação Microsoft Authenticator, selecione o **desejo usar uma aplicação de autenticador diferente** ligação.
    
    >Se sua organização permite-lhe escolher um método diferente, além da aplicação authenticator, pode selecionar o **quero configurar uma ligação de outro método**.

5. Manter-se no **configurar a sua conta** página enquanto configurar a aplicação Microsoft Authenticator no seu dispositivo móvel.

    ![Configurar a página da aplicação de autenticador](media/security-info/securityinfo-myprofile-setupauthapp.png)

6. Abra a aplicação Microsoft Authenticator, selecionar para permitir notificações (se lhe for pedido), selecione **adicionar conta** partir a **personalizar e controle** ícone no canto superior direito e, em seguida, selecione **trabalho ou conta escolar**.

7. Retorno para o **configurar a sua conta** página no seu e, em seguida, selecione **próxima**.

    O **digitalizar o código QR** é apresentada a página.

    ![Verifique o código QR com a aplicação de autenticador](media/security-info/securityinfo-myprofile-qrcodeauthapp.png)

6. Digitalize o código fornecido com o leitor de código QR de aplicação Microsoft Authenticator, que apareceu no seu dispositivo móvel, depois que sua conta escolar ou profissional que criou no passo 6.

    A aplicação authenticator deve com êxito adicionar seu trabalho conta escolar ou profissional sem a necessidade de qualquer informação adicional sobre. No entanto, se o leitor de código QR não é possível ler o código, pode selecionar o **não é possível verificar a ligação do código de QR** e introduzir manualmente o código e o URL para a aplicação Microsoft Authenticator. Para obter mais informações sobre como adicionar manualmente um código, consulte [adicionar manualmente uma conta para a aplicação](user-help-auth-app-add-account-manual.md).

7. Selecione **próxima** no **digitalizar o código QR** página no seu.

    É enviada uma notificação para a aplicação Microsoft Authenticator no seu dispositivo móvel, para testar a sua conta.

    ![Testar a sua conta com a aplicação de autenticador](media/security-info/securityinfo-myprofile-tryitauthapp.png)

8. Aprove a notificação na aplicação Microsoft Authenticator e, em seguida, selecione **seguinte**.

    ![Notificação de êxito, ligar a aplicação e a sua conta](media/security-info/securityinfo-myprofile-successauthapp.png)

    Por predefinição, para verificar a sua identidade quando utilizar a reposição de palavra-passe ou a verificação em dois passos, suas informações de segurança é atualizada para utilizar a aplicação Microsoft Authenticator.

## <a name="delete-your-authenticator-app-from-your-security-info-methods"></a>Eliminar aplicação de autenticador a partir de seus métodos de informações de segurança
Se já não quiser utilizar a aplicação de autenticação como um método de informações de segurança, pode removê-lo a partir da **informações de segurança** página. Isto funciona para todas as aplicações de autenticação, não apenas a aplicação Microsoft Authenticator. Depois de eliminar a aplicação, terá de ir para a aplicação de autenticador no seu dispositivo móvel e eliminar a conta.

>[!Important]
>Se eliminar a aplicação authenticator por engano, não é possível-lo. Terá que adicionar a aplicação de autenticador novamente, seguindo os passos a [configurar a aplicação de autenticador](#set-up-the-microsoft-authenticator-app-from-the-security-info-page) seção deste artigo.

### <a name="to-delete-the-authenticator-app"></a>Para eliminar a aplicação de autenticador

1. Sobre o **informações de segurança** página, selecione a **eliminar** ligação junto à aplicação de autenticador.

    ![Ligação para eliminar a aplicação authenticator a partir de informações de segurança](media/security-info/securityinfo-myprofile-deleteauthapp.png)

2. Selecione **Sim** da caixa de confirmação para eliminar a aplicação de autenticador. Depois da aplicação de autenticador é eliminada, é removido da suas informações de segurança e, este desaparece dos **informações de segurança** página. Se a aplicação de autenticador seu método padrão, a predefinição será alterado para outro método disponível.

3. Abra a aplicação de autenticador no seu dispositivo móvel, selecione **editar contas**e, em seguida, eliminar a sua conta escolar ou profissional a partir da aplicação de autenticador.

    Sua conta é removida por completo a partir da aplicação de autenticador para verificação de dois fatores e pedidos de reposição de palavra-passe.

## <a name="change-your-default-security-info-method"></a>Alterar o seu método de informações de segurança padrão
Se pretender que a aplicação de autenticador para ser o método predefinido utilizado quando iniciar sessão na sua conta escolar ou profissional com a verificação de dois fatores ou para pedidos de reposição de palavra-passe, pode configurá-lo a partir da segurança **informações** página.

### <a name="to-change-your-default-security-info-method"></a>Para alterar o método de informações de segurança padrão

1. No **informações de segurança** página, selecione a **alteração** ligação junto aos **predefinido do método de início de sessão** informações.

    ![Alterar ligação para o método de início de sessão predefinido](media/security-info/securityinfo-myprofile-changedefaultauthapp.png)

2. Escolher **Microsoft Authenticator - notificação** na lista pendente de métodos disponíveis. Se não estiver a utilizar a aplicação Microsoft Authenticator, selecione o **aplicação de autenticação ou do hardware token** opção.

    ![Escolha o método predefinido para início de sessão](media/security-info/securityinfo-myprofile-defaultauthapp.png)

3. Selecione **confirmar**.

    O método predefinido utilizado para alterações de início de sessão para a aplicação Microsoft Authenticator.

## <a name="additional-security-info-methods"></a>Métodos de informações de segurança adicionais
Tem opções adicionais para como os contactos de organização para verificar a sua identidade, com base no que é que está tentando fazer. As opções incluem:

- **Texto de dispositivos móveis.** Introduza o seu número de dispositivos móveis e obtenha um código que irá utilizar para verificação de dois passos ou palavra-passe de uma mensagem de reposição. Para obter instruções passo a passo sobre como verificar a sua identidade com uma mensagem de texto (SMS), consulte [definir informações de segurança para utilizar mensagens de texto (SMS)](security-info-setup-text-msg.md).

- **Dispositivo móvel ou de trabalho de chamadas telefónicas.** Introduza o seu número de dispositivos móveis e obtenha uma chamada telefónica para a reposição de palavra-passe ou a verificação em dois passos. Para obter instruções passo a passo sobre como verificar a sua identidade com um número de telefone, consulte [definir informações de segurança para utilizar chamadas telefónicas](security-info-setup-phone-number.md).

- **Endereço de e-mail.** Introduza o seu e-mail profissional ou escolar para receber um e-mail para a reposição de palavra-passe. Esta opção não está disponível para verificação de dois passos. Para obter instruções passo a passo sobre como configurar o seu e-mail, consulte [definir informações de segurança para utilizar o e-mail](security-info-setup-email.md).

- **Perguntas de segurança.** Responder a algumas perguntas de segurança criadas pelo seu administrador para a sua organização. Esta opção só está disponível para a reposição de palavra-passe e não para verificação de dois passos. Para obter instruções passo a passo sobre como configurar a suas perguntas de segurança, consulte a [definir informações de segurança para utilizar perguntas de segurança](security-info-setup-questions.md) artigo.
    
    >[!Note]
    >Se algumas dessas opções estão em falta, é mais provável porque a sua organização não permite que esses métodos. Se for este o caso, terá de escolher um método disponível ou contacte o administrador para obter mais ajuda.

## <a name="next-steps"></a>Passos Seguintes

- Repor a palavra-passe, se tiver perdido ou esquecidas, do [portal de reposição de palavra-passe](https://passwordreset.microsoftonline.com/) ou siga os passos a [repor a palavra-passe profissional ou escolar](user-help-reset-password.md) artigo.

- Obter solução de problemas dicas e ajuda para problemas de início de sessão no [não pode iniciar sessão na sua conta Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) artigo.