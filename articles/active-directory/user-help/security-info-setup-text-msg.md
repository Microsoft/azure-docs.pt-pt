---
title: Configurar mensagens de texto como seu método de verificação-Azure AD
description: Como configurar sua página de informações de segurança (versão prévia) para verificar sua identidade usando mensagens de texto como seu método de verificação.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: curtand
ms.openlocfilehash: 0525ee6d0ac5df61113e5a97c9812394323ffb3d
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77062358"
---
# <a name="set-up-text-messaging-as-your-verification-method"></a>Configurar mensagens de texto como seu método de verificação

Você pode seguir estas etapas para adicionar os métodos de verificação de dois fatores e de redefinição de senha. Depois de configurar isto pela primeira vez, pode voltar à página de **informações** de Segurança para adicionar, atualizar ou apagar as suas informações de segurança.

Se for solicitado que o instale imediatamente após iniciar sessão na sua conta de trabalho ou escola, consulte os passos detalhados no Conjunto de informações de segurança a partir do artigo de solicitação da página de início de [sessão.](security-info-setup-signin.md)

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Note]
>Se você não vir uma opção de telefone, é possível que sua organização não permita que você use essa opção para verificação. Nesse caso, você precisará escolher outro método ou entrar em contato com o suporte técnico da sua organização para obter mais assistência.

## <a name="set-up-text-messages-from-the-security-info-page"></a>Configurar mensagens de texto na página informações de segurança

Dependendo das configurações da sua organização, você poderá usar mensagens de texto como um dos seus métodos de informações de segurança. A opção de mensagem de texto é uma parte da opção de telefone, portanto, você definirá tudo da mesma maneira que faria para seu número de telefone, mas em vez de fazer com que a Microsoft chame você, você optará por usar uma mensagem de texto.

>[!Note]
>Se quiser receber uma chamada telefónica em vez de uma mensagem de texto, siga os passos na informação de [segurança configurar para utilizar](security-info-setup-phone-number.md) o artigo de chamadas telefónicas.

### <a name="to-set-up-text-messages"></a>Para configurar mensagens de texto

1. Inscreva-se na sua conta de trabalho ou escola e depois vá à sua página de https://myprofile.microsoft.com/.

    ![Minha página de perfil, mostrando links de informações de segurança realçadas](media/security-info/securityinfo-myprofile-page.png)

2. Selecione **informações de segurança** a partir do painel de navegação esquerdo ou do link no bloco de **informações** de Segurança e, em seguida, selecione **adicionar método** a partir da página de informações de **Segurança.**

    ![Página informações de segurança com a opção Adicionar método realçado](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Na página **Adicionar uma página de método,** selecione **Telefone** a partir da lista de lançamentos e, em seguida, selecione **Adicionar**.

    ![Caixa Adicionar método, com telefone selecionado](media/security-info/securityinfo-myprofile-addphonetext.png)

4. Na página **do Telefone,** digite o número de telefone do seu dispositivo móvel, escolha um código por **Texto- Me**e, em seguida, selecione **Seguinte**.

    ![Adicionar número de telefone e escolher mensagens de texto](media/security-info/securityinfo-myprofile-phonetext-addnumber.png)

5. Digite o código que lhe foi enviado através de mensagem de texto para o seu dispositivo móvel e, em seguida, selecione **Next**.

    ![Adicionar número de telefone e escolher mensagens de texto](media/security-info/securityinfo-myprofile-phonetext-entercode.png)

    A página é alterada para mostrar o seu sucesso.

    ![Notificação de êxito, conectando o número de telefone, a escolha para receber mensagens de texto e sua conta](media/security-info/securityinfo-myprofile-phonetext-success.png)

    Suas informações de segurança são atualizadas e você pode usar mensagens de texto para verificar sua identidade ao usar a verificação em duas etapas ou a redefinição de senha. Se pretender fazer mensagens de texto no seu método predefinido, consulte a secção de métodos de [informação](#change-your-default-security-info-method) de segurança padrão deste artigo.

## <a name="delete-text-messaging-from-your-security-info-methods"></a>Excluir mensagens de texto de seus métodos de informações de segurança

Se já não quiser utilizar mensagens de texto como um método de informação de segurança, pode removê-la da página de informações de **Segurança.**

>[!Important]
>Se você excluir mensagens de texto por engano, não será possível desfazê-las. Terá de adicionar o método novamente, seguindo os passos na secção de mensagens de [texto Configurar](#set-up-text-messages-from-the-security-info-page) este artigo.

### <a name="to-delete-text-messaging"></a>Para excluir mensagens de texto

1. Na página de informações de **Segurança,** selecione o link **Eliminar** ao lado da opção **Telefone.**

    ![Link para excluir o telefone e o método de mensagens de texto das informações de segurança](media/security-info/securityinfo-myprofile-phonetext-delete.png)

2. Selecione **Sim** da caixa de confirmação para apagar o número de **telefone.** Depois de o seu número de telefone ser apagado, é removido das suas informações de segurança e desaparece da página de informações de **Segurança.** Se o **Telefone** for o seu método predefinido, a predefinição mudará para outro método disponível.

## <a name="change-your-default-security-info-method"></a>Alterar o método de informações de segurança padrão

Se pretender que as mensagens de texto sejam o método padrão utilizado quando iniciar sessão na sua conta de trabalho ou escolar utilizando verificação de dois fatores ou para pedidos de redefinição de palavras-passe, pode defini-lo a partir da página de informações de **Segurança.**

### <a name="to-change-your-default-security-info-method"></a>Para alterar o método de informações de segurança padrão

1. Na página de informações de **Segurança,** selecione o link **Alterar** ao lado da informação do método de **iniciar sessão Predefinido.**

    ![Alterar o link para o método de entrada padrão](media/security-info/securityinfo-myprofile-phonetext-defaultchange.png)

2. Selecione **Telefone - texto *_(your_phone_number_* )** da lista de métodos disponíveis e, em seguida, selecione **Confirmar**.

    ![Escolha o método para entrar no padrão](media/security-info/securityinfo-myprofile-phonetext-changeddefault.png)

    O método predefinido utilizado para alterações de sessão no **Telefone - texto ( *_your_phone_number_* )** .

## <a name="additional-security-info-methods"></a>Métodos adicionais de informações de segurança

Você tem opções adicionais de como sua organização entra em contato com você para verificar sua identidade, com base no que você está tentando fazer. As opções incluem:

- **Aplicação autenticadora.** Baixe e use um aplicativo autenticador para obter uma notificação de aprovação ou um código de aprovação gerado aleatoriamente para verificação em duas etapas ou redefinição de senha. Para obter instruções passo a passo sobre como configurar e utilizar a aplicação Microsoft Authenticator, consulte [Configurar informações de segurança para utilizar uma aplicação autenticadora](security-info-setup-auth-app.md).

- **Dispositivo móvel ou chamada telefónica de trabalho.** Insira o número do seu dispositivo móvel e obtenha uma chamada telefônica para verificação em duas etapas ou redefinição de senha. Para obter instruções passo a passo sobre como verificar a sua identidade com um número de telefone, consulte [Configurar informações](security-info-setup-phone-number.md)de segurança para utilizar chamadas telefónicas .

- **Chave de segurança.** Registre sua chave de segurança compatível com a Microsoft e use-a junto com um PIN para verificação em duas etapas ou redefinição de senha. Para obter instruções passo a passo sobre como verificar a sua identidade com uma chave de segurança, consulte [Configurar informações](security-info-setup-security-key.md)de segurança para utilizar uma chave de segurança .

- **Endereço de e-mail.** Insira seu endereço de email corporativo ou de estudante para obter um email para redefinição de senha. Essa opção não está disponível para verificação em duas etapas. Para obter instruções passo a passo sobre como configurar o seu e-mail, consulte [Configurar informações](security-info-setup-email.md)de segurança para utilizar o e-mail .

- **Questões de segurança.** Responda a algumas perguntas de segurança criadas pelo administrador da sua organização. Essa opção só está disponível para a redefinição de senha e não para a verificação em duas etapas. Para obter instruções passo a passo sobre como configurar as suas questões de segurança, consulte a informação de [segurança configurar para utilizar](security-info-setup-questions.md) o artigo de questões de segurança.

    >[!Note]
    >Se algumas dessas opções estiverem ausentes, isso provavelmente ocorrerá porque sua organização não permite esses métodos. Se esse for o caso, você precisará escolher um método disponível ou entrar em contato com o administrador para obter mais ajuda.

## <a name="next-steps"></a>Passos seguintes

- Redefinir a sua palavra-passe se a perdeu ou esqueceu, a partir do portal de [reset password](https://passwordreset.microsoftonline.com/) ou siga os passos no artigo de [reset do seu trabalho ou palavra-passe escolar.](active-directory-passwords-update-your-own-password.md)

- Obtenha dicas de resolução de problemas e ajude a iniciar sessão no [Artigo da conta da Microsoft.](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)
